---
title: Samouczek — zarządzanie konfiguracją maszyny wirtualnej systemu Windows na platformie Azure
description: W tym samouczku dowiesz się, jak identyfikować zmiany i zarządzać aktualizacjami pakietów na maszynie wirtualnej systemu Windows
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/05/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: d97323f1916ee46e6b1f8d4ca8723b950baca39c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238580"
---
# <a name="tutorial-monitor-changes-and-update-a-windows-virtual-machine-in-azure"></a>Samouczek: Monitorowanie zmian i aktualizowanie maszyny wirtualnej systemu Windows na platformie Azure

Dzięki usłudze Azure [Change Tracking](../../automation/change-tracking.md) and [Update Management](../../automation/automation-update-management.md)można łatwo identyfikować zmiany na maszynach wirtualnych systemu Windows na platformie Azure i zarządzać aktualizacjami systemu operacyjnego dla tych maszyn wirtualnych.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Zarządzanie aktualizacjami systemu Windows.
> * Monitoruj zmiany i zapasy.

## <a name="open-azure-cloud-shell"></a>Otwieranie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna powłoka interaktywna, której można użyć do uruchomienia kroków w tym artykule. Ma wspólne narzędzia platformy Azure preinstalowane i skonfigurowane do użycia z kontem platformy Azure.

Aby otworzyć dowolny blok kodu w usłudze Cloud Shell, po prostu wybierz **opcję Wypróbuj go** w prawym górnym rogu tego bloku kodu.

