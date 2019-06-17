---
title: Role dla jednostek
titleSuffix: Azure Cognitive Services
description: Role są podtypy nazwanych, kontekstowych podmiotu używana tylko we wzorcach. Na przykład w wypowiedź `buy a ticket from New York to London`, zarówno w Nowym Jorku, jak i w Londynie są miast, ale każda ma inne znaczenie w zdaniu. Nowy Jork jest miasto źródła i Londyn jest miasta docelowego.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 318e71b68bbabeeef34c75a412f9fdd5b6db754a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65073025"
---
# <a name="entity-roles-for-contextual-subtypes"></a>Role jednostki dla podtypów kontekstowych

Zezwalaj na role mają nazwane podtypy jednostki. Rolę można używać z dowolnego typu wbudowanych lub niestandardowych instytucji rządowej USA i używać zarówno w przykładzie wypowiedzi i wzorce. 

<a name="example-role-for-entities"></a>
<a name="roles-with-prebuilt-entities"></a>

## <a name="machine-learned-entity-example-of-roles"></a>Przykład przedstawiono maszyny jednostki ról

W polu wypowiedź "Kup biletu z **Nowy Jork** do **Londyn**, zarówno w Nowym Jorku, jak i w Londynie są miast, ale każda ma inne znaczenie w zdaniu. Nowy Jork jest miasto źródła i Londyn jest miasta docelowego. 

```
buy a ticket from New York to London
```

Role nadaj nazwę różnic:

|Typ jednostki|Nazwa jednostki|Rola|Przeznaczenie|
|--|--|--|--|
|Proste|Lokalizacja|źródło|Jeżeli płaszczyzny pozostawia z|
|Proste|Lokalizacja|miejsce docelowe|gdzie wyładowuje płaszczyzna|

## <a name="non-machine-learned-entity-example-of-roles"></a>Przykład — przedstawiono maszyny jednostki ról

W wypowiedź "Zaplanuj spotkanie od 8 do 9", obie liczby wskazują godzinę, ale zawsze ma inne znaczenie w wypowiedź. Role zawierają nazwę różnice. 

```
Schedule the meeting from 8 to 9
```

|Typ jednostki|Nazwa roli|Wartość|
|--|--|--|
|Wstępnie utworzone datetimeV2|Godzina rozpoczęcia|8|
|Wstępnie utworzone datetimeV2|Czas zakończenia|9|

## <a name="are-multiple-entities-in-an-utterance-the-same-thing-as-roles"></a>Czy wiele jednostek w wypowiedź tak samo jak role? 

Wiele jednostek może znajdować się w wypowiedź i można wyodrębnić bez korzystania z ról. Jeśli kontekst zdania wskazuje za pomocą wersji jednostki ma wartość, a następnie roli powinny być używane. 

### <a name="dont-use-roles-for-duplicates-without-meaning"></a>Nie używaj ról dla duplikatów bez znaczenia

Jeśli wypowiedź zawiera listę lokalizacji, `I want to travel to Seattle, Cairo, and London.`, jest to lista gdzie każdy element nie ma dodatkowych znaczeń. 

### <a name="use-roles-if-duplicates-indicate-meaning"></a>Używanie ról, jeśli duplikaty wskazują znaczenie

Jeśli wypowiedź zawiera listę lokalizacji o znaczeniu, `I want to travel from Seattle, with a layover in Londen, landing in Cairo.`, rozumieniu pochodzenia, layover i docelowym mają być przechwytywane z rolami.

### <a name="roles-can-indicate-order"></a>Role można wskazać kolejność

Zmiana wypowiedź do wskazania kolejności, w którym chcesz wyodrębnić, `I want to first start with Seattle, second London, then third Cairo`, można wyodrębnić na kilka sposobów. Można oznaczyć tokenów, które wskazują z ról, `first start with`, `second`, `third`. Możesz też użyć wstępnie utworzone jednostki **numer** i **GeographyV2** wstępnie utworzone jednostki w jednostce złożone do przechwytywania pomysł, kolejność i miejsca. 

## <a name="how-are-roles-used-in-example-utterances"></a>Jak role są używane w wyrażenia o przykład?

Jeśli określona jednostka ma rolę, a jednostka jest oznaczony w przykładzie wypowiedź, do wyboru masz wybranie tylko jednostki lub wybrania jednostki oraz roli. 

Poniższy przykład wypowiedzi użyć jednostki i role:

|Wyświetl tokenu|Wyświetl jednostki|
|--|--|
|Jestem interesujące dowiedzieć więcej na temat **Seattle**|Interesuję się dodatkowe informacje o lokalizacji {Location}|
|Kup biletu z Seattle w Nowym Jorku|Kup biletu pochodzenia {lokalizacji:} do {lokalizacji: Destination}|

## <a name="how-are-roles-used-in-patterns"></a>Jak role są używane we wzorcach?
W polu wypowiedź szablonu wzorca role są używane w ramach wypowiedź: 

|Wzorzec z rolami jednostki|
|--|
|`buy a ticket from {Location:origin} to {Location:destination}`|


## <a name="role-syntax-in-patterns"></a>Składnia roli we wzorcach
Jednostki i rola są ujęte w nawiasach, `{}`. Jednostka i rola są rozdzielone średnikiem. 

## <a name="entity-roles-versus-collaborator-roles"></a>Role jednostki i role współpracownika

Role jednostki mają zastosowanie do modelu danych aplikacji usługi LUIS. [Współautor](luis-concept-collaborator.md) role dotyczą poziomów dostępu do tworzenia. 

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="next-steps"></a>Kolejne kroki

* Użyj [Podręcznik samouczku](tutorial-entity-roles.md) przy użyciu ról jednostki przy użyciu jednostek przedstawiono maszyny
* Dowiedz się, jak dodać [ról](luis-how-to-add-entities.md#add-a-role-to-pattern-based-entity)
