---
title: Maszyna wirtualna aktualizacji i zarządzanie za pomocą usługi Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać usługi Azure Monitor dla maszyn wirtualnych, zarządzanie aktualizacjami, śledzenie zmian i rozwiązania magazynu w usłudze Azure Automation do zarządzania Windows i maszyn wirtualnych systemu Linux, które są wdrożone w usłudze Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: jeffgilb
ms.reviewer: rtiberiu
ms.lastreviewed: 03/20/2019
ms.openlocfilehash: cb8258c0f837d0e70ba87a26246f055b0efe5c00
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316153"
---
# <a name="azure-stack-vm-update-and-management"></a>Usługa Azure Stack VM update i zarządzanie
Następujące funkcje rozwiązania usługi Azure Automation służy do zarządzania Windows i maszyn wirtualnych systemu Linux, które są wdrażane przy użyciu usługi Azure Stack:

- **[Zarządzanie aktualizacjami](https://docs.microsoft.com/azure/automation/automation-update-management)**. Rozwiązanie Update Management możesz szybko ocenić stan dostępnych aktualizacji na wszystkich komputerach z agentami i zarządzanie procesem instalacji wymaganych aktualizacji dla tych maszyn wirtualnych systemu Linux i Windows.

- **[Śledzenie zmian](https://docs.microsoft.com/azure/automation/automation-change-tracking)**. Zmiany zainstalowanego oprogramowania, usług Windows, plików i rejestru Windows i demonów systemu Linux na monitorowanych serwerach są wysyłane do usługi Azure Monitor w chmurze do przetwarzania. Logika jest stosowana do odebranych danych i usługi w chmurze rejestruje dane. Korzystając z informacji podanych na pulpicie nawigacyjnym śledzenia zmian, łatwo widać zmiany wprowadzone w ramach infrastruktury serwera.

- **[Spis](https://docs.microsoft.com/azure/automation/automation-vm-inventory)**. Spis śledzenia dla maszyny wirtualnej usługi Azure Stack zapewnia interfejs użytkownika oparty na przeglądarce do instalowania i konfigurowania zbierania spisu.

- **[Usługa Azure Monitor dla maszyn wirtualnych](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview)**. Usługa Azure Monitor dla maszyn wirtualnych monitoruje maszynach wirtualnych platformy Azure i usługi Azure Stack (VM) i zestawy skalowania maszyn wirtualnych na dużą skalę. Analizuje wydajności i kondycji maszyn wirtualnych systemu Linux i Windows i monitoruje ich procesy i zależności dla innych zasobów i procesów zewnętrznych. 

> [!IMPORTANT]
> Te rozwiązania są takie same jak te używane do zarządzania maszynami wirtualnymi platformy Azure. Maszyny wirtualne platformy Azure Stack i Azure odbywa się w taki sam sposób, przy użyciu tego samego interfejsu, przy użyciu tych samych narzędzi. Maszyny wirtualne Azure Stack również kosztują taka sama jak maszyny wirtualne platformy Azure, korzystając z rozwiązań zarządzania aktualizacjami, śledzenie zmian, spisu i Azure monitorowanie maszyn wirtualnych z usługą Azure Stack.

## <a name="prerequisites"></a>Wymagania wstępne
Kilka wymagań wstępnych, muszą zostać spełnione przed rozpoczęciem korzystania z tych funkcji do aktualizacji i zarządzanie maszynami wirtualnymi platformy Azure Stack. Obejmują one kroki, które należy podjąć w witrynie Azure portal, jak i portalu administracyjnego usługi Azure Stack.

### <a name="in-the-azure-portal"></a>W witrynie Azure Portal
Aby używać usługi Azure Monitor dla maszyn wirtualnych, magazynu, śledzenia zmian i funkcje automatyzacji Update Management Azure maszyn wirtualnych platformy Azure Stack, należy najpierw włączyć te rozwiązania na platformie Azure.

> [!TIP]
> Jeśli masz już te funkcje włączone dla maszyn wirtualnych platformy Azure, używając istniejących poświadczeń LogAnalytics obszaru roboczego. Jeśli masz już LogAnalytics WorkspaceID i klucz podstawowy, którego chcesz używać, przejdź do sekcji [następnej sekcji](./vm-update-management.md#in-the-azure-stack-administration-portal). W przeciwnym razie jest nadal w tej sekcji, aby utworzyć nowy obszar roboczy LogAnalytics i konto usługi automation.

Pierwszym krokiem podczas włączania tych rozwiązań jest [Utwórz obszar roboczy LogAnalytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace) w subskrypcji platformy Azure. Obszar roboczy usługi Log Analytics jest unikatowy środowiska dzienniki usługi Azure Monitor z własnym repozytorium danych, źródłami danych i rozwiązań. Po utworzeniu obszaru roboczego, należy pamiętać, identyfikator WorkspaceID i klucz. Aby wyświetlić te informacje, przejdź do bloku obszaru roboczego, kliknij **Zaawansowane ustawienia**i przejrzyj **identyfikator obszaru roboczego** i **klucz podstawowy** wartości. 

Następnie należy [Tworzenie konta usługi Automation](https://docs.microsoft.com/azure/automation/automation-create-standalone-account). Konto usługi Automation jest kontenerem dla zasobów usługi Azure Automation. Umożliwia ona rozdzielenie środowisk lub dokładniejsze uporządkowanie przepływów pracy automatyzacji i zasobów. Po utworzeniu konta usługi automation możesz należy włączyć spis, śledzenie zmian i aktualizacji funkcji zarządzania. Aby to zrobić, wykonaj następujące kroki, aby umożliwić każdej funkcji:

1. W witrynie Azure portal przejdź do konta usługi Automation, do którego chcesz używać.

2. Wybierz rozwiązanie, aby włączyć (albo **spisu**, **śledzenie zmian**, lub **rozwiązanie Update management**).

3. Użyj **wybierz obszar roboczy...**  listy rozwijanej, aby wybrać obszar roboczy usługi Log Analytics do użycia.

4. Sprawdź, czy wszystkie pozostałe informacje są poprawne, a następnie kliknij przycisk **Włącz** Aby włączyć rozwiązanie.

5. Powtórz kroki 2 – 4, aby włączyć wszystkie trzy rozwiązania. 

   [![](media/vm-update-management/1-sm.PNG "Włączanie funkcji konta usługi automation")](media/vm-update-management/1-lg.PNG#lightbox)

### <a name="enable-azure-monitor-for-vms"></a>Włączanie usługi Azure Monitor dla maszyn wirtualnych

Usługa Azure Monitor dla maszyn wirtualnych monitoruje usługi Azure virtual machines (VM) i zestawy skalowania maszyn wirtualnych na dużą skalę. Analizuje wydajności i kondycji maszyn wirtualnych systemu Linux i Windows i monitoruje ich procesy i zależności dla innych zasobów i procesów zewnętrznych.

Rozwiązanie usługi Azure Monitor dla maszyn wirtualnych obejmuje obsługę monitorowania wydajności i aplikacji zależności dla maszyn wirtualnych, które są hostowane lokalnie lub u innego dostawcy chmury. Trzy kluczowe funkcje dostarczać dokładniejsze omówienie:

1. Logiczne składniki maszyn wirtualnych platformy Azure, który uruchamiany Windows i Linux: Są mierzone względem kryteria kondycji wstępnie skonfigurowane, i ich wysyłania alertów, gdy ocenianą warunek jest spełniony. 

2. Wstępnie zdefiniowane popularne wykresy wydajności: Wyświetla podstawowe metryki wydajności z systemu operacyjnego gościa maszyny Wirtualnej.

3. Mapa zależności: Wyświetla powiązanych elementów z maszyną Wirtualną z różnych subskrypcji i grupy zasobów.

Po utworzeniu obszaru roboczego usługi Log Analytics, należy włączyć liczniki wydajności w obszarze roboczym dla kolekcji w systemie Linux i Windows maszyn wirtualnych, jak również zainstalować i włączyć ServiceMap i InfrastructureInsights rozwiązania w Twoim obszarze roboczym. Proces jest opisany w [wdrożenia usługi Azure Monitor dla maszyn wirtualnych](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-onboard#deploy-azure-monitor-for-vms) przewodnik.

### <a name="in-the-azure-stack-administration-portal"></a>W portalu administracyjnym usługi Azure Stack
Po włączeniu rozwiązania usługi Azure Automation w witrynie Azure portal, następnie należy zalogować się do portalu administracyjnego usługi Azure Stack jako administrator w chmurze i Pobierz **usługi Azure Monitor, aktualizacji i zarządzania konfiguracją** i **Usługi azure Monitor, aktualizacji i zarządzania konfiguracją dla systemu Linux** elementów portalu marketplace usługi Azure Stack rozszerzenia. 

   ![Usługa Azure Monitor, aktualizacji i konfiguracji zarządzania rozszerzenie elementu portalu marketplace](media/vm-update-management/2.PNG) 

Aby włączyć usługi Azure Monitor na potrzeby rozwiązania mapy maszyn wirtualnych i Uzyskaj szczegółowe informacje dotyczące sieci zależności, musisz również pobrać **agenta usługi Azure Monitor zależności**:

   ![Usługa Azure Monitor agenta zależności](media/vm-update-management/2-dependency.PNG) 

## <a name="enable-update-management-for-azure-stack-virtual-machines"></a>Włączanie rozwiązania Update Management dla maszyn wirtualnych usługi Azure Stack
Wykonaj następujące kroki, aby włączyć zarządzanie aktualizacjami dla maszyn wirtualnych platformy Azure Stack.

1. Zaloguj się do portalu użytkowników usługi Azure Stack.

2. W aplikacji portal użytkowników usługi Azure Stack, przejdź do bloku rozszerzeń maszyn wirtualnych, dla których chcesz włączyć te rozwiązania, kliknij przycisk **+ Dodaj**, wybierz opcję **aktualizacji baz danych Azure i zarządzanie konfiguracją** rozszerzenie, a następnie kliknij przycisk **Utwórz**:

   [![](media/vm-update-management/3-sm.PNG "Blok rozszerzenia maszyny Wirtualnej")](media/vm-update-management/3-lg.PNG#lightbox)

3. Podaj utworzony wcześniej identyfikator obszaru roboczego i klucz podstawowy Połącz agenta z obszarem roboczym LogAnalytics, a następnie kliknij przycisk **OK** Aby wdrożyć rozszerzenie.

   [![](media/vm-update-management/4-sm.PNG "Podając identyfikator WorkspaceID i klucz")](media/vm-update-management/4-lg.PNG#lightbox) 

4. Zgodnie z opisem w [dokumentacja zarządzania aktualizacji usługi automation](https://docs.microsoft.com/azure/automation/automation-update-management), musisz włączyć rozwiązania do zarządzania aktualizacjami dla każdej maszyny Wirtualnej, którą chcesz zarządzać. Aby włączyć rozwiązanie dla wszystkich maszyn wirtualnych, które raporty do obszaru roboczego, wybierz pozycję **rozwiązanie Update management**, kliknij przycisk **zarządzać maszynami**, a następnie wybierz pozycję **Włącz na wszystkich dostępnych i przyszłych maszynach** opcji.

   [![](media/vm-update-management/5-sm.PNG "Podając identyfikator WorkspaceID i klucz")](media/vm-update-management/5-lg.PNG#lightbox) 

   > [!TIP]
   > Powtórz ten krok, aby włączyć każde z tych rozwiązań dla tego raportu do obszaru roboczego maszyny wirtualne stosu platformy Azure. 
  
Po włączeniu rozszerzenia aktualizacji baz danych Azure i zarządzanie konfiguracją skanowanie odbywa się dwa razy dziennie dla każdej zarządzanej maszyny Wirtualnej. Interfejs API jest wywoływana co 15 minut, aby wykonać zapytanie o czas ostatniej aktualizacji ustalić, czy stan się zmienił. Jeśli stan się zmienił, inicjowane jest skanowanie pod kątem zgodności.

Po przeskanowaniu maszyn wirtualnych pojawią się one w ramach konta usługi Azure Automation w rozwiązaniu do zarządzania aktualizacjami: 

   [![](media/vm-update-management/6-sm.PNG "Podając identyfikator WorkspaceID i klucz")](media/vm-update-management/6-lg.PNG#lightbox) 

> [!IMPORTANT]
> Może upłynąć od 30 minut do 6 godzin dla pulpitu nawigacyjnego wyświetlić zaktualizowane dane z zarządzanych komputerów.

Maszyny wirtualne Azure Stack mogą być teraz dołączane w zaplanowanych wdrożeń aktualizacji wraz z maszynami wirtualnymi platformy Azure.

## <a name="enable-azure-monitor-for-vms-running-on-azure-stack"></a>Włączanie usługi Azure Monitor dla maszyn wirtualnych uruchomionych w usłudze Azure Stack
Gdy maszyna wirtualna ma **usługi Azure Monitor, aktualizacji i zarządzania konfiguracją** i **agenta usługi Azure Monitor zależności** zainstalowanych rozszerzeń, rozpocznie się zgłaszających dane w [usługi Azure Monitor w przypadku maszyn wirtualnych](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview) rozwiązania. 

> [!TIP]
> **Agenta usługi Azure Monitor zależności** rozszerzenia nie wymaga żadnych parametrów. Usługi Azure Monitor dla agenta zależności mapy maszyn wirtualnych nie przesyłać żadnych danych, a nie wymaga wprowadzania zmian w zapory i porty. Dane mapy są zawsze przesyłane przez agenta usługi Log Analytics w usłudze Azure Monitor, bezpośrednio lub za pośrednictwem [bramy pakietu OMS](https://docs.microsoft.com/azure/azure-monitor/platform/gateway) Jeśli Twoje informatyczne zasady zabezpieczeń nie pozwalają komputerom w sieci, aby nawiązać połączenie z Internetem.

Usługa Azure Monitor dla maszyn wirtualnych zawiera zbiór wykresy wydajności przeznaczonych wykonuje kilka kluczowych wskaźników wydajności (KPI), aby ułatwić ustalenie, jak dobrze maszynę wirtualną. Wykresy pokazują wykorzystanie zasobów w określonym czasie, dzięki czemu możesz określić wąskie gardła, anomalie, lub przełącz się do perspektywy, wyświetlanie listy każdej maszyny w celu wyświetlenia wykorzystania zasobów, w oparciu o wybraną metryką. Dostępnych jest wiele elementów, należy wziąć pod uwagę podczas rozwiązywania problemów związanych z wydajnością, usługi Azure Monitor dla wskaźników wydajności systemu operacyjnego klucza monitory maszyny wirtualne dotyczące procesora, pamięci, karty sieciowej i wykorzystanie dysku. Wydajność uzupełniają funkcję monitorowania kondycji i pomaga ujawnić problemy, które wskazują możliwe awarii składników, dostrajanie pomocy technicznej i optymalizacji do osiągnięcia wydajności oraz obsługi planowania pojemności.

   ![Usługa Azure kartę monitorowanie wydajności](https://docs.microsoft.com/azure/azure-monitor/insights/media/vminsights-performance/vminsights-performance-aggview-01.png)

Wyświetlanie składniki odnalezionych aplikacji w Windows i Linux maszyn wirtualnych w usłudze Azure Stack można zaobserwować na dwa sposoby, za pomocą usługi Azure Monitor w przypadku maszyn wirtualnych z maszyny wirtualnej, który jest bezpośrednio lub grupami maszyn wirtualnych za pomocą usługi Azure Monitor.
[Przy użyciu usługi Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) mapę, aby poznać składniki aplikacji](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-maps) artykuł pomoże Ci zrozumieć środowisko między dwóch perspektyw i jak za pomocą funkcji mapy.

   ![Usługa Azure kartę monitorowanie wydajności](https://docs.microsoft.com/azure/azure-monitor/insights/media/vminsights-maps/map-multivm-azure-monitor-01.png)


## <a name="enable-update-management-using-a-resource-manager-template"></a>Włączanie rozwiązania Update Management przy użyciu szablonu usługi Resource Manager
Jeśli masz dużą liczbę maszyn wirtualnych platformy Azure Stack, możesz użyć [tego szablonu usługi Azure Resource Manager](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) łatwo wdrożyć rozwiązanie na maszynach wirtualnych. Szablon wdraża rozszerzenia Microsoft Monitoring Agent do istniejącej maszyny Wirtualnej usługi Azure Stack i dodaje go do istniejącego obszaru roboczego LogAnalytics platformy Azure.
 
## <a name="next-steps"></a>Kolejne kroki
[Optymalizuj wydajność maszyny Wirtualnej programu SQL Server](azure-stack-sql-server-vm-considerations.md)




