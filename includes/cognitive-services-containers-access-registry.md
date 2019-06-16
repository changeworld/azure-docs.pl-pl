---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2019
ms.openlocfilehash: 02beb6bdce096c35d8948cc8aefab6cc97be907c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063942"
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
