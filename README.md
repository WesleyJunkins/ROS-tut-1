This is the first tutorial in communicating using ROS through a web browser.
The package, roslib, should be installed using npm install roslib.
This program communicates using ROS over the rosbridge.

Followed along with this tutorial: https://wiki.ros.org/roslibjs/Tutorials/BasicRosFunctionality

<><><>
General overview of the ROS construct:
- We have a DDS (Data Distribution Service), which is a way of code communicating with each other through a pipeline.
- We also have Nodes, which are simply executed code files that use ROS functionality. These are usually developed in C++ and Python.
- Nodes use the DDS to send messages between them.
- Nodes can communicate with each other in the following ways:
    - Publisher/Subscriber method: communication pipelines between nodes called topics are established. One node publishes to the topic while other nodes subscribe to the topic.
      An example of this is a camera.py node being the publisher while the detect_objects.py node is the subscriber. Camera.py sends pictures by publishing a message through a topic named /sendPics, for example.
      The detect_objects.py node is subscribed to the /sendPics topic, so it receives the message that camera.py publishes to that topic.
      One publisher could send messages to multiple subscribers. 
    - Services method: imagine you have a node called survey.py. Survey.py is a service client, and it sends a request to to turn a robot's camera 45 degrees. This request is received by the camera_mover.py node.
      The camera_mover.py node is a service server, which will complete the request and send a response back to the service client, survey.py. For example, camera_mover.py might respond by sending back the picture it took after turning to the requested angle.
    - Actions method: imagine we have a node called way_point_nav.py that wants to command a robot to travel to a certain latitude and longitude and take a picture. We also have an action server node called
      controller.py which will process the goal, perform the action, and send progress updates to the client while performing the requested action. For example, controller.py may send back data on how far away the robot is from the destination. These progress updates are called feedback. The action server node will continue to send feedback back to the client node until the goal is reached. The action server takes a picture at the final destination and sends it back to the client, for example, which is called the result of the action. 
- Nodes also have a parameter feature. Say we have two robots that are exactly the same in every way, except one has wheels with a larger radius. Instead of rewriting nodes for such a simple difference and having
  to recompile our project, we can simply use node parameters, which can be adjusted to tweak specific values inside a node. Parameters can be modified by a user or other nodes. For example, we could set wheel_radius = 0.5 when calling our node calc_velocity.py.
- When you want to collect data from a robot, you will usually create a bag file, which subscribes to multiple topics and records the data as it comes in. These bag files can be played back later and the messages
  in them will be published over the same corresponding topic names.
- ROS code is organized in packages. These packages will contain all the code for a particular robot functionality. 
<><><>

-------
-------
-------

In the simple.html file:

<script type="text/javascript"
        src="https://cdn.jsdelivr.net/npm/eventemitter2@6.4.9/lib/eventemitter2.min.js"></script>
<script type="text/javascript" src="https://cdn.jsdelivr.net/npm/roslib@1/build/roslib.min.js"></script>

These lines import the things we need for the program. They use the Robot Web Tools CDN.

-------

  var ros = new ROSLIB.Ros({
    url : 'ws://localhost:9090'
  });

This creates a ROS node and connects it to localhost 9090.

-------

  ros.on('XXX', function() {
    console.log('XXX');
  });

These are listeners. They listen for the specified event and run a function when those events happen.

-------

var cmdVel = new ROSLIB.Topic({
    ros: ros,
    name: '/cmd_vel',
    messageType: 'geometry_msgs/Twist'
});

This creates a new ros topic and assigns to it the ros object from earlier, a name, and a message type.

-------

var twist = new ROSLIB.Message({
    linear: {
        x: 0.1,
        y: 0.2,
        z: 0.3
    },
    angular: {
        x: -0.1,
        y: -0.2,
        z: -0.3
    }
});
cmdVel.publish(twist);

This creates a new ros message and publishes it to the topic.
In ROS, you have topics, which are channels between devices.
On those topics, you pass messages to and from devices.
One device is a subscriber while the other is a publisher.
Devices are either listening on a topic or publishing to a topic.

-------

  var listener = new ROSLIB.Topic({
    ros : ros,
    name : '/listener',
    messageType : 'std_msgs/String'
  });

  listener.subscribe(function(message) {
    console.log('Received message on ' + listener.name + ': ' + message.data);
    listener.unsubscribe();
  });

This creates a new ros topic called listener and subscribes to that topic. It also defines a callback function which defines what to do when something is published on the listener topic.

-------

  var addTwoIntsClient = new ROSLIB.Service({
    ros : ros,
    name : '/add_two_ints',
    serviceType : 'rospy_tutorials/AddTwoInts'
  });

This creates a new ros service named addTwoIntsClient.

-------

  var request = new ROSLIB.ServiceRequest({
    a : 1,
    b : 2
  });

  addTwoIntsClient.callService(request, function(result) {
    console.log('Result for service call on '
      + addTwoIntsClient.name
      + ': '
      + result.sum);
  });

Whereas a topic is called using a message object, a service is called using a serviceRequest.
We have to create the service and then call the service using a service request and service call.

-------














