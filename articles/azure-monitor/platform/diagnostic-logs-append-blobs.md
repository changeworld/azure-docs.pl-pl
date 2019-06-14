---
title: Przygotowanie do zmiany formatu do dzienników diagnostycznych usługi Azure Monitor
description: Dzienniki diagnostyczne platformy Azure zostaną przeniesione do użycia uzupełnialnych obiektów BLOB od 1 listopada 2018 r.
author: johnkemnetz
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: ab5fba6bbbf6ade83c7699edec937ba02b222939
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60237663"
---
# <a name="prepare-for-format-change-to-azure-monitor-diagnostic-logs-archived-to-a-storage-account"></a>Przygotowanie do zmiany formatu do dzienników diagnostycznych usługi Azure Monitor zarchiwizować na koncie magazynu

> [!WARNING]
> W przypadku wysyłania [dzienników diagnostycznych zasobów platformy Azure lub metryk z kontem magazynu za pomocą ustawień diagnostycznych zasobu](./../../azure-monitor/platform/archive-diagnostic-logs.md) lub [profile dzienników dzienników aktywności do konta magazynu przy użyciu](./../../azure-monitor/platform/archive-activity-log.md), formatu danych w Konto magazynu zmieni się na wiersze JSON od 1 listopada 2018 r. W poniższych instrukcjach opisano wpływ i aktualizacji narzędzi do obsługi nowego formatu. 
>
> 

## <a name="what-is-changing"></a>Co ulega zmianie

Usługa Azure Monitor udostępnia możliwości, który umożliwia wysyłanie danych diagnostycznych zasobów i dane dzienników aktywności do konta usługi Azure storage, przestrzeń nazw usługi Event Hubs, lub do obszaru roboczego usługi Log Analytics w usłudze Azure Monitor. Aby rozwiązać problem z wydajnością systemu, na **1 listopada 2018, 12:00 do północy czasu UTC** zmieni format dziennika danych wysyła do magazynu obiektów blob. Jeśli masz, czyli narzędzia odczytywania danych z magazynu obiektów blob, należy zaktualizować narzędzi w taki sposób, aby zrozumieć nowego formatu danych.

