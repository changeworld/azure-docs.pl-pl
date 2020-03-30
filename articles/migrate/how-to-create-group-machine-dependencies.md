---
title: Konfigurowanie analizy zależności opartej na agentach w usłudze Azure Migrate Server Assessment
description: W tym artykule opisano sposób konfigurowania analizy zależności opartej na agentach w usłudze Azure Migrate Server Assessment.
ms.topic: how-to
ms.date: 2/24/2020
ms.openlocfilehash: e61b7b4e6c3e566aa67d2bd585d2049ae885083b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79453619"
---
# <a name="set-up-dependency-visualization"></a>Konfigurowanie wizualizacji zależności

W tym artykule opisano sposób konfigurowania analizy zależności opartej na agentach w usłudze Azure Migrate:Server Assessment. [Analiza zależności](concepts-dependency-visualization.md) ułatwia identyfikowanie i rozumienie zależności między komputerami, które chcesz ocenić i przeprowadzić migrację na platformę Azure.

## <a name="before-you-start"></a>Przed rozpoczęciem

- [Dowiedz się więcej o](concepts-dependency-visualization.md#agent-based-analysis) analizie zależności opartej na agentach.
- Przejrzyj wymagania wstępne i wymagania dotyczące obsługi technicznej dotyczące konfigurowania wizualizacji zależności opartych na agentach dla [maszyn wirtualnych VMware,](migrate-support-matrix-vmware.md#agent-based-dependency-analysis-requirements) [serwerów fizycznych](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)i maszyn [wirtualnych funkcji Hyper V](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements).
- Upewnij się, że [utworzono](how-to-add-tool-first-time.md) projekt migracji platformy Azure.
- Jeśli projekt został już utworzony, upewnij się, że [dodano](how-to-assess.md) narzędzie oceny migracji:serwer platformy Azure.
- Upewnij się, że skonfigurowałeś [urządzenie migracji platformy Azure](migrate-appliance.md) do odnajdywanie komputerów lokalnych. Dowiedz się, jak skonfigurować urządzenie dla serwerów [VMware,](how-to-set-up-appliance-vmware.md) [Hyper-V](how-to-set-up-appliance-hyper-v.md)lub [fizycznych.](how-to-set-up-appliance-physical.md) Urządzenie odnajduje maszyny lokalne i wysyła metadane, dane o wydajności do oceny migracji:serwera platformy Azure.
- Aby użyć wizualizacji zależności, należy [skojarzyć obszar roboczy usługi Log Analytics](../azure-monitor/platform/manage-access.md) z projektem migracji platformy Azure:
    - Obszar roboczy można dołączyć tylko po skonfigurowaniu urządzenia migracji platformy Azure i odnajdowaniu maszyn w projekcie migracji platformy Azure.
    - Upewnij się, że masz obszar roboczy w subskrypcji, która zawiera projekt migracji platformy Azure.
    - Obszar roboczy musi znajdować się w regionach Wschodnich Stanów Zjednoczonych, Azji Południowo-Wschodniej lub Europy Zachodniej. Obszarów roboczych w innych regionach nie można skojarzyć z projektem.
    - Obszar roboczy musi znajdować się w regionie, w którym [jest obsługiwana mapa usługi](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites).
    - Nowy lub istniejący obszar roboczy usługi Log Analytics można skojarzyć z projektem migracji platformy Azure.
    - Obszar roboczy należy dołączyć przy pierwszym skonfigurowaniu wizualizacji zależności dla komputera. Obszaru roboczego dla projektu migracji platformy Azure nie można zmodyfikować po jego dodaniu.
    - W usłudze Log Analytics obszar roboczy skojarzony z programem Azure Migrate jest oznaczony kluczem projektu migracji i nazwą projektu.

## <a name="associate-a-workspace"></a>Kojarzenie obszaru roboczego

1. Po wykryciu maszyn do oceny w **obszarze Usługi Server** > **Azure Migrate: Server Assessment**, kliknij pozycję **Przegląd**.  
2. W **obszarze Migracja na platformę Azure: ocena serwera**kliknij przycisk **Essentials**.
3. W **obszarze roboczym systemu OMS**kliknij pozycję **Wymaga konfiguracji**.

     ![Konfigurowanie obszaru roboczego usługi Log Analytics](./media/how-to-create-group-machine-dependencies/oms-workspace-select.png)   

4. W **obszarze roboczej Konfiguruj system OMS**określ, czy chcesz utworzyć nowy obszar roboczy, czy użyć istniejącego.
    - Można wybrać istniejący obszar roboczy ze wszystkich obszarów roboczych w subskrypcji projektu migracji.
    - Aby go skojarzyć, potrzebujesz dostępu programu Reader do obszaru roboczego.
5. Jeśli utworzysz nowy obszar roboczy, wybierz dla niego lokalizację.

    ![Dodawanie nowego obszaru roboczego](./media/how-to-create-group-machine-dependencies/workspace.png)


## <a name="download-and-install-the-vm-agents"></a>Pobieranie i instalowanie agentów maszyny wirtualnej

Na każdym komputerze, który chcesz przeanalizować, zainstaluj agentów.

> [!NOTE]
> W przypadku maszyn monitorowanych przez program System Center Operations Manager 2012 R2 lub nowszych nie trzeba instalować agenta MMA. Mapa usługi integruje się z programem Operations Manager. [Postępuj zgodnie](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites) ze wskazówkami dotyczącymi integracji.

1. W **obszarze Migracja platformy Azure: Ocena serwera**kliknij pozycję **Odnalezione serwery**.
2. Dla każdego komputera, który chcesz analizować za pomocą wizualizacji zależności, w kolumnie **Zależności** kliknij pozycję **Wymaga instalacji agenta**.
3. Na stronie **Zależności** pobierz agenta MMA i zależności dla systemu Windows lub Linux.
4. W obszarze **Konfigurowanie agenta MMA**skopiuj identyfikator i klucz obszaru roboczego. Są one potrzebne podczas instalowania agenta MMA.

    ![Zainstaluj agentów](./media/how-to-create-group-machine-dependencies/dependencies-install.png)


## <a name="install-the-mma"></a>Instalowanie programu MMA

Zainstaluj MMA na każdym komputerze z systemem Windows lub Linux, który chcesz przeanalizować.

### <a name="install-mma-on-a-windows-machine"></a>Instalowanie programu MMA na komputerze z systemem Windows

Aby zainstalować agenta na komputerze z systemem Windows:

1. Kliknij dwukrotnie pobranego agenta.
2. Na **stronie powitalnej** kliknij przycisk **Dalej**. Na stronie **Postanowienia licencyjne** kliknij pozycję **Wyrażam zgodę** na zaakceptowanie licencji.
3. W **obszarze Folder docelowy**zachowaj lub zmodyfikuj domyślny folder instalacyjny > **Dalej**.
4. W **obszarze Opcje konfiguracji agenta**wybierz pozycję Azure Log **Analytics** > **Next**.
5. Kliknij **przycisk Dodaj,** aby dodać nowy obszar roboczy usługi Log Analytics. Wklej identyfikator obszaru roboczego i klucz skopiowany z portalu. Kliknij przycisk **alej**.

Agenta można zainstalować z wiersza polecenia lub przy użyciu zautomatyzowanej metody, takiej jak Menedżer konfiguracji lub [Intigua](https://go.microsoft.com/fwlink/?linkid=2104196).
- [Dowiedz się więcej](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration) o instalowaniu agenta MMA przy użyciu tych metod.
- Agenta programu MMA można również zainstalować za pomocą [tego](https://go.microsoft.com/fwlink/?linkid=2104394) skryptu.
- [Dowiedz się więcej](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-windows-operating-systems) o systemach operacyjnych Windows obsługiwanych przez MMA.

### <a name="install-mma-on-a-linux-machine"></a>Instalowanie MMA na komputerze z systemem Linux

Aby zainstalować MMA na komputerze z systemem Linux:

1. Przenieś odpowiedni pakiet (x86 lub x64) na komputer z systemem Linux za pomocą scp/sftp.
2. Zainstaluj pakiet przy użyciu argumentu --install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[Dowiedz się więcej](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-linux-operating-systems) o liście systemów operacyjnych Linux obsługiwanych przez MMA. 

## <a name="install-the-dependency-agent"></a>Instalowanie agenta zależności

1. Aby zainstalować agenta zależności na komputerze z systemem Windows, kliknij dwukrotnie plik instalacyjny i postępuj zgodnie z instrukcjami kreatora.
2. Aby zainstalować agenta zależności na komputerze z systemem Linux, zainstaluj jako katalog główny za pomocą następującego polecenia:

    ```sh InstallDependencyAgent-Linux64.bin```

- [Dowiedz się więcej](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-hybrid-cloud#installation-script-examples) o tym, jak zainstalować agenta zależności za pomocą skryptów.
- [Dowiedz się więcej](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#supported-operating-systems) o systemach operacyjnych obsługiwanych przez agenta zależności.


## <a name="create-a-group-using-dependency-visualization"></a>Tworzenie grupy przy użyciu wizualizacji zależności

Teraz utwórz grupę do oceny. 


> [!NOTE]
> Grupy, dla których chcesz wizualizować zależności nie powinny zawierać więcej niż 10 maszyn. Jeśli masz więcej niż 10 maszyn, podziel je na mniejsze grupy.

1. W **obszarze Migracja platformy Azure: Ocena serwera**kliknij pozycję **Odnalezione serwery**.
2. W kolumnie **Zależności** kliknij pozycję **Wyświetl zależności** dla każdej maszyny, którą chcesz przejrzeć.
3. Na mapie zależności można zobaczyć następujące elementy:
    - Połączenia TCP przychodzące (klientów) i wychodzące (serwery) do i z komputera.
    - Maszyny zależne, które nie mają zainstalowanych agentów zależności, są pogrupowane według numerów portów.
    - Maszyny zależne z zainstalowanymi agentami zależności są wyświetlane jako oddzielne pola.
    - Procesy uruchomione wewnątrz maszyny. Rozwiń każde pole maszyny, aby wyświetlić procesy.
    - Właściwości komputera (w tym FQDN, system operacyjny, adres MAC). Kliknij każde pole maszyny, aby wyświetlić szczegóły.

4. Możesz przyjrzeć się zależnościom dla różnych czasów, klikając czas trwania w etykiecie zakresu czasu.
    - Domyślnie zakres wynosi godzinę. 
    - Można zmodyfikować zakres czasu lub określić daty rozpoczęcia i zakończenia oraz czas trwania.
    - Zakres czasu może wynosić do godziny. Jeśli potrzebujesz dłuższego zasięgu, użyj usługi Azure Monitor, aby zbadać dane zależne przez dłuższy okres.

5. Po zidentyfikowaniu komputerów zależnych, które chcesz zgrupować, użyj klawiszy Ctrl+Click, aby wybrać wiele komputerów na mapie, a następnie kliknij pozycję **Grupuj maszyny**.
6. Określ nazwę grupy.
7. Sprawdź, czy maszyny zależne są odnajdowane przez usługę Azure Migrate.

    - Jeśli komputer zależny nie zostanie wykryty przez usługę Azure Migrate: Server Assessment, nie można dodać go do grupy.
    - Aby dodać maszynę, uruchom ponownie odnajdowanie i sprawdź, czy urządzenie zostało odnalezione.

8. Jeśli chcesz utworzyć ocenę dla tej grupy, zaznacz to pole wyboru, aby utworzyć nową ocenę dla grupy.
8. Kliknij **przycisk OK,** aby zapisać grupę.

Po utworzeniu grupy zaleca się zainstalowanie agentów na wszystkich komputerach w grupie, a następnie wizualizację zależności dla całej grupy.

## <a name="query-dependency-data-in-azure-monitor"></a>Dane zależności kwerendy w usłudze Azure Monitor

Można wysyłać zapytania do danych zależności przechwyconych przez mapę usługi w obszarze roboczym usługi Log Analytics skojarzonym z projektem migracji platformy Azure. Usługa Log Analytics służy do pisania i uruchamiania zapytań dziennika usługi Azure Monitor.

- [Dowiedz się, jak wyszukiwać](../azure-monitor/insights/service-map.md#log-analytics-records) dane mapy usług w usłudze Log Analytics.
- [Zapoznaj się](../azure-monitor/log-query/get-started-queries.md) z omówieniem zapytań dziennika w [usłudze Log Analytics](../azure-monitor/log-query/get-started-portal.md).

Uruchom kwerendę dla danych zależności w następujący sposób:

1. Po zainstalowaniu agentów przejdź do portalu i kliknij przycisk **Przegląd**.
2. W **obszarze Migracja platformy Azure: ocena serwera**kliknij pozycję **Przegląd**. Kliknij strzałkę w dół, aby rozwinąć **pozycję Essentials**.
3. W **obszarze roboczym systemu OMS**kliknij nazwę obszaru roboczego.
3. Na stronie obszaru roboczego usługi Log Analytics > **ogólne**kliknij pozycję **Dzienniki**.
4. Napisz zapytanie i kliknij przycisk **Uruchom**.

### <a name="sample-queries"></a>Przykładowe zapytania

Oto kilka przykładowych zapytań, których można użyć do wyodrębnienia danych zależności.

- Można zmodyfikować kwerendy, aby wyodrębnić preferowane punkty danych.
- [Przejrzyj](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) pełną listę rekordów danych zależności.
- [Przejrzyj](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches) dodatkowe przykładowe zapytania.

#### <a name="sample-review-inbound-connections"></a>Przykład: przeglądanie połączeń przychodzących

Przejrzyj połączenia przychodzące dla zestawu maszyn wirtualnych.

- Rekordy w tabeli dla metryk połączeń (VMConnection) nie reprezentują poszczególnych fizycznych połączeń sieciowych.
- Wiele fizycznych połączeń sieciowych jest grupowanych w połączenie logiczne.
- [Dowiedz się więcej](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections) o agregacji danych połączenia sieciowego w vmconnection.

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

#### <a name="sample-summarize-sent-and-received-data"></a>Przykład: podsumuj wysłane i odebrane dane

W tym przykładzie podsumowano ilość danych wysyłanych i odbieranych na połączeniach przychodzących między zestawem komputerów.

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

## <a name="next-steps"></a>Następne kroki

[Tworzenie oceny](how-to-create-assessment.md) dla grupy.


