---
title: Planowanie aplikacji
titleSuffix: Language Understanding - Azure Cognitive Services
description: Konspekt odpowiednią aplikację intencje i podmioty, a następnie utwórz swoje plany aplikacji w Language Understanding Intelligent Services (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: diberry
ms.openlocfilehash: 9d54cff81f39f41b60800e9b33f3b4da1a735d85
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58893439"
---
# <a name="plan-your-luis-app-with-subject-domain-intents-and-entities"></a>Planowanie aplikacji usługi LUIS z domeny podmiotu, intencje i podmioty

Należy zaplanować Twojej aplikacji. Określ domenę, w tym możliwe intencje i podmioty, które mają zastosowanie do aplikacji.  

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

Po określeniu intencji Utwórz 10 lub 15 wypowiedzi przykład dla każdego intencji. Najpierw nie będą mieć mniej niż ta liczba lub utworzyć wiele wypowiedzi dla każdego intencji. Każdy wypowiedź powinna być inna niż poprzednie wypowiedź. Dobre różnych w wypowiedzi zawiera ogólną wyrazów, wybór programu word, zlecenie czasu teraźniejszego i znaki interpunkcyjne. 

## <a name="identify-your-entities"></a>Identyfikowanie jednostek

Na przykład wypowiedzi identyfikuje jednostek, które mają zostać wyodrębnione. Aby zarezerwować lotu, muszą pewne informacje, takie jak docelowy, Data, linii lotniczych, kategoria bilet, a podróży klasy. Utwórz jednostki dla tych typów danych, a następnie Oznacz [jednostek](luis-concept-entity-types.md) w wypowiedzi przykładzie ponieważ są one ważne przy wykonywaniu intencji. 

Po ustaleniu, które obiekty do użycia w aplikacji, należy pamiętać o tym, że istnieją różne rodzaje jednostek do przechwytywania relacje między typami obiektów. [Jednostki w LUIS](luis-concept-entity-types.md) zapewnia więcej szczegółów na temat różnych typów.

## <a name="next-steps"></a>Kolejne kroki

Po Twojej aplikacji jest uczony opublikowane i pobiera wypowiedzi punktu końcowego, planu wdrożenia przewidywanie ulepszenia [aktywne uczenie](luis-how-to-review-endpoint-utterances.md), [frazę list](luis-concept-feature.md), i [wzorców](luis-concept-patterns.md). 


* Zobacz [Utwórz swoją pierwszą aplikację Language Understanding Intelligent Services (LUIS)](luis-get-started-create-app.md) szybki Przewodnik tworzenia aplikacji usługi LUIS.
