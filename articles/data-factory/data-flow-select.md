---
title: Mapowanie Azure Data Factory Wybieranie przepływu danych
description: Mapowanie Azure Data Factory Wybieranie przepływu danych
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 974243da07a2570e851b7d44eac2556c201c2782
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2019
ms.locfileid: "68678524"
---
# <a name="mapping-data-flow-select-transformation"></a>Mapowanie wybierania przepływu danych
[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Użyj tej transformacji dla selektywnej kolumny (zmniejszając liczbę kolumn), kolumn aliasów i nazw strumieni oraz Zmień kolejność kolumn.

## <a name="how-to-use-select-transformation"></a>Jak używać transformacji SELECT
Wybranie przekształcenia pozwala na aliasowanie całego strumienia lub kolumn w tym strumieniu, przypisywanie różnych nazw (aliasów), a następnie odwoływanie się do tych nowych nazw później w przepływie danych. To przekształcenie jest przydatne w scenariuszach samosprzężenia. Sposobem implementacji samosprzężenia w przepływie danych ADF jest wykonanie strumienia, rozgałęzienie go przy użyciu "nowej gałęzi", a następnie natychmiastowe dodanie przekształcenia "Select". Ten strumień będzie teraz miał nową nazwę, której można użyć do dołączenia do oryginalnego strumienia, tworząc samosprzężenie:

![] Samosprzężenie (media/data-flow/selfjoin.png "") Samosprzężenie

Na powyższym diagramie wybór przekształceń znajduje się u góry. Jest to alias oryginalnego strumienia do "OrigSourceBatting". W wyróżnionym przekształceniu Join poniżej można zobaczyć, że używamy tego strumienia SELECT aliasu jako sprzężenia po prawej stronie, umożliwiając nam odwoływanie się do tego samego klucza zarówno w lewej & po prawej stronie sprzężenia wewnętrznego.

Opcji wybierz można także użyć jako sposobu usuwania kolumn z przepływu danych. Jeśli na przykład masz 6 kolumn zdefiniowanych w ujścia, ale chcesz tylko wybrać konkretny 3 do przekształcenia, a następnie przetworzyć przepływ do ujścia, możesz wybrać tylko te 3 przy użyciu opcji Przekształć.

![Wybierz transformację](media/data-flow/newselect1.png "Wybierz alias")

## <a name="options"></a>Opcje
* Ustawieniem domyślnym dla opcji "Select" jest uwzględnianie wszystkich kolumn przychodzących i zachowywanie tych oryginalnych nazw. Strumień można aliasować przez ustawienie nazwy przekształcenia SELECT.
* Aby określić alias poszczególnych kolumn, usuń zaznaczenie opcji "Zaznacz wszystko" i użyj mapowania kolumn u dołu.
* Wybierz pozycję Pomiń duplikaty, aby wyeliminować zduplikowane kolumny z metadanych wejściowych lub wyjściowych.

![Pomiń duplikaty](media/data-flow/select-skip-dup.png "Pomiń duplikaty")

> [!NOTE]
> Aby wyczyścić reguły mapowania, naciśnij przycisk **Resetuj** .

## <a name="mapping"></a>Mapowanie
Domyślnie wybranie przekształcenia spowoduje automatyczne zamapowanie wszystkich kolumn, które przechodzą przez wszystkie kolumny przychodzące do tej samej nazwy w danych wyjściowych. Nazwa strumienia wyjściowego ustawiona w obszarze Wybierz ustawienia spowoduje zdefiniowanie nowej nazwy aliasu dla strumienia. Jeśli zachowasz pozycję Wybierz dla opcji Automap, możesz odaliasować cały strumień ze wszystkimi kolumnami w ten sam sposób.

![Wybieranie reguł przekształcania](media/data-flow/rule2.png "Mapowanie oparte na regułach")

Jeśli chcesz, aby alias, usuwanie, zmienianie nazwy lub zmiana kolejności kolumn, musisz najpierw wyłączyć "Automap". Domyślnie zostanie wyświetlona domyślna reguła o nazwie "wszystkie kolumny wejściowe". Tę regułę można pozostawić w miejscu, jeśli zamierzasz zawsze zezwolić na mapowanie wszystkich kolumn przychodzących na taką samą nazwę w danych wyjściowych.

Jeśli jednak chcesz dodać reguły niestandardowe, kliknij pozycję "Dodaj mapowanie". Mapowanie pól umożliwi wyświetlenie listy nazw kolumn przychodzących i wychodzących na potrzeby mapowania i aliasowania. Wybierz pozycję "mapowanie oparte na regułach", aby utworzyć reguły dopasowania wzorców.

## <a name="rule-based-mapping"></a>Mapowanie oparte na regułach
Po wybraniu mapowania opartego na regułach, nastąpi naliczanie PODAJNIKa, aby oszacować pasujące wyrażenie zgodne z regułami przychodzącego wzorca i zdefiniować nazwy pól wychodzących. Możesz dodać dowolną kombinację mapowań pól i reguł. Nazwy pól są następnie generowane w czasie wykonywania przez moduł ADF na podstawie przychodzących metadanych ze źródła. Podczas debugowania można wyświetlać nazwy wygenerowanych pól i korzystać z okienka Podgląd danych.

Więcej szczegółów na temat dopasowywania do wzorca jest dostępnych w [dokumentacji wzorca kolumny](concepts-data-flow-column-pattern.md).

## <a name="next-steps"></a>Następne kroki
* Po użyciu opcji wybierz, aby zmienić nazwę, kolejność i aliasowanie kolumn Użyj [transformacji ujścia](data-flow-sink.md) , aby wyrównać dane do magazynu danych.
