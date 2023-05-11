# syntax=docker/dockerfile:1
FROM rust:latest AS build #latest is bullseye
WORKDIR /usr/src/myapp

ARG dofiles_url
ENV dotfiles_url $dotfiles
ARG gh_token
ENV gh_token $gh_token

# Installs tooling for dev
RUN apt-get install stow && cd ~/GitHub/Dotfiles && chmod +x .stow && ./.stow && \
  apt-get upgrade && \
  apt-get install fish git && \
  add-apt-repository ppa:maveonair/helix-editor && \
  apt update && \
  apt install helix && \
  hx -g fetch && hx -g build && \
  cargo install --locked zellij && \
  LAZYGIT_VERSION=$(curl -s "https://api.github.com/repos/jesseduffield/lazygit/releases/latest" | grep -Po '"tag_name": "v\K[^"]*') && \
  cd /tmp && curl -Lo lazygit.tar.gz "https://github.com/jesseduffield/lazygit/releases/latest/download/lazygit_${LAZYGIT_VERSION}_Linux_x86_64.tar.gz" && \
  tar xf lazygit.tar.gz lazygit && cd - && \
  install lazygit /usr/local/bin && \
  echo "Done installing Software"
  
RUN useradd -m dev && mkdir -r /home/dev/GitHub/ && cd /home/dev/Github \
  git clone https://$gh_token@github.com/$dotfiles_url && \
  usermod -aG sudo dev

COPY ~/Documents/GitHub/Dotfiles /home/dev/GitHub/Dotfiles
FROM build AS development
 
CMD ["su", "dev", "&&", "zellij"]