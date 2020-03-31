---
title: Przygotowanie do zmiany formatu w dziennikach zasobów usługi Azure Monitor
description: Dzienniki zasobów platformy Azure zostały przeniesione do używania dołączanych obiektów blob 1 listopada 2018 r.
author: johnkemnetz
services: monitoring
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 001dfbc78c0027249143e933684523d47af383d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096782"
---
# <a name="prepare-for-format-change-to-azure-monitor-platform-logs-archived-to-a-storage-account"></a>Przygotowanie do zmiany formatu na dzienniki platformy Azure Monitor zarchiwizowane na koncie magazynu

> [!WARNING]
> Jeśli wysyłasz [dzienniki zasobów platformy Azure lub metryki do konta magazynu przy użyciu ustawień diagnostycznych](resource-logs-collect-storage.md) lub [dzienników aktywności do konta magazynu przy użyciu profilów dziennika,](resource-logs-collect-storage.md)format danych na koncie magazynu został zmieniony na JSON Lines w dniu 1 listopada 2018 r. Poniższe instrukcje opisują wpływ i sposób aktualizowania narzędzi do obsługi nowego formatu.
>

## <a name="what-changed"></a>Co się zmieniło

Usługa Azure Monitor oferuje możliwość wysyłania dzienników zasobów i dzienników aktywności do konta magazynu platformy Azure, obszaru nazw usługi Event Hubs lub do obszaru roboczego usługi Log Analytics w usłudze Azure Monitor. Aby rozwiązać problem z wydajnością systemu, **1 listopada 2018 o godzinie 12:00 czasu UTC** zmieniono format danych dziennika wysyłanych do magazynu obiektów blob. Jeśli masz narzędzia, które odczytuje dane z magazynu obiektów blob, należy zaktualizować narzędzia, aby zrozumieć nowy format danych.

