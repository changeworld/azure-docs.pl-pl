---
title: Eksportowanie & usuwania danych — usługa LUIS
titleSuffix: Azure Cognitive Services
description: Masz pełną kontrolę nad wyświetlaniem, eksportowaniem i usuwaniem ich danych. Usuń dane klientów, aby zapewnić prywatność i zgodność.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/02/2019
ms.author: diberry
ms.openlocfilehash: 4e3e0d04b0086905b80e26fb4f838c36b5b5545e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273364"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Eksportowanie i usuwanie danych klientów w usłudze Language Understanding (LUIS) w usługach Cognitive Services

Usuń dane klientów, aby zapewnić prywatność i zgodność.

## <a name="summary-of-customer-data-request-features"></a>Podsumowanie funkcji żądania danych klientów
Language Understanding Intelligent Service (LUIS) zachowuje zawartość klienta do obsługi usługi, ale użytkownik usługi LUIS ma pełną kontrolę nad wyświetlaniem, eksportowanie i usuwanie danych. Można to zrobić za pośrednictwem [portalu](luis-reference-regions.md) sieciowego usługi LUIS lub [interfejsów API tworzenia usługi LUIS (znanych również jako programowe).](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Zawartość klienta jest przechowywana w sieci danych masowych platformy Microsoft regional Azure i obejmuje:

- Zawartość konta użytkownika zbierana podczas rejestracji
- Dane szkoleniowe wymagane do tworzenia modeli
- Zarejestrowane zapytania użytkowników używane przez [aktywne uczenie się](luis-concept-review-endpoint-utterances.md) w celu ulepszenia modelu
  - Użytkownicy mogą wyłączyć rejestrowanie zapytań, dołączając `&log=false` do żądania, szczegóły [tutaj](troubleshooting.md#how-can-i-disable-the-logging-of-utterances)

## <a name="deleting-customer-data"></a>Usuwanie danych klienta
Użytkownicy usługi LUIS mają pełną kontrolę nad usuwaniem zawartości użytkownika za pośrednictwem portalu sieci web usługi LUIS lub interfejsów API tworzenia usługi LUIS (znanych również jako programowe). W poniższej tabeli przedstawiono łącza pomagające w obu tych elementów:

| | **Konto użytkownika** | **Aplikacja** | **Przykładowe wypowiedzi** | **Zapytania użytkowników końcowych** |
| --- | --- | --- | --- | --- |
| **Portal** | [Link](luis-concept-data-storage.md#delete-an-account) | [Link](luis-how-to-start-new-app.md#delete-app) | [Link](luis-concept-data-storage.md#utterances-in-an-intent) | [Aktywne wypowiedzi edukacyjne](luis-how-to-review-endpoint-utterances.md#disable-active-learning)<br>[Rejestrowane wypowiedzi](luis-concept-data-storage.md#disable-logging-utterances) |
| **Interfejsy API** | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>Eksportowanie danych klientów
Użytkownicy usługi LUIS mają pełną kontrolę, aby wyświetlić dane w portalu, jednak musi być eksportowane za pośrednictwem interfejsu API tworzenia usługi LUIS (znany również jako Programmatic) interfejsów API. W poniższej tabeli są wyświetlane łącza ułatwiające eksport danych za pośrednictwem interfejsów API tworzenia usługi LUIS (znanych również jako programowe):

| | **Konto użytkownika** | **Aplikacja** | **Wypowiedzi** | **Zapytania użytkowników końcowych** |
| --- | --- | --- | --- | --- |
| **Interfejsy API** | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |

## <a name="location-of-active-learning"></a>Lokalizacja aktywnego uczenia się

Aby włączyć [aktywne uczenie](luis-how-to-review-endpoint-utterances.md#enable-active-learning)się , zarejestrowane wypowiedzi użytkowników, odebrane w opublikowanych punktach końcowych usługi LUIS, są przechowywane w następujących regionach geograficznych platformy Azure:

* [Europa](#europe)
* [Australia](#australia)
* [Stany Zjednoczone](#united-states)

Z wyjątkiem aktywnych danych szkoleniowych (wyszczególnionych poniżej), usługa LUIS stosuje [praktyki przechowywania danych dla usług regionalnych.](https://azuredatacentermap.azurewebsites.net/)

### <a name="europe"></a>Europa

Portal [eu.luis.ai](https://eu.luis.ai) i tworzenie w Europie (znane również jako programowe interfejsy API) są hostowane w geografii europejskiej platformy Azure. Portal eu.luis.ai i tworzenie w Europie (znane również jako programowe interfejsy API) obsługują wdrażanie punktów końcowych w następujących regionach geograficznych platformy Azure:

* Europa
* Francja
* Wielka Brytania

Podczas wdrażania w tych regionach geograficznych platformy Azure wypowiedzi odebrane przez punkt końcowy od użytkowników końcowych aplikacji będą przechowywane w geografii europejskiej platformy Azure do aktywnego uczenia się. Możesz wyłączyć aktywne uczenie się, zobacz [Wyłączanie aktywnego uczenia się](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Aby zarządzać przechowywanymi wypowiedziami, zobacz [Usuwanie wypowiedzi](luis-how-to-review-endpoint-utterances.md#delete-utterance).

### <a name="australia"></a>Australia

[Portal au.luis.ai](https://au.luis.ai) i Australia Authoring (znany również jako programowe interfejsy API) są hostowane w lokalizacji geograficznej Australii platformy Azure. Portal au.luis.ai i Australia Authoring (znany również jako programowe interfejsy API) obsługują wdrażanie punktów końcowych do następujących obszarów geograficznych platformy Azure:

* Australia

Podczas wdrażania w tych regionach geograficznych platformy Azure wypowiedzi odebrane przez punkt końcowy od użytkowników końcowych aplikacji będą przechowywane w geografii Australii platformy Azure do aktywnego uczenia się. Możesz wyłączyć aktywne uczenie się, zobacz [Wyłączanie aktywnego uczenia się](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Aby zarządzać przechowywanymi wypowiedziami, zobacz [Usuwanie wypowiedzi](luis-how-to-review-endpoint-utterances.md#delete-utterance).

### <a name="united-states"></a>Stany Zjednoczone

Portal [luis.ai](https://www.luis.ai) i tworzenie w Stanach Zjednoczonych (znane również jako programowe interfejsy API) są hostowane w lokalizacji geograficznej platformy Azure w Stanach Zjednoczonych. Portal luis.ai i tworzenie w Stanach Zjednoczonych (znane również jako programowe interfejsy API) obsługują wdrażanie punktów końcowych w następujących regionach geograficznych platformy Azure:

* Obszary geograficzne platformy Azure nie są obsługiwane przez regiony tworzące Europę lub Australię

Podczas wdrażania w tych regionach geograficznych platformy Azure wypowiedzi odebrane przez punkt końcowy od użytkowników końcowych aplikacji będą przechowywane w lokalizacji geograficznej platformy Azure w Stanach Zjednoczonych do aktywnego uczenia się. Możesz wyłączyć aktywne uczenie się, zobacz [Wyłączanie aktywnego uczenia się](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Aby zarządzać przechowywanymi wypowiedziami, zobacz [Usuwanie wypowiedzi](luis-how-to-review-endpoint-utterances.md#delete-utterance).


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Odwołanie do regionów usługi LUIS](./luis-reference-regions.md)
