### PowerShell Config

```PowerShell
# Install-Module -Name Terminal-Icons, posh-git, posh-dotnet, PSFzf -Repository PSGallery
# PowerShell_profile

Import-Module posh-git
Import-Module posh-dotnet
Import-Module Terminal-Icons
oh-my-posh --init --shell pwsh --config $env:POSH_THEMES_PATH/robbyrussell.omp.json | Invoke-Expression

# Set some useful Alias to shorten typing and save some key stroke 
Set-Alias ll ls 
Set-Alias g git 
Set-Alias grep findstr

# Set Some Option for PSReadLine to show the history of our typed commands
Set-PSReadLineOption -PredictionSource History 
Set-PSReadLineOption -PredictionViewStyle ListView 
Set-PSReadLineOption -EditMode Windows 

#Fzf (Import the fuzzy finder and set a shortcut key to begin searching)
Import-Module PSFzf
Set-PsFzfOption -PSReadlineChordProvider 'Ctrl+f' -PSReadlineChordReverseHistory 'Ctrl+r'

# Utility Command that tells you where the absolute path of commandlets are 
function which ($command) { 
 Get-Command -Name $command -ErrorAction SilentlyContinue | 
 Select-Object -ExpandProperty Path -ErrorAction SilentlyContinue 
}
```
