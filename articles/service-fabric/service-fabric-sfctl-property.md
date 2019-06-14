---
title: Azure usługi Service Fabric interfejsu wiersza polecenia sfctl właściwość | Dokumentacja firmy Microsoft
description: Opisuje właściwości polecenia sfctl interfejsu wiersza polecenia usługi Service Fabric.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 54cb9f604e9d1b817947990e657390387df6c881
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60556598"
---
# <a name="sfctl-property"></a>sfctl property
Właściwości Store i zapytania w obszarze nazw usługi Service Fabric.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| delete | Usuwa określoną właściwość usługi Service Fabric. |
| Pobierz | Pobiera określoną właściwość usługi Service Fabric. |
| list | Pobiera informacje na temat wszystkich właściwości usługi Service Fabric w ramach określonej nazwie. |
| Umieść | Tworzy lub aktualizuje właściwości usługi Service Fabric. |

## <a name="sfctl-property-delete"></a>Usuń właściwość sfctl
Usuwa określoną właściwość usługi Service Fabric.

Usuwa określoną właściwość usługi Service Fabric w ramach określonej nazwie. Właściwość musi zostać utworzona, aby można było usunąć.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator nazwy [wymagane] | Nazwa usługi Service Fabric, bez "Service fabric\:" schemat identyfikatora URI. |
| — [wymagane] Nazwa właściwości | Określa nazwę właściwości do pobrania. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-property-get"></a>Pobierz właściwości sfctl
Pobiera określoną właściwość usługi Service Fabric.

Pobiera określoną właściwość usługi Service Fabric w ramach określonej nazwie. Ta funkcja zawsze zwraca wartość i metadanych.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator nazwy [wymagane] | Nazwa usługi Service Fabric, bez "Service fabric\:" schemat identyfikatora URI. |
| — [wymagane] Nazwa właściwości | Określa nazwę właściwości do pobrania. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-property-list"></a>Lista właściwości sfctl
Pobiera informacje na temat wszystkich właściwości usługi Service Fabric w ramach określonej nazwie.

Nazwa usługi Service Fabric może mieć co najmniej jeden nazwane właściwości, które przechowują informacje o niestandardowych. Ta operacja pobiera informacje o tych właściwościach liście stronicowanej. Informacje obejmują nazwę, wartość oraz metadane dotyczące każdej właściwości.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator nazwy [wymagane] | Nazwa usługi Service Fabric, bez "Service fabric\:" schemat identyfikatora URI. |
| --token kontynuacji | Parametr tokenu kontynuacji służy do uzyskania następny zestaw wyników. Token kontynuacji o wartości niepuste znajduje się w odpowiedzi interfejsu API, gdy wyniki z systemu nie mieszczą się w jednej odpowiedzi. Jeśli ta wartość jest przekazywana do następnego wywołania interfejsu API, interfejs API zwraca następny zestaw wyników. Jeśli nie istnieją żadne dalsze wyniki, token kontynuacji nie zawiera wartości. Wartość tego parametru nie powinny być zakodowane w adresie URL. |
| --include-values | Umożliwia określenie czy mają zostać dołączone wartości właściwości, zwracany. Wartość true, jeśli ma zostać zwrócone wartości z metadanymi; Wartość FAŁSZ, aby zwrócić tylko metadane właściwości. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-property-put"></a>Interfejs sfctl właściwość put
Tworzy lub aktualizuje właściwości usługi Service Fabric.

Tworzy lub aktualizuje określoną właściwość usługi Service Fabric w ramach określonej nazwie.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator nazwy [wymagane] | Nazwa usługi Service Fabric, bez "Service fabric\:" schemat identyfikatora URI. |
| — [wymagane] Nazwa właściwości | Nazwa właściwości usługi Service Fabric. |
| --Wartość [wymagane] | W tym artykule opisano wartość właściwości usługi Service Fabric. Jest to ciąg JSON. <br><br> Ciąg json ma dwa pola: "Kind" dane i "Value" dane. Wartość "Kind" musi być pierwszym elementem pojawią się w ciągu JSON i może mieć wartości "Binarny", "Int64", "Double", "String" lub "Guid". Wartość powinna być serializacji — możliwość danym typami. Należy podać wartości "Kind" i "Dane" jako ciągi. |
| --custom-id-type | Identyfikator niestandardowego typu właściwości. Używanie tej właściwości, użytkownik będzie mógł tag typu wartości właściwości. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |


## <a name="next-steps"></a>Kolejne kroki
- [Konfigurowanie](service-fabric-cli.md) interfejsu wiersza polecenia usługi Service Fabric.
- Dowiedz się, jak używać przy użyciu interfejsu wiersza polecenia usługi Service Fabric [przykładowe skrypty](/azure/service-fabric/scripts/sfctl-upgrade-application).