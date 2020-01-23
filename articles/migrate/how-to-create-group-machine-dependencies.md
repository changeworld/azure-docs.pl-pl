---
title: Grupowanie maszyn przy użyciu zależności maszyn z Azure Migrate | Microsoft Docs
description: Opisuje sposób tworzenia oceny przy użyciu zależności maszyn z usługą Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 10/01/2019
ms.author: hamusa
ms.openlocfilehash: 96d1c44eb9ecb71684e817a89f9376a07dbe3ccb
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514973"
---
# <a name="set-up-dependency-visualization-for-assessment"></a>Konfigurowanie wizualizacji zależności dla oceny

W tym artykule opisano sposób konfigurowania mapowania zależności w Azure Migrate: Ocena serwera.

Mapowanie zależności ułatwia wizualizację zależności między maszynami, które mają zostać poddane ocenie i przeprowadzeniem migracji.

- W Azure Migrate: Ocena serwera umożliwia zebranie maszyn w celu oceny. Zazwyczaj maszyny, które chcesz migrować ze sobą.
- Mapowanie zależności zazwyczaj jest stosowane, gdy chcesz ocenić grupy o wyższym poziomie zaufania.
- Mapowanie zależności ułatwia sprawdzanie zależności między maszynami przed uruchomieniem oceny i migracji.
- Mapowanie i Wizualizacja zależności ułatwiają efektywne planowanie migracji na platformę Azure. Gwarantuje to, że nic nie pozostało w tym samym czasie, co pozwoli uniknąć awarii podczas migracji.
- Korzystając z mapowania, można odnajdywać systemy zależne, które muszą zostać zmigrowane wspólnie. Możesz również określić, czy uruchomiony system nadal obsługuje użytkowników, czy też jest kandydatem do likwidacji zamiast migracji.

