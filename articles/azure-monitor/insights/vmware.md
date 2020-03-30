---
title: Rozwiązanie do monitorowania VMware w usłudze Azure Monitor | Dokumenty firmy Microsoft
description: Dowiedz się, jak rozwiązanie do monitorowania VMware może pomóc w zarządzaniu dziennikami i monitorowaniu hostów ESXi.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/04/2018
ms.openlocfilehash: c1622ef16155206d779c6d703fc7da568d233e7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77664783"
---
# <a name="vmware-monitoring-deprecated-solution-in-azure-monitor"></a>Rozwiązanie do monitorowania vmware (przestarzałe) w usłudze Azure Monitor

![Symbol VMware](./media/vmware/vmware-symbol.png)

> [!NOTE]
> Rozwiązanie do monitorowania VMware zostało przestarzałe.  Klienci, którzy już zainstalowali rozwiązanie, mogą nadal z niego korzystać, ale nie można dodać monitorowania VMware do żadnych nowych obszarów roboczych.

Rozwiązanie do monitorowania VMware w usłudze Azure Monitor to rozwiązanie ułatwiające tworzenie scentralizowanego podejścia do rejestrowania i monitorowania dużych dzienników VMware. W tym artykule opisano, jak można rozwiązywać problemy, przechwytywać i zarządzać hostami ESXi w jednej lokalizacji przy użyciu rozwiązania. Dzięki rozwiązaniu możesz zobaczyć szczegółowe dane dla wszystkich hostów ESXi w jednej lokalizacji. Możesz zobaczyć najważniejsze liczby zdarzeń, stan i trendy hostów maszyn wirtualnych i ESXi udostępniane za pośrednictwem dzienników hostów ESXi. Rozwiązywanie problemów można rozwiązywać, wyświetlając i wyszukując scentralizowane dzienniki hostów ESXi. Można też tworzyć alerty na podstawie zapytań wyszukiwania dziennika.

Rozwiązanie używa natywnej funkcji syslog hosta ESXi do wypychania danych do docelowej maszyny Wirtualnej, która ma agenta usługi Log Analytics. Jednak rozwiązanie nie zapisuje plików do syslogu w docelowej maszynie Wirtualnej. Agent usługi Log Analytics otwiera port 1514 i nasłuchuje tego. Po odebraniu danych agent usługi Log Analytics wypycha dane do usługi Azure Monitor.

## <a name="install-and-configure-the-solution"></a>Instalowanie i konfigurowanie rozwiązania
Skorzystaj z poniższych informacji, aby zainstalować i skonfigurować rozwiązanie.

