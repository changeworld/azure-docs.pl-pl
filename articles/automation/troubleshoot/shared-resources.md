---
title: Rozwiązywanie problemów z błędami za pomocą zasobów udostępnionych usługi Azure Automation
description: Dowiedz się, jak rozwiązywać problemy i rozwiązywać problemy z zasobami udostępnionymi usługi Azure Automation obsługującymi elementy runbook.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 4cea558b11d7ee7bbe838cecbd061cd487b536d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278326"
---
# <a name="troubleshoot-errors-with-shared-resources"></a>Rozwiązywanie problemów z błędami przy za pomocą zasobów udostępnionych

W tym artykule omówiono rozwiązania w celu rozwiązania problemów, które mogą wystąpić podczas korzystania z zasobów udostępnionych w usłudze Azure Automation.

## <a name="modules"></a>Moduły

### <a name="scenario-a-module-is-stuck-importing"></a><a name="module-stuck-importing"></a>Scenariusz: Moduł jest zablokowany importowania

#### <a name="issue"></a>Problem

Moduł jest zablokowany w stanie importowania podczas **importowania** lub aktualizowania modułów w automatyzacji platformy Azure.

#### <a name="cause"></a>Przyczyna

Importowanie modułów programu PowerShell jest złożonym procesem wieloetapowym. Proces ten wprowadza możliwość modułu nie importowane poprawnie. Jeśli wystąpi ten problem, importowany moduł może zostać zablokowany w stanie przejściowym. Aby dowiedzieć się więcej o tym procesie, zobacz [Importowanie modułu programu PowerShell](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process).

#### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, należy usunąć moduł, który utknął w stanie **importowania** przy użyciu polecenia cmdlet [Remove-AzureRmAutomationModule.](/powershell/module/azurerm.automation/remove-azurermautomationmodule) Następnie można ponowić próbę zaimportowania modułu.

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="scenario-azurerm-modules-are-stuck-importing-after-trying-to-update-them"></a><a name="update-azure-modules-importing"></a>Scenariusz: Moduły usługi AzureRM utknęły podczas importowania po próbie ich zaktualizowania

#### <a name="issue"></a>Problem

Baner z następującym komunikatem pozostaje na koncie po próbie zaktualizowania modułów usługi AzureRM:

```error
Azure modules are being updated
```

#### <a name="cause"></a>Przyczyna

Istnieje znany problem z aktualizowaniem modułów AzureRM w kontie automatyzacji, który znajduje się w grupie zasobów o nazwie numerycznej, która rozpoczyna się od 0.

#### <a name="resolution"></a>Rozwiązanie

Aby zaktualizować moduły platformy Azure na koncie automatyzacji, musi on znajdować się w grupie zasobów o nazwie alfanumerycznej. Grupy zasobów o nazwach liczbowych zaczyna nych od 0 nie mogą obecnie aktualizować modułów usługi AzureRM.

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a><a name="module-fails-to-import"></a>Scenariusz: Nie można zaimportować modułu lub nie można wykonać polecenia cmdlet po zaimportowaniu

#### <a name="issue"></a>Problem

Moduł nie można zaimportować lub importować pomyślnie, ale nie są wyodrębniane polecenia cmdlet.

#### <a name="cause"></a>Przyczyna

Niektóre typowe powody, dla których moduł może nie zostać pomyślnie zaimportować do usługi Azure Automation są:

* Struktura nie pasuje do struktury, w jakiej automatyzacja potrzebuje.
* Moduł zależy od innego modułu, który nie został wdrożony na koncie automatyzacji.
* Moduł brakuje jego zależności w folderze.
* Polecenie `New-AzureRmAutomationModule` cmdlet jest używane do przekazywania modułu i nie podano pełnej ścieżki magazynu lub nie załadowano modułu przy użyciu publicznie dostępnego adresu URL.

#### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, które rozwiązań:

* Upewnij się, że moduł jest zgodny z **->** następującym formatem: **->** ModuleName.Zip ModuleName lub Numer wersji (ModuleName.psm1, ModuleName.psd1)
* Otwórz plik .psd1 i sprawdź, czy moduł ma jakieś zależności. Jeśli tak, prześlij te moduły do konta automatyzacji.
* Upewnij się, że wszystkie pliki dll, do których istnieją odwołania, znajdują się w folderze modułu.

### <a name="scenario-update-azuremoduleps1-suspends-when-updating-modules"></a><a name="all-modules-suspended"></a>Scenariusz: Update-AzureModule.ps1 zawiesza się podczas aktualizowania modułów

#### <a name="issue"></a>Problem

Podczas korzystania z [update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) runbook, aby zaktualizować moduły platformy Azure aktualizacji modułu proces aktualizacji zostanie zawieszony.

