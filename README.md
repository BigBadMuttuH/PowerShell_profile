### Install Soft
```cmd
scoop install git
scoop install bat fzf eza ripgrep fd lazygit zoxide
# Модули PowerShell:
Install-Module PSFzf -Scope CurrentUser -Force
Install-Module Terminal-Icons -Scope CurrentUser -Force
# PSReadLine уже встроен в современные PS, но на всякий:
# Install-Module PSReadLine -Scope CurrentUser -Force
```


### PowerShell Config

```PowerShell
# --- Prompt (oh-my-posh) ---
oh-my-posh init pwsh --config robbyrussell | Invoke-Expression

# --- Aliases/shortcuts ---
Set-Alias lg lazygit

# --- Модули / украшательства ---
Import-Module Terminal-Icons -ErrorAction SilentlyContinue

# --- fzf базовые настройки ---
$env:FZF_DEFAULT_OPTS    = '--height 40% --layout=reverse --border'
$env:FZF_DEFAULT_COMMAND = 'fd --type f --hidden --follow --exclude .git'

# --- PSReadLine: поведение и подсказки ---
Import-Module PSReadLine -ErrorAction SilentlyContinue
Set-PSReadLineOption -EditMode Windows
Set-PSReadLineOption -PredictionSource HistoryAndPlugin
Set-PSReadLineOption -PredictionViewStyle ListView
# Удобный поиск по истории по префиксу стрелками ↑/↓
Set-PSReadLineKeyHandler -Key UpArrow   -Function HistorySearchBackward
Set-PSReadLineKeyHandler -Key DownArrow -Function HistorySearchForward

# --- PSFzf: интеграция fzf с PSReadLine ---
Import-Module PSFzf -ErrorAction SilentlyContinue
Set-PsFzfOption `
  -PSReadlineChordProvider 'Ctrl+t' `
  -PSReadlineChordReverseHistory 'Ctrl+r' `
  -EnableAliasFuzzyHistory `
  -EnableFd `
  -EnableRipgrep `
  -TabExpansion

# --- zoxide: умное cd ---
#  (быстрые прыжки по часто используемым папкам: z, zi, z path)
Invoke-Expression (& { (zoxide init powershell | Out-String) })

# --- Функции в стиле Linux (вместо alias с параметрами) ---

# ls на базе eza (быстро, с иконками, директории сверху)
function ls { eza --color=always --icons=always --group-directories-first @args }

function ll { eza -lh  --git --icons=always --group-directories-first @args }
function la { eza -lha --git --icons=always --group-directories-first @args }
function llt { eza -lhT --level=2 --icons=always --group-directories-first @args }

# cat -> bat (красиво, без пейджера, как обычный cat)
function cat { bat --style=plain --paging=never @args }

# grep через ripgrep с цветом (быстрее и умнее стандартного grep)
function grep { rg --color=always @args }

# Быстрый поиск файла в каталоге (Ctrl+T уже даёт провайдер, но отдельная команда тоже удобна)
function fzf-file { fd --type f --hidden --follow --exclude .git | fzf }

# Быстрый cd в выбранную папку
function cdf { fd --type d --hidden --follow --exclude .git | fzf | ForEach-Object { Set-Location $_ } }

# Поиск по содержимому через rg + fzf с превью
function rga {
    param([string]$q)
    if (-not $q) { $q = Read-Host 'Query' }
    rg --line-number --no-heading --hidden --follow --color always $q |
        fzf --ansi --preview 'bat --style=plain --color=always --line-range :200 {1} --highlight-line {2}' `
            --delimiter : --nth 3.. --bind 'enter:become(powershell -NoProfile -Command "code --goto {1}:{2}")'
}

# --- Качество жизни ---
# Сделать Ctrl+L очисткой экрана, если вдруг не работает
Set-PSReadLineKeyHandler -Key Ctrl+L -Function ClearScreen
```
