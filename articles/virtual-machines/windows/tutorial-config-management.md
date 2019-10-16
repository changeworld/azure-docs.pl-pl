---
title: Samouczek — zarządzanie konfiguracją maszyny wirtualnej z systemem Windows na platformie Azure | Microsoft Docs
description: W tym samouczku dowiesz się, jak identyfikować zmiany i zarządzać aktualizacjami pakietów na maszynie wirtualnej z systemem Windows
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
ms.openlocfilehash: 96e73b228604db519beb5284ee5a8fb8dc4c4f66
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376121"
---
# <a name="tutorial-monitor-changes-and-update-a-windows-virtual-machine-in-azure"></a>Samouczek: monitorowanie zmian i aktualizowanie maszyny wirtualnej z systemem Windows na platformie Azure

Usługa Azure [Change Tracking](../../automation/change-tracking.md) umożliwia łatwe identyfikowanie zmian i [Update Management](../../automation/automation-update-management.md) umożliwia zarządzanie aktualizacjami systemu operacyjnego dla maszyn wirtualnych z systemem Windows Azure.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Zarządzanie aktualizacjami systemu Windows
> * Monitorowanie zmian i spisu

## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie.

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Możesz również uruchomić usługę Cloud Shell w oddzielnej karcie przeglądarki, przechodząc do strony [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Wybierz przycisk **Kopiuj**, aby skopiować bloki kodu, wklej je do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby je uruchomić.

## <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej

Aby w tym samouczku móc skonfigurować monitorowanie i zarządzanie aktualizacjami na platformie Azure, konieczne jest posiadanie maszyny wirtualnej z systemem Windows na platformie Azure. Najpierw ustaw nazwę użytkownika i hasło administratora maszyny wirtualnej przy użyciu polecenia [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Następnie utwórz maszynę wirtualną za pomocą polecenia [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). W poniższym przykładzie zostanie utworzona maszyna wirtualna o nazwie *myVM* w lokalizacji *EastUS*. Grupa zasobów *myResourceGroupMonitorMonitor* i pomocnicze zasoby sieciowe zostaną utworzone, jeśli jeszcze nie istnieją:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupMonitor" `
    -Name "myVM" `
    -Location "East US" `
    -Credential $cred
```

Utworzenie maszyny wirtualnej i zasobów może potrwać kilka minut.

## <a name="manage-windows-updates"></a>Zarządzanie aktualizacjami systemu Windows

Update Management umożliwia zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych z systemem Windows Azure.
Bezpośrednio z poziomu maszyny wirtualnej możesz szybko ocenić stan dostępnych aktualizacji, zaplanować instalację wymaganych aktualizacji i przejrzeć wyniki wdrażania, aby sprawdzić, czy aktualizacje zostały zastosowane pomyślnie do maszyny wirtualnej.

Aby uzyskać informacje o cenach, zobacz [Cennik usługi Automation dla zarządzania aktualizacjami](https://azure.microsoft.com/pricing/details/automation/).

### <a name="enable-update-management"></a>Włączanie rozwiązania Update Management

Włącz rozwiązanie Update Management dla maszyny wirtualnej:

1. Po lewej stronie ekranu wybierz pozycję **Maszyny wirtualne**.
2. Z listy wybierz maszynę wirtualną.
3. Na ekranie maszyny wirtualnej w sekcji **Operacje** kliknij pozycję **Update Management**. Zostanie wyświetlony ekran **Włączanie rozwiązania Update Management**.

Jest przeprowadzana walidacja w celu ustalenia, czy rozwiązanie Update Management jest włączone dla tej maszyny wirtualnej.
Walidacja obejmuje kontrole obszaru roboczego usługi Log Analytics i powiązanego konta usługi Automation i tego, czy rozwiązanie znajduje się w obszarze roboczym.

Obszar roboczy usługi [Log Analytics](../../log-analytics/log-analytics-overview.md) służy do zbierania danych generowanych przez funkcje i usługi, takie jak rozwiązanie Update Management.
Obszar roboczy zawiera pojedynczą lokalizację do przeglądania i analizowania danych z wielu źródeł.
Aby wykonać dodatkowe akcje na maszynach wirtualnych, które wymagają aktualizacji, usługa Azure Automation pozwala na uruchamianie elementów Runbook dla maszyn wirtualnych, takich jak pobieranie i stosowanie aktualizacji.

Proces walidacji sprawdza również, czy maszyna wirtualna jest aprowizowana za pomocą programu Microsoft Monitoring Agent (MMA) i hybrydowego procesu roboczego elementu Runbook usługi Automation.
Ten agent jest używany do komunikacji z maszyną wirtualną i uzyskiwania informacji dotyczących stanu aktualizacji.

Wybierz obszar roboczy Log Analytics i konto usługi Automation, a następnie kliknij pozycję **Włącz** , aby włączyć rozwiązanie. Włączanie rozwiązania może trwać do 15 minut.

Jeśli którekolwiek z następujących wymagań wstępnych nie będzie występować podczas dołączania, zostanie ono automatycznie dołączone:

* Obszar roboczy usługi [Log Analytics](../../log-analytics/log-analytics-overview.md)
* [Automatyzacja](../../automation/automation-offering-get-started.md)
* [Hybrydowy proces roboczy elementu Runbook](../../automation/automation-hybrid-runbook-worker.md) jest włączony na maszynie wirtualnej

Zostanie otwarty ekran **Rozwiązanie Update Management**. Skonfiguruj lokalizację, Log Analytics obszar roboczy i konto usługi Automation, a następnie kliknij pozycję **Włącz**. Jeśli pola są wygaszone, oznacza to, że inne rozwiązanie automatyzacji jest włączone dla maszyny wirtualnej, a tym samym należy użyć tego samego obszaru roboczego i konta automatyzacji.

![Włączanie rozwiązania Update Management](./media/tutorial-monitoring/manageupdates-update-enable.png)

Włączanie rozwiązania może potrwać do 15 minut. W tym czasie nie należy zamykać okna przeglądarki. Po włączeniu rozwiązania informacje dotyczące brakujących aktualizacji maszyny wirtualnej są przekazywane do dzienników usługi Azure Monitor. Udostępnienie danych do analizy może potrwać od 30 minut do 6 godzin.

### <a name="view-update-assessment"></a>Wyświetlanie oceny aktualizacji

Po włączeniu rozwiązania **Update Management** zostanie wyświetlony ekran **Update Management**. Po zakończeniu oceny aktualizacji możesz zobaczyć listę brakujących aktualizacji na karcie **Brakujące aktualizacje**.

 ![Wyświetlanie stanu aktualizacji](./media/tutorial-monitoring/manageupdates-view-status-win.png)

### <a name="schedule-an-update-deployment"></a>Planowanie wdrożenia aktualizacji

Aby zainstalować aktualizacje, zaplanuj wdrożenie zgodnie z harmonogramem wydawania i oknem obsługi. Możesz wybrać typy aktualizacji, które mają zostać uwzględnione we wdrożeniu. Możesz na przykład uwzględnić aktualizacje krytyczne lub aktualizacje zabezpieczeń i wykluczyć pakiety zbiorcze aktualizacji.

Zaplanuj nowe wdrożenie aktualizacji dla maszyny wirtualnej, klikając pozycję **Zaplanuj wdrażanie aktualizacji** w górnej części ekranu **Update Management**. Na ekranie **Nowe wdrożenie aktualizacji** podaj następujące informacje:

Aby utworzyć nowe wdrożenie aktualizacji, wybierz pozycję **Zaplanuj wdrożenie aktualizacji**. Zostanie otwarta strona **nowe wdrożenie aktualizacji** . Wprowadź wartości dla właściwości opisanych w poniższej tabeli, a następnie kliknij pozycję **Utwórz**:

| Właściwość | Opis |
| --- | --- |
| Nazwa |Unikatowa nazwa identyfikującą wdrożenie aktualizacji. |
|System operacyjny| System Linux lub Windows|
| Grupy do zaktualizowania |W przypadku maszyn platformy Azure Zdefiniuj zapytanie w oparciu o kombinację subskrypcji, grup zasobów, lokalizacji i tagów, aby utworzyć dynamiczną grupę maszyn wirtualnych platformy Azure, które mają zostać uwzględnione we wdrożeniu. </br></br>W przypadku maszyn spoza platformy Azure Wybierz istniejące zapisane wyszukiwanie, aby wybrać grupę maszyn nienależących do platformy Azure, które mają zostać uwzględnione we wdrożeniu. </br></br>Aby dowiedzieć się więcej, zobacz [Grupy dynamiczne](../../automation/automation-update-management-groups.md)|
| Maszyny do zaktualizowania |Wybierz zapisane wyszukiwanie bądź zaimportowaną grupę lub wybierz maszynę z listy rozwijanej, a następnie wybierz poszczególne maszyny. Jeśli wybierzesz pozycję **Maszyny**, gotowość maszyny będzie wyświetlana w kolumnie **AKTUALIZUJ GOTOWOŚĆ AGENTA**.</br> Aby dowiedzieć się więcej na temat różnych metod tworzenia grup komputerów w dziennikach usługi Azure Monitor, zobacz [Computer groups in Azure Monitor logs (Grupy komputerów w dziennikach usługi Azure Monitor)](../../azure-monitor/platform/computer-groups.md) |
|Klasyfikacje aktualizacji|Wybierz wszystkie wymagane klasyfikacje aktualizacji|
|Uwzględnij/Wyklucz aktualizacje|Spowoduje to otwarcie strony **dołączania/wykluczania** . Aktualizacje, które mają zostać uwzględnione lub wykluczone, znajdują się na osobnych kartach. Aby uzyskać więcej informacji na temat obsługi dołączania, zobacz [Planowanie wdrożenia aktualizacji](../../automation/automation-tutorial-update-management.md#schedule-an-update-deployment) . |
|Ustawienia harmonogramu|Wybierz godzinę do uruchomienia, a następnie wybierz jedno lub cykliczne dla cyklu|
| Skrypty przed skryptami + po skrypcie|Wybierz skrypty do uruchomienia przed i po wdrożeniu|
| Okno obsługi |Liczba minut ustawiona dla aktualizacji. Wartość nie może być mniejsza niż 30 minut i nie więcej niż 6 godzin |
| Kontrola ponownego uruchamiania| Określa, jak należy obsługiwać ponowny rozruch. Dostępne opcje:</br>Ponowne uruchomienie, jeśli jest to wymagane (ustawienie domyślne)</br>Zawsze uruchamiaj ponownie</br>Nigdy nie uruchamiaj ponownie</br>Tylko ponowne uruchomienie — aktualizacje nie zostaną zainstalowane|

Wdrożenia aktualizacji można także tworzyć programowo. Aby dowiedzieć się, jak utworzyć wdrożenie aktualizacji za pomocą interfejsu API REST, zobacz [konfiguracje aktualizacji oprogramowania — tworzenie](/rest/api/automation/softwareupdateconfigurations/create). Istnieje również przykładowy element Runbook, który może służyć do tworzenia tygodniowego wdrożenia aktualizacji. Aby dowiedzieć się więcej na temat tego elementu Runbook, zobacz [Tworzenie tygodniowego wdrożenia aktualizacji dla co najmniej jednej maszyny wirtualnej w grupie zasobów](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

Po ukończeniu konfigurowania harmonogramu kliknij przycisk **Utwórz**, aby wrócić do pulpitu nawigacyjnego stanu.
Tabela **Zaplanowane** zawiera utworzony harmonogram wdrożenia.

### <a name="view-results-of-an-update-deployment"></a>Wyświetlanie wyników wdrażania aktualizacji

Po rozpoczęciu zaplanowanego wdrażania stan tego wdrożenia można sprawdzić na karcie **Wdrożenia aktualizacji** na ekranie rozwiązania **Update Management**.
Jeśli wdrażanie trwa, wdrożenie ma stan **W toku**. Po pomyślnym ukończeniu wdrażania stan zmienia się na **Powodzenie**.
W przypadku błędu co najmniej jednej aktualizacji w ramach wdrożenia jest wyświetlany stan **Częściowe niepowodzenie**.
Kliknij ukończone wdrożenie aktualizacji, aby wyświetlić pulpit nawigacyjny tego wdrożenia.

![Pulpit nawigacyjny stanu wdrożenia aktualizacji dla określonego wdrożenia](./media/tutorial-monitoring/manageupdates-view-results.png)

Kafelek **Wyniki aktualizacji** zawiera podsumowanie z łączną liczbą aktualizacji i wynikami wdrożenia na maszynie wirtualnej.
Tabela po prawej stronie zawiera szczegółowy podział każdej aktualizacji i wyniki instalacji, które mogą mieć jedną z następujących wartości:

* **Nie podjęto próby** — nie zainstalowano aktualizacji z powodu niewystarczającego czasu w zdefiniowanym oknie konserwacji.
* **Powodzenie** — aktualizacja powiodła się.
* **Niepowodzenie** — aktualizacja nie powiodła się.

Kliknij pozycję **Wszystkie dzienniki**, aby wyświetlić wszystkie wpisy dziennika utworzone przez wdrożenie.

Kliknij kafelek **Dane wyjściowe**, aby wyświetlić strumień zadań elementu runbook odpowiedzialnego za zarządzanie wdrożeniem aktualizacji na docelowej maszynie wirtualnej.

Kliknij pozycję **Błędy**, aby wyświetlić szczegółowe informacje o błędach związanych z wdrożeniem.

## <a name="monitor-changes-and-inventory"></a>Monitorowanie zmian i spisu

Możesz zbierać i wyświetlać spis oprogramowania, plików, demonów systemu Linux, usług systemu Windows i kluczy rejestru systemu Windows znajdujących się na Twoich komputerach. Śledzenie konfiguracji maszyn ułatwia identyfikowanie problemów operacyjnych w środowisku oraz lepsze rozumienie stanu maszyn.

### <a name="enable-change-and-inventory-management"></a>Włączanie zarządzania zmianami i spisem

Aby włączyć zarządzanie zmianami i spisem na maszynie wirtualnej:

1. Po lewej stronie ekranu wybierz pozycję **Maszyny wirtualne**.
2. Z listy wybierz maszynę wirtualną.
3. Na ekranie maszyny wirtualnej w sekcji **Operacje** kliknij pozycję **Spis** lub **Śledzenie zmian**. Zostanie otwarty ekran **Włączanie śledzenia zmian i spisu**.

Skonfiguruj lokalizację, Log Analytics obszar roboczy i konto usługi Automation, a następnie kliknij pozycję **Włącz**. Jeśli pola są wygaszone, oznacza to, że inne rozwiązanie automatyzacji jest włączone dla maszyny wirtualnej, a tym samym należy użyć tego samego obszaru roboczego i konta automatyzacji. Nawet jeśli te rozwiązania są oddzielone w menu, jest to jedno rozwiązanie. Włączenie jednego z nich oznacza włączenie obydwu na maszynie wirtualnej.

![Włączanie śledzenia zmian i spisu](./media/tutorial-monitoring/manage-inventory-enable.png)

Po włączeniu rozwiązania zebranie danych spisu na maszynie wirtualnej przed ich wyświetleniem może potrwać pewien czas.

### <a name="track-changes"></a>Śledzenie zmian

Na maszynie wirtualnej wybierz pozycję **Śledzenie zmian** w obszarze **OPERACJE**. Kliknij pozycję **Edytuj ustawienia**. Zostanie wyświetlona strona **Śledzenie zmian**. Wybierz typ ustawienia do śledzenia, a następnie kliknij pozycję **+ Dodaj** w celu skonfigurowania ustawień. W przypadku systemu Windows dostępne są następujące opcje:

* Rejestr systemu Windows
* Pliki systemu Windows

Aby uzyskać szczegółowe informacje na temat rozwiązania Change Tracking, zobacz [Rozwiązywanie problemów dotyczących zmian na maszynie wirtualnej](../../automation/automation-tutorial-troubleshoot-changes.md)

### <a name="view-inventory"></a>Wyświetlanie spisu

Na maszynie wirtualnej wybierz pozycję **Spis** w obszarze **OPERACJE**. Na karcie **Oprogramowanie** znajduje się lista tabelowa oprogramowania, które zostało odnalezione. Szczegółowe informacje wysokiego poziomu dotyczące każdego rekordu oprogramowania są wyświetlane w tabeli. Obejmują one nazwę, wersję, wydawcę i czas ostatniego odświeżenia oprogramowania.

![Wyświetlanie spisu](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>Monitorowanie dzienników aktywności i zmian

Ze strony **Śledzenie zmian** na swojej maszynie wirtualnej wybierz pozycję **Zarządzanie połączeniem dziennika aktywności**. To zadanie powoduje otwarcie strony **Dziennik aktywności platformy Azure**. Wybierz pozycję **Połącz**, aby połączyć śledzenie zmian z dziennikiem aktywności platformy Azure dla Twojej maszyny wirtualnej.

Po włączeniu tego ustawienia przejdź do strony **Omówienie** dla maszyny wirtualnej i wybierz pozycję **Zatrzymaj**, aby zatrzymać swoją maszynę wirtualną. Po wyświetleniu monitu wybierz pozycję **Tak**, aby zatrzymać maszynę wirtualną. Po cofnięciu jej przydziału wybierz pozycję **Start**, aby ponownie uruchomić maszynę wirtualną.

Zatrzymanie i uruchomienie maszyny wirtualnej rejestruje zdarzenie w jego dzienniku aktywności. Przejdź z powrotem do strony **Śledzenie zmian**. Wybierz kartę **Zdarzenia** u dołu strony. Po chwili zdarzenia są wyświetlane na wykresie i w tabeli. Każde zdarzenie można wybrać, aby wyświetlić szczegółowe informacje o zdarzeniu.

![Wyświetlanie zmian w dzienniku aktywności](./media/tutorial-monitoring/manage-activitylog-view-results.png)

Wykres pokazuje zmiany, które wystąpiły w czasie. Po dodaniu połączenia dziennika aktywności wykres liniowy u góry wyświetla zdarzenia dziennika aktywności platformy Azure. Każdy wiersz wykresów słupkowych reprezentuje innego typu zmiany umożliwiające śledzenie. Do tych typów należą demony systemu Linux, pliki, klucze rejestru systemu Windows, oprogramowanie i usługi systemu Windows. Karta zmiany przedstawia szczegółowe informacje dla zmian pokazanych w wizualizacji w kolejności malejącej według czasu, kiedy wystąpiła zmiana (najnowsze na początku).

## <a name="next-steps"></a>Następne kroki

W tym samouczku skonfigurowano i sprawdzono Change Tracking i Update Management dla maszyny wirtualnej. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie grupy zasobów i maszyny wirtualnej
> * Zarządzanie aktualizacjami systemu Windows
> * Monitorowanie zmian i spisu

Przejdź do następnego samouczka, aby dowiedzieć się więcej o monitorowaniu maszyny wirtualnej.

> [!div class="nextstepaction"]
> [Monitorowanie maszyn wirtualnych](tutorial-monitor.md)
