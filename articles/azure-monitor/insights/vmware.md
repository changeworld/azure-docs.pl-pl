---
title: VMware Monitoring rozwiązanie w Azure Monitor | Microsoft Docs
description: Dowiedz się więcej o tym, jak to rozwiązanie monitorowanie VMware mogą ułatwić zarządzanie dziennikami i monitorowanie hostów ESXi.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/04/2018
ms.openlocfilehash: c1622ef16155206d779c6d703fc7da568d233e7e
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77664783"
---
# <a name="vmware-monitoring-deprecated-solution-in-azure-monitor"></a>Rozwiązanie VMware Monitoring (przestarzałe) w Azure Monitor

![Symbol programu VMware](./media/vmware/vmware-symbol.png)

> [!NOTE]
> To rozwiązanie monitorowanie VMware jest przestarzała.  Klienci, którzy już zainstalowano rozwiązania można nadal z niego korzystać, ale monitorowanie VMware nie można dodać do żadnych nowych obszarów roboczych.

VMware Monitoring rozwiązanie w Azure Monitor to rozwiązanie, które ułatwia tworzenie scentralizowanego podejścia do rejestrowania i monitorowania dużych dzienników VMware. W tym artykule opisano, jak można rozwiązać, przechwytywania i zarządzać hostów ESXi w obrębie jednej lokalizacji za pomocą rozwiązania. Dzięki rozwiązaniu można poznać szczegółowe dane dla wszystkich hostów ESXi w obrębie jednej lokalizacji. Widać, liczby najważniejszych zdarzeń, stanu i trendów maszyn wirtualnych i ESXi hostów, oferowana w ramach dzienniki hostów ESXi. Można rozwiązać, wyświetlania i przeszukiwania scentralizowanych dzienników hostów ESXi. Ponadto możesz utworzyć alerty w oparciu o zapytań funkcji przeszukiwania dzienników.

Rozwiązanie korzysta z funkcji natywnej syslog hosta ESXi w celu wypychania danych do docelowej maszyny Wirtualnej, która ma agenta usługi Log Analytics. Jednak to rozwiązanie nie zapisywać pliki do usługi syslog w ramach docelowej maszyny Wirtualnej. Agent usługi Log Analytics otwiera port 1514 i nasłuchuje tej. Po odebraniu danych Agent Log Analytics wypycha dane do Azure Monitor.

## <a name="install-and-configure-the-solution"></a>Instalowanie i konfigurowanie rozwiązania
Skorzystaj z poniższych informacji, aby zainstalować i skonfigurować rozwiązanie.

