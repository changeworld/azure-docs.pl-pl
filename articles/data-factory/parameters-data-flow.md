---
title: Azure Data Factory mapowanie parametrów przepływu danych
description: Dowiedz się, jak Sparametryzuj przepływ danych mapowania z potoków usługi Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: 0a1051d67bf45e96f82833ef8190008204cdc90b
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2019
ms.locfileid: "72387534"
---
# <a name="mapping-data-flow-parameters"></a>Mapowanie parametrów przepływu danych



Mapowanie przepływów danych w Azure Data Factory obsługuje używanie parametrów. Można definiować parametry wewnątrz definicji przepływu danych, których można używać w całym wyrażeniu. Wartości parametrów mogą być ustawiane przez potok wywołujący za pośrednictwem działania wykonaj przepływ danych. Dostępne są trzy opcje ustawiania wartości w wyrażeniach działania przepływu danych:

* Używanie języka wyrażeń przepływu sterowania potoku do ustawiania wartości dynamicznej
* Używanie języka wyrażeń przepływu danych do ustawiania wartości dynamicznej
* Użyj dowolnego języka wyrażeń, aby ustawić statyczną wartość literału

Użyj tej funkcji, aby zapewnić przepływ danych ogólnego przeznaczenia, elastyczne i wielokrotnego użytku. Za pomocą tych parametrów można Sparametryzuj ustawienia przepływu danych i wyrażenia.

> [!NOTE]
> Aby użyć wyrażeń przepływu sterowania potokiem, parametr przepływu danych musi być typu String.

## <a name="create-parameters-in-mapping-data-flow"></a>Utwórz parametry w mapowaniu przepływu danych

Aby dodać parametry do przepływu danych, kliknij pustą część kanwy przepływu danych, aby wyświetlić właściwości ogólne. W okienku Ustawienia zostanie wyświetlona karta o nazwie "Parameters". Kliknij przycisk "nowy", aby wygenerować nowy parametr. Dla każdego parametru należy przypisać nazwę, wybrać typ i opcjonalnie ustawić wartość domyślną.

![Utwórz parametry przepływu danych](media/data-flow/create-params.png "Utwórz parametry przepływu danych")

Parametrów można używać w dowolnym wyrażeniu przepływu danych. Parametry zaczynają się od $ i są niezmienne. Listę dostępnych parametrów można znaleźć w ramach konstruktora wyrażeń na karcie "parametry".

![Wyrażenie parametru przepływu danych](media/data-flow/parameter-expression.png "Wyrażenie parametru przepływu danych")

## <a name="use-parameters-in-your-data-flow"></a>Używanie parametrów w przepływie danych

* Można użyć wartości parametrów wewnątrz wyrażeń transformacji. Lista parametrów znajduje się na karcie Parametry w Konstruktorze wyrażeń. ![Użyj parametrów przepływu danych](media/data-flow/params9.png "Uparametry przepływu danych SE)

* Parametry są również używane do konfigurowania wartości dynamicznych dla ustawień transformacji źródła i ujścia. Po kliknięciu wewnątrz konfigurowalnych pól zostanie wyświetlone łącze "Dodaj dynamiczny contect". Kliknięcie spowoduje przejście do konstruktora wyrażeń, w którym można użyć parametrów do używania wartości dynamicznych. ![Zawartość dynamiczna przepływu danych](media/data-flow/params6.png "Dzawartość dynamiczna przepływu usługi ATA ")

## <a name="set-mapping-data-flow-parameters-from-pipeline"></a>Ustaw mapowanie parametrów przepływu danych z potoku

Po utworzeniu przepływu danych z parametrami można wykonać je z potoku za pomocą działania wykonaj przepływ danych. Po dodaniu działania do kanwy potoku zostaną wyświetlone dostępne parametry przepływu danych na karcie "parametry" działania.

![Ustawianie parametru przepływu danych](media/data-flow/parameter-assign.png "Ustawianie parametru przepływu danych")

Jeśli typ danych parametru to ciąg, po kliknięciu pola tekstowego, aby ustawić wartości parametrów, możesz wprowadzić potok lub wyrażenie przepływu danych. W przypadku wybrania wyrażenia potoku zostanie wyświetlony panel wyrażenia potoku. Upewnij się, że dołączasz funkcje potoku wewnątrz składni interpolacji ciągu przy użyciu `'@{<expression>}'`, na przykład:

```'@{pipeline().RunId}'```

Jeśli parametr nie jest typu String, zawsze będzie wyświetlany Konstruktor wyrażeń przepływu danych. W tym miejscu możesz wprowadzić dowolne wartości wyrażenia lub literału, które pasują do typu danych parametru. Poniżej znajdują się przykłady wyrażenia przepływu danych i ciąg literału z konstruktora wyrażeń:

* ```toInteger(Role)```
* ```'this is my static literal string'```

Każdy przepływ danych mapowania może mieć dowolną kombinację parametrów potoków i przepływów danych. 

![Przykład parametrów przepływu danych](media/data-flow/parameter-example.png "Przykład parametrów przepływu danych")



## <a name="next-steps"></a>Następne kroki
* [Działanie wykonywania przepływu danych](control-flow-execute-data-flow-activity.md)
* [Wyrażenia przepływu sterowania](control-flow-expression-language-functions.md)
