---
title: Grupowanie maszyn za pomocą zależności maszyn za pomocą usługi Azure Migrate | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób tworzenia oceny za pomocą zależności maszyn za pomocą usługi Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/05/2018
ms.author: raynew
ms.openlocfilehash: 686c91669e5eccd7979c248db42d6f5b5079308b
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59280914"
---
# <a name="group-machines-using-machine-dependency-mapping"></a>Grupowanie maszyn za pomocą mapowania zależności maszyn

W tym artykule opisano sposób tworzenia grupy maszyn na potrzeby [usługi Azure Migrate](migrate-overview.md) oceny przez wizualizację zależności maszyn. Ta metoda jest zazwyczaj używana, gdy chcesz ocenić grupy maszyn wirtualnych z wyższego poziomu zaufania, krzyżowe sprawdzanie zależności maszyny, przed uruchomieniem oceny. Wizualizacja zależności mogą pomóc w efektywnego planowania migracji do platformy Azure. Ułatwia on upewnij się, że nic nie pozostawione awarii dziwnego, że nie występują podczas migracji na platformę Azure. Może odnajdywać wszystkie współzależne systemy, które trzeba migrowane razem i ustalić, czy na komputerze z uruchomionym systemem nadal działa jako użytkownicy lub kwalifikuje się do likwidacji zamiast migracji.

> [!NOTE]
> Funkcja wizualizacji zależności nie jest dostępna na platformie Azure Government.

## <a name="prepare-for-dependency-visualization"></a>Przygotowanie do wizualizacji zależności
Usługa Azure Migrate korzysta z rozwiązania Service Map w dzienniki usługi Azure Monitor, aby włączyć wizualizacja zależności maszyn.

### <a name="associate-a-log-analytics-workspace"></a>Skojarzyć obszar roboczy usługi Log Analytics
Aby korzystać z wizualizacji zależności, należy skojarzyć obszar roboczy usługi Log Analytics, nowej lub istniejącej z projektem migracji platformy Azure. Możesz tworzyć lub dołączyć obszaru roboczego w tej samej subskrypcji, w której jest tworzony projekt migracji.

- Aby dołączyć obszar roboczy usługi Log Analytics do projektu, w **Przegląd**, przejdź do **Essentials** części projektu, kliknij przycisk **wymaga konfiguracji**

    ![Skojarzyć obszar roboczy usługi Log Analytics](./media/concepts-dependency-visualization/associate-workspace.png)

