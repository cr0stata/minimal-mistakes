---
layout: single
permalink: /research/testing/opencv/
toc: false
---
hello! 

Here you'll find an old project that use [OpenCV](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=&cad=rja&uact=8&ved=2ahUKEwj4kIqdleH3AhVxh_0HHePwCSYQFnoECBEQAQ&url=https%3A%2F%2Fopencv.org%2F&usg=AOvVaw0nLWFztJIlbNMAYoheT9Qm) integrated in HTML. 

Enjoy!

<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Opencv JS</title>
    <script async src="../../../assets/js/opencv/opencv.js" onload="openCvReady();"></script>
    <script src="../../../assets/js/opencv/utils.js"></script>
<body>
    <video  id="cam_input" height="480" width="640"></video>
    <canvas id="canvas_output"></canvas>
</body>

<script type="text/JavaScript">
function openCvReady() {
  cv['onRuntimeInitialized']=()=>{
    let video = document.getElementById("cam_input"); // video is the id of video tag
    navigator.mediaDevices.getUserMedia({ video: true, audio: false })
    .then(function(stream) {
        video.srcObject = stream;
        video.play();
    })
    .catch(function(err) {
        console.log("An error occurred! " + err);
    });
    let src = new cv.Mat(video.height, video.width, cv.CV_8UC4);
    let dst = new cv.Mat(video.height, video.width, cv.CV_8UC1);
    let gray = new cv.Mat();
    let cap = new cv.VideoCapture(cam_input);
    let faces = new cv.RectVector();
    let classifier = new cv.CascadeClassifier();
    let utils = new Utils('errorMessage');
    let faceCascadeFile = '../../../haarcascade_default.xml'; // path to xml
    utils.createFileFromUrl(faceCascadeFile, faceCascadeFile, () => {
    classifier.load(faceCascadeFile); // in the callback, load the cascade from file 
});
    const FPS = 100;
    function processVideo() {
        let begin = Date.now();
        cap.read(src);
        src.copyTo(dst);
        cv.cvtColor(dst, gray, cv.COLOR_RGBA2GRAY, 0);
        try{
            classifier.detectMultiScale(gray, faces, 1.1, 3, 0);
            console.log(faces.size());
        }catch(err){
            console.log(err);
        }
        for (let i = 0; i < faces.size(); ++i) {
            let face = faces.get(i);
            let point1 = new cv.Point(face.x, face.y);
            let point2 = new cv.Point(face.x + face.width, face.y + face.height);
            cv.rectangle(dst, point1, point2, [255, 0, 0, 255]);
        }
        cv.imshow("canvas_output", dst);
        // schedule next one.
        let delay = 1000/FPS - (Date.now() - begin);
        setTimeout(processVideo, delay);
}
// schedule first one.
setTimeout(processVideo, 0);
  };
}
</script>

</head>
</html>