# Kompletny przewodnik po narzędziach terminalowych dla Windows


![PWSH Setup](https://github.com/user-attachments/assets/1407b0a7-c369-419e-9f9e-29e6adee4cb3)



## Spis treści
- [Przygotowanie środowiska](#przygotowanie-środowiska)
- [Instalacja czcionek](#instalacja-czcionek)
- [Instalacja narzędzi](#instalacja-narzędzi)
- [Opis narzędzi](#opis-narzędzi)
- [Konfiguracja PowerShell](#konfiguracja-powershell)
- [Skróty klawiszowe](#skróty-klawiszowe)
- [Wskazówki](#wskazówki)

## Przygotowanie środowiska

### 1. Instalacja Windows Terminal
```powershell
winget install Microsoft.WindowsTerminal
```

### 2. Instalacja PowerShell 7
```powershell
winget install Microsoft.PowerShell
```

## Instalacja czcionek

### Metoda przez Scoop
```powershell
# Ustawienie polityki wykonywania dla PowerShell
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser

# Instalacja Scoop (jeśli nie jest zainstalowany)
irm get.scoop.sh | iex

# Dodanie repozytorium czcionek
scoop bucket add nerd-fonts

# Instalacja czcionki JetBrainsMono (zalecana)
scoop install JetBrainsMono-NF
```

### Konfiguracja Windows Terminal

1. Otwórz Windows Terminal
2. Naciśnij `Ctrl + ,` aby otworzyć ustawienia
3. Kliknij PowerShell w menu po lewej stronie
4. W zakładce "Wygląd":
   - Znajdź pole "Czcionka"
   - Wybierz "JetBrainsMono Nerd Font"
   - Ustaw rozmiar czcionki (np. 11)
   - Zapisz ustawienia

## Instalacja narzędzi

### 1. Instalacja Scoop i repozytoriów

```powershell
# Instalacja Scoop (jeśli nie został zainstalowany wcześniej)
irm get.scoop.sh | iex

# Dodanie repozytoriów
scoop bucket add extras
scoop bucket add versions
```

### 2. Instalacja wszystkich narzędzi

```powershell
scoop install `
    csvlens `        # Przeglądarka CSV
    spotify-player ` # Odtwarzacz Spotify
    micro `          # Edytor tekstu
    mc `            # Midnight Commander
    lf `            # Menadżer plików
    jc `            # Konwerter do JSON
    lazygit `       # Git UI
    lazydocker `    # Docker UI
    oh-my-posh `    # Prompt
    gh `            # GitHub CLI
    zoxide `        # Inteligentna nawigacja
    bottom `        # Monitor systemu
    ripgrep `       # Wyszukiwanie tekstu
    glow `          # Przeglądarka Markdown
    marktext `      # Edytor Markdown
    quicklook `     # Szybki podgląd
    mupdf `         # Przeglądarka PDF
    vale `          # Linter tekstu
    flow-launcher ` # Launcher
    everything      # Wyszukiwarka plików
```

## Opis narzędzi

### 📊 Narzędzia do przeglądania danych
- **CSVLens** 
  - Przeglądanie plików CSV
  - Użycie: `csvlens dane.csv`
  - Funkcje: sortowanie, filtrowanie, wyszukiwanie

### 🎵 Multimedia
- **Spotify Player**
  - Terminal UI dla Spotify
  - Wymaga konta Premium
  - Skróty: `q` (wyjście), `space` (play/pause)

### ✏️ Edycja tekstu
- **Micro**
  - Nowoczesny edytor tekstowy
  - Użycie: `micro plik.txt`
  - Obsługuje mysz i podstawowe skróty (Ctrl+S, Ctrl+Q)

### 📂 Menadżery plików
- **Midnight Commander (mc)**
  - Klasyczny menadżer dwupanelowy
  - Użycie: `mc`
- **LF**
  - Lekki i szybki menadżer plików
  - Użycie: `lf`
- **Broot**
  - Nawigacja po drzewie katalogów
  - Użycie: `br`

### 🔧 Narzędzia deweloperskie
- **LazyGit**
  - Terminal UI dla Git
  - Użycie: `lazygit` lub `lg`
- **LazyDocker**
  - Terminal UI dla Docker
  - Użycie: `lazydocker` lub `ld`
- **JC**
  - Konwersja wyjścia CLI do JSON
  - Użycie: `jc [komenda]`

### 🔍 Wyszukiwanie
- **Ripgrep**
  - Szybkie przeszukiwanie tekstu
  - Użycie: `rg wzorzec`
- **FD**
  - Wyszukiwanie plików
  - Użycie: `fd wzorzec`
- **Everything**
  - Błyskawiczne wyszukiwanie plików
  - Integracja z Flow Launcher

## Konfiguracja PowerShell

### 1. Utworzenie/edycja profilu

```powershell
# Utworzenie profilu jeśli nie istnieje
if (!(Test-Path -Path $PROFILE)) {
    New-Item -ItemType File -Path $PROFILE -Force
}

# Otwarcie profilu do edycji
micro $PROFILE
```

### 2. Zawartość profilu

```powershell
# Oh My Posh - customizacja promptu
oh-my-posh init pwsh --config "$env:POSH_THEMES_PATH\paradox.omp.json" | Invoke-Expression

# Inicjalizacja Zoxide
Invoke-Expression (& {
    $hook = if ($PSVersionTable.PSVersion.Major -lt 6) { 'prompt' } else { 'pwd' }
    (zoxide init --hook $hook powershell | Out-String)
})


# WSL Here - funkcja do otwierania WSL w aktualnym katalogu
function Start-WSLHere {
    param (
        [Parameter(ValueFromRemainingArguments=$true)]
        [string[]]$Command
    )
    # Pobierz aktualną ścieżkę
    $currentPath = (Get-Location).Path
    # Zamień backslashe na forwardslashe
    $wslPath = $currentPath -replace "\\", "/"
    # Zamień literę dysku na ścieżkę WSL
    if ($wslPath -match "^([A-Za-z]):(.*)") {
        $driveLetter = $matches[1].ToLower()
        $remainingPath = $matches[2]
        $wslPath = "/mnt/$driveLetter$remainingPath"
    }
    if ($Command) {
        # Jeśli podano komendę, wykonaj ją w WSL
        $commandString = $Command -join " "
        wsl.exe --cd "$wslPath" $commandString
    }
    else {
        # Jeśli nie podano komendy, uruchom interaktywną sesję WSL
        wsl.exe --cd "$wslPath"
    }
}
# Dodaj alias dla wygody
Set-Alias -Name wslhere -Value Start-WSLHere

# Aliasy
Set-Alias -Name vim -Value micro
Set-Alias -Name lg -Value lazygit
Set-Alias -Name ld -Value lazydocker
Set-Alias -Name ll -Value ls

# PSReadLine - historia komend
Set-PSReadLineOption -PredictionSource History
Set-PSReadLineOption -PredictionViewStyle ListView

# Funkcje pomocnicze
function which ($command) {
    Get-Command -Name $command -ErrorAction SilentlyContinue |
        Select-Object -ExpandProperty Path -ErrorAction SilentlyContinue
}

# Funkcja do szybkiej nawigacji w górę
function .. { Set-Location .. }
function ... { Set-Location ..\.. }

# Terminal Icons - ikony plików
Import-Module Terminal-Icons
```

### 3. Załadowanie konfiguracji

```powershell
. $PROFILE
```

## Skróty klawiszowe

### Windows Terminal
- `Ctrl + ,` - Ustawienia
- `Ctrl + Shift + T` - Nowa zakładka
- `Ctrl + Tab` - Przełączanie zakładek
- `Alt + Shift + D` - Podział pionowy
- `Alt + Shift + -` - Podział poziomy

### Flow Launcher
- `Alt + Space` - Aktywacja
- `Ctrl + O` - Kontekstowe menu pliku
- `Tab` - Akcje dla wyniku

### Micro
- `Ctrl + S` - Zapisz
- `Ctrl + Q` - Wyjście
- `Ctrl + E` - Command palette
- `Ctrl + F` - Wyszukaj
- `Ctrl + G` - Idź do linii

### LF
- `h,j,k,l` - Nawigacja
- `q` - Wyjście
- `y` - Kopiuj
- `p` - Wklej
- `d` - Usuń
- `a` - Nowy plik

## Wskazówki

1. **Aktualizacja narzędzi**
   ```powershell
   scoop update *
   ```

2. **Czyszczenie cache Scoop**
   ```powershell
   scoop cleanup *
   ```

3. **Backup konfiguracji**
   - Zapisz zawartość `$PROFILE`
   - Zachowaj konfigurację Windows Terminal
   - Eksportuj ustawienia Flow Launcher

4. **Rozwiązywanie problemów**
   - Sprawdź `$PROFILE` pod kątem błędów
   - Upewnij się, że czcionka jest poprawnie zainstalowana
   - Sprawdź uprawnienia do wykonywania skryptów

5. **Dobre praktyki**
   - Regularnie aktualizuj narzędzia
   - Twórz kopie zapasowe konfiguracji
   - Dostosuj motywy do swoich preferencji

## Dodatkowe motywy

Aby zmienić motyw Oh My Posh:

1. Zobacz dostępne motywy:
   ```powershell
   Get-PoshThemes
   ```

2. Wybierz motyw i zaktualizuj profil:
   ```powershell
   oh-my-posh init pwsh --config "$env:POSH_THEMES_PATH\[nazwa-motywu].omp.json" | Invoke-Expression
   ```

## Problemy i rozwiązania

1. **Problem z czcionką**
   ```powershell
   scoop uninstall JetBrainsMono-NF
   scoop install JetBrainsMono-NF
   ```

2. **Resetowanie PowerShell**
   ```powershell
   pwsh -NoProfile
   ```

3. **Sprawdzenie instalacji**
   ```powershell
   scoop list
   ```

6. **Używanie WSL w aktualnym katalogu**
   - Użyj komendy `wslhere` aby otworzyć WSL w bieżącym katalogu
   - Możesz również przekazać komendę do wykonania: `wslhere ls -la`
