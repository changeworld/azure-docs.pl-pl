---
title: Samouczek — monitorowanie maszyn wirtualnych systemu Windows na platformie Azure
description: W tym samouczku dowiesz się, jak monitorować wydajność i odnalezione składniki aplikacji uruchomione na maszynach wirtualnych systemu Windows.
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
ms.openlocfilehash: 9e807927a10399b02c2c89359c2ffffaf87ba57b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74112491"
---
# <a name="tutorial-monitor-a-windows-virtual-machine-in-azure"></a>Samouczek: Monitorowanie maszyny wirtualnej systemu Windows na platformie Azure

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

## <a name="view-boot-diagnostics"></a>Wyświetlanie diagnostyki rozruchu

Podczas rozruchu maszyn wirtualnych z systemem Windows agent diagnostyki rozruchu przechwytuje dane wyjściowe z ekranu, których można używać do rozwiązywania problemów. Ta funkcja jest domyślnie włączona. Przechwycone zrzuty ekranu są przechowywane na koncie magazynu platformy Azure, które jest również tworzone domyślnie.

Dane diagnostyczne rozruchu można pobrać za pomocą polecenia [Get-AzureRmVMBootDiagnosticsData](https://docs.microsoft.com/powershell/module/az.compute/get-azvmbootdiagnosticsdata). W poniższym przykładzie dane diagnostyki rozruchu są pobierane do katalogu głównego na dysku *c:\*.

```powershell
Get-AzVMBootDiagnosticsData -ResourceGroupName "myResourceGroupMonitor" -Name "myVM" -Windows -LocalPath "c:\"
```

## <a name="view-host-metrics"></a>Wyświetlanie metryki hosta

Maszyna wirtualna z systemem Windows ma na platformie Azure dedykowaną maszynę wirtualną hosta, z którą wchodzi w interakcję. Metryki są automatycznie zbierane dla hosta. Można je wyświetlić w witrynie Azure Portal.

1. W witrynie Azure Portal kliknij pozycję **Grupy zasobów**, wybierz grupę **myResourceGroupMonitor**, a następnie wybierz maszynę wirtualną **myVM** na liście zasobów.
2. Aby zobaczyć, jak działa maszyna wirtualna hosta, kliknij pozycję **Metryki** w bloku maszyny wirtualnej, a następnie wybierz dowolną metrykę Host w obszarze **Dostępne metryki**.

    ![Wyświetlanie metryki hosta](./media/tutorial-monitoring/tutorial-monitor-host-metrics.png)

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
