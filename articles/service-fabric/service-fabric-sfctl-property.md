---
title: Właściwość interfejsu wiersza polecenia sieci szkieletowej usługi Azure
description: Dowiedz się więcej o sfctl, interfejsie wiersza polecenia sieci szkieletowej usługi Azure. Zawiera listę poleceń do przechowywania i wykonywania zapytań właściwości.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: a9bd75e0b7f8bfceb50a71ca83b60ff1e7b45508
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905828"
---
# <a name="sfctl-property"></a>sfctl property
Przechowuj i wysyłaj zapytania właściwości w obszarze Nazwy sieci szkieletowej usług.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| delete | Usuwa określoną właściwość sieci szkieletowej usług. |
| get | Pobiera określoną właściwość sieci szkieletowej usług. |
| list | Pobiera informacje o wszystkich właściwościach sieci szkieletowej usług pod podmienianą nazwy. |
| umieszczanie | Tworzy lub aktualizuje właściwość sieci szkieletowej usług. |

## <a name="sfctl-property-delete"></a>usuwanie właściwości sfctl
Usuwa określoną właściwość sieci szkieletowej usług.

Usuwa określoną właściwość sieci szkieletowej usług pod daną nazwą. Właściwość musi zostać utworzona, zanim będzie można ją usunąć.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --name-id [Wymagane] | Nazwa sieci szkieletowej usług\:bez schematu URI 'fabric'. |
| --nazwa właściwości [Wymagane] | Określa nazwę właściwości, aby uzyskać. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-property-get"></a>sfctl właściwość dostać
Pobiera określoną właściwość sieci szkieletowej usług.

Pobiera określoną właściwość sieci szkieletowej usług pod daną nazwą. Spowoduje to zawsze zwrócenie zarówno wartości, jak i metadanych.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --name-id [Wymagane] | Nazwa sieci szkieletowej usług\:bez schematu URI 'fabric'. |
| --nazwa właściwości [Wymagane] | Określa nazwę właściwości, aby uzyskać. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-property-list"></a>lista właściwości sfctl
Pobiera informacje o wszystkich właściwościach sieci szkieletowej usług pod podmienianą nazwy.

Nazwa sieci szkieletowej usług może mieć jedną lub więcej nazwanych właściwości, które przechowują informacje niestandardowe. Ta operacja pobiera informacje o tych właściwościach na liście stronicowanych. Informacje obejmują nazwę, wartość i metadane dotyczące każdej z właściwości.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --name-id [Wymagane] | Nazwa sieci szkieletowej usług\:bez schematu URI 'fabric'. |
| --continuation-token | Parametr tokenu kontynuacji jest używany do uzyskania następnego zestawu wyników. Token kontynuacji z wartością niepustą jest uwzględniony w odpowiedzi interfejsu API, gdy wyniki z systemu nie mieszczą się w pojedynczej odpowiedzi. Gdy ta wartość jest przekazywana do następnego wywołania interfejsu API, interfejs API zwraca następny zestaw wyników. Jeśli nie ma żadnych dalszych wyników, token kontynuacji nie zawiera wartości. Wartość tego parametru nie powinna być zakodowana w adresie URL. |
| --include-values | Umożliwia określenie, czy mają być uwzględniane wartości zwracanych właściwości. Prawda, jeśli wartości powinny być zwracane z metadanymi; False, aby zwrócić tylko metadane właściwości. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-property-put"></a>sfctl właściwość umieścić
Tworzy lub aktualizuje właściwość sieci szkieletowej usług.

Tworzy lub aktualizuje określoną właściwość sieci szkieletowej usług pod daną nazwą.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --name-id [Wymagane] | Nazwa sieci szkieletowej usług\:bez schematu URI 'fabric'. |
| --nazwa właściwości [Wymagane] | Nazwa właściwości sieci szkieletowej usług. |
| --value [Wymagane] | Opisuje wartość właściwości sieci szkieletowej usług. Jest to ciąg JSON. <br><br> Ciąg json ma dwa pola, "Rodzaj" danych i wartość, wprowadzone jako "Dane" danych. Wartość "Rodzaj" musi być pierwszym elementem, który pojawi się w ciągu JSON i może być wartościami "Binary", "Int64", "Double", "String" lub "Guid". Wartość powinna być serialize-stanie do danego typu. Zarówno "Kind" i "Dane" wartości powinny być dostarczane jako ciągi. |
| --custom-id-type | Identyfikator typu niestandardowego właściwości. Za pomocą tej właściwości, użytkownik jest w stanie oznaczyć typ wartości właściwości. |
| --timeout -t | Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |


## <a name="next-steps"></a>Następne kroki
- [Konfigurowanie](service-fabric-cli.md) interfejsu wiersza polecenia sieci szkieletowej usług.
- Dowiedz się, jak używać interfejsu wiersza polecenia sieci szkieletowej usług przy użyciu [przykładowych skryptów.](/azure/service-fabric/scripts/sfctl-upgrade-application)