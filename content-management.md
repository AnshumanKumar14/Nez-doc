# Content management

[Nez](https://github.com/prime31/Nez) includes it's own content management system that builds on the MonoGame/FNA [ContentManager](https://github.com/FNA-XNA/FNA/blob/master/src/Content/ContentManager.cs) class. All content management goes through the [NezContentManager](https://github.com/prime31/Nez/blob/master/Nez.Portable/Assets/NezContentManager.cs) which is a subclass of [ContentManager](https://github.com/prime31/Nez/blob/master/Nez.Portable/Assets/NezContentManager.cs). The [debug console](https://github.com/prime31/Nez/blob/master/Nez.Portable/Debug/Console/DebugConsole.cs) has a 'assets' command that will log all scene or global assets so you will always know what is still in memory.

nez provides containers for global and per-scene content. You can also create your own [NezContentManager](https://github.com/prime31/Nez/blob/master/Nez.Portable/Assets/NezContentManager.cs) at any time if you need to manage some short-lived assets. You can also unload assets at any time via the `UnloadAsset<T>` method. Note that Effects should be unloaded with `UnloadEffect` since they are a special case.

## Global Content

There is a global [NezContentManager](https://github.com/prime31/Nez/blob/master/Nez.Portable/Assets/NezContentManager.cs) available via `Core.Content`. You can use this to load up assets that will survive the life of your application. Things like your fonts, shared atlases, shared sound effects, etc.

## Scene Content

Each scene has it's own [NezContentManager](https://github.com/prime31/Nez/blob/master/Nez.Portable/Assets/NezContentManager.cs) \(Scene.Content\) that you can use to load per-scene assets. When a new scene is set all of the assets from the previous scene will automatically be unloaded for you.

## Loading [Effects](https://github.com/prime31/Nez/tree/master/Nez.Portable/Graphics/Effects)

There are several ways to load [Effects](https://github.com/prime31/Nez/tree/master/Nez.Portable/Graphics/Effects) with Nez that are not present in MonoGame/FNA. These were added to make [Effect](https://github.com/prime31/Nez/tree/master/Nez.Portable/Graphics/Effects) management easier, especially when dealing with [Effects](https://github.com/prime31/Nez/tree/master/Nez.Portable/Graphics/Effects) that are subclasses of [Effect](https://github.com/FNA-XNA/FNA/blob/master/src/Graphics/Effect/Effect.cs) \(such as [AlphaTestEffect](https://github.com/FNA-XNA/FNA/blob/master/src/Graphics/Effect/StockEffects/AlphaTestEffect.cs) and [BasicEffect](https://github.com/FNA-XNA/FNA/blob/master/src/Graphics/Effect/StockEffects/BasicEffect.cs)\). All of the built-in Nez [Effects](https://github.com/prime31/Nez/tree/master/Nez.Portable/Graphics/Effects) can also be loaded easily. The available methods are:

* **LoadMonoGameEffect:** loads and manages any Effect that is built-in to MonoGame such as BasicEffect, AlphaTestEffect, etc
* **LoadEffect/LoadEffect:** loads an ogl/fxb effect directly from file and handles disposing of it when the ContentManager is disposed
* **LoadEffect\( string name, byte\[\] effectCode \):** loads an ogl/fxb effect directly from its bytes and handles disposing of it when the ContentManager is disposed
* **LoadNezEffect:** loads a built-in Nez effect. These are any of the `Effect` subclasses in the [Nez/Graphics/Effects](https://github.com/prime31/Nez/tree/master/Nez.Portable/Graphics/Effects) folder.

## Auto Generating Content Paths

Nez includes [a T4 template](https://github.com/prime31/Nez/blob/master/T4Templates/ContentPathGenerator.tt) that will generate a static `Nez.Content` class for you that contains the names of all of the files processed by the [Pipeline Tool](http://www.monogame.net/downloads/). This lets you change code like the following:

```csharp
// before using the ContentPathGenerator you have strings to represent your content
var tex = content.Load<Texture2D>( "Textures/Scene1/blueBird" );

// after using the ContentPathGenerator you will have compile-tile safety for your content
var tex = content.Load<Texture2D>( Nez.Content.Textures.Scene1.blueBird );
```

The big advantage to using it is that you will never have a reference to content that doesnt actually exist in your project. You get compile-time checking of all your content. Setup is as follows:

* copy the [ContentPathGenerator.tt](https://github.com/prime31/Nez/blob/master/T4Templates/ContentPathGenerator.tt) file into the root of your project \(you could place it elsewhere and then modify the `sourceFolder` variable in the file. For example, if using only precompiled XNB files in an FNA project you would set `sourceFolder = "Content/"`\)
* in the properties pane for the file set the "Custom Tool" to "TextTemplatingFileGenerator"
* right click the file and choose Tools -&gt; Process T4 Template to generate the Content class

## Async Loading

[NezContentManager](https://github.com/prime31/Nez/blob/master/Nez.Portable/Assets/NezContentManager.cs) provides asynchronous loading of assets as well. You can load a single asset or an array of assets via the `LoadAsync<T>` method. It takes a callback Action that will be called once the assets are loaded.

