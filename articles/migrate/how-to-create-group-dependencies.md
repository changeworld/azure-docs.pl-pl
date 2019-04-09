---
title: Uściślij grupę oceny za pomocą mapowania zależności grup w usłudze Azure Migrate | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak uzyskiwać dokładniejsze oceny za pomocą mapowania zależności dla grup w usłudze Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/05/2018
ms.author: raynew
ms.openlocfilehash: 71f792dd1238b11810abfb6a97ac9e051da2ec45
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59274639"
---
# <a name="refine-a-group-using-group-dependency-mapping"></a>Uściślanie zawartości grupy za pomocą mapowania zależności grupy

W tym artykule opisano Uściślanie zawartości grupy przez wizualizację zależności dla wszystkich maszyn w grupie. Ta metoda jest zazwyczaj używana, gdy zajdzie potrzeba doprecyzowania członkostwa dla istniejącej grupy przez krzyżowe sprawdzanie zależności między grupami, przed uruchomieniem oceny. Rafinacja grupy za pomocą wizualizacji zależności mogą pomóc efektywnego planowania migracji do platformy Azure. Może odnajdywać wszystkie współzależne systemy, które chcesz zmigrować razem. Ułatwia on upewnij się, że nic nie pozostawione awarii dziwnego, że nie występują podczas migracji na platformę Azure.


> [!NOTE]
> Grupy, dla których chcesz zwizualizować zależności nie powinien zawierać więcej niż 10 maszyny. Jeśli masz więcej niż 10 maszyn w grupie, firma Microsoft zaleca, aby podzielić ją na mniejszych grup, aby korzystać z funkcji wizualizacji zależności.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prepare-for-dependency-visualization"></a>Przygotowanie do wizualizacji zależności
Usługa Azure Migrate korzysta z rozwiązania Service Map w dzienniki usługi Azure Monitor, aby włączyć wizualizacja zależności maszyn.

> [!NOTE]
> Funkcja wizualizacji zależności nie jest dostępna na platformie Azure Government.

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
Aby wyświetlić zależności grupy, musisz pobrać i zainstalować agentów na każdym komputerze w środowisku lokalnym, który jest częścią grupy. Ponadto w przypadku maszyn bez łączności internetowej musisz pobrać i zainstalować [bramy usługi Log Analytics](../azure-monitor/platform/gateway.md) na nich.

1. W **Przegląd**, kliknij przycisk **Zarządzaj** > **grup**, przejdź do wymaganej grupy.
2. Na liście maszyn wirtualnych w **agenta zależności** kolumny, kliknij przycisk **wymaga instalacji** Aby uzyskać instrukcje dotyczące sposobu pobierania i instalowania agentów.
3. Na **zależności** strony, Pobierz i zainstaluj program Microsoft Monitoring Agent (MMA) i agenta zależności na każdej maszynie Wirtualnej, która jest częścią grupy.
4. Skopiuj identyfikator i klucz obszaru roboczego. Będą one potrzebne po zainstalowaniu programu MMA na maszynach w środowisku lokalnym.

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

#### <a name="install-the-agent-on-a-machine-monitored-by-system-center-operations-manager"></a>Zainstaluj agenta na maszynie monitorowane przez System Center Operations Manager

Dla komputerów monitorowanych przez program Operations Manager 2012 R2 lub nowszym nie ma potrzeby instalowania agenta MMA. Rozwiązania Service Map jest zintegrowana z programem Operations Manager, który wykorzystuje MMA menedżera operacji na zbieranie danych niezbędnych zależności. Można włączyć integracji, korzystając ze wskazówek [tutaj](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites). Należy jednak pamiętać, że konieczne zainstalowanie na tych maszynach agenta zależności.

### <a name="install-the-dependency-agent"></a>Instalowanie agenta zależności
1. Aby zainstalować agenta zależności na komputerze Windows, kliknij dwukrotnie plik instalacyjny i wykonaj instrukcje kreatora.
2. Aby zainstalować agenta zależności na maszynę z systemem Linux, zainstaluj jako użytkownik główny, używając następującego polecenia:

    ```sh InstallDependencyAgent-Linux64.bin```

