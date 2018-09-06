---
title: Planowanie aplikacji usługi Language Understanding (LUIS) — usługi Azure cognitive Services | Dokumentacja firmy Microsoft
description: Konspekt odpowiednią aplikację intencje i podmioty, a następnie utwórz swoje plany aplikacji w Language Understanding Intelligent Services (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 09/06/2017
ms.author: diberry
ms.openlocfilehash: 7c8974767621ad574b243fba5c3e49d97ec142e6
ms.sourcegitcommit: 3d0295a939c07bf9f0b38ebd37ac8461af8d461f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2018
ms.locfileid: "43842276"
---
# <a name="plan-your-luis-app"></a>Planowanie aplikacji usługi LUIS

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

### <a name="simple-entity"></a>Prosta jednostka
Proste jednostki w tym artykule opisano pojęcia pojedynczego.

![proste jednostki](./media/luis-plan-your-app/simple-entity.png)

Zobacz [wyodrębnianie danych](luis-concept-data-extraction.md#simple-entity-data) dowiedzieć się więcej o wyodrębniania proste jednostki z punktu końcowego odpowiedź JSON na zapytanie. Skorzystaj z tej [Szybki Start](luis-quickstart-primary-and-secondary-data.md) Aby dowiedzieć się więcej o sposobie używania prostego jednostki.

### <a name="hierarchical-entity"></a>Jednostka hierarchiczna
Hierarchiczna jednostka jest specjalnym typem **proste** jednostki; Definiowanie kategorii i jej elementów członkowskich w formie relacji nadrzędny podrzędny. Relacja jest określany przez kontekst z w ramach wypowiedź. Elementy podrzędne hierarchiczne jednostki są także proste jednostki.

![Hierarchiczna jednostki](./media/luis-plan-your-app/hierarchical-entity.png)

Zobacz [wyodrębnianie danych](luis-concept-data-extraction.md#hierarchical-entity-data) dowiedzieć się więcej o wyodrębniania hierarchiczne jednostki z punktu końcowego odpowiedź JSON na zapytanie. Skorzystaj z tej [Szybki Start](luis-quickstart-intent-and-hier-entity.md) Aby dowiedzieć się więcej o sposobie używania hierarchiczne jednostki.

### <a name="composite-entity"></a>Złożone jednostki
Jednostka złożonego składa się z innych jednostek, które tworzą części całości. Złożone jednostka zawiera różne typy jednostek.

![Złożone jednostki](./media/luis-plan-your-app/composite-entity.png)

Zobacz [wyodrębnianie danych](luis-concept-data-extraction.md#composite-entity-data) dowiedzieć się więcej o wyodrębniania złożonego jednostki z punktu końcowego odpowiedź JSON na zapytanie. Skorzystaj z tej [samouczek](luis-tutorial-composite-entity.md) Aby dowiedzieć się więcej o sposobie używania złożonego jednostki.

### <a name="prebuilt-entity"></a>Wstępnie utworzone jednostki
Udostępnia usługi LUIS [ze wstępnie utworzonych jednostek](luis-prebuilt-entities.md) dla wspólnych typów danych, takich jak numer, danych, adres e-mail i adres URL. Liczba wstępnie utworzone jednostki można użyć liczby biletów, w kolejności biletu.

![Liczba wstępnie utworzone jednostki](./media/luis-plan-your-app/number-entity.png)

Zobacz [wyodrębnianie danych](luis-concept-data-extraction.md#prebuilt-entity-data) dowiedzieć się więcej o wyodrębniania wstępnie utworzone jednostki z punktu końcowego odpowiedź JSON na zapytanie. 

### <a name="list-entity"></a>Jednostka listy 
Jednostki listy to lista wyraźnie określonych wartości. Każda wartość składa się z co najmniej jeden synonimów. W aplikacji podróży można utworzyć jednostkę listy do reprezentowania nazwy lotniczego.

![Lista jednostek](./media/luis-plan-your-app/list-entity.png)

Zobacz [wyodrębnianie danych](luis-concept-data-extraction.md#list-entity-data) dowiedzieć się więcej o wyodrębniania listy jednostek z punktu końcowego odpowiedź JSON na zapytanie. Skorzystaj z tej [Szybki Start](luis-quickstart-intent-and-list-entity.md) Aby dowiedzieć się więcej o sposobie używania obiektami listy.

### <a name="regular-expression-entity"></a>Wyrażenie regularne jednostki
Jednostka wyrażenia regularnego umożliwia LUIS można wyodrębnić dobrze sformatowane dane z wypowiedź oparte na wyrażeniu regularnym.

![Wyrażenie regularne jednostki](./media/luis-plan-your-app/regex-entity.png)

Zobacz [wyodrębnianie danych](luis-concept-data-extraction.md#regular-expression-entity-data) dowiedzieć się więcej o wyodrębnianie jednostek wyrażenia regularnego z punktu końcowego odpowiedź JSON na zapytanie. Spróbuj [Szybki Start](luis-quickstart-intents-regex-entity.md) Aby dowiedzieć się więcej o sposobie używania jednostki wyrażenia regularnego.

## <a name="next-steps"></a>Kolejne kroki
Po Twojej aplikacji jest uczony opublikowane i pobiera wypowiedzi punktu końcowego, planu wdrożenia przewidywanie ulepszenia [aktywne uczenie](luis-how-to-review-endoint-utt.md), [frazę list](luis-concept-feature.md), i [wzorców](luis-concept-patterns.md). 


* Zobacz [Utwórz swoją pierwszą aplikację Language Understanding Intelligent Services (LUIS)](luis-get-started-create-app.md) szybki Przewodnik tworzenia aplikacji usługi LUIS.
