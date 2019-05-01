---
title: Eksportowanie i usuwanie danych
titleSuffix: Azure Cognitive Services
description: Usuń dane klienta w celu zapewnienia ochrony prywatności i zgodności.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: diberry
ms.openlocfilehash: a82452f4b41aee9c4ea6f269d92fbc91a5697d16
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64916944"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Eksportowanie i usunąć swoje dane klienta w Language Understanding (LUIS) w usługach Cognitive Services

Usuń dane klienta w celu zapewnienia ochrony prywatności i zgodności. 

## <a name="summary-of-customer-data-request-features"></a>Podsumowanie funkcji żądania danych klienta
Language Understanding Intelligent Service (LUIS) pozwala zachować zawartość klienta w celu oferowania usługi, ale użytkownik usługi LUIS ma pełną kontrolę nad wyświetlania, eksportowania i usuwania ich danych. Można to zrobić za pośrednictwem sieci web usługi LUIS [portal](luis-reference-regions.md) lub [LUIS tworzenia interfejsów API (znany także jako programowy)](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Zawartość klienta jest przechowywane w postaci zaszyfrowanej w usłudze Azure storage regionalnych firmy Microsoft i zawiera:

- Zawartość konta użytkownika zebranych podczas rejestracji
- Dane szkoleniowe, które są wymagane do tworzenia modeli
- Zalogowany użytkownik zapytaniami używanymi przez [aktywne uczenie](luis-concept-review-endpoint-utterances.md) do poprawy modelu
  - Użytkownicy mogą wyłączyć rejestrowanie zapytań, dodając `&log=false` na żądanie, szczegóły [tutaj](troubleshooting.md#how-can-i-disable-the-logging-of-utterances)

## <a name="deleting-customer-data"></a>Usuwanie danych klienta
Usługa LUIS użytkownicy mają pełną kontrolę do usunięcia każdemu użytkownikowi zawartości za pośrednictwem portalu sieci web usługi LUIS lub interfejsów API usługi LUIS Authoring (określana także jako programistyczne). Poniższa tabela zawiera linki z obu:

| | **Konto użytkownika** | **Aplikacja** | **Przykład Utterance(s)** | **Zapytania użytkowników końcowych** |
| --- | --- | --- | --- | --- |
| **Portal** | [Link](luis-concept-data-storage.md#delete-an-account) | [Link](luis-how-to-start-new-app.md#delete-app) | [Link](luis-concept-data-storage.md#utterances-in-an-intent) | [Aktywna nauka wypowiedzi](luis-how-to-review-endpoint-utterances.md#disable-active-learning)<br>[Rejestrowane wypowiedzi](luis-concept-data-storage.md#disable-logging-utterances) |
| **Interfejsy API** | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>Eksportowanie danych klienta
Użytkownicy usługi LUIS mają pełną kontrolę do wyświetlania danych w portalu, jednak musi być eksportowany za pośrednictwem usługi LUIS tworzenia API (znany także jako programowy). Poniższa tabela zawiera linki ułatwiających eksportowanie danych za pośrednictwem (znany także jako programowy) interfejsów API usługi LUIS tworzenia:

| | **Konto użytkownika** | **Aplikacja** | **Utterance(s)** | **Zapytania użytkowników końcowych** |
| --- | --- | --- | --- | --- |
| **Interfejsy API** | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |

## <a name="location-of-active-learning"></a>Lokalizacja aktywne uczenie

Aby włączyć [aktywne uczenie](luis-how-to-review-endpoint-utterances.md#enable-active-learning), wypowiedzi zarejestrowanych użytkowników, odbierane w opublikowanych punktów końcowych usługi LUIS, są przechowywane w następujących lokalizacjach geograficznych platformy Azure:

* [Europa](#europe)
* [Australia](#australia)
* [Stany Zjednoczone](#united-states)

Z wyjątkiem danych aktywne uczenie (szczegóły przedstawiono poniżej), następuje LUIS [rozwiązań magazynu danych dla usług regionalnych](https://azuredatacentermap.azurewebsites.net/). 

### <a name="europe"></a>Europa

[Eu.luis.ai](https://eu.luis.ai) portalu i Europy autorstwa (znany także jako programistycznych interfejsów API) znajdują się w Europie platformy Azure w lokalizacji geograficznej. Eu.luis.ai portal i Europy Authoring (znany także jako programistycznych interfejsów API) obsługują wdrażanie punktów końcowych w następujących lokalizacjach geograficznych platformy Azure:

* Europa
* Francja
* Zjednoczone Królestwo

Podczas wdrażania tych lokalizacjach geograficznych platformy Azure, wypowiedzi odebranych przez punkt końcowy od użytkowników końcowych w aplikacji będą przechowywane w lokalizacji geograficznej z Europy platformy Azure dla aktywne uczenie. Można wyłączyć aktywne uczenie, zobacz [Wyłącz aktywne uczenie](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Aby zarządzać wypowiedzi składowanych, zobacz [Usuń wypowiedź](luis-how-to-review-endpoint-utterances.md#delete-utterance). 

### <a name="australia"></a>Australia

[Au.luis.ai](https://au.luis.ai) portalu i Australii autorstwa (znany także jako programistycznych interfejsów API) znajdują się w Australii platformy Azure w lokalizacji geograficznej. Au.luis.ai portal i Australii Authoring (znany także jako programistycznych interfejsów API) obsługują wdrażanie punktów końcowych w następujących lokalizacjach geograficznych platformy Azure:

* Australia

Podczas wdrażania tych lokalizacjach geograficznych platformy Azure, wypowiedzi odebranych przez punkt końcowy od użytkowników końcowych w aplikacji będą przechowywane w lokalizacji geograficznej z Australii platformy Azure dla aktywne uczenie. Można wyłączyć aktywne uczenie, zobacz [Wyłącz aktywne uczenie](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Aby zarządzać wypowiedzi składowanych, zobacz [Usuń wypowiedź](luis-how-to-review-endpoint-utterances.md#delete-utterance). 

### <a name="united-states"></a>Stany Zjednoczone

[Luis.ai](https://www.luis.ai) portalu i Stanów Zjednoczonych Authoring (znany także jako programistycznych interfejsów API), które są przechowywane w Stanach Zjednoczonych platformy Azure w lokalizacji geograficznej. Luis.ai portal i Stanach Zjednoczonych Authoring (znany także jako programistycznych interfejsów API) obsługują wdrażanie punktów końcowych w następujących lokalizacjach geograficznych platformy Azure:

* Lokalizacje geograficzne platformy Azure, które nie są obsługiwane przez Europa lub Australia tworzenia regionów

Podczas wdrażania tych lokalizacjach geograficznych platformy Azure, wypowiedzi odebranych przez punkt końcowy od użytkowników końcowych w aplikacji będą przechowywane w Geografia Stanów Zjednoczonych platformy Azure dla aktywne uczenie. Można wyłączyć aktywne uczenie, zobacz [Wyłącz aktywne uczenie](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Aby zarządzać wypowiedzi składowanych, zobacz [Usuń wypowiedź](luis-how-to-review-endpoint-utterances.md#delete-utterance). 


## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Odwołanie do regionów usługi LUIS](./luis-reference-regions.md)
