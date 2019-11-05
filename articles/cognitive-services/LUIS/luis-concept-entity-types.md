---
title: Typy jednostek — LUIS
titleSuffix: Azure Cognitive Services
description: 'Jednostki wyodrębniają dane z wypowiedź. Typy jednostek umożliwiają przewidywalną wyodrębnianie danych. Istnieją dwa typy jednostek: Uczenie maszynowe i niekomputerowe. Ważne jest, aby wiedzieć, który typ jednostki jest używany w wyrażenia długości.'
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 9dc26e50e1c0f43e816e422f0fee91a246ea04a9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73487596"
---
# <a name="entities-and-their-purpose-in-luis"></a>Jednostki i ich cele w LUIS

Głównym celem jednostek jest nadanie aplikacji klienckiej przewidywalnej ekstrakcji danych. _Opcjonalne_, pomocnicze przeznaczenie ma na celu zwiększenie przewidywania zamiaru przy użyciu deskryptorów. 

Istnieją dwa typy jednostek: 

* komputer — z kontekstu
* nauczenie maszynowe — dla dokładnej dopasowania tekstu

Zawsze zaczyna się od jednostki uczeniej maszynowego, ponieważ zapewnia szeroką gamę opcji wyodrębniania danych.

## <a name="entity-compared-to-intent"></a>Jednostka porównana z intencją

Jednostka reprezentuje koncepcję danych w wypowiedź, który ma zostać wyodrębniony. 

Weź pod uwagę następujące 3 wyrażenia długości:

|Wypowiedź|Wyodrębnione dane|Wyjaśnienie|
|--|--|--|
|`Help`|-|Brak elementów do wyodrębnienia.|
|`Send Bob a present`|Robert, obecny|Robert jest ważne, aby zakończyć zadanie. Obecny może być wystarczająca informacja lub bot może być konieczne wyjaśnienie, co jest obecne z pytaniem.|
|`Send Bob a box of chocolates.`|Dwie ważne fragmenty danych, Robert i pudełka czekolady są ważne do zakończenia żądania użytkownika.|

Wypowiedź może zawierać wiele jednostek lub wcale nie. Aplikacja kliencka _może_ potrzebować jednostky do wykonania zadania. 

Zgodnie z porównaniem _wymagana_ jest prognoza zamiaru wypowiedź i reprezentuje cały Wypowiedź. LUIS wymaga przykładowego wyrażenia długości zawartego w zamierzeniu. Jeśli podstawowy zamiar wypowiedź nie jest istotny dla aplikacji klienckiej, Dodaj wszystkie wyrażenia długości do zamiaru brak. 

Jeśli okaże się, że w dalszej części cyklu życia aplikacji chcesz przerwać wyrażenia długości, możesz to łatwo zrobić. Może to być organizowanie wyrażenia długości podczas tworzenia lub można użyć przewidywanego zamiaru w aplikacji klienckiej. 

Nie ma potrzeby korzystania z przewidywanych zamierzeń w aplikacji klienckiej, ale jest ona zwracana jako część odpowiedzi dotyczącej punktu końcowego przewidywania.

## <a name="entities-represent-data"></a>Jednostki reprezentują dane

Jednostki są danymi, które chcesz ściągnąć z wypowiedź. Może to być nazwa, Data, nazwa produktu lub dowolna Grupa wyrazów. 

|Wypowiedź|Jednostka|Dane|
|--|--|--|
|Kup 3 bilety do Nowego Jorku|Wstępnie zbudowany numer<br>Location. Destination|3<br>Nowy Jork|
|Kup bilet od Nowego Jorku do Londyn w dniu 5 marca|Lokalizacja. Źródło<br>Location. Destination<br>Wstępnie zbudowany datetimeV2|Nowy Jork<br>Londyn<br>5 marca 2018 r.|

## <a name="entities-are-optional-but-highly-recommended"></a>Jednostki są opcjonalne, ale zdecydowanie zalecane

Podczas gdy wymagane są wymagania, jednostki są opcjonalne. Nie trzeba tworzyć jednostek dla każdej koncepcji w aplikacji, ale tylko dla tych, które są wymagane do podjęcia działania przez aplikację kliencką. 

Jeśli wyrażenia długości nie mają szczegółowych informacji, bot należy kontynuować, nie trzeba ich dodawać. Gdy Twoja aplikacja została dojrzała, możesz dodać je później. 

Jeśli nie masz pewności, jak korzystać z tych informacji, Dodaj kilka typowych wstępnie utworzonych jednostek, takich jak [datetimeV2](luis-reference-prebuilt-datetimev2.md), [porządkowa](luis-reference-prebuilt-ordinal.md), [adres e-mail](luis-reference-prebuilt-email.md)i [numer telefonu](luis-reference-prebuilt-phonenumber.md).

## <a name="design-entities-for-decomposition"></a>Projektowanie jednostek do dekompozycji

