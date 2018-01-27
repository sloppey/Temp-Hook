Source
```C
void* placeHook(DWORD address, void* hook, bool revert = false){
	DWORD oldprot;
	if (!revert){
		void* oldmem = new void*;
		void* result = new void*;
		memcpy(oldmem, (void*)address, sizeof(void*) * 4);
		VirtualProtect((LPVOID)address, 1, PAGE_EXECUTE_READWRITE, &oldprot);
		*(char*)address = 0xE9; *(DWORD*)(address + 1) = (DWORD)hook - address - 5;
		memcpy(result, oldmem, sizeof(void*) * 4);
		VirtualProtect((LPVOID)address, 1, oldprot, &oldprot);
		return result;
	}
	else{
		VirtualProtect((LPVOID)address, 1, PAGE_EXECUTE_READWRITE, &oldprot);
		memcpy((void*)address, hook, sizeof(void*) * 4);
		VirtualProtect((LPVOID)address, 1, oldprot, &oldprot);
		return NULL;
	}
}
```
Example
```C
	inline void Hook(){
	MessageBoxA(0, "Hooked!", "Hooked!", 0); //!unsafe as this messagebox will prevent the memory from changing back
						 
	}


	DWORD _CRASH = format(0x4BEE50); //function what roblox eventually calls when it crashes
	void* omem = placeHook(_CRASH, Hook); //store the location's memory into 'omem' before changing it
	typedef int(__cdecl * rndFunc)(int, int, int, int, int);
	rndFunc rH = (rndFunc)ASLR(0x72CF00);
	rH(1, 2, 3, 4, 5); //cause roblox to crash by calling a function with incorrect args
			   //this will call our 'Hook' function instead
	placeHook(_CRASH, omem, true); //revert changes to prevent roblox from detecting the change

```
Example 2(Getting roblox's lua state, by hooking onto index2adr)
```C
	//index2adr gets called all the time, allowing our hook to retrieve and store the state somewhere else in memory 
	//which we can then use later.
	
	//removed for now



```
