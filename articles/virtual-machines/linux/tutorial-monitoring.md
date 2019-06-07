---
title: Samouczek — monitorowanie i aktualizowanie maszyn wirtualnych z systemem Linux na platformie Azure | Microsoft Docs
description: W tym samouczku dowiesz się, jak monitorować diagnostykę rozruchu i metryki wydajności, a także jak zarządzać aktualizacjami pakietów na maszynie wirtualnej z systemem Linux
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/26/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 921505e7f470d337d9e9e491c6db79930d487eb5
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2019
ms.locfileid: "66754379"
---
# <a name="tutorial-monitor-and-update-a-linux-virtual-machine-in-azure"></a>Samouczek: Monitorowanie i aktualizowanie maszyny wirtualnej z systemem Linux na platformie Azure

Aby upewnić się, że maszyny wirtualne na platformie Azure działają prawidłowo, możesz sprawdzić diagnostykę rozruchu i metryki wydajności oraz zarządzać aktualizacjami pakietów. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Włączanie diagnostyki rozruchu na maszynie wirtualnej
> * Wyświetlanie diagnostyki rozruchu
> * Wyświetlanie metryki hosta
> * Włączanie rozszerzenia diagnostyki na maszynie wirtualnej
> * Wyświetlanie metryki maszyny wirtualnej
> * Tworzenie alertów w oparciu o metrykę diagnostyki
> * Zarządzanie aktualizacjami pakietów
> * Monitorowanie zmian i spisu
> * Konfigurowanie zaawansowanego monitorowania

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.30 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="create-vm"></a>Tworzenie maszyny wirtualnej

Aby zobaczyć diagnostykę i metryki w akcji, potrzebujesz maszyny wirtualnej. Najpierw utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroupMonitor* w lokalizacji *eastus*.

```azurecli-interactive
az group create --name myResourceGroupMonitor --location eastus
```

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm#az-vm-create). Następujący przykład umożliwia utworzenie maszyny wirtualnej o nazwie *myVM* i wygenerowanie kluczy SSH, jeśli jeszcze nie istnieją w folderze *~/.ssh/* :

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="enable-boot-diagnostics"></a>Włączanie diagnostyki rozruchu

Podczas rozruchu maszyny wirtualnej z systemem Linux rozszerzenie diagnostyki rozruchu przechwytuje dane wyjściowe rozruchu i przechowuje je w magazynie na platformie Azure. Dane te mogą posłużyć do rozwiązywania problemów związanych z rozruchem maszyny wirtualnej. Diagnostyka rozruchu nie jest automatycznie włączona podczas tworzenia maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure.

