---
title: Interfejs API użycia zasobów dostawcy | Dokumentacja firmy Microsoft
description: Dokumentacja dotycząca użycia zasobów interfejsu API, który pobiera informacje o użyciu usługi Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: 1963708fb05feb7797bc8b4df024d16175687b17
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55177912"
---
# <a name="provider-resource-usage-api"></a>Interfejs API użycia zasobów dostawcy
Termin *dostawcy* stosuje się do administratora usługi i żadnych delegowanych dostawców. Operatorzy usługi Azure Stack i delegowanych dostawców służy interfejs API użycia dostawcy do wyświetlania użycia bezpośrednich dzierżawcom. Na przykład jak pokazano na diagramie, P0 można wywołać interfejsu API, aby uzyskać informacje dotyczące firmy P1 zastosowania dostawcy i jego P2 bezpośredniego użycia i P1 może wywołać dotyczące użycia jednostek P3 i P4.

![Model koncepcyjny hierarchii dostawcy](media/azure-stack-provider-resource-api/image1.png)

## <a name="api-call-reference"></a>Dokumentacja wywołania interfejsu API
### <a name="request"></a>Żądanie
Żądanie pobiera szczegóły użycia dla żądanego subskrypcje i żądany przedział czasu. Nie treści żądania nie istnieje.

Ten interfejs API użycia jest dostawcy interfejsu API, aby obiekt wywołujący musi mieć przypisaną rolę właściciela, współautora lub czytelnika, w ramach subskrypcji dostawcy.

| **Metoda** | **Identyfikator URI żądania** |
| --- | --- |
| GET |https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity} & subscriberId = {sub1.1} & parametru api-version = 2015-06-01-preview & continuationToken = {wartości tokenu} |

### <a name="arguments"></a>Argumenty
| **Argument** | **Opis** |
| --- | --- |
| *armendpoint* |Usługa Azure Resource Manager punktu końcowego środowiskiem usługi Azure Stack. Konwencja usługi Azure Stack to, że nazwa punktu końcowego usługi Azure Resource Manager jest w formacie `https://adminmanagement.{domain-name}`. Na przykład deweloperski, jeśli nazwa domeny jest *local.azurestack.external*, punkt końcowy usługi Resource Manager jest `https://adminmanagement.local.azurestack.external`. |
| *subId* |Identyfikator subskrypcji użytkownika, który wywołuje tę funkcję. |
| *reportedStartTime* |Uruchomienie zapytania. Wartość *daty/godziny* powinny być uniwersalnego czasu koordynowanego (UTC) i na początku godziny, na przykład, 13:00. Codzienne agregacji należy ustawić tę wartość na północy czasu UTC. Format jest *poprzedzone znakiem zmiany znaczenia* ISO 8601. Na przykład *2015-06-16T18% 3a53% 3a11% 2b00% 3a00Z*, gdzie dwukropek jest zmieniany na *% 3a* i plus jest zmieniany na *% 2b* tak, aby identyfikator URI przyjazna. |
| *reportedEndTime* |Godzina zakończenia kwerendy. Ograniczenia, które są stosowane do *reportedStartTime* mają zastosowanie również do tego argumentu. Wartość *reportedEndTime* nie może przypadać w przyszłości lub daty bieżącej. Jeśli tak jest, wynikiem jest równa "przetwarzanie nie ukończone." |
| *aggregationGranularity* |Opcjonalnym parametrem, który ma dwie osobne wartości potencjalnych: dni i godzin. Jako wartości wskazują, jeden zwraca dane w codziennych stopień szczegółowości, a drugi to rozpoznawanie co godzinę. Opcja codzienne jest ustawieniem domyślnym. |
| *subscriberId* |Identyfikator subskrypcji. Aby uzyskać odfiltrowane dane, wymagany jest identyfikator subskrypcji, bezpośrednie dzierżawy dostawcy. Jeśli parametr Identyfikatora subskrypcji, nie jest określony, to wywołanie zwraca dane użycia dla wszystkich dostawca bezpośredniego dzierżaw. |
| *wersja interfejsu API* |Wersja protokołu, który jest używany do wykonania tego żądania. Ta wartość jest równa *2015-06-01-preview*. |
| *continuationToken* |Token jest pobierany z ostatniego wywołania, interfejs API użycia dostawcy. Token ten jest wymagane, gdy odpowiedź jest większa niż 1000 wierszy i działa jako zakładki uzyskać informacje o postępie. Jeśli token nie jest obecny, dane są pobierane z początku dnia lub godziny, w oparciu o stopień szczegółowości przekazana. |

### <a name="response"></a>Odpowiedź
Pobierz /subscriptions/sub1/providers/Microsoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00 & reportedEndTime = 2015-06-01T00% 3a00% 3a00% 2b00% 3a00 & aggregationGranularity = dzień c & subscriberId = sub1.1 & parametru api-version = 1,0

