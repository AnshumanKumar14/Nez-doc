# Nez Setup

## Setup

### Install as a submodule:

* create a `Monogame Cross Platform Desktop Project`
* clone or download the [Nez](https://github.com/prime31/Nez) repository
* add the `Nez.Portable/Nez.csproj` project to your solution and add a reference to it in your main project
* make your main Game class \(`Game1.cs` in a default project\) subclass [Nez.Core](https://github.com/prime31/Nez/blob/master/Nez.Portable/Core.cs)

If you intend to use any of the built in Effects or PostProcessors you should also copy or link the [DefaultContent/effects](https://github.com/prime31/Nez/tree/master/DefaultContent) folder into your projects [Content/nez/effects](https://github.com/prime31/Nez/tree/master/DefaultContent/effects) folder and the [DefaultContent/textures](https://github.com/prime31/Nez/tree/master/DefaultContent/textures) folder into `Content/nez/textures`. Be sure to set the Build Action to Content and enable the "Copy to output directory" property so they get copied into your compiled game. See the Nez.Samples csproj for an example on how to do this.

Note: if you get compile errors referencing a missing `project.assets.json` file run `msbuild Nez.sln /t:restore` in the root Nez folder to restore them.

### Install through [NuGet](https://www.nuget.org/):

Add [Nez](https://www.nuget.org/packages/Nez/) to your project's NuGet packages. Optionally add the [Nez.FarseerPhysics](https://github.com/prime31/Nez/tree/master/Nez.FarseerPhysics) and [Nez.Persistence](https://github.com/prime31/Nez/tree/master/Nez.Persistence) NuGet packages.

Installing through [NuGet](https://www.nuget.org/), the contents of the [DefaultContent](https://github.com/prime31/Nez/tree/master/DefaultContent) content folder is also included in the package. You will find them under `packages/Nez.{VERSION}/tools`.

All Nez shaders are compiled for OpenGL so be sure to use the DesktopGL template, not DirectX! Nez only supports OpenGL out of the box to keep things compatible across Android/iOS/Mac/Linux/Windows.

If you are developing a mobile application you will need to enable touch input by calling `Input.Touch.EnableTouchSupport()`.

## Samples Repository

You can find the samples repo [here](https://github.com/prime31/Nez-Samples). It contains a variety of sample scenes that demonstrate the basics of getting stuff done with Nez. [The wiki](https://github.com/prime31/Nez/wiki) also contains a few short examples. [This YouTube playlist](https://www.youtube.com/playlist?list=PLb8LPjN5zpx0ZerxdoVarLKlWJ1_-YD9M) also has a few relevant videos.

## Using Nez with FNA

Note that you have to install the required FNA native libs per the [FNA documentation](https://github.com/FNA-XNA/FNA/wiki/1:-Download-and-Update-FNA). Here is what you need to do to get up and running with Nez + FNA:

* clone this repo recursively
* open the Nez solution \(Nez/Nez.sln\) and build it. This will cause the NuGet packages to refresh.
* download/clone FNA
* open your game's project and add a reference to FNA and Nez.FNA
* \(optionally\) add references to Nez.FNA.ImGui or Nez.FNA.FarseerPhysics if you need them

The folder structure the cscproj files expect is something like this:

* TopLevelFolderHousingEverything
  * FNA
  * YourGameProject
  * Nez

