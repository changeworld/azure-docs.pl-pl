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
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: dc648b30dc1236080be06044f510557ae0ce9476
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638315"
---
# <a name="plan-your-luis-app-with-subject-domain-intents-and-entities"></a>Planowanie aplikacji LUIS przy użyciu domeny podmiotu, intencji i jednostek

Aby zaplanować aplikację, zidentyfikuj domenę obszaru tematu. Obejmuje to możliwe intencje i jednostki, które są istotne dla Twojej aplikacji.  

## <a name="identify-your-domain"></a>Zidentyfikuj domeny

Aplikacją usługi LUIS skupia się na temat specyficznego dla domeny.  Na przykład masz aplikację podróży, która wykonuje rezerwacji biletów, lotów, hotele i samochody dzierżawy. Innej aplikacji może przekazać zawartości powiązanej z wykonywania i śledzenie działań przydatności ustawienie cele. Identyfikującej domenę, pomoże Ci znaleźć słów i fraz, które są istotne dla Twojej domeny.

> [!TIP]
> Oferuje usługi LUIS [ze wstępnie utworzonych domen](luis-how-to-use-prebuilt-domains.md) obsługę wielu typowych scenariuszy.
> Sprawdź, jeśli wstępnie domeny można użyć jako punktu wyjścia dla twojej aplikacji.

## <a name="identify-your-intents"></a>Zidentyfikuj swoje intencji

Pomyśl o [intencji](luis-concept-intent.md) , które są istotne dla zadań aplikacji. Spójrzmy na przykład aplikacji podróży, z funkcjami Zarezerwuj lot i sprawdzić informacje o pogodzie w miejscu docelowym użytkownika. Można zdefiniować "BookFlight" i "GetWeather" intencji do wykonywania tych akcji. W bardziej złożonych aplikacji z innych funkcji masz więcej intencje i należy zdefiniować je dokładnie tak, aby nie były zbyt określonych. Na przykład "BookFlight" i "BookHotel" może być potrzebny oddzielne intencji, ale "BookInternationalFlight" i "BookDomesticFlight" może być zbyt podobne.

> [!NOTE]
> Jest najlepszym rozwiązaniem, aby używał tylko tyle intencji na potrzeby wykonywania funkcji aplikacji. Jeśli zdefiniujesz intencji zbyt wiele, zrozumieniem usługi LUIS do klasyfikowania wypowiedzi poprawnie. Jeśli zdefiniujesz zbyt kilku, może być tak ogólne dotyczące nakładające się przestrzenie.

## <a name="create-example-utterances-for-each-intent"></a>Utwórz przykład wypowiedzi dla każdego intencji

Po ustaleniu intencji należy utworzyć dla każdego zamiaru 15 do 30 przykład wyrażenia długości. Aby rozpocząć od, nie ma mniej niż tej liczby lub Utwórz zbyt wiele wyrażenia długości dla każdego zamiaru. Każdy wypowiedź powinna być inna niż poprzednie wypowiedź. Dobre różnych w wypowiedzi zawiera ogólną wyrazów, wybór programu word, zlecenie czasu teraźniejszego i znaki interpunkcyjne. 

Przejrzyj [wyrażenia długości](luis-concept-utterance.md) , aby uzyskać więcej informacji.

## <a name="identify-your-entities"></a>Identyfikowanie jednostek

Na przykład wypowiedzi identyfikuje jednostek, które mają zostać wyodrębnione. Aby zaksięgować lot, potrzebne są informacje, takie jak lokalizacja docelowa, Data, linia lotnicza, Kategoria biletu i Klasa podróży. Utwórz jednostki dla tych typów danych, a następnie Oznacz [jednostki](luis-concept-entity-types.md) w przykładzie wyrażenia długości, ponieważ są one ważne dla realizacji zamierzeń. 

Po ustaleniu, które obiekty do użycia w aplikacji, należy pamiętać o tym, że istnieją różne rodzaje jednostek do przechwytywania relacje między typami obiektów. [Jednostki w LUIS](luis-concept-entity-types.md) zapewnia więcej szczegółów na temat różnych typów.

## <a name="next-steps"></a>Następne kroki

Po Twojej aplikacji jest uczony opublikowane i pobiera wypowiedzi punktu końcowego, planu wdrożenia przewidywanie ulepszenia [aktywne uczenie](luis-how-to-review-endpoint-utterances.md), [frazę list](luis-concept-feature.md), i [wzorców](luis-concept-patterns.md). 


* Zobacz [Utwórz swoją pierwszą aplikację Language Understanding Intelligent Services (LUIS)](luis-get-started-create-app.md) szybki Przewodnik tworzenia aplikacji usługi LUIS.
