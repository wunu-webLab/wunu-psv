# Equirectangular tiles

<Badges module="equirectangular-tiles-adapter"/>

::: module
Reduce the initial loading time and used bandwidth by slicing big equirectangular panoramas into many smaller tiles.

This adapter is available in the [@photo-sphere-viewer/equirectangular-tiles-adapter](https://www.npmjs.com/package/@photo-sphere-viewer/equirectangular-tiles-adapter) package.
:::

```js
const viewer = new PhotoSphereViewer.Viewer({
    adapter: PhotoSphereViewer.EquirectangularTilesAdapter,
    panorama: {
        width: 12000,
        cols: 16,
        rows: 8,
        baseUrl: 'panorama_low.jpg',
        tileUrl: (col, row) => {
            return `panorama_${col}_${row}.jpg`;
        },
    },
});
```

## Example

::: code-demo

```yaml
title: PSV Equirectangular Tiles Demo
packages:
    - name: equirectangular-tiles-adapter
      imports: EquirectangularTilesAdapter
```

```js
const baseUrl = 'https://photo-sphere-viewer-data.netlify.app/assets/';

const viewer = new PhotoSphereViewer.Viewer({
    container: 'viewer',
    adapter: PhotoSphereViewer.EquirectangularTilesAdapter,
    panorama: {
        width: 6656,
        cols: 16,
        rows: 8,
        baseUrl: `${baseUrl}sphere-small.jpg`,
        tileUrl: (col, row) => {
            const num = row * 16 + col + 1;
            return `${baseUrl}sphere-tiles/image_part_${('000' + num).slice(-3)}.jpg`;
        },
    },
    caption: 'Parc national du Mercantour <b>&copy; Damien Sorel</b>',
    loadingImg: baseUrl + 'loader.gif',
    touchmoveTwoFingers: true,
    mousewheelCtrlKey: true,
});
```

:::

## Configuration

#### `baseBlur`

-   type: `boolean`
-   default: `true`

Applies a 1px blur to the base image (option `baseUrl`).

#### `showErrorTile`

-   type: `boolean`
-   default: `true`

Shows a warning sign on tiles that cannot be loaded.

#### `resolution`

-   type: `number`
-   default: `64`

The number of faces of the sphere geometry used to display the panorama, higher values can reduce deformations on straight lines at the cost of performances.

_Note: the actual number of faces is `resolution² / 2`._

## Panorama options

When using this adapter, the `panorama` option and the `setPanorama()` method accept an object to configure the tiles.

You may choose to provide a single tiles configuration or multiple configurations which will be applied at different zoom levels, this allows to serve files adapted to the current zoom level and achieve very high resolutions without consuming too much bandwidth.

:::: tabs

::: tab Single level

#### `width` (required)

-   type: `number`

Total width of the panorama, the height is always width / 2.

#### `cols` (required)

-   type: `number`

Number of columns, must be power of two (4, 8, 16, 32, 64) and the maximum value is 64.

#### `rows` (required)

-   type: `number`

Number of rows, must be power of two (2, 4, 8, 16, 32) and the maximum value is 32.

#### `tileUrl` (required)

-   type: `function: (col, row) => string`

Function used to build the URL of a tile.
If the function returns `null` the corresponding tile will not be loaded.

#### `baseUrl` (recommended)

-   type: `string`

URL of a low resolution complete panorama image to display while the tiles are loading.

#### `basePanoData`

-   type: `object | function<Image, object>`

Panorama configuration associated to low resolution first image, following the same format as [`panoData` configuration object](../config.md#panodata).

:::

::: tab Multiple levels

#### `levels` (required)

-   type: `array`

Array of available tiles configurations. Each element is an object with `width`, `cols` and `rows` (see "Single level") as well as `zoomRange`, an array containing the minimum and the maximum zoom level. **The levels must be ordered and cover the whole 0-100 zoom range.**

```js
levels: [
    {
        width: 6144,
        cols: 16,
        rows: 8,
        zoomRange: [0, 30],
    },
    {
        width: 12288,
        cols: 32,
        rows: 16,
        zoomRange: [30, 70],
    },
    {
        width: 24576,
        cols: 64,
        rows: 32,
        zoomRange: [70, 100],
    },
]
```

#### `tileUrl` (required)

-   type: `function: (col, row, level) => string`

Function used to build the URL of a tile.
If the function returns `null` the corresponding tile will not be loaded.

#### `baseUrl` (recommended)

-   type: `string`

URL of a low resolution complete panorama image to display while the tiles are loading.

#### `basePanoData`

-   type: `object | function<Image, object>`

Panorama configuration associated to low resolution first image, following the same format as [`panoData` configuration object](../config.md#panodata).

:::

::::

## Preparing the panorama

The tiles can be easily generated using [ImageMagick](https://imagemagick.org) tool.

Let's say you have a 12.000x6.000 pixels panorama you want to split in 16 columns and 8 rows, use the following command:

```
magick.exe panorama.jpg \
  -crop 750x750 -quality 95 \
  -set filename:tile "%[fx:page.x/750]_%[fx:page.y/750]" \
  -set filename:orig %t \
  %[filename:orig]_%[filename:tile].jpg
```

You can also use this [online tool](https://pinetools.com/split-image).

::: tip Performances
It is recommanded to not exceed tiles with a size of 1024x1024 pixels, thus limiting the maximum panorama size to 65.536x32.768 pixels (2 Gigapixels).
:::
