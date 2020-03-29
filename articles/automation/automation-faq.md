---
title: Często zadawane pytania dotyczące usługi Azure Automation | Dokumenty firmy Microsoft
description: Odpowiedzi na często zadawane pytania dotyczące usługi Azure Automation.
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 129a5316c2e7be329b479c79706791e993d20b74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77925815"
---
# <a name="azure-automation-frequently-asked-questions"></a>Często zadawane pytania dotyczące usługi Azure Automation

To często zadawane pytania firmy Microsoft to lista często zadawanych pytań dotyczących usługi Azure Automation. Jeśli masz jakieś dodatkowe pytania dotyczące jego możliwości, wejdź na forum dyskusyjne i opublikuj swoje pytania. Gdy pytanie jest często zadawane, dodajemy je do tego artykułu, aby można je było szybko i łatwo znaleźć.

## <a name="update-management-solution"></a>Update Management solution (Rozwiązanie Update Management)

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>Czy mogę zapobiec nieoczekiwanym uaktualnieniom na poziomie systemu operacyjnego?

W przypadku niektórych wariantów systemu Linux, takich jak Red Hat Enterprise Linux, uaktualnienia na poziomie systemu operacyjnego mogą występować za pośrednictwem pakietów. Może to prowadzić do zarządzania aktualizacjami działa, gdzie zmienia się numer wersji systemu operacyjnego. Ponieważ usługa Zarządzanie aktualizacjami używa tych samych metod do aktualizowania pakietów, których administrator będzie używał lokalnie na komputerze z systemem Linux, to zachowanie jest zamierzone.

Aby uniknąć aktualizowania wersji systemu operacyjnego za pośrednictwem wdrożeń zarządzania aktualizacjami, użyj funkcji **wykluczeń.**

W red hat enterprise linux, nazwa pakietu do wykluczenia jest redhat-release-server.x86_64.

### <a name="why-arent-criticalsecurity-updates-applied"></a>Dlaczego nie są stosowane krytyczne/aktualizacje zabezpieczeń?

Podczas wdrażania aktualizacji na komputerze z systemem Linux można wybrać klasyfikacje aktualizacji. Ta opcja filtruje aktualizacje, które są stosowane do komputera, które spełniają określone kryteria. Ten filtr jest stosowany lokalnie na komputerze po wdrożeniu aktualizacji.

Ponieważ usługa Zarządzanie aktualizacjami wzbogaciła się o aktualizację w chmurze, niektóre aktualizacje można oznaczyć w bezpieczeństwie aktualizacji jako mające wpływ na bezpieczeństwo, nawet jeśli komputer lokalny nie ma tych informacji. W rezultacie jeśli zastosujesz aktualizacje krytyczne do komputera z systemem Linux, mogą istnieć aktualizacje, które nie są oznaczone jako mające wpływ na bezpieczeństwo na tym komputerze i dlatego aktualizacje nie są stosowane. Jednak zarządzanie aktualizacjami może nadal zgłaszać, że komputer jako niezgodny, ponieważ zawiera dodatkowe informacje na temat odpowiedniej aktualizacji.

Wdrażanie aktualizacji według klasyfikacji aktualizacji nie działa w wersjach RTM centos. Aby poprawnie wdrożyć aktualizacje dla systemu CentOS, wybierz wszystkie klasyfikacje, aby upewnić się, że aktualizacje są stosowane. W przypadku funkcji SUSE wybranie *opcji* **Inne aktualizacje** jako klasyfikacja może spowodować zainstalowanie niektórych aktualizacji zabezpieczeń, jeśli najpierw wymagane są aktualizacje zabezpieczeń związane z zamkiem błyskawicznym (menedżerem pakietów) lub jego zależnościami. To zachowanie jest ograniczeniem zypper. W niektórych przypadkach może być wymagane ponowne uruchomienie wdrożenia aktualizacji. Aby sprawdzić, sprawdź dziennik aktualizacji.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Czy mogę wdrażać aktualizacje w dzierżawach platformy Azure?

Jeśli masz maszyny w innej dzierżawy platformy Azure raportowania do zarządzania aktualizacjami, które należy załatać, musisz użyć następującego obejścia, aby je zaplanować. Można użyć polecenia cmdlet [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) z `-ForUpdate` przełącznikiem, aby utworzyć harmonogram i użyć polecenia cmdlet [konfiguracji New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) i przekazać maszyny w innej dzierżawie do parametru. `-NonAzureComputer` W poniższym przykładzie pokazano, jak to zrobić:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>Następne kroki

Jeśli na twoje pytanie nie ma odpowiedzi, możesz znaleźć się na poniższym forum, aby uzyskać dodatkowe pytania i odpowiedzi.

- [Automatyzacja platformy Azure](https://social.msdn.microsoft.com/Forums/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)

Aby uzyskać ogólne opinie na temat rozwiązania do zarządzania aktualizacjami, odwiedź [forum opinii](https://feedback.azure.com/forums/905242-update-management).