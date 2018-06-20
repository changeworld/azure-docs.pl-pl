---
title: Planowanie aplikacji LUIS | Dokumentacja firmy Microsoft
description: Konspekt intencje odpowiednią aplikację i jednostek, a następnie utwórz planów aplikacji w języku opis inteligentnego usług (LUIS).
services: cognitive-services
author: DeniseMak
manager: hsalama
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2017
ms.author: v-geberr
ms.openlocfilehash: 7aec5d5b90ac7145ce9f337ec74c590b4b88c6b1
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266366"
---
# <a name="plan-your-luis-app"></a>Planowanie aplikacji LUIS

Należy zaplanować aplikacji przed rozpoczęciem tworzenia go w LUIS. Przygotuj konspekt lub schemat możliwe intencje i jednostek, które mają zastosowanie do tematu specyficznego dla domeny aplikacji.  

## <a name="identify-your-domain"></a>Zidentyfikować domenę
Aplikacja LUIS skupia się na temat specyficznego dla domeny.  Na przykład może być aplikacji podróży, który wykonuje rezerwacji biletów, lotach, hotele i wynajem samochodów. Inną aplikację może udostępnić zawartość związanych z wykonywania i śledzenia działań przydatności oraz Ustawianie celów. 

> [!TIP]
> Oferuje LUIS [wbudowane domen](luis-how-to-use-prebuilt-domains.md) dla wielu typowych scenariuszy.
> Sprawdź, czy wbudowane domeny można użyć jako punkt początkowy aplikacji.

## <a name="identify-your-intents"></a>Zidentyfikuj Twojej lokalizacji docelowych
Pomyśl o [intencje](luis-concept-intent.md) które są istotne dla twojej aplikacji zadań. Spójrzmy przykład aplikacji podróży, z funkcjami zarezerwować lot i sprawdzić informacje o pogodzie w miejscu docelowym użytkownika. Można określić opcji "BookFlight" i "GetWeather" dla tych akcji. W bardziej złożonych aplikacji z funkcjami więcej masz więcej lokalizacji docelowych i należy zdefiniować je dokładnie tak, aby nie były zbyt określonych. Na przykład "BookFlight" i "BookHotel" może być konieczne osobnych lokalizacji docelowych, ale "BookInternationalFlight" i "BookDomesticFlight" może być zbyt podobne.

> [!NOTE]
> Jest najlepszym rozwiązaniem jest użycie tylko tyle intencje potrzebnych do wykonywania funkcji aplikacji. W przypadku definiowania zbyt wiele opcji, staje się on trudniej LUIS do klasyfikowania zniesławiających poprawnie. W przypadku definiowania za kilka, może być tak ogólne dotyczące nakładać.


## <a name="identify-your-entities"></a>Identyfikowanie jednostek
Zarezerwować lot, konieczne pewne informacje, takie jak docelowego, datę, linii lotniczych, kategoria bilet i podróży klasy. Można dodać je jako [jednostek](luis-concept-entity-types.md) ponieważ są one ważne przy wykonywaniu celem. 

Po określeniu, które jednostki do wykorzystania w aplikacji, należy pamiętać, że istnieją różne typy jednostek przechwytywania relacje między typami obiektów. [Jednostki w LUIS](luis-concept-entity-types.md) zawiera szczegółowe informacje o różnych typach.

### <a name="simple-entity"></a>Proste jednostki
Podmiot prosty opisuje jednej koncepcji.

![proste jednostki](./media/luis-plan-your-app/simple-entity.png)

