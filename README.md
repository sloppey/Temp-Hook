Source
```C
void* placeHook(DWORD address, void* hook, bool revert=false){
		DWORD oldprot;
		if (!revert){
		    //placing hook
		    //- Removed for now
		return (void*)0;
		}
		else{
		    //reverting back to prevent roblox from detecting our hook
		    //- Removed for now
		    return (void*)0;
		}
	}
```
Example
```C
	inline void Hook(){
	MessageBoxA(0, "Hooked!", "Hooked!", 0); //!unsafe as this messagebox will prevent the memory from changing back
						 
	}


	DWORD _CRASH = format(0x4BEE50); //function what roblos eventually calls when it crashes
	void* omem = placeHook(_CRASH, Hook); //store the location's memory into 'omem' before changing it
	typedef int(__cdecl * rndFunc)(int, int, int, int, int);
	rndFunc rH = (rndFunc)ASLR(0x72CF00);
	rH(1, 2, 3, 4, 5); //cause roblox to crash by calling a function with incorrect args
			   //this will call our 'Hook' function instead
	placeHook(_CRASH, omem, true); //revert changes to prevent roblox from detecting the change

```
