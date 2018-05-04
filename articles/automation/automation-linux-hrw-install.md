---
title: Hybrydowy proces roboczy elementu runbook usługi Azure Automation (Linux)
description: Ten artykuł zawiera informacje na temat instalowania Azure hybrydowego procesu roboczego Runbook automatyzacji, który służy do uruchamiania elementów runbook na komputerach opartych na systemie Linux w lokalnym centrum danych lub w środowisku chmury.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: bc6c98784195aaf80cb6ca32ef29f75666099b06
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-deploy-a-linux-hybrid-runbook-worker"></a>Jak wdrożyć procesu roboczego elementu Runbook dla hybrydowych w systemie Linux

Elementy Runbook automatyzacji Azure nie może uzyskać dostępu zasobów w innych chmur lub w środowisku lokalnym, ponieważ działają w chmurze Azure. Funkcja hybrydowy proces roboczy elementu Runbook usługi Automatyzacja Azure umożliwia uruchamianie elementów runbook, bezpośrednio na komputerze hostującym rolę i względem zasobów w środowisku, aby zarządzać tymi zasobów lokalnych. Elementy Runbook są przechowywane i zarządzane w automatyzacji Azure i następnie dostarczany do co najmniej jednym komputerze wyznaczonym.

Na poniższej ilustracji przedstawiono tę funkcję:<br>  

![Omówienie procesu roboczego elementu Runbook hybrydowego](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

Informacje techniczne roli hybrydowy proces roboczy elementu Runbook, zobacz [Przegląd architektury automatyzacji](automation-offering-get-started.md#automation-architecture-overview). Przejrzyj następujące informacje dotyczące [wymagania sprzętowe i programowe](automation-offering-get-started.md#hybrid-runbook-worker) i [informacji do przygotowania sieci](automation-offering-get-started.md#network-planning) przed rozpoczęciem wdrażania hybrydowego procesu roboczego elementu Runbook. Po pomyślnym wdrożeniu runbook worker, przejrzyj [uruchamiania elementów runbook na hybrydowy proces roboczy elementu Runbook](automation-hrw-run-runbooks.md) Aby dowiedzieć się, jak skonfigurować elementy runbook do automatyzacji procesów w lokalnym centrum danych lub w innym środowisku chmury.     

## <a name="hybrid-runbook-worker-groups"></a>Hybrydowego procesu roboczego elementu Runbook grup
Każdy hybrydowy proces roboczy elementu Runbook jest członkiem grupy hybrydowego procesu roboczego elementu Runbook, którą określono podczas instalacji agenta. Grupy mogą obejmować jednego agenta, ale można zainstalować wielu agentów w grupie wysokiej dostępności.

Po uruchomieniu elementu runbook na hybrydowy proces roboczy elementu Runbook można określić grupy, do której jest uruchamiany na. Członkowie grupy określają, które procesu roboczego obsługującym żądanie. Nie można określić określonego procesu roboczego.

## <a name="installing-linux-hybrid-runbook-worker"></a>Instalowanie systemu Linux hybrydowego Runbook Worker
Aby zainstalować i skonfigurować hybrydowy proces roboczy elementu Runbook na komputerze z systemem Linux, należy wykonać proces bezpośrednio do przodu, aby ręcznie zainstalować i skonfigurować rolę. Wymaga to włączenia **automatyzacji hybrydowy proces roboczy** rozwiązania w obszarze roboczym analizy dzienników i ponownie uruchomić zestaw poleceń, aby zarejestrować komputer jako proces roboczy, a następnie dodaj go do nowej lub istniejącej grupy. 

Przed kontynuowaniem należy pamiętać obszaru roboczego analizy dzienników, z którą połączony jest Twoje konto usługi Automatyzacja, a także klucz podstawowy dla konta automatyzacji. Oba z portalu można znaleźć konta automatyzacji, a zaznaczając **obszaru roboczego** dla identyfikator obszaru roboczego i wybierając **klucze** klucza podstawowego.  

1.  Włącz rozwiązanie "Automatyzacji hybrydowy proces roboczy" na platformie Azure. Można to zrobić przez:

   1. Dodaj **automatyzacji hybrydowy proces roboczy** rozwiązania do subskrypcji przy użyciu procedury na [rozwiązań do zarządzania dodać analizy dzienników do swojego obszaru roboczego](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions).
   2. Uruchom następujące polecenie cmdlet:

        ```powershell
         $null = Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

2.  Uruchom następujące polecenie, zmiana wartości parametrów *-w*, *-k*, *-g*, i *-e*. Aby uzyskać *-g* parametru Zamień wartość nazwę grupy hybrydowego procesu roboczego elementu Runbook, w której powinien dołączyć nowy Linux hybrydowy proces roboczy. Jeśli nazwa nie istnieje już na Twoim koncie automatyzacji, nowej grupy hybrydowego procesu roboczego elementu Runbook jest nawiązywane o tej nazwie.
    
    ```python
    sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
    ```
3. Po zakończeniu polecenia bloku hybrydowego procesu roboczego grupy w portalu Azure zostaną wyświetlone nowe grupy i liczby elementów członkowskich lub jeśli istniejącą grupę, do liczby elementów członkowskich jest zwiększany. Można zaznaczyć grupę z listy na **hybrydowego procesu roboczego grupy** bloku, a następnie wybierz **hybrydowych procesów roboczych** kafelka. Na **hybrydowych procesów roboczych** bloku, zobacz każdego członka grupy na liście.  


## <a name="turning-off-signature-validation"></a>Wyłączenie sprawdzania poprawności podpisu 
Domyślnie Linux hybrydowymi elementami roboczymi Runbook wymaga weryfikacji podpisu. Jeśli pracownik wykonywane bez znaku elementu runbook, zostanie wyświetlony błąd zawierający "Walidacja podpisu nie powiodła się". Aby wyłączyć sprawdzanie poprawności podpisu, uruchom następujące polecenie, zastępując drugi parametr swój identyfikator obszaru roboczego analizy dzienników:

 ```python
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <LogAnalyticsworkspaceId>
 ```

## <a name="supported-runbook-types"></a>Typy obsługiwane elementu runbook

Linux hybrydowymi elementami roboczymi Runbook nie obsługują pełny zestaw typy elementu runbook, które zostały znalezione w automatyzacji Azure.

Następujące typy elementów runbook działają na hybrydowy proces roboczy Linux:

* Python 2
* PowerShell
 
Następujące typy elementu runbook nie działają na hybrydowy proces roboczy Linux:

* Przepływ pracy programu PowerShell
* Graficzna
* Graficzny przepływ pracy programu PowerShell

## <a name="next-steps"></a>Kolejne kroki

* Przegląd [uruchamiania elementów runbook na hybrydowy proces roboczy elementu Runbook](automation-hrw-run-runbooks.md) Aby dowiedzieć się, jak skonfigurować elementy runbook do automatyzacji procesów w lokalnym centrum danych lub w innym środowisku chmury.
* Aby uzyskać instrukcje dotyczące sposobu usuwania hybrydowych procesów roboczych elementu Runbook, zobacz [Usuń Azure Automation hybrydowe procesy robocze elementu Runbook](automation-remove-hrw.md)
