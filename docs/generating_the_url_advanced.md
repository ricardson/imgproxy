# Generating the URL (Advanced)

This guide describes the advanced URL format that allows the use of all the imgproxy features. Read our [Generating the URL (Basic)](./generating_the_url_basic.md) guide to learn about the _basic_ URL format that is compatible with imgproxy 1.x.

### Format definition

The advanced URL should contain the signature, processing options, and encoded source URL, like this:

```
/%signature/%processing_options/%encoded_url.%extension
```

Check out the [example](#example) at the end of this guide.

#### Signature

Signature protects your URL from being altered by an attacker. It is highly recommended to sign imgproxy URLs in production.

Once you set up your [URL signature](./configuration.md#url-signature), check out the [Signing the URL](./signing_the_url.md) guide to know how to sign your URLs. Otherwise, use any string here.

#### Processing options

Processing options should be specified as URL parts divided by slashes (`/`). Processing option has the following format:

```
%option_name:%argument1:%argument2:...:argumentN
```

The list of processing options does not define imgproxy's processing pipeline. Instead, imgproxy already comes with a specific, built-in image processing pipeline for the maximum performance. Read more about it in the [About processing pipeline](./about_processing_pipeline.md) guide.

imgproxy supports the following processing options:

##### Resize

`resize:%resizing_type:%width:%height:%enlarge`
`rs:%resizing_type:%width:%height:%enlarge`

Meta-option that defines the [resizing type](#resizing-type), [width](#width), [height](#height), and [enlarge](#enlarge). All arguments are optional and can be omited to use their default values.

##### Size

`size:%width:%height:%enlarge`
`s:%width:%height:%enlarge`

Meta-option that defines the [width](#width), [height](#height), and [enlarge](#enlarge). All arguments are optional and can be omited to use their default values.

##### Resizing type

`resizing_type:%resizing_type`
`rt:%resizing_type`

Defines how imgproxy will resize the source image. Supported resizing types are:

* `fit`: resizes the image while keeping aspect ratio to fit given size;
* `fill`: resizes the image while keeping aspect ratio to fill given size and cropping projecting parts;
* `crop`: crops the image to a given size.

Default: `fit`

##### Width

`width:%width`
`w:%width`

Defines the width of the resulting image. When set to `0`, imgproxy will calculate the resulting width using the defined height and source aspect ratio. When set to `0` and the `crop` resizing type is used, imgproxy will use the full width of the source image.

Default: `0`

##### Height

`height:%height`
`h:%height`

Defines the height of the resulting image. When set to `0`, imgproxy will calculate resulting height using the defined width and source aspect ratio. When set to `0` and `crop` resizing type is used, imgproxy will use the full height of the source image.

Default: `0`

##### Enlarge

`enlarge:%enlarge`
`el:%enlarge`

If set to `0`, imgproxy will not enlarge the image if it is smaller than the given size. With any other value, imgproxy will enlarge the image.

Default: `0`

##### Gravity

`gravity:%gravity`
`g:%gravity`

When imgproxy needs to cut some parts of the image, it is guided by the gravity. The following values are supported:

* `no`: north (top edge);
* `so`: south (bottom edge);
* `ea`: east (right edge);
* `we`: west (left edge);
* `ce`: center;
* `sm`: smart. `libvips` detects the most "interesting" section of the image and considers it as the center of the resulting image;
* `fp:%x:%y`: focus point. `x` and `y` are floating point numbers between 0 and 1 that define the coordinates of the center of the resulting image. Treat 0 and 1 as right/left for `x` and top/bottom for `y`.

Default: `ce`

##### Background

`background:%R:%G:%B`
`bg:%R:%G:%B`

`background:%hex_color`
`bg:%hex_color`

When set, imgproxy will fill the resulting image background with the specified color. `R`, `G`, and `B` are red, green and blue channel values of the background color (0-255). `hex_color` is a hex-coded value of the color. Useful when you convert an image with alpha-channel to JPEG.

With no arguments provided, disables any background manipulations.

Default: disabled

##### Blur

`blur:%sigma`
`bl:%sigma`

When set, imgproxy will apply the gaussian blur filter to the resulting image. `sigma` defines the size of a mask imgproxy will use.

Default: disabled

##### Sharpen

`sharpen:%sigma`
`sh:%sigma`

When set, imgproxy will apply the sharpen filter to the resulting image. `sigma` the size of a mask imgproxy will use.

As an approximate guideline, use 0.5 sigma for 4 pixels/mm (display resolution), 1.0 for 12 pixels/mm and 1.5 for 16 pixels/mm (300 dpi == 12 pixels/mm).

Default: disabled

##### Preset

`preset:%preset_name1:%preset_name2:...:%preset_nameN`
`pr:%preset_name1:%preset_name2:...:%preset_nameN`

Defines a list of presets to be used by imgproxy. Feel free to use as many presets in a single URL as you need.

Read more about presets in the [Presets](./presets.md) guide.

Default: empty

##### Format

`format:%extension`
`f:%extension`
`ext:%extension`

Specifies the resulting image format. Alias for [extension](#extension) URL part.

Default: `jpg`

#### Encoded URL

The source URL should be encoded with URL-safe Base64. The encoded URL can be split with `/` for your needs.

#### Extension

Extension specifies the format of the resulting image. At the moment, imgproxy supports only `jpg`, `png` and `webp`, them being the most popular and useful image formats on the Web.

The extension part can be omitted. In this case, if the format is not defined by processing options, imgproxy will use `jpg` by default. You can also [enable WebP support detection](./configuration.md#webp-support-detection) to use it as default resulting format when possible.

### Example

Signed imgproxy URL that uses `sharp` preset, resizes `http://example.com/images/curiosity.jpg` to fill `300x400` area with smart gravity without enlarging, and then converts the image to `png`:

```
http://imgproxy.example.com/AfrOrF3gWeDA6VOlDG4TzxMv39O7MXnF4CXpKUwGqRM/preset:sharp/resize:fill:300:400:0/gravity:sm/aHR0cDovL2V4YW1w/bGUuY29tL2ltYWdl/cy9jdXJpb3NpdHku/anBn.png
```

The same URL with shortcuts will look like this:

```
http://imgproxy.example.com/AfrOrF3gWeDA6VOlDG4TzxMv39O7MXnF4CXpKUwGqRM/pr:sharp/rs:fill:300:400:0/g:sm/aHR0cDovL2V4YW1w/bGUuY29tL2ltYWdl/cy9jdXJpb3NpdHku/anBn.png
```
