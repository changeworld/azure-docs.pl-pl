---
title: Samouczek — zarządzanie konfiguracją maszyny wirtualnej z systemem Linux na platformie Azure | Microsoft Docs
description: W tym samouczku dowiesz się, jak identyfikować zmiany i zarządzać aktualizacjami pakietów na maszynie wirtualnej z systemem Linux
services: virtual-machines-linux
documentationcenter: virtual-machines
author: mgoedtel
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/27/2019
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 78c924ebe15186025f4f2a79f87be6fb4fbf5db9
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71680050"
---
# <a name="tutorial-monitor-changes-and-update-a-linux-virtual-machine-in-azure"></a>Samouczek: monitorowanie zmian i aktualizowanie maszyny wirtualnej z systemem Linux na platformie Azure

Usługa Azure [Change Tracking](../../automation/change-tracking.md) pozwala łatwo identyfikować zmiany, a [Update Management](../../automation/automation-update-management.md) umożliwia zarządzanie aktualizacjami systemu operacyjnego dla maszyn wirtualnych z systemem Linux systemu Azure.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Zarządzaj aktualizacjami systemu Windows
> * Monitorowanie zmian i spisu

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell uruchamiania

Azure Cloud Shell to bezpłatna interaktywna powłoka, za pomocą której można wykonać kroki opisane w tym artykule. Ma ona wspólne narzędzia platformy Azure preinstalowane i skonfigurowane do użycia z Twoim kontem. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.30 lub nowszej. Uruchom `az --version`, aby znaleźć wersję. Jeśli konieczne jest zainstalowanie lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="create-vm"></a>Tworzenie maszyny wirtualnej

