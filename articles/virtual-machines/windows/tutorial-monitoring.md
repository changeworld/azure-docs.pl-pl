---
title: Azure monitorowanie i aktualizowanie maszyn wirtualnych systemu Windows i | Dokumentacja firmy Microsoft
description: Samouczek — monitorowania i aktualizowania maszyny wirtualnej systemu Windows przy użyciu programu Azure PowerShell
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: b908e8877162a6a1d9292616a1704c1c528e1725
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="monitor-and-update-a-windows-virtual-machine-with-azure-powershell"></a>Monitorowanie i aktualizowanie maszyny wirtualnej systemu Windows przy użyciu programu Azure PowerShell

Azure monitorowanie używa agentów do zbierania danych rozruchu i wydajności z maszyn wirtualnych platformy Azure, przechowywania tych danych w magazynie Azure i stał się dostępny za pośrednictwem portalu, moduł Azure PowerShell i interfejsu wiersza polecenia Azure. Zarządzanie aktualizacjami umożliwia zarządzanie aktualizacje i poprawki dla maszyn wirtualnych systemu Windows Azure.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Włącz diagnostykę rozruchu na maszynie Wirtualnej
> * Wyświetlanie diagnostyki rozruchu
> * Wyświetlaj metryki hosta maszyny Wirtualnej
> * Zainstaluj rozszerzenie diagnostyki
> * Wyświetlanie metryki maszyny wirtualnej
> * Utwórz alert
> * Zarządzanie aktualizacjami systemu Windows
> * Monitor zmiany i magazynu
> * Konfigurowanie zaawansowanego monitorowania

Dla tego samouczka jest wymagany moduł Azure PowerShell w wersji 3.6 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps).

Do utworzenia przykładu przedstawionego w tym samouczku potrzebna jest istniejąca maszyna wirtualna. W razie potrzeby [ten przykładowy skrypt](../scripts/virtual-machines-windows-powershell-sample-create-vm.md) pomoże Ci go utworzyć. Podczas pracy z samouczkiem, Zastąp grupy zasobów, nazwę maszyny Wirtualnej i lokalizację w razie potrzeby.

## <a name="view-boot-diagnostics"></a>Wyświetlanie diagnostyki rozruchu

Jak rozruchu maszyn wirtualnych systemu Windows, agenta diagnostyki rozruchu przechwytuje danych wyjściowych ekranu, który może służyć do rozwiązywania problemów z celem. Ta funkcja jest domyślnie włączona. Zrzuty ekranu przechwyconych są przechowywane w koncie magazynu platformy Azure, której tworzona jest również domyślnie.

Można uzyskać danych diagnostycznych rozruchu z [Get-AzureRmVMBootDiagnosticsData](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmbootdiagnosticsdata) polecenia. W poniższym przykładzie diagnostyki rozruchu są pobierane z katalogiem głównym * c:\* dysku.

```powershell
Get-AzureRmVMBootDiagnosticsData -ResourceGroupName myResourceGroup -Name myVM -Windows -LocalPath "c:\"
```

## <a name="view-host-metrics"></a>Wyświetlanie metryki hosta

Maszyny Wirtualnej systemu Windows ma dedykowanego hosta maszyny Wirtualnej na platformie Azure, która współdziała ona z. Metryki są automatycznie pobierane dla hosta i mogą być wyświetlane w portalu Azure.

1. W witrynie Azure Portal kliknij pozycję **Grupy zasobów**, wybierz grupę **myResourceGroup**, a następnie wybierz maszynę wirtualną **myVM** na liście zasobów.
2. Kliknij przycisk **metryki** w bloku maszyny Wirtualnej, a następnie wybierz jedno z hosta metryki w obszarze **dostępne metryki** aby zobaczyć, jak działa hosta maszyny Wirtualnej.

    ![Wyświetlanie metryki hosta](./media/tutorial-monitoring/tutorial-monitor-host-metrics.png)

## <a name="install-diagnostics-extension"></a>Instalacja rozszerzenia diagnostyki

