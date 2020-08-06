---
title: 'NixOS - A descoberta de uma grande ideia'
description: 'Como isolar pacotes dentro de um mesmo sistema operacional'
tags: computacao, linux, devops
---

Essa semana instalei um sistema operacional GNU/Linux chamado NixOS. Que
descoberta fantástica. Essa distro linux permite isolar pacotes de dependências,
isto é, permite que um binário possa depender de um biblioteca de determinada
versão. Isso diminui o problema de DLL Hell. O DLL Hell é quando instalamos
uma versão nova de determinada library e esta quebra pacotes já instalados
no S.O. . Muito comum em ambiente windows.

Mais um ponto positivo são os scripts de ativação do shell. Isso permite que
um projeto mais restrito com versões de dependências não polua o teu ambiente
de produção local. Por exemplo, minha máquina tem versão 3 de um componente XYZ,
mas o meu projeto de desenvolvimento utiliza a versão 1. Com o NixOS, é possível
abrir um shell onde a versão 1 está ativada ocultando a versão 3, mas não
desistalando.

O que não gostei da distro: a complexidade para instanciar shells. Temos que
aprender uma linguagem nova chamada Nix. É o preço que se paga por utilizar
algo benéfico.

Penso em comprar um pendrive para instalar a versão sem blobs propritários
chamada Guix. Acho immportante para a liberdade permanecer num ambiente
sem a intervenção e interesses de empresas que não cuidam dos assets, como
o próprio dado da pessoa.


