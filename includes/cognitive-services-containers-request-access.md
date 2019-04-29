---
author: diberry
ms.author: v-junlch
ms.service: cognitive-services
ms.topic: include
origin.date: 01/24/2019
ms.date: 02/21/2019
ms.openlocfilehash: 11a336bbcf75c6c4de61f1bb681ab6ee7aa05650
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60815633"
---
Najpierw należy wypełnić oraz przesłać [formularz żądania kontenerów przetwarzania w usłudze Cognitive Services](https://aka.ms/VisionContainersPreview) Aby zażądać dostępu do kontenera. Formularz żądania informacji o Tobie, firmy i Scenariusz użytkownika, dla którego będzie używać kontenera. Po przesłaniu, zespół usług Azure Cognitive Services przegląda formularza, aby upewnić się, że spełniają kryteria, aby uzyskać dostęp do prywatnego rejestru kontenerów.

> [!IMPORTANT]
> Należy użyć adresu e-mail skojarzonego z kontem Microsoft (MSA) lub Azure Active Directory kontem (Azure AD) w formularzu.

Jeśli Twoje żądanie zostanie zatwierdzone, następnie otrzymasz wiadomość e-mail z instrukcjami opisujących sposób uzyskać poświadczenia i dostęp do prywatnego rejestru kontenerów.

## <a name="log-in-to-the-private-container-registry"></a>Zaloguj się do prywatnego rejestru kontenerów

Istnieje kilka sposobów uwierzytelniania za pomocą prywatnego rejestru kontenera dla kontenerów usługi Cognitive Services, ale zalecana metoda w wierszu polecenia jest za pomocą [interfejsu wiersza polecenia Docker](https://docs.docker.com/engine/reference/commandline/cli/).

Użyj [docker login](https://docs.docker.com/engine/reference/commandline/login/) polecenia, jak pokazano w poniższym przykładzie, aby zalogować się do `containerpreview.azurecr.io`, prywatnego rejestru kontenera dla kontenerów usługi Cognitive Services. Zastąp *\<username\>* z nazwą użytkownika i *\<hasło\>* przy użyciu hasła podanego poświadczenia otrzymane od platformy Azure Zespół usługi cognitive Services.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Jeśli objęta swoje poświadczenia w pliku tekstowym, można łączyć ze sobą zawartość ten tekst pliku, przy użyciu `cat` polecenia do `docker login` polecenia, jak pokazano w poniższym przykładzie. Zastąp *\<wartości passwordFile\>* ze ścieżką i nazwą pliku tekstowego zawierającego hasło i *\<username\>* z nazwą użytkownika podać swoje poświadczenia.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```


<!-- ms.date: 02/21/2019 -->