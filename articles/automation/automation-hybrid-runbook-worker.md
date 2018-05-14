---
title: Hybrydowymi elementami roboczymi Runbook usługi Automatyzacja Azure
description: Ten artykuł zawiera informacje o instalowaniu i używaniu hybrydowy proces roboczy elementu Runbook, która jest funkcją automatyzacji Azure, która służy do uruchamiania elementów runbook na maszynach w lokalnym centrum danych lub dostawcy usług w chmurze.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 43a3427b05b8e4f1fbaf0f8f5e6b60da9e837a46
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="automate-resources-in-your-data-center-or-cloud-with-hybrid-runbook-worker"></a>Automatyzację zasobów w centrum danych lub w chmurze chronionej za pomocą hybrydowy proces roboczy elementu Runbook

Elementy Runbook automatyzacji Azure nie można uzyskać dostępu do zasobów w innych chmur lub w środowisku lokalnym, ponieważ działają w chmurze Azure. Funkcja hybrydowy proces roboczy elementu Runbook usługi Automatyzacja Azure umożliwia uruchamianie elementów runbook, bezpośrednio na komputerze hostującym rolę i względem zasobów w środowisku, aby zarządzać tymi zasobów lokalnych. Elementy Runbook są przechowywane i zarządzane w automatyzacji Azure i następnie dostarczany do co najmniej jednym komputerze wyznaczonym.

Na poniższej ilustracji przedstawiono tę funkcję:

![Omówienie procesu roboczego elementu Runbook hybrydowego](media/automation-hybrid-runbook-worker/automation.png)

## <a name="hybrid-runbook-worker-groups"></a>Hybrydowego procesu roboczego elementu Runbook grup

Każdy hybrydowy proces roboczy elementu Runbook jest członkiem grupy hybrydowego procesu roboczego elementu Runbook, którą określono podczas instalacji agenta. Grupy mogą obejmować jednego agenta, ale można zainstalować wielu agentów w grupie wysokiej dostępności.

Po uruchomieniu elementu runbook na hybrydowy proces roboczy elementu Runbook można określić grupy, do której jest uruchamiany na. Każdy pracownik w grupie sonduje Automatyzacja Azure, aby sprawdzić, czy wszystkie zadania są dostępne. Jeśli istnieje zadanie to pierwszy proces roboczy, aby uzyskać zadanie bierze go. Nie można określić określonego procesu roboczego.

## <a name="installing-a-hybrid-runbook-worker"></a>Instalowanie hybrydowy proces roboczy elementu Runbook

Proces, aby zainstalować hybrydowego Runbook worker różni się w zależności od systemu operacyjnego. Poniższa tabela zawiera łącza do różnych metod, używanej do zainstalowania hybrydowy proces roboczy elementu Runbook. Aby zainstalować i skonfigurować Windows hybrydowego Runbook Worker, istnieją dwie metody dostępne. Zalecaną metodą jest całkowicie zautomatyzować proces, należy skonfigurować komputer z systemem Windows przy użyciu element runbook usługi Automatyzacja. Druga metoda jest po procedurą krok po kroku, aby ręcznie zainstalować i skonfigurować rolę. Dla maszyn z systemem Linux Uruchom skrypt w języku Python do zainstalowania agenta na komputerze

