---
title: Azure Service Fabric interfejsu wiersza polecenia sfctl magazynu | Dokumentacja firmy Microsoft
description: Zawiera opis poleceń interfejsu wiersza polecenia usługi sieć szkieletowa sfctl magazynu.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 05/23/2018
ms.author: bikang
ms.openlocfilehash: 39ecf568c5c41c0007b358670af755be1dd5d99e
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763242"
---
# <a name="sfctl-store"></a>sfctl store
Wykonywanie operacji poziomu podstawowego pliku magazynu obrazu klastra.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| usuwanie | Usuwa istniejące obrazu przechowywania zawartości. |
| root-info | Pobiera informacje o zawartości w katalogu głównym magazynu obrazów. |
| stat | Pobiera informacje o zawartości magazynu obrazów. |

## <a name="sfctl-store-delete"></a>Usuń magazyn sfctl
Usuwa istniejące obrazu przechowywania zawartości.

Usuwa istniejące zawartości magazynu obrazu zostanie znaleziony w ciągu danego obrazu przechowywać ścieżki względnej. To polecenie umożliwia usunięcie pakietów przekazano aplikacji po ich udostępnieniu.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --zawartości path [wymagane] | Ścieżka względna do pliku lub folderu w magazynie obrazu z elementem głównym. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-store-root-info"></a>sfctl magazynu głównego — informacje
Pobiera informacje o zawartości w katalogu głównym magazynu obrazów.

Zwraca informacje o magazynie obrazu zawartości w katalogu głównym magazynu obrazów.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-store-stat"></a>stat magazynu sfctl
Pobiera informacje o zawartości magazynu obrazów.

Zwraca informacje o zawartości magazynu obrazu w określonym contentPath. Jest contentPath względem katalogu głównego magazynu obrazów.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --zawartości path [wymagane] | Ścieżka względna do pliku lub folderu w magazynie obrazu z elementem głównym. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="next-steps"></a>Kolejne kroki
- [Instalator](service-fabric-cli.md) sieci szkieletowej usług interfejsu wiersza polecenia.
- Dowiedz się, jak używać przy użyciu interfejsu wiersza polecenia usługi sieć szkieletowa [przykładowe skrypty](/azure/service-fabric/scripts/sfctl-upgrade-application).