# Image Magick Notes

[Command-line documentation](https://imagemagick.org/script/command-line-processing.php)


Refer to frames within a gif using square brackets:
`magick 'images.gif[0]' image.png`
This supports ranges too:
`magick 'images.gif[0-3]' images.mng`


Convert the frames of a gif to individual pngs:
`magick level2_background.gif level2_background%d.png`

See [Output Filename](https://imagemagick.org/script/command-line-processing.php#output) documentation for details on the `%d` business.

Pad the backgrounds of several pngs with black border:
`magick level2_background?.png -background black -gravity center -extent 500x500 level2_background_pad.png`
