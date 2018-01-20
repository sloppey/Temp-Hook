Source
```C
void* placeHook(DWORD address, void* hook, bool revert=false){
		DWORD oldprot;
		if (!revert){
		    //placing hook
		return (void*)0;
		}
		else{
		    //reverting back to prevent roblox from detecting our hook
		}
	}
```
Example
```C
	![#1589F0]DWORD _CRASH = format(0x4BEE50); //function what roblos calls when it crashes
	void* omem;
	omem = placeHook(_CRASH, Hook); //store the location's memory into 'omem' before changing it
	typedef int(__cdecl * randomhook)(int, int, int, int, int);
	randomhook rH = (randomhook)ASLR(0x72CF00);
	rH(1, 2, 3, 4, 5); //cause roblox to crash by calling a function with incorrect args
	placeHook(_CRASH, omem, true); //revert changes to prevent roblox from detecting the change

```