#### <a name="cause"></a>Przyczyna

Domyślne ustawienie określające, ile modułów jest aktualizowanych jednocześnie, wynosi 10 podczas korzystania ze skryptu. `Update-AzureModule.ps1` Proces aktualizacji jest podatny na błędy, gdy zbyt wiele modułów są aktualizowane w tym samym czasie.

#### <a name="resolution"></a>Rozwiązanie

Nie jest powszechne, że wszystkie moduły AzureRM są wymagane na tym samym koncie automatyzacji. Zalecane jest importowanie tylko modułów AzureRM, które są potrzebne.

> [!NOTE]
> Należy unikać importowania modułu **AzureRM.** Importowanie modułów **usługi AzureRM** powoduje, że wszystkie moduły **\* azurerm.** do zaimportowania, to nie jest zalecany.

Jeśli proces aktualizacji zostanie wstrzymany, `SimultaneousModuleImportJobCount` należy dodać `Update-AzureModules.ps1` parametr do skryptu i podać niższą wartość niż wartość domyślna, która wynosi 10. Jest zalecane, jeśli zaimplementujesz tę logikę, aby rozpocząć od wartości 3 lub 5. `SimultaneousModuleImportJobCount`jest parametrem `Update-AutomationAzureModulesForAccount` systemu runbook, który jest używany do aktualizacji modułów platformy Azure. Ta zmiana sprawia, że proces jest dłuższy, ale ma większe szanse na ukończenie. Poniższy przykład pokazuje parametr i gdzie umieścić go w elementów runbook:

 ```powershell
         $Body = @"
            {
               "properties":{
               "runbook":{
                   "name":"Update-AutomationAzureModulesForAccount"
               },
               "parameters":{
                    ...
                    "SimultaneousModuleImportJobCount":"3",
                    ... 
               }
              }
           }
"@
```

## <a name="run-as-accounts"></a>Konta Uruchom jako

### <a name="scenario-youre-unable-to-create-or-update-a-run-as-account"></a><a name="unable-create-update"></a>Scenariusz: nie można utworzyć ani zaktualizować konta Uruchom jako

#### <a name="issue"></a>Problem

Podczas próby utworzenia lub zaktualizowania konta Uruchom jako pojawia się błąd podobny do następującego komunikatu o błędzie:

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Przyczyna

Nie masz uprawnień, które są potrzebne do utworzenia lub aktualizacji konta Uruchom jako lub zasób jest zablokowany na poziomie grupy zasobów.

#### <a name="resolution"></a>Rozwiązanie

Aby utworzyć lub zaktualizować konto Uruchom jako, musisz mieć odpowiednie uprawnienia do różnych zasobów używanych przez konto Uruchom jako. Aby dowiedzieć się więcej o uprawnieniach potrzebnych do utworzenia lub zaktualizowania konta Uruchom jako, zobacz [Uruchamianie jako uprawnienia konta](../manage-runas-account.md#permissions).

Jeśli problem jest z powodu blokady, sprawdź, czy blokada jest ok, aby go usunąć. Następnie przejdź do zablokowanego zasobu, kliknij prawym przyciskiem myszy blokadę i wybierz polecenie **Usuń,** aby usunąć blokadę.

### <a name="scenario-you-receive-the-error-unable-to-find-an-entry-point-named-getperadapterinfo-in-dll-iplpapidll-when-executing-a-runbook"></a><a name="iphelper"></a>Scenariusz: Podczas wykonywania śmigieł element runbook pojawia się komunikat o błędzie "Nie można znaleźć punktu wejścia o nazwie 'GetPerAdapterInfo'.

#### <a name="issue"></a>Problem

Podczas wykonywania elementów runbook otrzymasz następujący wyjątek:

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>Przyczyna

Ten błąd jest najprawdopodobniej spowodowany przez niepoprawnie skonfigurowane [uruchom jako konto](../manage-runas-account.md).

#### <a name="resolution"></a>Rozwiązanie

Upewnij się, że [konto Uruchom jako](../manage-runas-account.md) jest poprawnie skonfigurowane. Po poprawnej konfiguracji upewnij się, że masz odpowiedni kod w bieście runbook do uwierzytelniania za pomocą platformy Azure. W poniższym przykładzie przedstawiono fragment kodu do uwierzytelnienia na platformie Azure w uruchomieniu za pomocą uruchom jako konto.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów w zakresie platformy Azure na [forach dotyczących platformy Azure](https://azure.microsoft.com/support/forums/)
* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta, łącząc społeczność platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
* Jeśli potrzebujesz więcej pomocy, możesz zgłosić zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc **techniczną**.
