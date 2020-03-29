---
title: Uwierzytelnianie
titleSuffix: Azure Cognitive Services
description: 'Istnieją trzy sposoby uwierzytelniania żądania do zasobu usługi Azure Cognitive Services: klucz subskrypcji, token nośny lub subskrypcja wielu usług. W tym artykule dowiesz się o każdej metodzie i jak złożyć żądanie.'
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 11/22/2019
ms.author: erhopf
ms.openlocfilehash: 1c13c2cc4d4e562d3512de90338d874091dfeef6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74423943"
---
# <a name="authenticate-requests-to-azure-cognitive-services"></a>Uwierzytelnij żądania w usługach Azure Cognitive Services

Każde żądanie do usługi Azure Cognitive Service musi zawierać nagłówek uwierzytelniania. Ten nagłówek przekazuje klucz subskrypcji lub token dostępu, który służy do sprawdzania poprawności subskrypcji dla usługi lub grupy usług. W tym artykule dowiesz się o trzech sposobach uwierzytelniania żądania i wymaganiach dla każdego z nich.

* [Uwierzytelnij się przy użyciu klucza subskrypcji z jedną usługą](#authenticate-with-a-single-service-subscription-key)
* [Uwierzytelnij się przy użyciu klucza subskrypcji z wieloma usługami](#authenticate-with-a-multi-service-subscription-key)
* [Uwierzytelnij się za pomocą tokenu](#authenticate-with-an-authentication-token)
* [Uwierzytelnij się za pomocą usługi Azure Active Directory (AAD)](#authenticate-with-azure-active-directory)

## <a name="prerequisites"></a>Wymagania wstępne

Przed złożeniem żądania potrzebne jest konto platformy Azure i subskrypcja usług Azure Cognitive Services. Jeśli masz już konto, przejdź dalej i przejdź do następnej sekcji. Jeśli nie masz konta, mamy przewodnik, który umożliwia konfigurację w ciągu kilku minut: [Utwórz konto usług Cognitive Services dla platformy Azure.](cognitive-services-apis-create-account.md)

Klucz subskrypcji można uzyskać z [witryny Azure portal](cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) po utworzeniu konta lub aktywowaniu [bezpłatnej wersji próbnej.](https://azure.microsoft.com/try/cognitive-services/my-apis)

## <a name="authentication-headers"></a>Nagłówki uwierzytelniania

Przejrzyjmy szybko nagłówki uwierzytelniania dostępne do użytku z usługami Azure Cognitive Services.

| Nagłówek | Opis |
|--------|-------------|
| Ocp-Apim-Subscription-Key | Ten nagłówek służy do uwierzytelniania przy użyciu klucza subskrypcji dla określonej usługi lub klucza subskrypcji wielu usług. |
| Ocp-Apim-Subscription-Region | Ten nagłówek jest wymagany tylko w przypadku korzystania z klucza subskrypcji z wieloma usługami w [interfejsie API tekstu translatora](./Translator/reference/v3-0-reference.md). Ten nagłówek służy do określania regionu subskrypcji. |
| Autoryzacja | Użyj tego nagłówka, jeśli używasz tokenu uwierzytelniania. Kroki do wykonania wymiany tokenu są szczegółowo opisane w poniższych sekcjach. Podana wartość jest `Bearer <TOKEN>`następująca w następującym formacie: . |

## <a name="authenticate-with-a-single-service-subscription-key"></a>Uwierzytelnij się przy użyciu klucza subskrypcji z jedną usługą

Pierwszą opcją jest uwierzytelnienie żądania za pomocą klucza subskrypcji dla określonej usługi, takiej jak Tekst tłumacza. Klucze są dostępne w witrynie Azure portal dla każdego utworzonego zasobu. Aby użyć klucza subskrypcji do uwierzytelnienia żądania, musi `Ocp-Apim-Subscription-Key` on zostać przekazany jako nagłówek.

Te przykładowe żądania pokazuje, `Ocp-Apim-Subscription-Key` jak używać nagłówka. Pamiętaj, że korzystając z tego przykładu, musisz dołączyć prawidłowy klucz subskrypcji.

Jest to przykładowe wywołanie interfejsu API wyszukiwania w sieci Web Bing:
```cURL
curl -X GET 'https://api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

Jest to przykładowe wywołanie interfejsu API tekstu tłumacza:
```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

Poniższy film pokazuje przy użyciu klucza usług Cognitive Services.

## <a name="authenticate-with-a-multi-service-subscription-key"></a>Uwierzytelnij się przy użyciu klucza subskrypcji z wieloma usługami

>[!WARNING]
> Obecnie te usługi **nie** obsługują kluczy wielodosługowych: QnA Maker, Speech Services, Custom Vision i Anomaly Detector.

Ta opcja używa również klucza subskrypcji do uwierzytelniania żądań. Główną różnicą jest to, że klucz subskrypcji nie jest powiązany z określoną usługą, a pojedynczy klucz może służyć do uwierzytelniania żądań dla wielu usług Cognitive Services. Zobacz [Cennik usług Cognitive Services, aby](https://azure.microsoft.com/pricing/details/cognitive-services/) uzyskać informacje o dostępności regionalnej, obsługiwanych funkcjach i cenach.

Klucz subskrypcji jest podany w `Ocp-Apim-Subscription-Key` każdym żądaniu jako nagłówek.

[![Demonstracja klucza subskrypcji wielu usług dla usług Cognitive Services](./media/index/single-key-demonstration-video.png)](https://www.youtube.com/watch?v=psHtA1p7Cas&feature=youtu.be)

### <a name="supported-regions"></a>Obsługiwane regiony

Podczas korzystania z klucza subskrypcji wielu `api.cognitive.microsoft.com`usług, aby złożyć żądanie do , należy dołączyć region w adresie URL. Na przykład: `westus.api.cognitive.microsoft.com`.

W przypadku korzystania z klucza subskrypcji wielu usług z interfejsem `Ocp-Apim-Subscription-Region` API tekstu translatora należy określić region subskrypcji z nagłówkiem.

Uwierzytelnianie wielu usług jest obsługiwane w następujących regionach:

| | | |
|-|-|-|
| `australiaeast` | `brazilsouth` | `canadacentral` |
| `centralindia` | `eastasia` | `eastus` |
| `japaneast` | `northeurope` | `southcentralus` |
| `southeastasia` | `uksouth` | `westcentralus` |
| `westeurope` | `westus` | `westus2` |


### <a name="sample-requests"></a>Przykładowe żądania

Jest to przykładowe wywołanie interfejsu API wyszukiwania w sieci Web Bing:

```cURL
curl -X GET 'https://YOUR-REGION.api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

Jest to przykładowe wywołanie interfejsu API tekstu tłumacza:

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Ocp-Apim-Subscription-Region: YOUR_SUBSCRIPTION_REGION' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

## <a name="authenticate-with-an-authentication-token"></a>Uwierzytelnianie przy użyciu tokenu uwierzytelniania

Niektóre usługi Azure Cognitive Services akceptują, a w niektórych przypadkach wymagają tokenu uwierzytelniania. Obecnie te usługi obsługują tokeny uwierzytelniania:

* Interfejs API tłumaczenia tekstu
* Usługi mowy: interfejs API REST między mowę a tekstem
* Usługi mowy: interfejs API REST od zamiany tekstu na mowę

>[!NOTE]
> QnA Maker również używa nagłówka Autoryzacja, ale wymaga klucza końcowego. Aby uzyskać więcej informacji, zobacz [QnA Maker: Uzyskaj odpowiedź z bazy wiedzy](./qnamaker/quickstarts/get-answer-from-knowledge-base-using-url-tool.md).

>[!WARNING]
> Usługi, które obsługują tokeny uwierzytelniania mogą ulec zmianie wraz z czasem, sprawdź odwołanie do interfejsu API dla usługi przed użyciem tej metody uwierzytelniania.

Klucze subskrypcji pojedynczej usługi i wielu usług mogą być wymieniane na tokeny uwierzytelniania. Tokeny uwierzytelniania są prawidłowe przez 10 minut.

Tokeny uwierzytelniania są uwzględniane `Authorization` w żądaniu jako nagłówek. Podana wartość tokenu musi `Bearer`być poprzedzona, na przykład: `Bearer YOUR_AUTH_TOKEN`.

### <a name="sample-requests"></a>Przykładowe żądania

Ten adres URL służy do wymiany klucza subskrypcji tokenu uwierzytelniania: `https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken`.

```cURL
curl -v -X POST \
"https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
-H "Content-type: application/x-www-form-urlencoded" \
-H "Content-length: 0" \
-H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

Te regiony wielu usług obsługują wymianę tokenów:

| | | |
|-|-|-|
| `australiaeast` | `brazilsouth` | `canadacentral` |
| `centralindia` | `eastasia` | `eastus` |
| `japaneast` | `northeurope` | `southcentralus` |
| `southeastasia` | `uksouth` | `westcentralus` |
| `westeurope` | `westus` | `westus2` |

Po otrzymasz token uwierzytelniania, musisz przekazać go w `Authorization` każdym żądaniu jako nagłówek. Jest to przykładowe wywołanie interfejsu API tekstu tłumacza:

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Authorization: Bearer YOUR_AUTH_TOKEN' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

[!INCLUDE [](../../includes/cognitive-services-azure-active-directory-authentication.md)]

## <a name="see-also"></a>Zobacz też

* [Co to są usługi poznawcze Cognitive Services?](welcome.md)
* [Cennik usług Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/)
* [Niestandardowe poddomeny](cognitive-services-custom-subdomains.md)
