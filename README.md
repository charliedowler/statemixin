## StateMixin
> Fixes issues with the state being set inside REST callbacks that aren't notified of the component being unmounted.

### Problem:
```js
var React = require('react');

var Component = React.createClass({
    mixins: [SomeAPIMixin],
    componentWillMount: function() {
        this.api.request('http://google.com', function(err, res) {
            /**
            * Ahhhh, another event has fired elsewhere in the app
            * that causes this component to unmount while we were
            * waiting for this request to come back.
            */
            this.setState({ data: res.body });
        }.bind(this);
    },
    render: function() {
        return <div> Results: {this.state.data.length} </div>;
    }
});
```

### Solution:
```js
var React = require('react');
var StateMixin = require('StateMixin');

var Component = React.createClass({
    mixins: [SomeAPIMixin, StateMixin],
    debounce: 500, // Throttle "Invariant Violation" warnings
    componentWillMount: function() {
        this.api.request('http://google.com', function(err, res) {
            /**
            * With the StateMixin the setState call is caught and
            * a check is run to see if the component is currently mounted
            * Saving you from any possible "Invariant Violation's"
            */
            this.setState({ data: res.body });
        }.bind(this);
    },
    render: function() {
        return <div> Results: {this.state.data.length} </div>;
    }
});
```