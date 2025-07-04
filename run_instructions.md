
# Object Detection Using DeepStream on Jetson Nano

Welcome to the DeepStream object detection task! In this guide, you'll use NVIDIA's DeepStream SDK on a Jetson Nano to perform object detection on a video using the **SSD MobileNet V1** model.

#

## Pre-requisites

- **Jetson Nano** – L4T version 32.6.1 or 32.7.1  
- **JetPack Version** – 4.6 
- **DeepStream SDK** – 6.0  
- Familiarity with Docker (containers, images, running/committing, etc.)   
- Basic understanding of object detection and how DeepStream works   
- A little patience – trust the process! 


## Let's Get Started

### Step 1: Login to Jetson Nano

Open your Jetson Nano’s desktop environment.


### Step 2: Check for the DeepStream Docker Image

Run the following command to check if the DeepStream image is already available:

```bash
sudo docker images
```

Look for:

```
nvcr.io/nvidia/deepstream-l4t:6.0-triton
```

If it’s not listed, pull it using:

```bash
sudo docker pull nvcr.io/nvidia/deepstream-l4t:6.0-triton
```

Then, run the container (mount a working directory if needed):

```bash
sudo docker run -it --rm --runtime nvidia --network host \
-v /home/csed/Desktop/pose:/home/pose \
nvcr.io/nvidia/deepstream-l4t:6.0-triton
```

---

### Step 3: Navigate to the Model Directory

Inside the container, navigate to:

```bash
cd /opt/nvidia/deepstream/deepstream/samples/trtis-model-repo
```

We’ll use the **SSD MobileNet V1 (COCO)** model.

Download the model from [TensorFlow 1 Detection Model Zoo](https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/tf1_detection_zoo.md)

Use `wget` to download the model `.tar.gz` and extract it:

```bash
wget <model-tar-url>
tar -xvf <model-tar-file>
```

Then move the `frozen_inference_graph.pb` to a new subfolder named `ssd_mobilenet_v1_coco/1` and rename it as `model.graphdef`.

---

### Step 4: Configure the Model

Edit `config.pbtxt` in the model folder:

- Comment out any line referring to `frozen_inference_graph.pb`
- Ensure the model path and filename match what you renamed

---

### Step 5: Create App Config Files

Navigate to the `configs` directory:

```bash
cd /opt/nvidia/deepstream/deepstream/samples/config
mkdir deepstream-app-trtis
cd deepstream-app-trtis
```

Copy sample config files from the `deepstream-app-triton` folder:

```bash
cp ../deepstream-app-triton/sample_* .
```

Update the `config.txt`:

- `input-dims=300;300;3`
- `model-name=ssd_mobilenet_v1_coco`
- `model-repo=<full-path-to-trtis-model-repo>`
- `model-version=1`

Update the `source.txt`:

```ini
[source0]
enable=1
type=3     # For video file input

[sink0]
enable=1
type=4     # For RTSP output stream
```

---

### Step 6: Run DeepStream 🎥

Now run the application:

```bash
deepstream-app -c source.txt
```

Give it a moment... ⏳

---

## View the Output

To watch your RTSP stream, open a video player (like **VLC**) or browser that supports RTSP and enter the link:

```plaintext
rtsp://<jetbot-ip>:8554/ds-stream
```

For example:

```plaintext
rtsp://192.168.1.42:8554/ds-stream
```

You’ll see your video being processed live — objects will appear with **bounding boxes** and **class labels**. 🟩📦

---

## That’s It!

You've successfully run real-time object detection using DeepStream on a Jetson Nano! 🎉

Feel free to experiment with:
- Different models
- Custom video inputs
- Sink types (e.g., local file saving, screen display)

