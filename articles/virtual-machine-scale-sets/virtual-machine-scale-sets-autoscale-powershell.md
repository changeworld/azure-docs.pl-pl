---
title: Zestawy skalowania automatycznego skalowania maszyny wirtualnej z programu Azure PowerShell | Dokumentacja firmy Microsoft
description: "Ustawia sposobu tworzenia reguł skalowania automatycznego skalowania maszyn wirtualnych przy użyciu programu Azure PowerShell"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 88886cad-a2f0-46bc-8b58-32ac2189fc93
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: iainfou
ms.openlocfilehash: 8928e56f353858234db314714d411a9c2990eb4e
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2017
---
# <a name="automatically-scale-a-virtual-machine-scale-set-with-azure-powershell"></a>Automatycznie skalować skali maszyny wirtualnej ustawić przy użyciu programu Azure PowerShell
Podczas tworzenia zestawu skalowania, należy zdefiniować liczba wystąpień maszyn wirtualnych, które chcesz uruchomić. Twoje żądanie aplikacji zmian, można automatycznie zwiększyć lub zmniejszyć liczbę wystąpień maszyn wirtualnych. Możliwość skalowania automatycznego umożliwia nadąża z popyt lub reagowania na zmiany wydajności aplikacji w całym cyklu życia aplikacji.

W tym artykule przedstawiono sposób tworzenia reguły automatycznego skalowania z programu Azure PowerShell monitorowania wydajności wystąpień maszyny Wirtualnej w zestawie skali. Te reguły automatycznego skalowania zwiększyć lub zmniejszyć liczbę wystąpień maszyn wirtualnych w odpowiedzi na te metryki wydajności. Można również wykonać te kroki [Azure CLI 2.0](virtual-machine-scale-sets-autoscale-cli.md) lub [portalu Azure](virtual-machine-scale-sets-autoscale-portal.md).


## <a name="prerequisites"></a>Wymagania wstępne
Aby utworzyć zasady automatycznego skalowania, należy istniejącej maszyny wirtualnej zestawu skali. Możesz utworzyć zestaw o skali [portalu Azure](virtual-machine-scale-sets-create-portal.md), [programu Azure PowerShell](virtual-machine-scale-sets-create-powershell.md), lub [Azure CLI 2.0](virtual-machine-scale-sets-create-cli.md).

Aby ułatwić tworzenie reguł skalowania automatycznego, zdefiniuj niektóre zmienne dla zestawu skalowania. W poniższym przykładzie zdefiniowano zmienne zestaw o nazwie skalowania *myScaleSet* w grupie zasobów o nazwie *myResourceGroup* i *wschodnie stany USA* regionu. Identyfikator są uzyskiwane z subskrypcji [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription). Jeśli masz wiele subskrypcji skojarzonych z Twoim kontem, zwracany jest tylko pierwszy subskrypcji. Ustawienia nazwy i identyfikator subskrypcji:

```powershell
$mySubscriptionId = (Get-AzureRmSubscription).Id
$myResourceGroup = "myResourceGroupScaleSet"
$myScaleSet = "myScaleSet"
$myLocation = "East US"
```


## <a name="create-a-rule-to-automatically-scale-out"></a>Tworzenie reguły automatycznie skalować w poziomie
Jeśli Twoje żądanie aplikacji zwiększa, obciążenie wystąpień maszyn wirtualnych w skali sieci ustawić zwiększa. Jeśli ta zwiększone obciążenie jest spójne, a nie tylko krótki żądanie, można skonfigurować reguł skalowania automatycznego, aby zwiększyć liczbę wystąpień maszyny Wirtualnej w zestawie skalowania. Gdy te wystąpień maszyn wirtualnych są tworzone i wdrożonych aplikacji, zestaw skali rozpoczyna Dystrybuuj ruch do nich za pośrednictwem usługi równoważenia obciążenia. Można określić, jakie metryk do monitorowania, takie jak procesor CPU lub dysku, jak długo obciążenia aplikacji muszą spełniać podany próg i ustaw liczbę wystąpień maszyny Wirtualnej, aby dodać do skali.

