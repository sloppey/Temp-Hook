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
Example (Getting roblox's lua state by hooking onto the VM)
```C
	DWORD pro_out = format(0x719D1D);
int rstate_hk=0;
__declspec(naked) void vm_hook(){
	DWORD _eax;
	__asm{
	me:
		mov _eax, eax
		mov eax, [ebp+8]
		mov rstate_hk, eax
		mov eax, _eax
	st:
		push    ebx
		mov     ebx, esp
		sub     esp, 8
		and     esp, 0FFFFFFF0h
		add     esp, 4
	    push    ebp
		mov     ebp, [ebx + 4]
		mov[esp + 0Ch + 8], ebp
		mov     ebp, esp
		sub     esp, 138h
		push    esi
		mov     esi, [ebx + 8]
		push    edi
		lea     ecx, [esi + 1Ch]
		lea     eax, [esi + 0Ch]
		mov[ebp - 24h], ecx
		mov[ebp - 4Ch], eax
	ed:
		jmp pro_out
	}
}

void Init(){
void* old = placeHook(format(0x719CF0), vm_hook);
	do{ Sleep(1); } while (rstate_hk == 0);
	placeHook(format(0x719CF0), old, 1);
	rbx::getfield(rstate_hk, -10002, "print");
	rbx::pushstring(rstate_hk, "hello world!");
	rbx::pcall(rstate_hk, 1, 0, 0);
}


```

