---
title: Parametry przepływu danych usługi Azure Data Factory mapowania
description: Dowiedz się, jak zdefiniować parametry mapowania przepływu danych z poziomu potoków fabryki danych
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: af5f421cc3802f3a7ad44bb294f5066c32569f8b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082887"
---
# <a name="mapping-data-flow-parameters"></a>Parametry przepływu danych mapowania

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Mapowanie danych przepływów w usłudze data factory obsługuje parametrów. Można zdefiniować parametry wewnątrz definicja przepływu danych, którego można użyć w całej Twojej wyrażeń. Następnie można ustawić parametrów przez wywołującego potok za pomocą działania wykonywania przepływu danych. Masz trzy opcje służące do ustawiania wartości przepływu danych wyrażenia działania:

* Użyj języka wyrażeń przepływu sterowania potoku, aby ustawić wartość dynamiczna
* Użyj języka wyrażeń przepływu danych, aby ustawić wartość dynamiczna
* Użyj dowolnego języka wyrażeń, aby ustawić wartość literału statycznego

Użyj tej funkcji, aby wprowadzić przepływów danych, ogólnego przeznaczenia, elastyczne i wielokrotnego użytku. Możecie ustawienia przepływu danych i wyrażeń przy użyciu tych parametrów.

> [!NOTE]
> Aby użyć wyrażeń przepływ sterowania w potoku, Twój parametr przepływu danych musi być typu string.

* Dodaj przepływ danych wykonaj działania na kanwę potoku.
* Jeśli Twój przepływ danych ma parametry, zobaczysz listę parametrów dostępnych w parametrach tab.* *, kliknij pole tekstowe obok każdego parametru, aby wprowadzić wartość parametru.
* Można utworzyć wyrażenie parametru za pomocą języka wyrażeń przepływu sterowania potoku lub wyrażenia przepływu danych.

![Przepływ danych parametry 3](media/data-flow/params3.png "parametry 3 przepływ danych")

## <a name="create-parameters-in-data-flow"></a>Parametry tworzenia przepływu danych

![Parametry 1 przepływ danych](media/data-flow/params1.png "parametry 1 przepływ danych")

Aby dodać parametry do przepływu danych, kliknij pustą część kanwy przepływu danych, aby wyświetlić ogólne właściwości. W okienku ustawienia zostanie wyświetlona karta nazywane parametrami. Kliknij przycisk Nowy, aby wygenerować nowe parametry, które można następnie ustawić z potoku przekazywania wartości do przepływu danych. Wprowadź nazwę parametru, a następnie wybierz typ danych dla każdego parametru.

Wewnątrz wyrażenia przepływu danych możesz użyć parametrów przy użyciu wartości z potoku. Parametry zaczynają się od $ i są niezmienne. Będzie można również znaleźć listę dostępnych parametry wewnątrz Konstruktor wyrażeń na karcie Parametry. Mimo że nie przypiszesz nowe wartości parametrów, można użyć tych wartości w ramach Twojej wyrażenia.

![Przepływ danych parametry 2](media/data-flow/params2.png "2 parametry przepływu danych")

## <a name="set-data-flow-parameters-from-pipeline"></a>Parametry przepływu danych zestawu z potoku

Po utworzeniu przepływu danych z parametrami, można teraz wykonywać tego przepływu danych z potoku z działaniem wykonania przepływu danych. Po dodaniu działania do Twojego obszaru roboczego projektowania potoku zostanie wyświetlona przy użyciu dostępnych danych parametry przepływu w parametrach działania, ustawienie karty.

![Język wyrażeń parametry przepływ danych](media/data-flow/params4.png "język wyrażeń parametry przepływu danych")

Po kliknięciu w polu tekstowym, aby wypełnianie wartości parametrów, zostaną wyświetlone Konstruktor wyrażeń przepływu danych. W tym miejscu można wprowadzić wyrażenie dowolnej wartości literału ma pasujący typ danych parametru. Poniżej przedstawiono przykładowe wyrażenie przepływu danych i ciąg literału z konstruktorem wyrażeń:

* ```toInteger(Role)```
* ```'this is my static literal string'```

Jeśli danego typu danych parametru jest ciąg, można wybrać wprowadzenia potoku lub wyrażenie przepływu danych. Jeśli wyrażenie potok, możesz zamiast tego zobaczy panel wyrażenia potoku. Upewnij się, że zawierają funkcje potoku wewnątrz przy użyciu składni interpolacji ciągu "@{<expression>}", na przykład:

```'@{pipeline().RunId}'```

![Przykładowe parametry przepływu danych](media/data-flow/params5.png "przykładowe parametry przepływu danych")

## <a name="next-steps"></a>Kolejne kroki

* [Wykonywanie działań przepływu danych](control-flow-execute-data-flow-activity.md)
* [Wyrażenia przepływu sterowania](control-flow-expression-language-functions.md)
