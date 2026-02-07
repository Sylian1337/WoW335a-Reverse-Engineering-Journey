# Understanding The DBC Loading Flow
The client needs to load all our **DBCs**. The way that is done is that it loads all these during the start-up of the game, and in this document, I will outline the whole **DBC** loading flow for you.

The first function called will be **StaticDBLoadAll** (**0x6337D0**):
```C++
// 00005400
int __usercall StaticDBLoadAll@<eax>(void (__thiscall *a1)(void *this)@<esi>)
{
  a1(&g_achievementDB);
  a1(&g_achievement_CriteriaDB);
  a1(&g_achievement_CategoryDB);
  a1(&g_animationDataDB);
  a1(&g_areaGroupDB);
  a1(&g_areaPOIDB);
  a1(&g_areaTableDB);
  a1(&g_spellDB);
/// Keeps on going, but you get the idea...
  return ((int (__thiscall *)(void *))a1)(&g_soundFilterElemDB);
}
```
---

For each of the **DBCs**, it calls a **DBCLoadFunction (0x6348B0)**, which is called for each of the **DBCs**

Here is how that function looks:
```C++
int __thiscall DBCLoadFunction(void *this)
{
  return (*(int (__thiscall **)(void *))(*(_DWORD *)this + 4))(this);
}
```
---

Our **DBCLoadFunction** is now calling another function to load the DBC we are currently looking at, which, if we just take the example of **Spell.DBC**, that function would be our **WowClientDB__SpellRec__LoadDB (0x650940)**.

Each DBC has its own function for each of them.
```C++
void __thiscall WowClientDB__SpellRec__LoadDB(_DWORD *this, char *filename, int32_t ExitCode)
{
  char *v4; // eax
  char v5; // al
  char v6; // al
  char v7; // al
  char v8; // al
  char v9; // al
  char v10; // al
  char v11; // al
  char v12; // al
  void *v13; // eax
  void (__thiscall *v14)(_DWORD *, void *, char *, int32_t); // edx
  char v15; // al
  void *v16; // [esp-14h] [ebp-2Ch]
  int v17; // [esp+4h] [ebp-14h] BYREF
  int v18; // [esp+8h] [ebp-10h] BYREF
  uint32_t bytes; // [esp+Ch] [ebp-Ch] BYREF
  char ArgList[4]; // [esp+10h] [ebp-8h] BYREF
  void *Block; // [esp+14h] [ebp-4h] BYREF

  if ( this[1] )
  {
    SpellRec__GetFilename();
    nullsub_3();
  }
  else
  {
    v4 = (char *)SpellRec__GetFilename();
    if ( !SFile__OpenEx(0, v4, 0x20000, &Block) )
    {
      v5 = SpellRec__GetFilename();
      CConsole__PrintError(0x85100079, "Unable to open %s", v5);
    }
    if ( !SFile__Read(Block, ArgList, 4, 0, 0, 0) )
    {
      v6 = SpellRec__GetFilename();
      CConsole__PrintError(0x85100079, "Unable to read signature from %s", v6);
    }
    if ( *(_DWORD *)ArgList != 1128416343 )
    {
      SpellRec__GetFilename();
      CConsole__PrintError(0x85100079, "Invalid signature 0x%x from %s", ArgList[0]);
    }
    if ( !SFile__Read(Block, this + 2, 4, 0, 0, 0) )
    {
      v7 = SpellRec__GetFilename();
      CConsole__PrintError(0x85100079, "Unable to read record count from %s", v7);
    }
    if ( this[2] )
    {
      if ( !SFile__Read(Block, &v18, 4, 0, 0, 0) )
      {
        v8 = SpellRec__GetFilename();
        CConsole__PrintError(0x85100079, "Unable to read column count from %s", v8);
      }
      if ( v18 != 234 )
      {
        v9 = SpellRec__GetFilename();
        CConsole__PrintError(0x85100079, "%s has wrong number of columns (found %i, expected %i)", v9);
      }
      if ( !SFile__Read(Block, &v17, 4, 0, 0, 0) )
      {
        v10 = SpellRec__GetFilename();
        CConsole__PrintError(0x85100079, "Unable to read row size from %s", v10);
      }
      if ( v17 != 936 )
      {
        v11 = SpellRec__GetFilename();
        CConsole__PrintError(0x85100079, "%s has wrong row size (found %i, expected %i)", v11);
      }
      if ( !SFile__Read(Block, &bytes, 4, 0, 0, 0) )
      {
        v12 = SpellRec__GetFilename();
        CConsole__PrintError(0x85100079, "Unable to read string size from %s", v12);
      }
      v13 = SMemAlloc(bytes, filename, ExitCode, 0);
      v14 = *(void (__thiscall **)(_DWORD *, void *, char *, int32_t))(*this + 12);
      this[5] = v13;
      v16 = Block;
      this[3] = 0;
      this[4] = 0xFFFFFFF;
      v14(this, v16, filename, ExitCode);
      if ( !SFile__Read(Block, this[5], bytes, 0, 0, 0) )
      {
        v15 = SpellRec__GetFilename();
        sub_772AA0("%s: Cannot read string table", v15);
      }
      SFile__Close(Block);
      this[1] = 1;
    }
    else
    {
      SFile__Close(Block);
    }
  }
}
```
---

That was all for the **DBC** loading flow.
I hope it helps you understand how it all works when the client starts up.
