---
title: Przykłady szybkiego uruchamiania interfejsu wiersza polecenia usługi Azure Monitor
description: Przykładowe polecenia interfejsu wiersza polecenia dla funkcji usługi Azure Monitor. Usługa Azure Monitor to usługa Platformy Microsoft Azure, która umożliwia wysyłanie powiadomień alertów, wywoływanie adresów URL sieci web na podstawie wartości skonfigurowanych danych telemetrycznych oraz usługi w chmurze automatycznej skali, maszyny wirtualne i aplikacje sieci Web.
ms.subservice: ''
ms.topic: conceptual
ms.date: 05/16/2018
ms.openlocfilehash: 69687cee8b8a907b82f2c848242ac64d54dedb87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248933"
---
# <a name="azure-monitor-cli-quick-start-samples"></a>Przykłady szybkiego uruchamiania interfejsu wiersza polecenia usługi Azure Monitor
W tym artykule przedstawiono przykładowe polecenia interfejsu wiersza polecenia (CLI), które ułatwią dostęp do funkcji usługi Azure Monitor. Usługa Azure Monitor umożliwia skalowanie usług w chmurze, maszyn wirtualnych i aplikacji sieci Web oraz wysyłanie powiadomień alertów lub wywoływanie adresów URL sieci web na podstawie wartości skonfigurowanych danych telemetrycznych.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie zainstalowano jeszcze interfejsu wiersza polecenia platformy Azure, postępuj zgodnie z instrukcjami dotyczącymi [instalowania interfejsu wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli) Można również użyć [usługi Azure Cloud Shell](/azure/cloud-shell) do uruchomienia interfejsu wiersza polecenia jako interaktywne środowisko w przeglądarce. Zobacz pełne odwołanie do wszystkich dostępnych poleceń w [odwołaniu do interfejsu wiersza polecenia usługi Azure Monitor](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest). 

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.
Pierwszym krokiem jest zalogowanie się do konta platformy Azure.

```azurecli
az login
```

Po uruchomieniu tego polecenia musisz zalogować się za pomocą instrukcji wyświetlanych na ekranie. Wszystkie polecenia działają w kontekście subskrypcji domyślnej.

Aby wyświetlić szczegółowe informacje o bieżącej subskrypcji, użyj następującego polecenia.

```azurecli
az account show
```

Aby zmienić kontekst pracy na inną subskrypcję, należy użyć następującego polecenia.

```azurecli
az account set -s <Subscription ID or name>
```

Aby wyświetlić listę wszystkich obsługiwanych poleceń usługi Azure Monitor, wykonaj następujące czynności.

```azurecli
az monitor -h
```

## <a name="view-activity-log-for-a-subscription"></a>Wyświetlanie dziennika aktywności dla subskrypcji

Aby wyświetlić listę zdarzeń dziennika aktywności, wykonaj następujące czynności.

```azurecli
az monitor activity-log list
```

Spróbuj wykonać następujące czynności, aby wyświetlić wszystkie dostępne opcje.

```azurecli
az monitor activity-log list -h
```

Oto przykład listy dzienników przez resourceGroup

```azurecli
az monitor activity-log list --resource-group <group name>
```

Przykład listy dzienników według osoby dzwoniącej

```azurecli
az monitor activity-log list --caller myname@company.com
```

Przykład listy dzienników przez wywołującego w typie zasobu w zakresie dat

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>Praca z alertami 
> [!NOTE]
> Tylko alerty (klasyczne) jest obsługiwany w interfejsie wiersza polecenia w tej chwili. 

### <a name="get-alert-classic-rules-in-a-resource-group"></a>Uzyskaj reguły alertów (klasyczne) w grupie zasobów

```azurecli
az monitor activity-log alert list --resource-group <group name>
az monitor activity-log alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-metric-alert-classic-rule"></a>Tworzenie reguły alertu metryki (klasycznej)

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-an-alert-classic-rule"></a>Usuwanie reguły alertu (klasycznej)

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="log-profiles"></a>Profile dzienników

Użyj informacji w tej sekcji do pracy z profilami dziennika.

### <a name="get-a-log-profile"></a>Uzyskaj profil dziennika

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

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>Dodawanie profilu dziennika z przechowywaniem i eventhubem

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

Użyj informacji w tej sekcji, aby pracować z ustawieniami diagnostycznymi.

### <a name="get-a-diagnostic-setting"></a>Pobierz ustawienie diagnostyczne

```azurecli
az monitor diagnostic-settings list --resource <target resource ID>
```

### <a name="create-a-diagnostic-setting"></a>Tworzenie ustawienia diagnostycznego 

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

### <a name="delete-a-diagnostic-setting"></a>Usuwanie ustawienia diagnostycznego

```azurecli
az monitor diagnostic-settings delete --name <diagnostic name> \
    --resource <target resource ID>
```

## <a name="autoscale"></a>Automatyczne skalowanie

Użyj informacji w tej sekcji, aby pracować z ustawieniami skalowania automatycznego. Należy zmodyfikować te przykłady.

### <a name="get-autoscale-settings-for-a-resource-group"></a>Pobierz ustawienia skalowania automatycznego dla grupy zasobów

```azurecli
az monitor autoscale list --resource-group <group name>
```

### <a name="get-autoscale-settings-by-name-in-a-resource-group"></a>Automatyczne skalowanie ustawień według nazwy w grupie zasobów

```azurecli
az monitor autoscale show --name <settings name> --resource-group <group name>
```

### <a name="set-autoscale-settings"></a>Ustawianie ustawień skalowania automatycznego

```azurecli
az monitor autoscale create --name <settings name> --resource-group <group name> \
    --count <# instances> \
    --resource <target resource ID>
```

