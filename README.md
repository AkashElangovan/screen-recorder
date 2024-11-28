# screen-recorder WIP


import 'package:flutter/material.dart';
import 'package:google_ml_kit/google_ml_kit.dart';
import 'package:image_picker/image_picker.dart';

void main() {
  runApp(TextHighlighterApp());
}

class TextHighlighterApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: TextRecognitionScreen(),
    );
  }
}

class TextRecognitionScreen extends StatefulWidget {
  @override
  _TextRecognitionScreenState createState() => _TextRecognitionScreenState();
}

class _TextRecognitionScreenState extends State<TextRecognitionScreen> {
  String recognizedText = '';
  final ImagePicker _picker = ImagePicker();

  Future<void> recognizeTextFromImage() async {
    final XFile? image = await _picker.pickImage(source: ImageSource.gallery);
    if (image == null) return;

    final InputImage inputImage = InputImage.fromFilePath(image.path);
    final TextRecognizer textRecognizer = GoogleMlKit.vision.textRecognizer();

    try {
      final RecognizedText result = await textRecognizer.processImage(inputImage);
      setState(() {
        recognizedText = result.text;
      });
    } catch (e) {
      print('Error: $e');
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('OCR and Highlight')),
      body: Column(
        children: [
          ElevatedButton(
            onPressed: recognizeTextFromImage,
            child: Text('Pick and Recognize Text'),
          ),
          Expanded(
            child: SingleChildScrollView(
              child: Text(recognizedText, style: TextStyle(fontSize: 16)),
            ),
          ),
        ],
      ),
    );
  }
}
