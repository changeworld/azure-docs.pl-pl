---
title: Planowanie aplikacji — LUIS
titleSuffix: Azure Cognitive Services
description: Zaplanuj odpowiednie intencje i jednostki dotyczące aplikacji, a następnie utwórz plany aplikacji w Language Understanding Intelligent Services (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: b5e5df111b81cb60b6d194be190421bdb5ce2683
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467702"
---
# <a name="plan-your-luis-app-schema-with-subject-domain-and-data-extraction"></a>Planowanie schematu aplikacji LUIS przy użyciu domeny podmiotu i wyodrębniania danych

Schemat aplikacji LUIS zawiera intencje i jednostki powiązane z Twoją domeną podmiotu. Zamiary klasyfikują wyrażenia długości użytkownika i jednostki wyodrębnią dane z wyrażenia długości użytkownika. 

## <a name="identify-your-domain"></a>Zidentyfikuj domenę

Aplikacja LUIS jest wyśrodkowana wokół tematu specyficznego dla domeny.  Na przykład możesz mieć aplikację podróży, która wykonuje rezerwacje biletów, lotów, hoteli i samochodów. Inna aplikacja może zapewnić zawartość powiązaną z wykonywaniem, śledzeniem wysiłków związanych z sprawnością i ustawianiem celów. Zidentyfikowanie domeny ułatwia znalezienie wyrazów lub fraz, które są ważne dla Twojej domeny.

> [!TIP]
> LUIS oferuje [prebudowane domeny](luis-how-to-use-prebuilt-domains.md) dla wielu typowych scenariuszy.
> Sprawdź, czy możesz użyć wstępnie skompilowanej domeny jako punktu wyjścia dla aplikacji.

## <a name="identify-your-intents"></a>Zidentyfikuj swoje intencje

Pomyśl o [intencjach](luis-concept-intent.md) , które są ważne dla zadania aplikacji. 

Przyjrzyjmy się przykładowi do aplikacji podróży z funkcjami do rezerwacji lotu i sprawdzania pogody w miejscu docelowym użytkownika. Można zdefiniować `BookFlight` i `GetWeather` intencje dla tych działań. 

W bardziej złożonej aplikacji z większą liczbą funkcji masz więcej intencji i należy je dokładnie definiować, aby nie było to intencje. Na przykład `BookFlight` i `BookHotel` mogą wymagać oddzielenia intencji, ale `BookInternationalFlight` i `BookDomesticFlight` mogą być zbyt podobne.

> [!NOTE]
> Najlepszym rozwiązaniem jest użycie tylko tyle rzeczy, ile potrzebujesz do wykonywania funkcji aplikacji. Jeśli określisz zbyt wiele intencji, będzie trudniejsze, aby LUIS prawidłowo klasyfikować wyrażenia długości. Jeśli zdefiniujesz zbyt kilka, może to być ogólny, że nakładają się na siebie.

Jeśli nie musisz identyfikować ogólnego zamiaru użytkownika, Dodaj wszystkie przykładowe wyrażenia długości użytkownika do zamiaru brak. Jeśli aplikacja zostanie powiększona o potrzebę bardziej zamiarów, można utworzyć je później. 

## <a name="create-example-utterances-for-each-intent"></a>Utwórz przykład wyrażenia długości dla każdego zamiaru

Po ustaleniu intencji należy utworzyć dla każdego zamiaru 15 do 30 przykład wyrażenia długości. Aby rozpocząć od, nie ma mniej niż tej liczby lub Utwórz zbyt wiele wyrażenia długości dla każdego zamiaru. Każdy wypowiedź powinien różnić się od poprzedniego wypowiedźu. Dobra odmiana wyrażenia długości obejmuje ogólną liczbę słów, wybór wyrazów, intensywność i znaki interpunkcyjne. 

Przejrzyj [wyrażenia długości](luis-concept-utterance.md) , aby uzyskać więcej informacji.

## <a name="identify-your-entities"></a>Identyfikowanie jednostek

W przykładzie wyrażenia długości Określ jednostki, które mają zostać wyodrębnione. Aby zaksięgować lot, potrzebne są informacje, takie jak lokalizacja docelowa, Data, linia lotnicza, Kategoria biletu i Klasa podróży. Utwórz jednostki dla tych typów danych, a następnie Oznacz [jednostki](luis-concept-entity-types.md) w przykładzie wyrażenia długości, ponieważ są one ważne dla realizacji zamierzeń. 

Podczas określania, które obiekty mają być używane w aplikacji, należy pamiętać, że istnieją różne typy jednostek do przechwytywania relacji między typami obiektów. [Jednostki w Luis](luis-concept-entity-types.md) zapewniają więcej szczegółowych informacji o różnych typach.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z typowym [cyklem programistycznym](luis-concept-app-iteration.md).  