* Dodaj rozwiązanie VMware Monitoring do subskrypcji przy użyciu procesu opisanego w artykule [Instalowanie rozwiązania monitorowania](../insights/solutions.md#install-a-monitoring-solution).

#### <a name="supported-vmware-esxi-hosts"></a>Obsługiwane hosty VMware ESXi
vSphere hosta ESXi 5.5, 6.0 i 6.5

#### <a name="prepare-a-linux-server"></a>Przygotowanie na serwerze z systemem Linux
Utwórz system operacyjny Linux maszyny Wirtualnej, aby otrzymywać wszystkie dane usługi syslog hostów ESXi. [Agent log Analytics Linux](../learn/quick-collect-linux-computer.md) to punkt kolekcji dla wszystkich danych dziennika systemowego hosta ESXi. Do przekazywania dzienników na jednym serwerze z systemem Linux, jak w poniższym przykładzie, można użyć wielu hostach ESXi.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

   ![Przepływ usługi SYSLOG](./media/vmware/diagram.png)

### <a name="configure-syslog-collection"></a>Konfiguruj zbieranie dzienników systemowych
1. Konfigurowanie przekazywania usługi syslog dla VSphere. Aby uzyskać szczegółowe informacje ułatwiające skonfigurowanie przekazywania dziennika systemu, zobacz [Konfigurowanie dziennika systemowego na ESXi 5,0 i wyższych (2003322)](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2003322). Przejdź do pozycji **Konfiguracja hosta ESXi** > **Software** > **Settings Advanced** > **dziennika**systemowego.
   ![vsphereconfig](./media/vmware/vsphere1.png)  
1. W polu *Dziennik system. Global. logHost* Dodaj serwer z systemem Linux i numer portu *1514*. Na przykład `tcp://hostname:1514` lub `tcp://123.456.789.101:1514`
1. Otwórz zaporę hosta ESXi, dla dziennika systemowego. **Konfiguracja hosta ESXi** > **oprogramowania** > **profilu zabezpieczeń** > **Zapora** i Otwórz **Właściwości**.  

    ![vspherefw](./media/vmware/vsphere2.png)  

    ![vspherefwproperties](./media/vmware/vsphere3.png)  
1. Sprawdź vSphere konsoli, aby sprawdzić, czy ten syslog nie jest prawidłowo skonfigurowany. Na hoście ESXI upewnij się, że skonfigurowano port **1514** .
1. Pobierz i zainstaluj agenta usługi Log Analytics dla systemu Linux na serwerze z systemem Linux. Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją dla programu log Analytics Agent dla systemu Linux](https://github.com/Microsoft/OMS-Agent-for-Linux).
1. Po agenta usługi Log Analytics dla systemu Linux jest zainstalowana, przejdź do katalogu /etc/opt/microsoft/omsagent/sysconf/omsagent.d i skopiuj vmware_esxi.conf plik do katalogu /etc/opt/microsoft/omsagent/conf/omsagent.d i zmiany właściciela/grupę i uprawnienia do pliku. Na przykład:

    ```
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/vmware_esxi.conf /etc/opt/microsoft/omsagent/conf/omsagent.d
   sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf
    ```
1. Uruchom ponownie agenta Log Analytics dla systemu Linux, uruchamiając `sudo /opt/microsoft/omsagent/bin/service_control restart`.
1. Przetestuj połączenie między serwerem z systemem Linux i hostem ESXi przy użyciu polecenia `nc` na hoście ESXi. Na przykład:

    ```
    [root@ESXiHost:~] nc -z 123.456.789.101 1514
    Connection to 123.456.789.101 1514 port [tcp/*] succeeded!
    ```

1. W Azure Portal wykonaj zapytanie dziennika dla `VMware_CL`. Gdy Azure Monitor zbiera dane dziennika systemu, zachowuje format dziennika systemowego. W portalu są przechwytywane określone pola, takie jak *Nazwa hosta* i *procesname*.  

    ![type](./media/vmware/type.png)  

    Jeśli wyświetlanie wyników wyszukiwania dziennika są podobne do powyższej ilustracji, gotowe do użycia pulpit nawigacyjny rozwiązania monitorowanie VMware.  

## <a name="vmware-data-collection-details"></a>Szczegóły zbierania danych VMware
To rozwiązanie monitorowanie VMware zbiera różne dane metryk i dzienników wydajności z hostów ESXi przy użyciu agentów usługi Log Analytics dla systemu Linux, które mają włączone.

W poniższej tabeli przedstawiono metody zbierania danych i inne szczegóły dotyczące sposobu zbierania danych.

| Platforma | Log Analytics agent dla systemu Linux | Agenta programu SCOM | Azure Storage | SCOM wymagane? | Dane agenta programu SCOM wysyłane za pośrednictwem grupy zarządzania | Częstotliwość zbierania |
| --- | --- | --- | --- | --- | --- | --- |
| Linux |&#8226; |  |  |  |  |co 3 minuty |

Poniższa tabela wyświetlanie przykładów pól danych zbieranych przez rozwiązanie monitorowanie VMware:

| Nazwa pola | description |
| --- | --- |
| Device_s |Urządzenia magazynujące programu VMware |
| ESXIFailure_s |typy błędów |
| EventTime_t |Godzina wystąpienia zdarzenia |
| HostName_s |Nazwa hosta ESXi |
| Operation_s |Tworzenie maszyny Wirtualnej lub Usuń maszynę Wirtualną |
| ProcessName_s |Nazwa zdarzenia |
| ResourceId_s |Nazwa hosta VMware |
| ResourceLocation_s |VMware |
| ResourceName_s |VMware |
| ResourceType_s |Funkcja Hyper-V |
| SCSIStatus_s |Stan VMware SCSI |
| SyslogMessage_s |Dane usługi SYSLOG |
| UserName_s |Użytkownik, który utworzeniu lub usunięciu maszyny Wirtualnej |
| VMName_s |Nazwa maszyny wirtualnej |
| Computer |komputer-host |
| TimeGenerated |czas wygenerowania danych |
| DataCenter_s |Centrum danych VMware |
| StorageLatency_s |Magazyn, opóźnienie (ms) |

## <a name="vmware-monitoring-solution-overview"></a>Monitorowanie VMware — Omówienie rozwiązania
Na kafelku VMware pojawia się w obszarze roboczym usługi Log Analytics. Zapewnia ogólny widok wszystkich błędów. Po kliknięciu kafelka, przejdź do widoku pulpitu nawigacyjnego.

![kafelek](./media/vmware/tile.png)

#### <a name="navigate-the-dashboard-view"></a>Przejdź do widoku pulpitu nawigacyjnego
W widoku pulpitu nawigacyjnego **VMware** bloki są zorganizowane według:

* Liczba stanu błędu
* Najważniejsze hosta według liczb zdarzeń
* Liczby najważniejszych zdarzeń
* Działania maszyny wirtualnej
* Zdarzenia dotyczące dysków hostów ESXi

![solution1](./media/vmware/solutionview1-1.png)

![solution2](./media/vmware/solutionview1-2.png)

Kliknij przycisk bloku, aby otworzyć okienko wyszukiwania usługi Log Analytics, która zawiera szczegółowe informacje specyficzne dla bloku.

W tym miejscu można edytować zapytanie dziennika w celu zmodyfikowania go dla określonego elementu. Aby uzyskać szczegółowe informacje na temat tworzenia zapytań dziennika, zobacz [Znajdowanie danych przy użyciu zapytań dzienników w Azure monitor](../log-query/log-query-overview.md).

#### <a name="find-esxi-host-events"></a>Znajdź wydarzenia hosta ESXi
Jeden host ESXi generuje wiele dzienników, w oparciu o ich procesów. To rozwiązanie monitorowanie VMware centralizuje je i zawiera podsumowanie liczby zdarzeń. To scentralizowany widok pomaga zrozumieć, które hostów ESXi ma dużej liczby zdarzeń i jakie zdarzenia występują najczęściej w danym środowisku.

![zdarzenie](./media/vmware/events.png)

Możesz przejść do dalszych klikając hosta ESXi lub typu zdarzenia.

Po kliknięciu nazwy hosta ESXi, możesz przeglądać informacje z tego hosta ESXi. Jeśli chcesz zawęzić wyniki przy użyciu typu zdarzenia, Dodaj `“ProcessName_s=EVENT TYPE”` w zapytaniu wyszukiwania. W filtrze wyszukiwania można wybrać wartość **ProcessName** . Który zawęża informacje dla Ciebie.

![Przechodzenie do szczegółów](./media/vmware/eventhostdrilldown.png)

#### <a name="find-high-vm-activities"></a>Znajdź wysokiej działania maszyny Wirtualnej
Maszyny wirtualne można tworzyć i usunięty na żadnym hoście ESXi. Jest to przydatne dla administratora zidentyfikować ile maszyn wirtualnych, które tworzy hosta ESXi. Tego umożliwiłoby, ułatwia zrozumienie wydajności i planowania pojemności. Rejestrowanie informacji o zdarzeń działania maszyny Wirtualnej jest niezwykle istotne w przypadku, gdy zarządzania środowiskiem.

![Przechodzenie do szczegółów](./media/vmware/vmactivities1.png)

Jeśli chcesz wyświetlić dodatkowe dane tworzenia maszyny Wirtualnej hosta ESXi, kliknij nazwę hosta ESXi.

![Przechodzenie do szczegółów](./media/vmware/createvm.png)

#### <a name="common-log-queries"></a>Typowe zapytania dzienników
Rozwiązanie zawiera inne przydatne zapytania, które mogą pomóc Ci w zarządzaniu hostów ESXi, takie jak miejsce do magazynowania o wysokiej, opóźnienie magazynu i ścieżka błędu.

![zapytania](./media/vmware/queries.png)


#### <a name="save-queries"></a>Zapisywanie zapytań
Zapisywanie zapytań dzienników jest standardową funkcją w Azure Monitor i ułatwia zachowanie wszelkich zapytań, które okazały się przydatne. Po utworzeniu kwerendy, która jest przydatna, Zapisz ją, klikając przycisk **Ulubione**. Zapisane zapytanie pozwala z łatwością użyć go później na stronie [mój pulpit nawigacyjny](../learn/tutorial-logs-dashboards.md) , na której można tworzyć własne niestandardowe pulpity nawigacyjne.

![DockerDashboardView](./media/vmware/dockerdashboardview.png)

#### <a name="create-alerts-from-queries"></a>Tworzenie alertów na podstawie zapytania
Po utworzeniu zapytania, można użyć zapytań ostrzega w przypadku wystąpienia określonych zdarzeń. Aby uzyskać informacje o sposobach tworzenia alertów, zobacz [alerty w log Analytics](../platform/alerts-overview.md) . Przykłady zapytań dotyczących alertów i innych przykładów zapytań można znaleźć w blogu [monitorowanie oprogramowania VMware przy użyciu log Analytics](https://blogs.technet.microsoft.com/msoms/2016/06/15/monitor-vmware-using-oms-log-analytics) .

## <a name="frequently-asked-questions"></a>Często zadawane pytania
### <a name="what-do-i-need-to-do-on-the-esxi-host-setting-what-impact-will-it-have-on-my-current-environment"></a>Co muszę na ESXi hosta ustawienie? Jaki wpływ będzie miał na Moje bieżącego środowiska?
Rozwiązanie używa natywnego Syslog hosta ESXi, mechanizm przekazywania. Nie potrzebujesz żadnego dodatkowego oprogramowania firmy Microsoft na hoście ESXi w celu przechwytywania dzienników. Powinien on mały wpływ na istniejącym środowiskiem. Jednakże należy ustawić przekazywania usługi syslog, czyli funkcji ESXI.

### <a name="do-i-need-to-restart-my-esxi-host"></a>Należy ponownie uruchomić mój hosta ESXi?
Nie. Ten proces nie wymaga ponownego uruchomienia komputera. Czasami vSphere, nie jest poprawnie aktualizowany syslog. W takim przypadku należy zalogować się na hoście ESXi i ponownie załaduj syslog. Nie trzeba ponownie, uruchom ponownie hosta, więc ten proces nie jest znaczący wpływ na środowisko.

### <a name="can-i-increase-or-decrease-the-volume-of-log-data-sent-to-log-analytics"></a>Można zwiększyć lub zmniejszyć ilość danych dziennika wysyłane do usługi Log Analytics?
Tak, możesz to zrobić. Umożliwia ustawienie poziomie dzienników hostów ESXi w vSphere. Zbieranie dzienników opiera się na poziomie *informacji* . Aby przeprowadzić inspekcję tworzenia lub usuwania maszyn wirtualnych, należy zachować poziom *informacji* na hoście. Aby uzyskać więcej informacji, zobacz [bazę wiedzy programu VMware](https://kb.vmware.com/selfservice/microsites/search.do?&cmd=displayKC&externalId=1017658).

### <a name="why-is-hostd-not-providing-data-to-log-analytics-my-log-setting-is-set-to-info"></a>Dlaczego Hostd nie dostarcza dane do usługi Log Analytics? Moje ustawienia dziennika jest równa informacji.
Wystąpił błąd hosta ESXi sygnatury czasowej syslog. Aby uzyskać więcej informacji, zobacz [bazę wiedzy programu VMware](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2111202). Po zastosowaniu obejścia Hostd powinny działać normalnie.

### <a name="can-i-have-multiple-esxi-hosts-forwarding-syslog-data-to-a-single-vm-with-omsagent"></a>Czy można mieć wiele hostów ESXi przekazywania danych z serwera syslog z jedną maszyną wirtualną za pomocą omsagent?
Tak. Może mieć wiele hostów ESXi przekazywania z jedną maszyną wirtualną za pomocą omsagent.

### <a name="why-dont-i-see-data-flowing-into-log-analytics"></a>Dlaczego nie widzisz dane trafiające do usługi Log Analytics?
Może istnieć kilka przyczyn:

* ESXi host nie jest poprawnie wypychanie danych do maszyny Wirtualnej z systemem omsagent. Aby przetestować, wykonaj następujące czynności:

  1. Aby potwierdzić, zaloguj się do hosta ESXi za pomocą protokołu SSH i uruchom następujące polecenie: `nc -z ipaddressofVM 1514`

      Jeśli to się nie powiedzie, vSphere ustawień konfiguracji zaawansowanych prawdopodobnie nieprawidłowy. Zobacz [Konfigurowanie kolekcji dziennika](#configure-syslog-collection) systemowego, aby uzyskać informacje na temat sposobu konfigurowania hosta ESXi na potrzeby przesyłania dalej dziennika systemowego.
  1. Jeśli połączenie z portem dziennika systemowego zakończyło się pomyślnie, ale nie widzisz żadnych danych, Załaduj ponownie dziennik systemowy na hoście ESXi przy użyciu protokołu SSH, aby uruchomić następujące polecenie: `esxcli system syslog reload`
* Maszyny Wirtualnej przy użyciu agenta usługi Log Analytics nie jest poprawnie ustawiony. Aby to przetestować, wykonaj następujące czynności:

  1. Usługa log Analytics nasłuchuje na porcie 1514. Aby sprawdzić, czy jest otwarty, uruchom następujące polecenie: `netstat -a | grep 1514`
  1. Powinien zostać wyświetlony `1514/tcp` port otwarty. Jeśli tego nie zrobisz, sprawdź, czy omsagent jest poprawnie zainstalowany. Jeśli nie widzisz informacje o porcie syslog port nie jest otwarty na maszynie Wirtualnej.

    a. Sprawdź, czy Log Analytics Agent jest uruchomiony przy użyciu `ps -ef | grep oms`. Jeśli nie jest uruchomiony, uruchom proces, uruchamiając polecenie `sudo /opt/microsoft/omsagent/bin/service_control start`

     b. Otwórz plik `/etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf`.

     c. Sprawdź, czy odpowiednie ustawienie użytkownika i grupy jest prawidłowe, podobnie jak w przypadku: `-rw-r--r-- 1 omsagent omiusers 677 Sep 20 16:46 vmware_esxi.conf`

     d. Jeśli plik nie istnieje lub ustawienia użytkownika i grupy są błędne, wykonaj działania naprawcze, [przygotowując serwer z systemem Linux](#prepare-a-linux-server).

## <a name="next-steps"></a>Następne kroki
* Użyj [zapytań dzienników](../log-query/log-query-overview.md) w log Analytics, aby wyświetlić szczegółowe dane hosta VMware.
* [Utwórz własne pulpity nawigacyjne](../learn/tutorial-logs-dashboards.md) przedstawiające dane hosta VMware.
* [Utwórz alerty](../platform/alerts-overview.md) w przypadku wystąpienia określonych zdarzeń hosta VMware.
