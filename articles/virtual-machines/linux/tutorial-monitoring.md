---
title: Monitorowanie i aktualizowanie maszyn wirtualnych z systemem Linux na platformie Azure | Microsoft Docs
description: Dowiedz się, jak monitorować diagnostykę rozruchu i metryki wydajności, a także jak zarządzać aktualizacjami pakietów na maszynie wirtualnej z systemem Linux na platformie Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/08/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 9ffd36da535a2e5ac4a355f429394dc4209348b7
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
---
# <a name="how-to-monitor-and-update-a-linux-virtual-machine-in-azure"></a>Jak monitorować i aktualizować maszynę wirtualną z systemem Linux na platformie Azure

Aby upewnić się, że maszyny wirtualne na platformie Azure działają prawidłowo, możesz sprawdzić diagnostykę rozruchu i metryki wydajności oraz zarządzać aktualizacjami pakietów. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Włączanie diagnostyki rozruchu na maszynie wirtualnej
> * Wyświetlanie diagnostyki rozruchu
> * Wyświetlanie metryki hosta
> * Włączanie rozszerzenia diagnostyki na maszynie wirtualnej
> * Wyświetlanie metryki maszyny wirtualnej
> * Tworzenie alertów w oparciu o metrykę diagnostyki
> * Zarządzanie aktualizacjami pakietów
> * Konfigurowanie zaawansowanego monitorowania


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-vm"></a>Tworzenie maszyny wirtualnej