* Dodaj rozwiązanie do monitorowania VMware do subskrypcji przy użyciu procesu [opisanego](../insights/solutions.md#install-a-monitoring-solution)w install a monitoring solution .

#### <a name="supported-vmware-esxi-hosts"></a>Obsługiwane hosty VMware ESXi
VSphere ESXi Host 5.5, 6.0 i 6.5

#### <a name="prepare-a-linux-server"></a>Przygotowanie serwera systemu Linux
Utwórz maszynę wirtualną systemu operacyjnego Linux, aby odbierać wszystkie dane syslogu z hostów ESXi. [Agent systemu Linux usługi Log Analytics](../learn/quick-collect-linux-computer.md) jest punktem zbierania wszystkich danych syslogu hosta ESXi. Można użyć wielu hostów ESXi do przekazywania dzienników do jednego serwera Linux, jak w poniższym przykładzie.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

   ![przepływ syslog](./media/vmware/diagram.png)

### <a name="configure-syslog-collection"></a>Konfigurowanie kolekcji syslog
1. Skonfiguruj przekazywanie syslogu dla VSphere. Aby uzyskać szczegółowe informacje ułatwiające konfigurowanie przekazywania syslogu, zobacz [Konfigurowanie syslogu w ESXi 5.0 i nowszym (2003322)](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2003322). Przejdź do **ESXi Host Configuration** > **Software** > **Advanced Settings** > **Syslog**.
   ![vsphereconfig](./media/vmware/vsphere1.png)  
1. W polu *Syslog.global.logHost* dodaj serwer Linux i numer portu *1514*. Na przykład `tcp://hostname:1514` lub `tcp://123.456.789.101:1514`
1. Otwórz zaporę hosta ESXi dla syslogu. **Zapora** > **profilu****Firewall**  >  > zabezpieczeń oprogramowania konfiguracji**hosta**ESXi i otwórz **właściwości**.  

    ![vspherefw ( vspherefw )](./media/vmware/vsphere2.png)  

    ![vspherefwproperties](./media/vmware/vsphere3.png)  
1. Sprawdź konsolę vSphere, aby sprawdzić, czy syslog jest poprawnie skonfigurowany. Upewnij się na hoście ESXI, że port **1514** jest skonfigurowany.
1. Pobierz i zainstaluj agenta log analytics dla systemu Linux na serwerze Linux. Aby uzyskać więcej informacji, zobacz [dokumentację agenta analizy dzienników dla systemu Linux](https://github.com/Microsoft/OMS-Agent-for-Linux).
1. Po zainstalowaniu agenta Log Analytics dla systemu Linux przejdź do katalogu /etc/opt/microsoft/omsagent/sysconf/omsagent.d i skopiuj plik vmware_esxi.conf do katalogu /etc/opt/microsoft/omsagent/conf/omsagent.d oraz zmień właściciela/grupę i uprawnienia pliku. Przykład:

    ```
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/vmware_esxi.conf /etc/opt/microsoft/omsagent/conf/omsagent.d
   sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf
    ```
1. Uruchom ponownie agenta usługi `sudo /opt/microsoft/omsagent/bin/service_control restart`Log Analytics dla systemu Linux, uruchamiając plik .
1. Przetestuj łączność między serwerem Linux a `nc` hostem ESXi za pomocą polecenia na hoście ESXi. Przykład:

    ```
    [root@ESXiHost:~] nc -z 123.456.789.101 1514
    Connection to 123.456.789.101 1514 port [tcp/*] succeeded!
    ```

1. W witrynie Azure portal wykonaj `VMware_CL`kwerendę dziennika dla . Gdy usługa Azure Monitor zbiera dane syslog, zachowuje format syslog. W portalu są przechwytywane niektóre określone pola, takie jak *Nazwa hosta* i *Nazwa procesu*.  

    ![type](./media/vmware/type.png)  

    Jeśli wyniki wyszukiwania dziennika widoku są podobne do powyższego obrazu, można ustawić użycie pulpitu nawigacyjnego rozwiązania do monitorowania VMware.  

## <a name="vmware-data-collection-details"></a>Szczegóły zbierania danych VMware
Rozwiązanie do monitorowania VMware zbiera różne metryki wydajności i dane dziennika z hostów ESXi przy użyciu agentów usługi Log Analytics dla systemu Linux, które zostały włączone.

W poniższej tabeli przedstawiono metody zbierania danych i inne szczegóły dotyczące sposobu zbierania danych.

| platforma | Agent analizy dzienników dla systemu Linux | Agent SCOM | Azure Storage | SCOM wymagane? | Dane agenta SCOM przesyłane za pośrednictwem grupy zarządzania | częstotliwość zbierania |
| --- | --- | --- | --- | --- | --- | --- |
| Linux |&#8226; |  |  |  |  |co 3 minuty |

W poniższej tabeli przedstawiono przykłady pól danych zebranych przez rozwiązanie do monitorowania VMware:

| nazwa pola | description |
| --- | --- |
| Device_s |Urządzenia pamięci masowej VMware |
| ESXIFailure_s |typy awarii |
| EventTime_t |czas, w którym miało miejsce zdarzenie |
| HostName_s |Nazwa hosta ESXi |
| Operation_s |tworzenie maszyny Wirtualnej lub usuwanie maszyny Wirtualnej |
| ProcessName_s |nazwa zdarzenia |
| ResourceId_s |nazwa hosta VMware |
| ResourceLocation_s |VMware |
| ResourceName_s |VMware |
| ResourceType_s |Funkcja Hyper-V |
| SCSIStatus_s |Stan VMware SCSI |
| SyslogMessage_s |Dane syslogu |
| UserName_s |użytkownik, który utworzył lub usunął maszynę wirtualną |
| VMName_s |Nazwa maszyny wirtualnej |
| Computer (Komputer) |komputer hosta |
| TimeGenerated |czas wygenerowania danych |
| DataCenter_s |Centrum danych VMware |
| StorageLatency_s |opóźnienie magazynu (ms) |

## <a name="vmware-monitoring-solution-overview"></a>Omówienie rozwiązania do monitorowania VMware
Kafelek VMware pojawi się w obszarze roboczym usługi Log Analytics. Zapewnia widok wysokiego poziomu wszelkich błędów. Po kliknięciu kafelka przejdź do widoku pulpitu nawigacyjnego.

![kafelek](./media/vmware/tile.png)

#### <a name="navigate-the-dashboard-view"></a>Poruszanie się po widoku pulpitu nawigacyjnego
W widoku pulpitu nawigacyjnego **VMware** ostrza są uporządkowane według:

* Liczba stanów awarii
* Najpopularniejszy gospodarz według liczby zdarzeń
* Liczba najważniejszych zdarzeń
* Działania maszyny wirtualnej
* Zdarzenia dysku hosta ESXi

![rozwiązanie1](./media/vmware/solutionview1-1.png)

![rozwiązanie2](./media/vmware/solutionview1-2.png)

Kliknij dowolny blok, aby otworzyć okienko wyszukiwania usługi Log Analytics, które zawiera szczegółowe informacje specyficzne dla bloku.

W tym miejscu można edytować kwerendę dziennika, aby zmodyfikować ją pod kątem określonego. Aby uzyskać szczegółowe informacje na temat tworzenia zapytań dziennika, zobacz [Znajdowanie danych przy użyciu zapytań dziennika w usłudze Azure Monitor](../log-query/log-query-overview.md).

#### <a name="find-esxi-host-events"></a>Znajdź wydarzenia hosta ESXi
Pojedynczy host ESXi generuje wiele dzienników na podstawie ich procesów. Rozwiązanie do monitorowania VMware centralizuje je i podsumowuje liczbę zdarzeń. Ten scentralizowany widok pomaga zrozumieć, który host ESXi ma dużą liczbę zdarzeń i jakie zdarzenia występują najczęściej w twoim środowisku.

![event](./media/vmware/events.png)

Można drążyć dalej, klikając hosta ESXi lub typu zdarzenia.

Kliknięcie nazwy hosta ESXi umożliwia wyświetlanie informacji z tego hosta ESXi. Jeśli chcesz zawęzić wyniki `“ProcessName_s=EVENT TYPE”` z typem zdarzenia, dodaj zapytanie wyszukiwania. W filtrze wyszukiwania można wybrać **processname.** To zawęża informacje dla Ciebie.

![Wiertła](./media/vmware/eventhostdrilldown.png)

#### <a name="find-high-vm-activities"></a>Znajdowanie wysokich aktywności maszyn wirtualnych
Maszynę wirtualną można utworzyć i usunąć na dowolnym hoście ESXi. Jest to przydatne dla administratora, aby określić, ile maszyn wirtualnych tworzy host ESXi. To z kolei pomaga zrozumieć wydajność i planowanie pojemności. Śledzenie zdarzeń aktywności maszyny Wirtualnej ma kluczowe znaczenie podczas zarządzania środowiskiem.

![Wiertła](./media/vmware/vmactivities1.png)

Jeśli chcesz wyświetlić dodatkowe dane tworzenia maszyny wirtualnej hosta ESXi, kliknij nazwę hosta ESXi.

![Wiertła](./media/vmware/createvm.png)

#### <a name="common-log-queries"></a>Typowe zapytania dziennika
Rozwiązanie zawiera inne przydatne zapytania, które mogą pomóc w zarządzaniu hostami ESXi, takie jak duża ilość miejsca do magazynowania, opóźnienie magazynu i błąd ścieżki.

![Kwerendy](./media/vmware/queries.png)


#### <a name="save-queries"></a>Zapisywanie zapytań
Zapisywanie zapytań dziennika jest standardową funkcją w usłudze Azure Monitor i może pomóc zachować wszelkie zapytania, które zostały uznane za przydatne. Po utworzeniu kwerendy, która okaże się przydatna, zapisz ją, klikając **przycisk Ulubione**. Zapisane zapytanie umożliwia łatwe ponowne użycie go później na stronie [Mój pulpit nawigacyjny,](../learn/tutorial-logs-dashboards.md) na której można tworzyć własne niestandardowe pulpity nawigacyjne.

![Widok dockerdashboard](./media/vmware/dockerdashboardview.png)

#### <a name="create-alerts-from-queries"></a>Tworzenie alertów z zapytań
Po utworzeniu kwerend, można użyć kwerendy, aby ostrzec, gdy wystąpią określone zdarzenia. Zobacz [alerty w usłudze Log Analytics,](../platform/alerts-overview.md) aby uzyskać informacje na temat tworzenia alertów. Aby uzyskać przykłady alertów kwerend i innych przykładów zapytań, zobacz [Monitor VMware przy użyciu wpisu](https://blogs.technet.microsoft.com/msoms/2016/06/15/monitor-vmware-using-oms-log-analytics) w blogu usługi Log Analytics.

## <a name="frequently-asked-questions"></a>Często zadawane pytania
### <a name="what-do-i-need-to-do-on-the-esxi-host-setting-what-impact-will-it-have-on-my-current-environment"></a>Co muszę zrobić w ustawieniach hosta ESXi? Jaki będzie to miało wpływ na moje obecne środowisko?
Rozwiązanie wykorzystuje natywnego mechanizmu przekazywania hosta ESXi. Do przechwytywania dzienników nie jest potrzebne żadne dodatkowe oprogramowanie firmy Microsoft na hoście ESXi Host. Powinien mieć niewielki wpływ na istniejące środowisko. Jednak trzeba ustawić syslog przekazywania, który jest funkcją ESXI.

### <a name="do-i-need-to-restart-my-esxi-host"></a>Czy muszę ponownie uruchomić hosta ESXi?
Nie. Ten proces nie wymaga ponownego uruchomienia. Czasami vSphere nie poprawnie zaktualizować syslog. W takim przypadku zaloguj się do hosta ESXi i przeładuj syslog. Ponownie nie trzeba ponownie uruchomić hosta, więc ten proces nie jest uciążliwy dla środowiska.

### <a name="can-i-increase-or-decrease-the-volume-of-log-data-sent-to-log-analytics"></a>Czy mogę zwiększyć lub zmniejszyć ilość danych dziennika wysyłanych do usługi Log Analytics?
Tak. Ustawienia poziomu dziennika hosta ESXi można używać w vSphere. Zbieranie dzienników jest na podstawie poziomu *informacji.* Tak więc, jeśli chcesz przeprowadzić inspekcję tworzenia lub usuwania maszyn wirtualnych, musisz zachować poziom *informacji* na hostach. Aby uzyskać więcej informacji, zobacz [bazę wiedzy VMware](https://kb.vmware.com/selfservice/microsites/search.do?&cmd=displayKC&externalId=1017658).

### <a name="why-is-hostd-not-providing-data-to-log-analytics-my-log-setting-is-set-to-info"></a>Dlaczego hostd nie dostarcza danych do usługi Log Analytics? Moje ustawienie dziennika jest ustawione na info.
Wystąpił błąd hosta ESXi dla sygnatury czasowej syslogu. Aby uzyskać więcej informacji, zobacz [bazę wiedzy VMware](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2111202). Po zastosowaniu obejścia hostd powinien działać normalnie.

### <a name="can-i-have-multiple-esxi-hosts-forwarding-syslog-data-to-a-single-vm-with-omsagent"></a>Czy mogę mieć wiele hostów ESXi przekazywania danych syslog do jednej maszyny Wirtualnej z omsagent?
Tak. Można mieć wiele hostów ESXi przekazywania do jednej maszyny Wirtualnej z omsagent.

### <a name="why-dont-i-see-data-flowing-into-log-analytics"></a>Dlaczego nie widzę danych płynących do usługi Log Analytics?
Przyczyn może być wiele:

* Host ESXi nie jest poprawnie wypychanie danych do maszyny Wirtualnej z systemem omsagent. Aby przetestować, wykonaj następujące czynności:

  1. Aby potwierdzić, zaloguj się do hosta ESXi za pomocą ssh i uruchom następujące polecenie:`nc -z ipaddressofVM 1514`

      Jeśli to się nie powiedzie, ustawienia vSphere w konfiguracji zaawansowanej prawdopodobnie nie są poprawne. Zobacz [Konfigurowanie kolekcji syslog,](#configure-syslog-collection) aby uzyskać informacje dotyczące konfigurowania hosta ESXi do przekazywania syslogu.
  1. Jeśli łączność z portem syslog zakończy się pomyślnie, ale nadal nie widzisz żadnych danych, a następnie ponownie załaduj syslog na hoście ESXi za pomocą ssh, aby uruchomić następujące polecenie:`esxcli system syslog reload`
* Maszyna wirtualna z agentem usługi Log Analytics nie jest ustawiona poprawnie. Aby to sprawdzić, wykonaj następujące czynności:

  1. Usługa Log Analytics nasłuchuje portu 1514. Aby sprawdzić, czy jest otwarty, uruchom następujące polecenie:`netstat -a | grep 1514`
  1. Powinien zostać `1514/tcp` otwarty port. Jeśli nie, sprawdź, czy omsagent jest zainstalowany poprawnie. Jeśli informacje o porcie nie są widoczne, port syslog nie jest otwarty na maszynie wirtualnej.

    a. Sprawdź, czy agent usługi Log `ps -ef | grep oms`Analytics jest uruchomiony przy użyciu programu . Jeśli nie jest uruchomiony, uruchom proces, uruchamiając polecenie`sudo /opt/microsoft/omsagent/bin/service_control start`

     b. Otwórz plik `/etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf`.

     d. Sprawdź, czy prawidłowe ustawienie użytkownika i grupy jest prawidłowe, podobnie jak:`-rw-r--r-- 1 omsagent omiusers 677 Sep 20 16:46 vmware_esxi.conf`

     d. Jeśli plik nie istnieje lub ustawienie użytkownika i grupy jest nieprawidłowe, należy podjąć działania naprawcze, [przygotowując serwer Linux](#prepare-a-linux-server).

## <a name="next-steps"></a>Następne kroki
* Użyj [zapytań dziennika](../log-query/log-query-overview.md) w usłudze Log Analytics, aby wyświetlić szczegółowe dane hosta VMware.
* [Twórz własne pulpity nawigacyjne przedstawiające](../learn/tutorial-logs-dashboards.md) dane hosta VMware.
* [Tworzenie alertów,](../platform/alerts-overview.md) gdy wystąpią określone zdarzenia hosta VMware.
