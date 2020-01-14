---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 01/08/2020
ms.author: orspodek
ms.openlocfilehash: f9788e4623ce60ad55d79558d1d77a17eb2a9f26
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75779958"
---
### <a name="event-system-properties-mapping"></a>Mapowanie właściwości systemu zdarzeń

W przypadku wybrania **Właściwości systemu zdarzeń** w sekcji **Źródło danych** w powyższej tabeli przejdź do [interfejsu użytkownika sieci Web](https://dataexplorer.azure.com/) , aby uruchomić odpowiednie polecenie KQL w celu utworzenia odpowiedniego mapowania.

   **Dla mapowania woluminów CSV:**

    ```kusto
    .create table MyTable ingestion csv mapping "CsvMapping1"
    '['
    '   { "column" : "messageid", "DataType":"string", "Properties":{"Ordinal":"0"}},'
    '   { "column" : "userid", "DataType":"string", "Properties":{"Ordinal":"1"}},'
    '   { "column" : "other", "DataType":"int", "Properties":{"Ordinal":"2"}}'
    ']'
    ```
 
   **Dla mapowania JSON:**

    ```kusto
    .create table MyTable ingestion json mapping "JsonMapping1"
    '['
    '    { "column" : "messageid", "datatype" : "string", "Properties":{"Path":"$.message-id"}},'
    '    { "column" : "userid", "Properties":{"Path":"$.user-id"}},'
    '    { "column" : "other", "Properties":{"Path":"$.other"}}'
    ']'
    ```

   > [!TIP]
   > * W mapowaniu należy uwzględnić wszystkie wybrane właściwości. 
   > * Kolejność właściwości jest ważna w mapowaniu woluminów CSV. Właściwości systemu muszą być wymienione przed wszystkimi innymi właściwościami i w takiej samej kolejności, w jakiej są wyświetlane na liście rozwijanej **Właściwości systemu zdarzeń** .