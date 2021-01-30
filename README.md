<p align="center">
    <!-- Badges -->
    <a href="https://travis-ci.org/seeseemelk/MockBukkit">
        <img alt="Build Status" src="https://github.com/seeseemelk/MockBukkit/workflows/Build/badge.svg?event=push" />
    </a>
    <a href="https://mockbukkit.readthedocs.io/en/latest/?badge=latest">
        <img alt="Documentation Status" src="https://readthedocs.org/projects/mockbukkit/badge/?version=latest" />
    </a>
    <a href="https://codeclimate.com/github/seeseemelk/MockBukkit/maintainability">
        <img alt="Maintainability" src="https://api.codeclimate.com/v1/badges/403a4bb837ca47333d33/maintainability" />
    </a>
    <a href="https://codeclimate.com/github/seeseemelk/MockBukkit/test_coverage">
        <img alt="Test Coverage" src="https://api.codeclimate.com/v1/badges/403a4bb837ca47333d33/test_coverage" />
    </a>
    <!-- Logo -->
    <hr />
        <img alt="MockBukkit logo" src="logo.png"/>
    <hr />
</p>

MockBukkit is a framework that makes the unit testing of Bukkit plugins a whole lot easier.
It aims to be provide complete mock implementation of CraftBukkit that can be completely controlled from a unit test.

## :page_facing_up: Table of contents
1. [Usage](#todo)
    - [Adding MockBukkit via gradle](#todo)
    - [Adding MockBukkit via Maven](#todo)
    - [Using MockBukkit](#todo)
2. [Features](#todo)
    - [Mock Plugins](#todo)
    - [Mock Players](#todo)
    - [Mock Worlds](#todo)
3. [Troubleshooting (My tests are being skipped)](#todo)

## :mag: Usage
MockBukkit can easily be included in your project using either Maven or gradle.

### Adding MockBukkit via gradle
MockBukkit can easily be included in gradle using mavenCentral.

```gradle
repositories {
	mavenCentral()
	maven { url 'https://hub.spigotmc.org/nexus/content/repositories/public/' }
}

dependencies {
	testImplementation 'com.github.seeseemelk:MockBukkit-v1.16:0.22.1'
}
```

Note: use v1.13-SNAPSHOT to test a Bukkit 1.13 plugin or any other version if the branch exists.
These branches will not be receiving patches actively, but any issues will be resolved and any pull requests on them will be accepted.
This is because backporting every single patch on every branch is incredibely time consuming and slows down the development of Mockbukkit.

If you prefer to always have the latest Git version or need a specific commit/branch, you can always use JitPack as your maven repository:

```gradle
repositories {
	maven { url 'https://jitpack.io' }
}

dependencies {
	implementation 'com.github.seeseemelk:MockBukkit:v1.16-SNAPSHOT'
}
```

### Adding MockBukkit via Maven
MockBukkit can be included by adding the dependency to your `pom.xml`.<br>
You won't need to add any additional repositories since MockBukkit is served via maven-central. Make sure to update the version as necessary.

```xml
<dependencies>
  <dependency>
    <groupId>com.github.seeseemelk</groupId>
    <artifactId>MockBukkit-v1.16</artifactId>
    <version>0.22.1</version>
    <scope>test</scope>
  </dependency>
</dependencies>
```

Note: use v1.13-SNAPSHOT to test a Bukkit 1.13 plugin or any other version if the branch exists.
These branches will not be receiving patches actively, but any issues will be resolved and any pull requests on them will be accepted.
This is because backporting every single patch on every branch is incredibely time consuming and slows down the development of Mockbukkit.

The `scope` test is important here since you are likely to only be using MockBukkit during the `test` stage of your Maven lifecycle and not in your final product.

### Using MockBukkit
In order to use MockBukkit the plugin to be tested needs an extra constructor and it has to be initialised before each test.
The plugin will need both a default constructor and an extra one that will call a super constructor.
Your plugins constructor will look like this if your plugin was called ```MyPlugin```.
```java
public class MyPlugin extends JavaPlugin
{
    public MyPlugin()
    {
        super();
    }

    protected MyPlugin(JavaPluginLoader loader, PluginDescriptionFile description, File dataFolder, File file)
    {
        super(loader, description, dataFolder, file);
    }
}
```
The plugin is now ready to be tested by MockBukkit.
A plugin can be loaded in this initialiser block.

```java
private ServerMock server;
private MyPlugin plugin;

@Before
public void setUp()
{
    server = MockBukkit.mock();
    plugin = (MyPlugin) MockBukkit.load(MyPlugin.class);
}

@After
public void tearDown()
{
    MockBukkit.unmock();
}
```

## :sparkles: Features
### Mock Plugins
MockBukkit contains several functions that make the unit testing of Bukkit plugins a lot easier.

It is possible to create a mock plugin.
This is useful when the plugin you are testing may be looking at other loaded plugins.
The following piece of code creates a placeholder plugin that extends JavaPlugin.
```java
MockPlugin plugin = MockBukkit.createMockPlugin()
```

### Mock Players
MockBukkit makes it easy to create several mock players to use in unit testing.
By running ```server.setPlayers(int numberOfPlayers)``` one can set the number of online players.
From then on it's possible to get a certain player using ```server.getPlayer(int i)```.

An even easier way to create a player on the fly is by simply using
```java
PlayerMock player = server.addPlayer();
```

A mock player also supports several simulated actions, such as damaging a block or even
breaking it. This will fire all the required events and will remove the block if the
events weren't cancelled.
```java
Block block = ...;
player.simulateBlockBreak(block);
```

### Mock Worlds
Another feature is the easy creation of mock worlds.
One can make a superflat world using one simple command:
```java
World world = new WorldMock(Material material, int heightUntilAir)
```
Using Material.DIRT and 3 as heightUntilAir will create a superflat world with a height of a 128.
At y=0 everything will be Material.BEDROCK, and from 1 until 3 (inclusive) will be Material.DIRT
and everything else will be Material.AIR.
Each block is created the moment it is first accessed, so if only one block is only ever touched only one
block will ever be created in-memory.

## :question: My tests are being skipped!? (UnimplementedOperationException)
Sometimes your code may use a method that is not yet implemented in MockBukkit.
When this happens MockBukkit will, instead of returning placeholder values, throw
an `UnimplementedOperationException`.
These exception extends `AssumationException` and will cause the test to be skipped.

These exceptions should just be ignored, though pull requests that add functionality to MockBukkit are always welcome!
If you don't want to add the required methods yourself you can also request the method on the issues page.
