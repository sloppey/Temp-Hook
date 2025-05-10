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
Example (Getting lua state by hooking onto the VM)
```C
DWORD pro_out = loc;
int rstate_hk=0;
__declspec(naked) void vm_hook(){
	DWORD _eax;
	__asm{

		me:
			mov _eax, eax
			mov eax, [ebx+8]
			mov rstate_hk, eax
			mov eax, _eax
		strt:
			mov     eax, [ebp - 2Ch]
			mov     ecx, [ebp - 24h]
			mov     esi, [ebx + 8]
			push    1
			mov     eax, [eax + 0Ch]
			mov[ebp - 0A8h], eax
			
		jmp pro_out
	}
}



```

