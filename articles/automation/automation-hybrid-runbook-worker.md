---
title: Usługa Azure Automation hybrydowego procesu roboczego Runbook
description: Ten artykuł zawiera informacje na temat instalowania i przy użyciu hybrydowego procesu roboczego Runbook, jest to funkcja usługi Azure Automation, którego używasz do uruchamiania elementów runbook na maszynach w lokalnym centrum danych lub dostawca usług w chmurze.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c4e237af7e85223839b3f26bcc33007f8abb9d0a
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47034233"
---
# <a name="automate-resources-in-your-datacenter-or-cloud-by-using-hybrid-runbook-worker"></a>Automatyzuj zasobów w centrum danych lub w chmurze przy użyciu hybrydowego procesu roboczego Runbook

Elementy Runbook w usłudze Azure Automation nie można uzyskać dostęp do zasobów w innych chmurach, lub w środowisku w środowisku lokalnym, ponieważ są one uruchomione na platformie Azure w chmurze. Funkcja hybrydowego procesu roboczego Runbook usługi Azure Automation umożliwia uruchamianie elementów runbook bezpośrednio na komputerze, który jest hostem roli i w odniesieniu do zasobów w środowisku w celu zarządzania tymi zasobami lokalnymi. Elementy Runbook są przechowywane i zarządzane w usłudze Azure Automation i następnie dostarczane do co najmniej jeden komputer wyznaczonym.

Na poniższym obrazie przedstawiono tę funkcję:

![Omówienie hybrydowych procesów roboczych elementów Runbook](media/automation-hybrid-runbook-worker/automation.png)

Każdy hybrydowy proces roboczy elementu Runbook jest członkiem grupy hybrydowego procesu roboczego Runbook, który jest określany podczas instalacji agenta. Grupa może obejmować jednego agenta, ale można zainstalować wielu agentów w grupie w celu zapewnienia wysokiej dostępności.

