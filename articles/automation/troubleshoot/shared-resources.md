---
title: Rozwiązywanie problemów z zasobami usługi Azure Automation udostępnione
description: Dowiedz się, jak rozwiązywać problemy związane z zasobami usługi Azure Automation udostępnione
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 66165a196c8b934df948f1d88b09a5859d3e792f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60401480"
---
# <a name="troubleshoot-errors-with-shared-resources"></a>Rozwiązywanie problemów z udostępnionymi zasobami

W tym artykule omówiono rozwiązania, aby rozwiązać problemy, które może działać w przypadku korzystania z udostępnionych zasobów w usłudze Azure Automation.

## <a name="modules"></a>Moduły

### <a name="module-stuck-importing"></a>Scenariusz: Moduł jest zablokowany, importowanie

#### <a name="issue"></a>Problem

Moduł utkwiła w automatycznej **importowanie** stan podczas importowania lub zaktualizować moduły w usłudze Azure automation.

#### <a name="cause"></a>Przyczyna

Importowanie modułów programu PowerShell jest złożony proces obejmujący wiele kroków. Ten proces wprowadza możliwość modułu nie importowanie poprawnie. Jeśli ten problem wystąpi, moduł, który jest importowany może zostać zatrzymane w stan przejściowy. Aby dowiedzieć się więcej o tym procesie, zobacz [zaimportowanie modułu PowerShell]( /powershell/developer/module/importing-a-powershell-module#the-importing-process).

#### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, należy usunąć moduł, który jest zablokowany w **importowanie** stanu przy użyciu [Remove-AzureRmAutomationModule](/powershell/module/azurerm.automation/remove-azurermautomationmodule) polecenia cmdlet. Można następnie ponów próbę importowania modułu.

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="update-azure-modules-importing"></a>Scenariusz: Moduły usługi AzureRM jest zatrzymany, importowanie po aktualizacji ich

#### <a name="issue"></a>Problem

Baner z następującym komunikatem pozostaje na swoim koncie po próby zaktualizowania moduły AzureRM:

```error
Azure modules are being updated
```

#### <a name="cause"></a>Przyczyna

Istnieje znany problem z aktualizowaniem moduł AzureRM na koncie usługi Automation, która znajduje się w grupie zasobów o nazwie numeryczne, która rozpoczyna się od 0.

#### <a name="resolution"></a>Rozwiązanie

Aby zaktualizować moduły platformy Azure na koncie usługi Automation, musi być w grupie zasobów o nazwie alfanumeryczne. Nie można zaktualizować usługi AzureRM moduły w tym momencie są grupy zasobów o nazwach liczbowe od 0.

### <a name="module-fails-to-import"></a>Scenariusz: Moduł nie powiedzie się zaimportować lub poleceń cmdlet, nie można wykonać po zaimportowaniu

#### <a name="issue"></a>Problem

Nie powiedzie się zaimportować moduł, lub pomyślnie importowany, ale są wyodrębniane nie poleceń cmdlet.

#### <a name="cause"></a>Przyczyna

Niektóre typowe przyczyny, które moduł nie może pomyślnie zaimportować do usługi Azure Automation są:

* Struktura nie jest zgodna strukturę, która automatyzacji musi się on w.
* Moduł jest zależny od innego modułu, który nie został wdrożony na Twoim koncie usługi Automation.
* Moduł brakuje jego zależności w folderze.
* `New-AzureRmAutomationModule` Polecenia cmdlet jest używane do przekazywania modułu, a nie jeszcze podanej ścieżki przechowywania pełnej lub nie został załadowany moduł przy użyciu publicznie dostępnego adresu URL.

#### <a name="resolution"></a>Rozwiązanie

Dowolne z poniższych rozwiązań rozwiązać ten problem:

* Upewnij się, że moduł następuje w następującym formacie: ModuleName.Zip **->** ModuleName lub numer wersji **->** (ModuleName.psm1, ModuleName.psd1)
* Otwórz plik psd1 i sprawdzić, czy moduł wszelkie zależności. Jeśli tak jest, należy przekazać te moduły, do konta usługi Automation.
* Upewnij się, że wszystkie odwołania dll debuggle znajdują się w folderze modułu.

### <a name="all-modules-suspended"></a>Scenariusz: Aktualizacja AzureModule.ps1 zawiesza się podczas aktualizacji modułów

#### <a name="issue"></a>Problem

Korzystając z [AzureModule.ps1 aktualizacji](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) elementu runbook, aby zaktualizować moduły platformy Azure aktualizowanie modułu proces aktualizacji zostanie zawieszone.

#### <a name="cause"></a>Przyczyna

Ustawienie domyślne, aby określić, jak wiele modułów zaktualizowani jednocześnie wynosi 10, korzystając z `Update-AzureModule.ps1` skryptu. Proces aktualizacji jest podatny na błędy, gdy za dużo modułów są aktualizowane w tym samym czasie.

#### <a name="resolution"></a>Rozwiązanie

Nie jest powszechne, że wszystkie moduły AzureRM są wymagane w ramach tego samego konta usługi Automation. Zaleca się importowanie tylko moduły AzureRM, które są potrzebne.

> [!NOTE]
> Należy unikać importowania **AzureRM** modułu. Importowanie **AzureRM** modułów powoduje, że wszystkie **AzureRM.\***  modułów do zaimportowania, to nie jest zalecane.

Jeśli proces aktualizacji wstrzymuje, musisz dodać `SimultaneousModuleImportJobCount` parametr `Update-AzureModules.ps1` skryptów i zapewnia mniejszą wartość niż domyślna, która wynosi 10. Zalecane jest, jeśli możesz wdrożyć tę logikę, aby uruchomić o wartości 3 lub 5. `SimultaneousModuleImportJobCount` jest parametrem `Update-AutomationAzureModulesForAccount` runbook system, który służy do aktualizowania modułów platformy Azure. Ta zmiana sprawia, że proces wykonywania dłużej, ale ma szanse na ukończenie. Poniższy przykład przedstawia, parametr i gdzie umieścić go w elemencie runbook:

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

### <a name="unable-create-update"></a>Scenariusz: Nie można utworzyć lub zaktualizować konto Uruchom jako możesz

#### <a name="issue"></a>Problem

Podczas próby utworzenia lub zaktualizowania konta Uruchom jako, komunikat o błędzie podobny do następujący komunikat o błędzie:

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Przyczyna

Nie masz uprawnień, które należy utworzyć lub zaktualizować konto Uruchom jako lub zasób jest zablokowany na poziomie grupy zasobów.

#### <a name="resolution"></a>Rozwiązanie

Aby utworzyć lub zaktualizować konto Uruchom jako, musi mieć odpowiednie uprawnienia do różnych zasobów, które są używane przez konto Uruchom jako. Aby dowiedzieć się o uprawnienia potrzebne do tworzenia lub aktualizowania konta Uruchom jako, zobacz [Uruchom jako uprawnień konta](../manage-runas-account.md#permissions).

Jeśli problem będzie się ze względu na blokadę, sprawdź, czy blokada jest ok, aby go usunąć. Następnie przejdź do zasobu, który jest zablokowany, kliknij prawym przyciskiem myszy blokady i wybierz **Usuń** usunięcia blokady.

### <a name="iphelper"></a>Scenariusz: Pojawia się błąd "Nie można odnaleźć punktu wejścia o nazwie"GetPerAdapterInfo"w pliku DLL"iplpapi.dll"" podczas wykonywania elementu runbook.

#### <a name="issue"></a>Problem

Podczas wykonywania elementu runbook jest wyświetlany następujący wyjątek:

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>Przyczyna

Ten błąd jest prawdopodobnie spowodowane przez niepoprawnie skonfigurowany [konto Uruchom jako](../manage-runas-account.md).

#### <a name="resolution"></a>Rozwiązanie

Upewnij się, że Twoje [konto Uruchom jako](../manage-runas-account.md) jest poprawnie skonfigurowany. Gdy został on poprawnie skonfigurowany, upewnij się, że masz prawidłowego kodu w elemencie runbook do uwierzytelniania za pomocą usługi Azure. Fragment kodu do uwierzytelniania na platformie Azure w elemencie runbook za pomocą konta Uruchom jako można znaleźć w poniższym przykładzie.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>Kolejne kroki

Jeśli nie był widoczny problemu lub są w stanie rozwiązać problemu, odwiedź jedną z następujących kanałów obsługi więcej:

* Uzyskaj odpowiedzi od ekspertów w zakresie platformy Azure na [forach dotyczących platformy Azure](https://azure.microsoft.com/support/forums/)
* Połącz się z kontem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnym kontem platformy Microsoft Azure utworzonym w celu podniesienia jakości obsługi klientów przez połączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
* Jeśli potrzebujesz więcej pomocy, mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz **uzyskiwanie pomocy technicznej**.
