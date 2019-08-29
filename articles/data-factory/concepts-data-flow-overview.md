---
title: Azure Data Factory mapowanie przepływu danych — omówienie
description: Omówienie mapowania przepływów danych w Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 6f4c124c59584c8538d85ac61650661ae559a77b
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70123922"
---
# <a name="what-are-mapping-data-flows"></a>Co to jest mapowanie przepływów danych?

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Mapowanie przepływów danych to wizualnie zaprojektowana transformacja danych w Azure Data Factory. Przepływy danych umożliwiają inżynierom danych Tworzenie logiki transformacji danych graficznych bez pisania kodu. Wyniki przepływów danych są wykonywane jako działania w ramach potoków Azure Data Factory przy użyciu klastrów Azure Databricks skalowanych w poziomie.

Zamiarem przepływu danych Azure Data Factory jest zapewnienie w pełni wizualizacji, bez konieczności kodowania. Przepływy danych będą wykonywane na własnym klastrze wykonawczym na potrzeby przetwarzania danych skalowanych w poziomie. Azure Data Factory obsługuje wszystkie tłumaczenia kodu, optymalizację ścieżki i wykonywanie zadań przepływu danych.

Zacznij od utworzenia przepływów danych w trybie debugowania, aby umożliwić interaktywną weryfikację logiki transformacji. Następnie Dodaj do potoku działanie przepływu danych w celu wykonania i przetestowania przepływu danych w debugowaniu potoku lub użyj "Wyzwól teraz" w potoku, aby przetestować przepływ danych z działania potoku.

Następnie można zaplanować i monitorować działania przepływu danych przy użyciu potoków Azure Data Factory, które wykonują działanie przepływu danych.

Przełącznik tryb debugowania na powierzchni projektowej przepływu danych umożliwia interaktywne Kompilowanie transformacji danych. Tryb debugowania zapewnia przygotowanie danych i środowisko podglądu danych dla konstruowania przepływu danych.

## <a name="begin-building-your-data-flow-logical-graph"></a>Rozpocznij budowanie wykresu logicznego przepływu danych

Rozpocznij Kompilowanie przepływów danych przy użyciu znaku + w obszarze zasoby fabryki, aby utworzyć nowy przepływ danych.

![Nowy przepływ danych](media/data-flow/newdataflow2.png "Nowy przepływ danych")

Zacznij od skonfigurowania transformacji źródłowej, a następnie Dodaj transformację danych do każdego kolejnego kroku przy użyciu znaku +. Podczas tworzenia grafu logicznego można przełączać się między trybami grafu i konfiguracji przy użyciu przycisku "Pokaż graf" i "Ukryj wykres".

![Pokaż wykres](media/data-flow/showg.png "Pokaż wykres")

## <a name="configure-transformation-logic"></a>Konfigurowanie logiki transformacji

![Ukryj wykres](media/data-flow/hideg.png "Ukryj wykres")

Ukrycie wykresu pozwoli na przechodzenie przez węzły transformacji w późniejszym czasie.

![Przejdź](media/data-flow/showhide.png "Przejdź")

## <a name="next-steps"></a>Następne kroki

* [Rozpocznij od transformacji źródłowej](data-flow-source.md)
