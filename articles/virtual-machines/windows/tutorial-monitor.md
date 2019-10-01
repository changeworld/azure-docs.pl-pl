---
title: Samouczek — monitorowanie maszyn wirtualnych z systemem Windows na platformie Azure | Microsoft Docs
description: W tym samouczku dowiesz się, jak monitorować wydajność i wykryte składniki aplikacji uruchomione na maszynach wirtualnych z systemem Windows.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/27/2018
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: a2f4083841c801db3edf1b2838b8d3271b700731
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71680064"
---
# <a name="tutorial-monitor-a-windows-virtual-machine-in-azure"></a>Samouczek: monitorowanie maszyny wirtualnej z systemem Windows na platformie Azure

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

Aby otworzyć Cloud Shell, po prostu wybierz opcję **Wypróbuj** w prawym górnym rogu bloku kodu. Cloud Shell można również uruchomić na oddzielnej karcie przeglądarki, przechodząc do [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Wybierz pozycję **Kopiuj** , aby skopiować bloki kodu, wkleić je do Cloud Shell i naciśnij klawisz ENTER, aby go uruchomić.

## <a name="create-virtual-machine"></a>Utwórz maszynę wirtualną

Aby skonfigurować platformę Azure do monitorowania i aktualizacji w tym samouczku, potrzebna jest maszyna wirtualna z systemem Windows na platformie Azure. Najpierw Ustaw nazwę użytkownika i hasło administratora dla maszyny wirtualnej przy użyciu [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Teraz Utwórz maszynę wirtualną za pomocą [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). Poniższy przykład tworzy maszynę wirtualną o nazwie *myVM* w lokalizacji *Wschodnie* . Jeśli jeszcze nie istnieją, Grupa zasobów *myResourceGroupMonitorMonitor* i pomocnicze zasoby sieciowe są tworzone:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupMonitor" `
    -Name "myVM" `
    -Location "East US" `
    -Credential $cred
```

Utworzenie zasobów i maszyny wirtualnej może potrwać kilka minut.

## <a name="view-boot-diagnostics"></a>Wyświetl diagnostykę rozruchu

W miarę uruchamiania maszyn wirtualnych z systemem Windows, Agent diagnostyki rozruchu przechwytuje dane wyjściowe ekranu, których można użyć do rozwiązywania problemów. Ta funkcja jest domyślnie włączona. Przechwycone zrzuty ekranu są przechowywane na koncie usługi Azure Storage, które jest również tworzone domyślnie.

Rozruch danych diagnostycznych można uzyskać za pomocą polecenia [Get-AzureRmVMBootDiagnosticsData](https://docs.microsoft.com/powershell/module/az.compute/get-azvmbootdiagnosticsdata) . W poniższym przykładzie Diagnostyka rozruchu jest pobierana do katalogu głównego dysku * c: \*.

```powershell
Get-AzVMBootDiagnosticsData -ResourceGroupName "myResourceGroupMonitor" -Name "myVM" -Windows -LocalPath "c:\"
```

## <a name="view-host-metrics"></a>Wyświetl metryki hosta

Maszyna wirtualna z systemem Windows ma dedykowaną maszynę wirtualną hosta na platformie Azure, z którą współpracuje. Metryki są automatycznie zbierane dla hosta i mogą być wyświetlane w Azure Portal.

1. W Azure Portal kliknij pozycję **grupy zasobów**, wybierz pozycję **myResourceGroupMonitor**, a następnie wybierz pozycję **myVM** na liście zasobów.
2. Kliknij pozycję **metryki** w bloku maszyna wirtualna, a następnie wybierz dowolną metrykę hosta w obszarze **Dostępne metryki** , aby zobaczyć, jak działa maszyna wirtualna hosta.

    ![Wyświetl metryki hosta](./media/tutorial-monitoring/tutorial-monitor-host-metrics.png)

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
