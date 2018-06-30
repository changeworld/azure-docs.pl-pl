---
title: Użyj Cortana wbudowane aplikacji z LUIS | Dokumentacja firmy Microsoft
description: Użyj Pomocnik Cortana, wbudowane aplikacji z języka opis inteligentnego usług (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: c7249cb8d8cff29f419412025c69e3b2b76b49d1
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110661"
---
# <a name="cortana-prebuilt-app"></a>Wbudowane aplikacji Cortana

> [!IMPORTANT]
> Firma Microsoft zaleca użycie [wbudowane domen](./luis-how-to-use-prebuilt-domains.md), zamiast aplikacji wbudowane Cortana. Na przykład zamiast z **builtin.intent.calendar.create_calendar_entry**, użyj **Calendar.Add** z **kalendarza** wbudowane domeny.
> Wbudowane domeny zapewniają następujące korzyści: 
> * Udostępniają one pakietów wbudowane i pretrained intencje i jednostek, które są zaprojektowane do pracy ze sobą. Można je zintegrować domeny wbudowane bezpośrednio w aplikacji. Na przykład, jeśli tworzysz tracker przydatności, można dodać **przydatności** domeny i mieć cały zbiór intencje i jednostki do śledzenia działań przydatności, w tym opcji śledzenia wagi i planowania zawierają najważniejsze nowości, pozostały czas lub odległość i zapisywanie przydatności działania uwagi.
> * Intencje wbudowane domeny są dostosowywane. Na przykład, jeśli chcesz podać przeglądami hoteli, można nauczenia i dostosować **Places.GetReviews** konwersji z **miejsca** domeny do rozpoznawania żądań dotyczących przeglądami hoteli.
> * Wbudowane domeny są rozszerzalne. Na przykład, jeśli chcesz użyć **miejsca** wbudowane domeny w bot, która wyszukuje restauracji i potrzebę celem pobierania typu cuisine, możesz skompilować i uczenia **Places.GetCuisine** celem.

Oprócz umożliwienia tworzenia własnych aplikacji, LUIS także intencje i jednostki z osobistego asystenta Microsoft Cortana jako wbudowane aplikację. Nie można zmienić zachowanie LUIS publicznie dostępnych aplikacji. Profile i jednostki w tej aplikacji nie można edytować ani zintegrowana z innymi aplikacjami LUIS. Jeśli chcesz mają dostęp do tej aplikacji wbudowane oraz aplikacji LUIS przez aplikację klienta, aplikacja kliencka ma odwołania obie aplikacje LUIS.

Wbudowane Pomocnik aplikacja jest dostępna w tych kultury (ustawienia regionalne): angielskim, francuskim, włoskim, hiszpańskim i chiński.

## <a name="get-the-endpoint-for-the-cortana-prebuilt-app"></a>Uzyskać punktu końcowego dla aplikacji wbudowane Cortana

Aby dostęp do aplikacji wbudowane Cortana przy użyciu następujących punktów końcowych: 

| Język | Endpoint|
|--------| ------------------|
| Polski| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/c413b2ef-382c-45bd-8ff0-f76d60e2a821|
|    Chiński| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/c27c4af7-d44a-436f-a081-841bb834fa29|
|    Francuski| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/0355ead1-2d08-4955-ab95-e263766e8392|
|    Hiszpański| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/cb2675e5-fbea-4f8b-8951-f071e9fc7b38|
|    Włoski| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/30a0fddc-36f4-4488-b022-03de084c1633|


> [!NOTE]
> Punkt końcowy adresy URL są także dostępne w [aplikacje — Pobierz Pomocnik aplikacje](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c32) interfejsu API.

## <a name="try-out-the-personal-assistant-app"></a>Wypróbowanie aplikacji Pomocnik
Aby wywołać punkt końcowy, można dołączyć z punktu końcowego argument i zapytania ciąg klucza do punktu końcowego. 

Na przykład jeśli utterance chcesz zinterpretować jest "Utwórz termin spotkania zespołu", tym utterance można dołączyć do adresu URL punktu końcowego. 

```
https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/c413b2ef-382c-45bd-8ff0-f76d60e2a821?subscription-key={YOUR-SUBSCRIPTION-KEY}&q=create an appointment for team meeting
```

Wklej adres URL w przeglądarce sieci web, a klucz punktu końcowego w celu zastąpienia `{YOUR-SUBSCRIPTION-KEY}` pola.

W przeglądarce widać, że aplikacja wbudowane Cortana identyfikuje `builtin.intent.calendar.create_calendar_entry` jako zamierzone i `builtin.calendar.title` jako typu jednostki i utterance `create an appointment for team meeting`.

![Użycie aplikacji wbudowane Cortana](./media/luis-how-to-prebuilt-cortana/luis-prebuilt-cortana-browser.png)

## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
> [Odwołanie Cortana wbudowane aplikacji](./luis-reference-cortana-prebuilt.md)

