# Understanding Cache System.

So, the cache is how our client stores data about items and many other things.
I will make this documentation better later; for now, I will just focus on the item name cache.

---

When our client goes to get an item name, it does it through the cache, even if you patch the client to not use the cache. All it basically does is that it doesn't **create** the cache folder for us, but inside the client, it will still use the cache system, just never **save** it

---

So, a ton of data, such as item names, NPC names, and even gameobject names, are basically sent from server to client at startup; that is how the items know their own names without having to be inside a DBC file.
Below is the function that loads them:
```C++
int __usercall DbCache_LoadAll@<eax>(HOSFILE__ *a1@<ebx>, int a2@<esi>)
{
  DbDanceCache_Load((int)dword_C5D690, a1);
  DbGameObjectCache_Load(&dword_C5D718);
  DbPageTextCache_Load(dword_C5D828);
  DbItemTextCache_Load((int)&dword_C5D7A0, a1);
  DbItemNameCache_Load(&unk_C5D8B0);
  DbGuildCache_Load(dword_C5D938);
  DbArenaTeamCache_Load((int)dword_C5D9C0, a1);
  DbNameCache_Load(&dword_C5DA48);
  DbPetNameCache_Load(&dword_C5DAD0);
  DbQuestCache_Load(byte_C5DB58);
  DbNpcCache_Load(&dword_C5DBE0);
  DbCreatureCache_Load(&dword_C5DC68);
  DbWoWCache_Load((int)&unk_C5DCF0, (int)a1, a2);
  DbItemCache_Load(&unk_C5DD78);
  return DbPetitionCache_Load(&dword_C5DE00);
}
```

Here is the jumping function (Basically the function that just calls the above function)
```C++
// attributes: thunk
int __usercall j_DbCache_LoadAll@<eax>(HOSFILE__ *a1@<ebx>, int a2@<esi>)
{
  return DbCache_LoadAll(a1, a2);
}
```
