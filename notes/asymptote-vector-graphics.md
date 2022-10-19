# Asymptote vector graphics language

https://asymptote.sourceforge.io/

Tutorial: https://asymptote.sourceforge.io/asymptote_tutorial.pdf

Source Code: https://github.com/vectorgraphics/asymptote (often the best reference to things not well documented)

Quick Reference Card: https://asymptote.sourceforge.io/asyRefCard.pdf

## Installing on Mac

```bash
brew install asymptote
brew install --cask mactex # Required for some dependencies like pdflatex
```

## Run on the command line

```bash
asy -V -f pdf test.asy
```

## Sizing
Seems like you can either specify the unit size explicitly, e.g.:

```
unitsize(1cm);
```

In that case, a coordinate delta of 1 will correspond to 1cm in the rendered drawing.

Or you can set the size of the final document, e.g.:

```
size(10cm);
```

and (I think) in that case, it will figure out what the unit size should be to make the drawing fit within this size. So, say, in your code you have something positioned at x coordinate 100 and something else at -100, it will set up the unit size such that [-100, 100] fits within 10cm. 

## Transforms
Per [the docs](https://asymptote.sourceforge.io/doc/Transforms.html), "Transforms can be applied to pairs, guides, paths, pens, strings, transforms, frames, and pictures by multiplication (via the binary operator \*) on the left..." e.g. 

```
scale(1.5)*"$\theta$"
```
scales the string with the symbol theta by a factor of 1.5. 

## Trig functions
All the standard trig and other math functions are supported ([docs](https://asymptote.sourceforge.io/doc/Mathematical-functions.html)). For trig functions specifically, they take radians but have variants e.g. `Sin()`, `Cos()` (vs `sin()`, `cos()`) that take degrees.

## Map 
Returns the array formed by applying a given function to all elements of a given array. 

```
pair[] points = {... some list of points ...};

// Get the x coordinates by mapping the function xpart() to the points array. 
real[] xs = map(xpart, points);
// Get the y coordinates by mapping the function ypart() to the points array. 
real[] ys = map(ypart, points);
```

## Labels
The `label()` function takes some text (which might have a transform applied to it), a path to which the label applies, and a compass direction which specifies where the label should appear relative to the path. 

```
// Horizontal label
label("w$^\prime$", (x1, y1)--(x2, y2), NE);

// Vertical label
label(rotate(90)*"h$^\prime$", (x1, y1)--(x2, y2), W);
```

Note that labels can have Latex math expressions between `$`s.

## Markers
The [`markers` module](https://asymptote.sourceforge.io/doc/markers.html) provides routines for marking paths and angles. 

To mark an angle defined by 3 points:

```
markangle(
	"$\theta$", // label 
	radius=60, // radius of the arc
	(x1, y1), // point 1
	(x2, y2), // point 2
	(x3, y3), // point 3
	ArcArrow(1mm),
	red
);
```

## Leaving some margin around the edges
I don't really understand all the concepts in the [frames and pictures docs](https://asymptote.sourceforge.io/doc/Frames-and-pictures.html) but at the end of the day, adding this to the end of a program seems to do the right thing (create a 0.25cm margin around the edges):

```
shipout(bbox(0.25cm));
```
