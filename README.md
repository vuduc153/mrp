# Cruzr/Tiago Mobile Robotic Telepresence (MRP)

This repository serves as the main index for the code used in the Cruzr/Tiago MRP system. The system consists of the following components:

- An [Android app](https://github.com/CHRI-Lab/mrp-app) running on the robot's tablet for video and audio communication.
- A [web-based client](https://github.com/CHRI-Lab/mrp-client) that provides a control interface for the robot.
- A ROS system running on the robot. This should include at least the [navigation](http://wiki.ros.org/navigation) stack and [rosbridge_suite](http://wiki.ros.org/rosbridge_suite). The system should also run the [robot_pose_publisher](http://wiki.ros.org/robot_pose_publisher) node. **Note**: The Tiago robot already has this node running by default, but the JavaScript library used in the web client relies on an older version of the `robot_pose_publisher` node with a different message format. To resolve this, clone the `robot_pose_publisher_legacy` package from this repository and run it as a new node. This will publish messages compatible with the JavaScript code on the `robot_pose_legacy` topic.
- An ASR (Automatic Speech Recognition) system for speech transcription. We use a forked version of the real-time [whisper-streaming](https://github.com/vuduc153/whisper_streaming) repository, which includes improved silence detection. The code in the `multi-server` branch slightly modifies the launch parameters to better suit our setup.
- Visual Language Maps (VLMaps) for open-vocabulary entity indexing. This [forked repository](https://github.com/CHRI-Lab/vlmaps) provides the code to serve VLMaps through an API server.

For detailed instructions on setting up and using each component, refer to the README of the corresponding repository. To set up the MRP system as a whole, follow these steps:

1. **Install and run the Android app** on the robot's tablet. Once the app is running, the device's IP address will be displayed.
2. **Configure and launch all the necessary ROS packages**. Ensure that they are properly communicating with the robot’s ROS system. **Note**: Whenever the robot is restarted, the navigation stack must be initialized with an initial pose. The easiest way to do this is through rviz.
3. **Launch two whisper-streaming processes**. Each process will handle an audio stream from either the remote or local environment. The web client expects the servers to run on ports 43006 and 43007. You can launch the processes with the following command:
    ```
    python3 whisper_server.py --backend faster-whisper --language en --min-chunk-size 2 --silence-size 2 --model base.en --silence-threshold 0.01 --port
    ```
4. **Launch the VLMaps server** by following the instructions in its repository.
5. **Launch the web client server**. From the client interface, you can connect to other system components using their host device IPs.