Aby zobaczyć diagnostykę i metryki w akcji, potrzebujesz maszyny wirtualnej. Najpierw utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroupMonitor* w lokalizacji *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupMonitor --location eastus
```

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](https://docs.microsoft.com/cli/azure/vm#az_vm_create). W poniższym przykładzie utworzono maszynę wirtualną o nazwie *myVM*:

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

Przed włączeniem diagnostyki rozruchu należy utworzyć konto magazynu do przechowywania dzienników rozruchu. Konta magazynu muszą mieć globalnie unikatowe nazwy o długości od 3 do 24 znaków, które zawierają wyłącznie cyfry i małe litery. Utwórz konto magazynu przy użyciu polecenia [az storage account create](/cli/azure/storage/account#az_storage_account_create). W tym przykładzie losowy ciąg jest używany do utworzenia unikatowej nazwy konta magazynu. 

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

Teraz włącz diagnostykę rozruchu przy użyciu polecenia [az vm boot-diagnostics enable](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az_vm_boot_diagnostics_enable). Wartość `--storage` to identyfikator URI obiektu blob, który został zebrany w poprzednim kroku.

```azurecli-interactive 
az vm boot-diagnostics enable \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --storage $bloburi
```


## <a name="view-boot-diagnostics"></a>Wyświetlanie diagnostyki rozruchu

Jeśli diagnostyka rozruchu jest włączona, za każdym razem w przypadku zatrzymania i uruchomienia maszyny wirtualnej informacje o procesie rozruchu zostaną zapisane w pliku dziennika. W tym przykładzie najpierw cofnij przydział maszyny wirtualnej przy użyciu polecenia [az vm deallocate](/cli/azure/vm#az_vm_deallocate) w następujący sposób:

```azurecli-interactive 
az vm deallocate --resource-group myResourceGroupMonitor --name myVM
```

Teraz uruchom maszynę wirtualną przy użyciu polecenia [az vm start]( /cli/azure/vm#az_vm_stop) w następujący sposób:

```azurecli-interactive 
az vm start --resource-group myResourceGroupMonitor --name myVM
```

Możesz uzyskać dane diagnostyki rozruchu dla maszyny wirtualnej *myVM* przy użyciu polecenia [az vm boot-diagnostics get-boot-log](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az_vm_boot_diagnostics_get_boot_log) w następujący sposób:

```azurecli-interactive 
az vm boot-diagnostics get-boot-log --resource-group myResourceGroupMonitor --name myVM
```


## <a name="view-host-metrics"></a>Wyświetlanie metryki hosta

Maszyna wirtualna z systemem Linux ma dedykowanego hosta na platformie Azure, z którym wchodzi w interakcję. Metryki są automatycznie zbierane dla hosta. Można je wyświetlić w witrynie Azure Portal w następujący sposób:

1. W witrynie Azure Portal kliknij pozycję **Grupy zasobów**, wybierz grupę **myResourceGroupMonitor**, a następnie wybierz maszynę wirtualną **myVM** na liście zasobów.
1. Aby zobaczyć, jak działa maszyna wirtualna hosta, kliknij pozycję **Metryki** w bloku maszyny wirtualnej, a następnie wybierz dowolną metrykę *[Host]* w pozycji **Dostępne metryki**.

    ![Wyświetlanie metryki hosta](./media/tutorial-monitoring/monitor-host-metrics.png)


## <a name="install-diagnostics-extension"></a>Instalacja rozszerzenia diagnostyki

> [!IMPORTANT]
> Ten dokument opisuje wersję 2.3 rozszerzenia diagnostyki systemu Linux, która została wycofana. Wersja 2.3 będzie obsługiwana do 30 czerwca 2018 r.
>
> Zamiast tego można włączyć wersję 3.0 rozszerzenia diagnostycznego systemu Linux. Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją](./diagnostic-extension.md).

Dostępne są podstawowe metryki hosta, ale wyświetlenie bardziej szczegółowych metryk właściwych dla danej maszyny wirtualnej wymaga zainstalowania rozszerzenia diagnostyki platformy Azure na maszynie wirtualnej. Rozszerzenie diagnostyki platformy Azure umożliwia pobieranie dodatkowych danych dotyczących monitorowania i diagnostyki z maszyny wirtualnej. Możesz wyświetlić te metryki wydajności i utworzyć alerty w oparciu o wydajność maszyny wirtualnej. Rozszerzenie diagnostyki jest instalowane za pośrednictwem witryny Azure Portal w następujący sposób:

1. W witrynie Azure Portal kliknij pozycję **Grupy zasobów**, wybierz grupę **myResourceGroup**, a następnie wybierz maszynę wirtualną **myVM** na liście zasobów.
1. Kliknij pozycję **Ustawienia diagnozy**. Lista pokazuje, że *Diagnostyka rozruchu* została już włączona w poprzedniej sekcji. Kliknij pole wyboru pozycji *Metryki podstawowe*.
1. W sekcji *Konto magazynu* przejdź do konta *mydiagdata[1234]* utworzonego w poprzedniej sekcji i wybierz je.
1. Kliknij przycisk **Zapisz**.

    ![Wyświetlanie metryki diagnostyki](./media/tutorial-monitoring/enable-diagnostics-extension.png)


## <a name="view-vm-metrics"></a>Wyświetlanie metryki maszyny wirtualnej

Możesz wyświetlić metryki maszyny wirtualnej w ten sam sposób co metryki maszyny wirtualnej hosta:

1. W witrynie Azure Portal kliknij pozycję **Grupy zasobów**, wybierz grupę **myResourceGroup**, a następnie wybierz maszynę wirtualną **myVM** na liście zasobów.
1. Aby zobaczyć, jak działa maszyna wirtualna, kliknij pozycję **Metryki** w bloku maszyny wirtualnej, a następnie wybierz dowolną metrykę diagnostyki w pozycji **Dostępne metryki**.

    ![Wyświetlanie metryki maszyny wirtualnej](./media/tutorial-monitoring/monitor-vm-metrics.png)


## <a name="create-alerts"></a>Tworzenie alertów

Możesz utworzyć alerty w oparciu o konkretne metryki wydajności. Przykładowo alertów można używać do wysyłania powiadomień w przypadku przekroczenia wybranego progu użycia procesora lub spadku dostępnego miejsca na dysku poniżej wybranej wartości. Alerty mogą być wyświetlane w witrynie Azure Portal lub wysyłane za pośrednictwem poczty e-mail. Możesz też wyzwolić elementy runbook usługi Azure Automation lub aplikacje usługi Azure Logic Apps w ramach reakcji na wygenerowane alerty.

Poniższy przykład tworzy alert dotyczący średniego użycia procesora.

1. W witrynie Azure Portal kliknij pozycję **Grupy zasobów**, wybierz grupę **myResourceGroup**, a następnie wybierz maszynę wirtualną **myVM** na liście zasobów.
2. Kliknij pozycję **Reguły alertów** w bloku maszyny wirtualnej, a następnie kliknij pozycję **Dodaj alert metryki** w górnej części bloku alertów.
4. Podaj **nazwę** alertu, np. *myAlertRule*
5. Aby wyzwolić alert, gdy procent użycia procesora przekracza 1,0 przez pięć minut, pozostaw wybrane wszystkie inne wartości domyślne.
6. Opcjonalnie zaznacz pole pozycji *Wyślij wiadomość e-mail do właścicieli, współautorów i czytelników*, aby wysłać powiadomienie w wiadomości e-mail. Domyślne działanie to prezentowanie powiadomienia w portalu.
7. Kliknij przycisk **OK**.

## <a name="manage-package-updates"></a>Zarządzanie aktualizacjami pakietów

Przy użyciu rozwiązania Update Management możesz zarządzać aktualizacjami i poprawkami pakietów dla maszyn wirtualnych z systemem Linux na platformie Azure. Bezpośrednio z poziomu maszyny wirtualnej możesz szybko ocenić stan dostępnych aktualizacji, zaplanować instalację wymaganych aktualizacji i przejrzeć wyniki wdrażania, aby sprawdzić, czy aktualizacje zostały zastosowane pomyślnie do maszyny wirtualnej.

Aby uzyskać informacje o cenach, zobacz [cennik usługi Automation dla rozwiązania Update Management](https://azure.microsoft.com/pricing/details/automation/)

### <a name="enable-update-management-preview"></a>Włączanie rozwiązania Update Management (wersja zapoznawcza)

Włączanie rozwiązania Update Management dla maszyny wirtualnej

1. Po lewej stronie ekranu wybierz pozycję **Maszyny wirtualne**.
1. Z listy wybierz maszynę wirtualną.
1. Na ekranie maszyny wirtualnej w sekcji **Operacje** kliknij pozycję **Update Management**. Zostanie wyświetlony ekran **Włączanie rozwiązania Update Management**.

Jest przeprowadzana walidacja w celu ustalenia, czy rozwiązanie Update Management jest włączone dla tej maszyny wirtualnej. Walidacja obejmuje kontrole obszaru roboczego usługi Log Analytics i powiązanego konta usługi Automation i tego, czy rozwiązanie znajduje się w obszarze roboczym.

Obszar roboczy usługi Log Analytics służy do zbierania danych generowanych przez funkcje i usługi, takie jak rozwiązanie Update Management. Obszar roboczy zawiera pojedynczą lokalizację do przeglądania i analizowania danych z wielu źródeł. Aby wykonać dodatkowe akcje na maszynach wirtualnych, które wymagają aktualizacji, usługa Azure Automation pozwala na uruchamianie skryptów dla maszyn wirtualnych, takich jak pobieranie i stosowanie aktualizacji.

Proces walidacji sprawdza również, czy maszyna wirtualna jest aprowizowana za pomocą programu Microsoft Monitoring Agent (MMA) i hybrydowego procesu roboczego. Ten agent jest używany do komunikacji z maszyną wirtualną i uzyskiwania informacji dotyczących stanu aktualizacji. 

Jeśli te wymagania wstępne nie są spełnione, zostanie wyświetlony transparent zawierający opcję włączenia rozwiązania.

![Transparent konfiguracji dołączony do rozwiązania Update Management](./media/tutorial-monitoring/manage-updates-onboard-solution-banner.png)

Kliknij transparent, aby włączyć rozwiązanie. Jeśli którekolwiek z następujących wymagań wstępnych nie będzie występować po walidacji, zostanie ono automatycznie dodane:

* Obszar roboczy usługi [Log Analytics](../../log-analytics/log-analytics-overview.md)
* [Automatyzacja](../../automation/automation-offering-get-started.md)
* [Hybrydowy proces roboczy elementu Runbook](../../automation/automation-hybrid-runbook-worker.md) jest włączony na maszynie wirtualnej

Zostanie wyświetlony ekran **Włączanie rozwiązania Update Management**. Skonfiguruj ustawienia, a następnie kliknij przycisk **Włącz**.

![Włączanie rozwiązania Update Management](./media/tutorial-monitoring/manage-updates-update-enable.png)

Włączanie rozwiązania może potrwać do 15 minut. W tym czasie nie powinno zamykać się okna przeglądarki. Po włączeniu rozwiązania informacje dotyczące brakujących aktualizacji maszyn wirtualnych z menedżera pakietów są przekazywane do usługi Log Analytics.
Udostępnienie danych do analizy może potrwać od 30 minut do 6 godzin.

### <a name="view-update-assessment"></a>Wyświetlanie oceny aktualizacji

Po włączeniu rozwiązania **Update Management** zostanie wyświetlony ekran **Update Management**. Możesz wyświetlić listę brakujących aktualizacji na karcie **Brakujące aktualizacje**.

![Wyświetlanie stanu aktualizacji](./media/tutorial-monitoring/manage-updates-view-status-linux.png)

### <a name="schedule-an-update-deployment"></a>Planowanie wdrożenia aktualizacji

Aby zainstalować aktualizacje, zaplanuj wdrożenie zgodnie z harmonogramem wydawania i oknem konserwacji.

Zaplanuj nowe wdrożenie aktualizacji dla maszyny wirtualnej, klikając pozycję **Zaplanuj wdrażanie aktualizacji** w górnej części ekranu **Update Management**. Na ekranie **Nowe wdrożenie aktualizacji** podaj następujące informacje:

* **Nazwa** — wprowadź unikatową nazwę identyfikującą wdrożenie aktualizacji.
* **Aktualizacje do wykluczenia** — wybierz tę opcję, aby wprowadzić nazwy pakietów do wykluczenia z aktualizacji.
* **Ustawienia harmonogramu** — możesz zaakceptować domyślną datę i godzinę (30 minut po bieżącej godzinie) lub określić inny czas. Możesz też określić, czy wdrożenie ma występować raz, czy zgodnie z ustawionym harmonogramem cyklicznym. Kliknij opcję Cyklicznie w obszarze Cykl, aby skonfigurować harmonogram cykliczny.

  ![Ekran ustawień harmonogramu aktualizacji](./media/tutorial-monitoring/manage-updates-schedule-linux.png)

* **Okno konserwacji (w minutach)** — podaj okres, w którym ma zostać przeprowadzone wdrażanie aktualizacji.  Pozwala to zagwarantować, że zmiany będą wprowadzane w ramach zdefiniowanych okien konserwacji. 

Po ukończeniu konfigurowania harmonogramu kliknij przycisk **Utwórz**, aby wrócić do pulpitu nawigacyjnego stanu.
Tabela **Zaplanowane** zawiera utworzony harmonogram wdrożenia.

> [!WARNING]
> Maszyna wirtualna zostanie automatycznie ponownie uruchomiona po zainstalowaniu aktualizacji, jeśli w oknie konserwacji zostało wystarczająco dużo czasu.

Rozwiązanie Update Management używa istniejącego menedżera pakietów w maszynie wirtualnej do instalowania pakietów.

### <a name="view-results-of-an-update-deployment"></a>Wyświetlanie wyników wdrażania aktualizacji

Po rozpoczęciu zaplanowanego wdrażania stan tego wdrożenia można sprawdzić na karcie **Wdrożenia aktualizacji** na ekranie **Update Management**.
Jeśli wdrażanie trwa, wdrożenie ma stan **W toku**. Po pomyślnym ukończeniu wdrażania stan zmienia się na **Powodzenie**.
W przypadku błędu co najmniej jednej aktualizacji w ramach wdrożenia jest wyświetlany stan **Niepowodzenie**.
Kliknij ukończone wdrożenie aktualizacji, aby wyświetlić pulpit nawigacyjny tego wdrożenia.

![Pulpit nawigacyjny stanu wdrożenia aktualizacji dla określonego wdrożenia](./media/tutorial-monitoring/manage-updates-view-results.png)

Kafelek **Wyniki aktualizacji** zawiera podsumowanie z łączną liczbą aktualizacji i wynikami wdrożenia na maszynie wirtualnej.
Tabela po prawej stronie zawiera szczegółowy podział każdej aktualizacji i wyniki instalacji, które mogą mieć jedną z następujących wartości:

* **Nie podjęto próby** — nie zainstalowano aktualizacji z powodu niewystarczającego czasu w zdefiniowanym oknie konserwacji.
* **Powodzenie** — aktualizacja została pomyślnie pobrana i zainstalowana na maszynie wirtualnej
* **Niepowodzenie** — nie można pobrać ani zainstalować aktualizacji na maszynie wirtualnej.

Kliknij pozycję **Wszystkie dzienniki**, aby wyświetlić wszystkie wpisy dziennika utworzone przez wdrożenie.

Kliknij kafelek **Dane wyjściowe**, aby wyświetlić strumień zadań elementu runbook odpowiedzialnego za zarządzanie wdrożeniem aktualizacji na docelowej maszynie wirtualnej.

Kliknij pozycję **Błędy**, aby wyświetlić szczegółowe informacje o błędach związanych z wdrożeniem.

## <a name="advanced-monitoring"></a>Zaawansowane monitorowanie 

Możesz przeprowadzić bardziej zaawansowane monitorowanie maszyny wirtualnej przy użyciu rozwiązania [Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview). Jeśli jeszcze tego nie zrobiono, możesz zarejestrować się w celu skorzystania z [bezpłatnej wersji próbnej](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite-trial) rozwiązania Operations Management Suite.

Po uzyskaniu dostępu do portalu OMS możesz znaleźć klucz oraz identyfikator obszaru roboczego w bloku Ustawienia. Zastąp fragmenty <workspace-key> i <workspace-id> wartościami dotyczącymi obszaru roboczego OMS, a następnie użyj polecenia **az vm extension set**, aby dodać rozszerzenie OMS do maszyny wirtualnej:

```azurecli-interactive 
az vm extension set \
  --resource-group myResourceGroupMonitor \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.3 \
  --protected-settings '{"workspaceKey": "<workspace-key>"}' \
  --settings '{"workspaceId": "<workspace-id>"}'
```

W bloku przeszukiwania dzienników w portalu OMS powinna być widoczna maszyna wirtualna *myVM*, tak jak przedstawiono na poniższej ilustracji:

![Blok OMS](./media/tutorial-monitoring/tutorial-monitor-oms.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku skonfigurowano i sprawdzono aktualizacje dla maszyny wirtualnej oraz zarządzano nimi. W tym samouczku omówiono:

> [!div class="checklist"]
> * Włączanie diagnostyki rozruchu na maszynie wirtualnej
> * Wyświetlanie diagnostyki rozruchu
> * Wyświetlanie metryki hosta
> * Włączanie rozszerzenia diagnostyki na maszynie wirtualnej
> * Wyświetlanie metryki maszyny wirtualnej
> * Tworzenie alertów w oparciu o metrykę diagnostyki
> * Zarządzanie aktualizacjami pakietów
> * Konfigurowanie zaawansowanego monitorowania

Przejdź do następnego samouczka, aby dowiedzieć się więcej na temat usługi Azure Security Center.

> [!div class="nextstepaction"]
> [Zarządzanie zabezpieczeniami maszyn wirtualnych](./tutorial-azure-security.md)
