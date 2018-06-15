---
title: Zwróć uwagę, wycofywanie 1 rodziny systemów operacyjnych gościa | Dokumentacja firmy Microsoft
description: Informacje na temat po stało się wycofanie 1 rodziny systemu operacyjnego gościa Azure i sposobu ustalenia, czy ma wpływ
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
ms.openlocfilehash: 3178a09dab1cb972a3460d54dc9908fb95cce68b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23843133"
---
# <a name="guest-os-family-1-retirement-notice"></a>Powiadomienie wycofanie 1 rodziny systemu operacyjnego gościa
Wycofanie 1 rodziny System operacyjny najpierw ogłoszono w dniu 1 czerwca 2013.

**2 września 2014** systemu operacyjnego gościa Azure (systemu operacyjnego gościa) rodziny 1.x, które jest oparte na systemie operacyjnym Windows Server 2008, oficjalnie została wycofana. Wszystkie próby w przypadku wdrażania nowych usług lub uaktualnić istniejących usług przy użyciu 1 rodziny zakończy się niepowodzeniem z komunikatem o błędzie informujący, że 1 rodziny systemu operacyjnego gościa został wycofany.

**3 listopada 2014 r.** zakończone rozszerzoną obsługę 1 rodziny systemu operacyjnego gościa i jest w pełni wycofane. Wpłynie na wszystkie usługi nadal na 1 rodziny. Może zatrzymać tych usług w dowolnym momencie. Nie ma żadnej gwarancji, które usługi będzie nadal działać, chyba że należy ręcznie uaktualnić je samodzielnie.

Jeśli masz dodatkowe pytania, odwiedź stronę [fora usługi w chmurze](http://social.msdn.microsoft.com/Forums/home?forum=windowsazuredevelopment&filter=alltypes&sort=lastpostdesc) lub [skontaktuj się z pomocą techniczną platformy Azure](https://azure.microsoft.com/support/options/).

## <a name="are-you-affected"></a>Możesz dotyczy?
Usługi w chmurze są uwzględnione w przypadku któregokolwiek z następujących warunków:

1. Ma wartość "rodziny systemów operacyjnych ="1"jawnie określone w pliku pliku ServiceConfiguration.cscfg dla usługi w chmurze.
2. Nie masz wartość dla rodziny systemów operacyjnych jawnie określone w pliku pliku ServiceConfiguration.cscfg dla usługi w chmurze. Obecnie system używa domyślną wartość "1" w tym przypadku.
3. Azure portal zawiera wartość rodziny System operacyjny gościa jako "Windows Server 2008".

Aby dowiedzieć się, które usługi w chmurze systemem rodziny systemów operacyjnych, które, można uruchomić poniższy skrypt programu Azure PowerShell, chociaż należy [Konfigurowanie programu Azure PowerShell](/powershell/azureps-cmdlets-docs) pierwszy. Aby uzyskać więcej informacji na skryptu, zobacz [Azure gościa systemu operacyjnego rodziny 1 End życia: czerwca 2014](http://blogs.msdn.com/b/ryberry/archive/2014/04/02/azure-guest-os-family-1-end-of-life-june-2014.aspx).

```Powershell
foreach($subscription in Get-AzureSubscription) {
    Select-AzureSubscription -SubscriptionName $subscription.SubscriptionName

    $deployments=get-azureService | get-azureDeployment -ErrorAction Ignore | where {$_.SdkVersion -NE ""}

    $deployments | ft @{Name="SubscriptionName";Expression={$subscription.SubscriptionName}}, ServiceName, SdkVersion, Slot, @{Name="osFamily";Expression={(select-xml -content $_.configuration -xpath "/ns:ServiceConfiguration/@osFamily" -namespace $namespace).node.value }}, osVersion, Status, URL
}
```

Usługi w chmurze wpłyną wycofanie 1 rodziny systemu operacyjnego, jeśli kolumna rodziny systemów operacyjnych w danych wyjściowych skryptu jest pusty lub zawiera wartość "1".

## <a name="recommendations-if-you-are-affected"></a>Zalecenia, jeśli ma wpływ
Firma Microsoft zaleca się, że migracja poszczególnych ról usługi w chmurze do jednego z obsługiwanych rodzin systemu operacyjnego gościa:

**System operacyjny gościa rodziny 4.x** -Windows Server 2012 R2 *(zalecane)*

1. Upewnij się, że aplikacja korzysta z zestawu SDK 2.1 lub nowszej platformy .NET framework 4.0, 4.5 i 4.5.1.
2. Atrybut rodziny systemów operacyjnych z "4" w pliku ServiceConfiguration.cscfg pliku i wdrożenie usługi w chmurze.

**System operacyjny gościa rodziny 3.x** -Windows Server 2012

1. Upewnij się, że aplikacja korzysta z zestawu SDK 1,8 lub nowszej platformy .NET framework 4.0 lub 4.5.
2. Atrybut rodziny systemów operacyjnych dla "3" w pliku ServiceConfiguration.cscfg pliku i wdrożenie usługi w chmurze.

**System operacyjny gościa rodziny 2.x** -Windows Server 2008 R2

1. Upewnij się, że aplikacja korzysta z zestawu SDK 1.3 i powyżej platformy .NET framework w wersji 3.5 lub 4.0.
2. Atrybut rodziny systemów operacyjnych "2" w pliku ServiceConfiguration.cscfg pliku i wdrożenie usługi w chmurze.

## <a name="extended-support-for-guest-os-family-1-ended-nov-3-2014"></a>Rozszerzona obsługa 1 rodziny systemu operacyjnego gościa zakończył 3 listopada 2014 r.
Usługi w chmurze rodziny systemów operacyjnych gościa 1 nie są już obsługiwane. Migrowanie wyłączone rodziny 1 tak szybko, jak to możliwe, aby uniknąć przerw w działaniu usługi.  

## <a name="next-steps"></a>Następne kroki
Przejrzyj najnowszej [wersje systemu operacyjnego gościa](cloud-services-guestos-update-matrix.md).
