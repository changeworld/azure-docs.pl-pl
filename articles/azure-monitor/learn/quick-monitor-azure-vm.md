---
title: Monitorowanie zasobu platformy Azure za pomocą usługi Azure Monitor
description: Dowiedz się, jak zbierać i analizować dane dla zasobu platformy Azure w usłudze Azure Monitor.
ms.service: azure-monitor
ms. subservice: logs
ms.topic: quickstart
author: bwren
ms.author: bwren
ms.date: 03/10/2020
ms.openlocfilehash: 12616fbc80468d35ebb8b0bc5cd12bfd5871e788
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79503657"
---
# <a name="quickstart-monitor-an-azure-virtual-machine-with-azure-monitor"></a>Szybki start: monitorowanie maszyny wirtualnej platformy Azure za pomocą usługi Azure Monitor
[Usługa Azure Monitor](../overview.md) rozpoczyna zbieranie danych z maszyn wirtualnych platformy Azure w momencie ich utworzenia. W tym przewodniku Szybki start omówisz krótki przewodnik po danych, które są automatycznie zbierane dla maszyny Wirtualnej platformy Azure i jak ją wyświetlić w witrynie Azure portal. Następnie włączysz [usługę Azure Monitor dla maszyn wirtualnych](../insights/vminsights-overview.md) dla maszyny wirtualnej, która umożliwi agentom na maszynie wirtualnej zbieranie i analizowanie danych z systemu operacyjnego gościa, w tym procesów i ich zależności.

W tym przewodniku Szybki start przyjęto założenie, że masz już maszynę wirtualną platformy Azure. Jeśli nie, możesz utworzyć [maszynę wirtualną z systemem Windows](../../virtual-machines/windows/quick-create-portal.md) lub utworzyć [maszynę wirtualną z systemem Linux](../../virtual-machines/linux/quick-create-cli.md) po naszych szybkich startach maszyn wirtualnych.

Aby uzyskać bardziej szczegółowe opisy danych monitorowania zebranych z zasobów platformy Azure, zobacz [Monitorowanie maszyn wirtualnych platformy Azure za pomocą usługi Azure Monitor.](../insights/monitor-vm-azure.md)


## <a name="complete-the-monitor-an-azure-resource-quickstart"></a>Ukończ przewodnik szybki start zasobów monitoruj za pomocą platformy Azure.
Ukończ [zasób platformy Azure za pomocą usługi Azure Monitor,](quick-monitor-azure-resource.md) aby wyświetlić stronę przeglądu, dziennik aktywności i metryki maszyny Wirtualnej w ramach subskrypcji. Maszyny wirtualne platformy Azure zbierają te same dane monitorowania, co każdy inny zasób platformy Azure, ale dotyczy to tylko maszyny Wirtualnej hosta. Pozostała część tego przewodnika Szybki start skupi się na monitorowaniu systemu operacyjnego gościa i jego obciążeń.


## <a name="enable-azure-monitor-for-vms"></a>Włączanie usługi Azure Monitor dla maszyn wirtualnych
Podczas gdy metryki i dzienniki aktywności będą zbierane dla maszyny Wirtualnej hosta, potrzebujesz agenta i konfiguracji do zbierania i analizowania danych monitorowania z systemu operacyjnego gościa i jego obciążeń. Usługa Azure Monitor dla maszyn wirtualnych instaluje tych agentów i zapewnia dodatkowe zaawansowane funkcje do monitorowania maszyn wirtualnych.

1. Przejdź do menu maszyny wirtualnej.
2. Kliknij pozycję **Przejdź do aplikacji Insights** na kafelku na stronie **Przegląd** lub kliknij **pozycję Insights** z menu **Monitorowanie.**

    ![Strona przeglądowa](media/quick-monitor-azure-vm/overview-insights.png)

3. Jeśli usługa Azure Monitor dla maszyn wirtualnych nie została jeszcze włączona dla maszyny wirtualnej, kliknij przycisk **Włącz**. 

    ![Włączanie szczegółowych informacji](media/quick-monitor-azure-vm/enable-insights.png)

4. Jeśli maszyna wirtualna nie jest jeszcze dołączony do obszaru roboczego usługi Log Analytics, zostanie wyświetlony monit, aby wybrać istniejący obszar roboczy lub utworzyć nowy. Wybierz wartość domyślną, która jest obszarem roboczym o unikatowej nazwie w tym samym regionie co maszyna wirtualna.

    ![Wybór obszaru roboczego](media/quick-monitor-azure-vm/select-workspace.png)

5. Dołączanie potrwa kilka minut, ponieważ rozszerzenia są włączone i agenci są instalowani na maszynie wirtualnej. Po jego zakończeniu zostanie wyświetlony komunikat, że szczegółowe informacje zostały pomyślnie wdrożone. Kliknij **pozycję Azure Monitor,** aby otworzyć usługę Azure Monitor dla maszyn wirtualnych.

    ![Otwórz monitor platformy Azure](media/quick-monitor-azure-vm/azure-monitor.png)

