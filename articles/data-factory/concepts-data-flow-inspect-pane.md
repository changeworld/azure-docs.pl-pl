---
title: Zbadaj przepływ danych mapowania fabryki danych platformy Azure i danych w wersji zapoznawczej
description: Azure fabryki mapowanie danych przepływ danych ma okienko, w którym przedstawiono metadanych przepływu danych (Sprawdź) i danych w wersji zapoznawczej w trybie debugowania (danych w wersji zapoznawczej)
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: e7d03ecd8ea4aecf1e0cfdd02d3b9bc5300abe6b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56213406"
---
# <a name="azure-data-factory-mapping-data-flow-transformation-inspect-tab"></a>Przekształcanie przepływu danych mapowania fabryki danych platformy Azure Sprawdź kartę

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Sprawdź okienko](media/data-flow/agg3.png "Sprawdź okienko")

Sprawdź okienko zapewnia wgląd w metadanych strumień danych, który jest przekształcenie. Można wyświetlić liczby kolumn, zmienić kolumny, kolumny dodane, typy danych, kolejność kolumn i odwołania do kolumn. "Sprawdź" to jest widok tylko do odczytu metadanych. Nie musisz mieć włączony zobaczenie metadate w okienku sprawdź tryb debugowania.

W przypadku zmiany kształtu danych przy użyciu transformacji, zostanie wyświetlony metadanych zmiany za pomocą okienka inspekcji. Jeśli w swojej transformacji źródłowej nie ma zdefiniowanego schematu, metadanych nie będą widoczne w okienku inspekcji. Brak metadanych jest często używany w scenariuszach dryfu schematu.

![Podgląd danych](media/data-flow/datapreview.png "danych (wersja zapoznawcza)")

Podgląd danych to okienko, który udostępnia widok tylko do odczytu danych, ponieważ jest on przekształcany. Możesz wyświetlić dane wyjściowe transformacji i wyrażenia, aby zweryfikować przepływ danych. Konieczne jest posiadanie przesuwania, aby wyświetlić podgląd danych tryb debugowania. Po kliknięciu kolumny w siatce danych (wersja zapoznawcza) zostanie wyświetlony kolejnych panelu po prawej stronie. Panel wyskakujący pokaże profilu statystyk dotyczących poszczególnych kolumn, które możesz wybrać.

![Dane wykresu w wersji zapoznawczej](media/data-flow/chart.png "danych wykresu w wersji zapoznawczej")

