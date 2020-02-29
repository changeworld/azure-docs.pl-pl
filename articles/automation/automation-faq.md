---
title: Azure Automation często zadawane pytania | Microsoft Docs
description: Odpowiedzi na często zadawane pytania dotyczące Azure Automation.
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 129a5316c2e7be329b479c79706791e993d20b74
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77925815"
---
# <a name="azure-automation-frequently-asked-questions"></a>Azure Automation często zadawane pytania

Ta firma Microsoft — często zadawane pytania dotyczące Azure Automation. Jeśli masz dodatkowe pytania dotyczące jego możliwości, przejdź na forum dyskusyjne i Opublikuj swoje pytania. Gdy zadawane pytanie dodajemy go do tego artykułu tak, aby możliwe było szybkie i łatwe.

## <a name="update-management-solution"></a>Update Management rozwiązanie

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>Czy mogę uniknąć nieoczekiwanych uaktualnień na poziomie systemu operacyjnego?

W przypadku niektórych odmian systemu Linux, takich jak Red Hat Enterprise Linux, uaktualnienia na poziomie systemu operacyjnego mogą odbywać się za poorednictwem pakietów. Może to prowadzić do Update Management uruchamiania w przypadku zmiany numeru wersji systemu operacyjnego. Ponieważ Update Management używa tych samych metod do aktualizacji pakietów, które administrator może używać lokalnie na komputerze z systemem Linux, to zachowanie jest zamierzone.

Aby uniknąć aktualizacji wersji systemu operacyjnego za pomocą wdrożeń Update Management, użyj funkcji **wykluczania** .

W Red Hat Enterprise Linux nazwa pakietu do wykluczenia to RedHat-Release-Server. x86_64.

### <a name="why-arent-criticalsecurity-updates-applied"></a>Dlaczego aktualizacje krytyczne/zabezpieczenia są stosowane?

Podczas wdrażania aktualizacji na komputerze z systemem Linux można wybrać klasyfikacje aktualizacji. Ta opcja umożliwia filtrowanie aktualizacji, które są stosowane do komputera spełniającego określone kryteria. Ten filtr jest stosowany lokalnie na komputerze podczas wdrażania aktualizacji.

Ponieważ Update Management wykonuje wzbogacanie aktualizacji w chmurze, niektóre aktualizacje mogą być oflagowane w Update Management jako mające wpływ na bezpieczeństwo, nawet jeśli maszyna lokalna nie ma tych informacji. W związku z tym w przypadku zastosowania aktualizacji krytycznych do maszyny z systemem Linux mogą wystąpić aktualizacje, które nie są oznaczone jako mające wpływ na zabezpieczenia na tym komputerze i dlatego aktualizacje nie są stosowane. Jednak Update Management może nadal zgłosić ten komputer jako niezgodny, ponieważ zawiera on dodatkowe informacje na temat odpowiedniej aktualizacji.

Wdrażanie aktualizacji według klasyfikacji aktualizacji nie działa w wersjach RTM CentOS. Aby poprawnie wdrożyć aktualizacje dla programu CentOS, wybierz pozycję Wszystkie klasyfikacje, aby upewnić się, że aktualizacje są stosowane. W *przypadku opcji SUSE* wybranie **innych aktualizacji** jako klasyfikacji może spowodować, że niektóre aktualizacje zabezpieczeń również zostaną zainstalowane, jeśli najpierw wymagane są aktualizacje zabezpieczeń powiązane z użyciu narzędzia zypper (Menedżer pakietów) lub jej zależności. To zachowanie jest ograniczeniem użyciu narzędzia zypper. W niektórych przypadkach może być konieczne ponowne uruchomienie wdrożenia aktualizacji. Aby sprawdzić, sprawdź dziennik aktualizacji.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Czy można wdrażać aktualizacje w ramach dzierżawców platformy Azure?

Jeśli masz maszyny w innym raportowaniu dzierżawy platformy Azure, aby Update Management, które należy zastosować, należy użyć następującego obejścia w celu zaplanowania ich planowania. Można użyć polecenia cmdlet [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) z przełącznikiem `-ForUpdate`, aby utworzyć harmonogram, i użyć polecenia cmdlet [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) i przekazać maszyny w innej dzierżawie do parametru `-NonAzureComputer`. Poniższy przykład pokazuje, jak to zrobić:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>Następne kroki

Jeśli na pytanie nie ma odpowiedzi, możesz zapoznać się z poniższym forum w celu uzyskania dodatkowych pytań i odpowiedzi.

- [Azure Automation](https://social.msdn.microsoft.com/Forums/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)

Aby uzyskać ogólne informacje na temat rozwiązania Update Management, odwiedź [forum opinii](https://feedback.azure.com/forums/905242-update-management).