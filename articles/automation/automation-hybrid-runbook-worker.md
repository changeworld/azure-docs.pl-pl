---
title: Proces roboczy elementu Runbook hybrydowego usługi Automatyzacja Azure
description: Ten artykuł zawiera informacje o instalowaniu i używaniu procesu roboczego Runbook hybrydowych, która jest funkcją automatyzacji Azure, która służy do uruchamiania elementów runbook na maszynach w lokalnym centrum danych lub dostawcy usług w chmurze.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 54b2cab2ad6b1a22d35fcf0755f257063573e58b
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37128626"
---
# <a name="automate-resources-in-your-datacenter-or-cloud-by-using-hybrid-runbook-worker"></a>Automatyzację zasobów w centrum danych lub w chmurze przy użyciu hybrydowego procesu roboczego elementu Runbook

Elementy Runbook automatyzacji Azure nie można uzyskać dostępu do zasobów w innych chmur lub w środowisku lokalnym, ponieważ działają na platformie Azure chmury. Funkcja hybrydowy proces roboczy elementu Runbook usługi Automatyzacja Azure służy do uruchamiania elementów runbook bezpośrednio na komputerze, który jest hostem roli i względem zasobów w środowisku, aby zarządzać tymi zasobów lokalnych. Elementy Runbook są przechowywane i zarządzane w automatyzacji Azure i następnie dostarczany do co najmniej jednym komputerze wyznaczonym.

Na poniższym obrazie przedstawiono tę funkcję:

![Omówienie hybrydowych procesów roboczych elementów Runbook](media/automation-hybrid-runbook-worker/automation.png)

Każdy hybrydowy proces roboczy elementu Runbook jest członkiem grupy hybrydowego procesu roboczego elementu Runbook, którą określono podczas instalacji agenta. Grupy mogą obejmować jednego agenta, ale można zainstalować wielu agentów w grupie wysokiej dostępności.

Po uruchomieniu elementu runbook na hybrydowy proces roboczy elementu Runbook można określić grupy, do której jest uruchamiany na. Każdy pracownik w grupie sonduje Automatyzacja Azure, aby sprawdzić, czy wszystkie zadania są dostępne. Jeśli zadanie jest dostępne, to pierwszy proces roboczy, aby uzyskać zadanie przełączy go. Nie można określić określonego procesu roboczego.

## <a name="install-a-hybrid-runbook-worker"></a>Zainstaluj hybrydowy proces roboczy elementu Runbook

Proces, aby zainstalować hybrydowy proces roboczy elementu Runbook zależy od systemu operacyjnego. Poniższa tabela zawiera łącza do metod, które służą do instalacji. 

Aby zainstalować i skonfigurować Runbook Worker hybrydowego systemu Windows, można użyć dwóch metod. Zalecaną metodą jest całkowicie zautomatyzować proces konfigurowania komputerów z systemem Windows przy użyciu element runbook usługi Automatyzacja. Druga metoda jest po procedurą krok po kroku, aby ręcznie zainstalować i skonfigurować rolę. Dla maszyn z systemem Linux możesz uruchomić skrypt w języku Python do zainstalowania agenta na komputerze.