Możesz również otworzyć usługę Cloud Shell w [https://shell.azure.com/powershell](https://shell.azure.com/powershell)osobnej karcie przeglądarki, przechodząc do . Wybierz **pozycję Kopiuj,** aby skopiować bloki kodu, wkleić je na karcie Powłoka chmury i wybierz klawisz Enter, aby uruchomić kod.

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Aby w tym samouczku móc skonfigurować monitorowanie i zarządzanie aktualizacjami na platformie Azure, konieczne jest posiadanie maszyny wirtualnej z systemem Windows na platformie Azure.

Najpierw ustaw nazwę użytkownika i hasło administratora maszyny wirtualnej przy użyciu polecenia [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Następnie utwórz maszynę wirtualną za pomocą [nowego pliku AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). Poniższy przykład tworzy maszynę wirtualną o nazwie `myVM` w `East US` lokalizacji. Jeśli jeszcze nie istnieją, tworzona jest grupa `myResourceGroupMonitor` zasobów i obsługujące zasoby sieciowe:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupMonitor" `
    -Name "myVM" `
    -Location "East US" `
    -Credential $cred
```

Utworzenie maszyny wirtualnej i zasobów może potrwać kilka minut.

## <a name="manage-windows-updates"></a>Zarządzanie aktualizacjami systemu Windows

Zarządzanie aktualizacjami ułatwia zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych systemu Windows platformy Azure. Bezpośrednio z maszyny Wirtualnej można szybko:

- Ocenianie stanu dostępnych aktualizacji.
- Planowanie instalacji wymaganych aktualizacji.
- Przejrzyj wyniki wdrożenia, aby sprawdzić, czy aktualizacje zostały pomyślnie zastosowane do maszyny Wirtualnej.

Aby uzyskać informacje o cenach, zobacz [Ceny automatyzacji dla zarządzania aktualizacjami](https://azure.microsoft.com/pricing/details/automation/).

### <a name="enable-update-management"></a>Włączanie rozwiązania Update Management

Aby włączyć zarządzanie aktualizacjami dla maszyny Wirtualnej:

1. Po lewej stronie okna wybierz pozycję **Maszyny wirtualne**.
1. Wybierz maszynę wirtualną z listy.
1. W okienku **Operacje** okna maszyny Wirtualnej wybierz pozycję **Aktualizuj zarządzanie**.
1. Zostanie otwarte okno **Włącz zarządzanie aktualizacjami.**

Sprawdzanie poprawności odbywa się w celu ustalenia, czy zarządzanie aktualizacjami jest włączone dla tej maszyny Wirtualnej. Sprawdzanie poprawności obejmuje sprawdzanie obszaru roboczego usługi Log Analytics, dla połączonego konta automatyzacji i czy rozwiązanie znajduje się w obszarze roboczym.

Obszar roboczy [usługi Log Analytics](../../log-analytics/log-analytics-overview.md) służy do zbierania danych generowanych przez funkcje i usługi, takie jak Zarządzanie aktualizacjami. Obszar roboczy zawiera pojedynczą lokalizację do przeglądania i analizowania danych z wielu źródeł.

Aby wykonać dodatkowe akcje na maszynach wirtualnych, które wymagają aktualizacji, można użyć usługi Azure Automation do uruchamiania śmiób życiornikowych na maszynach wirtualnych. Takie działania obejmują pobieranie lub stosowanie aktualizacji.

Proces sprawdzania poprawności sprawdza również, czy maszyna wirtualna jest aprowizowana za pomocą agenta monitorowania firmy Microsoft (MMA) i robota administracyjnego hybrydowego systemu administracyjnego automatyzacji. Agenta można używać do komunikowania się z maszyną wirtualną i uzyskania informacji o stanie aktualizacji.

W oknie **Włącz zarządzanie aktualizacjami** wybierz obszar roboczy usługi Log Analytics i konto automatyzacji, a następnie wybierz pozycję **Włącz**. Włączenie rozwiązania trwa do 15 minut.

Każdy z następujących wymagań wstępnych, których brakuje podczas dołączania, jest dodawany automatycznie:

* Obszar roboczy [usługi Log Analytics](../../log-analytics/log-analytics-overview.md)
* [Automatyzacja](../../automation/automation-offering-get-started.md)
* [Hybrydowy proces roboczy egonika](../../automation/automation-hybrid-runbook-worker.md), który jest włączony na maszynie wirtualnej

Po włączeniu rozwiązania zostanie otwarte okno **zarządzania aktualizacjami.** Skonfiguruj lokalizację, obszar roboczy usługi Log Analytics i konto automatyzacji do użycia, a następnie wybierz pozycję **Włącz**. Jeśli te opcje są wygaszone, inne rozwiązanie automatyzacji jest włączone dla maszyny Wirtualnej i tego rozwiązania obszaru roboczego i konta automatyzacji musi być używany.

![Włącz rozwiązanie do zarządzania aktualizacjami](./media/tutorial-monitoring/manageupdates-update-enable.png)

Rozwiązanie do zarządzania aktualizacjami może potrwać do 15 minut. W tym czasie nie należy zamykać okna przeglądarki. Po włączeniu rozwiązania informacje dotyczące brakujących aktualizacji maszyny wirtualnej są przekazywane do dzienników usługi Azure Monitor. Może upłynąć od 30 minut do 6 godzin, aby dane stały się dostępne do analizy.

### <a name="view-an-update-assessment"></a>Wyświetlanie oceny aktualizacji

Po włączeniu zarządzania aktualizacjami zostanie wyświetlone okno **zarządzania aktualizacjami.** Po zakończeniu oceny aktualizacji na karcie **Brakujące aktualizacje** zostanie wyświetlona lista brakujących aktualizacji.

 ![Wyświetlanie stanu aktualizacji](./media/tutorial-monitoring/manageupdates-view-status-win.png)

### <a name="schedule-an-update-deployment"></a>Planowanie wdrożenia aktualizacji

Aby zainstalować aktualizacje, zaplanuj wdrożenie zgodnie z harmonogramem wydawania i oknem obsługi. Można wybrać, które typy aktualizacji do uwzględnienia we wdrożeniu. Możesz na przykład uwzględnić aktualizacje krytyczne lub aktualizacje zabezpieczeń i wykluczyć pakiety zbiorcze aktualizacji.

Aby zaplanować nowe wdrożenie aktualizacji dla maszyny Wirtualnej, wybierz **pozycję Zaplanuj wdrożenie aktualizacji** u góry okna **zarządzania aktualizacjami.** W oknie **wdrażania Nowa aktualizacja** określ następujące informacje:

| Opcja | Opis |
| --- | --- |
| **Nazwa** |Wprowadź unikatową nazwę, aby zidentyfikować wdrożenie aktualizacji. |
|**System operacyjny**| Wybierz **linux** lub **windows**.|
| **Grupy do aktualizacji** |W przypadku maszyn wirtualnych hostowanych na platformie Azure zdefiniuj kwerendę opartą na kombinacji subskrypcji, grup zasobów, lokalizacji i tagów. Ta kwerenda tworzy dynamiczną grupę maszyn wirtualnych hostowanych w usłudze Azure do uwzględnienia w twoim wdrożeniu. </br></br>W przypadku maszyn wirtualnych, które nie są hostowane na platformie Azure, wybierz istniejące zapisane wyszukiwanie. Za pomocą tego wyszukiwania można wybrać grupę tych maszyn wirtualnych do uwzględnienia we wdrożeniu. </br></br> Aby dowiedzieć się więcej, zobacz [Grupy dynamiczne](../../automation/automation-update-management-groups.md).|
| **Maszyny do aktualizacji** |Wybierz **pozycję Zapisane wyszukiwanie,** **Zaimportowana grupa**lub **Maszyny**.<br/><br/>W przypadku **wybrania opcji Maszyny**można wybrać poszczególne maszyny z listy rozwijanej. Gotowość każdej maszyny jest wyświetlana w kolumnie **GOTOWOŚĆ AGENTA AKTUALIZACJI** tabeli.</br></br> Aby dowiedzieć się więcej na temat różnych metod tworzenia grup komputerów w dziennikach usługi Azure Monitor, zobacz [Computer groups in Azure Monitor logs (Grupy komputerów w dziennikach usługi Azure Monitor)](../../azure-monitor/platform/computer-groups.md) |
|**Klasyfikacje aktualizacji**|Wybierz wszystkie niezbędne klasyfikacje aktualizacji.|
|**Dołączanie/wykluczanie aktualizacji**|Wybierz tę opcję, aby otworzyć okienko **Dołącz/Wyklucz.** Aktualizacje, które mają zostać uwzględnione i te, które mają zostać wykluczone, znajdują się na oddzielnych kartach. Aby uzyskać więcej informacji na temat sposobu obsługi [dołączania,](../../automation/automation-tutorial-update-management.md#schedule-an-update-deployment)zobacz Planowanie wdrożenia aktualizacji . |
|**Ustawienia harmonogramu**|Wybierz godzinę rozpoczęcia i wybierz **opcję Raz** lub **Cyklicznie**.|
| **Skrypty wstępne + post-skrypty**|Wybierz skrypty do uruchomienia przed i po wdrożeniu.|
| **Okno konserwacji** | Wprowadź liczbę minut ustawioną dla aktualizacji. Prawidłowe wartości wahają się od 30 do 360 minut. |
| **Kontrola ponownego uruchomienia**| Wybierz sposób obsługi ponownych uruchomień. Dostępne są:<ul><li>**Uruchom ponownie, jeśli jest to wymagane**</li><li>**Zawsze uruchamiaj ponownie**</li><li>**Nigdy nie uruchamiaj ponownie**</li><li>**Tylko ponowne uruchomienie komputera**</li></ul>**Uruchom ponownie, jeśli jest to wymagane** jest wybór domyślny. Jeśli wybierzesz **opcję Tylko ponowne uruchomienie,** aktualizacje nie są zainstalowane.|

Po zakończeniu konfigurowania harmonogramu kliknij przycisk **Utwórz,** aby powrócić do pulpitu nawigacyjnego stanu. W tabeli **Zaplanowane** jest wyświetlany utworzony harmonogram wdrażania.

Można również tworzyć wdrożenia aktualizacji programowo. Aby dowiedzieć się, jak utworzyć wdrożenie aktualizacji za pomocą interfejsu API REST, zobacz [Konfiguracje aktualizacji oprogramowania — Tworzenie](/rest/api/automation/softwareupdateconfigurations/create). Istnieje również przykładowy system runbook, którego można użyć do utworzenia cotygodniowego wdrożenia aktualizacji. Aby dowiedzieć się więcej o tym zestawieniu, zobacz [Tworzenie cotygodniowego wdrożenia aktualizacji dla co najmniej jednej maszyny wirtualnej w grupie zasobów](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

### <a name="view-results-of-an-update-deployment"></a>Wyświetlanie wyników wdrażania aktualizacji

Po rozpoczęciu zaplanowanego wdrożenia stan wdrożenia można zobaczyć na karcie **Aktualizuj wdrożenia** w oknie **Zarządzanie aktualizacjami.**

Jeśli wdrożenie jest aktualnie uruchomione, jego stan jest wyświetlany jako "W toku". Po pomyślnym zakończeniu stan zmienia się na "Zakończę pomyślnie". Ale jeśli wszelkie aktualizacje we wdrożeniu nie powiodą się, stan jest "Częściowo nie powiodło się."

Wybierz ukończone wdrożenie aktualizacji, aby wyświetlić pulpit nawigacyjny dla tego wdrożenia.

![Pulpit nawigacyjny stanu wdrożenia aktualizacji dla określonego wdrożenia](./media/tutorial-monitoring/manageupdates-view-results.png)

Kafelek **Aktualizuj wyniki** zawiera podsumowanie całkowitej liczby aktualizacji i wyników wdrożenia na maszynie Wirtualnej. Tabela po prawej stronie zawiera szczegółowy podział każdej aktualizacji i wyniki instalacji. Każdy wynik ma jedną z następujących wartości:

* **Nie podjęto próby:** aktualizacja nie jest zainstalowana. Nie było wystarczająco dużo czasu dostępnego na podstawie zdefiniowanego czasu trwania okna konserwacji.
* **Powodzenie**: aktualizacja powiodła się.
* **Niepowodzenie**: aktualizacja nie powiodła się.

Aby wyświetlić wszystkie wpisy dziennika utworzone przez wdrożenie, wybierz opcję **Wszystkie dzienniki**.

Wybierz **kafelek Dane wyjściowe,** aby wyświetlić strumień zadań systemu runbook odpowiedzialnego za zarządzanie wdrożeniem aktualizacji na docelowej maszynie wirtualnej.

Wybierz **błędy,** aby wyświetlić szczegółowe informacje o błędach wdrażania.

## <a name="monitor-changes-and-inventory"></a>Monitorowanie zmian i spisu

Na komputerach można zbierać i wyświetlać spis oprogramowania, plików, demonów systemu Linux, usług systemu Windows i kluczy rejestru systemu Windows. Śledzenie konfiguracji maszyn pomaga zidentyfikować problemy operacyjne w całym środowisku i lepiej zrozumieć stan maszyn.

### <a name="enable-change-and-inventory-management"></a>Włączanie zmian i zarządzania zapasami

Aby włączyć zarządzanie zmianami i zapasami maszyny Wirtualnej:

1. Po lewej stronie okna wybierz pozycję **Maszyny wirtualne**.
1. Wybierz maszynę wirtualną z listy.
1. W obszarze **Operacje** w oknie maszyny Wirtualnej wybierz pozycję **Śledzenie zapasów** lub **Zmień**.
1. Zostanie otwarte **okienko Włącz śledzenie zmian i zapasy.**

Skonfiguruj lokalizację, obszar roboczy usługi Log Analytics i konto automatyzacji, a następnie wybierz pozycję **Włącz**. Jeśli opcje są wygaszone, rozwiązanie automatyzacji jest już włączone dla maszyny Wirtualnej. W takim przypadku należy użyć już włączonego obszaru roboczego i konta automatyzacji.

Mimo że rozwiązania są wyświetlane oddzielnie w menu, są tym samym rozwiązaniem. Włączenie jednego z nich oznacza włączenie obydwu na maszynie wirtualnej.

![Włączanie śledzenia zmian i spisu](./media/tutorial-monitoring/manage-inventory-enable.png)

Po włączeniu rozwiązania może upłynąć trochę czasu, zanim pojawią się dane na maszynie Wirtualnej.

### <a name="track-changes"></a>Śledzenie zmian

Na maszynie wirtualnej w obszarze **OPERACJE**wybierz pozycję **Zmień śledzenie,** a następnie wybierz pozycję **Edytuj ustawienia**. Zostanie otwarte okienko **śledzenia zmian.** Wybierz typ ustawienia do śledzenia, a następnie kliknij pozycję **+ Dodaj** w celu skonfigurowania ustawień.

Dostępne opcje ustawień dla systemu Windows to:

* Rejestr systemu Windows
* Pliki systemu Windows

Aby uzyskać szczegółowe informacje na temat śledzenia zmian, zobacz [Rozwiązywanie problemów ze zmianami na maszynie wirtualnej](../../automation/automation-tutorial-troubleshoot-changes.md).

### <a name="view-inventory"></a>Wyświetlanie spisu

Na maszynie wirtualnej wybierz **pozycję Zapasy** w obszarze **OPERACJE**. Na karcie **Oprogramowanie** znajduje się tabela z znalezionym oprogramowaniem. Szczegóły wysokiego poziomu dla każdego rekordu oprogramowania są wyświetlane w tabeli. Szczegóły te obejmują nazwę oprogramowania, wersję, wydawcę i ostatnio odświeżony czas.

![Wyświetlanie spisu](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>Monitorowanie dzienników aktywności i zmian

W oknie **Śledzenie zmian** na maszynie wirtualnej wybierz pozycję **Zarządzaj połączeniem dziennika aktywności,** aby otworzyć okienko **dziennika aktywności platformy Azure.** Wybierz **pozycję Połącz,** aby połączyć śledzenie zmian z dziennikiem aktywności platformy Azure dla maszyny Wirtualnej.

Po włączeniu śledzenia zmian przejdź do **okienka Przegląd** maszyny Wirtualnej i wybierz **pozycję Zatrzymaj,** aby zatrzymać maszynę wirtualną. Po wyświetleniu monitu wybierz pozycję **Tak**, aby zatrzymać maszynę wirtualną. Po cofniętej alokacji maszyny Wirtualnej wybierz przycisk **Start,** aby ponownie uruchomić maszynę wirtualną.

Zatrzymywanie i ponowne uruchamianie maszyny Wirtualnej rejestruje zdarzenie w dzienniku aktywności. Wróć do okienka **Śledzenia zmiany** i wybierz kartę **Zdarzenia** u dołu okienka. Po pewnym czasie zdarzenia pojawiają się na wykresie i w tabeli. Możesz wybrać każde zdarzenie, aby wyświetlić szczegółowe informacje o tym zdarzeniu.

![Wyświetlanie zmian w dzienniku aktywności](./media/tutorial-monitoring/manage-activitylog-view-results.png)

Poprzedni wykres pokazuje zmiany, które wystąpiły w czasie. Po dodaniu połączenia dziennika aktywności platformy Azure wykres liniowy u góry wyświetla zdarzenia dziennika aktywności platformy Azure.

Każdy wiersz wykresów słupkowych reprezentuje inny typ zmiany, który można śledzić. Te typy to demony Linuksa, pliki, klucze rejestru systemu Windows, oprogramowanie i usługi systemu Windows. Karta **Zmiana** pokazuje szczegóły zmiany. Zmiany są wyświetlane w kolejności, w którym wystąpiły, a ostatnia zmiana jest wyświetlana jako pierwsza.

## <a name="next-steps"></a>Następne kroki

W tym samouczku skonfigurowano i przejrzano śledzenie zmian i zarządzanie aktualizacjami dla maszyny Wirtualnej. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie grupy zasobów i maszyny wirtualnej.
> * Zarządzanie aktualizacjami systemu Windows.
> * Monitoruj zmiany i zapasy.

Przejdź do następnego samouczka, aby dowiedzieć się więcej o monitorowaniu maszyny wirtualnej.

> [!div class="nextstepaction"]
> [Monitorowanie maszyn wirtualnych](tutorial-monitor.md)