```json
{
"value": [
{

"id":
"/subscriptions/sub1.1/providers/Microsoft.Commerce.Admin/UsageAggregate/sub1.1-

meterID1",
"name": "sub1.1-meterID1",
"type": "Microsoft.Commerce.Admin/UsageAggregate",

"properties": {
"subscriptionId":"sub1.1",
"usageStartTime": "2015-03-03T00:00:00+00:00",
"usageEndTime": "2015-03-04T00:00:00+00:00",
"instanceData":"{\"Microsoft.Resources\":{\"resourceUri\":\"resourceUri1\",\"location\":\"Alaska\",\"tags\":null,\"additionalInfo\":null}}",
"quantity":2.4000000000,
"meterId":"meterID1"

}
},

…
```

### <a name="response-details"></a>Szczegóły odpowiedzi
| **Argument** | **Opis** |
| --- | --- |
| *id* |Unikatowy identyfikator użycia agregacji. |
| *Nazwa* |Nazwa użycia agregacji. |
| *type* |Definicja zasobu. |
| *subscriptionId* |Identyfikator subskrypcji użytkownika usługi Azure Stack. |
| *usageStartTime* |Godzina przedział użycia, do której należy ta wartość zagregowana użycia rozpoczęcia UTC.|
| *usageEndTime* |Godzina zakończenia UTC zasobnika użycia, do której należy ta wartość zagregowana użycia. |
| *instanceData* |Pary klucz wartość Szczegóły wystąpienia (w nowym formacie):<br> *Identyfikator URI zasobu*: Pełni kwalifikowanego Identyfikatora zasobu, co obejmuje grupy zasobów i nazwę wystąpienia. <br> *Lokalizacja*: Region, w którym ta usługa została uruchomiona. <br> *Tagi*: Tagi zasobów, które są określone przez użytkownika. <br> *additionalInfo*: Wpisz więcej szczegółów na temat zasobów, który został wykorzystany, na przykład wersja systemu operacyjnego lub obrazu. |
| *Ilość* |Ilość użycia zasobów, które wystąpiły w tym horyzoncie czasowym. |
| *meterId* |Unikatowy identyfikator zasobu, który został wykorzystany (nazywane również *ResourceID*). |


## <a name="retrieve-usage-information"></a>Pobieranie informacji o użyciu

### <a name="powershell"></a>PowerShell

Aby wygenerować dane użycia, powinny mieć zasoby, które są uruchomione i aktywnie przy użyciu systemu, na przykład, aktywnej maszyny wirtualnej lub konto magazynu zawierające dane itp. Jeśli nie masz pewności czy dysponuje danymi umieszczonymi w usłudze Azure Stack w portalu Marketplace, Wdróż maszynę wirtualną (VM) i sprawdź maszyna wirtualna monitorowania bloku, aby upewnić się, jest uruchomiony. Aby wyświetlić dane użycia, użyj następujących poleceń cmdlet programu PowerShell:

1. [Zainstaluj program PowerShell dla usługi Azure Stack.](azure-stack-powershell-install.md)
2. [Konfigurowanie użytkownika usługi Azure Stack](user/azure-stack-powershell-configure-user.md) lub [operatora infrastruktury Azure Stack](azure-stack-powershell-configure-admin.md) środowiska PowerShell 
3. Aby pobrać dane użycia, użyj [Get UsageAggregates](/powershell/module/azurerm.usageaggregates/get-usageaggregates) polecenia cmdlet programu PowerShell:
```powershell
Get-UsageAggregates -ReportedStartTime "<Start time for usage reporting>" -ReportedEndTime "<end time for usage reporting>" -AggregationGranularity <Hourly or Daily>
```
### <a name="rest-api"></a>Interfejs API REST

Można zbierać informacje użycia dla subskrypcji usunięto, wywołując usługę Microsoft.Commerce.Admin. 

**Aby zwrócić wszystkie użycia dzierżawy dla usunięte dla aktywnych użytkowników:**

| **Metoda** | **Identyfikator URI żądania** |
| --- | --- |
| GET | https://{armendpoint}/subscriptions/{subId}/providersMicrosoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={start-time}&reportedEndTime={end-endtime}&aggregationGranularity=Hourly&api-version= 2015-06-01-preview |

**Aby zwrócić użycia usuniętych lub aktywnej dzierżawy:**

| **Metoda** | **Identyfikator URI żądania** |
| --- | --- |
| GET |https://{armendpoint}/subscriptions/{subId}/providersMicrosoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={start-time}&reportedEndTime={end-endtime}&aggregationGranularity=Hourly&subscriberId={ Identyfikator subskrybenta} & parametru api-version = 2015-06-01-preview |


## <a name="next-steps"></a>Kolejne kroki
[Dokumentacja interfejsu API użycia zasobów przez dzierżawcę](azure-stack-tenant-resource-usage-api.md)

[— Często zadawane pytania dotyczące wykorzystania](azure-stack-usage-related-faq.md)
