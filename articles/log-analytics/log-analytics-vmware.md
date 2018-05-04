---
title: Monitorowanie VMware rozwiązania analizy dzienników | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o tym, jak to rozwiązanie monitorowanie VMware można ułatwić zarządzania dziennikami i monitorować hostach ESXi.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: 16516639-cc1e-465c-a22f-022f3be297f1
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: magoedte
ms.openlocfilehash: 98969cf2f6604843bc1502990222264e7acc363b
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="vmware-monitoring-preview-solution-in-log-analytics"></a>VMware monitorowania (wersja zapoznawcza) rozwiązania analizy dzienników

![VMware symbol](./media/log-analytics-vmware/vmware-symbol.png)

> [!NOTE]
> To rozwiązanie monitorowanie VMware jest przestarzała.  Klienci, którzy zainstalowali rozwiązania można nadal z niego korzystać, ale monitorowania VMware nie można dodać do żadnych nowych obszarów roboczych.

Rozwiązanie monitorowania VMware w analizy dzienników jest rozwiązaniem, które pomaga w utworzeniu centralnego rejestrowania i monitorowania podejście do dużych dzienników VMware. W tym artykule opisano, jak można rozwiązać, przechwytywania i zarządzać hostach ESXi w jednej lokalizacji za pomocą rozwiązania. Dzięki rozwiązaniu można poznać szczegółowe dane na wszystkich hostach ESXi w jednym miejscu. Widać liczby zdarzeń top, stanu i trendów hostów maszyny Wirtualnej i ESXi realizowane za pośrednictwem dzienniki hosta ESXi. Można rozwiązać, wyświetlając i wyszukiwanie scentralizowane dzienniki hosta ESXi. I można tworzyć alerty na podstawie kwerend wyszukiwania dziennika.

W tym rozwiązaniu zastosowano syslog natywnej funkcji hosta ESXi przekazywaniu danych do docelowej maszyny Wirtualnej, którego Agent pakietu OMS. Jednak rozwiązanie nie zapisywać pliki na syslog w docelowej maszyny Wirtualnej. Agent pakietu OMS otwiera port 1514 i nasłuchuje to. Po otrzymaniu dane, agent pakietu OMS wypycha dane do analizy dzienników.

## <a name="install-and-configure-the-solution"></a>Instalowanie i konfigurowanie rozwiązania
Skorzystaj z poniższych informacji, aby zainstalować i skonfigurować rozwiązania.

