---
title: Interfejs wiersza polecenia platformy Azure Service Fabric — Właściwość sfctl | Microsoft Docs
description: Zawiera Service Fabric opis poleceń właściwości sfctl interfejsu wiersza polecenia.
services: service-fabric
documentationcenter: na
author: jeffj6123
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 368dad54c611e4532b46f11669bcf8e363b9a740
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901071"
---
# <a name="sfctl-property"></a>sfctl property
Właściwości magazynu i zapytania w obszarze nazwy Service Fabric.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| delete | Usuwa określoną właściwość Service Fabric. |
| Pobierz | Pobiera określoną właściwość Service Fabric. |
| list | Pobiera informacje o wszystkich Service Fabric właściwościach o podanej nazwie. |
| Ubrani | Tworzy lub aktualizuje właściwość Service Fabric. |

## <a name="sfctl-property-delete"></a>sfctl — usuwanie właściwości
Usuwa określoną właściwość Service Fabric.

Usuwa określoną właściwość Service Fabric pod podaną nazwą. Aby można było usunąć właściwość, należy ją utworzyć.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Name-ID [wymagane] | Nazwa Service Fabric bez schematu identyfikatora URI "Sieć szkieletowa\:". |
| --Property-Name [wymagane] | Określa nazwę właściwości do pobrania. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: JSON, jsonc, Table, TSV.  Domyślny\: JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać więcej informacji i przykładów, zobacz http\://jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-property-get"></a>Pobieranie właściwości sfctl
Pobiera określoną właściwość Service Fabric.

Pobiera określoną właściwość Service Fabric pod podaną nazwą. Zawsze zwróci zarówno wartość, jak i metadane.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Name-ID [wymagane] | Nazwa Service Fabric bez schematu identyfikatora URI "Sieć szkieletowa\:". |
| --Property-Name [wymagane] | Określa nazwę właściwości do pobrania. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: JSON, jsonc, Table, TSV.  Domyślny\: JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać więcej informacji i przykładów, zobacz http\://jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-property-list"></a>Lista właściwości sfctl
Pobiera informacje o wszystkich Service Fabric właściwościach o podanej nazwie.

Nazwa Service Fabric może mieć jedną lub więcej nazwanych właściwości, które przechowują informacje niestandardowe. Ta operacja pobiera informacje o tych właściwościach na liście stronicowanej. Informacje obejmują nazwę, wartość i metadane dotyczące poszczególnych właściwości.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Name-ID [wymagane] | Nazwa Service Fabric bez schematu identyfikatora URI "Sieć szkieletowa\:". |
| --Kontynuacja — token | Parametr tokenu kontynuacji służy do uzyskiwania następnego zestawu wyników. Token kontynuacji z niepustą wartością jest dołączany do odpowiedzi interfejsu API, gdy wyniki z systemu nie mieszczą się w pojedynczej odpowiedzi. Gdy ta wartość jest przenoszona do następnego wywołania interfejsu API, interfejs API zwraca następny zestaw wyników. Jeśli nie ma żadnych dalszych wyników, token kontynuacji nie zawiera wartości. Wartość tego parametru nie powinna być zakodowana w adresie URL. |
| --include-Values | Umożliwia określenie, czy mają zostać uwzględnione wartości zwracanych właściwości. Prawda, jeśli wartości powinny być zwracane z metadanymi; Zwraca wartość false, aby zwrócić tylko metadane właściwości. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: JSON, jsonc, Table, TSV.  Domyślny\: JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać więcej informacji i przykładów, zobacz http\://jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-property-put"></a>sfctl Właściwość Put
Tworzy lub aktualizuje właściwość Service Fabric.

Tworzy lub aktualizuje określoną właściwość Service Fabric pod daną nazwą.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Name-ID [wymagane] | Nazwa Service Fabric bez schematu identyfikatora URI "Sieć szkieletowa\:". |
| --Property-Name [wymagane] | Nazwa właściwości Service Fabric. |
| --wartość [wymagane] | Opisuje Service Fabric wartość właściwości. Jest to ciąg JSON. <br><br> Ciąg JSON ma dwa pola, typ "dane" i "wartość" danych. Wartość "Kind" musi być pierwszym elementem wyświetlanym w ciągu JSON i może mieć wartości "Binary", "Int64", "Double", "String" lub "GUID". Wartość powinna być serializowana dla danego typu. Wartości "Kind" i "Data" powinny być podane jako ciągi. |
| --typ niestandardowy-ID | Identyfikator typu niestandardowego właściwości. Korzystając z tej właściwości, użytkownik może oznaczyć typ wartości właściwości. |
| --timeout-t | Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: JSON, jsonc, Table, TSV.  Domyślny\: JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać więcej informacji i przykładów, zobacz http\://jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |


## <a name="next-steps"></a>Następne kroki
- [Skonfiguruj](service-fabric-cli.md) interfejs wiersza polecenia Service Fabric.
- Dowiedz się, jak używać interfejsu wiersza polecenia Service Fabric przy użyciu [przykładowych skryptów](/azure/service-fabric/scripts/sfctl-upgrade-application).