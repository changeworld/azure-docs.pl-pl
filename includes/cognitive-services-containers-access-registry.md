---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b9d0d2b97472eb3264f5e4600fddbfc7d3250918
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/28/2019
ms.locfileid: "67704329"
---
## <a name="use-the-docker-cli-to-authenticate-the-private-container-registry"></a>Uwierzytelnianie prywatnych rejestrów kontenerów za pomocą interfejsu wiersza polecenia platformy Docker

Można uwierzytelniać się za pomocą prywatnego rejestru kontenerów dla kontenerów Cognitive Services na kilka różnych sposobów, ale zalecaną metodą z wiersza polecenia jest użycie [interfejsu CLI platformy Docker](https://docs.docker.com/engine/reference/commandline/cli/).

Użyj [polecenia`docker login`](https://docs.docker.com/engine/reference/commandline/login/), jak pokazano w poniższym przykładzie, aby zalogować się do `containerpreview.azurecr.io`, prywatnego rejestru kontenerów dla kontenerów Cognitive Services. Zastąp\<nazwy użytkownika *\>* nazwą i *hasłem\<\>* z hasłem podanym w poświadczeniach otrzymanymi od zespołu Cognitive Services platformy Azure.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Jeśli poświadczenia zostały zabezpieczone w pliku tekstowym, można połączyć zawartość tego pliku tekstowego za pomocą polecenia `cat` do polecenia `docker login`, jak pokazano w poniższym przykładzie. Zastąp *\<passwordFile\>* ścieżką i nazwą pliku tekstowego, który zawiera hasło, i *\>\<* nazwę użytkownika, a następnie podaną w poświadczeniach.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```
