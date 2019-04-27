---
title: Przykłady — szybki start usługi Azure Monitor interfejsu wiersza polecenia
description: Przykładowe polecenia interfejsu wiersza polecenia dla funkcji usługi Azure Monitor. Usługa Azure Monitor to usługa Microsoft Azure, dzięki czemu można wysłać powiadomienia o alertach, wywołania adresu URL sieci web na podstawie wartości danych telemetrycznych skonfigurowane i automatycznego skalowania usługi w chmurze, maszyny wirtualne i aplikacje sieci Web.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: robb
ms.component: ''
ms.openlocfilehash: fa3293346fee6f6666db01dab5587dd760df84b2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60740887"
---
# <a name="azure-monitor-cli-quick-start-samples"></a>Przykłady — szybki start usługi Azure Monitor interfejsu wiersza polecenia
W tym artykule przedstawiono przykładowe polecenia interfejsu wiersza polecenia (CLI), aby ułatwić dostęp do funkcji usługi Azure Monitor. Usługa Azure Monitor umożliwia automatyczne skalowanie usługi w chmurze, maszyny wirtualne i aplikacje sieci Web i wysyłać powiadomienia o alertach lub zadzwoń do adresu URL sieci web na podstawie wartości danych telemetrycznych skonfigurowany.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie została jeszcze zainstalowana wiersza polecenia platformy Azure, postępuj zgodnie z instrukcjami dotyczącymi [zainstalować interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Można również użyć [usługi Azure Cloud Shell](/azure/cloud-shell) do uruchamiania interfejsu wiersza polecenia jako środowisko interaktywne w przeglądarce. Zobacz pełną dokumentację wszystkich dostępnych poleceń w [odwołanie do wiersza polecenia platformy Azure Monitor](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest). 

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.
Pierwszym krokiem jest Zaloguj się do konta platformy Azure.

```azurecli
az login
```

Po uruchomieniu tego polecenia, musisz zalogować się za pośrednictwem zgodnie z instrukcjami na ekranie. Wszystkie polecenia działają w ramach subskrypcji domyślnej.

Aby wyświetlić szczegółowe informacje o Twojej bieżącej subskrypcji, użyj następującego polecenia.

```azurecli
az account show
```

Aby zmienić kontekst pracy do innej subskrypcji, użyj następującego polecenia.

```azurecli
az account set -s <Subscription ID or name>
```

Aby wyświetlić listę wszystkich obsługiwanych poleceń usługi Azure Monitor, wykonaj następujące czynności.

```azurecli
az monitor -h
```

## <a name="view-activity-log-for-a-subscription"></a>Wyświetl dziennik aktywności subskrypcji

Aby wyświetlić listę zdarzenia dziennika aktywności, wykonaj następujące czynności.

```azurecli
az monitor activity-log list
```

Spróbuj wykonać następujące czynności, aby wyświetlić wszystkie dostępne opcje.

```azurecli
az monitor activity-log list -h
```

Oto przykład dzienniki listy według grupy zasobów

```azurecli
az monitor activity-log list --resource-group <group name>
```

Przykład listy dzienniki przez obiekt wywołujący

```azurecli
az monitor activity-log list --caller myname@company.com
```

Przykład listy dzienniki przez obiekt wywołujący na typu zasobu w zakresie dat.

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>Praca z alertami 
> [!NOTE]
> W tej chwili tylko alerty (klasyczne) jest obsługiwana w interfejsie wiersza polecenia. 

### <a name="get-alert-classic-rules-in-a-resource-group"></a>Pobierz reguły alertów (klasyczne) w grupie zasobów

```azurecli
az monitor activity-log alert list --resource-group <group name>
az monitor activity-log alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-metric-alert-classic-rule"></a>Utwórz regułę alertu metryki (klasyczne)

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-an-alert-classic-rule"></a>Usuwanie reguły alertów (klasyczne)

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="log-profiles"></a>Profile dziennika

Skorzystaj z informacji w tej sekcji, aby pracować z profile dziennika.

### <a name="get-a-log-profile"></a>Pobieranie profilu dziennika

```azurecli
az monitor log-profiles list
az monitor log-profiles show --name <profile name>
```

### <a name="add-a-log-profile-with-retention"></a>Dodaj profil dziennika z przechowywaniem

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true \
    --storage-account-id <storage account ID to store the logs in>
```

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>Dodaj profil dziennika, przechowywania i Centrum zdarzeń

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true
    --storage-account-id <storage account ID to store the logs in>
    --service-bus-rule-id <service bus rule ID to stream to>
```

### <a name="remove-a-log-profile"></a>Usuwanie profilu dziennika

```azurecli
az monitor log-profiles delete --name <profile name>
```

## <a name="diagnostics"></a>Diagnostyka

Skorzystaj z informacji w tej sekcji, aby pracować z ustawień diagnostycznych.

### <a name="get-a-diagnostic-setting"></a>Pobierz ustawienie diagnostyczne

```azurecli
az monitor diagnostic-settings list --resource <target resource ID>
```

### <a name="create-a-diagnostic-log-setting"></a>Tworzenie ustawienia dziennika diagnostycznego 

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

Skorzystaj z informacji w tej sekcji, aby pracować z ustawień automatycznego skalowania. Należy modyfikować tych przykładów.

### <a name="get-autoscale-settings-for-a-resource-group"></a>Pobieranie ustawień automatycznego skalowania dla grupy zasobów

```azurecli
az monitor autoscale list --resource-group <group name>
```

### <a name="get-autoscale-settings-by-name-in-a-resource-group"></a>Pobieranie ustawień automatycznego skalowania o nazwie w grupie zasobów

```azurecli
az monitor autoscale show --name <settings name> --resource-group <group name>
```

### <a name="set-autoscale-settings"></a>Ustawienia automatycznego skalowania

```azurecli
az monitor autoscale create --name <settings name> --resource-group <group name> \
    --count <# instances> \
    --resource <target resource ID>
```
