---
title: Eksportowanie i usunięcia danych klienta — rozpoznawania języka — usługa LUIS
titleSuffix: Azure Cognitive Services
description: Language Understanding Intelligent Service (LUIS) pozwala zachować zawartość klienta w celu oferowania usługi, ale użytkownik usługi LUIS ma pełną kontrolę nad wyświetlania, eksportowania i usuwania ich danych. Można to zrobić za pośrednictwem portalu sieci web usługi LUIS lub programistycznych interfejsów API usługi LUIS.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.technology: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 5b4b7a746a6a5f7e849777dedca9193455e1cc6c
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2018
ms.locfileid: "45630821"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Eksportowanie i usunąć swoje dane klienta w Language Understanding (LUIS) w usługach Cognitive Services

## <a name="summary-of-customer-data-request-features"></a>Podsumowanie funkcji żądania danych klienta
Language Understanding Intelligent Service (LUIS) pozwala zachować zawartość klienta w celu oferowania usługi, ale użytkownik usługi LUIS ma pełną kontrolę nad wyświetlania, eksportowania i usuwania ich danych. Można to zrobić za pośrednictwem sieci web usługi LUIS [portal](luis-reference-regions.md) lub [programistycznych interfejsów API usługi LUIS](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Zawartość klienta jest przechowywane w postaci zaszyfrowanej w usłudze Azure storage regionalnych firmy Microsoft i zawiera:

- Zawartość konta użytkownika zebranych podczas rejestracji
- Dane szkoleniowe, które są wymagane do tworzenia modeli (np. celem & jednostki)
- Zapytania użytkowników rejestrowane w czasie wykonywania, aby pomóc w ulepszaniu modeli użytkownika
  - Użytkownicy mogą wyłączyć rejestrowanie zapytań, dodając `&log=false` na żądanie, szczegóły [tutaj](luis-resources-faq.md#how-can-i-disable-the-logging-of-utterances)

## <a name="deleting-customer-data"></a>Usuwanie danych klienta
Usługa LUIS użytkownicy mają pełną kontrolę nad usuwania zawartości użytkownika, za pośrednictwem portalu sieci web usługi LUIS lub programistycznych interfejsów API usługi LUIS. Poniższa tabela zawiera linki z obu:

| | **Konto użytkownika** | **Aplikacja** | **Utterance(s)** | **Zapytania użytkowników końcowych** |
| --- | --- | --- | --- | --- |
| **Portal** | [Link](luis-how-to-account-settings.md) | [Link](luis-how-to-start-new-app.md#delete-app) | [Link](luis-how-to-start-new-app.md#delete-app) | [Link](luis-how-to-start-new-app.md#delete-app) |
| **Interfejsy API** | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>Eksportowanie danych klienta
Użytkownicy usługi LUIS mają pełną kontrolę do wyświetlania danych w portalu, jednak musi być eksportowany za pomocą programistycznych interfejsów API usługi LUIS. Poniższa tabela zawiera linki ułatwiających eksportowanie danych za pomocą programistycznych interfejsów API usługi LUIS:

| | **Konto użytkownika** | **Aplikacja** | **Utterance(s)** | **Zapytania użytkowników końcowych** |
| --- | --- | --- | --- | --- |
| **Interfejsy API** | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |


## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Odwołanie do regionów usługi LUIS](./luis-reference-regions.md)