* Czwartek, listopad 1 2018 północy czasu UTC, 12:00, format obiektów blob zmieni się [wierszy JSON](http://jsonlines.org/). Oznacza to, że każdy rekord będzie rozdzielone znakami nowego wiersza, z tablicą nie rekordów zewnętrzne i nie przecinkami, między rekordami JSON.
* Zmiany formatu obiektu blob dla wszystkich ustawień diagnostycznych dla wszystkich subskrypcji na raz. Pierwszy plik PT1H.json wysyłanego do 1 listopada użyje ten nowy format. Nazwy obiektów blob i kontener pozostają takie same.
* Ustawienie diagnostyczne od chwili 1 listopada w dalszym ciągu Emituj danych w bieżącym formacie, aż do 1 listopada.
* Ta zmiana zostanie przeprowadzona tylko raz we wszystkich regionach chmury publicznej. Zmiana zostanie przeprowadzona w chińskiej wersji platformy Azure, Azure (Niemcy) lub Azure dla instytucji rządowych chmurach jeszcze.
* Ta zmiana ma wpływ na następujące typy danych:
  * [Dzienniki diagnostyczne usługi Azure resource](./../../azure-monitor/platform/archive-diagnostic-logs.md) ([zobacz listę zasobów w tym miejscu](./../../azure-monitor/platform/diagnostic-logs-schema.md))
  * [Metryki zasobów platformy Azure są eksportowane przez ustawienia diagnostyczne](./../../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings)
  * [Dane dzienników aktywności platformy Azure są eksportowane przez profile dziennika](./../../azure-monitor/platform/archive-activity-log.md)
* Ta zmiana nie ma wpływu na:
  * Dzienniki sieciowe w usłudze flow
  * Dzienniki usługi platformy Azure nie udostępniane za pośrednictwem usługi Azure Monitor jeszcze (na przykład dzienników diagnostycznych usługi Azure App Service, dzienniki analiz magazynu)
  * Routing dzienniki diagnostyczne platformy Azure i dzienników aktywności do innych miejsc docelowych (usługa Event Hubs, usługi Log Analytics)

### <a name="how-to-see-if-you-are-impacted"></a>Jak sprawdzić, jeśli ma wpływ

Jedynie wpływ ta zmiana Jeśli możesz:
1. Konta usługi Azure storage przy użyciu ustawienie diagnostyczne zasobu definiuje, wysyłają dane dziennika i
2. Dostępne narzędzie, który zależy od struktury JSON te dzienniki w magazynie.
 
Aby sprawdzić, czy masz ustawienia diagnostyczne zasobów, które wysyłają dane do konta usługi Azure storage, możesz przejść do **Monitor** sekcji portalu, kliknij pozycję **ustawień diagnostycznych**i zidentyfikować wszystkie zasoby, które mają **stanie diagnostyki** równa **włączone**:

![Blok usługi Azure Monitor ustawień diagnostycznych](./media/diagnostic-logs-append-blobs/portal-diag-settings.png)

Jeśli stanie diagnostyki jest równa włączona, masz aktywne ustawienie diagnostyczne dla tego zasobu. Kliknij zasób, aby zobaczyć, jeśli wszystkie ustawienia diagnostyczne wysyłania danych do konta magazynu:

![Konto magazynu jest włączone](./media/diagnostic-logs-append-blobs/portal-storage-enabled.png)

Jeśli masz zasoby wysyłania danych do konta magazynu przy użyciu tych ustawień diagnostycznych zasobu formatu danych na tym koncie magazynu będzie wpływ tej zmiany. Jeśli nie masz niestandardowych narzędzi, który działa poza te konta magazynu, Zmień format nie ma wpływu na użytkownik.

### <a name="details-of-the-format-change"></a>Szczegółowe informacje o zmianę formatu

Bieżący format pliku PT1H.json w usłudze Azure blob storage korzysta z tablicą JSON rekordów. Oto przykładowy plik dziennika magazynu kluczy:

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

Używa nowego formatu [wierszy JSON](http://jsonlines.org/), gdzie każde zdarzenie jest linię i znak nowego wiersza wskazuje nowe zdarzenie. Poniżej przedstawiono, jak w powyższym przykładzie będą wyglądać w pliku PT1H.json po zmianie:

```json
{"time": "2016-01-05T01:32:01.2691226Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "78","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
{"time": "2016-01-05T01:33:56.5264523Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "83","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
```

Ten nowy format umożliwia wypychania plików dziennika przy użyciu usługi Azure Monitor [uzupełnialnych obiektów blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs), które są bardziej wydajne stale dołączania nowych danych zdarzenia.

## <a name="how-to-update"></a>Jak zaktualizować

Należy dokonać aktualizacji w przypadku niestandardowych narzędzi, które pozwalają pozyskać te pliki dziennika w celu dalszego przetwarzania. Jeśli wykonujesz korzystania z usługi zewnętrznej log analytics lub narzędziem SIEM, firma Microsoft zaleca [przy użyciu usługi event hubs pozyskiwać dane zamiast](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/). Event hubs integracji jest łatwiejsze pod kątem przetwarzania dzienników z wielu usług i tworzenie zakładek dla lokalizacji, w określonym dzienniku.

Narzędzia niestandardowe powinien zostać zaktualizowany do obsługi bieżącego formatu i format JSON wiersze opisanych powyżej. Pozwoli to zagwarantować, że po uruchomieniu danych pojawią się w nowym formacie narzędzi nie przerywają działania.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [archiwizowanie dzienników diagnostycznych zasobów do konta magazynu](./../../azure-monitor/platform/archive-diagnostic-logs.md)
* Dowiedz się więcej o [archiwizowania danych dziennika aktywności na koncie magazynu](./../../azure-monitor/platform/archive-activity-log.md)

