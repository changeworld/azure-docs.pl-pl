---
title: Konwersja modelu
description: Opisuje proces konwertowania modelu do renderowania
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 135c58a665779ffaad8750ffe618bdbe38639b66
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681495"
---
# <a name="convert-models"></a>Konwertowanie modeli

Renderowanie zdalne platformy Azure umożliwia renderowanie bardzo złożonych modeli. Aby osiągnąć maksymalną wydajność, dane muszą być wstępnie przetworzone, aby były w optymalnym formacie. W zależności od ilości danych ten krok może trochę potrwać. Byłoby niepraktyczne, gdyby ten czas został spędzony podczas ładowania modelu. Ponadto byłoby marnotrawstwem powtórzyć ten proces dla wielu sesji. Z tych powodów usługa ARR zapewnia dedykowaną *usługę konwersji,* którą można uruchomić z wyprzedzeniem.
Po konwersji model można załadować z konta usługi Azure Storage.

## <a name="supported-source-formats"></a>Obsługiwane formaty źródłowe

Usługa konwersji obsługuje następujące formaty:

- **FBX** (wersja 2011 i powyżej)
- **GLTF** (wersja 2.x)
- **GLB** (wersja 2.x)

Istnieją niewielkie różnice między formatami w odniesieniu do konwersji właściwości materiału, jak wymieniono w [mapowaniu materiałów rozdziałów dla formatów modelu](../../reference/material-mapping.md).

## <a name="the-conversion-process"></a>Proces konwersji

1. [Przygotuj dwa kontenery usługi Azure Blob Storage:](blob-storage.md)jeden dla wprowadzania danych, jeden dla danych wyjściowych
1. Przekaż model do kontenera wejściowego (opcjonalnie pod ścieżką podrzędną)
1. Wyzwalanie procesu konwersji za pomocą [interfejsu API REST konwersji modelu](conversion-rest-api.md)
1. Sonduj usługę pod kątem postępu konwersji
1. Po zakończeniu załaduj model
    - z połączonego konta magazynu (zobacz kroki "Łączenie kont magazynu" w witrynie [Tworzenie konta w](../create-an-account.md#link-storage-accounts) celu połączenia konta magazynu)
    - lub poprzez dostarczenie *sygnatury dostępu współdzielonego (SAS).*

Wszystkie dane modelu (dane wejściowe i wyjściowe) są przechowywane w magazynie obiektów blob dostarczonych przez użytkownika. Renderowanie zdalne platformy Azure zapewnia pełną kontrolę nad zarządzaniem zasobami.

## <a name="conversion-parameters"></a>Parametry konwersji

Aby zapoznać się z różnymi opcjami konwersji, zobacz [ten rozdział](configure-model-conversion.md).

## <a name="examples"></a>Przykłady

- [Szybki start: Konwertowanie modelu do renderowania](../../quickstarts/convert-model.md) jest wprowadzenie krok po kroku, jak konwertować model.
- [Przykładowe skrypty programu PowerShell,](../../samples/powershell-example-scripts.md)które pokazują użycie usługi konwersji, można znaleźć w [repozytorium próbek ARR](https://github.com/Azure/azure-remote-rendering) w folderze *Skrypty.*

## <a name="next-steps"></a>Następne kroki

- [Używanie usługi Azure Blob Storage do konwersji modelu](blob-storage.md)
- [Interfejs API REST konwersji modelu](conversion-rest-api.md)
- [Konfigurowanie konwersji modelu](configure-model-conversion.md)
- [Mapowanie materiałów dla formatów modeli](../../reference/material-mapping.md)
