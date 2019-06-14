---
title: Authentication
titleSuffix: Cognitive Services - Azure
description: 'Istnieją trzy sposoby, aby uwierzytelnić żądanie z zasobem usługi Azure Cognitive Services: klucz subskrypcji, token elementu nośnego lub wieloma usługami subskrypcji. W tym artykule dowiesz się, o każdej z metod i wykonać żądanie.'
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: erhopf
ms.openlocfilehash: 90bc2bf4c207f3bb2727d76c2e6b4fd5597539b1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60336788"
---
# <a name="authenticate-requests-to-azure-cognitive-services"></a>Uwierzytelnianie żądań usług Azure Cognitive Services

Każde żądanie do usługi Azure Cognitive Service musi zawierać nagłówek uwierzytelnienia. Tego pliku nagłówkowego przekazywane wraz z klucz subskrypcji lub access token, który służy do sprawdzania poprawności subskrypcji dla usługi lub grupy usług. W tym artykule dowiesz się o trzy sposoby, aby uwierzytelnić żądania i wymagań dla poszczególnych.

* [Uwierzytelnianie za pomocą klucza usługi pojedynczej subskrypcji](#authenticate-with-a-single-service-subscription-key)
* [Uwierzytelnianie za pomocą klucz subskrypcji z wieloma usługami](#authenticate-with-a-multi-service-subscription-key)
* [Uwierzytelnianie za pomocą tokenu](#authenticate-with-an-authentication-token)

## <a name="prerequisites"></a>Wymagania wstępne

Zanim dokonasz, żądania, potrzebujesz konta platformy Azure i subskrypcja usługi Azure Cognitive Services. Jeśli masz już konto, przejdź dalej, a następnie przejdź do następnej sekcji. Jeśli nie masz konta, mamy wskazówki ułatwiające konfigurowanie w ciągu kilku minut: [Tworzenie konta usług Cognitive Services dla platformy Azure](cognitive-services-apis-create-account.md).

Możesz uzyskać klucz subskrypcji z [witryny Azure portal](cognitive-services-apis-create-account.md#access-your-resource) po utworzeniu konta lub aktywowania [bezpłatna wersja próbna](https://azure.microsoft.com/try/cognitive-services/my-apis).
 
## <a name="authentication-headers"></a>Nagłówki uwierzytelniania

Omówmy szybko nagłówki uwierzytelniania dostępne w połączeniu z usług Azure Cognitive Services.

| nagłówek | Opis |
|--------|-------------|
| OCP-Apim-Subscription-Key | Użyj tego pliku nagłówkowego do uwierzytelniania za pomocą klucz subskrypcji dla określonej usługi lub klucz subskrypcji wielu usług. |
| Ocp-Apim-Subscription-Region | Tego pliku nagłówkowego jest tylko wymagany w przypadku korzystania z kluczem subskrypcji wielu usług za pomocą [interfejsu API tłumaczenia tekstu](./Translator/reference/v3-0-reference.md). Użyj tego pliku nagłówkowego, aby określić region subskrypcji. |
| Autoryzacja | Użyj tego pliku nagłówkowego, jeśli używasz tokenu uwierzytelniania. Czynności do wykonania wymiany tokenu są szczegółowo opisane w poniższych sekcjach. Podana wartość następujący format: `Bearer <TOKEN>`. |

## <a name="authenticate-with-a-single-service-subscription-key"></a>Uwierzytelnianie za pomocą klucza usługi pojedynczej subskrypcji

Pierwsza opcja jest do uwierzytelniania na żądanie przy użyciu klucz subskrypcji dla określonej usługi, takie jak tekst usługi Translator. Klucze są dostępne w witrynie Azure portal dla każdego zasobu, który został utworzony. Aby użyć klucz subskrypcji do uwierzytelnienia żądania, jego muszą być przekazywane wzdłuż jako `Ocp-Apim-Subscription-Key` nagłówka.

Te żądania na przykład pokazuje sposób użycia `Ocp-Apim-Subscription-Key` nagłówka. Należy pamiętać, korzystając z tego przykładu musisz uwzględnić klucz ważnej subskrypcji.

Oto przykładowe wywołanie interfejsu API wyszukiwania w sieci Web Bing:
```cURL
curl -X GET 'https://api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

Oto przykładowe wywołanie interfejsu API tłumaczenia tekstu:
```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

Poniższy klip wideo pokazuje, za pomocą klucza usługi Cognitive Services.

## <a name="authenticate-with-a-multi-service-subscription-key"></a>Uwierzytelnianie za pomocą klucz subskrypcji z wieloma usługami

>[!WARNING]
> W tej chwili tych usług **nie** obsługi wielu usług kluczy: Usługa QnA Maker usług przetwarzania mowy i Custom Vision.

Ta opcja używa także klucz subskrypcji do uwierzytelniania żądań. Główną różnicą jest, że klucz subskrypcji nie jest związany z określonej usługi, zamiast jednego klucza może służyć do uwierzytelniania żądań dla wielu usług Cognitive Services. Zobacz [cennik usług Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) uzyskać informacji o dostępności regionalnej, obsługiwane funkcje i ceny.

Klucz subskrypcji znajduje się w każdym żądaniu jako `Ocp-Apim-Subscription-Key` nagłówka.

[![Pokaz kluczy subskrypcji wielu usług dla usług Cognitive Services](./media/index/single-key-demonstration-video.png)](https://www.youtube.com/watch?v=psHtA1p7Cas&feature=youtu.be)

### <a name="supported-regions"></a>Obsługiwane regiony

Gdy za pomocą klucza subskrypcji z wieloma usługami, aby zgłosić wniosek o `api.cognitive.microsoft.com`, musi zawierać region w adresie URL. Na przykład: `westus.api.cognitive.microsoft.com`.

Używając klucz subskrypcji wielu usług za pomocą interfejsu API tłumaczenia tekstu, należy określić region subskrypcji o `Ocp-Apim-Subscription-Region` nagłówka.

Uwierzytelnianie wielu usług jest obsługiwane w tych regionach:

| | | |
|-|-|-|
| `australiaeast` | `brazilsouth` | `canadacentral` |
| `centralindia` | `eastasia` | `eastus` |
| `japaneast` | `northeurope` | `southcentralus` |
| `southeastasia` | `uksouth` | `westcentralus` |
| `westeurope` | `westus` | `westus2` |


### <a name="sample-requests"></a>Prośby o przykłady

Oto przykładowe wywołanie interfejsu API wyszukiwania w sieci Web Bing:

```cURL
curl -X GET 'https://YOUR-REGION.api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

Oto przykładowe wywołanie interfejsu API tłumaczenia tekstu:

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Ocp-Apim-Subscription-Region: YOUR_SUBSCRIPTION_REGION' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

## <a name="authenticate-with-an-authentication-token"></a>Uwierzytelnianie za pomocą tokenu uwierzytelniania

Niektóre usługi Azure Cognitive Services Zaakceptuj i w niektórych przypadkach wymagają, token uwierzytelniania. Obecnie te usługi obsługują tokeny uwierzytelniania:

* Interfejs API tłumaczenia tekstu
* Speech Services: Interfejs API REST mowy na tekst
* Speech Services: Interfejs API REST zamiany tekstu na mowę

>[!NOTE]
> Usługa QnA Maker również korzysta z nagłówka autoryzacji, ale wymaga klucza punktu końcowego. Aby uzyskać więcej informacji, zobacz [QnA Maker: Uzyskiwanie odpowiedzi z bazy wiedzy knowledge base](./qnamaker/quickstarts/get-answer-from-kb-using-curl.md).

>[!WARNING]
> Usług, które obsługują tokeny uwierzytelniania mogą ulec zmianie wraz z upływem czasu, dokumentacja interfejsu API sprawdzania dla usługi przed rozpoczęciem korzystania z tej metody uwierzytelniania.

Pojedynczy zarówno usługi i klucze subskrypcji z wieloma usługami, może zostać wymienione na tokeny uwierzytelniania. Tokeny uwierzytelniania są ważne przez 10 minut.

Tokeny uwierzytelniania są uwzględnione w żądaniu jako `Authorization` nagłówka. Podana wartość tokenu musi być poprzedzona `Bearer`, na przykład: `Bearer YOUR_AUTH_TOKEN`.

### <a name="sample-requests"></a>Prośby o przykłady

Użyj tego adresu URL, aby wymienić klucz subskrypcji dla tokenu uwierzytelniania: `https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken`.

```cURL
curl -v -X POST \
"https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
-H "Content-type: application/x-www-form-urlencoded" \
-H "Content-length: 0" \
-H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

Te regiony wielu usług obsługi wymiany tokenu:

| | | |
|-|-|-|
| `australiaeast` | `brazilsouth` | `canadacentral` |
| `centralindia` | `eastasia` | `eastus` |
| `japaneast` | `northeurope` | `southcentralus` |
| `southeastasia` | `uksouth` | `westcentralus` |
| `westeurope` | `westus` | `westus2` |

Po uzyskaniu tokenu uwierzytelniania, musisz przekazać go do wszystkich żądań jako `Authorization` nagłówka. Oto przykładowe wywołanie interfejsu API tłumaczenia tekstu:

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Authorization: Bearer YOUR_AUTH_TOKEN' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

## <a name="see-also"></a>Zobacz także

* [Co to są usługi poznawcze Cognitive Services?](welcome.md)
* [Cennik usług Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/)
* [Create an account](cognitive-services-apis-create-account.md) (Tworzenie konta)
