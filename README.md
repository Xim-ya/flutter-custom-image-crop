# custom_image_crop

An Image cropper that is customizable

<img src="./example/screenshots/customimagecrop.gif" alt="customcropcircle" height="320"/>
<img src="./example/screenshots/customcropsquare.png" alt="customcropsquare" height="320"/>
<img src="./example/screenshots/customcropcircle.png" alt="customcropcircle" height="320"/>

# CustomImageCrop

```
CustomImageCrop(
              cropController: controller,
              image: const AssetImage('assets/test.png'),
            ),
```

You can provide the image using any Imageprovider.

## Parameters

### required image
The image that needs to be cropped

### cropController
The controller used to adjust the image and crop it.

### overlayColor
The color above the image that will be cropped

### backgroundColor
The color behind the image. This color will also be used when there are gaps/empty space after the cropping

### shape
The shape of the cropping path.

### cropPercentage
How big the crop should be in regards to the width and height available to the cropping widget.

### drawPath
How the border of the crop should be painted. default DottedCropPathPainter.drawPath and SolidCropPathPainter.drawPath are provided, but you can create/provide any CustomPaint.

# Controller Methods

## addTransition

`void addTransition(CropImageData transition)`

Add the position, angle and scale to the current state. This can be used to adjust the image with sliders, buttons, etc.

## setData

`void setData(CropImageData data)`

Set the position, angle and scale to the specified values. This can be used to center the image by pressing a button for example.

## reset
`void reset()`

Reset the image to its default state

## onCropImage
`Future<MemoryImage> onCropImage()`

Crops the image in its current state, this will return a MemoryImage that contains the cropped image

# Example

See example/lib

```
class MyHomePage extends StatefulWidget {
  final String title;

  MyHomePage({
    required this.title,
    Key? key,
  }) : super(key: key);

  @override
  _MyHomePageState createState() => _MyHomePageState();
}

class _MyHomePageState extends State<MyHomePage> {
  late CustomImageCropController controller;

  @override
  void initState() {
    super.initState();
    controller = CustomImageCropController();
  }

  @override
  void dispose() {
    controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text(widget.title),
        brightness: Brightness.dark,
      ),
      body: Column(
        children: [
          Expanded(
            child: CustomImageCrop(
              cropController: controller,
              image: const AssetImage('assets/test.png'), // Any Imageprovider will work, try with a NetworkImage for example...
            ),
          ),
          Row(
            children: [
              IconButton(icon: const Icon(Icons.refresh), onPressed: controller.reset),
              IconButton(icon: const Icon(Icons.zoom_in), onPressed: () => controller.addTransition(CropImageData(scale: 1.33))),
              IconButton(icon: const Icon(Icons.zoom_out), onPressed: () => controller.addTransition(CropImageData(scale: 0.75))),
              IconButton(icon: const Icon(Icons.rotate_left), onPressed: () => controller.addTransition(CropImageData(angle: -pi / 4))),
              IconButton(icon: const Icon(Icons.rotate_right), onPressed: () => controller.addTransition(CropImageData(angle: pi / 4))),
              IconButton(
                icon: const Icon(Icons.crop),
                onPressed: () async {
                  final image = await controller.onCropImage();
                  if (image != null) {
                    Navigator.of(context).push(MaterialPageRoute(builder: (BuildContext context) => ResultScreen(image: image)));
                  }
                },
              ),
            ],
          ),
          SizedBox(height: MediaQuery.of(context).padding.bottom),
        ],
      ),
    );
  }
}
```
