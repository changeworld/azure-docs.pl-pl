---
title: Rozwiązywanie problemów z Azure Automation udostępnionymi zasobami
description: Informacje o rozwiązywaniu problemów i rozwiązywaniu problemów z Azure Automation udostępnionych zasobów obsługujących elementy Runbook.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 4cea558b11d7ee7bbe838cecbd061cd487b536d2
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769867"
---
# <a name="troubleshoot-errors-with-shared-resources"></a>Rozwiązywanie problemów z zasobami udostępnionymi

W tym artykule omówiono rozwiązania rozwiązywania problemów, które mogą być wykonywane w przypadku korzystania z udostępnionych zasobów w Azure Automation.

## <a name="modules"></a>Moduły

### <a name="module-stuck-importing"></a>Scenariusz: moduł jest zablokowany.

#### <a name="issue"></a>Problem

Podczas importowania lub aktualizowania modułów w usłudze Azure Automation moduł jest zablokowany w stanie **importowania** .

#### <a name="cause"></a>Przyczyna

Importowanie modułów programu PowerShell to złożony proces obejmujący wiele kroków. W tym procesie wprowadzono możliwość nieprawidłowego zaimportowania modułu. Jeśli wystąpi ten problem, importowany moduł może być zablokowany w stanie przejściowym. Aby dowiedzieć się więcej o tym procesie, zobacz [Importowanie modułu programu PowerShell](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process).

#### <a name="resolution"></a>Rozdzielczość

Aby rozwiązać ten problem, należy usunąć moduł, który jest zablokowany w stanie **importowania** , za pomocą polecenia cmdlet [Remove-AzureRmAutomationModule](/powershell/module/azurerm.automation/remove-azurermautomationmodule) . Następnie można ponowić próbę importowania modułu.

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="update-azure-modules-importing"></a>Scenariusz: moduły AzureRM są zablokowane po próbie ich zaktualizowania

#### <a name="issue"></a>Problem

Po ponowieniu próby zaktualizowania modułów AzureRM na koncie zostanie wyświetlony baner z następującym komunikatem:

```error
Azure modules are being updated
```

#### <a name="cause"></a>Przyczyna

Istnieje znany problem z aktualizacją modułów AzureRM na koncie usługi Automation, które znajduje się w grupie zasobów o nazwie liczbowej rozpoczynającej się od 0.

#### <a name="resolution"></a>Rozdzielczość

Aby zaktualizować moduły platformy Azure na koncie usługi Automation, musi ono należeć do grupy zasobów o nazwie alfanumerycznej. Grupy zasobów o nazwach liczbowych zaczynających się od 0 nie mogą teraz zaktualizować modułów AzureRM.

### <a name="module-fails-to-import"></a>Scenariusz: Importowanie modułu nie powiodło się lub polecenia cmdlet nie mogą zostać wykonane po zaimportowaniu

#### <a name="issue"></a>Problem

Nie można pomyślnie zaimportować lub importować modułu, ale żadne polecenia cmdlet nie są wyodrębniane.

#### <a name="cause"></a>Przyczyna

Niektóre typowe przyczyny, że moduł nie został pomyślnie zaimportowany do Azure Automation są następujące:

* Struktura nie jest zgodna ze strukturą, którą Automatyzacja potrzebuje.
* Moduł jest zależny od innego modułu, który nie został wdrożony na koncie usługi Automation.
* W module brakuje jego zależności w folderze.
* `New-AzureRmAutomationModule` polecenie cmdlet jest używane do przekazywania modułu i nie podano pełnej ścieżki magazynu lub nie załadowano modułu przy użyciu publicznie dostępnego adresu URL.

#### <a name="resolution"></a>Rozdzielczość

Niektóre z poniższych rozwiązań rozwiązują ten problem:

* Upewnij się, że moduł jest zgodny z następującym formatem: ModuleName. zip **->** ModuleName lub numer wersji **->** (ModuleName. PSM1, ModuleName. psd1)
* Otwórz plik. psd1 i sprawdź, czy moduł ma jakiekolwiek zależności. Jeśli tak, Przekaż te moduły do konta usługi Automation.
* Upewnij się, że wszystkie pliki dll, do których istnieją odwołania, są obecne w folderze modułu.

### <a name="all-modules-suspended"></a>Scenariusz: Update-AzureModule. ps1 zawiesza się podczas aktualizowania modułów

