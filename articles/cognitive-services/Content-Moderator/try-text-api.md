---
title: Moderowanie tekstu za pomocą interfejsu API moderowania tekstu — Moderator treści
titleSuffix: Azure Cognitive Services
description: Moderowanie tekstu na dysku testowym przy użyciu interfejsu API moderowania tekstu w konsoli online.
services: cognitive-services
author: PatrickFarley
ms.author: pafarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: ad365c2d4c171105d8dec89d818ef481361d1ff8
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272597"
---
# <a name="moderate-text-from-the-api-console"></a>Moderowanie tekstu z konsoli interfejsu API

Użyj [interfejsu API moderowania tekstu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) w usłudze Azure Content Moderator, aby skanować zawartość tekstową w poszukiwaniu wulgaryzmów i porównywać ją z listami niestandardowymi i udostępnionymi.

## <a name="get-your-api-key"></a>Pobierz klucz interfejsu API

Aby można było przetestować interfejs API w konsoli online, potrzebny jest klucz subskrypcji. Znajduje się on na karcie **Ustawienia** w polu **Ocp-Apim-Subscription-Key.** Aby uzyskać więcej informacji, zobacz [Omówienie](overview.md).

## <a name="navigate-to-the-api-reference"></a>Przejdź do odwołania do interfejsu API

Przejdź do [odwołania interfejsu API moderowania tekstu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f). 

  Zostanie otwarta strona **Tekst — ekran.**

## <a name="open-the-api-console"></a>Otwieranie konsoli interfejsu API

W przypadku **konsoli testowania otwartego interfejsu API**wybierz region, który najbardziej opisuje Twoją lokalizację. 

  ![Tekst — wybór regionu strony ekranowej](images/test-drive-region.png)

  Zostanie otwarta konsola interfejsu API **Text — Screen.**

## <a name="select-the-inputs"></a>Wybierz dane wejściowe

### <a name="parameters"></a>Parametry

Zaznacz parametry kwerendy, których chcesz użyć na ekranie tekstowym. W tym przykładzie należy użyć wartości domyślnej dla **języka**. Można również pozostawić puste, ponieważ operacja automatycznie wykryje prawdopodobny język jako część jego wykonania.

> [!NOTE]
> Dla **language** parametru języka `eng` przypisz lub pozostaw go pusty, aby wyświetlić odpowiedź **klasyfikacji** wspomaganej maszynowo (funkcja podglądu). **Ta funkcja obsługuje tylko język angielski**.
>
> W przypadku wykrywania **wulgaryzmów** należy użyć [kodu ISO 639-3](http://www-01.sil.org/iso639-3/codes.asp) obsługiwanych języków wymienionych w tym artykule lub pozostawić go pustym.

W przypadku **autokorekty** **, pii**i **klasyfikuj (podgląd)** wybierz **true**. Pozostaw pole **Nrd** puste.

  ![Tekst — parametry kwerendy konsoli ekranowej](images/text-api-console-inputs.PNG)

### <a name="content-type"></a>Typ zawartości

W przypadku **zawartości typu**wybierz typ zawartości, którą chcesz prześwietlić. W tym przykładzie należy użyć domyślnego **tekstu/zwykłego** typu zawartości. W polu **Ocp-Apim-Subscription-Key** wprowadź klucz subskrypcji.

### <a name="sample-text-to-scan"></a>Przykładowy tekst do skanowania

W **treści Żądanie** wprowadź tekst. W poniższym przykładzie pokazano celowe literówki w tekście.

```
Is this a grabage or crap email abcdef@abcd.com, phone: 4255550111, IP: 
255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
```

## <a name="analyze-the-response"></a>Analizowanie odpowiedzi

Poniższa odpowiedź pokazuje różne spostrzeżenia z interfejsu API. Zawiera potencjalne wulgaryzmy, dane osobowe, klasyfikację (wersja zapoznawcza) i wersję automatycznie poprawioną.

> [!NOTE]
> Funkcja "Klasyfikacja" wspomagana maszynowo znajduje się w wersji zapoznawczej i obsługuje tylko język angielski.

```json
{
   "original_text":"Is this a grabage or crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "normalized_text":"   grabage  crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "auto_corrected_text":"Is this a garbage or crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "status":{
      "code":3000,
      "description":"OK"
   },
   "pii":{
      "email":[
         {
            "detected":"abcdef@abcd.com",
            "sub_type":"Regular",
            "text":"abcdef@abcd.com",
            "index":32
         }
      ],
      "ssn":[

      ],
      "ipa":[
         {
            "sub_type":"IPV4",
            "text":"255.255.255.255",
            "index":72
         }
      ],
      "phone":[
         {
            "country_code":"US",
            "text":"6657789887",
            "index":56
         }
      ],
      "address":[
         {
            "text":"1 Microsoft Way, Redmond, WA 98052",
            "index":89
         }
      ]
   },
   "language":"eng",
   "terms":[
      {
         "index":12,
         "original_index":21,
         "list_id":0,
         "term":"crap"
      }
   ],
   "tracking_id":"WU_ibiza_65a1016d-0f67-45d2-b838-b8f373d6d52e_ContentModerator.
   F0_fe000d38-8ecd-47b5-a8b0-4764df00e3b5"
}
```

Szczegółowe wyjaśnienie wszystkich sekcji odpowiedzi JSON można znaleźć w przewodniku koncepcyjnym [moderacji tekstu.](text-moderation-api.md)

## <a name="next-steps"></a>Następne kroki

Użyj interfejsu API REST w kodzie lub postępuj zgodnie z [programem Szybki start .NET SDK,](dotnet-sdk-quickstart.md) aby zintegrować się z aplikacją.
