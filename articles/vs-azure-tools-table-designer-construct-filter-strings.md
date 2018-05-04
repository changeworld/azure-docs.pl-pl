---
title: Konstruowanie ciągach filtru dla projektanta tabel | Dokumentacja firmy Microsoft
description: Konstruowanie ciągach filtru dla projektanta tabel
services: visual-studio-online
author: ghogen
manager: douge
assetId: a1a10ea1-687a-4ee1-a952-6b24c2fe1a22
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 11/18/2016
ms.author: ghogen
ms.openlocfilehash: 7390e8f463d14f2e74e05263e970665a0199029a
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="constructing-filter-strings-for-the-table-designer"></a>Konstruowanie ciągach filtru dla projektanta tabel
## <a name="overview"></a>Przegląd
Można filtrować dane w tabeli platformy Azure, która jest wyświetlana w programie Visual Studio **projektanta tabel**, utworzyć ciąg filtru i wprowadzić go w polu filtru. Składnia ciągu filtru jest definiowana za pomocą usługi danych WCF i jest podobna do klauzuli SQL WHERE, ale są wysyłane do usługi tabel za pomocą żądania HTTP. **Projektanta tabel** obsługują kodowanie odpowiednie dla Ciebie, tak aby filtrować wartość żądanej właściwości, należy tylko wprowadź nazwę właściwości, operator porównania wartości kryteriów i opcjonalnie Boolean operatora w polu filtru. Nie należy do uwzględnienia opcji zapytania $filter, jak w przypadku, jeśli zostały konstruuje adres URL do badania tabeli za pomocą [dokumentacja interfejsu API REST usług magazynu](http://go.microsoft.com/fwlink/p/?LinkId=400447).

Usługi danych WCF są oparte na [Open Data Protocol](http://go.microsoft.com/fwlink/p/?LinkId=214805) (OData). Szczegółowe informacje na temat opcji zapytania filtru systemu (**$filter**), zobacz [specyfikacji Konwencji identyfikatora URI OData](http://go.microsoft.com/fwlink/p/?LinkId=214806).

## <a name="comparison-operators"></a>Operatory porównania
Obsługiwane są następujące operatory logiczne dla wszystkich typów właściwości:

| Operator logiczny | Opis | Przykład ciąg filtru |
| --- | --- | --- |
| eq |Równości |Eq Miasto "Redmond" |
| gt |Więcej niż |Cena gt 20 |
| GE |Większe niż lub równe |Cena ge 10 |
| lt |Mniej niż |Cena lt 20 |
| le |Mniejsze niż lub równe |Cena le 100 |
| ne |Nie ma wartości |Ne Miasto "Londyn" |
| i |I |Cena le 200 i cen gt 3.5 |
| lub |Lub |Cena le 3.5 lub gt cen 200 |
| nie |nie |nie isAvailable |

Podczas konstruowania ciąg filtru, ważne są następujące reguły:

* Operatory logiczne umożliwia porównanie właściwości na wartość. Należy pamiętać, że nie jest możliwe do porównania właściwości na wartość dynamiczną; po jednej stronie wyrażenia musi być stałą.
* We wszystkich częściach ciągu filtru jest rozróżniana wielkość liter.
* Wartość stała musi mieć ten sam typ danych co właściwość, aby filtr zwracał prawidłowe wyniki. Aby uzyskać szczegółowe informacje na temat obsługiwanych typów właściwości, zobacz [Omówienie modelu danych usługi Table service](http://go.microsoft.com/fwlink/p/?LinkId=400448).

## <a name="filtering-on-string-properties"></a>Filtrowanie właściwości ciągu
Podczas filtrowania na właściwości ciągów, ujmij stała ciągu w pojedynczy cudzysłów.

Poniższy przykład filtry **PartitionKey** i **RowKey** właściwości; dodatkowe niekluczowych właściwości można również dodać ciąg filtru:

    PartitionKey eq 'Partition1' and RowKey eq '00001'

Każde wyrażenie filtru można ująć w nawiasach, chociaż nie jest to wymagane:

    (PartitionKey eq 'Partition1') and (RowKey eq '00001')

Należy pamiętać, że usługa tabel nie obsługuje symboli wieloznacznych zapytań i nie są obsługiwane w projektancie tabeli albo. Można jednak wykonać Dopasowywanie przy użyciu operatorów na prefiksie żądaną prefiksów. Poniższy przykład zwraca jednostki z nazwisko właściwość rozpoczynająca się od litery "A":

    LastName ge 'A' and LastName lt 'B'

## <a name="filtering-on-numeric-properties"></a>Filtrowanie właściwości liczbowych
Aby filtrować liczbą całkowitą lub liczba zmiennoprzecinkowa, określ, ile bez znaków cudzysłowu.

W tym przykładzie zwraca wszystkie jednostki z właściwością wieku, którego wartość jest większa niż 30:

    Age gt 30

W tym przykładzie zwraca wszystkie jednostki z właściwością AmountDue, którego wartość jest mniejsza niż lub równa 100.25:

    AmountDue le 100.25

## <a name="filtering-on-boolean-properties"></a>Filtrowanie operatory logiczne
Aby filtrować wartość logiczną, określ **true** lub **false** bez znaków cudzysłowu.

Poniższy przykład zwraca wszystkie jednostki, w których właściwość IsActive ma wartość **true**:

    IsActive eq true

Można również napisać tego wyrażenia filtru bez operatora logicznego. W poniższym przykładzie usługi tabel zwrócone zostaną również wszystkie jednostki w przypadku IsActive **true**:

    IsActive

Aby przywrócić wszystkie jednostki, jeśli IsActive ma wartość false, można użyć nie operator:

    not IsActive

## <a name="filtering-on-datetime-properties"></a>Filtrowanie właściwości daty i godziny
Aby filtrować według wartości daty i godziny, określ **datetime** — słowo kluczowe, a następnie Stała daty/godziny w pojedynczy cudzysłów. Stała daty/godziny musi być w formacie UTC połączone, zgodnie z opisem w [formatowania wartości właściwości data/godzina](http://go.microsoft.com/fwlink/p/?LinkId=400449).

Poniższy przykład zwraca jednostki, w którym właściwość CustomerSince jest równa 10 lipca 2008:

    CustomerSince eq datetime'2008-07-10T00:00:00Z'
