---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/24/2019
ms.openlocfilehash: 4cdcec850f32d7e94f33eb28e5bf7839e511f347
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66124589"
---
Wypełnij i Prześlij [formularz żądania kontenerów przetwarzania w usłudze Cognitive Services](https://aka.ms/VisionContainersPreview) Aby zażądać dostępu do kontenera. Formularz żądania informacji o Tobie, firmy i Scenariusz użytkownika, dla którego będzie używać kontenera. Po przesłaniu formularza, zespół usług Azure Cognitive Services sprawdza ją, aby upewnić się, że spełniają kryteria, aby uzyskać dostęp do prywatnego rejestru kontenerów.

> [!IMPORTANT]
> Należy użyć adresu e-mail skojarzonego z kontem Microsoft (MSA) lub konta usługi Azure Active Directory (Azure AD) w postaci.

Jeśli Twoje żądanie zostanie zatwierdzone, otrzymasz wiadomość e-mail z instrukcjami, które opisują, jak uzyskać poświadczenia i dostęp do prywatnego rejestru kontenerów.

## <a name="log-in-to-the-private-container-registry"></a>Zaloguj się do prywatnego rejestru kontenerów

Istnieje kilka sposobów uwierzytelniania przy użyciu prywatnego rejestru kontenera dla kontenerów usług Cognitive Services. Zalecane jest użycie metody wiersza polecenia przy użyciu [interfejsu wiersza polecenia Docker](https://docs.docker.com/engine/reference/commandline/cli/).

Użyj [docker login](https://docs.docker.com/engine/reference/commandline/login/) polecenia, jak pokazano w poniższym przykładzie, zaloguj się do `containerpreview.azurecr.io`, który jest prywatnym rejestrze kontenerów dla kontenerów usług Cognitive Services. Zastąp *\<username\>* z nazwą użytkownika i *\<hasło\>* przy użyciu hasła podanego poświadczenia otrzymane od platformy Azure Zespół usługi cognitive Services.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Jeśli poświadczenia w pliku tekstowym jest zabezpieczona, można łączyć ze sobą zawartość tego pliku tekstowego `docker login` polecenia. Użyj `cat` polecenia, jak pokazano w poniższym przykładzie. Zastąp *\<wartości passwordFile\>* ścieżka i nazwa pliku tekstowego, który zawiera hasło. Zastąp *\<username\>* z nazwą użytkownika, podać swoje poświadczenia.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