Zobacz [wyodrębniania danych](luis-concept-data-extraction.md#simple-entity-data) Aby dowiedzieć się więcej o wyodrębniania jednostki prostej z punktu końcowego JSON odpowiedzi na kwerendę. Spróbuj jednostki prostej [szybkiego startu](luis-quickstart-primary-and-secondary-data.md) Aby dowiedzieć się więcej o sposobie używania prostego jednostki.

### <a name="hierarchical-entity"></a>Hierarchiczna jednostki
Hierarchiczna jednostka jest specjalnym rodzajem **proste** jednostki; Definiowanie kategorii i jej elementów członkowskich w postaci relacji nadrzędny podrzędny.

![Hierarchiczna jednostki](./media/luis-plan-your-app/hierarchical-entity.png)

Zobacz [wyodrębniania danych](luis-concept-data-extraction.md#hierarchical-entity-data) Aby dowiedzieć się więcej o wyodrębniania hierarchiczna jednostki z punktu końcowego JSON odpowiedzi na kwerendę. Spróbuj hierarchiczna jednostki [szybkiego startu](luis-quickstart-intent-and-hier-entity.md) Aby dowiedzieć się więcej o sposobie używania hierarchiczna jednostki.

### <a name="composite-entity"></a>Złożone jednostki
Złożone jednostki składa się z innych jednostek, które stanowią część całości. 

![złożone jednostki](./media/luis-plan-your-app/composite-entity.png)

Zobacz [wyodrębniania danych](luis-concept-data-extraction.md#composite-entity-data) Aby dowiedzieć się więcej o wyodrębniania obiekt złożony z punktu końcowego JSON odpowiedzi na kwerendę. Spróbuj złożonego jednostki [samouczek](luis-tutorial-composite-entity.md) Aby dowiedzieć się więcej o sposobie używania złożonego jednostki.

### <a name="prebuilt-entity"></a>Wbudowane jednostki
Udostępnia LUIS [wbudowane jednostek](Pre-builtEntities.md) dla typowych, takich jak `Number`, którego można użyć liczby biletów w kolejności biletu.

![Liczba jednostek wbudowane](./media/luis-plan-your-app/number-entity.png)

Zobacz [wyodrębniania danych](luis-concept-data-extraction.md#prebuilt-entity-data) Aby dowiedzieć się więcej o wyodrębniania jednostek wyrażenia regularnego z punktu końcowego JSON odpowiedzi na kwerendę. 

### <a name="list-entity"></a>Lista jednostki 
Jednostki listy to lista jawnie określonej wartości. Każda wartość składa się z co najmniej jeden synonimy. W aplikacji podróży można utworzyć jednostkę listy do reprezentowania nazwy lotnisku.

![Lista jednostki](./media/luis-plan-your-app/list-entity.png)

Zobacz [wyodrębniania danych](luis-concept-data-extraction.md#list-entity-data) Aby dowiedzieć się więcej o wyodrębniania listy jednostek z punktu końcowego JSON odpowiedzi na kwerendę. Spróbuj [szybkiego startu](luis-quickstart-intent-and-list-entity.md) Aby dowiedzieć się więcej na temat sposobu korzystania z jednostki listy.

### <a name="regular-expression-entity"></a>Wyrażenie regularne jednostki
Jednostki wyrażenia regularnego umożliwia LUIS wyodrębnić dane z utterance na podstawie wyrażenia wyrażenia regularnego.

![Wyrażenie regularne jednostki](./media/luis-plan-your-app/regex-entity.png)

Zobacz [wyodrębniania danych](luis-concept-data-extraction.md#regular-expression-entity-data) Aby dowiedzieć się więcej o wyodrębniania jednostek wyrażenia regularnego z punktu końcowego JSON odpowiedzi na kwerendę. Spróbuj [szybkiego startu](luis-quickstart-intents-regex-entity.md) Aby dowiedzieć się więcej na temat sposobu korzystania z jednostki wyrażenia regularnego.

## <a name="after-getting-endpoint-utterances"></a>Po pierwsze zniesławiających punktu końcowego
Po aplikacji pobiera zniesławiających punktu końcowego, planu wdrożenia ulepszenia prognozowania z [active learning](label-suggested-utterances.md), [frazę list](luis-concept-feature.md), i [wzorców](luis-concept-patterns.md). 

### <a name="patternany-entity"></a>Pattern.any jednostki
Patterns.any jest używana tylko w symbol zastępczy o zmiennej długości [deseniu](luis-concept-patterns.md) utterance szablonu, aby oznaczyć gdzie jednostki rozpoczęcia i zakończenia. Szablon zniesławiających odpowiadają [dokładny opis składni](luis-concept-patterns.md#pattern-syntax) do identyfikowania jednostki i tekst do pominięcia.


## <a name="next-steps"></a>Kolejne kroki
* Zobacz [tworzenie pierwszej aplikacji usługi inteligentnego opis języka (LUIS)] [ luis-get-started-create-app] Przewodnik szybkiego tworzenia aplikacji LUIS.

[luis-get-started-create-app]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app