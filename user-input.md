---
layout: default
title: User input
---

 Traditional DOM events are used to handle user input and relay that info back to the nodes in the scene. In Famous, DOM events bubble up the scene graph and program events are emitted down.

## Clicks

A key DOM event is the click. Here we add a `'click'` listener to `node` and an '.onReceive()' method to handle it. 

<iframe src='https://famous.org/examples/index.html?block=click&detail=false&header=false' style="width:100%;height:600px; margin: 15px 0px 25px 0px" scrolling='no' class='code-block' allowtransparency='true'></iframe>

Events are picked up on the node where the DOM element is attached. The `.addUIEvent()` method accepts the DOM event name passed in as a string (`'click'`). Adding an `.onReceive()` method to the node (or component attached to the node) will pick up all events. 


## Event Bubbling

DOM events bubble up the scene graph. All "parent" nodes in the scene graph receive their own and all of their children's events by adding an `.onReceive()` method. 

<iframe src='https://famous.org/examples/index.html?block=bubbleup&detail=false&header=false' style="width:100%;height:600px; margin: 15px 0px 25px 0px" scrolling='no' class='code-block' allowtransparency='true'></iframe>

Additionally, components with an `.onReceive()` method can receive all of the same events from the node where they are attached.

    node.addComponent({
        onReceive: function(event, payload){
           // This will receive all DOM events from `node`
           // and all child nodes below it  
        }
    })

## .onReceive()

When added to a node or component, the `.onReceive()` method is called whenever an event passes through the node. The method gives access to the event name (as a string) and an event object containing detailed information about the event.

    node.addComponent({
        onReceive: function(event, payload){
           // Access the node where event 
           // originated from with --> payload.node
        }
    })

Among other information, the event object ( named `payload` above ) gives access to the node where the event originated. 


## Custom Events

Nodes can emit custom events down the scene graph using the `.emit()` method. The emit method accepts an event name as a string and a payload object as its two arguments.

<iframe src='https://famous.org/examples/index.html?block=customevent&detail=false&header=false'  style="width:100%;height:600px; margin: 15px 0px 25px 0px" scrolling='no' class='code-block' allowtransparency='true'></iframe>

Events are sent to all child nodes (and all attached components) with an `.onReceive()` method. It is important to note that events cannot be emitted up the scene graph. 


## Drag and Drop

Drag and drop functionality is easy to acheive with a Gesture Handler.

<iframe src='https://famous.org/examples/index.html?block=drag&detail=false&header=false' style="width:100%;height:600px; margin: 15px 0px 25px 0px" scrolling='no' class='code-block' allowtransparency='true'></iframe>

 Read more at the [Gesture Handler](gesture-handler.html) section.

## Input Elements and Drag

Here we demonstrate several event types working together including grabbing input from a `<textarea>`.

<iframe src='https://famous.org/examples/index.html?block=draginput&detail=false&header=false' style="width:100%;height:600px; margin: 15px 0px 25px 0px" scrolling='no' class='code-block' allowtransparency='true'></iframe>

Click the grey square and type something in to see it in action!



<!-- Handling user input is a central component of every non-trivial application.
Building applications in Famous is no exception.

User Input in Famous is being handled through DOM events. The general pattern
for listening to DOM events can be summarized as follows:

  - Assuming you want to listen for e.g. `click` events on a specific node,
    the node has to have a DOMElement attached to it.
  - The DOMElement needs to be instructed to add an appropriate event listener. This is being achieved by calling `node.addUIEvent('click')`.
  - Receive the events. The events are now being dispatched on the previously defined node and all its ancestors ("event bubbling"). You can either override the Node's `.onReceive()` method or add individual components. Adding custom components usually leads to more modularized and reusable code.

For a more detailed introduction to the Eventing System, see the introduction to [Program Events](program-events.html).

## Example

In the following example we are going to create a simple Hello World
Application demonstrating how to read from an `<input>` element and broadcast
the value changes of the form element.

    function NameInput() {
        Node.call(this);
        this._domElement = new DOMElement(this, {
            tagName: 'input',
            attributes: {
                placeholder: 'Your Name'
            }
        });
        this.setProportionalSize(1, 0.5);

        // Adding keyup as an UI Event allows the previously added DOMElement to
        // instruct the DOMRenderer to act accordingly.
        this.addUIEvent('keyup');
    }

    NameInput.prototype = Object.create(Node.prototype);
    NameInput.prototype.constructor = NameInput;

In order to receive the `keyup` DOM event, we override the Node's `onReceive`
method. We add the `keyup` UI event in the constructor. Adding a UI Event
to a node notifies all components that are currently attached to it.

The DOMElement, being a component, instructed the DOMRenderer to attach an
appropriate event listener to the respective element in the DOM. All subsequent
`keyup` events that are being emitted on this element are being forwarded
to the NameInput node through the Dispatch.

    NameInput.prototype.onReceive = function onReceive(type, ev) {
        // Check if the received event is the keyup event.
        if (type === 'keyup') {
            // Instead of reading the value from the DOMElement, we have access
            // to the DOMElement's current value through the event payload
            // itself.
            //
            // We globally emit (broadcast) the event to all nodes (the entire
            // scene graph). This means all nodes, including this node, will
            // receive the dispatched event.
            this.emit('name', ev.value);
        }
        // Equivalent to Node.prototype.receive.call(this, type, ev);
        // Used in order to allow potential components to receive the event.
        this.receive(type, ev);
    };

In order to display the input changes, we create another Node in order to
listen for `name` events and display them in an appropriate way.

We call this node "NameOutput".

    function NameOutput() {
        Node.call(this);
        this._domElement = new DOMElement(this);

        this
            .setProportionalSize(1, 0.5)
            .setAlign(0, 1)
            .setMountPoint(0, 1);
    }

    NameOutput.prototype = Object.create(Node.prototype);
    NameOutput.prototype.constructor = NameOutput;

The constructor function simply positions and sizes the DOMElement correctly to
be displayed underneath the NameInput.

    NameOutput.prototype.onReceive = function onReceive(type, ev) {
        if (type === 'name') {
            this._domElement.setContent('Hello, ' + ev + ' !');
        }
        this.receive(type, ev);
    };

In the NameOutput's `onReceive` method we listen for the globally dispatched
`name` event and set the event's payload as content.

Finally, we set up our scene graph:

    var scene = FamousEngine.createScene();
    scene.addChild(new NameInput());
    scene.addChild(new NameOutput());
 -->
