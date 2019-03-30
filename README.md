
<!-- README.md is generated from README.Rmd. Please edit that file -->

# ggthreed - 3d geoms and stats for ggplot2<img src="man/figures/logo-with-pause.gif" align="right"/>

[![Travis build
status](https://travis-ci.org/coolbutuseless/ggthreed.svg?branch=master)](https://travis-ci.org/coolbutuseless/ggthreed)
[![AppVeyor build
status](https://ci.appveyor.com/api/projects/status/github/coolbutuseless/ggthreed?branch=master&svg=true)](https://ci.appveyor.com/project/coolbutuseless/ggthreed)
![](https://img.shields.io/badge/lifecycle-alpha-orange.svg)

[`ggthreed`](https://github.com/coolbutuseless/ggthreed) is a collection
of [`ggplot2`](https://github.com/tidyverse/ggplot2) geoms which use the
[`threed`](https://github.com/coolbutuseless/threed) library.

What’s in the box:

  - `geom_threedpie()` for creating 3d pie charts.
      - **I am fully aware of the crimes against visualisation I am
        committing here.**
  - `stat_anaglyph()` for creating red-blue anaglyph images

## Installation

You can install from github

``` r
# install.packages("devtools")
devtools::install_github("coolbutuseless/threed")
devtools::install_github("coolbutuseless/ggthreed")
```

# Red/blue anaglyphs with `stat_anaglyph()`

**Grab your glasses and lower your expectations\!\!**

![](man/figures/glasses.jpg)

## Usage

  - Works with point, line and polygon geoms. Although “works” may be a
    strong term - the 3d effect is very weak even if you tweak all the
    parameters
  - It might work with other geoms, but no guarantees that it produces
    anything worth looking at.
  - parameters:
      - `zoffset`, `zscale` - for determining how `z` aesthetic
        influences position
      - `red`, `blue` - hex colours to use for red/blue
      - `switch` - switch position of the colours

## Example red/blue anaglyphs with points and lines.

``` r
ggplot(mtcars) +
  geom_point(aes(mpg, y = wt, z = disp), stat = 'anaglyph', alpha = 0.75) +
  theme_bw()
```

<img src="man/figures/README-unnamed-chunk-2-1.png" width="100%" />

``` r
ggplot(mtcars) +
  geom_line(aes(mpg, y = wt, z = disp, group = cyl), stat = 'anaglyph', alpha = 0.75) +
  theme_bw()
```

<img src="man/figures/README-unnamed-chunk-3-1.png" width="100%" />

# 3d pie charts with `geom_threedpie()`

## Usage

  - Requires only an `x` variable - which must be discrete.
  - Default stat is `count`
  - Adjustable parameters:
      - camera position
      - pie height
      - starting angle for first pie slice
      - tilt angle of pie relative to camera

## Issues/Limitations

  - `geom_threedpie()` overrides the aspect ratio of the plot it is
    displaying. This is a gigantic hack\! It means it is not possible to
    use `coord_fixed()` etc to change the plot aspec ratio.
  - The initial panel grid and x- and y-axes are still generated by the
    geom. Currently the only way to turn this off it to use
    `theme_void()`. Not sure how to disable this from within the `geom`.
  - Pie slice resolution is in increments of 2 degrees.

## Example - Simple Pie Chart

``` r
ggplot(mtcars) + 
  geom_threedpie(aes(x = as.factor(cyl))) + 
  theme_void() + 
  theme(legend.position = 'bottom')
```

<img src="man/figures/README-example-simple-1.png" width="500" />

## Example - Facetted Pie Chart

``` r
ggplot(diamonds)  +
  geom_threedpie(aes(as.factor(cut))) +
  facet_wrap(~clarity, labeller = label_both) +
  labs(title = "Distribution of Diamond Cuts by Clarity") +
  scale_fill_brewer(name = "Cut", palette = 'Set2') +
  theme_void()
```

<img src="man/figures/README-example-facet-1.png" width="100%" />

## Example - Behind the Scenes

  - This plot shows the polygons making up the pie.
  - Triangular polygons are rendered on top, and quadrilaterals are
    rendered for the side.
  - The `threed` library is used to rotate the assembled polygons and
    perform perspective projection.
  - By plotting the polygons in order of distance from the camera
    (furtherest polygons first), polygons which are behind others are
    hidden from view.
  - The pie has no bottom.
  - The quadrilaterals making up the side are the same colour as the
    top, but darkened by 15%.

<!-- end list -->

``` r
ggplot(mtcars) + 
  geom_threedpie(aes(x = as.factor(cyl)), alpha = 0.5) + 
  theme_void() + 
  theme(legend.position = 'bottom')
```

<img src="man/figures/README-example-behind-1.png" width="100%" />

## Example - Pie configuration

User adjustable:

  - pie height
  - pie tilt
  - start of first pie slice

![](man/figures/pie-anim.gif)
