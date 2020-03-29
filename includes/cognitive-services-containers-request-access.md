---
author: IEvangelist
ms.author: dapine
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.openlocfilehash: 993b0e8cc5b1ec482b2f6041dfc970dc7e7409dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68229326"
---
Wypełnij i prześlij [formularz Żądania kontenerów usługi Cognitive Services Vision,](https://aka.ms/VisionContainersPreview) aby zażądać dostępu do kontenera. Formularz żąda informacji o Tobie, firmie i scenariuszu użytkownika, dla którego będziesz używać kontenera. Po przesłaniu formularza zespół usług Azure Cognitive Services przegląda go, aby upewnić się, że spełniasz kryteria dostępu do rejestru kontenerów prywatnych.

> [!IMPORTANT]
> W formularzu należy użyć adresu e-mail skojarzonego z kontem Microsoft Account (MSA) lub kontem usługi Azure Active Directory (Azure AD).

Jeśli żądanie zostanie zatwierdzone, otrzymasz wiadomość e-mail z instrukcjami opisującymi sposób uzyskiwania poświadczeń i uzyskiwania dostępu do rejestru kontenerów prywatnych.

## <a name="log-in-to-the-private-container-registry"></a>Zaloguj się do rejestru kontenerów prywatnych

Istnieje kilka sposobów uwierzytelniania za pomocą rejestru kontenerów prywatnych dla kontenerów usług Cognitive Services. Zaleca się użycie metody wiersza polecenia przy użyciu interfejsu [wiersza polecenia platformy Docker](https://docs.docker.com/engine/reference/commandline/cli/).

Użyj polecenia [logowania docker,](https://docs.docker.com/engine/reference/commandline/login/) jak pokazano w poniższym `containerpreview.azurecr.io`przykładzie, aby zalogować się do , który jest rejestr kontenerów prywatnych dla kontenerów usług Cognitive Services. Zastąp * \<nazwę użytkownika\> * nazwą użytkownika i * \<hasłem\> * hasłem podanym w poświadczeniach otrzymanych od zespołu usług Azure Cognitive Services.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Jeśli poświadczenia zostały zabezpieczone w pliku tekstowym, można połączyć zawartość `docker login` tego pliku tekstowego z poleceniem. Użyj `cat` polecenia, jak pokazano w poniższym przykładzie. Zamień * \<\> passwordFile* na ścieżkę i nazwę pliku tekstowego zawierającego hasło. Zastąp * \<nazwę użytkownika\> * nazwą użytkownika podana w poświadczeniach.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