Ta funkcja pozwala utworzyć regułę z [AzureRmAutoscaleRule nowy](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule) , która zwiększa liczbę wystąpień maszyn wirtualnych w skali ustawić, gdy średni obciążenie procesora CPU jest większa niż 70% w okresie 5 minut. Gdy zasada wyzwala, 20% zwiększa się liczba wystąpień maszyn wirtualnych. W zestawach skali z mniejszą liczbą wystąpień maszyn wirtualnych, można Opuść `-ScaleActionScaleType` i określić tylko `-ScaleActionValue` zwiększenie przez *1* lub *2* wystąpień. W zestawach skali z dużą liczbą wystąpień maszyn wirtualnych, zwiększenie 10% lub 20% wystąpień maszyn wirtualnych może być bardziej odpowiednie.

Dla tej reguły są używane następujące parametry:

| Parametr               | Wyjaśnienie                                                                                                         | Wartość          |
|-------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
| *-MetricName*           | Metryki wydajności do monitorowania i zastosować skali ustawić akcje.                                                   | Procentowe użycie procesora CPU |
| *-Ziarnem czasu*            | Jak często metryki są zbierane dla analizy.                                                                   | 1 minuta       |
| *-MetricStatistic*      | Określa, jak zebranych metryk powinny być agregowane dla celów analizy.                                                | Średnia        |
| *-TimeWindow*           | Ilość czasu monitorowane przed wartości metryki i próg są porównywane.                                   | 10 minut      |
| *-— Operator*             | Operator użyty do porównania danych metryki wartość progową.                                                     | Większa niż   |
| *— Próg*            | Wartość, która powoduje, że reguły automatycznego skalowania akcja wyzwalacza.                                                      | 70%            |
| *-ScaleActionDirection* | Określa, czy zestaw skalowania należy skalować w górę lub w dół gdy ta reguła ma zastosowanie.                                             | Zwiększ       |
| *— ScaleActionScaleType* | Wskazuje, czy liczba wystąpień maszyn wirtualnych powinny być zmieniane przez wartość procentowa.                                 | Procent zmiany |
| *-ScaleActionValue*     | Gdy zasada wyzwala, należy zmienić wartości procentowej wystąpień maszyny Wirtualnej.                                            | 20             |
| *-ScaleActionCooldown*  | Ilość czasu oczekiwania przed reguła została zastosowana ponownie, aby akcji skalowania automatycznego ma czas zaczęły obowiązywać. | 5 minut      |

Poniższy przykład tworzy obiekt o nazwie *myRuleScaleOut* przechowuje tej skali reguła. *- MetricResourceId* używa zmiennych wcześniej zdefiniowany dla Identyfikatora subskrypcji, nazwa grupy zasobów i skalowania Nazwa zestawu:

```powershell
$myRuleScaleOut = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -TimeGrain 00:01:00 `
  -MetricStatistic Average `
  -TimeWindow 00:10:00 `
  -Operator GreaterThan `
  -Threshold 70 `
  -ScaleActionDirection Increase `
  –ScaleActionScaleType PercentChangeCount `
  -ScaleActionValue 20 `
  -ScaleActionCooldown 00:05:00
```


## <a name="create-a-rule-to-automatically-scale-in"></a>Tworzenie reguły automatycznie skalować w
Na wieczorem lub w weekendy Twoje żądanie aplikacji mogą się zmniejszyć. Jeśli to zmniejszyć obciążenie jest spójne w danym okresie czasu, można skonfigurować reguły automatycznego skalowania, aby zmniejszyć liczbę wystąpień maszyny Wirtualnej w zestawie skalowania. Ta akcja skali zmniejsza koszt do uruchomienia na skalę ustawione jako tylko Uruchom liczba wystąpień wymaganych do bieżącego zapotrzebowania.

Utworzyć regułę z [AzureRmAutoscaleRule nowy](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule) zmniejsza się liczba wystąpień maszyn wirtualnych w skali ustawić, gdy średni obciążenie procesora CPU następnie spadnie poniżej 30% w okresie 10 minut. Gdy zasada wyzwala, 20% zmniejsza się liczba wystąpień maszyn wirtualnych. Poniższy przykład tworzy obiekt o nazwie *myRuleScaleDown* przechowuje tej skali reguła. *- MetricResourceId* używa zmiennych wcześniej zdefiniowany dla Identyfikatora subskrypcji, nazwa grupy zasobów i skalowania Nazwa zestawu:

```powershell
$myRuleScaleIn = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -Operator LessThan `
  -MetricStatistic Average `
  -Threshold 30 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:10:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Decrease `
  –ScaleActionScaleType PercentChangeCount `
  -ScaleActionValue 20
```