Rozpocznij projektowanie jednostki za pomocą jednostki, która jest wyuczenia maszynowego. Pozwala to na łatwe rozwój i wprowadzanie zmian w jednostce w czasie. Dodaj **podskładniki** (jednostki podrzędne) z **ograniczeniami** i **deskryptorami** , aby zakończyć projekt jednostki. 

Projektowanie pod kątem dekompozycji umożliwia LUISom zwrócenie głębokiego stopnia rozpoznawania jednostek do aplikacji klienckiej. Dzięki temu aplikacja kliencka może skupić się na regułach biznesowych i pozostawić dane do LUIS.

### <a name="machine-learned-entities-are-primary-data-collections"></a>Jednostki poznanie maszyn są podstawowymi kolekcjami danych

Obiekty, które są poznanie maszyn, są jednostką danych najwyższego poziomu. Podskładniki są jednostkami podrzędnymi jednostek obsługiwanych przez maszynę. 

**Ograniczenia** to jednostki pasujące do dokładnego tekstu, które stosują reguły do identyfikowania i wyodrębniania danych. **Deskryptory** to funkcje stosowane w celu zwiększenia znaczenia wyrazów lub fraz dla przewidywania.

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>Typy jednostek

Wybierz jednostkę na podstawie sposobu wyodrębniania danych i sposobu ich reprezentowania po wyodrębnieniu.

|Typ jednostki|Przeznaczenie|
|--|--|
|[**Komputer — informacje**](#composite-entity)|Nadrzędne grupowanie jednostek, niezależnie od typu jednostki. Jednostki poznanie maszyn są zgodne z kontekstem w wypowiedź. Sprawia to, że zmiany położenia w przykładzie wyrażenia długości są istotne. |
|[**Staw**](#list-entity)|Lista elementów i ich synonimy wyodrębnione z **dokładnym dopasowaniem do tekstu**.|
|[**Wzorzec. any**](#patternany-entity)|Obiekt, na którym koniec jednostki jest trudny do określenia. |
|[**Wstępnie utworzonych**](#prebuilt-entity)|Jest już szkolony do wyodrębniania określonego rodzaju danych, takich jak adres URL lub wiadomość e-mail. Niektóre z tych wstępnie skompilowanych jednostek są zdefiniowane w projekcie typu "Open Source [" — tekst](https://github.com/Microsoft/Recognizers-Text) . Jeśli określona kultura lub jednostka nie jest obecnie obsługiwana, współtworzy projekt.|
|[**Wyrażenie regularne**](#regular-expression-entity)|Używa wyrażenia regularnego do **dokładnego dopasowania tekstu**.|

### <a name="entity-role-defines-context"></a>Rola jednostki definiuje kontekst

Rola jednostki jest aliasem nazwanym na podstawie kontekstu w wypowiedź. Przykładem jest wypowiedź do zarezerwowania lotu, który ma dwie lokalizacje, źródło i miejsce docelowe.

`Book a flight from Seattle to Cairo`

Dwa przykłady jednostki `location` muszą zostać wyodrębnione. Klient-aplikacja musi znać typ lokalizacji dla każdej z nich, aby zakończyć zakupy biletów. Jednostka `location` musi mieć dwie role `origin` i `destination` i oba muszą być oznaczone w przykładzie wyrażenia długości. 

Jeśli LUIS odnajdzie `location` ale nie może ustalić roli, jednostka lokalizacji jest nadal zwracana. Aplikacja kliencka będzie musiała postępować zgodnie z pytaniem, aby określić, który typ lokalizacji należy do użytkownika. 

W wypowiedź może istnieć wiele jednostek i można go wyodrębnić bez użycia ról. Jeśli kontekst zdania wskazuje wartość jednostki, należy użyć roli.

Jeśli wypowiedź zawiera listę lokalizacji `I want to travel to Seattle, Cairo, and London.`, jest to lista, w której każdy element nie ma dodatkowego znaczenia. 

## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>Jeśli potrzebujesz więcej niż maksymalnej liczby jednostek 

Jeśli potrzebujesz więcej niż limit, skontaktuj się z pomocą techniczną. Aby to zrobić, Zbierz szczegółowe informacje o systemie, przejdź do witryny sieci Web [Luis](luis-reference-regions.md#luis-website) , a następnie wybierz pozycję **Pomoc techniczna**. Jeśli Twoja subskrypcja platformy Azure obejmuje usługi pomocy technicznej, skontaktuj się z [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/options/). 

## <a name="entity-prediction-status"></a>Stan przewidywania jednostek

Portal LUIS pokazuje, kiedy jednostka, w przykładzie wypowiedź, ma inne przewidywania jednostek niż wybrana przez Ciebie jednostka. Ten różny wynik jest oparty na bieżącym przeszkolonym modelu. 

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z pojęciami dotyczącymi dobrego [wyrażenia długości](luis-concept-utterance.md). 

Zobacz [Dodawanie jednostek](luis-how-to-add-entities.md) , aby dowiedzieć się więcej na temat dodawania jednostek do aplikacji Luis.
