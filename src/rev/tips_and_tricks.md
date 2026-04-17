# Techniky a tipy

V rámci reverse engineeringu se lze setkat s širokou škálou transformací dat, bitových operací, jednoduchých šifer, validačních smyček i obranných mechanismů, jako jsou anti-debug techniky nebo self-checky. Cílem této kapitoly je shrnout nejčastější techniky, se kterými se lze v CTF a jednoduchých crackme úlohách setkat, a ukázat praktické způsoby jejich analýzy.


## Bitové operace

Bitové operace patří mezi nejběžnější techniky používané při úpravě vstupu. V binárce se často objevují jako součást jednoduchého „šifrování“ nebo transformace dat před porovnáním.

Nejčastější operace jsou:

- `+` a `-` nad bajty
- `&` (AND)
- `|` (OR)
- `^` (XOR)
- bitové posuny `<<` a `>>`

Při analýze je důležité pamatovat na to, že se často pracuje pouze s jedním bajtem, tedy v rozsahu `0x00` až `0xff`.

### Sčítání nad bajty

Příklad transformace:

```c
out[i] = (input[i] + 0x10) & 0xff;
```

Ekvivalent v Pythonu:

```python
cipher = [0x81, 0x75, 0x83, 0x84]
plain = ''.join(chr((b - 0x10) & 0xff) for b in cipher)
print(plain)
```

Pokud známe výsledek a chceme získat původní vstup, provádíme opačnou operaci, tedy odečtení.

### Odčítání nad bajty

```c
out[i] = (input[i] - 3) & 0xff;
```

Python:

```python
cipher = [0x74, 0x62, 0x6f, 0x64]
plain = ''.join(chr((b + 3) & 0xff) for b in cipher)
print(plain)
```

### AND

Operace AND se používá pro maskování bitů:

```c
out[i] = input[i] & 0x7f;
```

Python:

```python
data = [0xf4, 0xe5, 0xf3, 0xf4]
masked = ''.join(chr(b & 0x7f) for b in data)
print(masked)
```

Je třeba počítat s tím, že AND bývá částečně nevratná operace — pokud maska maže bity, původní hodnotu již nemusí být možné jednoznačně rekonstruovat.

### OR

```c
out[i] = input[i] | 0x20;
```

Python:

```python
data = [0x41, 0x42, 0x43]
result = ''.join(chr(b | 0x20) for b in data)
print(result)
```

Tato operace se často používá například pro převod velkých písmen na malá.

### Bitové posuny

```c
out[i] = input[i] << 1;
```

Python:

```python
data = [0x21, 0x22, 0x23]
shifted = [(b << 1) & 0xff for b in data]
print([hex(x) for x in shifted])
```

Při zpětné rekonstrukci je nutné dávat pozor na ztrátu bitů při přetečení.

## XOR

XOR je natolik častý, že si zaslouží samostatnou sekci. V reverse engineeringu se používá pro jednoduché zakrývání řetězců, kontrolu vstupu i lehkou obfuskaci.

Základní vlastnost XOR:

```text
a ^ b ^ b = a
```

To znamená, že stejnou operací lze data jak zakódovat, tak dekódovat.

### XOR s jedním klíčem

```python
cipher = [0x35, 0x2c, 0x21, 0x26]
key = 0x42
plain = ''.join(chr(b ^ key) for b in cipher)
print(plain)
```

### XOR nad hex polem

```python
cipher = [0x10, 0x23, 0x37, 0x55]
key = 0x41
decoded = [b ^ key for b in cipher]
print(decoded)
print(''.join(chr(x) for x in decoded))
```

### XOR nad stringem

```python
text = "hello"
key = 0x20
encoded = bytes([ord(c) ^ key for c in text])
print(encoded)
print(''.join(chr(b ^ key) for b in encoded))
```

### XOR se opakujícím se klíčem

```python
cipher = bytes([0x27, 0x2a, 0x3f, 0x39, 0x24])
key = b"key"
plain = bytes(cipher[i] ^ key[i % len(key)] for i in range(len(cipher)))
print(plain)
```

V CTF se velmi často objevuje právě opakující se klíč nebo jednoduchý jednobajtový XOR.

## Obvyklé šifry

V jednoduchých reversing úlohách se pravidelně objevují i klasické substituční nebo posunové šifry. Obvykle nejde o skutečně bezpečné šifrování, ale spíše o překážku, kterou má řešitel rozpoznat.

