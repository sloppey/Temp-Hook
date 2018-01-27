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
DWORD pro_out = format(0x719F51);
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
			mov     eax, [ebp - 24h]
			sub     eax, edi
			mov     dword ptr[ebp - 0A0h], 7
			mov[ecx], eax
			mov     eax, edx
			shr     eax, 12h
			and     edx, 3FFFFh
			movzx   eax, al
			shl     eax, 4
			add     eax, [ebp - 8]
			push    eax
			shl     edx, 4
			lea     eax, [ebp - 0A8h]
			add     edx, [ebp - 10h]
			push    edx
			push    eax
		jmp pro_out
	}
}

void Init(){
	void* old = placeHook(format(0x719F09), vm_hook);
	do{ Sleep(1); } while (rstate_hk == 0);
	placeHook(format(0x719F09), old, 1);
	rbx::settop(rstate_hk, 0);
	rstate_hk = rbx::newthread(rstate_hk);
	rbx::getfield(rstate_hk, -10002, "print");
	rbx::pushstring(rstate_hk, "hello world");
	rbx::pcall(rstate_hk, 1, 0, 0);
}


```

