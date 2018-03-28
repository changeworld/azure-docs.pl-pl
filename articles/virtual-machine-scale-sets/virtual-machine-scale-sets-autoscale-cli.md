---
title: Zestawy skalowania automatycznego skalowania maszyny wirtualnej z wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: "Jak utworzyć zasady automatycznego skalowania skali maszyny wirtualnej ustawia 2.0 interfejsu wiersza polecenia platformy Azure"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 83e93d9c-cac0-41d3-8316-6016f5ed0ce4
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: iainfou
ms.openlocfilehash: 8552f6b2723fef2c61d49a34d2d60c2a6c209a32
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2017
---
# <a name="automatically-scale-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>Automatycznie skalować skalowania maszyny wirtualnej, ustaw 2.0 interfejsu wiersza polecenia platformy Azure
Podczas tworzenia zestawu skalowania, należy zdefiniować liczba wystąpień maszyn wirtualnych, które chcesz uruchomić. Twoje żądanie aplikacji zmian, można automatycznie zwiększyć lub zmniejszyć liczbę wystąpień maszyn wirtualnych. Możliwość skalowania automatycznego umożliwia nadąża z popyt lub reagowania na zmiany wydajności aplikacji w całym cyklu życia aplikacji.

W tym artykule przedstawiono sposób tworzenia reguły automatycznego skalowania 2.0 interfejsu wiersza polecenia Azure, która monitorowania wydajności wystąpień maszyny Wirtualnej w zestawie skali. Te reguły automatycznego skalowania zwiększyć lub zmniejszyć liczbę wystąpień maszyn wirtualnych w odpowiedzi na te metryki wydajności. Można również wykonać te kroki [programu Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md) lub [portalu Azure](virtual-machine-scale-sets-autoscale-portal.md).


## <a name="prerequisites"></a>Wymagania wstępne
Aby utworzyć zasady automatycznego skalowania, należy istniejącej maszyny wirtualnej zestawu skali. Możesz utworzyć zestaw o skali [portalu Azure](virtual-machine-scale-sets-create-portal.md), [Azure CLI 2.0](virtual-machine-scale-sets-create-cli.md), lub [programu Azure PowerShell](virtual-machine-scale-sets-create-powershell.md).

