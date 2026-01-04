# Understanding Slots + Bags.

Take your player CGObject, put it inside structure dissect in CE, now open 0008. take the address, lets call ours 0xDEADBEEF

That new 0xDEADBEEF+0x046C, that's where the equipment stuff starts, at 0xDEADBEEF + 0x0510, starts items in your backpack (The default you start with), after that are the slots of the other 4 bag containers.

```Plaintext
Address = object, then 8 to the unit data.

Address+0x05A8 = First Bag.
Address+0x05B0 = Second Bag.
Address+0x05B8 = Third Bag.
Address+0x05C0 = Forth Bag.

```

Here is a fully setup enum to do this, thanks to **[Kaev](https://github.com/Kaev)**

```C++
enum InventorySlots : uint8
{
    EQUIPMENT_SLOT_START        = 0,
    EQUIPMENT_SLOT_END          = 19,

    INVENTORY_SLOT_BAG_START    = 19,
    INVENTORY_SLOT_BAG_END      = 23,

    INVENTORY_SLOT_ITEM_START   = 23, // default backpack slots
    INVENTORY_SLOT_ITEM_END     = 47, 

    BANK_SLOT_ITEM_START        = 47,
    BANK_SLOT_ITEM_END          = 75,

    BANK_SLOT_BAG_START         = 75,
    BANK_SLOT_BAG_END           = 82,
};
```
---

# Order of slots for bags.
How the order of bags are is from the starter bag, fills the slot from first to last, like the game does it automatically.

---


### Another thing about them.

I figured out that bags work weird when we look in the database of our core.
Here you see Entry or automatically generated ID on creation.
If a bag is in a slot, the game automatically knows that entry is for bags, so it just set the item creation ID as the bag, and slot starts from the place where hearthstone normally is on newly created characters.