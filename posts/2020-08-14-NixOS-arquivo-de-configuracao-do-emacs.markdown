---
title: 'NixOS - Configurando o Emacs com um script nix'
description: 'Cada vez me surpreendo mais'
tags: emacs, nixos, functional
---

Seguindo as boas práticas do NixOS, eu nâo configurei de bate pronto o meu emacs.
No manual e em alguns posts, a recomendação é configurar tudo com um script para
que o sistema mantenha o seu aspecto puro, isto é, sem efeitos colaterais.

Eu atingi esse ponto, criando um arquivo chamado ```/.config/nixpkgs/config.nix```.
Nesse arquivo, é gerado um arquivo numa store. Desse modo, não afeta a pasta do
usuário e não compromente o estado do OS. Para criar um ambiente novo, é só
transportar esse arquivo e a mágica acontecerá novamente.

Segue o snippet do arquivo

```nix
{

  allowUnfree = true;

  packageOverrides = pkgs: with pkgs; rec {

  myEmacsConfig = writeText "default.el" ''
;; initialize package

(require 'package)

(package-initialize)

(custom-set-variables
 ;; custom-set-variables was added by Custom.
 ;; If you edit it by hand, you could mess it up, so be careful.
 ;; Your init file should contain only one such instance.
 ;; If there is more than one, they won't work right.
 '(backup-directory-alist (quote (("" . "/tmp"))))
 '(column-number-mode t)
 '(package-selected-packages (quote (monokai-theme haskell-mode))))
(custom-set-faces
 ;; custom-set-faces was added by Custom.
 ;; If you edit it by hand, you could mess it up, so be careful.
 ;; Your init file should contain only one such instance.
 ;; If there is more than one, they won't work right.
 )

(setq-default indent-tabs-mode nil)
(setq-default truncate-lines t)
(load-theme 'monokai t)
(toggle-frame-maximized)

(custom-set-variables '(haskell-stylish-on-save t))
(add-hook 'haskell-mode-hook 'turn-on-haskell-doc-mode)
(add-hook 'haskell-mode-hook 'turn-on-haskell-indentation)
(add-to-list 'completion-ignored-extensions ".hi")

 '';
    
  steam = pkgs.steam.override {
    withJava = true;
  };

  myDesktop = pkgs.buildEnv {
    name = "my-desktop";
    paths = [
      ; # pacotes para o usuário
    ];
  };

  myEmacs = emacsWithPackages (epkgs: (with epkgs.melpaStablePackages; [ 
      (runCommand "default.el" {} ''
mkdir -p $out/share/emacs/site-lisp
cp ${myEmacsConfig} $out/share/emacs/site-lisp/default.el
'')
      magit          # ; Integrate git <C-x g>
      haskell-mode
      use-package
    ]) ++ (with epkgs.melpaPackages; [
      markdown-mode
      w3m
      ztree
    ]));

  };
}

```

Esse arquivo será gravado em dentro da store do emacs e a configuração será
apenas vista para o usuário.

Realmente, se eu fosse um sysop que mantivesse um Linux para uma corporação,
eu escolheria o NixOS para ser o meu sistema principal sem medo de ser feliz.
Para servidores, eu não pensaria duas vezes.

