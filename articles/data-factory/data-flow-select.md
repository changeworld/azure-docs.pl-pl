---
title: Mapowanie Azure Data Factory Wybieranie przepływu danych
description: Mapowanie Azure Data Factory Wybieranie przepływu danych
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 15c74637a2dc42ec44f582878b5505d94637cd7b
ms.sourcegitcommit: da0a8676b3c5283fddcd94cdd9044c3b99815046
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2019
ms.locfileid: "68314215"
---
# <a name="azure-data-factory-mapping-data-flow-select-transformation"></a>Mapowanie Azure Data Factory Wybieranie przepływu danych
[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Użyj tej transformacji dla selektywnej kolumny (zmniejszając liczbę kolumn), kolumn aliasów i nazw strumieni oraz Zmień kolejność kolumn.

## <a name="how-to-use-select-transformation"></a>Jak używać transformacji SELECT
Wybranie przekształcenia pozwala na aliasowanie całego strumienia lub kolumn w tym strumieniu, przypisywanie różnych nazw (aliasów), a następnie odwoływanie się do tych nowych nazw później w przepływie danych. To przekształcenie jest przydatne w scenariuszach samosprzężenia. Sposobem implementacji samosprzężenia w przepływie danych ADF jest wykonanie strumienia, rozgałęzienie go przy użyciu "nowej gałęzi", a następnie natychmiastowe dodanie przekształcenia "Select". Ten strumień będzie teraz miał nową nazwę, której można użyć do dołączenia do oryginalnego strumienia, tworząc samosprzężenie:

![] Samosprzężenie (media/data-flow/selfjoin.png "") Samosprzężenie

Na powyższym diagramie wybór przekształceń znajduje się u góry. Jest to alias oryginalnego strumienia do "OrigSourceBatting". W wyróżnionym przekształceniu Join poniżej można zobaczyć, że używamy tego strumienia SELECT aliasu jako sprzężenia po prawej stronie, umożliwiając nam odwoływanie się do tego samego klucza zarówno w lewej & po prawej stronie sprzężenia wewnętrznego.

Opcji wybierz można także użyć jako sposobu usuwania kolumn z przepływu danych. Jeśli na przykład masz 6 kolumn zdefiniowanych w ujścia, ale chcesz tylko wybrać konkretny 3 do przekształcenia, a następnie przetworzyć przepływ do ujścia, możesz wybrać tylko te 3 przy użyciu opcji Przekształć.

> [!NOTE]
> Aby wybierać tylko określone kolumny, należy wyłączyć opcję "Zaznacz wszystko"

![Wybierz transformację](media/data-flow/select001.png "Wybierz alias")

## <a name="options"></a>Opcje
* Ustawieniem domyślnym dla opcji "Select" jest uwzględnianie wszystkich kolumn przychodzących i zachowywanie tych oryginalnych nazw. Strumień można aliasować przez ustawienie nazwy przekształcenia SELECT.
* Aby określić alias poszczególnych kolumn, usuń zaznaczenie opcji "Zaznacz wszystko" i użyj mapowania kolumn u dołu.
* Wybierz pozycję Pomiń duplikaty, aby wyeliminować zduplikowane kolumny z metadanych wejściowych lub wyjściowych.

![Pomiń duplikaty](media/data-flow/select-skip-dup.png "Pomiń duplikaty")

## <a name="next-steps"></a>Następne kroki
* Po użyciu opcji wybierz, aby zmienić nazwę, kolejność i aliasowanie kolumn Użyj [transformacji ujścia](data-flow-sink.md) , aby wyrównać dane do magazynu danych.
