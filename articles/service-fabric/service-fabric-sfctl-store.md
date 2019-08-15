---
title: Interfejs wiersza polecenia platformy Azure Service Fabric — magazyn sfctl | Microsoft Docs
description: Zawiera Service Fabric opis poleceń magazynu sfctl interfejsu wiersza polecenia.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: ccd9e8323f6e0de7b81c7600e7828e4858c51201
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035866"
---
# <a name="sfctl-store"></a>sfctl store
Wykonaj podstawowe operacje na poziomie plików w magazynie obrazów klastra.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| usuwanie | Usuwa istniejącą zawartość magazynu obrazów. |
| root-info | Pobiera informacje o zawartości w katalogu głównym magazynu obrazów. |
| Określ | Pobiera informacje o zawartości magazynu obrazu. |

## <a name="sfctl-store-delete"></a>Usuwanie magazynu sfctl
Usuwa istniejącą zawartość magazynu obrazów.

Usuwa istniejącą zawartość magazynu obrazu znajdującą się w danej ścieżce względnej magazynu obrazu. To polecenie służy do usuwania przekazanych pakietów aplikacji po ich udostępnieniu.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Content-Path [wymagane] | Ścieżka względna do pliku lub folderu w magazynie obrazów ze swojego katalogu głównego. |
| --timeout-t | Limit czasu serwera (w sekundach).  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, Table, TSV.  Domyślny\: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać\:więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-store-root-info"></a>Katalog główny sklepu sfctl — informacje
Pobiera informacje o zawartości w katalogu głównym magazynu obrazów.

Zwraca informacje o zawartości magazynu obrazu w katalogu głównym magazynu obrazów.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --timeout-t | Limit czasu serwera (w sekundach).  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, Table, TSV.  Domyślny\: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać\:więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-store-stat"></a>Statystyka sklepu sfctl
Pobiera informacje o zawartości magazynu obrazu.

Zwraca informacje o zawartości magazynu obrazu w określonym contentPath. ContentPath jest względem katalogu głównego magazynu obrazów.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Content-Path [wymagane] | Ścieżka względna do pliku lub folderu w magazynie obrazów ze swojego katalogu głównego. |
| --timeout-t | Limit czasu serwera (w sekundach).  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, Table, TSV.  Domyślny\: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać\:więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |


## <a name="next-steps"></a>Następne kroki
- [Skonfiguruj](service-fabric-cli.md) interfejs wiersza polecenia Service Fabric.
- Dowiedz się, jak używać interfejsu wiersza polecenia Service Fabric przy użyciu [przykładowych skryptów](/azure/service-fabric/scripts/sfctl-upgrade-application).