* W czwartek, 1 listopada 2018 o 12:00 o północy UTC, format obiektu blob zmienił się na [JSON Lines](http://jsonlines.org/). Oznacza to, że każdy rekord będzie rozdzielany przez nową linię, bez zewnętrznej tablicy rekordów i bez przecinków między rekordami JSON.
* Format obiektu blob zmieniono dla wszystkich ustawień diagnostycznych we wszystkich subskrypcjach jednocześnie. Pierwszy plik PT1H.json wyemitowany 1 listopada używał tego nowego formatu. Nazwy obiektów blob i kontenerów pozostają takie same.
* Ustawienie ustawienia diagnostycznego między przed 1 listopada nadal emitować dane w bieżącym formacie do 1 listopada.
* Ta zmiana wystąpiła jednocześnie we wszystkich regionach chmury publicznej. Zmiana nie nastąpi w microsoft azure obsługiwane przez 21Vianet, Azure Niemcy, lub azure instytucji rządowych chmury jeszcze.
* Ta zmiana ma wpływ na następujące typy danych:
  * [Dzienniki zasobów platformy Azure](archive-diagnostic-logs.md) [(zobacz listę zasobów tutaj](diagnostic-logs-schema.md))
  * [Metryki zasobów platformy Azure eksportowane przez ustawienia diagnostyczne](diagnostic-settings.md)
  * [Dane dziennika aktywności platformy Azure eksportowane przez profile dzienników](activity-log-collect.md)
* Ta zmiana nie ma wpływu:
  * Dzienniki przepływu sieci
  * Dzienniki usługi platformy Azure nie są jeszcze dostępne za pośrednictwem usługi Azure Monitor (na przykład dzienniki zasobów usługi Azure App Service, dzienniki analizy magazynu)
  * Routing dzienników zasobów platformy Azure i dzienników aktywności do innych miejsc docelowych (Centra zdarzeń, usługa Log Analytics)

### <a name="how-to-see-if-you-are-impacted"></a>Jak sprawdzić, czy masz wpływ na ciebie

Ta zmiana ma na ciebie wpływ tylko wtedy, gdy:
1. Wysyłają dane dziennika do konta magazynu platformy Azure przy użyciu ustawienia diagnostycznego i
2. Posiadaj narzędzia, które zależą od struktury JSON tych dzienników w magazynie.
 
Aby zidentyfikować, jeśli masz ustawienia diagnostyczne, które wysyłają dane do konta magazynu platformy Azure, możesz przejść do sekcji **Monitor** w portalu, kliknąć **ustawienia diagnostyczne**i zidentyfikować wszystkie zasoby, które mają stan **diagnostyczny** ustawiony na **Włączone:**

![Blok Ustawienia diagnostyczne monitora azure](media/diagnostic-logs-append-blobs/portal-diag-settings.png)

Jeśli stan diagnostyki jest włączony, masz aktywne ustawienie diagnostyczne na tym zasobie. Kliknij zasób, aby sprawdzić, czy jakieś ustawienia diagnostyczne wysyłają dane na konto magazynu:

![Włączone konto magazynu](media/diagnostic-logs-append-blobs/portal-storage-enabled.png)

Jeśli masz zasoby wysyłające dane do konta magazynu przy użyciu tych ustawień diagnostycznych zasobów, ten format danych na tym koncie magazynu będzie miał wpływ na tę zmianę. Jeśli nie masz niestandardowe narzędzia, które działa poza tymi kontami magazynu, zmiana formatu nie będzie miała wpływu na Ciebie.

### <a name="details-of-the-format-change"></a>Szczegóły zmiany formatu

Bieżący format pliku PT1H.json w magazynie obiektów blob platformy Azure używa tablicy rekordów JSON. Oto przykład pliku dziennika KeyVault teraz:

```json
{
    "records": [
        {
            "time": "2016-01-05T01:32:01.2691226Z",
            "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
            "operationName": "VaultGet",
            "operationVersion": "2015-06-01",
            "category": "AuditEvent",
            "resultType": "Success",
            "resultSignature": "OK",
            "resultDescription": "",
            "durationMs": "78",
            "callerIpAddress": "104.40.82.76",
            "correlationId": "",
            "identity": {
                "claim": {
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com",
                    "appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"
                }
            },
            "properties": {
                "clientInfo": "azure-resource-manager/2.0",
                "requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01",
                "id": "https://contosokeyvault.vault.azure.net/",
                "httpStatusCode": 200
            }
        },
        {
            "time": "2016-01-05T01:33:56.5264523Z",
            "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
            "operationName": "VaultGet",
            "operationVersion": "2015-06-01",
            "category": "AuditEvent",
            "resultType": "Success",
            "resultSignature": "OK",
            "resultDescription": "",
            "durationMs": "83",
            "callerIpAddress": "104.40.82.76",
            "correlationId": "",
            "identity": {
                "claim": {
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com",
                    "appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"
                }
            },
            "properties": {
                "clientInfo": "azure-resource-manager/2.0",
                "requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01",
                "id": "https://contosokeyvault.vault.azure.net/",
                "httpStatusCode": 200
            }
        }
    ]
}
```

Nowy format używa [linii JSON](http://jsonlines.org/), gdzie każde zdarzenie jest wierszem, a znak nowego wiersza wskazuje nowe zdarzenie. Oto, jak będzie wyglądać powyższy przykład w pliku PT1H.json po zmianie:

```json
{"time": "2016-01-05T01:32:01.2691226Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "78","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
{"time": "2016-01-05T01:33:56.5264523Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "83","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
```

Ten nowy format umożliwia usługi Azure Monitor wypychania plików dziennika przy użyciu [dołączania obiektów blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs), które są bardziej wydajne dla ciągłego dołączania nowych danych zdarzeń.

## <a name="how-to-update"></a>Jak zaktualizować

Wystarczy dokonać aktualizacji, jeśli masz niestandardowe narzędzia, które poskłyszają te pliki dziennika do dalszego przetwarzania. Jeśli korzystasz z zewnętrznej analizy dzienników lub narzędzia SIEM, [zalecamy użycie centrów zdarzeń do pozyskiwania tych danych.](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) Integracja centrów zdarzeń jest łatwiejsze pod względem przetwarzania dzienników z wielu usług i lokalizacji zakładek w określonym dzienniku.

Narzędzia niestandardowe powinny zostać zaktualizowane w celu obsługi zarówno bieżącego formatu, jak i opisanego powyżej formatu JSON Lines. Zapewni to, że gdy dane zaczną pojawiać się w nowym formacie, narzędzia nie zostaną przerwane.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [archiwizowaniu dzienników zasobów na koncie magazynu](./../../azure-monitor/platform/archive-diagnostic-logs.md)
* Dowiedz się więcej o [archiwizowaniu danych dziennika aktywności na koncie magazynu](./../../azure-monitor/platform/archive-activity-log.md)

