---
title: Przykłady szybki start Azure Monitor 2.0 interfejsu wiersza polecenia. | Microsoft Docs
description: Przykładowe polecenia 2.0 interfejsu wiersza polecenia dla funkcji Azure monitora. Azure Monitor jest usługą Microsoft Azure, co pozwala na wysyłanie powiadomień o alertach, wywołaj adresu URL sieci web na podstawie wartości danych telemetrycznych skonfigurowany, a automatycznego skalowania usługi w chmurze, maszyn wirtualnych i aplikacji sieci Web.
author: kamathashwin
manager: ''
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 1653aa81-0ee6-4622-9c65-d4801ed9006f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: ashwink
ms.openlocfilehash: a9c6cc0fb81b094e1c980e4c209184a0c0ebd428
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="azure-monitor-cli-20-quick-start-samples"></a>Przykładów dla platformy Azure szybki start Monitor 2.0 interfejsu wiersza polecenia
W tym artykule przedstawiono przykład się, że polecenia interfejsu wiersza polecenia (CLI) ułatwiają dostęp do funkcji Azure monitora. Azure Monitor pozwala automatycznego skalowania usługi w chmurze, maszyn wirtualnych i aplikacji sieci Web, jak i do wysyłania powiadomień o alertach lub zadzwoń na podstawie wartości dane telemetryczne skonfigurowanych adresów URL sieci web.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie została jeszcze zainstalowana wiersza polecenia platformy Azure, postępuj zgodnie z instrukcjami dotyczącymi [zainstalować 2.0 interfejsu wiersza polecenia Azure](/cli/azure/install-azure-cli). Można również użyć [powłoki chmury Azure](/azure/cloud-shell) do uruchamiania interfejsu wiersza polecenia jako interaktywna w przeglądarce. 

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.
Pierwszym krokiem jest logowania do konta platformy Azure.

```azurecli
az login
```

Po uruchomieniu tego polecenia, musisz zalogować się za pośrednictwem zgodnie z instrukcjami na ekranie. Wszystkie polecenia działają w kontekście subskrypcji domyślne.

Aby wyświetlić szczegółowe informacje o Twojej bieżącej subskrypcji, użyj następującego polecenia.

```azurecli
az account show
```

Aby zmienić kontekst pracy do innej subskrypcji, użyj następującego polecenia.

```azurecli
az account set -s <Subscription ID or name>
```

Aby wyświetlić listę wszystkich obsługiwanych poleceń Azure Monitor, należy wykonać następujące czynności.

```azurecli
az monitor -h
```

## <a name="view-activity-log-for-a-subscription"></a>Wyświetl dziennik aktywności dla subskrypcji

Aby wyświetlić listę zdarzeń dziennika aktywności, wykonaj następujące czynności.

```azurecli
az monitor activity-log list
```

Spróbuj wykonać następujące czynności, aby wyświetlić wszystkie dostępne opcje.

```azurecli
az monitor activity-log list -h
```

Oto przykład listy dzienniki w grupie zasobów

```azurecli
az monitor activity-log list --resource-group <group name>
```

Przykład listy dzienniki przez obiekt wywołujący

```azurecli
az monitor activity-log list --caller myname@company.com
```

Przykład listy dzienniki przez obiekt wywołujący na typ zasobu, w pewnym zakresie dat.

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>Praca z alertami 
[!NOTE] Tylko alerty (klasyczny) jest obsługiwana w interfejsu wiersza polecenia w tej chwili. 

### <a name="get-alert-classic-rules-in-a-resource-group"></a>Pobierz reguły alertów (klasyczne) w grupie zasobów

```azurecli
az monitor activity-log alert list --resource-group <group name>
az monitor activity-log alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-metric-alert-classic-rule"></a>Utwórz metryki regułę alertu (klasyczne)

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-an-alert-classic-rule"></a>Usuń regułę alertu (klasyczne)

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="log-profiles"></a>Profile dziennika

Skorzystaj z informacji w tej sekcji, aby pracować przy użyciu profilów dziennika.

### <a name="get-a-log-profile"></a>Pobierz profil dziennika

```azurecli
az monitor log-profiles list
az monitor log-profiles show --name <profile name>
```

### <a name="add-a-log-profile-with-retention"></a>Dodawanie profilu dziennika z przechowywaniem

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true \
    --storage-account-id <storage account ID to store the logs in>
```

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>Dodawanie profilu dziennika z przechowywania i EventHub

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true
    --storage-account-id <storage account ID to store the logs in>
    --service-bus-rule-id <service bus rule ID to stream to>
```

### <a name="remove-a-log-profile"></a>Usuń profil dziennika

```azurecli
az monitor log-profiles delete --name <profile name>
```

## <a name="diagnostics"></a>Diagnostyka

Skorzystaj z informacji w tej sekcji, aby pracować z ustawień diagnostycznych.

### <a name="get-a-diagnostic-setting"></a>Pobierz ustawienia diagnostyki

```azurecli
az monitor diagnostic-settings list --resource <target resource ID>
```

### <a name="create-a-diagnostic-log-setting"></a>Utwórz ustawienie dziennik diagnostyczny 

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <storage account ID> \
    --resource <target resource object ID> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

### <a name="delete-a-diagnostic-setting"></a>Usuwanie ustawień diagnostycznych

```azurecli
az monitor diagnostic-settings delete --name <diagnostic name> \
    --resource <target resource ID>
```

## <a name="autoscale"></a>Automatyczne skalowanie

Skorzystaj z informacji w tej sekcji, aby pracować z ustawieniami automatycznego skalowania. Należy modyfikować tych przykładów.

### <a name="get-autoscale-settings-for-a-resource-group"></a>Pobierz ustawienia skalowania automatycznego dla grupy zasobów.

```azurecli
az monitor autoscale list --resource-group <group name>
```

### <a name="get-autoscale-settings-by-name-in-a-resource-group"></a>Pobierz ustawienia skalowania automatycznego według nazwy w grupie zasobów

```azurecli
az monitor autoscale show --name <settings name> --resource-group <group name>
```

### <a name="set-autoscale-settings"></a>Ustawienia skalowania automatycznego

```azurecli
az monitor autoscale create --name <settings name> --resource-group <group name> \
    --count <# instances> \
    --resource <target resource ID>
```
