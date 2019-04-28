---
title: Zbadaj przepływ danych mapowania fabryki danych platformy Azure i danych w wersji zapoznawczej
description: Azure fabryki mapowanie danych przepływ danych ma okienko, w którym przedstawiono metadanych przepływu danych (Sprawdź) i danych w wersji zapoznawczej w trybie debugowania (danych w wersji zapoznawczej)
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 47cde50e0874f0f73523925b6d1b2f8ee4abaea9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61284118"
---
# <a name="azure-data-factory-mapping-data-flow-transformation-inspect-tab"></a>Przekształcanie przepływu danych mapowania fabryki danych platformy Azure Sprawdź kartę

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Sprawdź okienko](media/data-flow/agg3.png "Sprawdź okienko")

Sprawdź okienko zapewnia wgląd w metadanych strumień danych, który jest przekształcenie. Można wyświetlić liczby kolumn, zmienić kolumny, kolumny dodane, typy danych, kolejność kolumn i odwołania do kolumn. "Sprawdź" to jest widok tylko do odczytu metadanych. Nie musisz mieć włączony zobaczenie metadate w okienku sprawdź tryb debugowania.

W przypadku zmiany kształtu danych przy użyciu transformacji, zostanie wyświetlony metadanych zmiany za pomocą okienka inspekcji. Jeśli w swojej transformacji źródłowej nie ma zdefiniowanego schematu, metadanych nie będą widoczne w okienku inspekcji. Brak metadanych jest często używany w scenariuszach dryfu schematu.

![Podgląd danych](media/data-flow/datapreview.png "danych (wersja zapoznawcza)")

Podgląd danych to okienko, który udostępnia widok tylko do odczytu danych, ponieważ jest on przekształcany. Możesz wyświetlić dane wyjściowe transformacji i wyrażenia, aby zweryfikować przepływ danych. Konieczne jest posiadanie przesuwania, aby wyświetlić podgląd danych tryb debugowania. Po kliknięciu kolumny w siatce danych (wersja zapoznawcza) zostanie wyświetlony kolejnych panelu po prawej stronie. Panel wyskakujący pokaże profilu statystyk dotyczących poszczególnych kolumn, które możesz wybrać.

![Dane wykresu w wersji zapoznawczej](media/data-flow/chart.png "danych wykresu w wersji zapoznawczej")

