---
author: mgoedtel
ms.service: log-analytics
ms.topic: include
ms.date: 11/09/2018
ms.author: magoedte
ms.openlocfilehash: 6890c71ac7c265d46cc77751786fea4d0b228588
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66129699"
---
### <a name="troubleshoot-azure-diagnostics"></a>Rozwiązywanie problemów z funkcją Diagnostyka Azure

Jeśli zostanie wyświetlony następujący komunikat o błędzie, dostawca zasobów Microsoft.insights nie jest zarejestrowany:

`Failed to update diagnostics for 'resource'. {"code":"Forbidden","message":"Please register the subscription 'subscription id' with Microsoft.Insights."}`

Aby zarejestrować tego dostawcę zasobów, wykonaj następujące czynności w witrynie Azure Portal:

1.  W okienku nawigacji po lewej stronie kliknij pozycję *Subskrypcje*
2.  Wybierz subskrypcję określoną w komunikacie o błędzie
3.  Kliknij pozycję *Dostawcy zasobów*
4.  Odszukaj dostawcę *Microsoft.insights*
5.  Kliknij link *Zarejestruj*

![Rejestrowanie dostawcy zasobów microsoft.insights](./media/log-analytics-troubleshoot-azure-diagnostics/log-analytics-register-microsoft-diagnostics-resource-provider.png)

Po zarejestrowaniu dostawcy zasobów *Microsoft.insights* spróbuj ponownie skonfigurować diagnostykę.


W programie PowerShell Jeśli zostanie wyświetlony następujący komunikat o błędzie, należy zaktualizować wersję programu PowerShell:

`Set-AzDiagnosticSetting : A parameter cannot be found that matches parameter name 'WorkspaceId'.`

Zaktualizuj swoją wersję programu Azure PowerShell, postępuj zgodnie z instrukcjami wyświetlanymi w [Instalowanie programu Azure PowerShell](/powershell/azure/install-az-ps) artykułu.