Aby ułatwić tworzenie reguł skalowania automatycznego, zdefiniuj niektóre zmienne dla zestawu skalowania. W poniższym przykładzie zdefiniowano zmienne zestaw o nazwie skalowania *myScaleSet* w grupie zasobów o nazwie *myResourceGroup* i *eastus* regionu. Identyfikator są uzyskiwane z subskrypcji [Pokaż konto az](/cli/azure/account#az_account_show). Jeśli masz wiele subskrypcji skojarzonych z Twoim kontem, zwracany jest tylko pierwszy subskrypcji. Ustawienia nazwy i identyfikator subskrypcji:

```azurecli
sub=$(az account show --query id -o tsv)
resourcegroup_name="myResourceGroup"
scaleset_name="myScaleSet"
location_name="eastus"
```

## <a name="define-an-autoscale-profile"></a>Zdefiniuj profilów skalowania automatycznego
Reguły automatycznego skalowania są wdrażane w formacie JSON (JavaScript Object Notation) 2.0 interfejsu wiersza polecenia platformy Azure. Pełne JSON, który definiuje i wdraża zasady automatycznego skalowania można znaleźć w dalszej części tego artykułu. 

Początek profilów skalowania automatycznego definiuje domyślną, pojemność zestawu skalowania minimalną i maksymalną. Poniższy przykład przedstawia możliwości domyślne oraz minimum, *2* wirtualna wystąpień i maksymalnie *10*:

```json
{
  "name": "autoscale rules",
  "capacity": {
    "minimum": "2",
    "maximum": "10",
    "default": "2"
  }
}
```


## <a name="create-a-rule-to-automatically-scale-out"></a>Tworzenie reguły automatycznie skalować w poziomie
Jeśli Twoje żądanie aplikacji zwiększa, obciążenie wystąpień maszyn wirtualnych w skali sieci ustawić zwiększa. Jeśli ta zwiększone obciążenie jest spójne, a nie tylko krótki żądanie, można skonfigurować reguł skalowania automatycznego, aby zwiększyć liczbę wystąpień maszyny Wirtualnej w zestawie skalowania. Gdy te wystąpień maszyn wirtualnych są tworzone i wdrożonych aplikacji, zestaw skali rozpoczyna Dystrybuuj ruch do nich za pośrednictwem usługi równoważenia obciążenia. Można określić, jakie metryk do monitorowania, takie jak procesor CPU lub dysku, jak długo obciążenia aplikacji muszą spełniać podany próg i ustaw liczbę wystąpień maszyny Wirtualnej, aby dodać do skali.

Ta funkcja pozwala utworzyć regułę, która zwiększa się liczba wystąpień maszyn wirtualnych w skali ustawić, gdy średni obciążenie procesora CPU jest większa niż 70% w okresie 10 minut. Gdy zasada wyzwala, 20% zwiększa się liczba wystąpień maszyn wirtualnych. W zestawach skali z mniejszą liczbą wystąpień maszyn wirtualnych, można ustawić `type` do *ChangeCount* i zwiększyć `value` przez *1* lub *2* wystąpień. W zestawach skali z dużą liczbą wystąpień maszyn wirtualnych, zwiększenie 10% lub 20% wystąpień maszyn wirtualnych może być bardziej odpowiednie.

Dla tej reguły są używane następujące parametry:

| Parametr         | Wyjaśnienie                                                                                                         | Wartość           |
|-------------------|---------------------------------------------------------------------------------------------------------------------|-----------------|
| *metricName*      | Metryki wydajności do monitorowania i zastosować skali ustawić akcje.                                                   | Procentowe użycie procesora CPU  |
| *ziarnem czasu*       | Jak często metryki są zbierane dla analizy.                                                                   | 1 minuta        |
| *timeAggregation* | Określa, jak zebranych metryk powinny być agregowane dla celów analizy.                                                | Średnia         |
| *timeWindow*      | Ilość czasu monitorowane przed wartości metryki i próg są porównywane.                                   | 10 minut      |
| *operator*        | Operator użyty do porównania danych metryki wartość progową.                                                     | Większa niż    |
| *Próg*       | Wartość, która powoduje, że reguły automatycznego skalowania akcja wyzwalacza.                                                      | 70%             |
| *Kierunek*       | Określa, czy zestaw skalowania należy skalować w górę lub w dół gdy ta reguła ma zastosowanie.                                             | Zwiększ        |
| *Typ*            | Wskazuje, czy liczba wystąpień maszyn wirtualnych powinny być zmieniane przez wartość procentowa.                                 | Procent zmiany  |
| *wartość*           | Jak wiele wystąpień maszyn wirtualnych powinien być skalowany w górę lub w dół, gdy ta reguła ma zastosowanie.                                            | 20              |
| *cooldown*        | Ilość czasu oczekiwania przed reguła została zastosowana ponownie, aby akcji skalowania automatycznego ma czas zaczęły obowiązywać. | 5 minut       |

W poniższym przykładzie zdefiniowano reguły do skalowania w poziomie liczba wystąpień maszyn wirtualnych. *MetricResourceUri* używa zmiennych wcześniej zdefiniowany dla Identyfikatora subskrypcji, nazwa grupy zasobów i skalowania Nazwa zestawu:

```json
{
  "metricTrigger": {
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
    "metricResourceLocation": "'$location_name'",
    "timeGrain": "PT1M",
    "statistic": "Average",
    "timeWindow": "PT10M",
    "timeAggregation": "Average",
    "operator": "GreaterThan",
    "threshold": 70
  },
  "scaleAction": {
    "direction": "Increase",
    "type": "PercentChangeCount",
    "value": "20",
    "cooldown": "PT5M"
  }
}
```


## <a name="create-a-rule-to-automatically-scale-in"></a>Tworzenie reguły automatycznie skalować w
Na wieczorem lub w weekendy Twoje żądanie aplikacji mogą się zmniejszyć. Jeśli to zmniejszyć obciążenie jest spójne w danym okresie czasu, można skonfigurować reguły automatycznego skalowania, aby zmniejszyć liczbę wystąpień maszyny Wirtualnej w zestawie skalowania. Ta akcja skali zmniejsza koszt do uruchomienia na skalę ustawione jako tylko Uruchom liczba wystąpień wymaganych do bieżącego zapotrzebowania.

Utworzyć inną regułę, która zmniejsza liczbę wystąpień maszyn wirtualnych w skali ustawić, gdy średni obciążenie procesora CPU następnie spadnie poniżej 30% w okresie 10 minut. W poniższym przykładzie zdefiniowano reguły do skalowania w poziomie liczba wystąpień maszyn wirtualnych. *MetricResourceUri* używa zmiennych wcześniej zdefiniowany dla Identyfikatora subskrypcji, nazwa grupy zasobów i skalowania Nazwa zestawu:

```json
{
  "metricTrigger": {
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
    "metricResourceLocation": "'$location_name'",
    "timeGrain": "PT1M",
    "statistic": "Average",
    "timeWindow": "PT10M",
    "timeAggregation": "Average",
    "operator": "LessThan",
    "threshold": 30
  },
  "scaleAction": {
    "direction": "Decrease",
    "type": "PercentChangeCount",
    "value": "20",
    "cooldown": "PT5M"
  }
}
```


## <a name="apply-autoscale-rules-to-a-scale-set"></a>Zastosuj reguły skalowania automatycznego do zestawu skalowania
Ostatnim krokiem jest dotyczyć profilów skalowania automatycznego i reguł do zestawu skalowania. Na skalę będzie mógł automatycznie skalować lub na podstawie zapotrzebowania aplikacji. Zastosuj profil skalowania automatycznego z [utworzyć ustawienia skalowania automatycznego az monitora](/cli/azure/monitor/autoscale-settings#az_monitor_autoscale_settings_create) w następujący sposób. Pełna JSON korzysta z profilu i reguł zauważyć w poprzednich sekcjach.

```azurecli
az monitor autoscale-settings create \
    --resource-group myResourceGroup \
    --name autoscale \
    --parameters '{"autoscale_setting_resource_name": "autoscale",
      "enabled": true,
      "location": "'$location_name'",
      "notifications": [],
      "profiles": [
        {
          "name": "autoscale by percentage based on CPU usage",
          "capacity": {
            "minimum": "2",
            "maximum": "10",
            "default": "2"
          },
          "rules": [
            {
              "metricTrigger": {
                "metricName": "Percentage CPU",
                "metricNamespace": "",
                "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
                "metricResourceLocation": "'$location_name'",
                "timeGrain": "PT1M",
                "statistic": "Average",
                "timeWindow": "PT10M",
                "timeAggregation": "Average",
                "operator": "GreaterThan",
                "threshold": 70
              },
              "scaleAction": {
                "direction": "Increase",
                "type": "PercentChangeCount",
                "value": "20",
                "cooldown": "PT5M"
              }
            },
            {
              "metricTrigger": {
                "metricName": "Percentage CPU",
                "metricNamespace": "",
                "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
                "metricResourceLocation": "'$location_name'",
                "timeGrain": "PT1M",
                "statistic": "Average",
                "timeWindow": "PT10M",
                "timeAggregation": "Average",
                "operator": "LessThan",
                "threshold": 30
              },
              "scaleAction": {
                "direction": "Decrease",
                "type": "PercentChangeCount",
                "value": "20",
                "cooldown": "PT5M"
              }
            }
          ]
        }
      ],
      "tags": {},
      "target_resource_uri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'"
    }'
```


## <a name="monitor-number-of-instances-in-a-scale-set"></a>Monitor liczby wystąpień w zestawie skalowania
Aby wyświetlić liczbę i stan wystąpień maszyn wirtualnych, wyświetlić listę wystąpień w na skalę ustawiony za pomocą [wystąpienia listy az vmss](/cli/azure/vmss#az_vmss_list_instances). Stan wskazuje, czy wystąpienie maszyny Wirtualnej jest udostępniania automatycznie zestaw skalowania skaluje się lub jest anulowania obsługi jako skali jest automatycznie skalowany w. Poniższy przykład wyświetla stan wystąpienia maszyny Wirtualnej dla zestaw o nazwie skalowania *myScaleSet* w grupie zasobów o nazwie *myResourceGroup*:

```azurecli
az vmss list-instances \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --output table
```


## <a name="autoscale-based-on-a-schedule"></a>Funkcja automatycznego skalowania zgodnie z harmonogramem
Poprzednich przykładach automatycznie przeskalowany skali ustawić przychodzący lub wychodzący z podstawowego hosta metryki, takie jak użycie procesora CPU. Można również tworzyć reguły automatycznego skalowania, oparte na harmonogramów. Te zasady oparte na harmonogramie umożliwiają automatyczne skalowanie liczby wystąpień maszyny Wirtualnej przed oczekiwanego wzrostu popytu aplikacji, takich jak podstawowe godziny pracy, a następnie automatycznie skalować liczbę wystąpień w czasie, która będzie mniej żądanie, takich jak weekend.

Aby używać reguł na podstawie harmonogramu automatycznego skalowania, Utwórz profil JSON, który definiuje liczbę wystąpień maszyn wirtualnych do uruchamiania dla stałej rozpoczęcia i zakończenia przedział czasu. W poniższym przykładzie zdefiniowano reguły do skalowania w poziomie do *10* wystąpień w *9* przedpołudnie każdego dnia pracy (od poniedziałku do piątku).

```json
{
  "name": "Scale out during each work day",
  "capacity": {
      "minimum": "10",
      "maximum": "10",
      "default": "10"
  },
  "rules": [],
  "recurrence": {
      "frequency": "Week",
      "schedule": {
          "timeZone": "Pacific Standard Time",
          "days": [
              "Monday",
              "Tuesday",
              "Wednesday",
              "Thursday",
              "Friday"
          ],
          "hours": [
              9
          ],
          "minutes": [
              0
          ]
      }
  }
}
```

Aby skalować w trakcie wieczorem, należy utworzyć inną regułę, która określa mniejszą liczbę wystąpień maszyn wirtualnych i czas rozpoczęcia odpowiednie.

Poniższy przykład pełną definiuje reguły do skalowania w poziomie, a następnie skali w, a następnie stosuje profilów skalowania automatycznego z [utworzyć ustawienia skalowania automatycznego az monitora](/cli/azure/monitor/autoscale-settings#az_monitor_autoscale_settings_create). W tym przykładzie zastępują reguły na podstawie metryki automatycznego skalowania utworzone w poprzednich przykładach. *MetricResourceUri* używa zmiennych wcześniej zdefiniowany dla Identyfikatora subskrypcji, nazwa grupy zasobów i skalowania Nazwa zestawu:

```azurecli
az monitor autoscale-settings create \
    --resource-group myResourceGroup \
    --name autoscale \
    --parameters '{"autoscale_setting_resource_name": "autoscale",
      "enabled": true,
      "location": "'$location_name'",
      "notifications": [],
      "profiles": [
        {
          "name": "Scale out during each work day",
          "capacity": {
            "minimum": "10",
            "maximum": "10",
            "default": "10"
          },
          "rules": [],
          "recurrence": {
            "frequency": "Week",
            "schedule": {
              "timeZone": "Pacific Standard Time",
              "days": [
                "Monday",
                "Tuesday",
                "Wednesday",
                "Thursday",
                "Friday"
              ],
              "hours": [
                9
              ],
              "minutes": [
                0
              ]
            }
          }
        },
        {
          "name": "Scale in during the evening",
          "capacity": {
            "minimum": "3",
            "maximum": "3",
            "default": "3"
          },
          "rules": [],
          "recurrence": {
            "frequency": "Week",
            "schedule": {
              "timeZone": "Pacific Standard Time",
              "days": [
                "Monday",
                "Tuesday",
                "Wednesday",
                "Thursday",
                "Friday"
              ],
              "hours": [
                18
              ],
              "minutes": [
                0
              ]
            }
          }
        }
      ],
      "tags": {},
      "target_resource_uri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'"
    }'
```


## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób skalowanie w poziomie i zwiększyć lub zmniejszyć za pomocą reguł skalowania automatycznego *numer* wystąpień maszyn wirtualnych w skali sieci ustawiony. Możliwe jest także skalowanie w pionie Aby zwiększyć lub zmniejszyć wystąpienia maszyny Wirtualnej *rozmiar*. Aby uzyskać więcej informacji, zobacz [pionowy automatycznie skalowana za pomocą zestawów skali maszyny wirtualnej](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Aby uzyskać informacje na temat zarządzania wystąpień maszyn wirtualnych, zobacz [ustawia Zarządzaj skalowania maszyn wirtualnych przy użyciu programu Azure PowerShell](virtual-machine-scale-sets-windows-manage.md).

Aby dowiedzieć się, jak generować alerty, kiedy Twoje skalowania automatycznego zasady wyzwalacza, zobacz [użyć akcji skalowania automatycznego do wysyłania wiadomości e-mail i elementu webhook powiadomień o alertach w monitorze Azure](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md). Możesz również [dzienników inspekcji używany do wysyłania wiadomości e-mail i elementu webhook powiadomień o alertach w monitorze Azure](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).
