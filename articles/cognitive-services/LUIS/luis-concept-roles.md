---
title: Role dla jednostek — LUIS
titleSuffix: Azure Cognitive Services
description: Role są podtypy nazwanych, kontekstowych podmiotu używana tylko we wzorcach. Na przykład w wypowiedź `buy a ticket from New York to London`, zarówno w Nowym Jorku, jak i w Londynie są miast, ale każda ma inne znaczenie w zdaniu. Nowy Jork jest miasto źródła i Londyn jest miasta docelowego.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: diberry
ms.openlocfilehash: eead9e0fec8ac4322bc7816de4a4774f8be8129c
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70257969"
---
# <a name="entity-roles-for-contextual-subtypes"></a>Role jednostek dla podtypów kontekstowych

Role umożliwiają jednostkom posiadanie nazwanych podtypów. Roli można używać z dowolnym prekompilowanym lub niestandardowym typem jednostki i używanym w obu przykładach wyrażenia długości i wzorców. 

<a name="example-role-for-entities"></a>
<a name="roles-with-prebuilt-entities"></a>

## <a name="machine-learned-entity-example-of-roles"></a>Przykład ról poznania maszynowego

W wypowiedź "Kup bilet od **Nowego Jorku** do **Londyn**, zarówno Nowy Jork, jak i Londyn są miastami, ale każdy z nich ma inne znaczenie w zdaniu. Nowy Jork jest miasto źródła i Londyn jest miasta docelowego. 

```
buy a ticket from New York to London
```

Role nadaj nazwę różnic:

|Typ jednostki|Nazwa jednostki|Role|Cel|
|--|--|--|--|
|Proste|Location|źródło|Jeżeli płaszczyzny pozostawia z|
|Proste|Location|miejsce docelowe|gdzie wyładowuje płaszczyzna|

## <a name="non-machine-learned-entity-example-of-roles"></a>Przykład ról niezgodnych z maszyną

W wypowiedź "Zaplanuj spotkanie od 8 do 9", obie liczby wskazują godzinę, ale każdy z nich ma inne znaczenie w wypowiedź. Role podają nazwę różnic. 

```
Schedule the meeting from 8 to 9
```

|Typ jednostki|Nazwa roli|Value|
|--|--|--|
|Wstępnie utworzone datetimeV2|Rozpoczęcia|8|
|Wstępnie utworzone datetimeV2|Endtime|9|

## <a name="are-multiple-entities-in-an-utterance-the-same-thing-as-roles"></a>Czy wiele jednostek w wypowiedź tym samym jako role? 

W wypowiedź może istnieć wiele jednostek i można go wyodrębnić bez użycia ról. Jeśli kontekst zdania wskazuje, która wersja jednostki ma wartość, należy użyć roli. 

### <a name="dont-use-roles-for-duplicates-without-meaning"></a>Nie używaj ról dla duplikatów bez znaczenia

Jeśli wypowiedź zawiera listę lokalizacji `I want to travel to Seattle, Cairo, and London.`, to lista, w której każdy element nie ma dodatkowych znaczenia. 

### <a name="use-roles-if-duplicates-indicate-meaning"></a>Użyj ról, jeśli duplikaty wskazują znaczenie

Jeśli wypowiedź zawiera listę lokalizacji z znaczeniem `I want to travel from Seattle, with a layover in Londen, landing in Cairo.`, należy przechwycić to znaczenie dla elementu Origin, layover i Destination z rolami.

### <a name="roles-can-indicate-order"></a>Role mogą wskazywać kolejność

Jeśli wypowiedź zmienił się w celu wskazania kolejności, którą chcesz wyodrębnić, `I want to first start with Seattle, second London, then third Cairo`można wyodrębnić ją na kilka sposobów. Można oznaczyć tokeny wskazujące rolę, `first start with` `second`,, `third`. Można również użyć wstępnie skompilowanej **liczby porządkowej** jednostki i wstępnie skompilowanej jednostki **GeographyV2** w jednostce złożonej, aby przechwycić pomysł dotyczący kolejności i miejsca. 

## <a name="how-are-roles-used-in-example-utterances"></a>W jaki sposób role są używane w przykładowej wyrażenia długości?

Gdy jednostka ma rolę, a jednostka jest oznaczona jako przykład wypowiedź, możesz wybrać tylko jednostkę lub wybrać jednostkę i rolę. 

W poniższym przykładzie wyrażenia długości są używane jednostki i role:

|Widok tokenu|Widok jednostki|
|--|--|
|Chcę dowiedzieć się więcej o **Seattle**|Chcę dowiedzieć się więcej o lokalizacji {Location}|
|Kup bilet od Seattle do Nowego Jorku|Kup bilet od {Location: Origin} do {Location: Destination}|

## <a name="how-are-roles-used-in-patterns"></a>Jak role są używane we wzorcach?
W polu wypowiedź szablonu wzorca role są używane w ramach wypowiedź: 

|Wzorzec z rolami jednostki|
|--|
|`buy a ticket from {Location:origin} to {Location:destination}`|


## <a name="role-syntax-in-patterns"></a>Składnia roli we wzorcach
Jednostki i rola są ujęte w nawiasach, `{}`. Jednostka i rola są rozdzielone średnikiem. 

## <a name="entity-roles-versus-collaborator-roles"></a>Role ról i współpracowników

Role jednostek są stosowane do modelu danych aplikacji LUIS. Role [współpracowników lub współautorów](luis-concept-keys.md#contributions-from-other-authors) stosują się do poziomów dostępu autorstwa. 

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="next-steps"></a>Następne kroki

* Korzystanie z [samouczka praktycznego](tutorial-entity-roles.md) przy użyciu ról jednostek z jednostkami niezgodnymi z maszyną
* Dowiedz się, jak dodać [ról](luis-how-to-add-entities.md#add-a-role-to-pattern-based-entity)
