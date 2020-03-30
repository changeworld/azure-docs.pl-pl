---
title: Właściwości pozyskiwania danych dla Eksploratora danych platformy Azure
description: Dowiedz się więcej o różnych właściwościach pozyskiwania danych obsługiwanych przez Eksploratora danych platformy Azure.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 48bce1bf03a0a4c8d81fff7ae54e0b22261b70f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80109578"
---
# <a name="azure-data-explorer-data-ingestion-properties"></a>Właściwości pozyskiwania danych usługi Azure Data Explorer 

Pozyskiwania danych jest procesem, w którym dane są dodawane do tabeli i jest udostępniany do kwerendy w Eksploratorze danych platformy Azure. Właściwości są dodawanye do polecenia pozyskiwania `with` po słowie kluczowym.

## <a name="ingestion-properties"></a>Właściwości pozyskiwania

W poniższej tabeli wymieniono właściwości obsługiwane przez Eksploratora danych platformy Azure, opisano je i przedstawiono przykłady: 

|Właściwość              |Opis                                              |Przykład                                             |
|----------------------|---------------------------------------------------------|----------------------------------------------------|
|`ingestionMapping`    |Wartość ciągu wskazująca sposób mapowania danych z pliku źródłowego do rzeczywistych kolumn w tabeli. Zdefiniuj `format` wartość za pomocą odpowiedniego typu mapowania. Zobacz [mapowania danych](/azure/kusto/management/mappings).|`with (format="json", ingestionMapping = "[{\"column\":\"rownumber\", \"Properties\":{\"Path\":\"$.RowNumber\"}}, {\"column\":\"rowguid\", \"Properties\":{\"Path\":\"$.RowGuid\"}}]")`<br>`avroMapping`(przestarzałe: `csvMapping` `jsonMapping`, , ) |
|`ingestionMappingReference`|Wartość ciągu wskazująca sposób mapowania danych z pliku źródłowego do rzeczywistych kolumn w tabeli przy użyciu nazwanego obiektu zasad mapowania. Zdefiniuj `format` wartość za pomocą odpowiedniego typu mapowania. Zobacz [mapowania danych](/azure/kusto/management/mappings).|`with (format="csv", ingestionMappingReference = "Mapping1")`<br>`avroMappingReference`(przestarzałe: `csvMappingReference` `jsonMappingReference`, , )|
|`creationTime` |Wartość datetime (sformatowana jako ciąg ISO8601) do użycia w czasie tworzenia pozyskiwania zakresów danych. Jeśli nie określono, zostanie`now()`użyta bieżąca wartość ( ). Zastępowanie wartości domyślnej jest przydatne podczas pozyskiwania starszych danych, dzięki czemu zasady przechowywania zostaną zastosowane poprawnie.|`with (creationTime="2017-02-13T11:09:36.7992775Z")`|
|`extend_schema`|Wartość logiczna, która, jeśli jest określona, nakazuje polecenie rozszerzyć schemat `false`tabeli (domyślnie ). Ta opcja dotyczy `.append` tylko `.set-or-append` poleceń i poleceń. Tylko dozwolone rozszerzenia schematu mają dodatkowe kolumny dodane do tabeli na końcu.|Jeśli oryginalny schemat tabeli jest `(a:string, b:int)`, prawidłowe `(a:string, b:int, c:datetime, d:string)`rozszerzenie `(a:string, c:datetime)` schematu będzie , ale nie będzie prawidłowy|
|`folder` |W przypadku pozyskiwania poleceń [z kwerendy,](/azure/kusto/management/data-ingestion/ingest-from-query) folderu, który ma być przypisany do tabeli. Jeśli tabela już istnieje, ta właściwość zastąpi folder tabeli.|`with (folder="Tables/Temporary")`|
|`format` |Format danych (patrz [obsługiwane formaty danych](ingestion-supported-formats.md)).|`with (format="csv")`|
|`ingestIfNotExists`|Wartość ciągu, która, jeśli jest określona, uniemożliwia ponawianie się pomyślnie, `ingest-by:` jeśli tabela ma już dane oznaczone tagiem o tej samej wartości. Zapewnia to idempotentne pozyskiwania danych. Aby uzyskać więcej informacji, zobacz [ingest-by: tags](/azure/kusto/management/extents-overview#ingest-by-extent-tags).|Właściwości `with (ingestIfNotExists='["Part0001"]', tags='["ingest-by:Part0001"]')` wskazują, że jeśli dane `ingest-by:Part0001` z tagiem już istnieją, nie należy kończyć bieżącego pozyskiwania. Jeśli jeszcze nie istnieje, to nowe połknienie powinno mieć ten zestaw znaczników (w przypadku, gdy przyszłe połknienie spróbuje ponownie połknąć te same dane).|
|`ignoreFirstRecord` |Wartość logiczna, która, `true`jeśli jest ustawiona na , wskazuje, że połkowanie powinno ignorować pierwszy rekord każdego pliku. Ta właściwość jest `CSV`przydatna w przypadku plików w formatach i podobnych formatach, jeśli pierwszym rekordem w pliku są nazwy kolumn. Domyślnie `false` zakłada się.|`with (ignoreFirstRecord=false)`|
|`persistDetails` |Wartość logiczna, która, jeśli zostanie określona, wskazuje, że polecenie powinno utrwalić szczegółowe wyniki (nawet jeśli zakończy się pomyślnie), aby polecenie [.show operation details](/azure/kusto/management/operations#show-operation-details) mogło je pobrać. Wartość domyślna to `false`.|`with (persistDetails=true)`|
|`policy_ingestiontime`|Wartość logiczna, która, jeśli jest określona, opisuje, czy włączyć [zasadę czasu pozyskiwania](/azure/kusto/management/ingestiontimepolicy) w tabeli, która jest tworzona przez to polecenie. Wartość domyślna to `true`.|`with (policy_ingestiontime=false)`|
|`recreate_schema` |Wartość logiczna, która, jeśli jest określona, opisuje, czy polecenie może odtworzyć schemat tabeli. Ta właściwość dotyczy `.set-or-replace` tylko polecenia. Ta właściwość ma `extend_schema` pierwszeństwo przed właściwością, jeśli oba są ustawione.|`with (recreate_schema=true)`|
|`tags`|Lista [znaczników](/azure/kusto/management/extents-overview#extent-tagging) do skojarzenia z przyjmowanymi danymi sformatowana jako ciąg JSON |`with (tags="['Tag1', 'Tag2']")`|
|`validationPolicy`|Ciąg JSON, który wskazuje, które weryfikacje mają być uruchamiane podczas pozyskiwania. Zobacz [Połknienie danych,](/azure/kusto/management/data-ingestion/) aby uzyskać wyjaśnienie różnych opcji.| `with (validationPolicy='{"ValidationOptions":1, "ValidationImplications":1}')`(jest to w rzeczywistości domyślna zasada)|
|`zipPattern`|Ta właściwość służy podczas pozyskiwania danych z magazynu, który ma archiwum ZIP. Jest to wartość ciągu wskazująca wyrażenie regularne, którego należy użyć podczas wybierania plików w archiwum ZIP do pozyskiwania.  Wszystkie inne pliki w archiwum zostaną zignorowane.|`with (zipPattern="*.csv")`|

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [pozyskiwania danych](/azure/data-explorer/ingest-data-overview)
* Dowiedz się więcej o [obsługiwanych formatach danych](ingestion-supported-formats.md)