- Podczas tworzenia skojarzenia obszar roboczy, uzyskasz opcję, aby utworzyć nowy obszar roboczy lub dołączenie istniejącego:
  - Gdy tworzysz nowy obszar roboczy, musisz określić nazwę obszaru roboczego. Obszar roboczy zostanie utworzony w regionie, w tym samym [lokalizacja geograficzna platformy Azure](https://azure.microsoft.com/global-infrastructure/geographies/) jako projekt migracji.
  - Po dołączeniu istniejącego obszaru roboczego, możesz wybrać wszystkie dostępne obszary robocze w tej samej subskrypcji co projekt migracji. Należy pamiętać, że tylko te obszary robocze są wymienione, które zostały utworzone w regionie, w którym [Mapa usługi jest obsługiwany](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-configure#supported-azure-regions). Aby można było połączyć obszar roboczy, upewnij się, że masz "Reader" dostęp do obszaru roboczego.

> [!NOTE]
> Nie można zmienić obszar roboczy skojarzony z projektem migracji.

### <a name="download-and-install-the-vm-agents"></a>Pobieranie i instalowanie agentów maszyny wirtualnej
Po skonfigurowaniu obszar roboczy, musisz pobrać i zainstalować agentów na każdym komputerze w środowisku lokalnym, który ma zostać oceniona. Ponadto w przypadku maszyn bez łączności internetowej musisz pobrać i zainstalować [bramy usługi Log Analytics](../azure-monitor/platform/gateway.md) na nich.

1. W **Przegląd**, kliknij przycisk **Zarządzaj** > **maszyn**i wybierz maszynę wymagane.
2. W **zależności** kolumny, kliknij przycisk **agenci**.
3. Na **zależności** strony, Pobierz i zainstaluj program Microsoft Monitoring Agent (MMA) i agenta zależności na każdej maszynie Wirtualnej, które ma zostać oceniona.
4. Skopiuj identyfikator i klucz obszaru roboczego. Będą one potrzebne po zainstalowaniu programu MMA na maszynie lokalnej.

> [!NOTE]
> Można zautomatyzować instalację agentów można użyć dowolnego narzędzia wdrażania, takich jak System Center Configuration Manager lub użyć naszego narzędzia do partnera [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration), która ma agenta wdrażania rozwiązania dla usługi Azure Migrate.

### <a name="install-the-mma"></a>Instalowanie programu MMA

#### <a name="install-the-agent-on-a-windows-machine"></a>Zainstaluj agenta na komputerze Windows

Aby zainstalować agenta na komputerze Windows:

1. Kliknij dwukrotnie pobranego agenta.
2. Na **stronie powitalnej** kliknij przycisk **Dalej**. Na stronie **Postanowienia licencyjne** kliknij przycisk **Zgadzam się**, aby zaakceptować warunki licencji.
3. W **Folder docelowy**, zachować lub zmienić domyślny folder instalacji > **dalej**.
4. W **opcje instalacji agenta**, wybierz opcję **usługi Azure Log Analytics** > **dalej**.
5. Kliknij przycisk **Dodaj** Aby dodać nowy obszar roboczy usługi Log Analytics. Wklej identyfikator obszaru roboczego i klucz, który został skopiowany z portalu. Kliknij przycisk **Dalej**.

Można zainstalować agenta z wiersza polecenia lub przy użyciu zautomatyzowanej metody takie jak System Center Configuration Manager. [Dowiedz się więcej](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) o używaniu tych metod instalowania agenta MMA.

#### <a name="install-the-agent-on-a-linux-machine"></a>Zainstaluj agenta na maszynie z systemem Linux

Aby zainstalować agenta na maszynie z systemem Linux:

1. Przenieś odpowiedni zbiór (x86 lub x64), aby komputer z systemem Linux przy użyciu protokołu scp/sftp.
2. Zainstaluj zbiór przy użyciu argumentu--install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[Dowiedz się więcej](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) dotyczące listy obsługiwane systemy operacyjne Linux przez MMA.

#### <a name="install-the-agent-on-a-machine-monitored-by-scom"></a>Zainstaluj agenta na komputerze, na monitorowanych przez programu SCOM

Dla maszyn monitorowane przez program System Center Operations Manager 2012 R2 lub nowszym nie ma potrzeby instalowania agenta MMA. Mapa usługi jest zintegrowana z programem SCOM, który wykorzystuje MMA SCOM na zbieranie danych niezbędnych zależności. Można włączyć integracji, korzystając ze wskazówek [tutaj](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites). Należy jednak pamiętać, że agenta zależności należy zainstalować na tych maszynach.


### <a name="install-the-dependency-agent"></a>Instalowanie agenta zależności
1. Aby zainstalować agenta zależności na komputerze Windows, kliknij dwukrotnie plik instalacyjny i wykonaj instrukcje kreatora.
2. Aby zainstalować agenta zależności na maszynę z systemem Linux, zainstaluj jako użytkownik główny, używając następującego polecenia:

    ```sh InstallDependencyAgent-Linux64.bin```

Dowiedz się więcej o obsłudze agenta zależności [Windows](../azure-monitor/insights/service-map-configure.md#supported-windows-operating-systems) i [Linux](../azure-monitor/insights/service-map-configure.md#supported-linux-operating-systems) systemów operacyjnych.

[Dowiedz się więcej](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples) dotyczące wykorzystania skryptów do zainstalowania agenta zależności.


## <a name="create-a-group"></a>Tworzenie grupy

1. Po zainstalowaniu agentów, przejdź do portalu i kliknij przycisk **Zarządzaj** > **maszyn**.
2. Wyszukaj maszyny, na którym zainstalowano agentów.
3. **Zależności** kolumny maszyny powinny być teraz wyświetlane jako **wyświetlanie zależności**. Kliknij kolumnę, aby wyświetlić zależności maszyny.
4. Mapa zależności maszyny zawiera następujące informacje:
    - Dla ruchu przychodzącego (klientów) oraz połączenia wychodzące TCP (serwery) do/z maszyny
        - Zależnych maszyn, które nie mają zainstalowany agent MMA i zależności są pogrupowane według numerów portów
        - Zależnych maszyn, które mają programu MMA i zainstalować agenta zależności są wyświetlane jako oddzielne pola
    - Procesy uruchomione na maszynie, możesz rozwinąć każde pole maszyny do wyświetlania procesów
    - Właściwości, takie jak w pełni kwalifikowanej nazwy domeny, System operacyjny i każdej maszyny, itp. adres MAC, możesz kliknąć pole każdej maszyny w taki sposób, aby wyświetlić te szczegóły

      ![Wyświetlanie zależności maszyn](./media/how-to-create-group-machine-dependencies/machine-dependencies.png)

4. Zapoznanie się z zależnościami dla różnych czasów trwania, klikając na czas trwania w Etykieta zakresu czasu. Domyślnie zakres jest godzinę. Możesz modyfikować zakres czasu lub określ rozpoczęcia i zakończenia daty i czasu trwania.

   > [!NOTE]
   >    Wizualizacji zależności interfejsu użytkownika nie obsługuje obecnie wybór zakresu czasu, więcej niż jedna godzina. Użyj usługi Azure Monitor rejestruje [wykonywanie zapytań o dane zależności](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) przez dłuższy czas.

5. Po zidentyfikowaniu zależnych maszyn, które chcesz zgrupować, za pomocą kombinacji Ctrl + kliknięcie wybrać wiele maszyn na mapie, a następnie kliknij przycisk **grupowanie maszyn**.
6. Określ nazwę grupy. Upewnij się, że zależnych maszyn są wykrywane przez usługę Azure Migrate.

    > [!NOTE]
    > Jeśli maszynę zależny nie został odnaleziony przez usługę Azure Migrate, nie można dodać go do grupy. Aby dodać takich maszyn do grupy, należy ponownie uruchomić proces wykrywania z zakresem prawej w programie vCenter Server i upewnij się, że komputer został odnaleziony przez usługę Azure Migrate.  

7. Jeśli chcesz utworzyć ocenę dla tej grupy, zaznacz pole wyboru, aby utworzyć nową ocenę dla grupy.
8. Kliknij przycisk **OK** można zapisać grupy.

Po utworzeniu grupy zaleca się instalowanie agentów na wszystkich komputerach w grupie i ulepszyć grupę przez wizualizację zależności w całej grupy.

## <a name="query-dependency-data-from-azure-monitor-logs"></a>Zależności zapytania o dane z dzienników usługi Azure Monitor

Zależność dane przechwycone przez rozwiązania Service Map są dostępne do wykonywania zapytań w obszarze roboczym usługi Log Analytics skojarzonych z projektu Azure Migrate. [Dowiedz się więcej](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) o tabele danych mapy usługi do wykonywania zapytań w usłudze Azure Monitor dzienniki. 

Aby uruchamiać zapytania Kusto:

1. Po zainstalowaniu agentów, przejdź do portalu i kliknij przycisk **Przegląd**.
2. W **Przegląd**, przejdź do **Essentials** części projektu i kliknij przycisk Dalej, aby nazwa obszaru roboczego **obszaru roboczego pakietu OMS**.
3. Na stronie obszaru roboczego usługi Log Analytics kliknij **ogólne** > **dzienniki**.
4. Napisz zapytanie do zbierania danych zależności przy użyciu dzienników usługi Azure Monitor. Przykładowe zapytania, aby zebrać dane zależności są dostępne [tutaj](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches).
5. Uruchom zapytanie, klikając polecenie Uruchom. 

[Dowiedz się więcej](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) o tym, jak pisać zapytania Kusto. 

### <a name="sample-azure-monitor-logs-queries"></a>Przykład usługi Azure Monitor rejestruje zapytania

Poniżej przedstawiono przykładowe zapytania, można użyć, aby wyodrębnić dane zależności. Należy pamiętać, że zapytania można zmodyfikować, aby wyodrębnić punkty preferowany. Wyczerpujący wykaz pól w rekordach zależności jest dostępna [tutaj](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records)

#### <a name="summarize-inbound-connections-on-a-set-of-machines"></a>Podsumowanie połączenia przychodzące na zestawie maszyn

Należy pamiętać, rekordy w tabeli, metryki połączeń i VMConnection, nie reprezentują poszczególnych fizycznych połączeń sieciowych. Wiele połączeń sieci fizycznej są podzielone na połączenie logiczne. [Dowiedz się więcej](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections) o jak fizycznego połączenia sieciowego dane są agregowane w jednym rekordzie logicznych w VMConnection. 

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z); 
VMConnection
| where Direction == 'inbound' 
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(LinksEstablished) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

#### <a name="summarize-volume-of-data-sent-and-received-on-inbound-connections-between-a-set-of-machines"></a>Podsumowanie ilość danych wysłanych i odebranych dla połączeń przychodzących między zestawem maszyn

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z); 
VMConnection
| where Direction == 'inbound' 
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(BytesSent), sum(BytesReceived) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

## <a name="next-steps"></a>Kolejne kroki

- [Dowiedz się więcej](https://docs.microsoft.com/azure/migrate/resources-faq#dependency-visualization) temat — często zadawane pytania na wizualizacji zależności.
- [Dowiedz się, jak](how-to-create-group-dependencies.md) uściślić grupy przez wizualizację zależności między grupami.
- [Dowiedz się więcej](concepts-assessment-calculation.md) na temat sposobu obliczania ocen.