* Dodaj rozwiązanie monitorowania VMware do subskrypcji przy użyciu procesu opisanego w [Dodaj rozwiązanie do zarządzania](log-analytics-add-solutions.md#add-a-management-solution).

#### <a name="supported-vmware-esxi-hosts"></a>Hosty VMware ESXi obsługiwane
vSphere ESXi 5.5 hosta i 6.0

#### <a name="prepare-a-linux-server"></a>Przygotuj serwer systemu Linux
Tworzenie maszyny Wirtualnej, aby otrzymywać wszystkie dane syslog hostach ESXi systemem operacyjnym Linux. [Agenta systemu Linux OMS](log-analytics-linux-agents.md) jest punktem kolekcji dla wszystkich danych z serwera syslog hosta ESXi. Wielu hostach ESXi służy do przekazywania dzienników z jednym serwerem systemu Linux, jak w poniższym przykładzie.  

   ![Przepływ usługi SYSLOG](./media/log-analytics-vmware/diagram.png)

### <a name="configure-syslog-collection"></a>Konfigurowanie zbierania syslog
1. Skonfiguruj funkcję przesyłania syslog VSphere. Aby uzyskać szczegółowe informacje ułatwiające konfigurowanie przekazywania syslog, zobacz [Konfigurowanie syslog na ESXi 5.x i 6.0 (2003322)](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2003322). Przejdź do **Konfiguracja hosta ESXi** > **oprogramowania** > **Zaawansowane ustawienia** > **Syslog**.
   ![vsphereconfig](./media/log-analytics-vmware/vsphere1.png)  
2. W *Syslog.global.logHost* pól, Dodaj serwer z systemem Linux i numer portu *1514*. Na przykład `tcp://hostname:1514` lub `tcp://123.456.789.101:1514`
3. Otwórz zaporę hosta ESXi dla syslog. **Konfiguracja hosta ESXi** > **oprogramowania** > **profil zabezpieczeń** > **zapory** , a następnie otwórz **Właściwości**.  

    ![vspherefw](./media/log-analytics-vmware/vsphere2.png)  

    ![vspherefwproperties](./media/log-analytics-vmware/vsphere3.png)  
4. Sprawdź vSphere konsolę, aby sprawdzić, czy ten dziennik systemowy jest prawidłowo skonfigurowane. Upewnij się na hoście ESXI, ten port **1514** jest skonfigurowany.
5. Pobierz i zainstaluj agenta pakietu OMS dla systemu Linux na serwer z systemem Linux. Aby uzyskać więcej informacji, zobacz [dokumentacji Agent pakietu OMS Linux](https://github.com/Microsoft/OMS-Agent-for-Linux).
6. Po zainstalowaniu Agent pakietu OMS dla systemu Linux, przejdź do katalogu /etc/opt/microsoft/omsagent/sysconf/omsagent.d i skopiuj plik vmware_esxi.conf do katalogu /etc/opt/microsoft/omsagent/conf/omsagent.d i zmiany właściciela/grupy i uprawnienia do tego pliku. Na przykład:

    ```
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/vmware_esxi.conf /etc/opt/microsoft/omsagent/conf/omsagent.d
   sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf
    ```
7. Uruchom ponownie agenta pakietu OMS dla systemu Linux, uruchamiając `sudo /opt/microsoft/omsagent/bin/service_control restart`.
8. Test połączenia między serwerem systemu Linux i hosta ESXi przy użyciu `nc` polecenie na hoście ESXi. Na przykład:

    ```
    [root@ESXiHost:~] nc -z 123.456.789.101 1514
    Connection to 123.456.789.101 1514 port [tcp/*] succeeded!
    ```

9. W portalu Azure, wyszukaj dziennika `VMware_CL`. Podczas analizy dzienników umożliwia zbieranie danych z serwera syslog, zachowuje formatu syslog. W portalu, niektóre określonych pól są przechwytywane, takich jak *Hostname* i *ProcessName*.  

    ![type](./media/log-analytics-vmware/type.png)  

    Jeśli wyświetlanie wyników wyszukiwania dziennika są podobne do powyższy obraz, gotowe do użycia pulpit nawigacyjny rozwiązania monitorowanie VMware.  

## <a name="vmware-data-collection-details"></a>Szczegóły pobierania danych VMware
To rozwiązanie monitorowanie VMware zbiera różne metryki i dziennika dane dotyczące wydajności z hostach ESXi przy użyciu agentów OMS dla systemu Linux, które mają włączone.

W poniższej tabeli przedstawiono metody zbierania danych i inne szczegółowe informacje o jak zbierane są dane.

| Platformy | Agent pakietu OMS dla systemu Linux | Agenta programu SCOM | Azure Storage | SCOM wymagane? | Dane agenta programu SCOM wysyłane za pośrednictwem grupy zarządzania | Częstotliwość kolekcji |
| --- | --- | --- | --- | --- | --- | --- |
| Linux |&#8226; |  |  |  |  |co 3 minuty |

W poniższej tabeli przedstawiono przykłady pól danych zbieranych przez rozwiązanie monitorowanie VMware:

| Nazwa pola | description |
| --- | --- |
| Device_s |Urządzenia magazynujące VMware |
| ESXIFailure_s |typy niepowodzenia |
| EventTime_t |Godzina wystąpienia zdarzenia |
| HostName_s |Nazwa hosta ESXi |
| Operation_s |Tworzenie maszyny Wirtualnej lub usuwanie maszyny Wirtualnej |
| ProcessName_s |Nazwa zdarzenia |
| ResourceId_s |Nazwa hosta VMware |
| ResourceLocation_s |VMware |
| ResourceName_s |VMware |
| ResourceType_s |Funkcja Hyper-V |
| SCSIStatus_s |Stan VMware SCSI |
| SyslogMessage_s |Dane dziennika systemowego |
| UserName_s |Użytkownik, który utworzeniu lub usunięciu maszyny Wirtualnej |
| VMName_s |Nazwa maszyny wirtualnej |
| Computer (Komputer) |komputer-host |
| TimeGenerated |czas wygenerowania danych |
| DataCenter_s |Centrum danych VMware |
| StorageLatency_s |Magazyn czas oczekiwania (ms) |

## <a name="vmware-monitoring-solution-overview"></a>Monitorowanie VMware Omówienie rozwiązania
W obszarze roboczym analizy dzienników pojawi się Kafelek VMware. Zapewnia widok wysokiego poziomu zakończą się niepowodzeniem. Po kliknięciu kafelka, przejdź do widoku pulpitu nawigacyjnego.

![Kafelek](./media/log-analytics-vmware/tile.png)

#### <a name="navigate-the-dashboard-view"></a>Przejdź do widoku pulpitu nawigacyjnego
W **VMware** widoku pulpitu nawigacyjnego bloki są zorganizowane według:

* Licznik stan awarii
* Zlicza hosta najwyższego przez zdarzenie
* Liczba zdarzeń TOP
* Działania maszyny wirtualnej
* Zdarzenia dysku hosta ESXi

![solution1](./media/log-analytics-vmware/solutionview1-1.png)

![solution2](./media/log-analytics-vmware/solutionview1-2.png)

Kliknij przycisk bloku, aby otworzyć okienko wyszukiwania analizy dzienników, która zawiera szczegółowe informacje specyficzne dla bloku.

W tym miejscu można edytować zapytania wyszukiwania, aby zmodyfikować jego dla określonego elementu. Aby uzyskać więcej informacji na temat tworzenia dziennik wyszukiwania, zobacz [wyszukiwanie danych przy użyciu dziennika wyszukiwania w analizy dzienników](log-analytics-log-searches.md).

#### <a name="find-esxi-host-events"></a>Znajdź zdarzenia hosta ESXi
Na jednym hoście ESXi generuje wiele dzienników, opartych na ich procesów. To rozwiązanie monitorowanie VMware centralizuje je i zawiera podsumowanie liczby zdarzeń. Ten widok scentralizowane pomaga w zrozumieniu którym hoście ESXi ma dużą liczbę zdarzeń i jakie zdarzenia występują najczęściej w danym środowisku.

![event](./media/log-analytics-vmware/events.png)

Można przejść do szczegółów przez kliknięcie przycisku hosta ESXi lub typ zdarzenia.

Po kliknięciu nazwy hosta ESXi, możesz przeglądać informacje z tego hosta ESXi. Jeśli chcesz zawęzić wyniki z typem zdarzenia, Dodaj `“ProcessName_s=EVENT TYPE”` w kwerendzie wyszukiwania. Możesz wybrać **ProcessName** w filtrze wyszukiwania. Który zawęża informacje dla Ciebie.

![Przechodzenie do szczegółów](./media/log-analytics-vmware/eventhostdrilldown.png)

#### <a name="find-high-vm-activities"></a>Znajdź dużej aktywności maszyny Wirtualnej
Można tworzyć i usunąć na żadnym hoście ESXi maszyny wirtualnej. Jest to przydatne dla administratora określić liczbę maszyn wirtualnych, które tworzy hosta ESXi. Tego w — Włączanie, co pomoże nam poznać Planowanie wydajności i pojemności. Rejestrowanie informacji o zdarzenia działania maszyny Wirtualnej jest niezwykle istotne w przypadku, gdy zarządzanie środowiskiem.

![Przechodzenie do szczegółów](./media/log-analytics-vmware/vmactivities1.png)

Jeśli chcesz wyświetlić dodatkowe dane tworzenia maszyny Wirtualnej hosta ESXi, kliknij nazwę hosta ESXi.

![Przechodzenie do szczegółów](./media/log-analytics-vmware/createvm.png)

#### <a name="common-search-queries"></a>Typowe zapytania wyszukiwania
Rozwiązanie zawiera inne przydatne zapytań, które ułatwiają zarządzanie hostach ESXi, takich jak miejsca do magazynowania wysoka, opóźnienie magazynu ścieżka błędu.

[!INCLUDE[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

![— zapytania](./media/log-analytics-vmware/queries.png)


#### <a name="save-queries"></a>Zapisywanie zapytań
Zapisywanie zapytań wyszukiwania jest standardowa funkcja analizy dzienników i mogą pomóc żadnych zapytań, które zostały znalezione przydatne. Po utworzeniu kwerendę, która jest użyteczna, zapisz go, klikając **ulubione**. Zapisane zapytanie umożliwia łatwe ponowne używanie go później z [Mój pulpit nawigacyjny](log-analytics-dashboards.md) strony, w którym można tworzyć niestandardowe pulpity.

![DockerDashboardView](./media/log-analytics-vmware/dockerdashboardview.png)

#### <a name="create-alerts-from-queries"></a>Utwórz alerty na podstawie zapytania
Po utworzeniu zapytania można Użyj zapytania, aby ostrzec w przypadku wystąpienia określonych zdarzeń. Zobacz [alertów w analizy dzienników](log-analytics-alerts.md) informacji o sposobie tworzenia alertów. Przykłady alerty zapytania i inne przykłady zapytania, zobacz [VMware monitora przy użyciu analizy dzienników](https://blogs.technet.microsoft.com/msoms/2016/06/15/monitor-vmware-using-oms-log-analytics) wpis w blogu.

## <a name="frequently-asked-questions"></a>Często zadawane pytania
### <a name="what-do-i-need-to-do-on-the-esxi-host-setting-what-impact-will-it-have-on-my-current-environment"></a>Co trzeba ESXi hosta ustawienie? Jaki wpływ mają go na mój bieżącego środowiska?
W tym rozwiązaniu zastosowano natywnego Syslog hosta ESXi mechanizm przekazywania. Nie potrzebujesz dodatkowego oprogramowania firmy Microsoft na hoście ESXi, aby przechwycić dzienniki. Powinien on niski wpływ do istniejącego środowiska. Jednak należy ustawić przekazywania syslog, czyli ESXI funkcji.

### <a name="do-i-need-to-restart-my-esxi-host"></a>Należy ponownie uruchomić Moje hosta ESXi?
Nie. Ten proces nie wymaga ponownego uruchomienia komputera. Czasami vSphere, nie jest poprawnie aktualizowany syslog. W takim przypadku należy zalogować się do hosta ESXi i załaduj ponownie syslog. Ponownie nie trzeba ponownie uruchomić hosta, więc ten proces nie jest znaczący wpływ na środowisko.

### <a name="can-i-increase-or-decrease-the-volume-of-log-data-sent-to-log-analytics"></a>Można zwiększyć lub zmniejszyć ilość danych dziennika wysyłane do analizy dzienników?
Tak, można. Można użyć ustawień poziomu dziennika hosta ESXi, w vSphere. Zbieranie dzienników jest oparta na *informacji* poziom. Tak, jeśli chcesz inspekcji tworzenia maszyny Wirtualnej lub usunięcia, należy zachować *informacji* poziomu na Hostd. Aby uzyskać więcej informacji, zobacz [wiedzy VMware](https://kb.vmware.com/selfservice/microsites/search.do?&cmd=displayKC&externalId=1017658).

### <a name="why-is-hostd-not-providing-data-to-log-analytics-my-log-setting-is-set-to-info"></a>Dlaczego Hostd nie dostarcza dane do analizy dzienników? Mój dzienniku mają ustawioną wartość informacji.
Wystąpił błąd hosta ESXi sygnatura czasowa syslog. Aby uzyskać więcej informacji, zobacz [wiedzy VMware](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2111202). Po zastosowaniu obejścia Hostd powinny działać normalnie.

### <a name="can-i-have-multiple-esxi-hosts-forwarding-syslog-data-to-a-single-vm-with-omsagent"></a>Czy może mieć wielu hostach ESXi przekazywania danych z serwera syslog do jednej maszyny Wirtualnej z omsagent?
Tak. Może mieć wielu hostach ESXi przekazywania ich do jednej maszyny Wirtualnej z omsagent.

### <a name="why-dont-i-see-data-flowing-into-log-analytics"></a>Dlaczego nie widzę danych otrzymywanych przez analizy dzienników
Może istnieć wiele przyczyn:

* Hosta ESXi nie jest poprawnie wypychanie danych do maszyny Wirtualnej uruchomionej omsagent. Aby przetestować, wykonaj następujące czynności:

  1. Aby potwierdzić, zaloguj się do hosta ESXi przy użyciu ssh i uruchom następujące polecenie: `nc -z ipaddressofVM 1514`

      Jeśli to się nie powiedzie, prawdopodobnie vSphere ustawień konfiguracji Zaawansowane nieprawidlowy. Zobacz [Konfigurowanie zbierania syslog](#configure-syslog-collection) informacji o konfigurowaniu hosta ESXi do przekazywania dziennika systemowego.
  2. Jeśli połączenie port syslog zakończy się pomyślnie, ale nadal nie widzisz żadnych danych, Załaduj ponownie syslog na hoście ESXi, za pomocą ssh uruchom następujące polecenie: ` esxcli system syslog reload`
* Maszyna wirtualna z agentem pakietu OMS nie jest poprawnie ustawiony. Aby to sprawdzić, wykonaj następujące czynności:

  1. Analiza dzienników nasłuchuje portu 1514. Aby sprawdzić, czy jest otwarty, uruchom następujące polecenie: `netstat -a | grep 1514`
  2. Powinny pojawić się portu `1514/tcp` otworzyć. Jeśli nie chcesz, sprawdź, czy omsagent jest poprawnie zainstalowany. Jeśli nie ma informacji o porcie, syslog port nie jest otwarty na maszynie Wirtualnej.

    a. Sprawdź, czy Agent pakietu OMS jest uruchomiony przy użyciu `ps -ef | grep oms`. Jeśli nie jest uruchomiona, należy uruchomić proces, za pomocą polecenia ` sudo /opt/microsoft/omsagent/bin/service_control start`

    b. Otwórz plik `/etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf`.

    c. Sprawdź, czy odpowiednie użytkownika i ustawienia grupy jest prawidłowy, podobnie jak: `-rw-r--r-- 1 omsagent omiusers 677 Sep 20 16:46 vmware_esxi.conf`

    d. Jeśli plik nie istnieje lub jest nieprawidłowy, użytkownika i ustawienia grupy podjęcia działań naprawczych przez [przygotowania serwera Linux](#prepare-a-linux-server).

## <a name="next-steps"></a>Kolejne kroki
* Użyj [dziennik wyszukiwania](log-analytics-log-searches.md) analizy dzienników, aby wyświetlić szczegółowe VMware udostępniać dane.
* [Tworzenie własnych pulpity nawigacyjne](log-analytics-dashboards.md) przedstawiający danych hosta VMware.
* [Tworzenie alertów](log-analytics-alerts.md) po wystąpieniu określonych zdarzeń hosta VMware.
