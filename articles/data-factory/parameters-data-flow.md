---
title: Mapowanie parametrów do przepływu danych w usłudze Azure Data Factory
description: Dowiedz się, jak zdefiniować parametry mapowania przepływu danych z poziomu potoków fabryki danych
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: 0a7140f70db78c8511f3c4da00b2f9c11c368163
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477684"
---
# <a name="mapping-data-flow-parameters"></a>Mapowanie parametrów do przepływu danych

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Mapowanie przepływu danych w usłudze Azure Data Factory obsługuje parametrów. Można zdefiniować parametry wewnątrz definicja przepływu danych, którego można użyć w całej Twojej wyrażeń. Przez wywołującego potok za pomocą działania wykonywania przepływu danych można ustawić wartości parametru. Masz trzy opcje do ustawiania wartości przepływu danych wyrażenia działania:

* Użyj języka wyrażeń przepływu sterowania potoku, aby ustawić wartość dynamiczna
* Użyj języka wyrażeń przepływu danych, aby ustawić wartość dynamiczna
* Użyj dowolnego języka wyrażeń, aby ustawić wartość literału statycznego

Użyj tej funkcji, aby wprowadzić przepływów danych, ogólnego przeznaczenia, elastyczne i wielokrotnego użytku. Możecie ustawienia przepływu danych i wyrażeń przy użyciu tych parametrów.

> [!NOTE]
> Aby użyć wyrażeń przepływ sterowania w potoku, Twój parametr przepływu danych musi być typu string.

## <a name="create-parameters-in-mapping-data-flow"></a>Utwórz parametry w mapowanie przepływu danych

Aby dodać parametry do przepływu danych, kliknij pustą część kanwy przepływu danych, aby wyświetlić ogólne właściwości. W okienku ustawienia zostanie wyświetlona karta o nazwie "Parameters". Kliknij przycisk "Nowy", aby wygenerować nowy parametr. Dla każdego parametru należy przypisać nazwę, wybierz typ i opcjonalnie ustawić wartość domyślną.

![Utwórz przepływ danych parametry](media/data-flow/create-params.png "parametry tworzenia przepływu danych")

Parametry może być wykorzystywany w dowolne wyrażenie przepływu danych. Parametry zaczynają się od $ i są niezmienne. Zawiera listę parametrów dostępnych w Konstruktorze wyrażeń na karcie "Parameters".

![Przepływ danych wyrażenie parametru](media/data-flow/parameter-expression.png "wyrażenie parametru przepływu danych")

## <a name="use-parameters-in-your-data-flow"></a>Parametry przepływu danych

* Wewnątrz wyrażenia transformacji, można użyć wartości parametrów. Lista parametrów, na karcie parametrów można znaleźć w Konstruktorze wyrażeń. ![Korzystanie z parametrów przepływ danych](media/data-flow/params9.png "parametry przepływu danych użycia")

* Parametry są również używane do skonfigurować wartości dynamicznych dla źródła i ujścia ustawień przekształcania. Po kliknięciu wewnątrz pola można skonfigurować, zostanie wyświetlony link "Dodaj dynamiczne contect", są wyświetlane. Kliknięcie spowoduje przejście do Konstruktora wyrażeń, których można użyć parametrów można użyć wartości dynamicznych. ![Zawartość dynamiczna przepływ danych](media/data-flow/params6.png "zawartości dynamicznej przepływ danych")

## <a name="set-mapping-data-flow-parameters-from-pipeline"></a>Ustaw parametry mapowania przepływu danych z potoku

Po utworzeniu przepływu danych z parametrami, można wykonywać ją z potoku z działaniem wykonania przepływu danych. Po dodaniu działania na kanwie potoku, zostaną wyświetlone z dostępnymi danymi parametry przepływu na karcie "Parameters" tego działania.

![Ustawienie parametru danych przepływu](media/data-flow/parameter-assign.png "wartości parametrów przepływu danych")

Jeśli danego typu danych parametru jest ciągiem, po kliknięciu pola tekstowego, aby ustawić wartości parametrów, można wprowadzić potoku lub wyrażenie przepływu danych. Jeśli wyrażenie potoku, zostanie wyświetlony panel wyrażenia potoku. Upewnij się, że zawierają funkcje potoku wewnątrz przy użyciu składni interpolacji ciągu "@{<expression>}", na przykład:

```'@{pipeline().RunId}'```

Jeśli Twój parametr nie jest typu String, zawsze zobaczysz z konstruktorem wyrażeń przepływu danych. W tym miejscu można wprowadzić wyrażenie dowolnej wartości literału, którego chcesz, który jest zgodny typ danych parametru. Poniżej przedstawiono przykładowe wyrażenie przepływu danych i ciąg literału z konstruktorem wyrażeń:

* ```toInteger(Role)```
* ```'this is my static literal string'```

Każdy przepływ mapowanie danych może mieć dowolną kombinację parametry wyrażenie przepływu danych i potoku. 

![Przykładowe parametry przepływu danych](media/data-flow/parameter-example.png "przykładowe parametry przepływu danych")



## <a name="next-steps"></a>Kolejne kroki
* [Wykonywanie działań przepływu danych](control-flow-execute-data-flow-activity.md)
* [Wyrażenia przepływu sterowania](control-flow-expression-language-functions.md)
