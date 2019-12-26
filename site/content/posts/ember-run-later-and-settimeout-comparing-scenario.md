+++
author = "Esteban"
date = 2017-05-12T13:14:22Z
description = "Comparing both Ember.run.later and setTimeout native function because they both seem to have the same effect."
draft = false
slug = "ember-run-later-and-settimeout-comparing-scenario"
title = "Ember.run.later and setTimeout comparing scenario."

+++


In the Ember internals (Backburner) it can be found that Ember.run.later basically calls setTimeout() under the hood, but respecting the firing order by which other timeouts were added into the queue. 


```python

import Ember from 'ember';

export default Ember.Controller.extend({
  appName: "Timers comparison",
  init: function() {
    this._super();
    this.set('myErrCountLater', 0);
    this.set('myErrCountTimeout', 0);
    this.myFunc(0);
    this.myOtherFunc(0);
  },
  myFunc: function(errorCount) {
    if (errorCount < 7) {
      self = this;
            this.set('myErrCountLater', errorCount);
        console.log("Error Count Later is " + errorCount + ", repeating");
      Ember.run.later(this, function () {
        return self.myFunc(errorCount+1);
      }, 1500);
    }
    else {
        console.log("Everything failed");
    }
    },
  myOtherFunc: function(errorCount) {
    if (errorCount < 7) {
      self = this;
      this.set('myErrCountTimeout', errorCount)
      console.log("Error Count Timeout is " + errorCount + ", repeating");
      setTimeout(function() {
        return self.myOtherFunc(errorCount + 1);
      }, 1500);
    }
    else {
      console.log("Everything failed");
    }
  }
});

```


Internally the only difference is that every function that is run as a callback inside **Ember.run.later** will be pushed into an array where it will fire only after all previously queued events have fired, respecting the queuing order.

