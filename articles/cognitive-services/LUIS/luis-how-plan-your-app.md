---
title: Planowanie aplikacji — LUIS
titleSuffix: Azure Cognitive Services
description: Konspekt odpowiednią aplikację intencje i podmioty, a następnie utwórz swoje plany aplikacji w Language Understanding Intelligent Services (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: 6a155f4c43da03ccdc40d289742918973aa6da7b
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326777"
---
# <a name="plan-your-luis-app-schema-with-subject-domain-and-data-extraction"></a>Planowanie schematu aplikacji LUIS przy użyciu domeny podmiotu i wyodrębniania danych

Schemat aplikacji LUIS zawiera [intencje](luis-glossary.md#intent) i [jednostki](luis-glossary.md#entity) powiązane z Twoją [domeną](luis-glossary.md#domain)podmiotu. Zamiary klasyfikują [wyrażenia długości](luis-glossary.md#utterance)użytkownika i jednostki wyodrębnią dane z wyrażenia długości użytkownika.

## <a name="identify-your-domain"></a>Zidentyfikuj domeny

Aplikacja LUIS jest wyśrodkowana wokół domeny podmiotu. Na przykład może istnieć aplikacja podróży, która obsługuje rezerwacje biletów, lotów, hoteli i samochodów. Innej aplikacji może przekazać zawartości powiązanej z wykonywania i śledzenie działań przydatności ustawienie cele. Zidentyfikowanie domeny ułatwia znalezienie wyrazów lub fraz, które są istotne dla Twojej domeny.

> [!TIP]
> LUIS oferuje [prebudowane domeny](luis-how-to-use-prebuilt-domains.md) dla wielu typowych scenariuszy. Sprawdź, jeśli wstępnie domeny można użyć jako punktu wyjścia dla twojej aplikacji.

## <a name="identify-your-intents"></a>Zidentyfikuj swoje intencji

Pomyśl o [intencjach](luis-concept-intent.md) , które są ważne dla zadania aplikacji.

Spójrzmy na przykład aplikacji podróży, z funkcjami Zarezerwuj lot i sprawdzić informacje o pogodzie w miejscu docelowym użytkownika. Można zdefiniować `BookFlight` i `GetWeather` intencje dla tych działań.

W bardziej złożonej aplikacji z większą liczbą funkcji masz więcej intencji i należy je dokładnie definiować, aby nie było to intencje. Na przykład `BookFlight` i `BookHotel` mogą wymagać oddzielenia intencji, ale `BookInternationalFlight` i `BookDomesticFlight` mogą być zbyt podobne.

> [!NOTE]
> Jest najlepszym rozwiązaniem, aby używał tylko tyle intencji na potrzeby wykonywania funkcji aplikacji. Jeśli zdefiniujesz intencji zbyt wiele, zrozumieniem usługi LUIS do klasyfikowania wypowiedzi poprawnie. Jeśli zdefiniujesz zbyt kilka, może to być ogólny, że nakładają się na siebie.

Jeśli nie musisz identyfikować ogólnych założeń użytkowników, Dodaj wszystkie przykładowe wyrażenia długości użytkownika do zamiaru `None`. Jeśli aplikacja zostanie powiększona o potrzebę bardziej zamiarów, można utworzyć je później.

## <a name="create-example-utterances-for-each-intent"></a>Utwórz przykład wypowiedzi dla każdego intencji

Aby zacząć od, należy unikać tworzenia zbyt wielu wyrażenia długości dla każdego zamiaru. Po ustaleniu intencji należy utworzyć na przykład wyrażenia długości od 15 do 30. Każdy wypowiedź powinien różnić się od wcześniej podanej wyrażenia długości. Dobra odmiana wyrażenia długości zawierać ogólną liczbę słów, wybór wyrazów, intensywność i znaki interpunkcyjne.

Aby uzyskać więcej informacji, zobacz temat [wyrażenia długości for Luis Apps](luis-concept-utterance.md).

## <a name="identify-your-entities"></a>Identyfikowanie jednostek

Na przykład wypowiedzi identyfikuje jednostek, które mają zostać wyodrębnione. Aby zaksięgować lot, potrzebne są informacje, takie jak lokalizacja docelowa, Data, linia lotnicza, Kategoria biletu i Klasa podróży. Utwórz jednostki dla tych typów danych, a następnie Oznacz [jednostki](luis-concept-entity-types.md) w przykładzie wyrażenia długości. Jednostki są ważne do wykonywania zamierzeń.

Podczas określania obiektów, które mają być używane w aplikacji, należy pamiętać, że istnieją różne typy jednostek do przechwytywania relacji między typami obiektów. [Jednostki w Luis](luis-concept-entity-types.md) zapewniają więcej szczegółowych informacji o różnych typach.

> [!TIP]
> LUIS oferuje [wstępnie utworzone jednostki](luis-prebuilt-entities.md) na potrzeby typowych scenariuszy użytkownika. Rozważ użycie wstępnie utworzonych jednostek jako punktu wyjścia do tworzenia aplikacji.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Uczenie się LUIS Development lifecylce](luis-concept-app-iteration.md)