## <a name="define-an-autoscale-profile"></a>Zdefiniuj profilów skalowania automatycznego
Aby skojarzyć reguł skalowania automatycznego z zestawu skalowania, należy utworzyć profil. Profilów skalowania automatycznego definiuje domyślny, minimalną i maksymalną skalę zestawu pojemności i kojarzy reguł skalowania automatycznego. Utwórz profil skalowania automatycznego z [AzureRmAutoscaleProfile nowy](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleProfile). Poniższy przykład przedstawia możliwości domyślne oraz minimum, *2* wirtualna wystąpień i maksymalnie *10*. Skalowanie w poziomie, a następnie dołączonych skali w regułach utworzony w poprzednim kroku:

```powershell
$myScaleProfile = New-AzureRmAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rules $myRuleScaleOut,$myRuleScaleIn `
  -Name "autoprofile"
```


## <a name="apply-autoscale-rules-to-a-scale-set"></a>Zastosuj reguły skalowania automatycznego do zestawu skalowania
Ostatnim krokiem jest dotyczyć profilów skalowania automatycznego do zestawu skalowania. Na skalę będzie mógł automatycznie skalować lub na podstawie zapotrzebowania aplikacji. Zastosuj profil skalowania automatycznego z [AzureRmAutoscaleSetting Dodaj](/powershell/module/AzureRM.Insights/Add-AzureRmAutoscaleSetting) w następujący sposób:

```powershell
Add-AzureRmAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -AutoscaleProfiles $myScaleProfile
```


## <a name="monitor-number-of-instances-in-a-scale-set"></a>Monitor liczby wystąpień w zestawie skalowania
Aby wyświetlić liczbę i stan wystąpień maszyn wirtualnych, wyświetlić listę wystąpień w na skalę ustawiony za pomocą [Get-AzureRmVmssVM](/powershell/module/AzureRM.Compute/Get-AzureRmVmssVM). Stan wskazuje, czy wystąpienie maszyny Wirtualnej jest udostępniania automatycznie zestaw skalowania skaluje się lub jest anulowania obsługi jako skali jest automatycznie skalowany w. Poniższy przykład wyświetla stan wystąpienia maszyny Wirtualnej dla zestaw o nazwie skalowania *myScaleSet* w grupie zasobów o nazwie *myResourceGroup*:

```powershell
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```


## <a name="autoscale-based-on-a-schedule"></a>Funkcja automatycznego skalowania zgodnie z harmonogramem
Poprzednich przykładach automatycznie przeskalowany skali ustawić przychodzący lub wychodzący z podstawowego hosta metryki, takie jak użycie procesora CPU. Można również tworzyć reguły automatycznego skalowania, oparte na harmonogramów. Te zasady oparte na harmonogramie umożliwiają automatyczne skalowanie liczby wystąpień maszyny Wirtualnej przed oczekiwanego wzrostu popytu aplikacji, takich jak podstawowe godziny pracy, a następnie automatycznie skalować liczbę wystąpień w czasie, która będzie mniej żądanie, takich jak weekend.

Aby utworzyć zasady automatycznego skalowania w oparciu metryki harmonogram, a nie na hoście, użyj portalu Azure. Zasady oparte na harmonogramie obecnie nie można utworzyć przy użyciu programu Azure PowerShell.


## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób skalowanie w poziomie i zwiększyć lub zmniejszyć za pomocą reguł skalowania automatycznego *numer* wystąpień maszyn wirtualnych w skali sieci ustawiony. Możliwe jest także skalowanie w pionie Aby zwiększyć lub zmniejszyć wystąpienia maszyny Wirtualnej *rozmiar*. Aby uzyskać więcej informacji, zobacz [pionowy automatycznie skalowana za pomocą zestawów skali maszyny wirtualnej](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Aby uzyskać informacje na temat zarządzania wystąpień maszyn wirtualnych, zobacz [ustawia Zarządzaj skalowania maszyn wirtualnych przy użyciu programu Azure PowerShell](virtual-machine-scale-sets-windows-manage.md).

Aby dowiedzieć się, jak generować alerty, kiedy Twoje skalowania automatycznego zasady wyzwalacza, zobacz [użyć akcji skalowania automatycznego do wysyłania wiadomości e-mail i elementu webhook powiadomień o alertach w monitorze Azure](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md). Możesz również [dzienników inspekcji używany do wysyłania wiadomości e-mail i elementu webhook powiadomień o alertach w monitorze Azure](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).