|System operacyjny  |Typy wdrożeń  |
|---------|---------|
|Windows     | [Program PowerShell](automation-windows-hrw-install.md#automated-deployment)<br>[Ręcznie](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker)        |

> [!NOTE]
> Aby zarządzać konfiguracji serwerów obsługujących roli hybrydowy proces roboczy elementu Runbook z konfiguracji żądanego stanu (DSC), należy je dodać jako węzły DSC. Aby uzyskać więcej informacji na temat dołączania ich do zarządzania za pomocą usługi Konfiguracja DSC, zobacz [dołączania komputerów do zarządzania przez Konfiguracja DSC automatyzacji Azure](automation-dsc-onboarding.md).
>
>Po włączeniu [rozwiązania do zarządzania aktualizacjami](automation-update-management.md), każdy komputer, który jest podłączony do obszaru roboczego analizy dzienników Azure zostanie automatycznie skonfigurowany jako hybrydowy proces roboczy elementu Runbook do obsługi elementy runbook zawarte w tym rozwiązaniu. Jednak komputer nie jest zarejestrowany w żadnych grup hybrydowy proces roboczy już zdefiniowane na Twoim koncie automatyzacji. Komputer można dodać do grupy hybrydowego procesu roboczego elementu Runbook do Twojego konta automatyzacji do obsługi elementów runbook automatyzacji, tak długo, jak używasz tego samego konta dla rozwiązania i członkostwo w grupie hybrydowy proces roboczy elementu Runbook. Ta funkcja dodano do wersji 7.2.12024.0 hybrydowy proces roboczy elementu Runbook.

Przegląd [informacje na temat planowania sieci](#network-planning) przed rozpoczęciem wdrażania hybrydowego procesu roboczego elementu Runbook. Po pomyślnym wdrożeniu pracownika, przejrzyj [uruchamiania elementów runbook na hybrydowy proces roboczy elementu Runbook](automation-hrw-run-runbooks.md) Aby dowiedzieć się, jak skonfigurować elementy runbook do automatyzacji procesów w lokalnym centrum danych lub w innym środowisku chmury.

## <a name="remove-a-hybrid-runbook-worker"></a>Usuń hybrydowy proces roboczy elementu Runbook

Co najmniej jeden hybrydowymi elementami roboczymi Runbook można usunąć z grupy, lub można usunąć grupy, w zależności od wymagań. Aby usunąć hybrydowy proces roboczy elementu Runbook z komputera lokalnego, wykonaj następujące czynności:

1. W portalu Azure przejdź do swojego konta automatyzacji.
2. W obszarze **ustawienia**, wybierz pozycję **klucze** i zanotuj wartości **adres URL** i **podstawowy klucz dostępu**. Te informacje są niezbędne do następnego kroku.

### <a name="windows"></a>Windows

Otwórz sesję programu PowerShell w trybie administratora i uruchom następujące polecenie. Użyj **-Verbose** przełączać szczegółowy dziennik proces usuwania.

```powershell
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>
```

Aby usunąć stare maszyn z grupy hybrydowego procesu roboczego, Użyj opcjonalnego `machineName` parametru.

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey> -machineName <ComputerName>
```

### <a name="linux"></a>Linux

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Ten kod nie powoduje usunięcia programu Microsoft Monitoring Agent z komputera, tylko funkcje i konfiguracji roli hybrydowy proces roboczy elementu Runbook.

## <a name="remove-a-hybrid-worker-group"></a>Usuwanie grupy hybrydowych procesów roboczych

Aby usunąć grupę, należy najpierw usunąć hybrydowy proces roboczy elementu Runbook z każdego komputera, który jest członkiem grupy, korzystając z procedury przedstawionej wcześniej. Następnie wykonaj poniższe kroki, aby usunąć grupę:

1. Otwórz konto usługi Automatyzacja w portalu Azure.
1. W obszarze **automatyzacji procesu**, wybierz pozycję **hybrydowego procesu roboczego grupy**. Wybierz grupę, którą chcesz usunąć. Zostanie wyświetlona strona właściwości dla tej grupy.

   ![Strona właściwości](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

1. Na stronie właściwości dla wybranej grupy wybierz **usunąć**. Komunikat jest wyświetlany monit o potwierdzenie tej akcji. Wybierz **tak** Jeśli masz pewności, czy chcesz kontynuować.

   ![Komunikat z potwierdzeniem](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Ten proces może potrwać kilka sekund, aby zakończyć. Postęp możesz śledzić w sekcji **Powiadomienia** z poziomu menu.

## <a name="network-planning"></a>Konfigurowanie sieci

### <a name="hybrid-worker-role"></a>Hybrydowe roli procesu roboczego

Aby hybrydowy proces roboczy do nawiązania połączenia i zarejestruj analizy dzienników musi mieć dostęp do numer portu i adres URL, które zostały opisane w tej sekcji. Jest to dodatkowe [porty i adresy URL wymagane dla programu Microsoft Monitoring Agent](../log-analytics/log-analytics-agent-windows.md) do nawiązania połączenia analizy dzienników. 

Jeśli używasz serwera proxy do komunikacji między agentem i usługą analizy dzienników, upewnij się, że odpowiednie zasoby są dostępne. Jeśli używasz zapory, aby ograniczyć dostęp do Internetu, należy skonfigurować zaporę tak, aby zezwolić na dostęp.

Następujące adresy URL i port są wymagane dla roli hybrydowy proces roboczy elementu Runbook do komunikowania się z automatyzacji:

* Port: Ruch wychodzący do Internetu wymagany jest tylko TCP 443.
* Globalny adres URL: *.azure-automation.net
* Globalny adres URL Virginia wersji dla instytucji rządowych Stanów Zjednoczonych: *.azure automation.us
* Usługa agenta: https://\<workspaceId\>.agentsvc.azure-automation.net

Zaleca się używania adresów wymienionych podczas definiowania wyjątków. Adresy IP można pobrać [zakresów IP centrum danych Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Ten plik jest aktualizowana co tydzień i odzwierciedla aktualnie wdrożonych zakresy i nadchodzące zmiany zakresu adresów IP.

Jeśli masz konto automatyzacji, który jest zdefiniowany dla konkretnego regionu, można ograniczyć komunikacji z tym regionalne centrum danych. Poniższa tabela zawiera rekord DNS dla każdego regionu:

| **Region** | **Rekord DNS** |
| --- | --- |
| Środkowo-zachodnie stany USA | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice produkcyjną 1.azure-automation.net |
| Środkowo-południowe stany USA |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| Wschodnie stany USA 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Kanada Środkowa |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Europa Zachodnia |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Europa Północna |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Azja Południowo-Wschodnia |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Indie Środkowe |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Japonia Wschodnia |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Australia Południowo-Wschodnia |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Południowe Zjednoczone Królestwo | uks-jobruntimedata-prod-su1.azure-automation.net</br>UKS-agentservice produkcyjną 1.azure-automation.net |
| Administracja USA — Wirginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice produkcyjną 1.azure-automation.us |

Aby uzyskać listę adresów IP regionu zamiast nazwy regionów, Pobierz [adres IP centrum danych Azure](https://www.microsoft.com/download/details.aspx?id=41653) pliku XML z Microsoft Download Center.

> [!NOTE]
> Plik XML adres IP centrum danych Azure zawiera listę zakresów adresów IP, które są używane w centrach danych Microsoft Azure. Plik zawiera zakresy obliczeniowych, SQL i magazynu.
>
>Zaktualizowany plik jest wysyłany co tydzień. Plik odzwierciedla aktualnie wdrożonych zakresy i nadchodzące zmiany zakresu adresów IP. Nowe zakresy, które pojawiają się w pliku nie są używane w centrach danych, przez co najmniej jeden tydzień.
>
> Należy dobrze do pobrania nowego pliku XML, co tydzień. Następnie należy zaktualizować lokację poprawnie zidentyfikować usługi działające na platformie Azure. Azure ExpressRoute użytkowników należy zauważyć, że ten plik jest używany do zaktualizowania anonsu protokołu BGP (Border Gateway) Azure miejsca w pierwszym tygodniu miesiąca.

### <a name="update-management"></a>Zarządzanie aktualizacjami

Oprócz standardowych adresy i porty wymagane przez hybrydowy proces roboczy elementu Runbook następujące adresy są wymagane w szczególności do zarządzania aktualizacjami. Komunikacja z tych adresów odbywa się za pośrednictwem portu 443.

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*. ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *. oms.opinsights.azure.us        |
|*.blob.core.windows.net|*. blob.core.usgovcloudapi.net|

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Aby dowiedzieć się, jak rozwiązywać problemy z hybrydowych procesów roboczych elementu Runbook, zobacz [Rozwiązywanie problemów z hybrydowymi elementami roboczymi Runbook](troubleshoot/hybrid-runbook-worker.md#general)

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się, jak skonfigurować elementy runbook do automatyzacji procesów w lokalnym centrum danych lub w innym środowisku chmury, zobacz [uruchamiania elementów runbook na hybrydowy proces roboczy elementu Runbook](automation-hrw-run-runbooks.md).