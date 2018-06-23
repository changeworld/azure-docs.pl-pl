---
title: Eksport i usuwanie danych klienta - LUIS — kognitywnych usług Azure || Dokumentacja firmy Microsoft
description: Odwołanie do eksportowania i usunięcie danych klienta z języka opis usługi (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 05/23/2018
ms.author: v-geberr;
ms.openlocfilehash: f684b8ab875e2fbb774dc4a29bce25be41b24e6d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349581"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Wyeksportowanie i usunięcie danych klienta w języku opis (LUIS) w usługach kognitywnych

## <a name="summary-of-customer-data-request-features"></a>Podsumowanie funkcji żądania danych klienta
Usługa inteligentnego opis języka (LUIS) zachowuje zawartość klienta działanie usługi, ale LUIS użytkownik ma pełną kontrolę nad wyświetlanie, eksportowanie i usuwanie ich danych. Można to zrobić za pośrednictwem sieci web LUIS [portal](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-reference-regions) lub [LUIS programowe interfejsów API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Zawartość klienta jest przechowywany jako zaszyfrowany w magazynie Azure regionalnych firmy Microsoft i obejmuje:

- Zawartość konta użytkownika zebranych podczas rejestracji
- Dane szkoleniowe wymagane do tworzenia modeli (tj. zamiar & jednostki)
- Zapytania użytkownika rejestrowane w czasie wykonywania, aby pomóc w udoskonalaniu modeli użytkownika
  - Użytkownicy mogą wyłączyć rejestrowanie zapytań przez dołączenie `&log=false` na żądanie szczegółów [tutaj](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-resources-faq#how-can-i-disable-the-logging-of-utterances)

## <a name="deleting-customer-data"></a>Usuwanie danych klienta
LUIS użytkownicy mają pełną kontrolę, można usunąć żadnej zawartości użytkownika przy użyciu portalu sieci web LUIS lub interfejsów API programowe LUIS. W poniższej tabeli przedstawiono łącza z obu:

| | **Konto użytkownika** | **Aplikacji** | **Utterance(s)** | **Zapytania użytkownika końcowego** |
| --- | --- | --- | --- | --- |
| **Portal** | [Link](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-how-to-account-settings) | [Link](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/create-new-app#delete-app) | [Link](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/create-new-app#delete-app) | [Link](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/create-new-app#delete-app) |
| **Interfejsy API** | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>Eksportowanie danych klienta
LUIS użytkownicy mają pełną kontrolę do wyświetlania danych w portalu, jednak musi być eksportowany za pośrednictwem interfejsów API programowe LUIS. W poniższej tabeli przedstawiono łącza z eksportuje dane za pośrednictwem interfejsów API programowe LUIS:

| | **Konto użytkownika** | **Aplikacji** | **Utterance(s)** | **Zapytania użytkownika końcowego** |
| --- | --- | --- | --- | --- |
| **Interfejsy API** | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |


## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Odwołanie do LUIS regionów](./luis-reference-regions.md)