Metryki podstawowych hostów są dostępne, ale bardziej szczegółowe i metryki specyficzne dla maszyny Wirtualnej, można, należy zainstalować rozszerzenie diagnostycznych platformy Azure na maszynie Wirtualnej. Rozszerzenie diagnostyki platformy Azure umożliwia pobieranie dodatkowych danych dotyczących monitorowania i diagnostyki z maszyny wirtualnej. Możesz wyświetlić te metryki wydajności i utworzyć alerty w oparciu o wydajność maszyny wirtualnej. Rozszerzenie diagnostyki jest instalowane za pośrednictwem witryny Azure Portal w następujący sposób:

1. W witrynie Azure Portal kliknij pozycję **Grupy zasobów**, wybierz grupę **myResourceGroup**, a następnie wybierz maszynę wirtualną **myVM** na liście zasobów.
2. Kliknij pozycję **Ustawienia diagnozy**. Lista pokazuje, że *Diagnostyka rozruchu* została już włączona w poprzedniej sekcji. Kliknij pole wyboru pozycji *Metryki podstawowe*.
3. Kliknij przycisk **Włącz monitorowanie na poziomie gościa** przycisku.

    ![Wyświetlanie metryki diagnostyki](./media/tutorial-monitoring/enable-diagnostics-extension.png)

## <a name="view-vm-metrics"></a>Wyświetlanie metryki maszyny wirtualnej

Możesz wyświetlić metryki maszyny wirtualnej w ten sam sposób co metryki maszyny wirtualnej hosta:

1. W witrynie Azure Portal kliknij pozycję **Grupy zasobów**, wybierz grupę **myResourceGroup**, a następnie wybierz maszynę wirtualną **myVM** na liście zasobów.
2. Aby zobaczyć, jak działa maszyna wirtualna, kliknij pozycję **Metryki** w bloku maszyny wirtualnej, a następnie wybierz dowolną metrykę diagnostyki w pozycji **Dostępne metryki**.

    ![Wyświetlanie metryki maszyny wirtualnej](./media/tutorial-monitoring/monitor-vm-metrics.png)

## <a name="create-alerts"></a>Tworzenie alertów

Możesz utworzyć alerty w oparciu o konkretne metryki wydajności. Przykładowo alertów można używać do wysyłania powiadomień w przypadku przekroczenia wybranego progu użycia procesora lub spadku dostępnego miejsca na dysku poniżej wybranej wartości. Alerty mogą być wyświetlane w witrynie Azure Portal lub wysyłane za pośrednictwem poczty e-mail. Możesz też wyzwolić elementy runbook usługi Azure Automation lub aplikacje usługi Azure Logic Apps w ramach reakcji na wygenerowane alerty.

Poniższy przykład tworzy alert dotyczący średniego użycia procesora.

1. W witrynie Azure Portal kliknij pozycję **Grupy zasobów**, wybierz grupę **myResourceGroup**, a następnie wybierz maszynę wirtualną **myVM** na liście zasobów.
2. Kliknij pozycję **Reguły alertów** w bloku maszyny wirtualnej, a następnie kliknij pozycję **Dodaj alert metryki** w górnej części bloku alertów.
3. Podaj **nazwę** alertu, np. *myAlertRule*
4. Aby wyzwolić alert, gdy procent użycia procesora przekracza 1,0 przez pięć minut, pozostaw wybrane wszystkie inne wartości domyślne.
5. Opcjonalnie zaznacz pole pozycji *Wyślij wiadomość e-mail do właścicieli, współautorów i czytelników*, aby wysłać powiadomienie w wiadomości e-mail. Domyślne działanie to prezentowanie powiadomienia w portalu.
6. Kliknij przycisk **OK**.

## <a name="manage-windows-updates"></a>Zarządzanie aktualizacjami systemu Windows

Zarządzanie aktualizacjami umożliwia zarządzanie aktualizacje i poprawki dla maszyn wirtualnych systemu Windows Azure.
Bezpośrednio z poziomu maszyny wirtualnej możesz szybko ocenić stan dostępnych aktualizacji, zaplanować instalację wymaganych aktualizacji i przejrzeć wyniki wdrażania, aby sprawdzić, czy aktualizacje zostały zastosowane pomyślnie do maszyny wirtualnej.

