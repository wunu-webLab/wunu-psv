# Introduction to plugins

Plugins are used to add new functionalities to Photo Sphere Viewer. They can access all internal APIs of the viewer as well as the Three.js renderer to make the viewer even more awesome.

## Import official plugins

Official plugins (listed on the left menu) are available in various `@photo-sphere-viewer/***-plugin` packages. Some plugins also have an additional CSS file.

**Example for the Markers plugin:**

::::: tabs

:::: tab Direct import

```html
<!-- base imports of PSV and dependencies -->

<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/@photo-sphere-viewer/markers-plugin/index.min.css" />

<script src="https://cdn.jsdelivr.net/npm/@photo-sphere-viewer/markers-plugin/index.min.js"></script>
```

::::

:::: tab ES import

```js
import { MarkersPlugin } from '@photo-sphere-viewer/markers-plugin';
```

::: tip Stylesheet
Import `@photo-sphere-viewer/markers-plugin/index.css` with the prefered way depending on your tooling.
:::

::::

:::::

## Using a plugin

All plugins consists of a JavaScript class which must be provided to the `plugins` array. Some plugins will also take a configuration object provided in a nested array.

```js
const viewer = new PhotoSphereViewer.Viewer({
    plugins: [
        PhotoSphereViewer.GyroscopePlugin,
        [PhotoSphereViewer.MarkersPlugin, {
            option1: 'foo',
            option2: 'bar',
        }],
    ],
});
```

After initialization the plugin instance can be obtained with the `getPlugin` method, allowing to call methods on the plugin and subscribe to events.

```js
const markersPlugin = viewer.getPlugin(PhotoSphereViewer.MarkersPlugin);

markersPlugin.addMarker(/* ... */);

markersPlugin.addEventListener('select-marker', () => {
    /* ... */
});
```

Some plugins allow their configuration to be modified after init with the `setOption()` and `setOptions()` methods. The updatable configuration properties are documented on each plugin page.

```js
markersPlugin.setOption('gotoMarkerSpeed', '3rpm');

markersPlugin.setOptions({
    gotoMarkerSpeed: '3rpm',
    clickEventOnMarker: true,
});
```
