sh -c "$(curl -fsSL https://raw.githubusercontent.com/Linuxbrew/install/master/install.sh)"

brew install fish

mkdir -p ~/.config/fish

vim ~/.config/fish/config.fish

set -x HOMEBREW_PREFIX /home/linuxbrew/.linuxbrew
set -x HOMEBREW_CELLAR /home/linuxbrew/.linuxbrew/Cellar
set -x HOMEBREW_REPOSITORY /home/linuxbrew/.linuxbrew/Homebrew
set -x PATH /home/linuxbrew/.linuxbrew/bin:/home/linuxbrew/.linuxbrew/sbin $PATH
set -x MANPATH /home/linuxbrew/.linuxbrew/share/man $MANPATH
set -x INFOPATH /home/linuxbrew/.linuxbrew/share/info $INFOPATH


chsh -s /home/linuxbrew/.linuxbrew/bin/fish