6. Zobaczysz maszynę wirtualną z innymi maszynami wirtualnymi w ramach subskrypcji, które są wbudowane. Wybierz **kartę Niemonitorowane,** jeśli chcesz wyświetlić maszyny wirtualne w ramach subskrypcji, które nie są dołączane.

    ![Wprowadzenie](media/quick-monitor-azure-vm/get-started.png)


## <a name="configure-workspace"></a>Konfigurowanie obszaru roboczego
Podczas tworzenia nowego obszaru roboczego usługi Log Analytics, musi być skonfigurowany do zbierania dzienników. Ta konfiguracja musi być wykonana tylko raz, ponieważ konfiguracja jest wysyłana do wszystkich maszyn wirtualnych, które się z nią łączą.

1. Wybierz **pozycję Konfiguracja obszaru roboczego,** a następnie wybierz obszar roboczy.

2. Wybieranie **ustawień zaawansowanych**

    ![Ustawienia zaawansowane usługi Log Analytics](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png)

### <a name="data-collection-from-windows-vm"></a>Zbieranie danych z maszyny wirtualnej z systemem Windows


2. Wybierz pozycję **Dane**, a następnie pozycję **Dzienniki zdarzeń systemu Windows**.

3. Dodaj dziennik zdarzeń, wpisując nazwę dziennika.  Wpisz **system,** a następnie **+** wybierz znak plus .

4. W tabeli zaznacz ważności **Błąd** i **Ostrzeżenie**.

5. Wybierz **pozycję Zapisz** u góry strony, aby zapisać konfigurację.

### <a name="data-collection-from-linux-vm"></a>Zbieranie danych z maszyny wirtualnej z systemem Linux

1. Wybierz dziennik **Syslog**.  

2. Dodaj dziennik zdarzeń, wpisując nazwę dziennika.  Wpisz **Syslog,** a następnie **+** wybierz znak plus .  

3. W tabeli usuń zaznaczenie ważności **Informacje**, **Uwaga** i **Debugowanie**. 

4. Wybierz **pozycję Zapisz** u góry strony, aby zapisać konfigurację.

## <a name="view-data-collected"></a>Wyświetlanie zebranych danych

7. Kliknij maszynę wirtualną, a następnie wybierz kartę **Wydajność.** Spowoduje to wyświetlenie wybranej grupy liczników wydajności zebranych z systemu operacyjnego gościa maszyny Wirtualnej. Przewiń w dół, aby wyświetlić więcej liczników, i przesuń kursor myszy na wykres, aby wyświetlić średnie i percentyle w różnym czasie.

    ![Wydajność](media/quick-monitor-azure-vm/performance.png)

9. Wybierz **mapę,** aby otworzyć funkcję map, która pokazuje procesy uruchomione na maszynie wirtualnej i ich zależności. Wybierz **właściwości,** aby otworzyć okienko właściwości, jeśli nie jest jeszcze otwarte.

    ![Mapa](media/quick-monitor-azure-vm/map.png)

11. Rozwiń procesy dla maszyny wirtualnej. Wybierz jeden z procesów, aby wyświetlić jego szczegóły i wyróżnić jego zależności.

    ![Procesy](media/quick-monitor-azure-vm/processes.png)

12. Ponownie wybierz maszynę wirtualną, a następnie wybierz pozycję **Zaloguj zdarzenia**. 

    ![Rejestrowanie zdarzeń](media/quick-monitor-azure-vm/log-events.png)

13. Widoczna jest lista tabel przechowywanych w obszarze roboczym usługi Log Analytics dla maszyny wirtualnej. Ta lista będzie się różnić w zależności od tego, czy używasz maszyny wirtualnej systemu Windows lub Linux. Kliknij tabelę **Zdarzenie.** Obejmuje to wszystkie zdarzenia z dziennika zdarzeń systemu Windows. Usługa Log Analytics zostanie otwarta z prostą kwerendą do pobierania wpisów dziennika zdarzeń.

    ![Analiza dziennika](media/quick-monitor-azure-vm/log-analytics.png)

## <a name="next-steps"></a>Następne kroki
W tym przewodniku Szybki start włączono usługę Azure Monitor dla maszyn wirtualnych dla maszyny wirtualnej i skonfigurowano obszar roboczy usługi Log Analytics do zbierania zdarzeń dla systemu operacyjnego gościa. Aby dowiedzieć się, jak wyświetlać i analizować dane, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Wyświetlanie i analizowanie danych w usłudze Log Analytics](../../azure-monitor/learn/tutorial-viewdata.md)
