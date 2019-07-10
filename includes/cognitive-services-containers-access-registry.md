---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b9d0d2b97472eb3264f5e4600fddbfc7d3250918
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704329"
---
## <a name="use-the-docker-cli-to-authenticate-the-private-container-registry"></a>Użyj interfejsu wiersza polecenia platformy Docker do prywatnego rejestru kontenerów uwierzytelniania

Można wybrać metodę uwierzytelniania przy użyciu prywatnego rejestru kontenera dla Cognitive kontenerów usług w jeden z kilku sposobów, ale zalecana metoda w wierszu polecenia jest użycie [interfejsu wiersza polecenia Docker](https://docs.docker.com/engine/reference/commandline/cli/).

Użyj [ `docker login` polecenia](https://docs.docker.com/engine/reference/commandline/login/), jak pokazano w poniższym przykładzie, aby zalogować się do `containerpreview.azurecr.io`, prywatnego rejestru kontenera dla kontenerów usługi Cognitive Services. Zastąp *\<username\>* z nazwą użytkownika i *\<hasło\>* przy użyciu hasła, które podano poświadczenia otrzymane od Zespół platformy Azure Cognitive Services.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Jeśli została zabezpieczona swoje poświadczenia w pliku tekstowym, można połączyć zawartość tego pliku tekstowego przy użyciu `cat` polecenia do `docker login` polecenia, jak pokazano w poniższym przykładzie. Zastąp *\<wartości passwordFile\>* ścieżka i nazwa pliku tekstowego, który zawiera hasło i *\<username\>* z nazwą użytkownika dostarczanym swoje poświadczenia.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```
