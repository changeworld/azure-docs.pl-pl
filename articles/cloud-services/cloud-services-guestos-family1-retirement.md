---
title: Guest OS rodzina 1 informacja o emeryturze | Dokumenty firmy Microsoft
description: Zawiera informacje o tym, kiedy nastąpiła emerytura z rodziny 1 gościa usługi Azure i jak ustalić, czy dotyczy cię problem
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
ms.service: cloud-services
ms.topic: article
ms.date: 5/21/2017
ms.author: raiye
ms.openlocfilehash: ae2df6f47d99fc5d452a6d3ea70f2dd2e4e7416b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68359585"
---
# <a name="guest-os-family-1-retirement-notice"></a>Gość OS Rodzina 1 informacja o przejściu na emeryturę
Przejście na emeryturę OS Family 1 zostało po raz pierwszy ogłoszone 1 czerwca 2013 roku.

**2 września 2014 r.** Rodzina systemu operacyjnego Azure Guest (Guest OS) 1.x, która jest oparta na systemie operacyjnym Windows Server 2008, została oficjalnie wycofana. Wszystkie próby wdrożenia nowych usług lub uaktualnienia istniejących usług przy użyciu rodziny 1 zakończy się niepowodzeniem z komunikatem o błędzie informującym, że rodzina systemu operacyjnego gościa 1 została wycofana.

**3 listopada 2014 r.** Rozszerzona obsługa usługi Guest OS Family 1 została zakończona i jest całkowicie wycofana. Wpływ na wszystkie usługi nadal w rodzinie 1 będą miały wpływ. Możemy zatrzymać te usługi w dowolnym momencie. Nie ma żadnej gwarancji, że usługi będą nadal działać, chyba że ręcznie uaktualnić je samodzielnie.

Jeśli masz dodatkowe pytania, odwiedź [fora usług](https://social.msdn.microsoft.com/Forums/home?forum=windowsazuredevelopment&filter=alltypes&sort=lastpostdesc) w chmurze lub [skontaktuj się z pomocą techniczną platformy Azure.](https://azure.microsoft.com/support/options/)

## <a name="are-you-affected"></a>Czy dotyczy cię problem?
Usługi w chmurze są zagrożone, jeśli ma zastosowanie jedna z następujących sytuacji:

1. Masz wartość "osFamily = "1" jawnie określona w pliku ServiceConfiguration.cscfg dla usługi w chmurze.
2. Nie masz wartości osFamily jawnie określone w pliku ServiceConfiguration.cscfg dla usługi w chmurze. Obecnie system używa domyślnej wartości "1" w tym przypadku.
3. Portal Azure zawiera listę wartości rodziny systemu operacyjnego gościa jako "Windows Server 2008".

Aby znaleźć, które z usług w chmurze są uruchomione, które rodziny systemu operacyjnego, można uruchomić następujący skrypt w programie Azure PowerShell, mimo że należy [najpierw skonfigurować platformę Azure PowerShell.](/powershell/azureps-cmdlets-docs) Aby uzyskać więcej informacji na temat skryptu, zobacz [Rodzina systemu operacyjnego gościa azure 1 koniec życia: czerwiec 2014](https://blogs.msdn.com/b/ryberry/archive/2014/04/02/azure-guest-os-family-1-end-of-life-june-2014.aspx).

```Powershell
foreach($subscription in Get-AzureSubscription) {
    Select-AzureSubscription -SubscriptionName $subscription.SubscriptionName

    $deployments=get-azureService | get-azureDeployment -ErrorAction Ignore | where {$_.SdkVersion -NE ""}

    $deployments | ft @{Name="SubscriptionName";Expression={$subscription.SubscriptionName}}, ServiceName, SdkVersion, Slot, @{Name="osFamily";Expression={(select-xml -content $_.configuration -xpath "/ns:ServiceConfiguration/@osFamily" -namespace $namespace).node.value }}, osVersion, Status, URL
}
```

Na usługi w chmurze będzie mieć wpływ wycofanie z systemu operacyjnego Rodzina 1, jeśli kolumna osFamily w danych wyjściowych skryptu jest pusta lub zawiera wartość "1".

## <a name="recommendations-if-you-are-affected"></a>Zalecenia dotyczące
Zaleca się migrację ról usługi w chmurze do jednej z obsługiwanych rodzin systemu operacyjnego gościa:

**Rodzina systemów operacyjnych gościa 4.x** — Windows Server 2012 R2 *(zalecane)*

1. Upewnij się, że aplikacja używa SDK 2.1 lub nowszego z .NET framework 4.0, 4.5 lub 4.5.1.
2. Ustaw atrybut osFamily na "4" w pliku ServiceConfiguration.cscfg i ponownie wdrożyć usługę w chmurze.

**Rodzina systemów operacyjnych gościa 3.x** — Windows Server 2012

1. Upewnij się, że aplikacja używa SDK 1.8 lub nowszego z .NET framework 4.0 lub 4.5.
2. Ustaw atrybut osFamily na "3" w pliku ServiceConfiguration.cscfg i ponownie wdrożyć usługę w chmurze.

**Rodzina systemów operacyjnych gościa 2.x** — Windows Server 2008 R2

1. Upewnij się, że aplikacja używa SDK 1.3 i powyżej z .NET framework 3.5 lub 4.0.
2. Ustaw atrybut osFamily na "2" w pliku ServiceConfiguration.cscfg i ponownie wdrożyć usługę w chmurze.

## <a name="extended-support-for-guest-os-family-1-ended-nov-3-2014"></a>Rozszerzona obsługa rodziny systemu operacyjnego gościa 1 zakończona 3 listopada 2014
Usługi w chmurze w rodzinie systemu operacyjnego gościa 1 nie są już obsługiwane. Migracja z rodziny 1 tak szybko, jak to możliwe, aby uniknąć zakłóceń w świadczeniu usług.  

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z [najnowszymi wydaniami systemu operacyjnego dla gości](cloud-services-guestos-update-matrix.md).
