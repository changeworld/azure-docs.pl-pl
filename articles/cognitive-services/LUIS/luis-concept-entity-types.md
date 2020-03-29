---
title: Typy jednostek — USŁUGA LUIS
titleSuffix: Azure Cognitive Services
description: 'Jednostki wyodrębnić dane z wypowiedź. Typy jednostek zapewniają przewidywalne wyodrębnianie danych. Istnieją dwa typy jednostek: maszynowo nauczona i nieuczone maszyny. Ważne jest, aby wiedzieć, jaki typ jednostki pracujesz w wypowiedzi.'
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: 6ee156efb5512c92d86ba05513b6a2b91df4eae8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221030"
---
# <a name="entities-and-their-purpose-in-luis"></a>Jednostki i ich cel w usłudze LUIS

Głównym celem jednostek jest zapewnienie aplikacji klienckiej przewidywalne wyodrębnianie danych. _Opcjonalnym,_ pomocniczym celem jest zwiększenie przewidywania intencji lub innych jednostek za pomocą deskryptorów.

Istnieją dwa typy jednostek:

* maszynowo nauczona - z kontekstu
* nieuczone maszynowo — dla dokładnych dopasowań tekstu, dopasowań wzorców lub wykrywania przez wstępnie utworzone jednostki

Jednostki nauczenia się maszyn zapewniają najszerszy zakres opcji ekstrakcji danych. Jednostki nieuczone maszynowo działają na podstawie dopasowywania tekstu i mogą być używane niezależnie lub jako [ograniczenie](#design-entities-for-decomposition) dla jednostki nauczanej maszyną.

## <a name="entities-represent-data"></a>Jednostki reprezentują dane

Jednostki są danymi, które chcesz wyciągnąć z wypowiedź, takich jak nazwy, daty, nazwy produktów lub dowolnej znaczącej grupy słów. Wypowiedź może zawierać wiele jednostek lub w ogóle żadnych. Aplikacja _kliencka może_ potrzebować danych do wykonania swojego zadania.

Jednostki muszą być oznaczone spójnie we wszystkich wypowiedzi szkolenia dla każdego intencji w modelu.

 Można zdefiniować własne jednostki lub użyć wstępnie utworzonych encji, aby zaoszczędzić czas dla typowych pojęć, takich jak [datetimeV2](luis-reference-prebuilt-datetimev2.md), [liczba porządkowa,](luis-reference-prebuilt-ordinal.md) [poczta e-mail](luis-reference-prebuilt-email.md)i [numer telefonu.](luis-reference-prebuilt-phonenumber.md)

|Wypowiedź|Jednostka|Dane|
|--|--|--|
|Kup 3 bilety do Nowego Jorku|Wstępnie utworzony numer<br>Lokalizacja.Miejsce docelowe|3<br>Nowy Jork|
|Kup bilet z Nowego Jorku do Londynu 5 marca|Lokalizacja.Pochodzenie<br>Lokalizacja.Miejsce docelowe<br>Wstępnie utworzony datetimeV2|Nowy Jork<br>Londyn<br>5 marca 2018 r.|

### <a name="entities-are-optional"></a>Jednostki są opcjonalne

Podczas intencji są wymagane, jednostki są opcjonalne. Nie trzeba tworzyć jednostki dla każdej koncepcji w aplikacji, ale tylko dla tych wymaganych dla aplikacji klienckiej do podjęcia działań.

Jeśli wypowiedzi nie mają danych, których wymaga aplikacja kliencka, nie trzeba dodawać jednostek. W miarę rozwoju aplikacji i identyfikowania nowej potrzeby danych można dodać odpowiednie jednostki do modelu usługi LUIS później.

## <a name="entity-compared-to-intent"></a>Jednostka w porównaniu z zamiarem

Jednostka reprezentuje koncepcję danych wewnątrz wypowiedź, który ma być wyodrębniony.

Wypowiedź może opcjonalnie zawierać jednostki. Dla porównania przewidywanie intencji dla wypowiedź jest _wymagane_ i reprezentuje całą wypowiedź. Usługa LUIS wymaga wypowiedzi przykład są zawarte w intencji.

Należy wziąć pod uwagę następujące 4 wypowiedzi:

|Wypowiedź|Przewidywane intencje|Wyodrębnione jednostki|Wyjaśnienie|
|--|--|--|--|
|Pomoc|Pomoc|-|Nic do wyodrębnienia.|
|Wyślij coś|wyślij Coś|-|Nic do wyodrębnienia. Model nie został przeszkolony `something` do wyodrębniania w tym kontekście i nie ma też żadnego adresata.|
|Wyślij Bobowi prezent|wyślij Coś|`Bob`, `present`|Model został przeszkolony z [personName](luis-reference-prebuilt-person.md) wstępnie utworzonej jednostki, `Bob`która wyodrębniła nazwę . Jednostka nauczona maszynowo została `present`użyta do wyodrębnienia .|
|Wyślij Bobowi pudełko czekoladek|wyślij Coś|`Bob`, `box of chocolates`|Dwa ważne fragmenty danych, `Bob` `box of chocolates`a , zostały wyodrębnione przez podmioty.|

## <a name="design-entities-for-decomposition"></a>Elementy projektowe do rozkładu

Jest to dobry projekt jednostki, aby twój podmiot najwyższego poziomu jednostki maszynowo nauczył. Pozwala to na zmiany w projekcie jednostki w czasie i użycie **podskładników** (jednostek podrzędnych), opcjonalnie z **ograniczeniami** i **deskryptorami**, aby rozłożyć encję najwyższego poziomu na części wymagane przez aplikację kliencką.

Projektowanie do rozkładu umożliwia usługi LUIS zwrócić głęboki stopień rozpoznawania jednostek do aplikacji klienckiej. Dzięki temu aplikacja kliencka skupić się na regułach biznesowych i pozostawić rozpoznawanie danych do usługi LUIS.

### <a name="machine-learned-entities-are-primary-data-collections"></a>Jednostki nauczane maszynowo są podstawowymi zbiorami danych

[**Jednostki nauczanych maszyn**](tutorial-machine-learned-entity.md) są jednostką danych najwyższego poziomu. Podskładne są jednostkami podrzędnymi jednostek nauczanych maszyn.

Jednostki nauczane maszynowo wyzwala na podstawie kontekstu dowiedział się poprzez wypowiedzi szkolenia. Ograniczenia są **opcjonalnymi regułami** stosowanymi do jednostki nauczającej maszyną, która dodatkowo ogranicza wyzwalanie na podstawie definicji dopasowania dokładnego tekstu jednostki nieuczonej maszynowo, takiej jak [List](reference-entity-list.md) lub [Regex](reference-entity-regular-expression.md). Na przykład `size` jednostka nauczona maszynowo może `sizeList` mieć ograniczenie jednostki `size` listy, która ogranicza jednostkę `sizeList` do wyzwalania tylko wtedy, gdy napotkane są wartości zawarte w jednostce.

[**Deskryptory**](luis-concept-feature.md) są funkcje stosowane w celu zwiększenia znaczenia słów lub fraz dla przewidywania. Są one nazywane *deskryptorów,* ponieważ są one używane do *opisania* intencji lub jednostki. Deskryptory opisują wyróżniające cechy lub atrybuty danych, takie jak ważne słowa lub frazy, które usługa LUIS obserwuje i uczy się za pośrednictwem.

Podczas tworzenia funkcji listy fraz w aplikacji usługi LUIS jest domyślnie włączona globalnie i ma równomierne zastosowanie we wszystkich intencjach i jednostkach. Jeśli jednak lista fraz zostanie zastosowana jako deskryptor (funkcja) jednostki (lub *modelu*nauczonego maszynowo), jej zakres zmniejsza się do zastosowania tylko do tego modelu i nie jest już używany ze wszystkimi innymi modelami. Używanie listy fraz jako deskryptora do modelu pomaga rozkładu, pomagając w dokładności dla modelu, który jest stosowany do.

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>Typy jednostek

Wybierz jednostkę na podstawie sposobu wyodrębniania danych i sposobu ich przedstawiania po wyodrębnienia.

|Typ jednostki|Przeznaczenie|
|--|--|
|[**Maszynowo wyuczone**](tutorial-machine-learned-entity.md)|Jednostki nauczane maszynowo uczą się z kontekstu w wypowiedź. Nadrzędne grupowanie jednostek, niezależnie od typu jednostki. Dzięki temu zmienność rozmieszczenia w wypowiedzi przykład znaczące. |
|[**Listy**](reference-entity-list.md)|Lista elementów i ich synonimów wyodrębnionych z **dokładnym dopasowaniem tekstu**.|
|[**Wzór.any**](reference-entity-pattern-any.md)|Jednostka, w której koniec jednostki jest trudny do określenia. |
|[**Prekompilowanych**](luis-reference-prebuilt-entities.md)|Już przeszkolony, aby wyodrębnić określonego rodzaju danych, takich jak adres URL lub e-mail. Niektóre z tych wstępnie utworzonych jednostek są zdefiniowane w projekcie aparaty rozpoznawania typu open [source-Text.](https://github.com/Microsoft/Recognizers-Text) Jeśli określona kultura lub encja nie jest obecnie obsługiwana, współtwórz projekt.|
|[**Wyrażenie regularne**](reference-entity-regular-expression.md)|Używa wyrażenia regularnego do **dokładnego dopasowania tekstu**.|

## <a name="extracting-contextually-related-data"></a>Wyodrębnianie danych związanych kontekstowo

Wypowiedź może zawierać dwa lub więcej wystąpień jednostki, gdzie znaczenie danych opiera się na kontekście w wypowiedź. Przykładem jest wypowiedź do rezerwacji lotu, który ma dwie lokalizacje, pochodzenia i miejsca docelowego.

`Book a flight from Seattle to Cairo`

Dwa przykłady `location` jednostki muszą zostać wyodrębnione. Aplikacja kliencka musi znać typ lokalizacji dla każdego z nich, aby sfinalizować zakup biletu.

Istnieją dwie techniki wyodrębniania danych związanych z kontekstem:

 * Jednostka `location` jest jednostką nauczoną maszynowo i używa dwóch jednostek podskładnika do przechwytywania `origin` i `destination` (preferowane)
 * Jednostka `location` wykorzystuje dwie **roles** role `origin``destination`

Wiele jednostek może istnieć w wypowiedź i mogą być wyodrębnione bez użycia rozkładu lub ról, jeśli kontekst, w którym są one używane nie ma znaczenia. Na przykład jeśli wypowiedź zawiera listę `I want to travel to Seattle, Cairo, and London.`lokalizacji, jest to lista, na której każdy element nie ma dodatkowego znaczenia.

### <a name="using-subcomponent-entities-of-a-machine-learned-entity-to-define-context"></a>Używanie jednostek podskładnika jednostki jednostki nauczonej maszyn do definiowania kontekstu

Jednostki [**nauczane maszynowo**](tutorial-machine-learned-entity.md) można wyodrębnić dane opisujące akcję rezerwacji lotu, a następnie rozłożyć jednostkę najwyższego poziomu na oddzielne części wymagane przez aplikację kliencką.

W tym `Book a flight from Seattle to Cairo`przykładzie jednostka najwyższego `travelAction` poziomu może być `flight from Seattle to Cairo`i etykietowana w celu wyodrębnienia . Następnie tworzone są dwie jednostki podskładnika, wywoływane `origin` i `destination` `geographyV2` — oba z zastosowaniem ograniczenia wstępnie utworzonego encji. W wypowiedzi szkolenia `origin` i `destination` są odpowiednio oznaczone.

### <a name="using-entity-role-to-define-context"></a>Używanie roli encji do definiowania kontekstu

Rola jest nazwany alias dla jednostki na podstawie kontekstu w wypowiedź. Rola może być używana z dowolnym wstępnie utworzonym lub niestandardowym typem jednostki i używana zarówno w przykładowych wypowiedziach, jak i wzorcach. W tym przykładzie `location` jednostka potrzebuje `origin` `destination` dwóch ról i oba muszą być oznaczone w wypowiedzi przykład.

Jeśli usługa `location` LUIS znajdzie rolę, ale nie może określić roli, jednostka lokalizacji jest nadal zwracana. Aplikacja kliencka musiałaby wykonać z pytaniem, aby określić, jaki typ lokalizacji użytkownik miał na myśli.


## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>Jeśli potrzebujesz więcej niż maksymalna liczba jednostek

Jeśli potrzebujesz więcej niż limit, skontaktuj się z pomocą techniczną. Aby to zrobić, zbierz szczegółowe informacje o systemie, przejdź do witryny [sieci](luis-reference-regions.md#luis-website) Luis w sieci Web, a następnie wybierz pozycję **Pomoc techniczna**. Jeśli twoja subskrypcja platformy Azure obejmuje usługi pomocy technicznej, skontaktuj się z [pomocą techniczną platformy Azure.](https://azure.microsoft.com/support/options/)

## <a name="entity-prediction-status"></a>Stan przewidywania jednostki

Portal usługi LUIS pokazuje, gdy jednostka, w wypowiedź przykład, ma inny przewidywania jednostki niż wybrana jednostka. Ten inny wynik jest oparty na bieżącym przeszkolonym modelu.

## <a name="next-steps"></a>Następne kroki

Dowiedz się pojęć na temat dobrych [wypowiedzi](luis-concept-utterance.md).

Zobacz [Dodawanie jednostek,](luis-how-to-add-entities.md) aby dowiedzieć się więcej o dodaniu jednostek do aplikacji usługi LUIS.

Zobacz [Samouczek: Wyodrębnianie danych strukturalnych z wypowiedź użytkownika z jednostek nauczonych maszynowo w language understanding (LUIS),](tutorial-machine-learned-entity.md) aby dowiedzieć się, jak wyodrębnić dane strukturalne z wypowiedź przy użyciu jednostki maszyny.
 
