---
title: Samouczek — monitorowanie maszyn wirtualnych systemu Linux na platformie Azure
description: W tym samouczku dowiesz się, jak monitorować wydajność i odnalezione składniki aplikacji uruchomione na maszynach wirtualnych systemu Linux.
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
ms.date: 09/30/2019
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: b06342d5034b820be4e6fd49436546a5aa7b7e02
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75749781"
---
# <a name="tutorial-monitor-a-linux-virtual-machine-in-azure"></a>Samouczek: Monitorowanie maszyny wirtualnej systemu Linux na platformie Azure

Monitorowanie platformy Azure używa agentów do zbierania danych rozruchowych i wydajności z maszyn wirtualnych platformy Azure, przechowywania tych danych w magazynie platformy Azure i udostępnienia ich za pośrednictwem portalu, modułu Azure PowerShell i interfejsu wiersza polecenia platformy Azure. Zaawansowane monitorowanie jest dostarczane z usługą Azure Monitor dla maszyn wirtualnych przez zbieranie metryk wydajności, odnajdowanie składników aplikacji zainstalowanych na maszynie wirtualnej i obejmuje wykresy wydajności i mapę zależności.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Włączanie diagnostyki rozruchu na maszynie wirtualnej
> * Wyświetlanie diagnostyki rozruchu
> * Wyświetlanie metryki hosta maszyny wirtualnej
> * Włączanie usługi Azure Monitor dla maszyn wirtualnych
> * Wyświetlanie metryk wydajności maszyny Wirtualnej
> * Tworzenie alertu

## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. 

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Możesz również uruchomić usługę Cloud Shell w [https://shell.azure.com/powershell](https://shell.azure.com/powershell)osobnej karcie przeglądarki, przechodząc do . Wybierz przycisk **Kopiuj**, aby skopiować bloki kodu, wklej je do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby je uruchomić.

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.30 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-vm"></a>Tworzenie maszyny wirtualnej

Aby zobaczyć diagnostykę i metryki w akcji, potrzebujesz maszyny wirtualnej. Najpierw utwórz grupę zasobów za pomocą polecenia [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create). Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroupMonitor* w lokalizacji *eastus*.

```azurecli-interactive
az group create --name myResourceGroupMonitor --location eastus
```

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create). Następujący przykład umożliwia utworzenie maszyny wirtualnej o nazwie *myVM* i wygenerowanie kluczy SSH, jeśli jeszcze nie istnieją w folderze *~/.ssh/*:

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

Przed włączeniem diagnostyki rozruchu należy utworzyć konto magazynu do przechowywania dzienników rozruchu. Konta magazynu muszą mieć globalnie unikatowe nazwy o długości od 3 do 24 znaków, które zawierają wyłącznie cyfry i małe litery. Utwórz konto magazynu przy użyciu polecenia [az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create). W tym przykładzie losowy ciąg jest używany do utworzenia unikatowej nazwy konta magazynu.

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

Jeśli diagnostyka rozruchu jest włączona, za każdym razem w przypadku zatrzymania i uruchomienia maszyny wirtualnej informacje o procesie rozruchu zostaną zapisane w pliku dziennika. W tym przykładzie najpierw cofnij przydział maszyny wirtualnej przy użyciu polecenia [az vm deallocate](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-deallocate) w następujący sposób:

```azurecli-interactive
az vm deallocate --resource-group myResourceGroupMonitor --name myVM
```

Teraz uruchom maszynę wirtualną przy użyciu polecenia [az vm start](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-start) w następujący sposób:

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

## <a name="enable-advanced-monitoring"></a>Włącz zaawansowane monitorowanie

Aby włączyć monitorowanie maszyny wirtualnej platformy Azure za pomocą usługi Azure Monitor dla maszyn wirtualnych:

1. W witrynie Azure Portal kliknij pozycję **Grupy zasobów**, wybierz grupę **myResourceGroupMonitor**, a następnie wybierz maszynę wirtualną **myVM** na liście zasobów.

2. Na stronie Maszyny Wirtualnej w sekcji **Monitorowanie** wybierz pozycję **Insights (preview)**.

3. Na stronie **Statystyki (wersja zapoznawcza)** wybierz pozycję **Wypróbuj teraz**.

    ![Włączanie usługi Azure Monitor dla maszyn wirtualnych](../../azure-monitor/insights/media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

4. Na stronie **Dołączania usługi Azure Monitor Insights,** jeśli masz istniejący obszar roboczy usługi Log Analytics w tej samej subskrypcji, wybierz go na liście rozwijanej.  

    Lista wstępnieselektywa domyślnego obszaru roboczego i lokalizacji, w której maszyna wirtualna jest wdrażana w ramach subskrypcji. 

    >[!NOTE]
    >Aby utworzyć nowy obszar roboczy usługi Log Analytics do przechowywania danych monitorowania z maszyny Wirtualnej, zobacz [Tworzenie obszaru roboczego usługi Log Analytics](../../azure-monitor/learn/quick-create-workspace.md). Obszar roboczy usługi Log Analytics musi należeć do jednego z [obsługiwanych regionów.](../../azure-monitor/insights/vminsights-enable-overview.md#log-analytics)

Po włączeniu monitorowania może być konieczne odczekanie kilku minut, zanim będzie można wyświetlić metryki wydajności maszyny Wirtualnej.

![Włącz usługę Azure Monitor dla przetwarzania wdrażania maszyn wirtualnych](../../azure-monitor/insights/media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="view-vm-performance-metrics"></a>Wyświetlanie metryk wydajności maszyny Wirtualnej

Usługa Azure Monitor dla maszyn wirtualnych zawiera zestaw wykresów wydajności, które są przeznaczone dla kilku kluczowych wskaźników wydajności (KPI), aby ułatwić określenie, jak dobrze działa maszyna wirtualna. Aby uzyskać dostęp z maszyny Wirtualnej, wykonaj następujące kroki.

1. W witrynie Azure Portal kliknij pozycję **Grupy zasobów**, wybierz grupę **myResourceGroupMonitor**, a następnie wybierz maszynę wirtualną **myVM** na liście zasobów.

2. Na stronie Maszyny Wirtualnej w sekcji **Monitorowanie** wybierz pozycję **Insights (preview)**.

3. Wybierz kartę **Wydajność.**

Ta strona zawiera nie tylko wykresy wykorzystania wydajności, ale także tabelę przedstawiającą dla każdego wykrytego dysku logicznego, jego pojemność, wykorzystanie i średnią całkowitą według każdej miary.

## <a name="create-alerts"></a>Tworzenie alertów

Możesz utworzyć alerty w oparciu o konkretne metryki wydajności. Przykładowo alertów można używać do wysyłania powiadomień w przypadku przekroczenia wybranego progu użycia procesora lub spadku dostępnego miejsca na dysku poniżej wybranej wartości. Alerty mogą być wyświetlane w witrynie Azure Portal lub wysyłane za pośrednictwem poczty e-mail. Możesz też wyzwolić elementy runbook usługi Azure Automation lub aplikacje usługi Azure Logic Apps w ramach reakcji na wygenerowane alerty.

Poniższy przykład tworzy alert dotyczący średniego użycia procesora.

1. W witrynie Azure Portal kliknij pozycję **Grupy zasobów**, wybierz grupę **myResourceGroupMonitor**, a następnie wybierz maszynę wirtualną **myVM** na liście zasobów.

2. Kliknij pozycję **Reguły alertów** w bloku maszyny wirtualnej, a następnie kliknij pozycję **Dodaj alert metryki** w górnej części bloku alertów.

3. Podaj **nazwę** alertu, np. *myAlertRule*

4. Aby wyzwolić alert, gdy procent użycia procesora przekracza 1,0 przez pięć minut, pozostaw wybrane wszystkie inne wartości domyślne.

5. Opcjonalnie zaznacz pole pozycji *Wyślij wiadomość e-mail do właścicieli, współautorów i czytelników*, aby wysłać powiadomienie w wiadomości e-mail. Domyślne działanie to prezentowanie powiadomienia w portalu.

6. Kliknij przycisk **OK.**

## <a name="next-steps"></a>Następne kroki

W tym samouczku skonfigurowano i wyświetlano wydajność maszyny Wirtualnej. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie grupy zasobów i maszyny wirtualnej
> * Włączanie diagnostyki rozruchu na maszynie wirtualnej
> * Wyświetlanie diagnostyki rozruchu
> * Wyświetlanie metryki hosta
> * Włączanie usługi Azure Monitor dla maszyn wirtualnych
> * Wyświetlanie metryki maszyny wirtualnej
> * Tworzenie alertu

Przejdź do następnego samouczka, aby dowiedzieć się więcej na temat usługi Azure Security Center.

> [!div class="nextstepaction"]
> [Zarządzanie zabezpieczeniami maszyn wirtualnych](../../security/fundamentals/overview.md)