Podczas uruchamiania elementu runbook w hybrydowym procesie roboczym elementu Runbook należy określić grupę, która działa na. Każdemu procesowi roboczemu w tej grupie sonduje usługę Azure Automation, aby sprawdzić, czy wszystkie zadania są dostępne. Jeśli zadanie jest dostępna, to pierwszy proces roboczy, aby zakończyć dane zadanie przełączy go. Nie można określić określonego procesu roboczego. Zadanie [limity](../azure-subscription-service-limits.md#automation-limits) zastosowanie zarówno do piaskownic usługi Azure, jak i hybrydowych procesów roboczych Runbook.

## <a name="install-a-hybrid-runbook-worker"></a>Instalowanie hybrydowego procesu roboczego elementu Runbook

Proces, aby zainstalować hybrydowego procesu roboczego Runbook zależy od systemu operacyjnego. Poniższa tabela zawiera łącza do metod, które można użyć do zainstalowania.

Aby zainstalować i skonfigurować Windows hybrydowego procesu roboczego Runbook, można użyć dwóch metod. Zalecaną metodą jest całkowicie zautomatyzować proces konfigurowania komputerów Windows przy użyciu elementu runbook usługi Automation. Druga metoda jest następujące procedury krok po kroku, aby ręcznie zainstalować i skonfigurować rolę. Dla maszyn z systemem Linux możesz uruchomić skrypt języka Python, aby zainstalować agenta na maszynie.

|System operacyjny  |Typy wdrożeń  |
|---------|---------|
|Windows     | [Program PowerShell](automation-windows-hrw-install.md#automated-deployment)<br>[Ręcznie](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker)        |

> [!NOTE]
> Aby zarządzać konfiguracji serwerów, które obsługują roli hybrydowego procesu roboczego Runbook za pomocą Desired State Configuration (DSC), należy dodać je jako węzłów DSC. Aby uzyskać więcej informacji na temat dołączania ich do zarządzania za pomocą DSC, zobacz [dołączanie maszyn w celu zarządzania przez usługi Azure Automation DSC](automation-dsc-onboarding.md).
>
>Po włączeniu [rozwiązanie do zarządzania aktualizacjami](automation-update-management.md), dowolnym komputerze, który jest połączony z obszarem roboczym usługi Azure Log Analytics zostanie automatycznie skonfigurowany jako hybrydowy proces roboczy elementu Runbook w celu obsługi elementów runbook zawartych w tym rozwiązaniu. Jednak komputer nie jest zarejestrowany w żadnych grupach hybrydowych procesów roboczych, które już zdefiniowane na koncie usługi Automation. Komputer można dodać do grupy hybrydowego procesu roboczego Runbook na Twoim koncie usługi Automation w celu obsługi elementów runbook usługi Automation, tak długo, jak długo używasz tego samego konta zarówno dla rozwiązania i hybrydowego procesu roboczego Runbook członkostwa w grupie. Ta funkcja ma dodano do wersji 7.2.12024.0 hybrydowego procesu roboczego Runbook.

Przegląd [informacje dotyczące planowania sieci](#network-planning) przed rozpoczęciem wdrażania hybrydowego procesu roboczego elementu Runbook. Po pomyślnym wdrożeniu proces roboczy, przejrzyj [uruchamianie elementów runbook w hybrydowym procesie roboczym elementu Runbook](automation-hrw-run-runbooks.md) informacje na temat konfigurowania elementów runbook do automatyzacji procesów w lokalnym centrum danych lub w innym środowisku chmury.

## <a name="remove-a-hybrid-runbook-worker"></a>Usuń hybrydowy proces roboczy elementu Runbook

Co najmniej jeden hybrydowych procesów roboczych Runbook bankowym można usunąć z grupy, lub możesz usunąć grupę, w zależności od wymagań. Aby usunąć hybrydowy proces roboczy elementu Runbook na komputerze lokalnym, wykonaj następujące czynności:

1. W witrynie Azure portal przejdź do konta usługi Automation.
2. W obszarze **ustawienia**, wybierz opcję **klucze** i zwróć uwagę na wartości dla **adresu URL** i **podstawowy klucz dostępu**. Ta informacja będzie potrzebna do kolejnego kroku.

### <a name="windows"></a>Windows

Otwórz sesję programu PowerShell w trybie administratora i uruchom następujące polecenie. Użyj **-Verbose** przełącznika, aby uzyskać szczegółowy dziennik proces usuwania.

```powershell
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>
```

Aby usunąć stare maszyny z grupy hybrydowych procesów roboczych, Użyj opcjonalnego `machineName` parametru.

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey> -machineName <ComputerName>
```

### <a name="linux"></a>Linux

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Ten kod nie powoduje usunięcia programu Microsoft Monitoring Agent z komputera, tylko funkcje i konfiguracji roli hybrydowego procesu roboczego Runbook.

## <a name="remove-a-hybrid-worker-group"></a>Usuwanie grupy hybrydowych procesów roboczych

Aby usunąć grupę, należy najpierw usunąć hybrydowy proces roboczy elementu Runbook z każdego komputera, który jest członkiem grupy za pomocą procedury przedstawionej wcześniej. Następnie wykonaj poniższe kroki, aby usunąć grupę:

1. Otwórz konto usługi Automation w witrynie Azure portal.
1. W obszarze **automatyzacji procesów**, wybierz opcję **grupy hybrydowych procesów roboczych**. Wybierz grupę, która ma zostać usunięty. Zostanie wyświetlona strona właściwości dla tej grupy.

   ![Strona właściwości](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

1. Na stronie właściwości dla wybranej grupy wybierz **Usuń**. Komunikat jest wyświetlany monit o potwierdzenie tej akcji. Wybierz **tak** Jeśli, na pewno chcesz kontynuować.

   ![Komunikat z potwierdzeniem](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Ten proces może potrwać kilka sekund, aby zakończyć. Postęp możesz śledzić w sekcji **Powiadomienia** z poziomu menu.

## <a name="network-planning"></a>Konfigurowanie sieci

### <a name="hybrid-worker-role"></a>Rola procesu roboczego hybrydowego

Hybrydowego procesu roboczego Runbook nawiązać połączenie i zarejestrować w usłudze Log Analytics musi mieć dostęp do numeru portu oraz w adresach URL, które są opisane w tej sekcji. Jest to uzupełnienie [portów i adresów URL wymaganych dla programu Microsoft Monitoring Agent](../log-analytics/log-analytics-agent-windows.md) do łączenia z usługą Log Analytics. 

Jeśli używasz serwera proxy do komunikacji między agentem i usługę Log Analytics, upewnij się, że odpowiednie zasoby są dostępne. Jeśli używasz zapory, aby ograniczyć dostęp do Internetu, należy skonfigurować zaporę, aby zezwolić na dostęp. Jeśli używasz bramy pakietu OMS jako serwer proxy, upewnij się, że jest ono skonfigurowane dla hybrydowych procesów roboczych. Aby uzyskać instrukcje, jak to zrobić, zobacz [skonfigurować bramę pakietu OMS dla usługi Automation hybrydowych procesów roboczych](https://docs.microsoft.com/azure/log-analytics/log-analytics-oms-gateway#configure-for-automation-hybrid-workers).

Następujących portów i adresów URL są wymagane dla roli hybrydowego procesu roboczego Runbook do komunikowania się z usługą Automation:

* Port: Tylko 443 protokołu TCP jest wymagana dla ruchu wychodzącego dostępu do Internetu.
* Globalny adres URL: *.azure-automation.net
* Globalny adres URL Administracja USA — Wirginia: *.azure-automation.us
* Usługa agenta: https://\<workspaceId\>.agentsvc.azure-automation.net

Zaleca się używania adresów wymienionych podczas definiowania wyjątków. Adresy IP, możesz pobrać [zakresów IP centrum danych Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Ten plik jest aktualizowana co tydzień i odzwierciedla aktualnie wdrożone zakresy i wszystkie nadchodzące zmiany w zakresach adresów IP.

Jeśli masz konto usługi Automation, która jest zdefiniowana dla konkretnego regionu, można ograniczyć komunikację z tym regionalnym centrum danych. Poniższa tabela zawiera rekord DNS dla każdego regionu:

| **Region** | **Rekord DNS** |
| --- | --- |
| Środkowo-zachodnie stany USA | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| Środkowo-południowe stany USA |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| Wschodnie stany USA 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Kanada Środkowa |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Europa Zachodnia |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Europa Północna |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Azja Południowo-Wschodnia |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Indie Środkowe |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Japonia Wschodnia |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Australia Południowo-Wschodnia |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Południowe Zjednoczone Królestwo | uks-jobruntimedata-prod-su1.azure-automation.net</br>UKS-agentservice-prod-1.azure-automation.net |
| Administracja USA — Wirginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

Aby uzyskać listę adresów IP regionu zamiast nazwy regionów, Pobierz [adres IP centrum danych Azure](https://www.microsoft.com/download/details.aspx?id=41653) plik XML z Microsoft Download Center.

> [!NOTE]
> Plik XML adres IP centrum danych platformy Azure zawiera listę zakresów adresów IP, które są używane w centrach danych platformy Microsoft Azure. Plik zawiera zakresy obliczeń, SQL i storage.
>
>Tydzień jest publikowany zaktualizowany plik. Plik odzwierciedla aktualnie wdrożone zakresy i wszystkie nadchodzące zmiany w zakresach adresów IP. Nowe zakresy, które pojawiają się w pliku nie są używane w centrach danych, przez co najmniej jeden tydzień.
>
> To dobry pomysł, aby pobrać nowy plik XML, co tydzień. Następnie należy zaktualizować lokację do prawidłowo identyfikować usługi uruchomione na platformie Azure. Użytkownicy usługi Azure ExpressRoute należy zauważyć, że ten plik jest używany do aktualizacji anonsu protokołu BGP (Border Gateway) miejsca platformy Azure w pierwszym tygodniu każdego miesiąca.

### <a name="update-management"></a>Zarządzanie aktualizacjami

Oprócz standardowych adresy i porty wymagane przez hybrydowy proces roboczy elementu Runbook następujące adresy są wymagane dla rozwiązania Update Management. Komunikacja z tych adresów odbywa się za pośrednictwem portu 443.

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*. ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *. oms.opinsights.azure.us        |
|*.blob.core.windows.net|*. blob.core.usgovcloudapi.net|

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Aby dowiedzieć się, jak rozwiązywać problemy z hybrydowych procesów roboczych Runbook, zobacz [Rozwiązywanie problemów z hybrydowych procesów roboczych Runbook](troubleshoot/hybrid-runbook-worker.md#general)

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się, jak skonfigurować elementy runbook do automatyzacji procesów w lokalnym centrum danych lub w innym środowisku chmury, zobacz [uruchamianie elementów runbook w hybrydowym procesie roboczym elementu Runbook](automation-hrw-run-runbooks.md).