Aby wyświetlić diagnostykę i metryki w działaniu, potrzebna jest maszyna wirtualna. Najpierw utwórz grupę zasobów za pomocą polecenie [AZ Group Create](/cli/azure/group#az-group-create). Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroupMonitor* w lokalizacji *Wschodnie* .

```azurecli-interactive
az group create --name myResourceGroupMonitor --location eastus
```

Teraz Utwórz maszynę wirtualną za pomocą [AZ VM Create](/cli/azure/vm#az-vm-create). Poniższy przykład tworzy maszynę wirtualną o nazwie *myVM* i GENERUJE klucze SSH, jeśli jeszcze nie istnieją w *~/.SSH/* :

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="manage-software-updates"></a>Zarządzanie aktualizacjami oprogramowania

Zarządzanie aktualizacjami umożliwia zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych platformy Azure z systemem Linux.
Bezpośrednio z poziomu maszyny wirtualnej można szybko ocenić stan dostępnych aktualizacji, zaplanować instalację wymaganych aktualizacji i przejrzeć wyniki wdrożenia w celu sprawdzenia, czy aktualizacje zostały pomyślnie zastosowane do maszyny wirtualnej.

Aby uzyskać informacje o cenach, zobacz [Cennik usługi Automation dla zarządzania aktualizacjami](https://azure.microsoft.com/pricing/details/automation/)

### <a name="enable-update-management"></a>Włączanie zarządzania aktualizacjami

Włącz zarządzanie aktualizacjami dla maszyny wirtualnej:

1. Po lewej stronie ekranu wybierz pozycję **maszyny wirtualne**.
2. Z listy wybierz maszynę wirtualną.
3. Na ekranie maszyny wirtualnej w sekcji **operacje** wybierz pozycję **Update Management**. Zostanie otwarty ekran **włącz Update Management** .

Sprawdzanie poprawności jest wykonywane w celu ustalenia, czy dla tej maszyny wirtualnej jest włączone zarządzanie aktualizacjami.
Sprawdzanie poprawności obejmuje sprawdzenie obszaru roboczego Log Analytics i połączonego konta usługi Automation, a jeśli rozwiązanie znajduje się w obszarze roboczym.

Obszar roboczy [log Analytics](../../log-analytics/log-analytics-overview.md) służy do zbierania danych generowanych przez funkcje i usługi, takie jak zarządzanie aktualizacjami.
Obszar roboczy zawiera jedną lokalizację do przeglądania i analizowania danych z wielu źródeł.
Aby wykonać dodatkowe akcje na maszynach wirtualnych, które wymagają aktualizacji, Azure Automation umożliwia uruchamianie elementów Runbook na maszynach wirtualnych, takich jak pobieranie i stosowanie aktualizacji.

Proces walidacji sprawdza również, czy maszyna wirtualna jest obsługiwana za pomocą agenta Log Analytics i hybrydowego procesu roboczego elementu Runbook usługi Automation. Ten agent jest używany do komunikacji z maszyną wirtualną i uzyskiwania informacji o stanie aktualizacji.

Wybierz obszar roboczy Log Analytics i konto usługi Automation, a następnie wybierz pozycję **Włącz** , aby włączyć rozwiązanie. Włączenie rozwiązania może potrwać do 15 minut.

Jeśli brakuje któregokolwiek z poniższych wymagań wstępnych podczas dołączania, zostaną one automatycznie dodane:

* [Log Analytics](../../log-analytics/log-analytics-overview.md) obszar roboczy
* [Konto usługi Automation](../../automation/automation-offering-get-started.md)
* [Hybrydowy proces roboczy elementu Runbook](../../automation/automation-hybrid-runbook-worker.md) jest włączony na maszynie wirtualnej

Zostanie otwarty ekran **Update Management** . Skonfiguruj lokalizację, Log Analytics obszar roboczy i konto usługi Automation, a następnie wybierz pozycję **Włącz**. Jeśli pola są wygaszone, oznacza to, że inne rozwiązanie automatyzacji jest włączone dla maszyny wirtualnej i należy użyć tego samego obszaru roboczego i konta usługi Automation.

![Włącz rozwiązanie do zarządzania aktualizacjami](./media/tutorial-monitoring/manage-updates-update-enable.png)

Włączenie rozwiązania może potrwać do 15 minut. W tym czasie nie należy zamykać okna przeglądarki. Po włączeniu rozwiązania informacje dotyczące brakujących aktualizacji na maszynie wirtualnej są przepływane do dzienników Azure Monitor. Udostępnienie danych do analizy może potrwać od 30 minut do 6 godzin.

### <a name="view-update-assessment"></a>Wyświetlanie oceny aktualizacji

Po włączeniu **zarządzania aktualizacjami** zostanie wyświetlony ekran **Update Management** . Po zakończeniu oceny aktualizacji zostanie wyświetlona lista brakujących aktualizacji na karcie **brakujące aktualizacje** .

 ![Wyświetl stan aktualizacji](./media/tutorial-monitoring/manage-updates-view-status-linux.png)

### <a name="schedule-an-update-deployment"></a>Planowanie wdrożenia aktualizacji

Aby zainstalować aktualizacje, Zaplanuj wdrożenie zgodnie z harmonogramem wydań i oknem usługi. Można wybrać typy aktualizacji, które mają zostać uwzględnione we wdrożeniu. Można na przykład uwzględnić aktualizacje krytyczne lub zabezpieczeń i wykluczyć pakiety zbiorcze aktualizacji.

Zaplanuj nowe wdrożenie aktualizacji dla maszyny wirtualnej, klikając pozycję **Zaplanuj wdrażanie aktualizacji** w górnej części ekranu **zarządzania aktualizacjami** . Na ekranie **nowe wdrożenie aktualizacji** podaj następujące informacje:

Aby utworzyć nowe wdrożenie aktualizacji, wybierz pozycję **Zaplanuj wdrożenie aktualizacji**. Zostanie otwarta strona **nowe wdrożenie aktualizacji** . Wprowadź wartości dla właściwości opisanych w poniższej tabeli, a następnie kliknij pozycję **Utwórz**:

| Właściwość | Opis |
| --- | --- |
| Nazwa |Unikatowa nazwa identyfikująca wdrożenie aktualizacji. |
|System operacyjny| System Linux lub Windows|
| Grupy do zaktualizowania |W przypadku maszyn platformy Azure Zdefiniuj zapytanie w oparciu o kombinację subskrypcji, grup zasobów, lokalizacji i tagów, aby utworzyć dynamiczną grupę maszyn wirtualnych platformy Azure, które mają zostać uwzględnione we wdrożeniu. </br></br>W przypadku maszyn spoza platformy Azure Wybierz istniejące zapisane wyszukiwanie, aby wybrać grupę maszyn nienależących do platformy Azure, które mają zostać uwzględnione we wdrożeniu. </br></br>Aby dowiedzieć się więcej, zobacz [grupy dynamiczne](../../automation/automation-update-management.md#using-dynamic-groups)|
| Maszyny do zaktualizowania |Wybierz zapisane wyszukiwanie, zaimportowaną grupę lub wybierz maszynę z listy rozwijanej i wybierz poszczególne maszyny. W przypadku wybrania opcji **maszyny**gotowość komputera zostanie wyświetlona w kolumnie **Aktualizowanie gotowości agenta** .</br> Aby dowiedzieć się więcej na temat różnych metod tworzenia grup komputerów w dziennikach Azure Monitor, zobacz [grupy komputerów w dziennikach Azure monitor](../../azure-monitor/platform/computer-groups.md) |
|Klasyfikacje aktualizacji|Wybierz wszystkie wymagane klasyfikacje aktualizacji|
|Uwzględnij/Wyklucz aktualizacje|Spowoduje to otwarcie strony **dołączania/wykluczania** . Aktualizacje do dołączenia lub wykluczenia znajdują się na oddzielnych kartach. Aby uzyskać więcej informacji na temat sposobu obsługi dołączania, zobacz [zachowanie podczas włączania](../../automation/automation-update-management.md#inclusion-behavior) |
|Ustawienia harmonogramu|Wybierz godzinę do uruchomienia, a następnie wybierz jedno lub cykliczne dla cyklu|
| Skrypty przed skryptami + po skrypcie|Wybierz skrypty do uruchomienia przed i po wdrożeniu|
| Okno obsługi |Liczba minut ustawiona dla aktualizacji. Wartość nie może być mniejsza niż 30 minut i nie więcej niż 6 godzin |
| Kontrola ponownego uruchamiania| Określa, jak należy obsługiwać ponowny rozruch. Dostępne opcje to:</br>Uruchom ponownie, jeśli jest to wymagane (ustawienie domyślne)</br>Zawsze uruchamiaj ponownie</br>Nigdy nie uruchamiaj ponownie</br>Tylko ponowny rozruch — nie spowoduje instalacji aktualizacji|

Wdrożenia aktualizacji można także tworzyć programowo. Aby dowiedzieć się, jak utworzyć wdrożenie aktualizacji za pomocą interfejsu API REST, zobacz [konfiguracje aktualizacji oprogramowania — tworzenie](/rest/api/automation/softwareupdateconfigurations/create). Istnieje również przykładowy element Runbook, który może służyć do tworzenia tygodniowego wdrożenia aktualizacji. Aby dowiedzieć się więcej na temat tego elementu Runbook, zobacz [Tworzenie tygodniowego wdrożenia aktualizacji dla co najmniej jednej maszyny wirtualnej w grupie zasobów](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

Po zakończeniu konfigurowania harmonogramu kliknij przycisk **Utwórz** przycisk i wróć do pulpitu nawigacyjnego stanu.
Należy zauważyć, że **zaplanowana** tabela zawiera utworzony harmonogram wdrożenia.

### <a name="view-results-of-an-update-deployment"></a>Wyświetl wyniki wdrożenia aktualizacji

Po rozpoczęciu zaplanowanego wdrażania stan tego wdrożenia można sprawdzić na karcie **wdrożenia aktualizacji** na ekranie **Update Management** .
Jeśli jest aktualnie uruchomiona, stan jest wyświetlany jako **w toku**. Po jego zakończeniu, jeśli to się powiedzie, zmieni się na **powodzenie**.
W przypadku wystąpienia błędu w co najmniej jednej aktualizacji w ramach wdrożenia jest to stan **częściowo zakończony niepowodzeniem**.
Wybierz ukończone wdrożenie aktualizacji, aby wyświetlić pulpit nawigacyjny dla tego wdrożenia aktualizacji.

![Pulpit nawigacyjny stanu wdrożenia aktualizacji dla określonego wdrożenia](./media/tutorial-monitoring/manage-updates-view-results.png)

Kafelek **wyniki aktualizacji** zawiera podsumowanie łącznej liczby aktualizacji i wyników wdrożenia na maszynie wirtualnej.
W tabeli po prawej stronie znajduje się szczegółowy podział każdej aktualizacji i wyniki instalacji, które mogą mieć jedną z następujących wartości:

* **Nie podjęto próby** — nie zainstalowano aktualizacji z powodu niewystarczającego czasu na podstawie zdefiniowanego czasu trwania okna obsługi.
* **Powodzenie** — aktualizacja powiodła się
* **Niepowodzenie** — aktualizacja nie powiodła się

Wybierz pozycję **wszystkie dzienniki** , aby wyświetlić wszystkie wpisy dziennika utworzone przez wdrożenie.

Wybierz kafelek **dane wyjściowe** , aby wyświetlić strumień zadań elementu Runbook odpowiedzialnego za zarządzanie wdrożeniem aktualizacji na docelowej maszynie wirtualnej.

Wybierz pozycję **Błędy** , aby wyświetlić szczegółowe informacje o błędach z wdrożenia.

## <a name="monitor-changes-and-inventory"></a>Monitorowanie zmian i spisu

Można zbierać i wyświetlać Spis oprogramowania, plików, demonów systemu Linux, usług Windows i kluczy rejestru systemu Windows na komputerach. Śledzenie konfiguracji maszyn może pomóc w identyfikowaniu problemów operacyjnych w środowisku i lepszym zrozumieniu stanu maszyn.

### <a name="enable-change-and-inventory-management"></a>Włączanie zarządzania zmianami i spisem

Włącz zarządzanie zmianami i spisem dla maszyny wirtualnej:

1. Po lewej stronie ekranu wybierz pozycję **maszyny wirtualne**.
2. Z listy wybierz maszynę wirtualną.
3. Na ekranie maszyny wirtualnej w sekcji **operacje** wybierz pozycję **spis** lub **śledzenie zmian**. Zostanie otwarty ekran **włącz Change Tracking i spis** .

Skonfiguruj lokalizację, Log Analytics obszar roboczy i konto usługi Automation, a następnie wybierz pozycję **Włącz**. Jeśli pola są wygaszone, oznacza to, że inne rozwiązanie automatyzacji jest włączone dla maszyny wirtualnej i należy użyć tego samego obszaru roboczego i konta usługi Automation. Mimo że rozwiązania są oddzielne w menu, są to te same rozwiązania. Włączenie jednego z nich umożliwia obu MASZYNom wirtualnym.

![Włącz śledzenie zmian i spisu](./media/tutorial-monitoring/manage-inventory-enable.png)

Po włączeniu rozwiązania może upłynąć trochę czasu podczas zbierania spisu na maszynie wirtualnej przed wyświetleniem danych.

### <a name="track-changes"></a>Śledź zmiany

Na maszynie wirtualnej wybierz **Change Tracking** w obszarze **operacje**. Wybierz pozycję **Edytuj ustawienia**. zostanie wyświetlona strona **Change Tracking** . Wybierz typ ustawienia, które chcesz śledzić, a następnie wybierz pozycję **+ Dodaj** , aby skonfigurować ustawienia. Dostępna opcja systemu Linux to **pliki systemu Linux**

Aby uzyskać szczegółowe informacje na temat Change Tracking Zobacz temat [Rozwiązywanie problemów ze zmianami w maszynie wirtualnej](../../automation/automation-tutorial-troubleshoot-changes.md)

### <a name="view-inventory"></a>Wyświetlanie spisu

Na maszynie wirtualnej wybierz pozycję **spis** w obszarze **operacje**. Na karcie **oprogramowanie** znajduje się lista dostępnych oprogramowania. Szczegóły wysokiego poziomu dla każdego rekordu oprogramowania są widoczne w tabeli. Te szczegóły obejmują nazwę oprogramowania, wersję, wydawcę, czas ostatniego odświeżenia.

![Wyświetlanie spisu](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>Monitorowanie dzienników aktywności i zmian

Na stronie **śledzenie zmian** na maszynie wirtualnej wybierz pozycję **Zarządzaj połączeniem dziennika aktywności**. To zadanie otwiera stronę **dziennika aktywności platformy Azure** . Wybierz pozycję **Połącz** , aby połączyć śledzenie zmian z dziennikiem aktywności platformy Azure dla maszyny wirtualnej.

Po włączeniu tego ustawienia przejdź do strony **Przegląd** dla swojej maszyny wirtualnej i wybierz pozycję **Zatrzymaj** , aby zatrzymać maszynę wirtualną. Po wyświetleniu monitu wybierz pozycję **tak** , aby zatrzymać maszynę wirtualną. Po cofnięciu przydziału wybierz pozycję **Rozpocznij** , aby ponownie uruchomić maszynę wirtualną.

Zatrzymywanie i uruchamianie maszyny wirtualnej rejestruje zdarzenie w dzienniku aktywności. Wróć do strony **śledzenie zmian** . Wybierz kartę **zdarzenia** w dolnej części strony. Po czasie zdarzenia wyświetlane na wykresie i w tabeli. Każde zdarzenie można wybrać, aby wyświetlić szczegółowe informacje dotyczące zdarzenia.

![Wyświetl zmiany w dzienniku aktywności](./media/tutorial-monitoring/manage-activitylog-view-results.png)

Wykres pokazuje zmiany, które wystąpiły w czasie. Po dodaniu połączenia dziennika aktywności wykres liniowy u góry Wyświetla zdarzenia dziennika aktywności platformy Azure. Każdy wiersz wykresów słupkowych reprezentuje inny typ zmiany do śledzenia. Te typy to demony systemu Linux, pliki i oprogramowanie. Karta zmiana zawiera szczegóły dotyczące zmian pokazywanych w wizualizacji w kolejności malejącej w czasie, gdy nastąpiła zmiana (ostatnio pierwsza).

## <a name="next-steps"></a>Następne kroki

W tym samouczku skonfigurowano i sprawdzono Change Tracking i Update Management dla maszyny wirtualnej. Wiesz już, jak:

> [!div class="checklist"]
> * Utwórz grupę zasobów i maszynę wirtualną
> * Zarządzanie aktualizacjami systemu Linux
> * Monitorowanie zmian i spisu

Przejdź do następnego samouczka, aby dowiedzieć się więcej o monitorowaniu maszyny wirtualnej.

> [!div class="nextstepaction"]
> [Monitorowanie maszyn wirtualnych](tutorial-monitor.md)