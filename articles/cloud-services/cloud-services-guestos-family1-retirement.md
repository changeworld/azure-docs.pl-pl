---
title: Zwróć uwagę, 1 wycofanie rodziny systemów operacyjnych gościa | Dokumentacja firmy Microsoft
description: Zawiera informacje dotyczące po stało się wycofanie 1 rodziny systemów operacyjnych gościa platformy Azure i jak określić, jeśli ma wpływ
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
editor: ''
ms.assetid: 37b422e9-0713-4a81-a942-f553ef478064
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 5/21/2017
ms.author: raiye
ms.openlocfilehash: d6429766b6aac547fd99279659acb1067298e77c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60518966"
---
# <a name="guest-os-family-1-retirement-notice"></a>Powiadomienie o wycofaniu 1 rodziny systemów operacyjnych gościa
Wycofanie systemu operacyjnego rodziny 1 został po raz pierwszy 1 czerwca 2013 r.

**2 września 2014 r.** systemu operacyjnego gościa platformy Azure (systemu operacyjnego gościa) rodziny 1.x, która jest oparta na system operacyjny Windows Server 2008, oficjalnie została wycofana. Wszystkie próby wdrażać nowe usługi lub uaktualnienia istniejących usług za pomocą rodziny 1 zakończy się niepowodzeniem z komunikatem o błędzie informujący o tym, czy 1 rodziny systemów operacyjnych gościa został wycofany.

**3 listopada 2014 r.** zakończenia wsparcia dodatkowego dla 1 rodziny systemów operacyjnych gościa i w pełni została wycofana. Ma wpływ na wszystkie usługi nadal na 1 rodziny. Firma Microsoft może zatrzymać tych usług w dowolnym momencie. Nie ma żadnej gwarancji, które usługi będzie nadal działać, o ile nie należy ręcznie uaktualnić je samodzielnie.

Jeśli masz dodatkowe pytania, odwiedź stronę [fora usług w chmurze](https://social.msdn.microsoft.com/Forums/home?forum=windowsazuredevelopment&filter=alltypes&sort=lastpostdesc) lub [skontaktuj się z działem pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/).

## <a name="are-you-affected"></a>Możesz dotyczy problem?
Usługi w chmurze są zmienione, jeśli ma zastosowanie jednej z następujących czynności:

1. Ma wartość "rodziny systemów operacyjnych ="1"jawnie określone w pliku ServiceConfiguration.cscfg dla usługi w chmurze.
2. Nie masz wartość dla rodziny systemów operacyjnych jawnie określone w pliku ServiceConfiguration.cscfg dla usługi w chmurze. Obecnie system używa wartości domyślnej "1" w tym przypadku.
3. Witryna Azure portal Wyświetla wartość rodzina systemu operacyjnego gościa jako "Systemu Windows Server 2008".

Aby dowiedzieć się, które usługi w chmurze działają rodziny systemów operacyjnych, które, można uruchomić poniższy skrypt w programie Azure PowerShell, jednak należy [Konfigurowanie programu Azure PowerShell](/powershell/azureps-cmdlets-docs) pierwszy. Aby uzyskać więcej informacji na temat skryptu, zobacz [Azure gościa systemu operacyjnego rodziny 1 zakończenia życia: Czerwca 2014 r.](https://blogs.msdn.com/b/ryberry/archive/2014/04/02/azure-guest-os-family-1-end-of-life-june-2014.aspx).

```Powershell
foreach($subscription in Get-AzureSubscription) {
    Select-AzureSubscription -SubscriptionName $subscription.SubscriptionName

    $deployments=get-azureService | get-azureDeployment -ErrorAction Ignore | where {$_.SdkVersion -NE ""}

    $deployments | ft @{Name="SubscriptionName";Expression={$subscription.SubscriptionName}}, ServiceName, SdkVersion, Slot, @{Name="osFamily";Expression={(select-xml -content $_.configuration -xpath "/ns:ServiceConfiguration/@osFamily" -namespace $namespace).node.value }}, osVersion, Status, URL
}
```

Usługi w chmurze będzie wpływ wycofanie 1 rodziny systemów operacyjnych, jeśli kolumna rodziny systemów operacyjnych w danych wyjściowych skryptu jest pusta lub zawiera wartość "1".

## <a name="recommendations-if-you-are-affected"></a>Zalecenia, jeśli ma wpływ
Zaleca się, że migrację usługi ról usługi w chmurze do jednego z obsługiwanych rodzin systemu operacyjnego gościa:

**System operacyjny gościa rodziny 4.x** -Windows Server 2012 R2 *(zalecane)*

1. Upewnij się, że aplikacja korzysta z zestawu SDK 2.1 lub nowszej za pomocą .NET framework 4.0, 4.5 lub 4.5.1.
2. Ustaw atrybut rodziny systemów operacyjnych na "4" w pliku ServiceConfiguration.cscfg i ponowne wdrażanie usługi w chmurze.

**System operacyjny gościa rodziny 3.x** -Windows Server 2012

1. Upewnij się, że aplikacja korzysta z zestawu SDK 1.8 lub nowszego z programem .NET framework 4.0 lub 4.5.
2. Ustaw atrybut rodziny systemów operacyjnych z "3" w pliku ServiceConfiguration.cscfg i ponowne wdrażanie usługi w chmurze.

**System operacyjny gościa rodziny 2.x** -Windows Server 2008 R2

1. Upewnij się, aplikacja korzysta z zestawu SDK 1.3 i nowsze z programem .NET framework 3.5 lub 4.0.
2. Ustaw atrybut rodziny systemów operacyjnych na "2" w pliku ServiceConfiguration.cscfg i ponowne wdrażanie usługi w chmurze.

## <a name="extended-support-for-guest-os-family-1-ended-nov-3-2014"></a>Rozszerzona pomoc techniczna dla 1 rodziny systemów operacyjnych gościa zakończył się 3 listopada 2014 r.
Usługi w chmurze na rodziny systemów operacyjnych gościa w wersji 1 nie są już obsługiwane. Migrowanie wyłączone rodziny 1 tak szybko, jak to możliwe, aby uniknąć przerw w działaniu usługi.  

## <a name="next-steps"></a>Kolejne kroki
Przejrzyj najnowsze [wersji systemu operacyjnego gościa](cloud-services-guestos-update-matrix.md).
