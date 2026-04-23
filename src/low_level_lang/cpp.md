# C++

**C++** je rozšířením jazyka C o objektově orientované programování, šablony, výjimky a mnoho dalšího. Dnes se řadí mezi nejmocnější (a zároveň nejsložitější) programovací jazyky — dá se v něm psát od firmwaru pro mikrokontroléry až po operační systémy a herní enginy.

## Instalace

Na každé platformě máte na výběr z několika kompilátorů. Nejčastěji se používá **GCC** (`g++`) nebo **Clang** (`clang++`).

### Linux

Na většině distribucí je GCC už nainstalováno, případně dostupné v balíčkovacím systému:

```bash
# Debian / Ubuntu
sudo apt install g++

# Arch Linux
sudo pacman -S gcc

# Fedora
sudo dnf install gcc-c++
```

### macOS

Stačí nainstalovat Command Line Tools od Applu (obsahují Clang):

```bash
xcode-select --install
```

### Windows

Máte několik možností:

- **MSYS2** — přineste si UNIX-like toolchain včetně `g++` ([msys2.org](https://www.msys2.org/))
- **MinGW-w64** — port GCC pro Windows
- **Visual Studio** — oficiální IDE od Microsoftu s kompilátorem MSVC

### Ověření instalace

```bash
g++ --version
```

## Hello, World!

Uložte si do souboru `hello.cpp`:

```cpp
#include <iostream>

int main() {
    std::cout << "Hello, World!" << std::endl;
    return 0;
}
```

### Kompilace a spuštění

```bash
g++ hello.cpp -o hello
./hello
```

Výstup:

```
Hello, World!
```

### Co se v tom kódu děje?

- `#include <iostream>` — zpřístupní standardní I/O knihovnu (objekty `cout`, `cin`...)
- `int main()` — vstupní bod programu. Musí vracet `int`.
- `std::cout` — standardní výstupní proud (standard character out)
- `<<` — tzv. *stream insertion operator*, předává hodnotu do streamu
- `std::endl` — nový řádek + flush bufferu
- `return 0;` — program skončil úspěšně (nenulová hodnota by signalizovala chybu)

## Rozdíly oproti C

I když C++ z C vychází a většina C kódu se v C++ zkompiluje, obě jazyky se v mnohém liší. Tady jsou hlavní rozdíly:

### Objektově orientované programování

C je čistě procedurální. C++ přidává **třídy**, **dědičnost**, **polymorfismus** a další OOP koncepty:

```cpp
class Animal {
public:
    virtual void speak() { std::cout << "..." << std::endl; }
};

class Dog : public Animal {
public:
    void speak() override { std::cout << "Haf!" << std::endl; }
};
```

### Šablony (templates)

C++ umí generické programování pomocí šablon — v C byste museli používat makra nebo `void *`:

```cpp
template <typename T>
T max(T a, T b) {
    return (a > b) ? a : b;
}

max(3, 5);       // int verze
max(1.5, 2.7);   // double verze
```

### Standardní knihovna (STL)

C má minimalistickou standardní knihovnu. C++ má obří **STL** s kontejnery (`vector`, `map`, `set`...), algoritmy (`sort`, `find`...), streamy a další:

```cpp
#include <vector>
#include <algorithm>

std::vector<int> numbers = {3, 1, 4, 1, 5, 9, 2, 6};
std::sort(numbers.begin(), numbers.end());
```

### Reference

Kromě ukazatelů má C++ i **reference** — alternativní jméno pro již existující proměnnou, bezpečnější a čitelnější:

```cpp
int x = 10;
int& ref = x;    // reference na x
ref = 20;        // nyní x == 20
```

### RAII a automatická správa zdrojů

C++ přináší koncept **RAII** (Resource Acquisition Is Initialization) — zdroje (paměť, soubory, zámky) se automaticky uvolňují, když objekt zmizí ze scope. Třídy jako `std::string`, `std::vector` nebo `std::unique_ptr` tak v praxi eliminují potřebu ručního `free`:

```cpp
{
    std::vector<int> data(1000);
    // ...
}  // zde se paměť automaticky uvolní
```

### Výjimky

C používá návratové kódy pro chyby. C++ přidává **výjimky** (`throw` / `try` / `catch`):

```cpp
try {
    throw std::runtime_error("něco se pokazilo");
} catch (const std::exception& e) {
    std::cerr << e.what() << std::endl;
}
```

### Přetěžování funkcí a operátorů

V C nesmí dvě funkce mít stejné jméno. V C++ ano, pokud se liší parametry:

```cpp
int add(int a, int b) { return a + b; }
double add(double a, double b) { return a + b; }
```

### Jmenné prostory

C++ zavádí **namespace** jako ochranu před kolizemi názvů — proto `std::cout`, ne jen `cout`. V C tyhle problémy řešíte prefixy (`str_copy`, `list_append`…).

### Stručné shrnutí rozdílů

C je malý, jednoduchý a přímočarý — naučíte se ho za pár dní, ale musíte si všechno dělat sami. C++ je nepřeberně velký — naučit se ho pořádně trvá roky, ale za odměnu dostanete nejvyšší míru abstrakcí bez obětování výkonu.

>  Pokud začínáte, doporučuje se psát v **moderním C++** (C++17, C++20, C++23) — vyhněte se starým vzorům z 90. let jako ručnímu `new`/`delete`, C-style cast a raw pointerům všude.