[Dowiedz się więcej](concepts-dependency-visualization.md#how-does-it-work) o wizualizacji zależności.

## <a name="before-you-start"></a>Przed rozpoczęciem

- Upewnij się, że [utworzono](how-to-add-tool-first-time.md) projekt Azure Migrate.
- Jeśli projekt został już utworzony, upewnij się, że [dodano](how-to-assess.md) Azure Migrate: Narzędzie do oceny serwera.
- Upewnij się, że maszyny zostały odnalezione w Azure Migrate; w tym celu można skonfigurować urządzenie Azure Migrate dla oprogramowania [VMware](how-to-set-up-appliance-vmware.md) lub [funkcji Hyper-V](how-to-set-up-appliance-hyper-v.md). Urządzenie odnajduje maszyny lokalne i wysyła metadane i dane wydajności do Azure Migrate: Ocena serwera. [Dowiedz się więcej](migrate-appliance.md).


**Funkcje** | **Uwaga**
--- | ---
Dostępność | Wizualizacja zależności nie jest dostępna w Azure Government.
Mapa usługi | Wizualizacja zależności używa rozwiązania Service Map w Azure Monitor. [Service map](../azure-monitor/insights/service-map.md) automatycznie wykrywa i pokazuje połączenia między serwerami.
Agenci | Aby użyć wizualizacji zależności, zainstaluj następujących agentów na maszynach, które chcesz zmapować:<br/> Agent [agenta Log Analytics](../azure-monitor/platform/log-analytics-agent.md) - (wcześniej określany jako Microsoft Monitoring Agent (MMA).<br/> - [Service map agenta zależności](../azure-monitor/insights/vminsights-enable-overview.md#the-microsoft-dependency-agent).<br/><br/> Aby zautomatyzować instalację agenta, można użyć narzędzia do wdrażania, takiego jak Configuration Manager, które ma rozwiązanie do wdrażania agenta dla Azure Migrate.
Agent zależności | Przegląd [obsługi agentów zależności](../azure-monitor/insights/vminsights-enable-overview.md#the-microsoft-dependency-agent) dla systemów Windows i Linux.<br/><br/> [Dowiedz się więcej](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples) o używaniu skryptów w celu zainstalowania agenta zależności.
Agent Log Analytics (MMA) | [Dowiedz się więcej](../azure-monitor/platform/log-analytics-agent.md#install-and-configure-agent) o metodach instalacji MMA.<br/><br/> W przypadku maszyn monitorowanych przez System Center Operations Manager 2012 R2 lub nowsze nie trzeba instalować agenta MMA. Service Map integruje się z Operations Manager. Integrację można włączyć, korzystając z wskazówek przedstawionych [tutaj](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites). Należy jednak pamiętać, że Agent zależności musi być zainstalowany na tych komputerach.<br/><br/> [Zapoznaj](../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems) się z systemami operacyjnymi Linux obsługiwanymi przez agenta log Analytics.
Grupy oceny | Grupy, dla których chcesz wizualizować zależności, nie mogą zawierać więcej niż 10 maszyn. Jeśli masz więcej niż 10 maszyn, Podziel je na mniejsze grupy w celu wizualizacji zależności.

## <a name="associate-a-log-analytics-workspace"></a>Kojarzenie obszaru roboczego Log Analytics

Aby można było użyć wizualizacji zależności, należy skojarzyć [obszar roboczy log Analytics](../azure-monitor/platform/manage-access.md) z projektem Azure Migrate.

- Możesz dołączyć obszar roboczy tylko w ramach subskrypcji projektu Azure Migrate.
- Możesz dołączyć istniejący obszar roboczy lub utworzyć nowy.
- Podczas pierwszego konfigurowania wizualizacji zależności dla komputera należy dołączyć obszar roboczy.
- Obszar roboczy można dołączyć dopiero po odnalezieniu maszyn w projekcie Azure Migrate. W tym celu można skonfigurować urządzenie Azure Migrate dla oprogramowania [VMware](how-to-set-up-appliance-vmware.md) lub [funkcji Hyper-V](how-to-set-up-appliance-hyper-v.md). Urządzenie odnajduje maszyny lokalne i wysyła metadane i dane wydajności do Azure Migrate: Ocena serwera. [Dowiedz się więcej](migrate-appliance.md).

Dołącz obszar roboczy w następujący sposób:

1. W **Azure Migrate: Ocena serwera**, kliknij przycisk **Przegląd**. Jeśli nie dodano jeszcze narzędzia do oceny serwera, należy to [zrobić w pierwszej kolejności](how-to-assess.md).
2. W obszarze **Przegląd**kliknij strzałkę w dół, aby rozwinąć **podstawowe**elementy.
3. W **obszarze roboczym pakietu OMS**kliknij pozycję **wymaga konfiguracji**.
4. W obszarze **Konfiguruj obszar roboczy**Określ, czy chcesz utworzyć nowy obszar roboczy, czy użyć istniejącego:

    ![Dodaj obszar roboczy](./media/how-to-create-group-machine-dependencies/workspace.png)

    - Po określeniu nazwy nowego obszaru roboczego można wybrać [region](https://azure.microsoft.com/global-infrastructure/regions/) , w którym zostanie utworzony obszar roboczy.
    - Po dołączeniu istniejącego obszaru roboczego możesz wybrać wszystkie dostępne obszary robocze w tej samej subskrypcji co projekt migracji.
    - Potrzebujesz dostępu czytelnika do obszaru roboczego, aby móc go dołączyć.
    - Nie można modyfikować obszaru roboczego skojarzonego z projektem po jego dołączeniu.

## <a name="download-and-install-the-vm-agents"></a>Pobieranie i instalowanie agentów maszyny wirtualnej

Pobierz i zainstaluj agentów na każdej maszynie lokalnej, którą chcesz wizualizować przy użyciu mapowania zależności.

1. W **Azure Migrate: Ocena serwera**, kliknij przycisk **odnalezione serwery**.
2. Dla każdej maszyny, dla której chcesz użyć wizualizacji zależności, kliknij pozycję **wymaga instalacji agenta**.
3. Na stronie **zależności** dla maszyny > **pobrać i zainstalować program MMA**, pobrać odpowiedniego agenta i zainstalować go zgodnie z poniższym opisem.
4. W obszarze **Pobierz i Zainstaluj agenta zależności**Pobierz odpowiedniego agenta i zainstaluj go zgodnie z poniższym opisem.
5. W obszarze **Konfigurowanie agenta MMA**Skopiuj identyfikator i klucz obszaru roboczego. Są one potrzebne podczas instalacji agenta MMA.

### <a name="install-the-mma"></a>Instalowanie programu MMA

#### <a name="install-the-agent-on-a-windows-machine"></a>Instalowanie agenta na komputerze z systemem Windows

Aby zainstalować agenta na komputerze z systemem Windows:

1. Kliknij dwukrotnie pobranego agenta.
2. Na **stronie powitalnej** kliknij przycisk **Dalej**. Na stronie **Postanowienia licencyjne** kliknij przycisk **Zgadzam się**, aby zaakceptować warunki licencji.
3. W **folderze docelowym**Zachowaj lub zmodyfikuj domyślny Folder instalacji > **dalej**.
4. W obszarze **Opcje instalacji agenta**wybierz pozycję **Azure log Analytics** > **dalej**.
5. Kliknij przycisk **Dodaj** , aby dodać nowy obszar roboczy log Analytics. Wklej w obszarze Identyfikator i klucz obszaru roboczego skopiowane z portalu. Kliknij przycisk **Dalej**.

Agenta można zainstalować z wiersza polecenia lub przy użyciu metody zautomatyzowanej, takiej jak Configuration Manager lub [Intigua](https://go.microsoft.com/fwlink/?linkid=2104196). [Dowiedz się więcej](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) na temat korzystania z tych metod w celu zainstalowania agenta MMA. Agenta programu MMA można również zainstalować za pomocą [tego](https://go.microsoft.com/fwlink/?linkid=2104394) skryptu.

[Dowiedz się więcej](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-windows-operating-systems) o systemach operacyjnych Windows obsługiwanych przez MMA.

#### <a name="install-the-agent-on-a-linux-machine"></a>Instalowanie agenta na komputerze z systemem Linux

Aby zainstalować agenta na komputerze z systemem Linux:

1. Przenieś odpowiedni zbiór (x86 lub x64) na komputer z systemem Linux przy użyciu protokołu SCP/SFTP.
2. Zainstaluj pakiet przy użyciu argumentu--install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[Dowiedz się więcej](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-linux-operating-systems) o obsłudze systemu operacyjnego Linux przez program MMA. 

### <a name="install-the-dependency-agent"></a>Instalowanie agenta zależności
1. Aby zainstalować agenta zależności na komputerze z systemem Windows, kliknij dwukrotnie plik Instalatora i postępuj zgodnie z instrukcjami kreatora.
2. Aby zainstalować agenta zależności na komputerze z systemem Linux, należy zainstalować program jako katalog główny przy użyciu następującego polecenia:

    ```sh InstallDependencyAgent-Linux64.bin```

[Dowiedz się więcej](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-hybrid-cloud#installation-script-examples) na temat sposobu instalowania agenta zależności za pomocą skryptów.

[Dowiedz się więcej](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#supported-operating-systems) o systemach operacyjnych obsługiwanych przez agenta zależności.


## <a name="create-a-group-using-dependency-visualization"></a>Tworzenie grupy przy użyciu wizualizacji zależności

1. W **Azure Migrate: Ocena serwera**, kliknij przycisk **odnalezione serwery**.
2. W kolumnie **zależności** kliknij pozycję **Wyświetl zależności** dla każdej maszyny, którą chcesz przejrzeć.
3. Na mapie zależności można zobaczyć następujące elementy:
    - Połączenia TCP przychodzące (klienckie) i wychodzące (serwery), do i z komputera.
    - Maszyny zależne, na których nie zainstalowano agentów zależności, są pogrupowane według numerów portów.
    - Komputery zależne z zainstalowanymi agentami zależności są wyświetlane jako oddzielne pola.
    - Procesy działające na maszynie. Rozwiń wszystkie pola maszyn, aby wyświetlić procesy.
    - Właściwości maszyny (w tym nazwa FQDN, system operacyjny, adres MAC). Kliknij każde pole komputera, aby wyświetlić szczegóły.

4. Można przyjrzeć się zależnościom dla różnych czasów trwania, klikając czas trwania w etykiecie zakresu czasu. Domyślnie zakresem jest godzina. Możesz zmodyfikować zakres czasu lub określić datę początkową i końcową oraz czas trwania.

    > [!NOTE]
    > Zakres czasu może być maksymalnie godzinę. Jeśli potrzebujesz dłuższego zakresu, użyj Azure Monitor do wykonywania zapytań dotyczących danych zależnych przez dłuższy czas.

5. Po zidentyfikowaniu maszyn zależnych, które mają być pogrupowane, użyj kombinacji klawiszy CTRL i kliknięcia, aby wybrać wiele maszyn na mapie, a następnie kliknij pozycję **Grupuj maszyny**.
6. Określ nazwę grupy.
7. Sprawdź, czy maszyny zależne są odnajdywane przez Azure Migrate.

    - Jeśli maszyna zależna nie zostanie odnaleziona przez Azure Migrate: Ocena serwera, nie można dodać jej do grupy.
    - Aby dodać maszynę, ponownie uruchom odnajdywanie i sprawdź, czy komputer został odnaleziony.

8. Jeśli chcesz utworzyć ocenę dla tej grupy, zaznacz pole wyboru, aby utworzyć nową ocenę dla grupy.
8. Kliknij przycisk **OK** , aby zapisać grupę.

Po utworzeniu grupy zalecamy zainstalowanie agentów na wszystkich komputerach w grupie, a następnie wizualizowanie zależności dla całej grupy.

## <a name="query-dependency-data-in-azure-monitor"></a>Wykonywanie zapytań względem danych zależności w Azure Monitor

Możesz badać dane zależności przechwycone przez Service Map w obszarze roboczym Log Analytics skojarzonym z projektem Azure Migrate. Log Analytics służy do zapisywania i uruchamiania zapytań dziennika Azure Monitor.

- [Dowiedz się, jak](../azure-monitor/insights/service-map.md#log-analytics-records) wyszukiwać Service map dane w log Analytics.
- [Zapoznaj się z omówieniem](../azure-monitor/log-query/get-started-queries.md) zapisywania zapytań dzienników w [log Analytics](../azure-monitor/log-query/get-started-portal.md).

Uruchom zapytanie dotyczące danych zależności w następujący sposób:

1. Po zainstalowaniu agentów przejdź do portalu, a następnie kliknij pozycję **Przegląd**.
2. W **Azure Migrate: Ocena serwera**, kliknij przycisk **Przegląd**. Kliknij strzałkę w dół, aby rozwinąć **podstawowe**elementy.
3. W **obszarze roboczym pakietu OMS**kliknij nazwę obszaru roboczego.
3. Na stronie obszaru roboczego Log Analytics > **Ogólne**kliknij pozycję **dzienniki**.
4. Napisz zapytanie i kliknij przycisk **Uruchom**.

### <a name="sample-queries"></a>Przykładowe zapytania

Udostępniamy przykładowe zapytania, których można użyć do wyodrębnienia danych zależności.

- Zapytania można modyfikować w celu wyodrębnienia preferowanych punktów danych.
- [Zapoznaj](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) się z pełną listą rekordów danych zależności.
- [Przejrzyj](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches) dodatkowe przykładowe zapytania.

#### <a name="sample-review-inbound-connections"></a>Przykład: przeglądanie połączeń przychodzących

Przejrzyj połączenia przychodzące dla zestawu maszyn wirtualnych.

- Rekordy w tabeli dla metryk połączeń (VMConnection) nie reprezentują poszczególnych fizycznych połączeń sieciowych.
- Wiele połączeń sieci fizycznych jest zgrupowanych w połączenie logiczne.
- [Dowiedz się więcej](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections) o tym, jak dane połączenia sieci fizycznej są agregowane w VMConnection.

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

#### <a name="sample-summarize-sent-and-received-data"></a>Przykład: Podsumowanie wysłanych i odebranych danych

Ten przykład zawiera podsumowanie ilości danych wysłanych i odebranych w połączeniach przychodzących między zestawem maszyn.

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

[Utwórz ocenę](how-to-create-assessment.md) dla grupy.
