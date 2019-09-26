# Nez Core

## [Nez Core](https://github.com/prime31/Nez/blob/master/Nez.Portable/Core.cs)

The root class in the Nez world is the [Core](https://github.com/prime31/Nez/blob/master/Nez.Portable/Core.cs) class which is a subclass of the [Game](https://github.com/FNA-XNA/FNA/blob/master/src/Game.cs) class. Your Game class should subclass Core. Core provides access to all the important subsystems via static fields and methods for easy access.

### [Graphics](https://github.com/prime31/Nez/tree/master/Nez.Portable/Graphics)

Nez will create an instance of the [Graphics](https://github.com/prime31/Nez/tree/master/Nez.Portable/Graphics) class \(available via `Graphics.Instance`\) for you at startup. It includes a default [BitmapFont](https://github.com/prime31/Nez/blob/master/Nez.Portable/Assets/BitmapFonts/BitmapFont.cs) so you can be up and running right away with good looking text \(MonoGames [SpriteFont](https://github.com/FNA-XNA/FNA/blob/master/src/Graphics/SpriteFont.cs) has some terrible compression going on\) and should cover most of your rendering needs. Graphics provides direct access to a [SpriteBatch](https://github.com/FNA-XNA/FNA/blob/master/src/Graphics/SpriteBatch.cs) and there is a SpriteBatch [extension class](https://github.com/prime31/Nez/blob/master/Nez.Portable/Graphics/Batcher/BatcherDrawingExt.cs) with a bunch of helpers for drawing rectangles, circles, lines, etc.

### [Scene](https://github.com/prime31/Nez/blob/master/Nez.Portable/ECS/Scene.cs)

When you set Core.scene to a new [Scene](https://github.com/prime31/Nez/blob/master/Nez.Portable/ECS/Scene.cs), Nez will finish rendering the current Scene, fire off the `CoreEvents.SceneChanged` event and then start rendering the new Scene. For more information on Scenes see the [Scene-Entity-Component](https://github.com/AnshumanKumar14/Nez-doc/tree/5f7d6292ccc24dbf7fa542f9d08781702cc2199e/Scene-Entity-Component.md) FAQ.

### [Sprites](https://github.com/prime31/Nez/tree/master/Nez.Portable/ECS/Components/Renderables/Sprites)

You can't make a 2D game without sprites. Nez provides a variety of ways to render sprites from basic single texture rendering to sprite atlas support to nine patch sprites. Some of the common sprite Components to get to know are [SpriteRenderer](https://github.com/prime31/Nez/blob/master/Nez.Portable/ECS/Components/Renderables/Sprites/SpriteRenderer.cs), [SpriteAnimator](https://github.com/prime31/Nez/blob/master/Nez.Portable/ECS/Components/Renderables/Sprites/SpriteAnimator.cs), [SpriteTrail](https://github.com/prime31/Nez/blob/master/Nez.Portable/ECS/Components/Renderables/Sprites/SpriteTrail.cs), [TiledSprite](https://github.com/prime31/Nez/blob/master/Nez.Portable/ECS/Components/Renderables/Sprites/TiledSprite.cs), [ScrollingSprite](https://github.com/prime31/Nez/blob/master/Nez.Portable/ECS/Components/Renderables/Sprites/ScrollingSprite.cs) and [PrototypeSprite](https://github.com/prime31/Nez/blob/master/Nez.Portable/ECS/Components/Renderables/Sprites/PrototypeSprite.cs). The two most common things in a 2D game are static sprites and animated sprites. Examples are below:

```csharp
// load a single image texture into a static SpriteRenderer
var texture = Content.Load<Texture2D>("SomeTex");

var entity = CreateEntity("SpriteExample");
entity.AddComponent(new SpriteRenderer(texture));
```

```csharp
// load up a texture that is an atlas of 16x16 animation frames
var texture = Content.Load<Texture2D>("SomeCharacterTex");
var sprites = Sprite.SpritesFromAtlas(texture, 16, 16);

var entity = CreateEntity("SpriteExample");

// add a SpriteAnimator, which renders the current frame of the currently playing animation
var animator = entity.AddComponent<SpriteAnimator>();

// add some animations
animator.AddAnimation("Run", sprites[0], sprites[1], sprites[2]);
animator.AddAnimation("Idle", sprites[3], sprites[4]);

// some time later, play an animation
animator.Play("Run");
```

### [Physics](https://github.com/prime31/Nez/tree/master/Nez.Portable/Physics)

Be careful not to confuse the Nez [Physics](https://github.com/prime31/Nez/tree/master/Nez.Portable/Physics) system with realistic physics simulations \(such as Box2D, Farseer, Chipmunk, etc\)! That is not at all its purpose. The Physics system is here only to provide spatial and collision information. It does not attempt to handle a full, real-world physics simulation. At the core of the Physics system is a [SpatialHash](https://github.com/prime31/Nez/blob/master/Nez.Portable/Physics/SpatialHash.cs) that gets populated and updated automatically as you add/remove/move [Colliders](https://github.com/prime31/Nez/blob/master/Nez.Portable/Physics/Collisions.cs). You can access the various Physics-related methods via the **Physics** class which provides methods \(boxcast, raycast, etc\) to handle broadphase collision detection in a performant way. Internally, Nez uses the Physics systems for collision detection with various shapes such as rectangles, circles and polygons. The [Entity](https://github.com/prime31/Nez/blob/master/Nez.Portable/ECS/Entity.cs) class provides move methods that handle all this for you or you could opt to just query the Physics system and handle the narrow phase collision detection yourself.

### [TimerManager](https://github.com/prime31/Nez/blob/master/Nez.Portable/Utils/Timers/TimerManager.cs)

The [TimerManager](https://github.com/prime31/Nez/blob/master/Nez.Portable/Utils/Timers/TimerManager.cs) is a simple helper that lets you pass in an Action that can be called once or repeately with or without a delay. The **Core.Schedule** method provides easy access to the TimerManager. When you call **Schedule** you get back an ITimer object that has a **Stop** method that can be used to stop the timer from firing again.

### [CoroutineManager](https://github.com/prime31/Nez/blob/master/Nez.Portable/Utils/Coroutines/CoroutineManager.cs)

The [CoroutineManager](https://github.com/prime31/Nez/blob/master/Nez.Portable/Utils/Coroutines/CoroutineManager.cs) lets you pass in an IEnumerator which is then ticked each frame allowing you to break long running tasks up into smaller bits. The entry point for starting a coroutine is **Core.StartCoroutine** which returns an ICoroutine object with a single method: **Stop**. The execution of a coroutine can be paused at any point using the yield statement. You can yield a call to `Coroutine.WaitForSeconds` which will delay execution for N seconds or you can yield a call to **StartCoroutine** to pause until another coroutine completes.

### [Emitter](https://github.com/prime31/Nez/blob/master/Nez.Portable/Utils/Emitter.cs)

Core provides an emitter that fires events at some key times. Access is via **Core.Emitter.AddObserver** and **Core.Emitter.RemoveObserver**. The **CoreEvents** enum defines all the events available.

The [Emitter](https://github.com/prime31/Nez/blob/master/Nez.Portable/Utils/Emitter.cs) class is available for use in your own classes as well. You can key events by int, enum or any struct. It was really built with int or enum in mind but there is no way to use generics to constrain to just those types. Note that as a performance enhancement if you are using `enum` as the event type it is recommended to pass in a custom `IEqualityComparer` to the `Emitter` constructor to avoid boxing. See the [**CoreEventsComparer**](https://github.com/prime31/Nez/blob/master/Nez.Portable/CoreEvents.cs) for a simple template to copy for your own custom `IEqualityComparer`.

### [Debug Console](https://github.com/prime31/Nez/blob/master/Nez.Portable/Debug/Console/DebugConsole.cs)

If you are buliding with the DEBUG compilation symbol, Nez includes a simple console that provides some useful information. Press the tilde \(~\) key to show/hide the console. Once it is open you can type 'help' to view all the available commands which include helper to log all loaded assets, total entity count, physics colliders managed by the SpatialHash, etc. Type 'help COMMAND' to get help information for a specific command.

![Ingame console](.gitbook/assets/console.png)

You can also easily add your own command to the debug console. Just add the [**CommandAttribute**](https://github.com/prime31/Nez/blob/master/Nez.Portable/Debug/Console/DefaultCommands.cs) to any static method and specify the command name and help string. Commands can have a single parameter. Here is an example of one of the built-in commands:

```csharp
[Command( "assets", "Logs all loaded assets. Pass 's' for scene assets or 'g' for global assets" )]
static void LogLoadedAssets( string whichAssets = "s" )
```

### Global Managers

Nez lets you add a global manager object that will have an update method called every frame before Scene.update occurs. Any of your systems that should persist [Scene](https://github.com/prime31/Nez/blob/master/Nez.Portable/ECS/Scene.cs) changes can be put here. Nez has several of it's own systems setup as global managers as well including: scheduler, coroutine manager and tween manager. You can register/unregister your global managers via `Core.RegisterGlobalManager` and `Core.UnregisterGlobalManager`.

## Other Important Static Classes

### [Time](https://github.com/prime31/Nez/blob/master/Nez.Portable/Utils/Time.cs)

[Time](https://github.com/prime31/Nez/blob/master/Nez.Portable/Utils/Time.cs) provides easy, static access to `DeltaTime`, `UnscaledDeltaTime`, `TimeScale` and some other useful properties. For ease of use it also provides an `AltDeltaTime`/`AltTimeScale` so that you can have multiple different timelines going on without having to manage them yourself.

### [Input](https://github.com/prime31/Nez/blob/master/Nez.Portable/Input/Input.cs)

As you can probably guess, [Input](https://github.com/prime31/Nez/blob/master/Nez.Portable/Input/Input.cs) gets you access to all input \(mouse, keyboard, gamepad\). All the usual stuff is in there with button terminology defined in the following way:

* **Down**: true the entire time the button/key is down
* **Pressed**: true only the frame that a button/key is pressed
* **Released**: true only the frame that a button/key is released

Several virtual input classes are also provided which let you combine multiple input types into a single class that you can query. For example, you can setup a [VirtualButton](https://github.com/prime31/Nez/blob/master/Nez.Portable/Input/Virtual/VirtualButton.cs) that can map to a variety of different input types that should result in some object moving to the right. You can create the [VirtualButton](https://github.com/prime31/Nez/blob/master/Nez.Portable/Input/Virtual/VirtualButton.cs) with the D key, right arrow key, Dpad-right and left [GamePad](https://github.com/FNA-XNA/FNA/blob/master/src/Input/GamePad.cs) stick and just query it to know if any of those were pressed. The same applies to other common scenarios as well. Virtual input is available for button emulation \(`[VirtualButton](https://github.com/prime31/Nez/blob/master/Nez.Portable/Input/Virtual/VirtualButton.cs)`\), analog joystick emulation \(`VirtualJoystick`\) and digital \(on/off\) joystick emulation \(`VirtualIntegerAxis`\). Below is an example of mapping multiple inputs to a single action:

```csharp
    void SetupVirtualInput()
    {
        // setup input for shooting a fireball. we will allow z on the keyboard or a on the gamepad
        _fireInput = new VirtualButton();
        _fireInput.AddKeyboardKey( Keys.X )
                  .AddGamePadButton( 0, Buttons.A );

        // horizontal input from dpad, left stick or keyboard left/right
        _xAxisInput = new VirtualIntegerAxis();
        _xAxisInput.AddGamePadDPadLeftRight()
                   .AddGamePadLeftStickX()
                   .AddKeyboardKeys( VirtualInput.OverlapBehavior.TakeNewer, Keys.Left, Keys.Right );

        // vertical input from dpad, left stick or keyboard up/down
        _yAxisInput = new VirtualIntegerAxis();
        _yAxisInput.AddGamePadDpadUpDown()
                   .AddGamePadLeftStickY()
                   .AddKeyboardKeys( VirtualInput.OverlapBehavior.TakeNewer, Keys.Up, Keys.Down );
    }


    void IUpdatable.Update()
    {
        // gather input from the Virtual Inputs we setup above
        var moveDir = new Vector2( _xAxisInput.Value, _yAxisInput.Value );
        var isShooting = _fireInput.IsPressed;
    }
```

### [Debug](https://github.com/prime31/Nez/blob/master/Nez.Portable/Debug/Debug.cs)

The [Debug](https://github.com/prime31/Nez/blob/master/Nez.Portable/Debug/Debug.cs) class provides logging and a few drawing methods. The [Insist](https://github.com/prime31/Nez/blob/master/Nez.Portable/Debug/Insist.cs) class provides an assortment of assert conditions. These classes are only compiled into DEBUG builds so you can use them freely throughout your code and when you do a non-DEBUG build none of them will be compiled into your game.

### Flags

Do you love the ability to pack lots of data into a single int but hate the syntax of dealing with it? The Flags class is there to help. It includes helper methods for dealing with ints to check if bits are set and to set/unset them. Very handy for dealing with [Collider](https://github.com/prime31/Nez/blob/master/Nez.Portable/Physics/Collisions.cs).physicsLayer.