Aby uzyskać informacje o cenach, zobacz [cennik usługi Automation dla rozwiązania Update Management](https://azure.microsoft.com/pricing/details/automation/)

### <a name="enable-update-management"></a>Włączanie rozwiązania Update Management

Włącz zarządzanie aktualizacji dla maszyny Wirtualnej:

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

Wybierz obszar roboczy usługi Log Analytics i konto usługi Automation, a następnie kliknij przycisk **Włącz**, aby włączyć to rozwiązanie. Włączanie rozwiązania może trwać do 15 minut.

Jeśli którekolwiek z następujących wymagań wstępnych nie będzie występować podczas dołączania, zostanie ono automatycznie dołączone:

* Obszar roboczy usługi [Log Analytics](../../log-analytics/log-analytics-overview.md)
* [Automatyzacja](../../automation/automation-offering-get-started.md)
* [Hybrydowy proces roboczy elementu Runbook](../../automation/automation-hybrid-runbook-worker.md) jest włączony na maszynie wirtualnej

Zostanie otwarty ekran **Rozwiązanie Update Management**. Skonfiguruj lokalizację, obszar roboczy usługi Log Analytics i konto usługi Automation, a następnie kliknij pozycję **Włącz**. Jeśli pola są wygaszone, oznacza to, że inne rozwiązanie automatyzacji jest włączone dla maszyny wirtualnej, a tym samym należy użyć tego samego obszaru roboczego i konta automatyzacji.

![Włączanie rozwiązania Update Management](./media/tutorial-monitoring/manageupdates-update-enable.png)

Włączanie rozwiązania może potrwać do 15 minut. W tym czasie nie należy zamykać okna przeglądarki. Po włączeniu rozwiązania informacje dotyczące brakujących aktualizacji maszyn wirtualnych są przekazywane do usługi Log Analytics. Udostępnienie danych do analizy może potrwać od 30 minut do 6 godzin.

### <a name="view-update-assessment"></a>Wyświetlanie oceny aktualizacji

Po włączeniu rozwiązania **Update Management** zostanie wyświetlony ekran **Update Management**. Po zakończeniu oceny aktualizacji, wyświetlić listę brakujących aktualizacji na **brakujących aktualizacji** kartę.

 ![Wyświetlanie stanu aktualizacji](./media/tutorial-monitoring/manageupdates-view-status-win.png)

### <a name="schedule-an-update-deployment"></a>Planowanie wdrożenia aktualizacji

Aby zainstalować aktualizacje, zaplanuj wdrożenie zgodnie z harmonogramem wydawania i oknem obsługi. Możesz wybrać typy aktualizacji, które mają zostać uwzględnione we wdrożeniu. Możesz na przykład uwzględnić aktualizacje krytyczne lub aktualizacje zabezpieczeń i wykluczyć pakiety zbiorcze aktualizacji.

Zaplanuj nowe wdrożenie aktualizacji dla maszyny wirtualnej, klikając pozycję **Zaplanuj wdrażanie aktualizacji** w górnej części ekranu **Update Management**. Na ekranie **Nowe wdrożenie aktualizacji** podaj następujące informacje:

* **Nazwa** — wprowadź unikatową nazwę identyfikującą wdrożenie aktualizacji.
* **Klasyfikacja aktualizacji** — wybierz typy oprogramowania, które zostaną uwzględnione we wdrożeniu aktualizacji. Dostępne są następujące typy klasyfikacji:
  * Aktualizacje krytyczne
  * Aktualizacje zabezpieczeń
  * Pakiety zbiorcze aktualizacji
  * Pakiety funkcji
  * Dodatki Service Pack
  * Aktualizacje definicji
  * Narzędzia
  * Aktualizacje

* **Ustawienia harmonogramu** — możesz zaakceptować domyślną datę i godzinę (30 minut po bieżącej godzinie) lub określić inny czas.
  Możesz też określić, czy wdrożenie ma występować raz, czy zgodnie z ustawionym harmonogramem cyklicznym. Kliknij opcję Cyklicznie w obszarze Cykl, aby skonfigurować harmonogram cykliczny.

  ![Ekran ustawień harmonogramu aktualizacji](./media/tutorial-monitoring/manageupdates-schedule-win.png)

* **Okno konserwacji (w minutach)** — podaj okres, w którym ma zostać przeprowadzone wdrażanie aktualizacji.  Pozwala to zagwarantować, że zmiany będą wprowadzane w ramach zdefiniowanych okien obsługi.

Po ukończeniu konfigurowania harmonogramu kliknij przycisk **Utwórz**, aby wrócić do pulpitu nawigacyjnego stanu.
Tabela **Zaplanowane** zawiera utworzony harmonogram wdrożenia.

> [!WARNING]
> Aktualizacje, które wymagają ponownego uruchomienia systemu maszyna wirtualna zostanie ponownie uruchomiony.

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

## <a name="monitor-changes-and-inventory"></a>Monitor zmiany i magazynu

Możesz zbierać i wyświetlać spis oprogramowania, plików, demonów systemu Linux, usług systemu Windows i kluczy rejestru systemu Windows znajdujących się na Twoich komputerach. Śledzenie konfiguracji maszyn ułatwia identyfikowanie problemów operacyjnych w środowisku oraz lepsze rozumienie stanu maszyn.

### <a name="enable-change-and-inventory-management"></a>Zarządzanie zmianami enable i magazynu

Zarządzanie zmianami enable i magazynu dla maszyny Wirtualnej:

1. Po lewej stronie ekranu wybierz pozycję **Maszyny wirtualne**.
2. Z listy wybierz maszynę wirtualną.
3. Na ekranie maszyny Wirtualnej w **operacji** kliknij **spisu** lub **śledzenia zmian**. **Włączyć śledzenie zmian i spisu** ekranu zostanie otwarta.

Skonfiguruj lokalizację, obszar roboczy usługi Log Analytics i konto usługi Automation, a następnie kliknij pozycję **Włącz**. Jeśli pola są wygaszone, oznacza to, że inne rozwiązanie automatyzacji jest włączone dla maszyny wirtualnej, a tym samym należy użyć tego samego obszaru roboczego i konta automatyzacji. Eventhough rozwiązania są oddzielone w menu, są tego samego rozwiązania. Włączenie co umożliwia zarówno dla maszyny Wirtualnej.

![Włącz zmiany i monitorowania magazynu](./media/tutorial-monitoring/manage-inventory-enable.png)

Po włączeniu rozwiązania może zająć pewien czas spisu zbierany jest na maszynie Wirtualnej przed wyświetleniem danych.

### <a name="track-changes"></a>Śledź zmiany

Na Twojej maszyny Wirtualnej wybierz **śledzenia zmian** w obszarze **operacji**. Kliknij przycisk **edytowanie ustawień**, **śledzenia zmian** zostanie wyświetlona strona. Wybierz typ ustawienie chcesz śledzić, a następnie kliknij przycisk **+ Dodaj** do konfigurowania ustawień. Są dostępne opcje dla systemu Windows:

* Rejestr systemu Windows
* Pliki systemu Windows

Aby uzyskać szczegółowe informacje na temat śledzenia zmian, zobacz [Rozwiązywanie problemów z zmiany na maszynie Wirtualnej](../../automation/automation-tutorial-troubleshoot-changes.md)

### <a name="view-inventory"></a>Widok spisu

Na Twojej maszyny Wirtualnej wybierz **spisu** w obszarze **operacji**. Na karcie **Oprogramowanie** znajduje się lista tabelowa oprogramowania, które zostało odnalezione. Szczegółowe informacje wysokiego poziomu dotyczące każdego rekordu oprogramowania są wyświetlane w tabeli. Te informacje obejmują nazwę oprogramowania, wersja, wydawca, czas ostatniego odświeżenia.

![Widok spisu](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>Monitoruj Dzienniki aktywności i zmiany

Ze strony **Śledzenie zmian** na swojej maszynie wirtualnej wybierz pozycję **Zarządzanie połączeniem dziennika aktywności**. To zadanie powoduje otwarcie strony **Dziennik aktywności platformy Azure**. Wybierz pozycję **Połącz**, aby połączyć śledzenie zmian z dziennikiem aktywności platformy Azure dla Twojej maszyny wirtualnej.

Po włączeniu tego ustawienia przejdź do strony **Omówienie** dla maszyny wirtualnej i wybierz pozycję **Zatrzymaj**, aby zatrzymać swoją maszynę wirtualną. Po wyświetleniu monitu wybierz pozycję **Tak**, aby zatrzymać maszynę wirtualną. Po cofnięciu jej przydziału wybierz pozycję **Start**, aby ponownie uruchomić maszynę wirtualną.

Zatrzymanie i uruchomienie maszyny wirtualnej rejestruje zdarzenie w jego dzienniku aktywności. Przejdź z powrotem do strony **Śledzenie zmian**. Wybierz kartę **Zdarzenia** u dołu strony. Po chwili zdarzenia są wyświetlane na wykresie i w tabeli. Aby wyświetlić szczegółowe informacje o zdarzeniu można wybrać każdego zdarzenia.

![Przeglądanie zmian w dzienniku aktywności](./media/tutorial-monitoring/manage-activitylog-view-results.png)

Wykres pokazuje zmiany, które wystąpiły w czasie. Po dodaniu połączenia dziennika aktywności wykres liniowy u góry wyświetla zdarzenia dziennika aktywności platformy Azure. Każdy wiersz wykresów słupkowych reprezentuje innego typu zmiany umożliwiające śledzenie. Do tych typów należą demony systemu Linux, pliki, klucze rejestru systemu Windows, oprogramowanie i usługi systemu Windows. Karta zmiany przedstawia szczegółowe informacje dla zmian pokazanych w wizualizacji w kolejności malejącej według czasu, kiedy wystąpiła zmiana (najnowsze na początku).

## <a name="advanced-monitoring"></a>Zaawansowane monitorowanie

Możesz to zrobić bardziej zaawansowane monitorowanie maszyny wirtualnej przy użyciu rozwiązań, takich jak zarządzanie aktualizacjami i zmianami i spisu udostępniane przez usługi Automatyzacja Azure. [Operations Management Suite](../../automation/automation-intro.md).

Jeśli masz dostęp do obszaru roboczego analizy dzienników klucz obszaru roboczego i identyfikator obszaru roboczego można znaleźć na, wybierając **Zaawansowane ustawienia** w obszarze **ustawienia**. Użyj [AzureRmVMExtension zestaw](/powershell/module/azurerm.compute/set-azurermvmextension) polecenie, aby dodać rozszerzenie jako Microsoft Monitoring agent maszyny Wirtualnej. Zaktualizuj wartości zmiennej w poniżej przykładowy w celu uwzględnienia możesz klucz obszaru roboczego analizy dzienników i identyfikator obszaru roboczego

```powershell
$workspaceId = "<Replace with your workspace Id>"
$key = "<Replace with your primary key>"

Set-AzureRmVMExtension -ResourceGroupName myResourceGroup `
  -ExtensionName "Microsoft.EnterpriseCloud.Monitoring" `
  -VMName myVM `
  -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
  -ExtensionType "MicrosoftMonitoringAgent" `
  -TypeHandlerVersion 1.0 `
  -Settings @{"workspaceId" = $workspaceId} `
  -ProtectedSettings @{"workspaceKey" = $key} `
  -Location eastus
```

Po upływie kilku minut powinien zostać wyświetlony nowej maszyny Wirtualnej w obszarze roboczym Anaytics dziennika.

![Blok OMS](./media/tutorial-monitoring/tutorial-monitor-oms.png)

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku został skonfigurowany i przejrzeć maszyn wirtualnych w Centrum zabezpieczeń Azure. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie sieci wirtualnej
> * Tworzenie grupy zasobów i maszyny Wirtualnej
> * Włączanie diagnostyki rozruchu na maszynie wirtualnej
> * Wyświetlanie diagnostyki rozruchu
> * Wyświetlanie metryki hosta
> * Zainstaluj rozszerzenie diagnostyki
> * Wyświetlanie metryki maszyny wirtualnej
> * Utwórz alert
> * Zarządzanie aktualizacjami systemu Windows
> * Monitor zmiany i magazynu
> * Konfigurowanie zaawansowanego monitorowania

Przejdź do następnego samouczka, aby dowiedzieć się więcej na temat Centrum zabezpieczeń Azure.

> [!div class="nextstepaction"]
> [Zarządzanie zabezpieczeniami maszyn wirtualnych](./tutorial-azure-security.md)