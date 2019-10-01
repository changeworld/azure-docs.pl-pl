---
title: Samouczek — monitorowanie maszyn wirtualnych z systemem Linux na platformie Azure | Microsoft Docs
description: W tym samouczku dowiesz się, jak monitorować wydajność i wykryte składniki aplikacji uruchomione na maszynach wirtualnych z systemem Linux.
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
ms.openlocfilehash: 9c6458eea2b1352e7d13ea6691eac4498182ecd3
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71680078"
---
# <a name="tutorial-monitor-a-linux-virtual-machine-in-azure"></a>Samouczek: monitorowanie maszyny wirtualnej z systemem Linux na platformie Azure

Usługa Azure Monitoring używa agentów do zbierania danych dotyczących rozruchu i wydajności z maszyn wirtualnych platformy Azure, przechowywania tych danych w usłudze Azure Storage i udostępniania ich za pośrednictwem portalu, modułu Azure PowerShell i interfejsu wiersza polecenia platformy Azure. Zaawansowane monitorowanie jest dostarczane z Azure Monitor dla maszyn wirtualnych przez zbieranie metryk wydajności, odnajdywanie składników aplikacji zainstalowanych na maszynie wirtualnej i zawiera wykresy wydajności i mapę zależności.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Włącz diagnostykę rozruchu na maszynie wirtualnej
> * Wyświetl diagnostykę rozruchu
> * Wyświetl metryki hosta maszyny wirtualnej
> * Włącz Azure Monitor dla maszyn wirtualnych
> * Wyświetl metryki wydajności maszyny wirtualnej
> * Tworzenie alertu

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell uruchamiania

Azure Cloud Shell to bezpłatna interaktywna powłoka, za pomocą której można wykonać kroki opisane w tym artykule. Ma ona wspólne narzędzia platformy Azure preinstalowane i skonfigurowane do użycia z Twoim kontem. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.30 lub nowszej. Uruchom `az --version`, aby znaleźć wersję. Jeśli konieczne jest zainstalowanie lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-vm"></a>Tworzenie maszyny wirtualnej

Aby wyświetlić diagnostykę i metryki w działaniu, potrzebna jest maszyna wirtualna. Najpierw utwórz grupę zasobów za pomocą polecenie [AZ Group Create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create). Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroupMonitor* w lokalizacji *Wschodnie* .

```azurecli-interactive
az group create --name myResourceGroupMonitor --location eastus
```