|System operacyjny  |Typy wdrożeń  |
|---------|---------|
|Windows     | [Program PowerShell](automation-windows-hrw-install.md#automated-deployment)<br>[Ręcznie](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#installing-linux-hybrid-runbook-worker)        |

> [!NOTE]
> Aby zarządzać konfiguracji serwerów pomocniczych roli hybrydowy proces roboczy elementu Runbook z konfiguracji żądanego stanu (DSC), należy je dodać jako węzły DSC. Aby uzyskać więcej informacji na temat dołączania ich do zarządzania za pomocą usługi Konfiguracja DSC, zobacz [dołączania komputerów do zarządzania przez Konfiguracja DSC automatyzacji Azure](automation-dsc-onboarding.md).
>
>Po włączeniu [rozwiązania do zarządzania aktualizacjami](automation-update-management.md), dowolnego komputera podłączonego do obszaru roboczego analizy dzienników jest automatycznie konfigurowany jako hybrydowy proces roboczy elementu Runbook do obsługi elementy runbook zawarte w tym rozwiązaniu. Jednak nie jest zarejestrowany z wszystkich grup hybrydowy proces roboczy już zdefiniowane na Twoim koncie automatyzacji. Komputer można dodać do grupy hybrydowego procesu roboczego elementu Runbook do Twojego konta automatyzacji do obsługi elementów runbook automatyzacji, tak długo, jak używasz tego samego konta dla rozwiązania i członkostwa w grupie hybrydowy proces roboczy elementu Runbook. Ta funkcjonalność została dodana do wersji 7.2.12024.0 hybrydowego procesu roboczego elementu Runbook.

Przegląd [informacje na temat planowania sieci](#network-planning) przed rozpoczęciem wdrażania hybrydowego procesu roboczego elementu Runbook. Po pomyślnym wdrożeniu runbook worker, przejrzyj [uruchamiania elementów runbook na hybrydowy proces roboczy elementu Runbook](automation-hrw-run-runbooks.md) Aby dowiedzieć się, jak skonfigurować elementy runbook do automatyzacji procesów w lokalnym centrum danych lub w innym środowisku chmury.

## <a name="removing-hybrid-runbook-worker"></a>Usuwanie hybrydowy proces roboczy elementu Runbook

Co najmniej jeden hybrydowymi elementami roboczymi Runbook można usunąć z grupy lub usunąć grupy, w zależności od wymagań. Aby usunąć hybrydowy proces roboczy elementu Runbook z komputera lokalnego, wykonaj następujące czynności:

1. W portalu Azure przejdź do swojego konta automatyzacji.
2. Z **ustawienia** bloku, wybierz opcję **klucze** i zanotuj wartości dla pola **adres URL** i **podstawowy klucz dostępu**. Te informacje są niezbędne do następnego kroku.

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
> Nie powoduje usunięcia programu Microsoft Monitoring Agent z komputera, tylko funkcje i konfiguracji roli hybrydowy proces roboczy elementu Runbook.

## <a name="remove-hybrid-worker-groups"></a>Usuń grupy hybrydowego procesu roboczego

Aby usunąć grupę, należy najpierw usunąć hybrydowy proces roboczy elementu Runbook z każdego komputera, który jest członkiem grupy, korzystając z procedury przedstawionej wcześniej, a następnie wykonaj następujące kroki, aby usunąć grupę.

1. Otwórz konto usługi Automatyzacja w portalu Azure.
1. W obszarze **automatyzacji procesu**, wybierz pozycję **hybrydowego procesu roboczego grupy**. Wybierz grupę, którą chcesz usunąć. Po wybraniu określonej grupy, **grupy hybrydowych procesów roboczych** zostanie wyświetlona strona właściwości.

   ![Strona grupy hybrydowych procesów roboczych elementu Runbook](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

1. Na stronie właściwości dla wybranej grupy kliknij **usunąć**. Zostanie wyświetlony komunikat prośbą o potwierdzenie tej akcji, wybierz **tak** czy na pewno chcesz kontynuować.

   ![Okno dialogowe potwierdzenia usunięcia grupy](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Ten proces może potrwać kilka sekund. Możesz śledzić postęp w sekcji **Powiadomienia** z poziomu menu.

## <a name="network-planning"></a>Konfigurowanie sieci

### <a name="hybrid-worker-role"></a>Hybrydowe roli procesu roboczego

Aby hybrydowy proces roboczy do nawiązania połączenia i zarejestruj analizy dzienników musi mieć dostęp do numer portu i adres URL, które zostały opisane w tej sekcji. Jest to dodatkowe [porty i adresy URL wymagane dla programu Microsoft Monitoring Agent](../log-analytics/log-analytics-agent-windows.md) do nawiązania połączenia analizy dzienników.

Jeśli używasz serwera proxy do komunikacji między agentem i usługą analizy dzienników, upewnij się, że odpowiednie zasoby są dostępne. Jeśli używasz zapory, aby ograniczyć dostęp do Internetu, należy skonfigurować zaporę tak, aby zezwolić na dostęp.

Następujące adresy URL i port są wymagane dla roli hybrydowy proces roboczy elementu Runbook do komunikowania się z automatyzacji:

* Port: Ruch wychodzący do Internetu wymagany jest tylko TCP 443.
* Globalny adres URL: *.azure-automation.net
* Globalny adres URL Virginia wersji dla instytucji rządowych Stanów Zjednoczonych: *.azure automation.us
* Usługa agenta: https://\<workspaceId\>.agentsvc.azure-automation.net

Jeśli masz konto automatyzacji, który jest zdefiniowany dla konkretnego regionu, można ograniczyć komunikacji z tym regionalne centrum danych. Poniższa tabela zawiera rekord DNS dla każdego regionu.

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
> Plik XML adres IP centrum danych Azure zawiera listę zakresów adresów IP, które są używane w centrach danych Microsoft Azure. Zakresy obliczeniowych, SQL i Magazyn znajdują się w pliku.
>
>Zaktualizowany plik jest wysyłany co tydzień. Plik odzwierciedla aktualnie wdrożonych zakresy i nadchodzące zmiany zakresu adresów IP. Nowe zakresy, które pojawiają się w pliku nie są używane w centrach danych, przez co najmniej jeden tydzień.
>
> Należy dobrze do pobrania nowego pliku XML, co tydzień. Następnie należy zaktualizować lokację poprawnie zidentyfikować usługi działające na platformie Azure. Azure ExpressRoute użytkowników należy zauważyć, że ten plik ma być używane do aktualizowania anonsu protokołu BGP (Border Gateway) Azure miejsca w pierwszym tygodniu każdego miesiąca.

### <a name="update-management"></a>Zarządzanie aktualizacjami

Oprócz standardowych adresy i porty wymagane przez hybrydowy proces roboczy elementu Runbook następujące adresy są wymagane w szczególności do zarządzania aktualizacjami. Komunikacja z tych adresów odbywa się za pośrednictwem portu 443.

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*. ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *. oms.opinsights.azure.us        |
|*.blob.core.windows.net|*. blob.core.usgovcloudapi.net|

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Hybrydowy proces roboczy elementu Runbook jest zależna od programu Microsoft Monitoring Agent, aby komunikować się z Twoim kontem automatyzacji Zarejestruj pracownika, otrzymywanie zadania elementów runbook i zgłoszenia stanu. W przypadku niepowodzenia rejestracji pracownika poniżej przedstawiono niektóre możliwe przyczyny błędu:

1. Hybrydowy proces roboczy jest związany z serwera proxy lub zapory.

   Sprawdź, czy komputer ma dostęp ruchu wychodzącego do *.azure automation.net na porcie 443.

2. Komputer, hybrydowy proces roboczy na którym działa ma mniej niż minimalne wymagania sprzętowe.

   Komputery z systemem hybrydowy proces roboczy elementu Runbook powinna spełniać minimalne wymagania sprzętowe przed oznaczeniem go do obsługi tej funkcji. W przeciwnym razie w zależności od innych procesów w tle i rywalizacji spowodowane przez elementy runbook podczas wykonywania na wykorzystanie zasobów komputera staje się nadmiernie wykorzystywany i spowodować opóźnienia zadania elementu runbook i przekroczeń limitu czasu.

   Upewnij się, że komputer wyznaczony do uruchomienia funkcji hybrydowego procesu roboczego elementu Runbook spełnia minimalne wymagania sprzętowe. Jeśli tak, monitorowanie wykorzystania procesora CPU i pamięci, aby określić korelacja wydajności procesów hybrydowy proces roboczy elementu Runbook i Windows. Brak pamięci lub dużego wykorzystania procesora CPU, może to oznaczać konieczność uaktualnienia lub dodać dodatkowych procesorów lub zwiększ ilość pamięci do adresów wąskie gardło zasobów i Usuń przyczynę błędu. Opcjonalnie zaznacz zasób różnych obliczeń, który może obsługiwać minimalne wymagania i skalowania, gdy wymaganego obciążenia wskazywać wzrost jest konieczne.

3. Usługa Microsoft Monitoring Agent nie jest uruchomiona.

   Jeśli usługa Microsoft Monitoring Agent Windows nie jest uruchomiona, zapobiega to hybrydowy proces roboczy elementu Runbook komunikację z usługi Automatyzacja Azure. Sprawdź, agent nie działa, wprowadzając następujące polecenie w programie PowerShell: `get-service healthservice`. Jeśli usługa zostanie zatrzymana, wprowadź następujące polecenie w programie PowerShell, aby uruchomić usługę: `start-service healthservice`.

4. W **aplikacji i usług Menedżera Logs\Operations** dziennika zdarzeń, zobacz zdarzenia 4502 i zawierający EventMessage **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**z następujący opis: *certyfikat przedstawiony przez usługę \<wsid\>. oms.opinsights.azure.com nie został wystawiony przez urząd certyfikacji używany dla usług firmy Microsoft. Skontaktuj się z administratorem sieci, aby sprawdzić, czy działają z serwera proxy przechwytującego komunikację TLS/SSL. Artykuł KB3126513 zawiera dodatkowe informacje dotyczące rozwiązywania problemów, które występują problemy dotyczące połączenia.*
    Może to być spowodowane serwera proxy lub sieci Zapora blokuje komunikację do systemu Microsoft Azure. Sprawdź, czy komputer ma dostęp ruchu wychodzącego do *.azure automation.net na porty 443.

Dzienniki są przechowywane lokalnie na każdym hybrydowy proces roboczy na C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes. Można sprawdzić, czy istnieją ostrzeżenia lub błędu zdarzeń zapisywanych w **aplikacji i usług Logs\Microsoft-SMA\Operations** i **aplikacji i usług Menedżera Logs\Operations** dziennika zdarzeń, który wskazuje z łącznością lub innego problemu dołączania roli do automatyzacji Azure lub problem podczas wykonywania normalnych operacji.

Dodatkowe kroki dotyczące rozwiązywania problemów z zarządzania aktualizacjami, zobacz [zarządzania aktualizacjami — Rozwiązywanie problemów](automation-update-management.md#troubleshooting)

## <a name="next-steps"></a>Kolejne kroki

Przegląd [uruchamiania elementów runbook na hybrydowy proces roboczy elementu Runbook](automation-hrw-run-runbooks.md) Aby dowiedzieć się, jak skonfigurować elementy runbook do automatyzacji procesów w lokalnym centrum danych lub w innym środowisku chmury.
