---
title: Użyj wstępnie utworzona aplikacja Cortana Luis | Dokumentacja firmy Microsoft
description: Użyj Cortana osobistej asystentki, wbudowanych aplikacji z Language Understanding Intelligent Services (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 07/03/2018
ms.author: v-geberr
ms.openlocfilehash: 27900068d4420b3e70dacc6d1bdfdf0c52053ceb
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37767310"
---
# <a name="cortana-prebuilt-app"></a>Wstępnie utworzona aplikacja Cortana

> [!IMPORTANT]
> Firma Microsoft zaleca użycie [ze wstępnie utworzonych domen](./luis-how-to-use-prebuilt-domains.md), zamiast wstępnie utworzona aplikacja Cortana. Na przykład, zamiast z **builtin.intent.calendar.create_calendar_entry**, użyj **Calendar.Add** z **kalendarza** ze wstępnie utworzonych domen.
> Ze wstępnie utworzonych domen zapewniają następujące korzyści: 
> * Zapewniają one, pakietami wbudowanych i wstępnie przetrenowane intencje i podmioty, które są przeznaczone do pracy ze sobą. Możesz zintegrować ze wstępnie utworzonych domen bezpośrednio w aplikacji. Na przykład, jeśli tworzysz tracker przydatności, można dodać **przydatności** domeny i masz cały zestaw intencje i podmioty, do śledzenia działań przydatności, w tym intencji do śledzenia w zakresie wagi i planowania posiłku, pozostały czas lub odległości i zapisywanie przydatności działania uwagi.
> * Intencji ze wstępnie utworzonych domen są możliwe do dostosowania. Na przykład, jeśli chcesz udostępnić przeglądy hotele, możesz szkolenie i dostosować **Places.GetReviews** intencji z **miejsc** domeny do rozpoznawania żądań dotyczących przeglądy hotelu.
> * Ze wstępnie utworzonych domen są rozszerzalne. Na przykład, jeśli chcesz użyć **miejsc** ze wstępnie utworzonych domen w botów, wyszukująca restauracji i potrzebę intencji pobierania typu cuisine, możesz tworzyć i szkolenie **Places.GetCuisine** intencji.

Oprócz umożliwienia tworzenia własnych aplikacji, usługi LUIS także intencje i podmioty z osobistej asystentki Microsoft Cortana jako ze wstępnie utworzonej aplikacji. Nie można zmienić zachowanie tej publicznie dostępnych aplikacji usługi LUIS. Intencje i podmioty, w tej aplikacji nie można edytować ani zintegrowana z innymi aplikacjami usługi LUIS. Jeśli chcesz aplikacji klienckiej na dostęp do tej wstępnie utworzonych aplikacji i aplikacji usługi LUIS aplikacja kliencka musi odwoływać się do aplikacji zarówno usługi LUIS.

Aplikacja wbudowanych osobistej asystentki jest dostępna w tych kultur (ustawień regionalnych): angielskim, francuskim, włoskim, hiszpańskim i chińskim.

## <a name="get-the-endpoint-for-the-cortana-prebuilt-app"></a>Pobierz punkt końcowy dla wstępnie utworzona aplikacja Cortana

Aby uzyskać dostęp wstępnie utworzona aplikacja Cortana przy użyciu następujących punktów końcowych: 

| Język | Endpoint|
|--------| ------------------|
| Polski| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/c413b2ef-382c-45bd-8ff0-f76d60e2a821|
|    Chiński| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/c27c4af7-d44a-436f-a081-841bb834fa29|
|    Francuski| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/0355ead1-2d08-4955-ab95-e263766e8392|
|    Hiszpański| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/cb2675e5-fbea-4f8b-8951-f071e9fc7b38|
|    Włoski| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/30a0fddc-36f4-4488-b022-03de084c1633|


Punkt końcowy, adresy URL są także dostępne z [aplikacje — pobieranie osobistej asystentki aplikacji](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c32) interfejsu API.

## <a name="try-out-the-personal-assistant-app"></a>Wypróbuj aplikację osobistego asystenta
Na przykład chcąc wypowiedź interpretacji jest "Utwórz termin na potrzeby spotkanie zespołu", tym wypowiedź można dołączyć do adresu URL punktu końcowego. 

```
https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/c413b2ef-382c-45bd-8ff0-f76d60e2a821?subscription-key=YOUR-SUBSCRIPTION-KEY&q=create%20an%20appointment%20for%20team%20meeting
```

Wklej adres URL w przeglądarce sieci web i Zastąp klucz punktu końcowego usługi `YOUR-SUBSCRIPTION-KEY` pola.

W przeglądarce widać, że wstępnie utworzona aplikacja Cortana identyfikuje `builtin.intent.calendar.create_calendar_entry` jako intencji i `builtin.calendar.title` jako typu jednostki i wypowiedź `create an appointment for team meeting`.

```JSON
{
  "query": "create an appointment for team meeting",
  "topScoringIntent": {
    "intent": "builtin.intent.calendar.create_calendar_entry"
  },
  "entities": [
    {
      "entity": "team meeting",
      "type": "builtin.calendar.title"
    }
  ]
}
```

## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
> [Wstępnie utworzona aplikacja Cortana odwołania](./luis-reference-cortana-prebuilt.md)

