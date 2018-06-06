---
title: Azure Service Fabric interfejsu wiersza polecenia - sfctl właściwości | Dokumentacja firmy Microsoft
description: Informacje dotyczące poleceń właściwości sfctl interfejsu wiersza polecenia usługi sieci szkieletowej.
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
ms.openlocfilehash: acade3d828c785af9468baa30086d3b79542f9b7
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34764015"
---
# <a name="sfctl-property"></a>Właściwość sfctl
Właściwości magazynu i zapytań w obszarze nazwy sieci szkieletowej usług.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| usuwanie | Usuwa określony właściwość sieci szkieletowej usług. |
| Pobierz | Pobiera określonej właściwości sieci szkieletowej usług. |
| lista | Pobiera informacje dla wszystkich właściwości sieci szkieletowej usług w podanej nazwie. |
| Umieść | Tworzy lub aktualizuje właściwości sieci szkieletowej usług. |

## <a name="sfctl-property-delete"></a>Usuń właściwość sfctl
Usuwa określony właściwość sieci szkieletowej usług.

Usuwa określony właściwości sieci szkieletowej usług w podanej nazwie. Właściwość musi zostać utworzony, aby można było usunąć.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — [wymagane] identyfikatora nazwy | Bez nazwy sieci szkieletowej usług "sieć szkieletowa\:" schemat identyfikatora URI. |
| --nazwy właściwości [wymagane] | Określa nazwę właściwości do pobrania. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-property-get"></a>get właściwości sfctl
Pobiera określonej właściwości sieci szkieletowej usług.

Pobiera określony właściwości sieci szkieletowej usług w podanej nazwie. Ta funkcja zawsze zwraca wartość jak metadanych.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — [wymagane] identyfikatora nazwy | Bez nazwy sieci szkieletowej usług "sieć szkieletowa\:" schemat identyfikatora URI. |
| --nazwy właściwości [wymagane] | Określa nazwę właściwości do pobrania. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-property-list"></a>Lista właściwości sfctl
Pobiera informacje dla wszystkich właściwości sieci szkieletowej usług w podanej nazwie.

Nazwa sieci szkieletowej usług może mieć co najmniej jednej właściwości o nazwie, które przechowywania informacji niestandardowych. Ta operacja pobiera informacji o tych właściwościach liście stronicowanej. Informacje obejmują nazwę, wartość oraz metadane dotyczące każdej z właściwości.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — [wymagane] identyfikatora nazwy | Bez nazwy sieci szkieletowej usług "sieć szkieletowa\:" schemat identyfikatora URI. |
| --token kontynuacji | Parametr token kontynuacji służy do uzyskiwania następnej zestaw wyników. Token kontynuacji z niepustą wartość jest uwzględniana w odpowiedzi interfejsu API wyników z systemu nie mieszczą się w jednej odpowiedzi. Jeśli ta wartość jest przekazywany do następnego wywołania interfejsu API interfejsu API zwraca następny zestaw wyników. Jeśli nie są dalsze wyniki, token kontynuacji nie zawiera wartości. Wartość tego parametru nie powinny być zakodowane w adresie URL. |
| --zawierają wartości | Umożliwia określenie, czy dołączać wartości właściwości zwracane. Wartość true, jeśli wartości powinny być zwracane z metadanymi; Wartość FAŁSZ, aby zwrócić tylko metadane właściwości. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-property-put"></a>put właściwość sfctl
Tworzy lub aktualizuje właściwości sieci szkieletowej usług.

Tworzy lub aktualizuje określonej właściwości sieci szkieletowej usług w podanej nazwie.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — [wymagane] identyfikatora nazwy | Bez nazwy sieci szkieletowej usług "sieć szkieletowa\:" schemat identyfikatora URI. |
| --nazwy właściwości [wymagane] | Nazwa właściwości sieci szkieletowej usług. |
| — Wartość [wymagane] | W tym artykule opisano wartość właściwości sieci szkieletowej usług. Jest to ciąg JSON. <br><br> Ciąg json ma dwa pola "Kind" dane, a "Wartość" dane. Wartość "Kind" musi być pierwszym elementem pojawią się w ciągu JSON i może mieć wartości "Binary", "Int64", "Double", "String" lub "Guid". Wartość powinna można serializować-danego typu. Należy podać zarówno "Kind" i "Dane" wartości jako ciągi. |
| --niestandardowy identyfikator typu | Niestandardowe właściwości wpisz identyfikator. Za pomocą tej właściwości, użytkownik będzie mógł tagu typ wartości właściwości. |
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
- [Konfigurowanie](service-fabric-cli.md) interfejsu wiersza polecenia usługi sieci szkieletowej.
- Dowiedz się, jak używać przy użyciu interfejsu wiersza polecenia usługi sieć szkieletowa [przykładowe skrypty](/azure/service-fabric/scripts/sfctl-upgrade-application).