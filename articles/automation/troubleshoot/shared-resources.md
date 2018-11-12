---
title: Rozwiązywanie problemów z zasobami usługi Azure Automation udostępnione
description: Dowiedz się, jak rozwiązywać problemy związane z zasobami usługi Azure Automation udostępnione
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 11/05/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 385d2969e65647ab0b5c5e21c07b127104587e7e
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51263561"
---
# <a name="troubleshoot-errors-with-shared-resources"></a>Rozwiązywanie problemów z udostępnionymi zasobami

W tym artykule omówiono rozwiązania, aby rozwiązać problemy, które może działać w przypadku korzystania z udostępnionych zasobów w usłudze Azure Automation.

## <a name="modules"></a>Moduły

### <a name="module-stuck-importing"></a>Scenariusz: Moduł utkwiła importowania

#### <a name="issue"></a>Problem

Podczas importowania lub zaktualizować moduły w usłudze Azure automation, możesz znaleźć moduł, który jest zablokowany w **importowanie** stanu.

#### <a name="error"></a>Błąd

Importowanie modułów programu PowerShell jest złożony proces obejmujący wiele kroków. Ten proces wprowadza możliwość modułu nie importowanie poprawnie. W takiej sytuacji moduł, który jest importowany może zostać zatrzymane w stanie przejściowym. Aby dowiedzieć się więcej o tym procesie, zobacz [zaimportowanie modułu PowerShell]( /powershell/developer/module/importing-a-powershell-module#the-importing-process).

#### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, należy usunąć moduł, który jest zablokowany w **importowanie** stanu przy użyciu [Remove-AzureRmAutomationModule](/powershell/module/azurerm.automation/remove-azurermautomationmodule) polecenia cmdlet. Można następnie ponów próbę importowania modułu.

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

## <a name="next-steps"></a>Kolejne kroki

Jeśli nie był widoczny problemu lub są w stanie rozwiązać problemu, odwiedź jedną z następujących kanałów obsługi więcej:

* Uzyskaj odpowiedzi od ekspertów w zakresie platformy Azure na [forach dotyczących platformy Azure](https://azure.microsoft.com/support/forums/)
* Połącz się z kontem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnym kontem platformy Microsoft Azure utworzonym w celu podniesienia jakości obsługi klientów przez połączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
* Jeśli potrzebujesz więcej pomocy, mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz **uzyskiwanie pomocy technicznej**.