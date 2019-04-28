---
title: Mapowanie przekształcenie agregacji przepływu danych w usłudze Azure Data Factory
description: Przekształcenie agregacji przepływu danych fabryki danych platformy Azure
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 7b488b243c0520befb6b5470598f460b5a759fed
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61467381"
---
# <a name="azure-data-factory-mapping-data-flow-aggregate-transformation"></a>Mapowanie przekształcenie agregacji przepływu danych w usłudze Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Przekształcenie agregacji jest, gdzie należy zdefiniować agregacji kolumny w strumienie danych. W Konstruktorze wyrażeń można definiowania różnych typów agregacji (czyli SUM, MIN, MAX, liczba itp.) i utworzyć nowe pole w danych wyjściowych obejmującą tych agregacji za pomocą pola opcjonalne grupowania.

![Agreguj opcje przekształcania](media/data-flow/agg.png "agregacji 1")

## <a name="group-by"></a>Grupuj według
(Opcjonalnie) Wybierz klauzuli Group by dla Twojego agregacji, a następnie użyj nazwy istniejącej kolumny lub nową nazwę. Użyj "Dodaj kolumnę" Dodaj więcej klauzul group by, a następnie kliknij pole tekstowe obok nazwy kolumny, aby uruchomić Kreatora wyrażeń, które można wybrać tylko istniejącą kolumnę, kombinacja kolumny lub wyrażenia, które grupowanie.

## <a name="the-aggregate-column-tab"></a>Na karcie kolumnę agregacji 
(Wymagane) Wybierz kartę kolumnę agregacji, aby zbudować wyrażenia agregacji. Możesz wybierz istniejącą kolumnę, aby zastąpić tę wartość za pomocą agregacji lub utworzyć nowe pole z nową nazwą do agregacji. Wyrażenie, które mają być używane podczas agregacji zostanie wprowadzony w polu po prawej stronie obok nazwy selektora kolumn. Kliknięcie tego pola tekstowego będzie otwierają kreatora wyrażeń.

![Agreguj opcje przekształcania](media/data-flow/agg2.png "agregatora")

## <a name="data-preview-in-expression-builder"></a>Podgląd danych w Konstruktor wyrażeń

W trybie debugowania Konstruktor wyrażeń nie może utworzyć podglądów danych za pomocą funkcji agregujących. Aby wyświetlić podgląd danych dla przekształceń agregacji, należy zamknąć Konstruktor wyrażeń i wyświetlić profil danych przy użyciu projektanta przepływu danych.
