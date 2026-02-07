# Understanding The Cache System

So, the cache is how our client stores data about items and many other things.
I will make this documentation better later; for now, I will just focus on the item name cache.

---

When our client goes to get an item name, it does it through the cache, even if you patch the client to not use the cache. All it basically does is that it doesn't **create** the cache folder for us, but inside the client, it will still use the cache system, just never **save** it

---
## Understanding The DBC Flow.
When we start our client, it will be sent data from the server to the client, so the client will load all that data from the server.

So if you ever thought, "How come the client doesn't need item names in DBC?s"

That is because, as you see in this **DbCache__LoadAll (0x635060)** function, is that it loads the **a1 (First argument sent with the call)**, is the **HOSFILE__**, thats the data sent from our server to client, which the data.
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

As you see, it now call each of the **DbCache_Load** functions for each of the DBCaches that exist.

Below, we will just look at the **DbGameObjectCache_Load (0x67C0E0)** for an example:
```C++
void __usercall DbGameObjectCache_Load(int a1@<ecx>, HOSFILE__ *a2@<ebx>)
{
  bool v3; // zf
  HOSFILE__ *File; // eax
  HOSFILE__ *v5; // esi
  CDataStore__v_table *v6; // edi
  int v7; // ebx
  uint32_t v8; // eax
  _DWORD *v9; // esi
  int v10; // esi
  int *v11; // eax
  _BYTE buffer[12]; // [esp+4h] [ebp-4284h] BYREF
  HOSFILE v13; // [esp+10h] [ebp-4278h]
  _BYTE *v14; // [esp+14h] [ebp-4274h]
  uint32_t v15; // [esp+18h] [ebp-4270h]
  uint32_t *p_bytesRead; // [esp+1Ch] [ebp-426Ch]
  char dest[260]; // [esp+4004h] [ebp-284h] BYREF
  char v18[260]; // [esp+4108h] [ebp-180h] BYREF
  CDataStore__v_table *v19[6]; // [esp+420Ch] [ebp-7Ch] BYREF
  CDataStore__v_table *v20[6]; // [esp+4224h] [ebp-64h] BYREF
  int v21; // [esp+423Ch] [ebp-4Ch] BYREF
  int v22; // [esp+4240h] [ebp-48h] BYREF
  int v23; // [esp+4244h] [ebp-44h] BYREF
  int v24; // [esp+4248h] [ebp-40h] BYREF
  int v25; // [esp+424Ch] [ebp-3Ch] BYREF
  int v26; // [esp+4250h] [ebp-38h] BYREF
  _DWORD v27[6]; // [esp+4254h] [ebp-34h] BYREF
  HOSFILE fileHandle; // [esp+426Ch] [ebp-1Ch]
  uint32_t v29; // [esp+4270h] [ebp-18h]
  int v30; // [esp+4274h] [ebp-14h]
  uint32_t bytes; // [esp+4278h] [ebp-10h] BYREF
  int v32; // [esp+427Ch] [ebp-Ch] BYREF
  uint32_t bytesRead; // [esp+4280h] [ebp-8h] BYREF
  char v34; // [esp+4287h] [ebp-1h] BYREF

  v3 = *(_BYTE *)(a1 + 69) == 0;
  v30 = a1;
  *(_BYTE *)(a1 + 70) = 1;
  if ( !v3 )
  {
    sub_667EF0(v18);
    SStrPrintf(dest, 0x104u, "%s/%s", v18, *(const char **)(a1 + 52));
    File = OsCreateFile(dest, 0x80000000, 1u, 3u, 0x80u, 0x3F3F3F3Fu);
    v5 = File;
    fileHandle = File;
    if ( File != (HOSFILE__ *)-1 )
    {
      v13 = a2;
      v6 = (CDataStore__v_table *)buffer;
      v29 = 0x4000;
      OsReadFile(File, buffer, 0x18u, &bytesRead);
      if ( bytesRead == 24 )
      {
        v27[1] = buffer;
        v27[0] = &CDataStore__v_table;
        v27[2] = 0;
        v27[3] = -1;
        v27[4] = 24;
        v27[5] = 0;
        CDataStore__Get_uint32_t(v27, &v21);
        v7 = v30;
        if ( v21 == *(_DWORD *)(v30 + 48)
          && (CDataStore__Get_uint32_t(v27, &v25), v25 == 12340)
          && (CDataStore__Get_uint32_t(v27, &v26), v26 == dword_B38CD0)
          && (CDataStore__Get_uint32_t(v27, &v22), v22 == 160)
          && (CDataStore__Get_uint32_t(v27, &v24), v24 == 1) )
        {
          CDataStore__Get_uint32_t(v27, &v23);
          p_bytesRead = &bytesRead;
          v15 = 8;
          v14 = buffer;
          v13 = v5;
          *(_DWORD *)(v7 + 56) = v23;
          v34 = 0;
          OsReadFile(v13, v14, v15, p_bytesRead);
          if ( bytesRead == 8 )
          {
            while ( 1 )
            {
              CDataStore__CreateFromData(v20, v6, (CDataStore__v_table *)8);
              CDataStore__Get_uint32_t(v20, &v32);
              CDataStore__Get_uint32_t(v20, &bytes);
              if ( !v32 )
                break;
              v8 = bytes;
              if ( bytes > v29 )
              {
                if ( v29 > 0x4000 )
                {
                  SMemFree(v6, ".\\DBCache.cpp", 606, 0);
                  v8 = bytes;
                }
                v29 = v8;
                v6 = (CDataStore__v_table *)SMemAlloc(v8, ".\\DBCache.cpp", 608, 0);
                v8 = bytes;
              }
              OsReadFile(v5, v6, v8, &bytesRead);
              if ( bytesRead != bytes )
              {
                v34 = 1;
                break;
              }
              CDataStore__CreateFromData(v19, v6, (CDataStore__v_table *)bytes);
              v9 = sub_6F6020((_DWORD *)(v7 + 8), v32, (int)&v34);
              if ( !v9 )
              {
                v10 = v32;
                v11 = (int *)sub_67AFB0(v32, 0, 0);
                *v11 = v10;
                v9 = v11;
              }
              sub_98D750(v19);
              *((_BYTE *)v9 + 188) = 1;
              v9[46] = v32;
              CDataStore_Release((char *)v19);
              CDataStore_Release((char *)v20);
              OsReadFile(fileHandle, v6, 8u, &bytesRead);
              v5 = fileHandle;
              v7 = v30;
              if ( bytesRead != 8 )
                goto LABEL_21;
            }
            CDataStore_Release((char *)v20);
          }
          else
          {
LABEL_21:
            v34 = 1;
          }
          if ( v29 > 0x4000 )
            SMemFree(v6, ".\\DBCache.cpp", 633, 0);
          OsCloseFile(v5);
          if ( v34 )
            sub_66CC90(v7);
          CDataStore_Release((char *)v27);
        }
        else
        {
          OsCloseFile(v5);
          CDataStore_Release((char *)v27);
        }
      }
      else
      {
        OsCloseFile(v5);
      }
    }
  }
}
```

Now, this is how the whole flow works when loading all these DBCache, which comes from the server.

Hope this helped you understand the DBCache load flow.
