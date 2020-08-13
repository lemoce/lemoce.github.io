---
title: 'Haskell - Redescobrindo uma linguagem fantástica'
description: 'Por que eu parei de estudar Haskell?'
tags: computacao, haskell, functional
---

Bom, estou extasiado pelo NixOS. Aprendendo a usar a linguagem nix e o seu modelo de pacotes.
Aos poucos, estou mais fluente do mecanismo.

Ultimamente, tenho estudado muito sobre devops e percebi que necessitava de uma linguagem mais
robusta com um grande poder de expressividade. Desse modo, eu redescobri o Haskell. Eu decidi
ler o livro [Python for Devops](https://www.oreilly.com/library/view/python-for-devops/9781492057680/),
pois o assunto Devops está assumindo um papel recente na minha vida profissional.

Isso me trouxe um projeto que estava engavetado na minha escrivaninha: um pequeno robo de IA
para o Reddit. Basicamente, eu gostaria de usar um robo para verificar imagens no Reddit, com
o propósito para verificar a mensagem de usuários. Isso é uma brincadeira para colocar na
cabeça das pessoas a importância de checar fatos e não acreditar em tudo.

Desse modo, segue um pequeno snippet para consumir serviços do reddit:

```haskell
{-# LANGUAGE OverloadedStrings #-}
import qualified Data.ByteString.Lazy.Char8 as L8
import           Network.HTTP.Client
import           Network.HTTP.Client.TLS    (getGlobalManager)

main = do
    manager <- getGlobalManager
    initReq <- fmap (urlEncodedBody [ ("grant_type", "password")
                                    , ("username", "user")
                                    , ("password", "passpass")
                                    ]
                    )
               $ fmap (applyBasicAuth "appid" "tokenid")
               $ parseRequest "https://www.reddit.com/api/v1/access_token"

    putStrLn $ show initReq
    myResp <- httpLbs initReq manager
    putStrLn $ show $ myResp
```

Esse snippet permite que loguemos ao reddit e seja retornado um token de autenticação. De
posse do token, podemos acessar algumas apis para que o robo possa interagir com o usuários.

Realmente, porque eu parei de estudar uma linguagem fantástica e muito expressiva.