### Caesarova šifra

Každé písmeno se posune o pevný počet pozic.

```python
def caesar_decrypt(text, shift):
    out = []
    for c in text:
        if 'a' <= c <= 'z':
            out.append(chr((ord(c) - ord('a') - shift) % 26 + ord('a')))
        elif 'A' <= c <= 'Z':
            out.append(chr((ord(c) - ord('A') - shift) % 26 + ord('A')))
        else:
            out.append(c)
    return ''.join(out)

print(caesar_decrypt("Khoor", 3))
```

### ROT13

ROT13 je speciální případ Caesarovy šifry s posunem 13.

```python
def rot13(text):
    out = []
    for c in text:
        if 'a' <= c <= 'z':
            out.append(chr((ord(c) - ord('a') + 13) % 26 + ord('a')))
        elif 'A' <= c <= 'Z':
            out.append(chr((ord(c) - ord('A') + 13) % 26 + ord('A')))
        else:
            out.append(c)
    return ''.join(out)

print(rot13("synt{grfg}"))
```

### Atbash

Atbash převrací abecedu: `a -> z`, `b -> y`, atd.

```python
def atbash(text):
    out = []
    for c in text:
        if 'a' <= c <= 'z':
            out.append(chr(ord('z') - (ord(c) - ord('a'))))
        elif 'A' <= c <= 'Z':
            out.append(chr(ord('Z') - (ord(c) - ord('A'))))
        else:
            out.append(c)
    return ''.join(out)

print(atbash("uozt{gvhg}"))
```

### Base64

Base64 není šifra, ale velmi často se v reversing úlohách objevuje jako forma kódování dat.

```python
import base64

encoded = "ZmxhZ3t0ZXN0fQ=="
print(base64.b64decode(encoded).decode())
```

Při analýze je proto důležité rozlišovat mezi šifrováním, kódováním a prostou transformací dat.

## Hashe

Dalším častým vzorem je porovnávání hashe místo přímého porovnání vstupu. Program například neukládá heslo přímo, ale porovnává `md5`, `sha1` nebo `sha256` zadané hodnoty.

Pokud binárka porovnává hash, obvykle existují tři možnosti:

- uhodnout nebo odvodit vstup z kontextu
- použít slovníkový útok / brute force
- patchnout validaci a obejít kontrolu úplně

V CTF bývá hash často použit jen jako jednoduchá překážka, nikoli jako skutečná ochrana.

## Anti-debug

Některé programy se snaží detekovat přítomnost debuggeru a ukončit se, případně změnit své chování. Typické anti-debug techniky v Linux prostředí zahrnují například:

- `ptrace(PTRACE_TRACEME, ...)`
- kontrolu `/proc/self/status`
- časové kontroly
- self-checky nad vlastním kódem

Typický příklad:

```c
if (ptrace(PTRACE_TRACEME, 0, 1, 0) == -1) {
    puts("Debugger detected");
    exit(1);
}
```

### Jak anti-debug odhalit

Nejprve je vhodné hledat podezřelé funkce:

- ve statické analýze (`ptrace`, `getppid`, `clock_gettime`)
- v `ltrace` nebo `strace`
- v seznamu funkcí v GDB

Například:

```bash
ltrace ./program
strace ./program
gef➤  info functions
```

### Jak anti-debug obejít

Základní možnosti jsou:

- přeskočit kontrolní větev (`jump`)
- změnit návratovou hodnotu funkce
- patchnout podmínku nebo samotné volání

Praktický přístup v debuggeru bývá například:

1. breakpoint na `ptrace`
2. doběhnout za volání
3. nastavit návratovou hodnotu na úspěch

```bash
gef➤  break ptrace
gef➤  run
gef➤  finish
gef➤  set $rax = 0
gef➤  continue
```

Pokud je kontrola provedena pouze jednou, tento postup často stačí. V opačném případě je vhodnější patchnout podmíněný skok nebo celé volání trvale.

### Self-checky

Některé binárky kontrolují vlastní instrukce nebo checksum části kódu. To komplikuje patchování, protože každá změna může být detekována. V takovém případě bývá vhodné:

- patchnout samotnou kontrolu integrity
- přesměrovat tok programu za self-check
- analyzovat, která část je skutečně ověřována