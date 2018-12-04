---
title: Rozwiązywanie problemów z zasobami usługi Azure Automation udostępnione
description: Dowiedz się, jak rozwiązywać problemy związane z zasobami usługi Azure Automation udostępnione
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 12/3/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: ce78c86cdae9a06100fd17d00e0229805e42983b
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52848463"
---
# <a name="troubleshoot-errors-with-shared-resources"></a>Rozwiązywanie problemów z udostępnionymi zasobami

W tym artykule omówiono rozwiązania, aby rozwiązać problemy, które może działać w przypadku korzystania z udostępnionych zasobów w usłudze Azure Automation.

## <a name="modules"></a>Moduły

### <a name="module-stuck-importing"></a>Scenariusz: Moduł utkwiła importowania

#### <a name="issue"></a>Problem

Moduł utkwiła w automatycznej **importowanie** stan podczas importowania lub zaktualizować moduły w usłudze Azure automation.

#### <a name="cause"></a>Przyczyna

Importowanie modułów programu PowerShell jest złożony proces obejmujący wiele kroków. Ten proces wprowadza możliwość modułu nie importowanie poprawnie. Jeśli ten problem wystąpi, moduł, który jest importowany może zostać zatrzymane w stan przejściowy. Aby dowiedzieć się więcej o tym procesie, zobacz [zaimportowanie modułu PowerShell]( /powershell/developer/module/importing-a-powershell-module#the-importing-process).

#### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, należy usunąć moduł, który jest zablokowany w **importowanie** stanu przy użyciu [Remove-AzureRmAutomationModule](/powershell/module/azurerm.automation/remove-azurermautomationmodule) polecenia cmdlet. Można następnie ponów próbę importowania modułu.

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

## <a name="run-as-accounts"></a>Konta Uruchom jako

### <a name="unable-create-update"></a>Scenariusz: Nie możesz utworzyć lub zaktualizować konto Uruchom jako

#### <a name="issue"></a>Problem

Podczas próby utworzenia lub zaktualizowania konta Uruchom jako, komunikat o błędzie podobny do następujący komunikat o błędzie:

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Przyczyna

Nie masz uprawnień, które należy utworzyć lub zaktualizować konto Uruchom jako lub zasób jest zablokowany na poziomie grupy zasobów.

#### <a name="resolution"></a>Rozwiązanie

Aby utworzyć lub zaktualizować konto Uruchom jako, musi mieć odpowiednie uprawnienia do różnych zasobów, które są używane przez konto Uruchom jako. Aby dowiedzieć się o uprawnienia potrzebne do tworzenia lub aktualizowania konta Uruchom jako, zobacz [Uruchom jako uprawnień konta](../manage-runas-account.md#permissions).

Jeśli problem będzie się z powodu blokady, sprawdź, czy blokada jest ok, aby usunąć i przejdź do zasobu, który jest zablokowany, kliknij prawym przyciskiem myszy blokady i wybierz **Usuń** usunięcia blokady.

## <a name="next-steps"></a>Kolejne kroki

Jeśli nie był widoczny problemu lub są w stanie rozwiązać problemu, odwiedź jedną z następujących kanałów obsługi więcej:

* Uzyskaj odpowiedzi od ekspertów w zakresie platformy Azure na [forach dotyczących platformy Azure](https://azure.microsoft.com/support/forums/)
* Połącz się z kontem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnym kontem platformy Microsoft Azure utworzonym w celu podniesienia jakości obsługi klientów przez połączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
* Jeśli potrzebujesz więcej pomocy, mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz **uzyskiwanie pomocy technicznej**.