Przed włączeniem diagnostyki rozruchu należy utworzyć konto magazynu do przechowywania dzienników rozruchu. Konta magazynu muszą mieć globalnie unikatowe nazwy o długości od 3 do 24 znaków, które zawierają wyłącznie cyfry i małe litery. Utwórz konto magazynu przy użyciu polecenia [az storage account create](/cli/azure/storage/account#az-storage-account-create). W tym przykładzie losowy ciąg jest używany do utworzenia unikatowej nazwy konta magazynu.

```azurecli-interactive
storageacct=mydiagdata$RANDOM

az storage account create \
  --resource-group myResourceGroupMonitor \
  --name $storageacct \
  --sku Standard_LRS \
  --location eastus
```

Podczas włączania diagnostyki rozruchu jest wymagany identyfikator URI do kontenera magazynu obiektów blob. Następujące polecenie wysyła zapytanie do konta magazynu, aby zwrócić ten identyfikator URI. Wartość identyfikatora URI jest przechowywana w zmiennej o nazwie *bloburi*, która zostanie użyta w następnym kroku.

```azurecli-interactive
bloburi=$(az storage account show --resource-group myResourceGroupMonitor --name $storageacct --query 'primaryEndpoints.blob' -o tsv)
```

Teraz włącz diagnostykę rozruchu przy użyciu polecenia [az vm boot-diagnostics enable](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az-vm-boot-diagnostics-enable). Wartość `--storage` to identyfikator URI obiektu blob, który został zebrany w poprzednim kroku.

```azurecli-interactive
az vm boot-diagnostics enable \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --storage $bloburi
```

## <a name="view-boot-diagnostics"></a>Wyświetlanie diagnostyki rozruchu

Jeśli diagnostyka rozruchu jest włączona, za każdym razem w przypadku zatrzymania i uruchomienia maszyny wirtualnej informacje o procesie rozruchu zostaną zapisane w pliku dziennika. W tym przykładzie najpierw cofnij przydział maszyny wirtualnej przy użyciu polecenia [az vm deallocate](/cli/azure/vm#az-vm-deallocate) w następujący sposób:

```azurecli-interactive
az vm deallocate --resource-group myResourceGroupMonitor --name myVM
```

Teraz uruchom maszynę wirtualną przy użyciu polecenia [az vm start]( /cli/azure/vm#az-vm-stop) w następujący sposób:

```azurecli-interactive
az vm start --resource-group myResourceGroupMonitor --name myVM
```

Możesz uzyskać dane diagnostyki rozruchu dla maszyny wirtualnej *myVM* przy użyciu polecenia [az vm boot-diagnostics get-boot-log](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az-vm-boot-diagnostics-get-boot-log) w następujący sposób:

```azurecli-interactive
az vm boot-diagnostics get-boot-log --resource-group myResourceGroupMonitor --name myVM
```

## <a name="view-host-metrics"></a>Wyświetlanie metryki hosta

Maszyna wirtualna z systemem Linux ma dedykowanego hosta na platformie Azure, z którym wchodzi w interakcję. Metryki są automatycznie zbierane dla hosta. Można je wyświetlić w witrynie Azure Portal w następujący sposób:

1. W witrynie Azure Portal wybierz pozycję **Grupy zasobów**, wybierz pozycję **myResourceGroupMonitor**, a następnie na liście zasobów wybierz maszynę wirtualną **myVM**.
1. Aby zobaczyć, jak działa maszyna wirtualna hosta, w oknie maszyny wirtualnej wybierz pozycję **Metryki**, a następnie wybierz dowolną metrykę *[Host]* w pozycji **Dostępne metryki**.

    ![Wyświetlanie metryki hosta](./media/tutorial-monitoring/monitor-host-metrics.png)

## <a name="install-diagnostics-extension"></a>Instalacja rozszerzenia diagnostyki

Dostępne są podstawowe metryki hosta, ale wyświetlenie bardziej szczegółowych metryk właściwych dla danej maszyny wirtualnej wymaga zainstalowania rozszerzenia diagnostyki platformy Azure na maszynie wirtualnej. Rozszerzenie diagnostyki platformy Azure umożliwia pobieranie dodatkowych danych dotyczących monitorowania i diagnostyki z maszyny wirtualnej. Możesz wyświetlić te metryki wydajności i utworzyć alerty w oparciu o wydajność maszyny wirtualnej. Rozszerzenie diagnostyki jest instalowane za pośrednictwem witryny Azure Portal w następujący sposób:

1. W witrynie Azure Portal kliknij pozycję **Grupy zasobów**, wybierz pozycję **myResourceGroupMonitor**, a następnie na liście zasobów wybierz maszynę wirtualną **myVM**.
1. Wybierz pozycję **Ustawienia diagnozy**. W menu rozwijanym *Wybierz konto magazynu* wybierz utworzone w poprzedniej sekcji konto *mydiagdata [1234]* , jeśli jeszcze nie zostało wybrane.
1. Wybierz przycisk **Włącz monitorowanie na poziomie gościa**.

    ![Wyświetlanie metryki diagnostyki](./media/tutorial-monitoring/enable-diagnostics-extension.png)

## <a name="view-vm-metrics"></a>Wyświetlanie metryki maszyny wirtualnej

Możesz wyświetlić metryki maszyny wirtualnej w ten sam sposób co metryki maszyny wirtualnej hosta:

1. W witrynie Azure Portal kliknij pozycję **Grupy zasobów**, wybierz pozycję **myResourceGroupMonitor**, a następnie na liście zasobów wybierz maszynę wirtualną **myVM**.
1. Aby zobaczyć, jak działa maszyna wirtualna, w oknie maszyny wirtualnej wybierz pozycję **Metryki**, a następnie wybierz dowolną metrykę diagnostyki *[Gość]* w pozycji **Dostępne metryki**.

    ![Wyświetlanie metryki maszyny wirtualnej](./media/tutorial-monitoring/monitor-vm-metrics.png)

## <a name="create-alerts"></a>Tworzenie alertów

Możesz utworzyć alerty w oparciu o konkretne metryki wydajności. Przykładowo alertów można używać do wysyłania powiadomień w przypadku przekroczenia wybranego progu użycia procesora lub spadku dostępnego miejsca na dysku poniżej wybranej wartości. Alerty mogą być wyświetlane w witrynie Azure Portal lub wysyłane za pośrednictwem poczty e-mail. Możesz też wyzwolić elementy runbook usługi Azure Automation lub aplikacje usługi Azure Logic Apps w ramach reakcji na wygenerowane alerty.

Poniższy przykład tworzy alert dotyczący średniego użycia procesora.

1. W witrynie Azure Portal kliknij pozycję **Grupy zasobów**, wybierz pozycję **myResourceGroupMonitor**, a następnie na liście zasobów wybierz maszynę wirtualną **myVM**.
2. Wybierz opcję **Alerty (klasyczne)** , następnie w górnej części okna alertów wybierz opcję **Dodaj alert metryki (klasyczny)** .
3. Podaj **nazwę** alertu, np. *myAlertRule*
4. Aby wyzwolić alert, gdy procent użycia procesora przekracza 1,0 przez pięć minut, pozostaw wybrane wszystkie inne wartości domyślne.
5. Opcjonalnie zaznacz pole pozycji *Wyślij wiadomość e-mail do właścicieli, współautorów i czytelników*, aby wysłać powiadomienie w wiadomości e-mail. Domyślne działanie to prezentowanie powiadomienia w portalu.
6. Wybierz przycisk **OK**.

## <a name="manage-software-updates"></a>Zarządzanie aktualizacjami oprogramowania

Rozwiązanie Update Management pozwala zarządzać aktualizacjami i poprawkami dla maszyn wirtualnych platformy Azure z systemem Linux.
Bezpośrednio z poziomu maszyny wirtualnej możesz szybko ocenić stan dostępnych aktualizacji, zaplanować instalację wymaganych aktualizacji i przejrzeć wyniki wdrażania, aby sprawdzić, czy aktualizacje zostały zastosowane pomyślnie do maszyny wirtualnej.

Aby uzyskać informacje o cenach, zobacz [cennik usługi Automation dla rozwiązania Update Management](https://azure.microsoft.com/pricing/details/automation/)

### <a name="enable-update-management"></a>Włączanie rozwiązania Update Management

Włącz rozwiązanie Update Management dla maszyny wirtualnej:

1. Po lewej stronie ekranu wybierz pozycję **Maszyny wirtualne**.
2. Z listy wybierz maszynę wirtualną.
3. Na ekranie maszyny wirtualnej w sekcji **Operacje** wybierz pozycję **Update Management**. Zostanie wyświetlony ekran **Włączanie rozwiązania Update Management**.

Jest przeprowadzana walidacja w celu ustalenia, czy rozwiązanie Update Management jest włączone dla tej maszyny wirtualnej.
Walidacja obejmuje kontrole obszaru roboczego usługi Log Analytics i powiązanego konta usługi Automation i tego, czy rozwiązanie znajduje się w obszarze roboczym.

Obszar roboczy usługi [Log Analytics](../../log-analytics/log-analytics-overview.md) służy do zbierania danych generowanych przez funkcje i usługi, takie jak rozwiązanie Update Management.
Obszar roboczy zawiera pojedynczą lokalizację do przeglądania i analizowania danych z wielu źródeł.
Aby wykonać dodatkowe akcje na maszynach wirtualnych, które wymagają aktualizacji, usługa Azure Automation pozwala na uruchamianie elementów Runbook dla maszyn wirtualnych, takich jak pobieranie i stosowanie aktualizacji.

Proces walidacji sprawdza również, czy maszyna wirtualna jest aprowizowana za pomocą agenta usługi Log Analytics i hybrydowego procesu roboczego runbook usługi Automation. Ten agent jest używany do komunikacji z maszyną wirtualną i uzyskiwania informacji dotyczących stanu aktualizacji.

Wybierz usługi Log Analytics obszar roboczy i konto usługi automation i wybierz pozycję **Włącz** Aby włączyć rozwiązanie. Włączanie rozwiązania może trwać do 15 minut.

Jeśli którekolwiek z następujących wymagań wstępnych nie będzie występować podczas dołączania, zostanie ono automatycznie dołączone:

* Obszar roboczy usługi [Log Analytics](../../log-analytics/log-analytics-overview.md)
* [Konto usługi Automation](../../automation/automation-offering-get-started.md)
* [Hybrydowy proces roboczy elementu Runbook](../../automation/automation-hybrid-runbook-worker.md) jest włączony na maszynie wirtualnej

Zostanie otwarty ekran **Rozwiązanie Update Management**. Skonfiguruj lokalizację, obszar roboczy usługi Log Analytics i konto usługi Automation i wybierz **Włącz**. Jeśli pola są wygaszone, oznacza to, że inne rozwiązanie automatyzacji jest włączone dla maszyny wirtualnej, a tym samym należy użyć tego samego obszaru roboczego i konta automatyzacji.

![Włączanie rozwiązania Update Management](./media/tutorial-monitoring/manage-updates-update-enable.png)

Włączanie rozwiązania może potrwać do 15 minut. W tym czasie nie należy zamykać okna przeglądarki. Po włączeniu rozwiązania informacje dotyczące brakujących aktualizacji maszyny wirtualnej są przekazywane do dzienników usługi Azure Monitor. Udostępnienie danych do analizy może potrwać od 30 minut do 6 godzin.

### <a name="view-update-assessment"></a>Wyświetlanie oceny aktualizacji

Po włączeniu rozwiązania **Update Management** zostanie wyświetlony ekran **Update Management**. Po zakończeniu oceny aktualizacji możesz zobaczyć listę brakujących aktualizacji na karcie **Brakujące aktualizacje**.

 ![Wyświetlanie stanu aktualizacji](./media/tutorial-monitoring/manage-updates-view-status-linux.png)

### <a name="schedule-an-update-deployment"></a>Planowanie wdrożenia aktualizacji

Aby zainstalować aktualizacje, zaplanuj wdrożenie zgodnie z harmonogramem wydawania i oknem obsługi. Możesz wybrać typy aktualizacji, które mają zostać uwzględnione we wdrożeniu. Możesz na przykład uwzględnić aktualizacje krytyczne lub aktualizacje zabezpieczeń i wykluczyć pakiety zbiorcze aktualizacji.

Zaplanuj nowe wdrożenie aktualizacji dla maszyny wirtualnej, klikając pozycję **Zaplanuj wdrażanie aktualizacji** w górnej części ekranu **Update Management**. Na ekranie **Nowe wdrożenie aktualizacji** podaj następujące informacje:

Aby utworzyć nowe wdrożenie aktualizacji, wybierz **Zaplanuj wdrażanie aktualizacji**. **Nowe wdrożenie aktualizacji** zostanie otwarta strona. Wprowadź wartości dla właściwości opisane w poniższej tabeli, a następnie kliknij przycisk **Utwórz**:

| Właściwość | Opis |
| --- | --- |
| Name (Nazwa) |Unikatowa nazwa identyfikującą wdrożenie aktualizacji. |
|System operacyjny| System Linux lub Windows|
| Grupy aktualizacji |Dla maszyn należy zdefiniować zapytań, w zależności od kombinacji subskrypcji, grupy zasobów, lokalizacje i tagi, do tworzenia grupy dynamicznej maszyn wirtualnych platformy Azure, aby uwzględnić w danym wdrożeniu. </br></br>W przypadku komputerów spoza platformy Azure wybierz istniejącą zapisanego wyszukiwania, aby wybrać grupę maszyn spoza platformy Azure w celu uwzględnione we wdrożeniu. </br></br>Aby dowiedzieć się więcej, zobacz [Grupy dynamiczne](../../automation/automation-update-management.md#using-dynamic-groups)|
| Maszyny do zaktualizowania |Wybierz zapisane wyszukiwanie bądź zaimportowaną grupę lub wybierz maszynę z listy rozwijanej, a następnie wybierz poszczególne maszyny. Jeśli wybierzesz pozycję **Maszyny**, gotowość maszyny będzie wyświetlana w kolumnie **AKTUALIZUJ GOTOWOŚĆ AGENTA**.</br> Aby dowiedzieć się więcej na temat różnych metod tworzenia grup komputerów w dziennikach usługi Azure Monitor, zobacz [Computer groups in Azure Monitor logs (Grupy komputerów w dziennikach usługi Azure Monitor)](../../azure-monitor/platform/computer-groups.md) |
|Klasyfikacje aktualizacji|Wybierz wszystkie klasyfikacje aktualizacji, które są potrzebne|
|Uwzględnianie/wykluczanie aktualizacji|Spowoduje to otwarcie **uwzględniania/wykluczania** strony. Aktualizacje, które mają zostać uwzględnione lub wykluczone, znajdują się na osobnych kartach. Aby uzyskać więcej informacji na temat sposobu obsługi dołączania, zobacz [zachowanie dołączania](../../automation/automation-update-management.md#inclusion-behavior) |
|Ustawienia harmonogramu|Wybierz godzinę do uruchomienia i wybrać jednorazowo lub cykliczne cyklu|
| Skrypty przed i skryptu używanego po utworzeniu|Wybierz skrypty do uruchomienia przed i po wdrożeniu|
| Okno obsługi |Liczba minut dla aktualizacji. Wartość nie może być mniejsza niż 30 minut, a nie więcej niż 6 godzin |
| Ponowne uruchomienie kontroli| Określa sposób obsługi jest uruchamiany ponownie. Dostępne opcje:</br>Ponowne uruchomienie, jeśli jest to wymagane (ustawienie domyślne)</br>Zawsze uruchamiaj ponownie</br>Nigdy nie uruchamiaj ponownie</br>Tylko ponowne uruchomienie — aktualizacje nie zostaną zainstalowane|

Można także programowo tworzyć wdrożenia aktualizacji. Aby dowiedzieć się, jak utworzyć wdrożenie aktualizacji za pomocą interfejsu API REST, zobacz [konfiguracji aktualizacji oprogramowania — tworzenie](/rest/api/automation/softwareupdateconfigurations/create). Istnieje również przykładowy element runbook, który może służyć do tworzenia tygodniowy wdrożenia aktualizacji. Aby dowiedzieć się więcej na temat tego elementu runbook, zobacz [utworzyć tygodniowy wdrożenie aktualizacji dla jednego lub więcej maszyn wirtualnych w grupie zasobów](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

Po ukończeniu konfigurowania harmonogramu kliknij przycisk **Utwórz**, aby wrócić do pulpitu nawigacyjnego stanu.
Tabela **Zaplanowane** zawiera utworzony harmonogram wdrożenia.

### <a name="view-results-of-an-update-deployment"></a>Wyświetlanie wyników wdrażania aktualizacji

Po rozpoczęciu zaplanowanego wdrażania stan tego wdrożenia można sprawdzić na karcie **Wdrożenia aktualizacji** na ekranie rozwiązania **Update Management**.
Jeśli wdrażanie trwa, wdrożenie ma stan **W toku**. Po pomyślnym ukończeniu wdrażania stan zmienia się na **Powodzenie**.
W przypadku błędu co najmniej jednej aktualizacji w ramach wdrożenia jest wyświetlany stan **Częściowe niepowodzenie**.
Kliknij ukończone wdrożenie aktualizacji, aby wyświetlić pulpit nawigacyjny tego wdrożenia.

![Pulpit nawigacyjny stanu wdrożenia aktualizacji dla określonego wdrożenia](./media/tutorial-monitoring/manage-updates-view-results.png)

Kafelek **Wyniki aktualizacji** zawiera podsumowanie z łączną liczbą aktualizacji i wynikami wdrożenia na maszynie wirtualnej.
Tabela po prawej stronie zawiera szczegółowy podział każdej aktualizacji i wyniki instalacji, które mogą mieć jedną z następujących wartości:

* **Nie podjęto próby** — nie zainstalowano aktualizacji z powodu niewystarczającego czasu w zdefiniowanym oknie konserwacji.
* **Powodzenie** — aktualizacja powiodła się.
* **Niepowodzenie** — aktualizacja nie powiodła się.

Aby wyświetlić wszystkie wpisy dziennika utworzone przez wdrożenie, wybierz opcję **Wszystkie dzienniki**.

Wybierz kafelek **Dane wyjściowe**, aby wyświetlić strumień zadań elementu runbook odpowiedzialnego za zarządzanie wdrożeniem aktualizacji na docelowej maszynie wirtualnej.

Aby wyświetlić szczegółowe informacje o błędach związanych z wdrożeniem, wybierz pozycję **Błędy**.

## <a name="monitor-changes-and-inventory"></a>Monitorowanie zmian i spisu

Możesz zbierać i wyświetlać spis oprogramowania, plików, demonów systemu Linux, usług systemu Windows i kluczy rejestru systemu Windows znajdujących się na Twoich komputerach. Śledzenie konfiguracji maszyn ułatwia identyfikowanie problemów operacyjnych w środowisku oraz lepsze rozumienie stanu maszyn.

### <a name="enable-change-and-inventory-management"></a>Włączanie zarządzania zmianami i spisem

Aby włączyć zarządzanie zmianami i spisem na maszynie wirtualnej:

1. Po lewej stronie ekranu wybierz pozycję **Maszyny wirtualne**.
2. Z listy wybierz maszynę wirtualną.
3. Na ekranie maszyny wirtualnej w sekcji **Operacje** wybierz pozycję **Spis** lub **Śledzenie zmian**. Zostanie otwarty ekran **Włączanie śledzenia zmian i spisu**.

Skonfiguruj lokalizację, obszar roboczy usługi Log Analytics i konto usługi Automation i wybierz **Włącz**. Jeśli pola są wygaszone, oznacza to, że inne rozwiązanie automatyzacji jest włączone dla maszyny wirtualnej, a tym samym należy użyć tego samego obszaru roboczego i konta automatyzacji. Nawet jeśli te rozwiązania są oddzielone w menu, jest to jedno rozwiązanie. Włączenie jednego z nich oznacza włączenie obydwu na maszynie wirtualnej.

![Włączanie śledzenia zmian i spisu](./media/tutorial-monitoring/manage-inventory-enable.png)

Po włączeniu rozwiązania zebranie danych spisu na maszynie wirtualnej przed ich wyświetleniem może potrwać pewien czas.

### <a name="track-changes"></a>Śledzenie zmian

Na maszynie wirtualnej wybierz pozycję **Śledzenie zmian** w obszarze **OPERACJE**. Wybierz opcję **Edytuj ustawienia**. Zostanie wyświetlona strona **Śledzenie zmian**. Wybierz typ ustawienia do śledzenia, a następnie kliknij pozycję **+ Dodaj** w celu skonfigurowania ustawień. Opcja dostępna dla systemu Linux to **Pliki systemu Linux**

Aby uzyskać szczegółowe informacje na temat rozwiązania Change Tracking, zobacz [Rozwiązywanie problemów dotyczących zmian na maszynie wirtualnej](../../automation/automation-tutorial-troubleshoot-changes.md)

### <a name="view-inventory"></a>Wyświetlanie spisu

Na maszynie wirtualnej wybierz pozycję **Spis** w obszarze **OPERACJE**. Na karcie **Oprogramowanie** znajduje się lista tabelowa oprogramowania, które zostało odnalezione. Szczegółowe informacje wysokiego poziomu dotyczące każdego rekordu oprogramowania są wyświetlane w tabeli. Obejmują one nazwę, wersję, wydawcę i czas ostatniego odświeżenia oprogramowania.

![Wyświetlanie spisu](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>Monitorowanie dzienników aktywności i zmian

Ze strony **Śledzenie zmian** na swojej maszynie wirtualnej wybierz pozycję **Zarządzanie połączeniem dziennika aktywności**. To zadanie powoduje otwarcie strony **Dziennik aktywności platformy Azure**. Wybierz pozycję **Połącz**, aby połączyć śledzenie zmian z dziennikiem aktywności platformy Azure dla Twojej maszyny wirtualnej.

Po włączeniu tego ustawienia przejdź do strony **Omówienie** dla maszyny wirtualnej i wybierz pozycję **Zatrzymaj**, aby zatrzymać swoją maszynę wirtualną. Po wyświetleniu monitu wybierz pozycję **Tak**, aby zatrzymać maszynę wirtualną. Po cofnięciu jej przydziału wybierz pozycję **Start**, aby ponownie uruchomić maszynę wirtualną.

Zatrzymanie i uruchomienie maszyny wirtualnej rejestruje zdarzenie w jego dzienniku aktywności. Przejdź z powrotem do strony **Śledzenie zmian**. Wybierz kartę **Zdarzenia** u dołu strony. Po chwili zdarzenia są wyświetlane na wykresie i w tabeli. Każde zdarzenie można wybrać, aby wyświetlić szczegółowe informacje o zdarzeniu.

![Wyświetlanie zmian w dzienniku aktywności](./media/tutorial-monitoring/manage-activitylog-view-results.png)

Wykres pokazuje zmiany, które wystąpiły w czasie. Po dodaniu połączenia dziennika aktywności wykres liniowy u góry wyświetla zdarzenia dziennika aktywności platformy Azure. Każdy wiersz wykresów słupkowych reprezentuje innego typu zmiany umożliwiające śledzenie. Te typy to oprogramowanie, pliki i demony systemu Linux. Karta zmiany przedstawia szczegółowe informacje dla zmian pokazanych w wizualizacji w kolejności malejącej według czasu, kiedy wystąpiła zmiana (najnowsze na początku).

## <a name="advanced-monitoring"></a>Zaawansowane monitorowanie

Możesz wykonywać bardziej zaawansowane monitorowanie swojej maszyny wirtualnej, używając rozwiązania takiego jak usługa [Azure Monitor dla maszyn wirtualnych](../../azure-monitor/insights/vminsights-overview.md), która monitoruje maszyny wirtualne platformy Azure na dużą skalę, analizując wydajność i kondycję maszyn wirtualnych z systemami Windows i Linux, w tym ich różne procesy i sprzężone zależności od innych zasobów oraz procesów zewnętrznych. Zarządzanie konfiguracją maszyn wirtualnych platformy Azure jest realizowane za pomocą rozwiązania Change Tracking and Inventory [usługi Azure Automation](../../automation/automation-intro.md), co pozwala na łatwe identyfikowanie zmian w środowisku. Zarządzania zgodnością aktualizacji jest realizowane za pomocą rozwiązania Update Management usługi Azure Automation.   

Z poziomu obszaru roboczego usługi Log Analytics, z którym jest połączona maszyna wirtualna, możesz także pobierać, konsolidować i analizować zebrane dane za pomocą [zaawansowanego języka zapytań](../../azure-monitor/log-query/log-query-overview.md). 

![Obszar roboczy usługi log Analytics](./media/tutorial-monitoring/tutorial-monitor-oms.png)

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku skonfigurowano i sprawdzono aktualizacje dla maszyny wirtualnej oraz zarządzano nimi. W tym samouczku omówiono:

> [!div class="checklist"]
> * Włączanie diagnostyki rozruchu na maszynie wirtualnej
> * Wyświetlanie diagnostyki rozruchu
> * Wyświetlanie metryki hosta
> * Włączanie rozszerzenia diagnostyki na maszynie wirtualnej
> * Wyświetlanie metryki maszyny wirtualnej
> * Tworzenie alertów w oparciu o metrykę diagnostyki
> * Zarządzanie aktualizacjami pakietów
> * Monitorowanie zmian i spisu
> * Konfigurowanie zaawansowanego monitorowania

Przejdź do następnego samouczka, aby dowiedzieć się więcej na temat usługi Azure Security Center.

> [!div class="nextstepaction"]
> [Zarządzanie zabezpieczeniami maszyn wirtualnych](./tutorial-azure-security.md)
