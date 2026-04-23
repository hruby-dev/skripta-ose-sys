# Rust

**Rust** je moderní systémový programovací jazyk, který kombinuje výkon C/C++ s bezpečností paměti — a to vše **bez garbage collectoru**. Kompilátor (rustc) před překladem zkontroluje, že kód nedělá nic nebezpečného s pamětí, a pokud ano, prostě se nezkompiluje.

## Instalace

Rust se nejjednodušeji instaluje pomocí nástroje **`rustup`**, který spravuje verze kompilátoru a toolchainů.

### Linux / macOS

Jeden příkaz, který nainstaluje všechno potřebné:

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

Po instalaci je potřeba buď otevřít nový terminál, nebo načíst proměnné prostředí:

```bash
source $HOME/.cargo/env
```

### Windows

Stáhněte si instalátor `rustup-init.exe` z [rustup.rs](https://rustup.rs/) a spusťte. Alternativně přes `winget`:

```powershell
winget install Rustlang.Rustup
```

### Ověření instalace

```bash
rustc --version
cargo --version
```

Měli byste vidět něco jako `rustc 1.XX.X` a `cargo 1.XX.X`.

## Cargo — co to je a jak funguje

**Cargo** je oficiální build systém a package manager Rustu. Prakticky vše kolem Rust projektů se řeší přes něj:

- vytváření nových projektů
- kompilace a spouštění
- správa závislostí (balíčků z [crates.io](https://crates.io/))
- testování, benchmarky, dokumentace

Zatímco v C a C++ si musíte buildovací systém (Make, CMake, Meson…) vybrat a nastavit sami, v Rustu je Cargo standardem a funguje hned od začátku.

### Základní příkazy

```bash
cargo new projekt      # vytvoří novou složku s projektem
cargo build            # zkompiluje projekt (debug)
cargo build --release  # optimalizovaný release build
cargo run              # zkompiluje A spustí
cargo test             # spustí testy
cargo check            # rychlá kontrola bez generování binárky
cargo add nazev        # přidá závislost do projektu
```

### Struktura projektu

Když spustíte `cargo new hello`, Cargo vytvoří:

```
hello/
├── Cargo.toml      # manifest projektu (metadata, závislosti)
├── .gitignore      # předpřipraveno pro Git
└── src/
    └── main.rs     # vstupní bod programu
```

Soubor `Cargo.toml` vypadá takto:

```toml
[package]
name = "hello"
version = "0.1.0"
edition = "2021"

[dependencies]
```

Závislosti se přidávají buď ručně do `[dependencies]`, nebo pohodlněji pomocí `cargo add`:

```bash
cargo add serde
```

## Hello, World!

### Varianta 1: Přes Cargo (doporučený způsob)

```bash
cargo new hello
cd hello
cargo run
```

A je to! Cargo vám rovnou vygeneroval Hello World, zkompiloval ho a spustil:

```
   Compiling hello v0.1.0 (/path/to/hello)
    Finished dev [unoptimized + debuginfo] target(s) in 0.42s
     Running `target/debug/hello`
Hello, world!
```

Obsah vygenerovaného `src/main.rs`:

```rust
fn main() {
    println!("Hello, world!");
}
```

### Varianta 2: Bez Carga, ručně přes rustc

Pro jednorázové skripty se hodí vědět, že Rust zvládne i kompilaci jednoho souboru:

```bash
echo 'fn main() { println!("Hello, World!"); }' > hello.rs
rustc hello.rs
./hello
```

V reálných projektech ale **vždycky používejte Cargo** — jakmile budete chtít přidat knihovnu nebo napsat test, oceníte to.

### Co se v tom kódu děje?

```rust
fn main() {
    println!("Hello, world!");
}
```

- `fn main()` — vstupní bod programu, stejně jako `main` v C/C++
- `println!` — **makro** (poznáte podle vykřičníku), vypíše text na stdout a přidá nový řádek
- Žádné `return 0;` není potřeba — pokud funkce nic nevrací, implicitně vrací `()` (jednotkový typ)

## Co dělá Rust jiný

Bez zacházení do hloubky — hlavní nápady, které Rust přináší:

- **Ownership & borrowing** — každá hodnota má právě jednoho vlastníka. Když vlastník zmizí, paměť se uvolní. Pokud chcete hodnotu "půjčit", používáte reference s jasnými pravidly.
- **Borrow checker** — kompilátor při překladu kontroluje, že nedojde k use-after-free, data race ani dangling pointeru. Pokud ano, program se nezkompiluje.
- **Žádné `null`** — místo toho typ `Option<T>`, který vás nutí explicitně ošetřit případ "žádná hodnota".
- **Žádné výjimky** — chyby se vracejí přes typ `Result<T, E>`, takže je z kódu jasně vidět, co může selhat.
- **Paměťová bezpečnost bez GC** — vše výše uvedené běží bez runtime overheadu. Výsledné binárky jsou stejně rychlé jako C/C++.
- **Moderní tooling** — `cargo`, formátter `rustfmt`, linter `clippy`, skvělé error messages od kompilátoru.

Rust má pověst jazyka se **strmou křivkou učení** — zejména borrow checker může zpočátku frustrovat. Ale jakmile si zvyknete, získáte nástroj, ve kterém jde psát systémový kód s mnohem větším klidem, než jaký vám dá C nebo C++.

>  Pokud se chcete naučit Rust pořádně, doporučuje se oficiální kniha [*The Rust Programming Language*](https://doc.rust-lang.org/book/) (zdarma) a interaktivní cvičení [Rustlings](https://github.com/rust-lang/rustlings).
