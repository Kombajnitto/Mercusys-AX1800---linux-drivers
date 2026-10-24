# Mercusys AX1800 Linux drivers

Ten projekt zawiera sterownik DKMS dla adaptera Wi‑Fi Mercusys MA72XH/AX1800 oparty na chipsetach Realtek rtw89/8852au (vendor ID: 2c4e:0124).

## Co zawiera

- źródła sterownika do instalacji przez DKMS w katalogu `dkms/`
- skrypt `install.sh` do automatycznego dodania, zbudowania i zainstalowania modułu
- katalog `rtw89-build/` z przygotowanymi plikami build dla bieżącego środowiska

## Wymagania

Na Ubuntu/Debian musisz mieć zainstalowane pakiety:

```bash
sudo apt update
sudo apt install -y build-essential dkms linux-headers-$(uname -r)
```

Na CachyOS zainstaluj DKMS, Clang, narzędzia kompilacji i nagłówki dokładnie
odpowiadające używanemu wariantowi kernela. Dla standardowego kernela CachyOS:

```bash
sudo pacman -S --needed base-devel dkms clang linux-cachyos-headers
```

Dla kernela LTS użyj `linux-cachyos-lts-headers`; dla innego wariantu wybierz
odpowiadający mu pakiet `*-headers`. Sterownik jest budowany przez Clang, bo
kernel CachyOS może używać opcji kompilatora nieobsługiwanych przez GCC.

## Instalacja

1. Pobierz repozytorium:

```bash
git clone https://github.com/Ikabytez/Mercusys-AX1800---linux-drivers.git
cd Mercusys-AX1800---linux-drivers
```

2. Ustaw uprawnienia do skryptu instalacyjnego:

```bash
chmod +x install.sh
```

3. Uruchom instalację:

```bash
sudo ./install.sh
```

Skrypt wykonuje kolejno:

- usunięcie poprzedniej wersji DKMS, jeśli istnieje
- skopiowanie źródeł do `/usr/src`
- `dkms add`, `dkms build` i `dkms install`
- `depmod -a`
- sprawdzenie obecności identyfikatora `2c4e:0124`

Uwaga: test identyfikatora w `modinfo` potwierdza obecność aliasu urządzenia,
ale nie potwierdza, że moduł został załadowany ani że adapter działa. Jeśli
Secure Boot jest włączony, kernel może wymagać podpisania modułów DKMS.

4. Po zakończeniu zrestartuj komputer lub odłącz i podłącz ponownie adapter USB. W razie potrzeby uruchom ponownie system.

## Weryfikacja

Po restarcie możesz sprawdzić, czy moduł został poprawnie załadowany:

```bash
modinfo rtw89_8852au | grep -i 2C4E
```

Jeśli wynik zwróci `2c4e:0124`, instalacja jest poprawna.

## Uwagi

- Repozytorium jest przeznaczone do adaptera Mercusys MA72XH z ID `2c4e:0124`.
- Jeśli system używa innego jądra, zainstaluj odpowiadające mu `linux-headers` dla tej wersji.
- Przy problemach z modułem sprawdź diagnostykę DKMS:

```bash
dkms status
```
