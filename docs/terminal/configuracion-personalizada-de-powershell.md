# 🖥️ Configuración Personalizada de PowerShell

> Guía completa paso a paso para instalar y configurar un entorno de terminal moderno, productivo y visualmente atractivo en Windows.

---

## 📋 Tabla de Contenidos

- [Requisitos Previos](#-requisitos-previos)
- [1. Instalar PowerShell 7](#1️⃣-instalar-powershell-7)
- [2. Instalar la fuente Nerd Font](#2️⃣-instalar-la-fuente-nerd-font)
- [3. Instalar Oh My Posh](#3️⃣-instalar-oh-my-posh)
- [4. Instalar Terminal-Icons](#4️⃣-instalar-terminal-icons)
- [5. Instalar PSReadLine](#5️⃣-instalar-psreadline)
- [6. Instalar Zoxide](#6️⃣-instalar-zoxide)
- [7. Instalar herramientas CLI (fd, fzf, bat, rg)](#7️⃣-instalar-herramientas-cli)
- [8. Configurar el perfil `$PROFILE`](#8️⃣-configurar-el-perfil-profile)
- [9. Funciones personalizadas](#9️⃣-funciones-personalizadas)
- [10. Atajos de teclado](#-atajos-de-teclado)
- [Diagrama de arquitectura](#-diagrama-de-arquitectura)
- [Referencia rápida](#-referencia-rápida)

---

## 🔧 Requisitos Previos

Antes de comenzar, asegúrate de tener:

| Requisito | Versión mínima | Descarga / Más info |
|-----------|---------------|---------------------|
| Windows | 10 / 11 | Preferible Windows 11 |
| Microsoft Store | Actualizada | Incluida en Windows |
| Winget | Incluido en Win 11 | [github.com/microsoft/winget-cli](https://github.com/microsoft/winget-cli/releases) |
| VS Code | Cualquiera | [code.visualstudio.com](https://code.visualstudio.com/) |
| Git | 2.x | [git-scm.com/download/win](https://git-scm.com/download/win) |

---

## 1️⃣ Instalar PowerShell 7

### Opción A — Microsoft Store (recomendada)

1. Abre la **Microsoft Store** o visita directamente: [apps.microsoft.com — PowerShell](https://apps.microsoft.com/store/detail/powershell/9MZ1SNWT0N5D)
2. Busca **PowerShell**
3. Selecciona la versión publicada por **Microsoft Corporation**
4. Haz clic en **Obtener / Instalar**

### Opción B — Winget (terminal)

```powershell
winget install --id Microsoft.PowerShell --source winget
```

### Opción C — Descarga directa

Visita [github.com/PowerShell/PowerShell/releases](https://github.com/PowerShell/PowerShell/releases) y descarga el instalador `.msi` para Windows x64. Busca el archivo con nombre similar a `PowerShell-7.x.x-win-x64.msi`.

> ✅ **Verificación:** Abre una nueva terminal y ejecuta:
> ```powershell
> $PSVersionTable.PSVersion
> ```
> Debes ver `Major: 7` o superior.

---

## 2️⃣ Instalar la Fuente Nerd Font

Oh My Posh utiliza iconos especiales que requieren una fuente **Nerd Font** para renderizarse correctamente.

### Instalar con Oh My Posh (después del paso 3)

```powershell
oh-my-posh font install meslo
```

### O descargar manualmente

1. Ve a [nerdfonts.com/font-downloads](https://www.nerdfonts.com/font-downloads)
2. Descarga **[MesloLGS NF](https://github.com/romkatv/powerlevel10k-media/raw/master/MesloLGS%20NF%20Regular.ttf)** o **[CaskaydiaCove NF](https://github.com/ryanoasis/nerd-fonts/releases/latest/download/CascadiaCode.zip)**
3. Extrae el `.zip` → Selecciona todos los `.ttf` → Clic derecho → **Instalar para todos los usuarios**

### Configurar la fuente en Windows Terminal

1. Abre **Windows Terminal** → `Configuración` (Ctrl+,)
2. Selecciona tu perfil de PowerShell
3. Ve a **Apariencia** → **Fuente**
4. Escribe `MesloLGS NF` y guarda

```jsonc
// settings.json de Windows Terminal
{
  "profiles": {
    "defaults": {
      "font": {
        "face": "MesloLGS NF",
        "size": 12
      }
    }
  }
}
```

---

## 3️⃣ Instalar Oh My Posh

Oh My Posh es el motor de prompt que da el aspecto visual moderno a la terminal.

> 🌐 **Sitio oficial:** [ohmyposh.dev](https://ohmyposh.dev) — [Documentación](https://ohmyposh.dev/docs) — [GitHub](https://github.com/JanDeDobbeleer/oh-my-posh)

```powershell
winget install JanDeDobbeleer.OhMyPosh -s winget
```

### Descargar el tema personalizado

El archivo de tema usado es `.jandedobbeleer.omp.json`. Para obtenerlo:

```powershell
# Ver temas disponibles incluidos con Oh My Posh
Get-PoshThemes

# O copiar el tema jandedobbeleer al perfil de usuario
Copy-Item "$env:POSH_THEMES_PATH\jandedobbeleer.omp.json" "$env:USERPROFILE\.jandedobbeleer.omp.json"
```

> 💡 **Temas alternativos comentados en el perfil:**
> - `catppuccin_macchiato.omp.json` — Paleta suave y oscura
> - `powerlevel10k_rainbow.omp.json` — Estilo arco iris muy popular

---

## 4️⃣ Instalar Terminal-Icons

Agrega iconos de archivos y carpetas al explorar directorios con `ls` / `dir`.

> 🌐 **Repositorio:** [github.com/devblackops/Terminal-Icons](https://github.com/devblackops/Terminal-Icons) — [PSGallery](https://www.powershellgallery.com/packages/Terminal-Icons)

```powershell
Install-Module -Name Terminal-Icons -Repository PSGallery -Force
```

> ✅ **Prueba:** Después de configurar el perfil, ejecuta `ls` y verás iconos junto a cada archivo.

---

## 5️⃣ Instalar PSReadLine

PSReadLine mejora drásticamente la experiencia de escritura en la terminal con autocompletado inteligente, historial visual y más.

> 🌐 **Repositorio:** [github.com/PowerShell/PSReadLine](https://github.com/PowerShell/PSReadLine) — [PSGallery](https://www.powershellgallery.com/packages/PSReadLine)

```powershell
Install-Module PSReadLine -Force -SkipPublisherCheck
```

> 💡 PowerShell 7 ya incluye PSReadLine, pero esta versión puede estar desactualizada. Instalar desde la galería garantiza la versión más reciente.

---

## 6️⃣ Instalar Zoxide

Zoxide es un reemplazo inteligente de `cd` que aprende tus directorios más usados y permite saltar a ellos rápidamente.

> 🌐 **Repositorio:** [github.com/ajeetdsouza/zoxide](https://github.com/ajeetdsouza/zoxide)

```powershell
winget install ajeetdsouza.zoxide
```

### Uso básico de Zoxide

```powershell
# Primera vez: navegar normalmente para que zoxide aprenda
cd C:\Users\TuUsuario\Proyectos\MiApp

# Después, desde cualquier lugar:
z MiApp          # salta directamente al directorio
z Pro            # salta al directorio que contenga "Pro"
zi               # modo interactivo con fzf
```

---

## 7️⃣ Instalar Herramientas CLI

Estas herramientas son necesarias para las funciones `vf`, `vg` y `vcd`.

```powershell
# fd — buscador de archivos rápido (reemplazo de find)
winget install sharkdp.fd

# fzf — buscador difuso interactivo
winget install junegunn.fzf

# bat — visor de archivos con syntax highlighting (reemplazo de cat)
winget install sharkdp.bat

# ripgrep (rg) — búsqueda de texto ultrarrápida (reemplazo de grep)
winget install BurntSushi.ripgrep.MSVC
```

### Resumen de herramientas

| Herramienta | Propósito | Reemplaza | Repositorio |
|-------------|-----------|-----------|-------------|
| `fd` | Buscar archivos y carpetas | `find` | [github.com/sharkdp/fd](https://github.com/sharkdp/fd) |
| `fzf` | Selector interactivo fuzzy | — | [github.com/junegunn/fzf](https://github.com/junegunn/fzf) |
| `bat` | Ver archivos con color | `cat` | [github.com/sharkdp/bat](https://github.com/sharkdp/bat) |
| `rg` (ripgrep) | Buscar texto en archivos | `grep` | [github.com/BurntSushi/ripgrep](https://github.com/BurntSushi/ripgrep) |

> ✅ **Verificación de todas las herramientas:**
> ```powershell
> fd --version; fzf --version; bat --version; rg --version; zoxide --version
> ```

---

## 8️⃣ Configurar el Perfil `$PROFILE`

El archivo `$PROFILE` es el script que PowerShell ejecuta automáticamente al iniciar. Es el equivalente al `.bashrc` o `.zshrc` de Linux.

### Abrir el perfil para editar

```powershell
notepad $PROFILE
```

> 💡 Si el archivo no existe, créalo primero:
> ```powershell
> New-Item -Path $PROFILE -ItemType File -Force
> ```

### Contenido completo del perfil

Copia y pega el siguiente contenido en tu `$PROFILE`:

```powershell
# ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
#  🎨  OH MY POSH — Prompt visual con tema personalizado
# ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
oh-my-posh init pwsh --config "$env:USERPROFILE\.jandedobbeleer.omp.json" | Invoke-Expression
# Temas alternativos (descomenta para cambiar):
#oh-my-posh init pwsh --config "C:\Users\Mille\catppuccin_macchiato.omp.json" | Invoke-Expression
#oh-my-posh init pwsh --config "C:\Users\Mille\powerlevel10k_rainbow.omp.json" | Invoke-Expression

# ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
#  📁  TERMINAL-ICONS — Iconos en listados de archivos
# ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Import-Module Terminal-Icons

# ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
#  🧠  PSREADLINE — Autocompletado y experiencia de escritura
# ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Set-PSReadLineOption -PredictionViewStyle ListView     # Lista desplegable de sugerencias
Set-PSReadLineOption -PredictionSource History         # Basado en historial de comandos
Set-PSReadLineKeyHandler -Key Tab -Function Complete   # Tab completa el comando
Set-PSReadLineOption -BellStyle None                   # Sin sonidos al presionar Tab o error

# ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
#  🚀  ZOXIDE — Navegación inteligente de directorios
# ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Invoke-Expression ((zoxide init powershell) -join "`n")

# ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
#  🔍  vf — Abrir archivo con fzf + bat preview + VS Code
# ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
function vf {
    $root = git rev-parse --show-toplevel 2>$null
    if (-not $root) {
        $root = Get-Location
    }
    $item = fd --type f --hidden --exclude .git --exclude target --exclude node_modules |
        fzf --ansi `
            --height 80% `
            --layout=reverse `
            --border `
            --margin=1 `
            --padding=2 `
            --prompt " 📂  Abrir archivo  >  " `
            --delimiter "/" `
            --with-nth=-2.. `
            --color "fg:#cdd6f4,hl:#89b4fa,fg+:#ffffff,hl+:#a6e3a1,pointer:#f38ba8,marker:#fab387" `
            --preview 'bat --style=numbers,changes --color=always --paging=never {1} 2> $null' `
            --preview-window 'right:60%:wrap'
    if ($item) {
        code -r "$root" "$item"
    }
}
Set-PSReadLineKeyHandler -Key Ctrl+r -ScriptBlock { vf }

# ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
#  🔎  vg — Buscar texto en proyecto con ripgrep + fzf
# ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
function vg {
    $root = git rev-parse --show-toplevel 2>$null
    if (-not $root) {
        $root = Get-Location
    }
    $result = rg --line-number --no-heading --color=always --smart-case . |
        fzf --ansi `
            --height 80% `
            --layout=reverse `
            --border `
            --margin=1 `
            --padding=2 `
            --prompt " 🔎  Buscar en proyecto  >  " `
            --delimiter ":" `
            --nth 1,2,3 `
            --preview 'bat --style=numbers --color=always --highlight-line {2} {1} 2> $null' `
            --preview-window 'right:60%:wrap'
    if ($result) {
        $parts = $result -split ":"
        $file = $parts[0]
        $line = $parts[1]
        code -r "$root" --goto "${file}:${line}"
    }
}
Set-PSReadLineKeyHandler -Chord 'Ctrl+Shift+Q' -ScriptBlock { vg }

# ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
#  📁  vcd — Navegar a carpeta con fzf
# ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
function vcd {
    $dir = fd --type d --hidden --exclude .git --exclude target --exclude node_modules |
        fzf --ansi `
            --height 80% `
            --layout=reverse `
            --border `
            --margin=1 `
            --padding=2 `
            --prompt " 📁  Ir a carpeta  >  "
    if ($dir) {
        Set-Location $dir
    }
}
Set-PSReadLineKeyHandler -Chord 'Ctrl+Shift+O' -ScriptBlock { vcd }
```

### Recargar el perfil sin reiniciar

```powershell
. $PROFILE
```

---

## 9️⃣ Funciones Personalizadas

### `vf` — Visual File Finder

Busca y abre cualquier archivo del proyecto en VS Code usando una interfaz interactiva con vista previa.

```
Ctrl + R  →  Activa vf
```

**Flujo de uso:**

```
┌─────────────────────────────────────────────────────┐
│  📂  Abrir archivo  >  _                            │
├─────────────────────────────────────────────────────┤
│  src/main.rs                    │  1 │ fn main() { │
│  src/lib.rs                     │  2 │   println!  │
│  Cargo.toml                     │  3 │ }           │
│  README.md                      │                  │
└─────────────────────────────────────────────────────┘
         Lista de archivos              Preview (bat)
```

| Parámetro fzf | Significado |
|---------------|-------------|
| `--with-nth=-2..` | Muestra solo los 2 últimos segmentos del path |
| `--preview` | Vista previa con `bat` (syntax highlight) |
| `--color` | Esquema de colores Catppuccin |
| `--delimiter "/"` | Divide el path por `/` para `--with-nth` |

---

### `vg` — Visual Grep

Busca texto dentro de todos los archivos del proyecto con ripgrep y permite navegar a la línea exacta en VS Code.

```
Ctrl + Shift + Q  →  Activa vg
```

**Flujo de uso:**

```
┌─────────────────────────────────────────────────────┐
│  🔎  Buscar en proyecto  >  fn main               │
├─────────────────────────────────────────────────────┤
│  src/main.rs:5:fn main() {      │   4 │            │
│  src/lib.rs:12:pub fn main_     │ ► 5 │ fn main()  │
│                                 │   6 │   println! │
└─────────────────────────────────────────────────────┘
      Resultados de rg                 Preview en línea
```

| Parámetro | Significado |
|-----------|-------------|
| `--smart-case` | Insensible a mayúsculas si todo es minúsculas |
| `--highlight-line {2}` | Resalta la línea encontrada en la preview |
| `--goto "${file}:${line}"` | Abre VS Code en la línea exacta |

---

### `vcd` — Visual Change Directory

Navega a cualquier carpeta del proyecto con una interfaz interactiva.

```
Ctrl + Shift + O  →  Activa vcd
```

---

## 🔟 Atajos de Teclado

| Atajo | Función | Descripción |
|-------|---------|-------------|
| `Tab` | `Complete` | Autocompleta el comando actual |
| `Ctrl + R` | `vf` | Buscar y abrir archivo en VS Code |
| `Ctrl + Shift + Q` | `vg` | Buscar texto en el proyecto |
| `Ctrl + Shift + O` | `vcd` | Navegar a carpeta interactivamente |
| `↑ / ↓` | Historial | Navegar sugerencias en ListView |

---

## 🗺️ Diagrama de Arquitectura

```
┌──────────────────────────────────────────────────────────────────┐
│                    POWERSHELL 7  ($PROFILE)                      │
│                                                                  │
│  ┌─────────────┐   ┌──────────────┐   ┌────────────────────┐   │
│  │ OH MY POSH  │   │  PSREADLINE  │   │  TERMINAL-ICONS    │   │
│  │             │   │              │   │                    │   │
│  │ Prompt      │   │ Historial    │   │ Iconos en ls/dir   │   │
│  │ visual con  │   │ ListView     │   │ 📄 📁 🦀 🐍 📦    │   │
│  │ temas .json │   │ Autocomplet. │   │                    │   │
│  └─────────────┘   └──────────────┘   └────────────────────┘   │
│                                                                  │
│  ┌─────────────┐   ┌──────────────────────────────────────────┐ │
│  │   ZOXIDE    │   │         FUNCIONES PERSONALIZADAS         │ │
│  │             │   │                                          │ │
│  │ z <dir>     │   │  vf (Ctrl+R)    → fd | fzf | bat | code │ │
│  │ Aprende tus │   │  vg (Ctrl+S+Q)  → rg | fzf | bat | code │ │
│  │ dirs usados │   │  vcd (Ctrl+S+O) → fd | fzf | cd         │ │
│  └─────────────┘   └──────────────────────────────────────────┘ │
│                                                                  │
│  Herramientas externas: fd  •  fzf  •  bat  •  rg  •  git      │
└──────────────────────────────────────────────────────────────────┘
```

---

## 📌 Referencia Rápida

### Instalación completa en una sola sesión

```powershell
# 1. PowerShell 7
winget install --id Microsoft.PowerShell --source winget

# 2. Oh My Posh
winget install JanDeDobbeleer.OhMyPosh -s winget

# 3. Fuente Nerd Font
oh-my-posh font install meslo

# 4. Herramientas CLI
winget install sharkdp.fd junegunn.fzf sharkdp.bat BurntSushi.ripgrep.MSVC ajeetdsouza.zoxide

# 5. Módulos de PowerShell
Install-Module -Name Terminal-Icons -Repository PSGallery -Force
Install-Module PSReadLine -Force -SkipPublisherCheck

# 6. Copiar tema de Oh My Posh
Copy-Item "$env:POSH_THEMES_PATH\jandedobbeleer.omp.json" "$env:USERPROFILE\.jandedobbeleer.omp.json"

# 7. Crear/editar perfil
notepad $PROFILE
```

### Ubicaciones importantes

| Ruta | Descripción |
|------|-------------|
| `$PROFILE` | Script de inicio de PowerShell |
| `$env:USERPROFILE\.jandedobbeleer.omp.json` | Tema activo de Oh My Posh |
| `$env:POSH_THEMES_PATH` | Directorio con todos los temas incluidos |

### Cambiar de tema rápidamente

```powershell
# Ver todos los temas disponibles
Get-PoshThemes

# Cambiar tema temporalmente (esta sesión)
oh-my-posh init pwsh --config "$env:POSH_THEMES_PATH\catppuccin_macchiato.omp.json" | Invoke-Expression

# Para cambiar permanentemente: editar $PROFILE y cambiar la línea activa
notepad $PROFILE
```

---

## ❓ Solución de Problemas

### Los iconos no se muestran (aparecen cuadrados o signos de interrogación)

→ La fuente **Nerd Font** no está instalada o no está seleccionada en Windows Terminal.
Revisa el [Paso 2](#2️⃣-instalar-la-fuente-nerd-font).

### El perfil no carga (error de ejecución de scripts)

```powershell
# Ejecutar como Administrador:
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

### `oh-my-posh` no se reconoce como comando

→ Reinicia la terminal después de instalar con winget. Si persiste:
```powershell
$env:PATH += ";$env:LOCALAPPDATA\Programs\oh-my-posh\bin"
```

### `zoxide` no aprende los directorios

→ Asegúrate de que la línea `Invoke-Expression ((zoxide init powershell) -join "`n")` esté en el perfil y de haber recargado con `. $PROFILE`.

---

## 🔗 Todos los Links de Descarga

| Herramienta | Sitio oficial | GitHub / Releases | PSGallery |
|-------------|--------------|-------------------|-----------|
| **PowerShell 7** | [Microsoft Store](https://apps.microsoft.com/store/detail/powershell/9MZ1SNWT0N5D) | [Releases](https://github.com/PowerShell/PowerShell/releases) | — |
| **Oh My Posh** | [ohmyposh.dev](https://ohmyposh.dev) | [GitHub](https://github.com/JanDeDobbeleer/oh-my-posh) | — |
| **Nerd Fonts** | [nerdfonts.com](https://www.nerdfonts.com/font-downloads) | [Releases](https://github.com/ryanoasis/nerd-fonts/releases) | — |
| **Terminal-Icons** | — | [GitHub](https://github.com/devblackops/Terminal-Icons) | [PSGallery](https://www.powershellgallery.com/packages/Terminal-Icons) |
| **PSReadLine** | — | [GitHub](https://github.com/PowerShell/PSReadLine) | [PSGallery](https://www.powershellgallery.com/packages/PSReadLine) |
| **Zoxide** | — | [GitHub](https://github.com/ajeetdsouza/zoxide) | — |
| **fd** | — | [GitHub](https://github.com/sharkdp/fd) | — |
| **fzf** | — | [GitHub](https://github.com/junegunn/fzf) | — |
| **bat** | — | [GitHub](https://github.com/sharkdp/bat) | — |
| **ripgrep** | — | [GitHub](https://github.com/BurntSushi/ripgrep) | — |
| **VS Code** | [code.visualstudio.com](https://code.visualstudio.com/) | [Releases](https://github.com/microsoft/vscode/releases) | — |
| **Git** | [git-scm.com](https://git-scm.com/download/win) | [Releases](https://github.com/git-for-windows/git/releases) | — |
| **Windows Terminal** | [Microsoft Store](https://apps.microsoft.com/store/detail/windows-terminal/9N0DX20HK701) | [GitHub](https://github.com/microsoft/terminal) | — |

---

*Documentación generada para la configuración personal de PowerShell con Oh My Posh + PSReadLine + Zoxide + fd/fzf/bat/rg.*