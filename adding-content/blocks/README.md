---
description: Creating your first custom block
---

# 🧊 Blocks

## Basics

Registering a custom block can either be done with or without a model using the `CustomiesBlockFactory` class. Without a model all you need to do is register the block by providing a `Closure` and the identifier of the block. The closure must be of the type `Closure(int): Block`, where the int is the ID that should be used for the block. This **DOES NOT** allow you to use a custom ID for the block, and will cause issues if you do not use the provided value.

```php
use customiesdevs\customies\block\CustomiesBlockFactory;
use pocketmine\block\BlockBreakInfo;
use pocketmine\block\BlockIdentifier;
use pocketmine\block\BlockTypeIds;

// ...

public function onEnable(): void {
	CustomiesBlockFactory::getInstance()->registerBlock(static fn() => new Block(new BlockIdentifier(BlockTypeIds::newId(), 0), "Example Block", new BlockTypeInfo(BlockBreakInfo::indestructible()), "customies:example_block");
}

// ...
```

> It is important to note that the provided closure is shared across threads, meaning you cannot use any variables that are not serializable types defined outside the scope of the closure.

## Getting a Block

To get a custom block you need to use the `CustomiesBlockFactory` instead of the regular block factory so you can get the block from the custom identifier instead of a numeric id.

```php
$block = CustomiesBlockFactory::getInstance()->get("customies:example_block");
```

## Using Custom Models

If your block contains a different model, you can provide a `Model` as the 5th argument. A model requires the following:

* Materials: Array of materials that define how the texture is applied to specific faces
* Texture: Name of the texture to apply to the model
* Origin: The origin point of the selection box. `Vector3(0, 0, 0)` is the top right corner of the block
* Size: The size of the block in pixels. This must be between `Vector3(0, 0, 0)` and `Vector3(16, 16, 16)` as the client does not support blocks being larger than this

```php
use customiesdevs\customies\block\CustomiesBlockFactory;
use customiesdevs\customies\block\Material;
use customiesdevs\customies\block\Model;
use pocketmine\block\BlockBreakInfo;
use pocketmine\block\BlockIdentifier;
use pocketmine\math\Vector3;

// ...

public function onEnable(): void {
	$material = new Material(Material::TARGET_ALL, "example", Material::RENDER_METHOD_ALPHA_TEST);
	$model = new Model([$material], "geometry.example", new Vector3(-8, 0, -8), new Vector3(16, 16, 16));
	CustomiesBlockFactory::getInstance()->registerBlock(static fn(int $id) => new Block(new BlockIdentifier($id, 0), "Example Block", new BlockBreakInfo(1)), "customies:example_block", $model);
}

// ...
```

> More information about materials and the different properties can be found on [docs.microsoft.com](https://docs.microsoft.com/en-us/minecraft/creator/reference/content/blockreference).

## Creative Inventory

If you want add the block to the creative inventory, you can provide a `CreativeInventoryInfo` as the 6th argument. This contains the following:

* Category: The base category to show the item in, for example `CreativeInventoryInfo::CATEGORY_NATURE` will put the item in the nature category
* Group: The group to put the item in inside of the category, for example `CreativeInventoryInfo::GROUP_WOOD` will put the item at the end of the wood group

```php
use customiesdevs\customies\block\CustomiesBlockFactory;
use customiesdevs\customies\block\Material;
use customiesdevs\customies\block\Model;
use customiesdevs\customies\item\CreativeInventoryInfo;
use pocketmine\block\BlockBreakInfo;
use pocketmine\block\BlockIdentifier;
use pocketmine\math\Vector3;

// ...

public function onEnable(): void {
	$material = new Material(Material::TARGET_ALL, "example", Material::RENDER_METHOD_ALPHA_TEST);
	$model = new Model([$material], "geometry.example", new Vector3(-8, 0, -8), new Vector3(16, 16, 16));
	$creativeInfo = new CreativeInventoryInfo(CreativeInventoryInfo::CATEGORY_NATURE, CreativeInventoryInfo::GROUP_WOOD);
	CustomiesBlockFactory::getInstance()->registerBlock(static fn(int $id) => new Block(new BlockIdentifier($id, 0), "Example Block", new BlockBreakInfo(1)), "customies:example_block", $model, $creativeInfo);
}

// ...
```

> More information about creative categories and groups can be found on [docs.microsoft.com](https://docs.microsoft.com/en-us/minecraft/creator/reference/content/blockreference/examples/blockcomponents/minecraftblock\_creative\_category).