#### <a name="issue"></a>Problem

W przypadku aktualizowania modułów platformy Azure przy użyciu elementu Runbook [Update-AzureModule. ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) Aktualizacja modułu jest wstrzymana.

#### <a name="cause"></a>Przyczyna

Ustawienie domyślne określające, ile modułów można aktualizować jednocześnie, jest 10 przy użyciu skryptu `Update-AzureModule.ps1`. Proces aktualizacji jest podatny na błędy w przypadku jednoczesnego aktualizowania zbyt wielu modułów.

#### <a name="resolution"></a>Rozdzielczość

Nie jest powszechne, że wszystkie moduły AzureRM są wymagane na tym samym koncie usługi Automation. Zaleca się Importowanie tylko wymaganych modułów AzureRM.

> [!NOTE]
> Należy unikać importowania modułu **AzureRM** . Zaimportowanie modułów **AzureRM** powoduje zaimportowanie wszystkich modułów **AzureRM.\*** nie jest to zalecanych.

W przypadku zawieszania procesu aktualizacji należy dodać parametr `SimultaneousModuleImportJobCount` do skryptu `Update-AzureModules.ps1` i podać niższą wartość od wartości domyślnej 10. Jest to zalecane w przypadku zaimplementowania tej logiki, aby rozpocząć od wartości 3 lub 5. `SimultaneousModuleImportJobCount` to parametr elementu Runbook systemu `Update-AutomationAzureModulesForAccount`, który służy do aktualizowania modułów platformy Azure. Ta zmiana powoduje, że proces jest wykonywany dłużej, ale ma lepszą szansę ukończenia. Poniższy przykład przedstawia parametr i miejsce umieszczenia go w elemencie Runbook:

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

### <a name="unable-create-update"></a>Scenariusz: nie można utworzyć lub zaktualizować konta Uruchom jako

#### <a name="issue"></a>Problem

Podczas próby utworzenia lub zaktualizowania konta Uruchom jako zostanie wyświetlony komunikat o błędzie podobny do następującego:

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Przyczyna

Nie masz uprawnień wymaganych do utworzenia lub zaktualizowania konta Uruchom jako lub zasób jest zablokowany na poziomie grupy zasobów.

#### <a name="resolution"></a>Rozdzielczość

Aby utworzyć lub zaktualizować konto Uruchom jako, musisz mieć odpowiednie uprawnienia do różnych zasobów używanych przez konto Uruchom jako. Aby dowiedzieć się więcej o uprawnieniach wymaganych do utworzenia lub zaktualizowania konta Uruchom jako, zobacz [uprawnienia konta Uruchom jako](../manage-runas-account.md#permissions).

Jeśli problem występuje z powodu blokady, sprawdź, czy jest on prawidłowy, aby go usunąć. Następnie przejdź do zablokowanego zasobu, kliknij prawym przyciskiem myszy blokadę i wybierz polecenie **Usuń** , aby usunąć blokadę.

### <a name="iphelper"></a>Scenariusz: komunikat o błędzie "nie można odnaleźć punktu wejścia o nazwie" GetPerAdapterInfo "w bibliotece DLL" iplpapi. dll "" podczas wykonywania elementu Runbook.

#### <a name="issue"></a>Problem

Podczas wykonywania elementu Runbook jest wyświetlany następujący wyjątek:

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>Przyczyna

Ten błąd jest najprawdopodobniej spowodowany niepoprawnie skonfigurowanym [kontem Uruchom jako](../manage-runas-account.md).

#### <a name="resolution"></a>Rozdzielczość

Upewnij się [, że konto Uruchom jako](../manage-runas-account.md) jest prawidłowo skonfigurowane. Po poprawnym skonfigurowaniu upewnij się, że masz odpowiedni kod w elemencie Runbook do uwierzytelniania na platformie Azure. Poniższy przykład przedstawia fragment kodu do uwierzytelniania na platformie Azure w elemencie Runbook przy użyciu konta Uruchom jako.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów w zakresie platformy Azure na [forach dotyczących platformy Azure](https://azure.microsoft.com/support/forums/)
* Połącz się z kontem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnym kontem platformy Microsoft Azure utworzonym w celu podniesienia jakości obsługi klientów przez połączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
* Jeśli potrzebujesz więcej pomocy, możesz obsłużyć zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję **Uzyskaj pomoc techniczną**.
