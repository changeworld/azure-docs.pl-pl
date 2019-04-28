---
title: Usługi Azure Data Factory mapowanie danych przepływu, monitorowanie wizualne
description: Wizualnego monitorowania przepływu danych w usługi Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 90aa6261aebb9d1f7da89c101854bad8061dd6ff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61269047"
---
# <a name="monitor-data-flows"></a>Monitor przepływu danych

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Po zakończeniu kompilowania i debugowania przepływu danych, należy zaplanować przepływ danych w celu wykonania zgodnie z harmonogramem w ramach potoku. Można zaplanować potok z usługi Azure Data Factory przy użyciu wyzwalaczy. Możesz też Wyzwól teraz opcję Konstruktor potoku fabryki danych platformy Azure do wykonywania pojedynczej — uruchom wykonywanie do przetestowanie przepływu danych w ramach potoku.

Podczas wykonywania potoku, będzie można monitorowania potoku i działaniami, zawartych w potoku, w tym działania przepływu danych. Kliknij ikonę w lewym panelu, interfejs użytkownika usługi Azure Data Factory. Zostanie wyświetlony ekran podobny do przedstawionego poniżej. Wyróżnione ikony umożliwi można przejść do działania w potoku, w tym działania przepływu danych.

<img src="media/data-flow/mon001.png" width="800">

Zostanie wyświetlony statystyki, w tym poziom jako inculding dobrze, czas wykonywania i stan. Identyfikator uruchomienia na poziomie działania różni się że Uruchom IDENTYFIKATORA na poziomie potoku. Identyfikator uruchomienia na poprzednim poziomie jest dla potoku. Klikając okularów zapewni szczegółowe informacje szczegółowe na wykonywanie przepływu danych.

<img src="media/data-flow/mon002.png" width="800">

Jeśli jesteś w węźle graficzny widok monitorowania, zobaczysz uproszczonej wersji tylko do wyświetlania grafu przepływu danych.

<img src="media/data-flow/mon003.png" width="800">

## <a name="view-data-flow-execution-plans"></a>Plany wykonywania przepływu danych w widoku

Gdy przepływ danych jest wykonywana w usłudze Databricks, usługi Azure Data Factory określa optymalny kod ścieżki entirity przepływu danych w oparciu. Ponadto ścieżki wykonywania może wystąpić w różnych węzłach skalowalnego w poziomie i partycji danych. W związku z tym monitorowania wykresu reprezentuje projekt przepływu, biorąc pod uwagę ścieżki wykonywania przekształceń. Po kliknięciu na poszczególnych węzłach, zobaczysz "grupowania", które reprezentują kod, który został wykonany ze sobą w klastrze. Chronometraż i liczby wyświetlanych reprezentują te grupy, w przeciwieństwie do poszczególnych kroków w projekcie.

<img src="media/data-flow/mon004.png" width="800"> 

* Po kliknięciu wolnym miejscu w oknie monitorowania statystyki w dolnym okienku zostanie wyświetlona czasu i liczby wierszy dla każdego obiektu Sink i przekształcenia, które doprowadziły do ujścia danych pochodzenie transformacji.

* Po wybraniu pojedyncze przekształcenia, otrzymają dodatkowe opinie w panelu po prawej stronie, pokazujący partycji statystyki, liczba kolumn, skośność (jak to dane równomiernie między partycjami), a kurtoza (jak spikey jest danych).

* Po kliknięciu obiektu Sink w widoku węzłów zobaczysz pochodzenie kolumny. Istnieją trzy sposoby czy kolumny są zbierane przez cały przepływ danych, aby trafić w ujścia. Oto one:

  * Obliczane: Możesz użyć kolumny do przetwarzania warunkowego lub w wyrażeniu w przepływie danych, ale nie przejście do ujścia
  * Pochodne: Kolumna jest nową kolumnę, która wygenerowała w przepływie, czyli nie była obecna w źródle
  * Zamapowane: Kolumny pochodzi ze źródła i czy mapowania go do pola obiektu sink
  
## <a name="monitor-icons"></a>Ikony monitora

Ta ikona oznacza, że przekształcania danych została już zostały zbuforowane w klastrze, więc chronometrażu i ścieżki wykonywania zostały uwzględnione:

<img src="media/data-flow/mon005.png" width="800"> 

Zobaczysz również zielone koło ikony w transformacji. Reprezentują one liczbę ujścia, które dane będą przepływać do.