Dowiedz się więcej o obsłudze agenta zależności [Windows](../azure-monitor/insights/service-map-configure.md#supported-windows-operating-systems) i [Linux](../azure-monitor/insights/service-map-configure.md#supported-linux-operating-systems) systemów operacyjnych.

[Dowiedz się więcej](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples) dotyczące wykorzystania skryptów do zainstalowania agenta zależności.

## <a name="refine-the-group-based-on-dependency-visualization"></a>Dostosować grupę, w oparciu o wizualizacji zależności
Agenci zainstalowani na wszystkich komputerach w grupie możesz wizualizować zależności grupy i dostosuj je, wykonując poniższe kroki.

1. Na platformie Azure migracji projektu, w obszarze **Zarządzaj**, kliknij przycisk **grup**, a następnie wybierz grupę.
2. Na stronie grupy kliknij **wyświetlanie zależności**, aby otworzyć mapowania zależności grupy.
3. Mapa zależności dla grupy zawiera następujące informacje:
   - Dla ruchu przychodzącego (klientów) oraz połączenia wychodzące TCP (serwery), do i z wszystkich maszyn, które są częścią grupy
       - Zależnych maszyn, które nie mają zainstalowany agent MMA i zależności są pogrupowane według numerów portów
       - Zależnych maszyn, które mają programu MMA i zainstalować agenta zależności są wyświetlane jako oddzielne pola
   - Procesy uruchomione na maszynie, możesz rozwinąć każde pole maszyny do wyświetlania procesów
   - Właściwości, takie jak w pełni kwalifikowanej nazwy domeny, System operacyjny i każdej maszyny, itp. adres MAC, możesz kliknąć pole każdej maszyny w taki sposób, aby wyświetlić te szczegóły

     ![Wyświetlanie zależności grupowych](./media/how-to-create-group-dependencies/view-group-dependencies.png)

3. Aby wyświetlić bardziej szczegółowe zależności, kliknij zakres czasu, aby go zmodyfikować. Domyślnie zakres jest godzinę. Możesz modyfikować zakres czasu lub określ rozpoczęcia i zakończenia daty i czasu trwania.

   > [!NOTE]
   >    Wizualizacji zależności interfejsu użytkownika nie obsługuje obecnie wybór zakresu czasu, więcej niż jedna godzina. Użyj usługi Azure Monitor rejestruje [wykonywanie zapytań o dane zależności](https://docs.microsoft.com/azure/migrate/how-to-create-a-group) przez dłuższy czas.

4. Sprawdź zależnych maszyn proces uruchomiony na każdej maszynie, a następnie zidentyfikuje maszyny, które powinny zostać dodane lub usunięte z grupy.
5. Użyj klawiszy Ctrl + kliknięcie, aby wybrać maszyny na mapie, aby dodać lub usunąć je z niej.
    - Można dodawać tylko te maszyny, które zostały odnalezione.
    - Dodawanie i usuwanie maszyn w grupie unieważnia ostatnie ocen dla niego.
    - Po zmodyfikowaniu grupy, można opcjonalnie utworzyć nowego rozwiązania do oceny.
5. Kliknij przycisk **OK** można zapisać grupy.

    ![Dodawanie lub usuwanie komputerów](./media/how-to-create-group-dependencies/add-remove.png)

Jeśli chcesz sprawdzić zależności określonej maszyny, która pojawia się na mapie zależności grupy [Konfigurowanie mapowania zależności maszyn](how-to-create-group-machine-dependencies.md).

## <a name="query-dependency-data-from-azure-monitor-logs"></a>Zależności zapytania o dane z dzienników usługi Azure Monitor

Zależność dane przechwycone przez rozwiązania Service Map są dostępne do wykonywania zapytań w obszarze roboczym usługi Log Analytics skojarzonych z projektu Azure Migrate. [Dowiedz się więcej](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) o tabele danych mapy usługi do wykonywania zapytań w usłudze Azure Monitor dzienniki. 

Aby uruchamiać zapytania Kusto:

1. Po zainstalowaniu agentów, przejdź do portalu i kliknij przycisk **Przegląd**.
2. W **Przegląd**, przejdź do **Essentials** części projektu i kliknij przycisk Dalej, aby nazwa obszaru roboczego **obszaru roboczego pakietu OMS**.
3. Na stronie obszaru roboczego usługi Log Analytics kliknij **ogólne** > **dzienniki**.
4. Napisz zapytanie do zbierania danych zależności przy użyciu dzienników usługi Azure Monitor. Przykładowe zapytania, aby zebrać dane zależności są dostępne [tutaj](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches).
5. Uruchom zapytanie, klikając polecenie Uruchom. 

[Dowiedz się więcej](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) o tym, jak pisać zapytania Kusto. 

## <a name="sample-azure-monitor-logs-queries"></a>Przykład usługi Azure Monitor rejestruje zapytania

Poniżej przedstawiono przykładowe zapytania, można użyć, aby wyodrębnić dane zależności. Można zmodyfikować zapytania, które można wyodrębnić punkty preferowany. Wyczerpujący wykaz pól w rekordach zależności jest dostępna [tutaj](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records)

### <a name="summarize-inbound-connections-on-a-set-of-machines"></a>Podsumowanie połączenia przychodzące na zestawie maszyn

Należy pamiętać, rekordy w tabeli, metryki połączeń i VMConnection, nie reprezentują poszczególnych fizycznych połączeń sieciowych. Wiele połączeń sieci fizycznej są podzielone na połączenie logiczne. [Dowiedz się więcej](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections) o jak fizycznego połączenia sieciowego dane są agregowane w jednym rekordzie logicznych w VMConnection. 

```
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
- [Dowiedz się więcej](concepts-assessment-calculation.md) na temat sposobu obliczania ocen.