Teraz Utwórz maszynę wirtualną za pomocą [AZ VM Create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create). Poniższy przykład tworzy maszynę wirtualną o nazwie *myVM* i GENERUJE klucze SSH, jeśli jeszcze nie istnieją w *~/.SSH/* :

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="enable-boot-diagnostics"></a>Włącz diagnostykę rozruchu

W przypadku rozruchu maszyn wirtualnych z systemem Linux rozszerzenie diagnostyki rozruchu przechwytuje dane wyjściowe rozruchu i przechowuje je w usłudze Azure Storage. Te dane mogą służyć do rozwiązywania problemów z rozruchem maszyn wirtualnych. Diagnostyka rozruchu nie jest włączana automatycznie podczas tworzenia maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure.

Przed włączeniem diagnostyki rozruchu należy utworzyć konto magazynu do przechowywania dzienników rozruchowych. Konta magazynu muszą mieć globalnie unikatową nazwę o długości od 3 do 24 znaków i mogą zawierać tylko cyfry i małe litery. Utwórz konto magazynu za pomocą polecenia [AZ Storage account Create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) . W tym przykładzie losowy ciąg służy do tworzenia unikatowej nazwy konta magazynu.

```azurecli-interactive
storageacct=mydiagdata$RANDOM

az storage account create \
  --resource-group myResourceGroupMonitor \
  --name $storageacct \
  --sku Standard_LRS \
  --location eastus
```

Podczas włączania diagnostyki rozruchu wymagany jest identyfikator URI kontenera magazynu obiektów BLOB. Następujące polecenie wysyła zapytanie do konta magazynu w celu zwrócenia tego identyfikatora URI. Wartość identyfikatora URI jest przechowywana w nazwach zmiennych *bloburi*, które są używane w następnym kroku.

```azurecli-interactive
bloburi=$(az storage account show --resource-group myResourceGroupMonitor --name $storageacct --query 'primaryEndpoints.blob' -o tsv)
```

Teraz Włącz diagnostykę rozruchu przy użyciu [AZ VM Boot-Diagnostics Enable](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az-vm-boot-diagnostics-enable). Wartość `--storage` to identyfikator URI obiektu BLOB zebrany w poprzednim kroku.

```azurecli-interactive
az vm boot-diagnostics enable \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --storage $bloburi
```

## <a name="view-boot-diagnostics"></a>Wyświetl diagnostykę rozruchu

Gdy Diagnostyka rozruchu jest włączona, za każdym razem, gdy zatrzymasz i uruchomisz maszynę wirtualną, informacje o procesie rozruchu są zapisywane w pliku dziennika. W tym przykładzie najpierw Cofnij przydział maszyny wirtualnej za pomocą polecenia [AZ VM deallocate](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-deallocate) w następujący sposób:

```azurecli-interactive
az vm deallocate --resource-group myResourceGroupMonitor --name myVM
```

Teraz uruchom maszynę wirtualną za pomocą polecenia [AZ VM Start](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-start) w następujący sposób:

```azurecli-interactive
az vm start --resource-group myResourceGroupMonitor --name myVM
```

Dane diagnostyczne rozruchu dla *myVM* można uzyskać za pomocą polecenia [AZ VM Boot-Diagnostics Get-Boot-Log](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az-vm-boot-diagnostics-get-boot-log) w następujący sposób:

```azurecli-interactive
az vm boot-diagnostics get-boot-log --resource-group myResourceGroupMonitor --name myVM
```

## <a name="view-host-metrics"></a>Wyświetl metryki hosta

Maszyna wirtualna z systemem Linux ma dedykowanego hosta na platformie Azure, z którym współpracuje. Metryki są automatycznie zbierane dla hosta i mogą być wyświetlane w Azure Portal w następujący sposób:

1. W Azure Portal wybierz pozycję **grupy zasobów**, wybierz pozycję **myResourceGroupMonitor**, a następnie wybierz pozycję **myVM** na liście zasobów.
1. Aby zobaczyć, jak działa maszyna wirtualna hosta, wybierz pozycję **metryki** w oknie maszyny wirtualnej, a następnie wybierz dowolne metryki *[Host]* w obszarze **Dostępne metryki**.

    ![Wyświetl metryki hosta](./media/tutorial-monitoring/monitor-host-metrics.png)

## <a name="enable-advanced-monitoring"></a>Włącz monitorowanie zaawansowane

Aby włączyć monitorowanie maszyny wirtualnej platformy Azure przy użyciu Azure Monitor dla maszyn wirtualnych:

1. W Azure Portal kliknij pozycję **grupy zasobów**, wybierz pozycję **myResourceGroupMonitor**, a następnie wybierz pozycję **myVM** na liście zasobów.

2. Na stronie maszyna wirtualna w sekcji **monitorowanie** wybierz pozycję szczegółowe dane **(wersja zapoznawcza)** .

3. Na stronie **Insights (wersja zapoznawcza)** wybierz pozycję **Wypróbuj teraz**.

    ![Włączanie Azure Monitor dla maszyn wirtualnych dla maszyny wirtualnej](../../azure-monitor/insights/media/vminsights-enable-single-vm/enable-vminsights-vm-portal-01.png)

4. Na stronie Dołączanie do usługi **Azure monitor Insights** , jeśli masz istniejący obszar roboczy log Analytics w tej samej subskrypcji, wybierz go na liście rozwijanej.  

    Na tej liście jest wybierany domyślny obszar roboczy i lokalizacja, w której wdrożono maszynę wirtualną w ramach subskrypcji. 

    >[!NOTE]
    >Aby utworzyć nowy obszar roboczy Log Analytics do przechowywania danych monitorowania z maszyny wirtualnej, zobacz [tworzenie log Analytics obszaru roboczego](../../azure-monitor/learn/quick-create-workspace.md). Obszar roboczy Log Analytics musi należeć do jednego z [obsługiwanych regionów](../../azure-monitor/insights/vminsights-enable-overview.md#log-analytics).

Po włączeniu monitorowania może być konieczne odczekanie kilku minut, zanim będzie można wyświetlić metryki wydajności dla maszyny wirtualnej.

![Włącz przetwarzanie wdrożenia monitorowania Azure Monitor dla maszyn wirtualnych](../../azure-monitor/insights/media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="view-vm-performance-metrics"></a>Wyświetl metryki wydajności maszyny wirtualnej

Azure Monitor dla maszyn wirtualnych obejmuje zestaw wykresów wydajności przeznaczonych dla kilku kluczowych wskaźników wydajności (KPI), aby pomóc w określeniu, jak dobrze działa maszyna wirtualna. Aby uzyskać dostęp do maszyny wirtualnej, wykonaj następujące czynności.

1. W Azure Portal kliknij pozycję **grupy zasobów**, wybierz pozycję **myResourceGroupMonitor**, a następnie wybierz pozycję **myVM** na liście zasobów.

2. Na stronie maszyna wirtualna w sekcji **monitorowanie** wybierz pozycję szczegółowe dane **(wersja zapoznawcza)** .

3. Wybierz kartę **wydajność** .

Ta strona zawiera nie tylko wykresy wykorzystania wydajności, ale również tabelę zawierającą dla każdego wykrytego dysku logicznego, jego pojemność, wykorzystanie i łączną średnią wartość na podstawie każdej miary.

## <a name="create-alerts"></a>Tworzenie alertów

Możesz tworzyć alerty na podstawie określonych metryk wydajności. Przy użyciu alertów można powiadamiać, gdy średnie użycie procesora CPU przekroczy określony próg lub ilość wolnego miejsca na dysku spadnie poniżej określonej ilości, na przykład. Alerty są wyświetlane w Azure Portal lub mogą być wysyłane za pośrednictwem poczty e-mail. Możesz również wyzwolić Azure Automation elementy Runbook lub Azure Logic Apps w odpowiedzi na generowane alerty.

Poniższy przykład tworzy alert dla średniego użycia procesora CPU.

1. W Azure Portal kliknij pozycję **grupy zasobów**, wybierz pozycję **myResourceGroupMonitor**, a następnie wybierz pozycję **myVM** na liście zasobów.

2. Kliknij pozycję **reguły alertów** w bloku maszyny wirtualnej, a następnie kliknij pozycję **Dodaj alert metryki** w górnej części bloku alerty.

3. Podaj **nazwę** alertu, na przykład *myAlertRule*

4. Aby wyzwolić alert, gdy wartość procentowa procesora CPU przekracza 1,0 przez pięć minut, pozostaw wszystkie inne ustawienia domyślne.

5. Opcjonalnie zaznacz pole wyboru *właściciele, współautorzy i czytelnicy poczty e-mail* do wysyłania powiadomień e-mail. Domyślną akcją jest zaprezentowanie powiadomienia w portalu.

6. Kliknij przycisk **OK** .

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono Konfigurowanie i wyświetlanie wydajności maszyny wirtualnej. Wiesz już, jak:

> [!div class="checklist"]
> * Utwórz grupę zasobów i maszynę wirtualną
> * Włącz diagnostykę rozruchu na maszynie wirtualnej
> * Wyświetl diagnostykę rozruchu
> * Wyświetl metryki hosta
> * Włącz Azure Monitor dla maszyn wirtualnych
> * Wyświetl metryki maszyny wirtualnej
> * Tworzenie alertu

Przejdź do następnego samouczka, aby dowiedzieć się więcej o Azure Security Center.

> [!div class="nextstepaction"]
> [Zarządzanie zabezpieczeniami maszyn wirtualnych](../../security/fundamentals/overview.md)
