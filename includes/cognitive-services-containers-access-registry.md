---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b9d0d2b97472eb3264f5e4600fddbfc7d3250918
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67704329"
---
## <a name="use-the-docker-cli-to-authenticate-the-private-container-registry"></a>Uwierzytelnianie prywatnego rejestru kontenerów za pomocą interfejsu wiersza polecenia platformy Docker

Można uwierzytelnić za pomocą prywatnego rejestru kontenerów dla kontenerów usług Cognitive Services na dowolny z kilku sposobów, ale zalecaną metodą z wiersza polecenia jest użycie interfejsu [wiersza polecenia platformy Docker.](https://docs.docker.com/engine/reference/commandline/cli/)

Użyj [ `docker login` polecenia](https://docs.docker.com/engine/reference/commandline/login/), jak pokazano w poniższym `containerpreview.azurecr.io`przykładzie, aby zalogować się do rejestru kontenerów prywatnych dla kontenerów usług Cognitive Services. Zastąp * \<nazwę użytkownika\> * nazwą użytkownika i * \<hasłem\> * hasłem podanym w poświadczeniach otrzymanych od zespołu usług Azure Cognitive Services.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Jeśli poświadczenia zostały zabezpieczone w pliku tekstowym, można połączyć zawartość tego pliku `cat` tekstowego `docker login` za pomocą polecenia, do polecenia, jak pokazano w poniższym przykładzie. Zamień * \<\> passwordFile* na ścieżkę i nazwę pliku tekstowego zawierającego hasło i * \<nazwę użytkownika\> * nazwą użytkownika podana w poświadczeniach.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```
