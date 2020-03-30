---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: data-factory
author: memildin
ms.service: data-factory
ms.topic: include
ms.date: 03/10/2020
ms.author: memildin
ms.custom: include file
ms.openlocfilehash: 0b6864c3304b86e80549297fc073a2e387000d64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80272919"
---
```json
{
  "VendorName": "Microsoft",
  "AlertType": "SUSPECT_SVCHOST",
  "StartTimeUtc": "2016-12-20T13:38:00.000Z",
  "EndTimeUtc": "2019-12-20T13:40:01.733Z",
  "ProcessingEndTime": "2019-09-16T12:10:19.5673533Z",
  "TimeGenerated": "2016-12-20T13:38:03.000Z",
  "IsIncident": false,
  "Severity": "High",
  "Status": "New",
  "ProductName": "Azure Security Center",
  "SystemAlertId": "2342409243234234_F2BFED55-5997-4FEA-95BD-BB7C6DDCD061",
  "CompromisedEntity": "WebSrv1",
  "Intent": "Execution",
  "AlertDisplayName": "Suspicious process detected",
  "Description": "Suspicious process named 'SVCHOST.EXE' was running from path: %{Process Path}",
  "RemediationSteps": ["contact your security information team"],
  "ExtendedProperties": {
    "Process Path": "c:\\temp\\svchost.exe",
    "Account": "Contoso\\administrator",
    "PID": 944,
    "ActionTaken": "Detected"
  },
  "Entities": [],
  "ResourceIdentifiers": [
        {
            Type: "AzureResource",
            AzureResourceId: "/subscriptions/86057C9F-3CDD-484E-83B1-7BF1C17A9FF8/resourceGroups/backend-srv/providers/Microsoft.Compute/WebSrv1"
        },
        {
            Type: "LogAnalytics",
            WorkspaceId: "077BA6B7-8759-4F41-9F97-017EB7D3E0A8",
            WorkspaceSubscriptionId: "86057C9F-3CDD-484E-83B1-7BF1C17A9FF8",
            WorkspaceResourceGroup: "omsrg",
            AgentId: "5A651129-98E6-4E6C-B2CE-AB89BD815616",
        }
  ]
}
```

### <a name="the-data-model-of-the-schema"></a>Model danych schematu

|Pole|Typ danych|Opis|
|----|----|----|
|**Nazwa alertuWyświetlajna**|Ciąg|Wyświetlana nazwa alertu.|
|**Typ alertu**|Ciąg|Typ alertu. Alerty tego samego typu powinny mieć taką samą wartość. To pole jest ciągiem z kluczem reprezentującym typ alertu, a nie wystąpieniem alertu. Wszystkie wystąpienia alertów z tej samej logiki wykrywania/analityczne powinny mieć taką samą wartość dla typu alertu.|
|**Naruszoneentity**|Ciąg|Wyświetlana nazwa zasobu najbardziej powiązanego z tym alertem.|
|**Opis**|Ciąg|Opis wpisu.|
|**EndTimeUtc (Koniec czasu)**|DateTime|Czas ostatniego zdarzenia lub działania uwzględnionego w alercie.  Pole powinno być ciągiem zgodnym z formatem ISO8601, w tym informacjami o strefie czasowej UTC.|
|**Jednostki**|IEnumerable (IEntity)|Lista jednostek związanych z alertem. Ta lista może zawierać mieszaninę jednostek różnych typów. Typ jednostek może być dowolnym z typów zdefiniowanych w sekcji Jednostki. Jednostki, które nie znajdują się na poniższej liście mogą być również wysyłane, jednak nie ma gwarancji, że zostaną przetworzone (alert nie zakończy sprawdzanie poprawności z nowymi typami jednostek).|
|**Extendedproperties**|Słownik (ciąg,ciąg)|Dostawcy mogą (opcjonalnie) dołączyć pola niestandardowe w tym miejscu.|
|**Intencja**|Wyliczenie|Intencji łańcucha zabijania związanych za alertem. Aby uzyskać listę obsługiwanych wartości i objaśnienia obsługiwanych intencji łańcucha zabójstw obsługiwane przez usługę Azure Security Center, zobacz [Intencje](../articles/security-center/alerts-reference.md#intentions).<br/>To pole może mieć wiele wartości (oddzielonych przecinkiem).|
|**IsIncident (Nieident)**|Wartość logiczna|To pole określa, czy alert jest zdarzeniem (grupowanie złożone kilku alertów) czy pojedynczym alertem. Domyślna wartość pola to "false" (co oznacza, że jest to pojedynczy alert).|
|**ProcessingEndTime (Czas przetwarzania)**|DateTime|Czas, w którym alert był dostępny dla użytkownika końcowego w oryginalnym produkcie, w którym alert.|
|**Productname**|Ciąg|Nazwa produktu, który opublikował ten alert (Azure Security Center, Azure ATP, Microsoft Defender ATP, O365 ATP, MCAS i tak dalej).|
|**Kroki korygowania**|Listy<String>|Ręczne elementy akcji do podjęcia w celu skorygowanie alertu.|
|**Identyfikatory zasobów**|Lista (identyfikatory zasobów)|Identyfikatory zasobów dla tego alertu, który może służyć do kierowania alertu do odpowiedniej grupy ekspozycji produktu (dzierżawy, obszaru roboczego, subskrypcji itp.). Może istnieć wiele identyfikatorów innego typu na alert.|
|**Ważność**|Wyliczenie|Ważność alertu zgłoszonego przez dostawcę. Możliwe wartości: Informacje, Niskie, Średnie i Wysokie.|
|**StartTimeUtc**|DateTime|Czas pierwszego zdarzenia lub działania uwzględnionego w alercie. Pole powinno być ciągiem zgodnym z formatem ISO8601, w tym informacjami o strefie czasowej UTC.|
|**Stan**|Wyliczenie|Stan cyklu życia alertu.<br/>Obsługiwane stany to: Nowy, Rozwiązany, Odrzucony, Nieznany.<br/>Alertowi określającemu wartość inną niż obsługiwane opcje jest przypisywany stan "Nieznany".<br/>Alert, który nie określa wartości jest przypisany stan "Nowy".|
|**SystemAlertId**|Ciąg|Identyfikator alertu.|
|**TimeGenerated**|DateTime|Czas, w której alert został wygenerowany przez dostawcę alertów. Jeśli nie zostanie zgłoszony przez wewnętrznych dostawców alertów, produkt może wybrać przypisanie czasu odebranego do przetworzenia przez produkt.  Pole powinno być ciągiem zgodnym z formatem ISO8601, w tym informacjami o strefie czasowej UTC.|
|**Nazwadostawcy**|Ciąg|Nazwa dostawcy, który podnosi alert.|
|||
