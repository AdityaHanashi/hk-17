# HK-17
it is for the overview status of our project 
<br> 
at present we are just training the model for the pothole detection 
<br>
the model is trained for the image processing now we are processing it for mp.4 i,e video format 
<br>
around 9:30 we are updating the mp4 format so that video can also be uploaded to our server 
<br>
at now we finished our user interface in the flutter so thats our interface and a part of backend is ready and we are going to next that is databse
<br>
<br>
now at present i,e Nov 9th 4:27 am we  finished with the :
<br>
<ol>
  <li>web server with local host</li>
  <li>datasets {real datasets}</li>
  <li>fetching datasets location </li>
</ol>
<br>
the lead or the logic for the model that we have trained is : 
<br>
import 'dart:io';
import 'package:flutter/material.dart';
import 'package:image/image.dart' as img;
import 'package:tflite_flutter/tflite_flutter.dart';
import 'package:path_provider/path_provider.dart';

class YOLOImageDetection extends StatefulWidget {
  const YOLOImageDetection({super.key});

  @override
  State<YOLOImageDetection> createState() => _YOLOImageDetectionState();
}

class _YOLOImageDetectionState extends State<YOLOImageDetection> {
  Interpreter? _interpreter;
  String _resultText = "Processing...";

  @override
  void initState() {
    super.initState();
    _loadModelAndRun();
  }

  Future<void> _loadModelAndRun() async {
    try {
    
      _interpreter = await Interpreter.fromAsset('bestn2.tflite');

   
      final imageFile = File('assets/img90.jpg');
      final image = img.decodeImage(await imageFile.readAsBytes());

      if (image == null) {
        setState(() => _resultText = "Failed to load image.");
        return;
      }

    
      final resized = img.copyResize(image, width: 640, height: 640);
      final input = List.generate(1,
          () => List.generate(640, () => List.generate(640, (_) => List.filled(3, 0.0))));

      
      for (int y = 0; y < 640; y++) {
        for (int x = 0; x < 640; x++) {
          final pixel = resized.getPixel(x, y);
          input[0][y][x][0] = img.getRed(pixel) / 255.0;
          input[0][y][x][1] = img.getGreen(pixel) / 255.0;
          input[0][y][x][2] = img.getBlue(pixel) / 255.0;
        }
      }


      var output = List.filled(1 * 25200 * 85, 0.0).reshape([1, 25200, 85]);

 
      _interpreter!.run(input, output);

     
      setState(() {
        _resultText = "Detection complete. Found objects = ${output[0].length}";
      });
    } catch (e) {
      setState(() => _resultText = "Error: $e");
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text("YOLO Image Detection"),
        backgroundColor: Colors.indigo,
      ),
      body: Center(
        child: Padding(
          padding: const EdgeInsets.all(20.0),
          child: Text(
            _resultText,
            textAlign: TextAlign.center,
            style: const TextStyle(fontSize: 18),
          ),
        ),
      ),
    );
  }
}
<br>
this was running and training when the web server was being created 
<br>
