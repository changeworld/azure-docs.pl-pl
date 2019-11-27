---
title: Rozwiązywanie problemów z agentem systemu Linux analizy dzienników platformy Azure | Dokumentacja firmy Microsoft
description: Opisz objawy, przyczyny i rozwiązywanie typowych problemów dotyczących programu Log Analytics Agent dla systemu Linux w Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 11/21/2019
ms.openlocfilehash: ca0fcd3b68722d44fc285b2dff52b560c591d0be
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74306549"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-linux"></a>Jak rozwiązywać problemy związane z agenta usługi Log Analytics dla systemu Linux 

Ten artykuł zawiera informacje ułatwiające rozwiązywanie problemów dotyczących błędów, które mogą wystąpić w przypadku Log Analytics agenta dla systemu Linux w programie Azure Monitor i sugeruje rozwiązania, które można rozwiązać.

Jeśli żadna z powyższych czynności działa, następujących kanałów pomocy technicznej dostępne są również:

* Klienci korzystający z usług Premier Premium mogą otworzyć żądanie pomocy technicznej w wersji [Premium.](https://premier.microsoft.com/)
* Klienci z umowami pomocy technicznej systemu Azure mogą otworzyć żądanie pomocy technicznej [w Azure Portal](https://manage.windowsazure.com/?getsupport=true).
* Diagnozuj problemy z OMI, korzystając z [przewodnika rozwiązywania problemów z OMI](https://github.com/Microsoft/omi/blob/master/Unix/doc/diagnose-omi-problems.md).
* Zaplikowanie problemu z usługą [GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues).
* Odwiedź stronę Log Analytics opinię, aby przejrzeć przesłane pomysły i usterki [https://aka.ms/opinsightsfeedback](https://aka.ms/opinsightsfeedback) lub plik nowy.  

## <a name="important-log-locations-and-log-collector-tool"></a>Narzędzie moduł zbierający i ważne dziennika lokalizacji

 Plik | Ścieżka
 ---- | -----
 Agent analizy dziennika dla pliku dziennika systemu Linux | `/var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`
 Plik dziennika konfiguracji agenta analizy dzienników | `/var/opt/microsoft/omsconfig/omsconfig.log`

 Firma Microsoft zaleca używanie naszego narzędzia do modułu zbierającego dzienników do pobierania dzienników ważne podczas rozwiązywania problemów lub przed przesłaniem problem w usłudze GitHub. Więcej informacji na temat narzędzia i sposobu jego uruchomienia można znaleźć [tutaj](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md).

## <a name="important-configuration-files"></a>Pliki konfiguracji ważne

 Kategoria | Lokalizacja pliku
 ----- | -----
 Dziennik systemu | `/etc/syslog-ng/syslog-ng.conf` lub `/etc/rsyslog.conf` lub `/etc/rsyslog.d/95-omsagent.conf`
 Wydajność, Nagios, Zabbix, danych wyjściowych usługi Log Analytics i ogólne agenta | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`
 Dodatkowe konfiguracje | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/*.conf`

 >[!NOTE]
 >Edytowanie plików konfiguracji dla liczników wydajności i dziennika systemowego jest zastępowane, jeśli kolekcja została skonfigurowana z [menu dane log Analytics ustawienia zaawansowane](../../azure-monitor/platform/agent-data-sources.md#configuring-data-sources) w Azure Portal dla obszaru roboczego. Aby wyłączyć konfigurację dla wszystkich agentów, wyłącz zbieranie danych z **ustawień zaawansowanych** log Analytics lub dla jednego agenta Uruchom następujące polecenie:  
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`

## <a name="installation-error-codes"></a>Kody błędów instalacji

| Kod błędu | Znaczenie |
| --- | --- |
| NOT_DEFINED | Ponieważ nie są zainstalowane niezbędne zależności, wtyczka auditd auoms nie zostaną zainstalowane | Instalację auoms nie powiodło się, wykorzystują pakiet. |
| 2 | Nieprawidłowa opcja udostępniane przez pakiet powłoki. Uruchom `sudo sh ./omsagent-*.universal*.sh --help` do użycia |
| 3 | Brak opcji udostępniane przez pakiet powłoki. Uruchom `sudo sh ./omsagent-*.universal*.sh --help` do użycia. |
| 4 | Nieprawidłowy typ pakietu lub nieprawidłowe ustawienia serwera proxy; pakiety omsagent-*RPM*. sh można zainstalować tylko w systemach z procesorem RPM, a pakiety omsagent-*deb*. sh można zainstalować tylko w systemach opartych na debian. Zalecamy używanie Instalatora uniwersalnego z [najnowszej wersji](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux). Zapoznaj się również z tematem Sprawdzanie ustawień serwera proxy. |
| 5 | Pakiet shell musi zostać wykonana jako główny lub wystąpił błąd 403 zwrócił zestawu dokumentacji podczas dołączania. Uruchom polecenie, używając `sudo`. |
| 6 | Nieprawidłowa Architektura pakietu lub wystąpił błąd 200 podczas dołączania; pakiety omsagent-*x64.sh można zainstalować tylko w systemach 64-bitowych, a pakiety omsagent-* x86.sh można zainstalować tylko w systemach 32-bitowych. Pobierz prawidłowy pakiet dla swojej architektury z [najnowszej wersji](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/latest). |
| 17 | Nie można zainstalować pakietu OMS. Przejrzyj dane wyjściowe polecenia niepowodzenia głównego. |
| 19 | Nie można zainstalować pakiet OMI. Przejrzyj dane wyjściowe polecenia niepowodzenia głównego. |
| 20 | Instalacja pakietu SCX nie powiodło się. Przejrzyj dane wyjściowe polecenia niepowodzenia głównego. |
| 21 | Nie można zainstalować dostawcy zestawów. Przejrzyj dane wyjściowe polecenia niepowodzenia głównego. |
| 22 | Nie można zainstalować pakietu powiązane. Przejrzyj dane wyjściowe polecenia niepowodzenia głównego |
| 23 | Pakiet SCX lub OMI już zainstalowane. Użyj `--upgrade` zamiast `--install`, aby zainstalować pakiet powłoki. |
| 30 | Błąd wewnętrzny pakiet. Aby uzyskać szczegółowe informacje z danych wyjściowych, Zastąp problem z usługą [GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) . |
| 55 | Nieobsługiwana wersja OpenSSL lub nie można nawiązać połączenia z usługą Azure Monitor lub serwerach dpkg jest zablokowana lub brak programu zwinięcie. |
| 61 | Brak biblioteki ctypes Python. Zainstaluj biblioteki ctypes Python lub pakietów (python ctypes). |
| 62 | Brakujący program docelowy, tar instalacji. |
| 63 | Brakujący program sed, sed. instalacji |
| 64 | Brakujący program curl, zainstaluj narzędzie curl. |
| 65 | Brakujący program gpg, gpg instalacji. |

## <a name="onboarding-error-codes"></a>Kody błędów dołączania

| Kod błędu | Znaczenie |
| --- | --- |
| 2 | Nieprawidłowa opcja przekazywane do skryptu omsadmin. Uruchom `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` do użycia. |
| 3 | Nieprawidłowa konfiguracja przekazywane do skryptu omsadmin. Uruchom `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` do użycia. |
| 4 | Nieprawidłowy serwer proxy, przekazywane do skryptu omsadmin. Sprawdź serwer proxy i zapoznaj się z [dokumentacją dotyczącą korzystania z serwera proxy HTTP](log-analytics-agent.md#network-firewall-requirements). |
| 5 | 403 błąd HTTP odebrany z Azure Monitor. Zobacz pełne dane wyjściowe skryptu omsadmin, aby uzyskać szczegółowe informacje. |
| 6 | Odebrano błąd HTTP inny niż 200 z Azure Monitor. Zobacz pełne dane wyjściowe skryptu omsadmin, aby uzyskać szczegółowe informacje. |
| 7 | Nie można nawiązać połączenia z Azure Monitor. Zobacz pełne dane wyjściowe skryptu omsadmin, aby uzyskać szczegółowe informacje. |
| 8 | Błąd dołączania do obszaru roboczego usługi Log Analytics. Zobacz pełne dane wyjściowe skryptu omsadmin, aby uzyskać szczegółowe informacje. |
| 30 | Wewnętrzny błąd skryptu. Aby uzyskać szczegółowe informacje z danych wyjściowych, Zastąp problem z usługą [GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) . |
| 31 | Błąd podczas generowania agenta identyfikatora. Aby uzyskać szczegółowe informacje z danych wyjściowych, Zastąp problem z usługą [GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) . |
| 32 | Wystąpił błąd podczas generowania certyfikatów. Zobacz pełne dane wyjściowe skryptu omsadmin, aby uzyskać szczegółowe informacje. |
| 33 | Błąd podczas generowania metaconfiguration dla omsconfig. Aby uzyskać szczegółowe informacje z danych wyjściowych, Zastąp problem z usługą [GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) . |
| 34 | Metaconfiguration generowania skryptu nie istnieje. Ponowienie próby dołączenia za pomocą `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key>`. |

## <a name="enable-debug-logging"></a>Włączenie rejestrowania debugowania
### <a name="oms-output-plugin-debug"></a>Debugowanie wtyczki dane wyjściowe pakietu OMS
 Fluentd umożliwiający umożliwia poziomów rejestrowania specyficznych dla wtyczki, dzięki czemu możesz określić poziomy dziennika dla danych wejściowych i wyjściowych. Aby określić inny poziom dziennika dla danych wyjściowych pakietu OMS, Edytuj ogólną konfigurację agenta w `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`.  

 W dodatku danych wyjściowych pakietu OMS przed końcem pliku konfiguracji Zmień właściwość `log_level` z `info` na `debug`:

 ```
 <match oms.** docker.**>
  type out_oms
  log_level debug
  num_threads 5
  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
 ```

Rejestrowanie debugowania umożliwia wyświetlenie wsadowych operacji przekazywania do Azure Monitor rozdzielonych według typu, liczby elementów danych i czasu potrzebnego do wysłania:

*Przykładowy dziennik z włączoną obsługą debugowania:*

```
Success sending oms.nagios x 1 in 0.14s
Success sending oms.omi x 4 in 0.52s
Success sending oms.syslog.authpriv.info x 1 in 0.91s
```

### <a name="verbose-output"></a>Pełne dane wyjściowe
Zamiast korzystać z wtyczki wyjściowej OMS można także wyprowadzać elementy danych bezpośrednio do `stdout`, które są widoczne w pliku dziennika Log Analytics agenta dla systemu Linux.

W Log Analytics ogólny plik konfiguracji agenta w `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`, komentarz do wtyczki wyjściowej pakietu OMS przez dodanie `#` przed każdym wierszem:

```
#<match oms.** docker.**>
#  type out_oms
#  log_level info
#  num_threads 5
#  buffer_chunk_limit 5m
#  buffer_type file
#  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms*.buffer
#  buffer_queue_limit 10
#  flush_interval 20s
#  retry_limit 10
#  retry_wait 30s
#</match>
```

W przypadku wtyczki wyjściowej Usuń komentarz z poniższej sekcji, usuwając `#` przed każdym wierszem:

```
<match **>
  type stdout
</match>
```

## <a name="issue--unable-to-connect-through-proxy-to-azure-monitor"></a>Problem: nie można nawiązać połączenia przy użyciu serwera proxy w celu Azure Monitor

### <a name="probable-causes"></a>Prawdopodobne przyczyny
* Nieprawidłowy serwer proxy określony zestawu dokumentacji podczas dołączania
* Punkty końcowe usługi Azure Monitor i Azure Automation nie są listy dozwolonych w centrum danych 

### <a name="resolution"></a>Rozwiązanie
1. Reonboard Azure Monitor z agentem Log Analytics dla systemu Linux przy użyciu następującego polecenia z opcją `-v` włączona. Umożliwia pełne dane wyjściowe agenta łączącego się za pomocą serwera proxy w celu Azure Monitor. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key> -p <Proxy Conf> -v`

2. Przejrzyj sekcję [Aktualizowanie ustawień serwera proxy](agent-manage.md#update-proxy-settings) , aby upewnić się, że Agent jest prawidłowo skonfigurowany do komunikowania się za pomocą serwera proxy.    
* Sprawdź, czy następujące Azure Monitor punkty końcowe są listy dozwolonych:

    |Zasób agenta| Porty | Kierunek |
    |------|---------|----------|  
    |*.ods.opinsights.azure.com | Port 443| Dla ruchu przychodzącego i wychodzącego |  
    |*.oms.opinsights.azure.com | Port 443| Dla ruchu przychodzącego i wychodzącego |  
    |*.blob.core.windows.net | Port 443| Dla ruchu przychodzącego i wychodzącego |  

    Jeśli planujesz używać Azure Automation hybrydowego procesu roboczego elementu Runbook do nawiązywania połączenia z usługą Automation i zarejestrowania się z nią w celu używania elementów Runbook lub rozwiązań do zarządzania w danym środowisku, musi on mieć dostęp do numeru portu i adresów URL opisanych w temacie [Konfigurowanie sieci dla hybrydowego procesu roboczego elementu Runbook](../../automation/automation-hybrid-runbook-worker.md#network-planning). 

## <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>Problem: Komunikat o błędzie 403 podczas próby dołączyć

### <a name="probable-causes"></a>Prawdopodobne przyczyny
* Data i godzina jest nieprawidłowa na serwerze z systemem Linux 
* Identyfikator obszaru roboczego i klucz obszaru roboczego, używane są niepoprawne

### <a name="resolution"></a>Rozwiązanie

1. Sprawdź czas na serwerze Linux z datą polecenia. Jeśli czas +/-15 minut od bieżącego czasu dołączania kończy się niepowodzeniem. Aby poprawne to zaktualizuj datę i/lub strefy czasowej serwera systemu Linux. 
2. Sprawdź, czy zainstalowano najnowszą wersję agenta usługi Log Analytics dla systemu Linux.  Najnowszą wersję teraz powiadamia, że możesz Jeśli czasowego powoduje błąd dołączania.
3. Reonboard przy użyciu poprawny identyfikator obszaru roboczego i klucz obszaru roboczego, postępując zgodnie z instrukcjami instalacji we wcześniejszej części tego artykułu.

## <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>Problem: Zostanie wyświetlony 500 i 404 błąd w pliku dziennika bezpośrednio po dołączeniu
Jest to znany problem występujący w pierwszym przekazywania danych z systemem Linux do obszaru roboczego usługi Log Analytics. Nie dotyczy to danych wysłanego lub usługi.


## <a name="issue-you-see-omiagent-using-100-cpu"></a>Problem: widzisz omiagent przy użyciu procesora CPU 100%

### <a name="probable-causes"></a>Prawdopodobne przyczyny
Regresja w pakiecie NSS-PEM [1.0.3 -5. el7](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-7.el7.x86_64.rpm.html) spowodowała poważny problem z wydajnością, dzięki czemu widzimy dużo w dystrybucji RedHat/CentOS 7. x. Aby dowiedzieć się więcej o tym problemie, zapoznaj się z następującą dokumentacją: błąd [1667121 regresji wydajności w libcurl](https://bugzilla.redhat.com/show_bug.cgi?id=1667121).

Błędy związane z wydajnością nie zachodzą przez cały czas i są bardzo trudne do odtworzenia. Jeśli wystąpi ten problem z omiagent, należy użyć skryptu omiHighCPUDiagnostics.sh, który będzie zbierać ślad stosu dla omiagent, gdy zostanie przekroczony określony próg.

1. Pobierz skrypt <br/>
`wget https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/tools/LogCollector/source/omiHighCPUDiagnostics.sh`

2. Uruchom diagnostykę przez 24 godziny z progiem procesora 30% <br/>
`bash omiHighCPUDiagnostics.sh --runtime-in-min 1440 --cpu-threshold 30`

3. Stosu wywołań będzie zrzucany w pliku omiagent_trace, jeśli zauważysz wiele wywołań funkcji NSS, wykonaj kroki opisane poniżej.

### <a name="resolution-step-by-step"></a>Rozwiązanie (krok po kroku)

1. Uaktualnij pakiet NSS-PEM do wersji [1.0.3-5. el7_6.1](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-7.el7.x86_64.rpm.html). <br/>
`sudo yum upgrade nss-pem`

2. Jeśli NSS-PEM nie jest dostępny do uaktualnienia (na przykład w przypadku CentOS), nastąpi obniżenie poziomu do 7.29.0-46. Jeśli przez pomyłkę uruchomisz polecenie "yum update", zwinięcie zostanie uaktualnione do 7.29.0-51, a problem będzie się powtarzać. <br/>
`sudo yum downgrade curl libcurl`

3. Uruchom ponownie OMI: <br/>
`sudo scxadmin -restart`

## <a name="issue-you-are-not-seeing-any-data-in-the-azure-portal"></a>Problem: Nie widać żadnych danych w witrynie Azure portal

### <a name="probable-causes"></a>Prawdopodobne przyczyny

- Dołączanie do Azure Monitor nie powiodło się
- Połączenie z Azure Monitor zostało zablokowane
- Log Analytics agent dla danych z systemem Linux jest wykonywana kopia zapasowa

### <a name="resolution"></a>Rozwiązanie
1. Sprawdź, czy Azure Monitor dołączania zakończyło się pomyślnie, sprawdzając, czy następujący plik istnieje: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`
2. Reonboard przy użyciu instrukcji wiersza polecenia `omsadmin.sh`
3. Jeśli używasz serwera proxy, skorzystaj z procedury opisanej rozpoznawanie serwera proxy, podany wcześniej.
4. W niektórych przypadkach gdy agenta usługi Log Analytics dla systemu Linux nie może komunikować się z usługą, dane na agencie znajduje się w kolejce do rozmiaru buforu pełnej, czyli 50 MB. Agent należy uruchomić ponownie, uruchamiając następujące polecenie: `/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]`. 

    >[!NOTE]
    >Ten problem jest rozwiązany w 1.1.0-28 wersji agenta i nowszych wersjach.


## <a name="issue-you-are-not-seeing-forwarded-syslog-messages"></a>Problem: Nie widać przekazywanych dalej komunikatów usługi Syslog 

### <a name="probable-causes"></a>Prawdopodobne przyczyny
* Konfiguracja stosowany na serwerze z systemem Linux nie zezwala na zbiór wysłane urządzeń i/lub poziomy dziennika
* SYSLOG nie jest przesyłane dalej poprawnie na serwerze z systemem Linux
* Liczba komunikatów przesyłanych na sekundę są zbyt duże, podstawowej konfiguracji agenta usługi Log Analytics dla systemu Linux w celu obsługi

### <a name="resolution"></a>Rozwiązanie
* Sprawdź, czy konfiguracja w obszarze roboczym usługi Log Analytics dziennika systemowego zawiera wszystkich urządzeń i poziomy dziennika prawidłowy. Przejrzyj temat [Konfigurowanie kolekcji dziennika systemowego w Azure Portal](../../azure-monitor/platform/data-sources-syslog.md#configure-syslog-in-the-azure-portal)
* Sprawdź, czy natywne demoy komunikatów dziennika systemu (`rsyslog`, `syslog-ng`) mogą odbierać przesyłane dalej wiadomości
* Sprawdź ustawienia zapory na serwerze Syslog, aby upewnić się, że komunikaty nie są blokowane
* Symuluj komunikat dziennika systemowego, aby Log Analytics przy użyciu polecenia `logger`
  * `logger -p local0.err "This is my test message"`

## <a name="issue-you-are-receiving-errno-address-already-in-use-in-omsagent-log-file"></a>Problem: Otrzymujesz adres Errno już używane w pliku dziennika omsagent
Jeśli widzisz `[error]: unexpected error error_class=Errno::EADDRINUSE error=#<Errno::EADDRINUSE: Address already in use - bind(2) for "127.0.0.1" port 25224>` w omsagent. log.

### <a name="probable-causes"></a>Prawdopodobne przyczyny
Ten błąd wskazuje, że rozszerzenie diagnostyczne systemu Linux (LAD) jest instalowany obok rozszerzenia maszyny Wirtualnej systemu Linux programu Log Analytics i używa tego samego portu usługi syslog zbierania danych jako omsagent.

### <a name="resolution"></a>Rozwiązanie
1. Jako użytkownik główny wykonaj następujące polecenia (Zauważ, że 25224 znajduje się przykład i jest to możliwe, że w środowisku zostanie wyświetlony inny numer portu używany przez LAD):

    ```
    /opt/microsoft/omsagent/bin/configure_syslog.sh configure LAD 25229

    sed -i -e 's/25224/25229/' /etc/opt/microsoft/omsagent/LAD/conf/omsagent.d/syslog.conf
    ```

    Następnie należy edytować poprawną `rsyslogd` lub `syslog_ng` plik konfiguracji i zmienić konfigurację powiązaną z LAD na zapis w porcie 25229.

2. Jeśli maszyna wirtualna jest uruchomiona `rsyslogd`, plik do zmodyfikowania to: `/etc/rsyslog.d/95-omsagent.conf` (jeśli istnieje, Else `/etc/rsyslog`). Jeśli maszyna wirtualna jest uruchomiona `syslog_ng`, plik do zmodyfikowania to: `/etc/syslog-ng/syslog-ng.conf`.
3. Uruchom ponownie omsagent `sudo /opt/microsoft/omsagent/bin/service_control restart`.
4. Uruchom ponownie usługę syslog.

## <a name="issue-you-are-unable-to-uninstall-omsagent-using-purge-option"></a>Problem: Nie jest możliwe do odinstalowania omsagent przy użyciu opcji przeczyszczania

### <a name="probable-causes"></a>Prawdopodobne przyczyny

* Zainstalowano rozszerzenia diagnostycznego systemu Linux
* Rozszerzenia diagnostycznego systemu Linux była instalowana i odinstalowywana, ale nadal wyświetlony komunikat o błędzie o omsagent używany przez mdsd i nie można go usunąć.

### <a name="resolution"></a>Rozwiązanie
1. Odinstaluj rozszerzenie diagnostyczne systemu Linux (LAD).
2. Usuń pliki rozszerzeń diagnostycznych systemu Linux z maszyny, jeśli znajdują się one w następującej lokalizacji: `/var/lib/waagent/Microsoft.Azure.Diagnostics.LinuxDiagnostic-<version>/` i `/var/opt/microsoft/omsagent/LAD/`.

## <a name="issue-you-cannot-see-data-any-nagios-data"></a>Problem: Nie widzisz danych żadnych danych Nagios 

### <a name="probable-causes"></a>Prawdopodobne przyczyny
* Omsagent użytkownik nie ma uprawnień do odczytu z pliku dziennika Nagios
* Źródło Nagios i filtr nie zostały jeszcze pozbawionym znaków komentarza wierszu z pliku omsagent.conf

### <a name="resolution"></a>Rozwiązanie
1. Dodaj użytkownika omsagent do odczytu z pliku Nagios, wykonując te [instrukcje](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#nagios-alerts).
2. W pliku konfiguracji Log Analytics agenta dla systemu Linux w `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`**upewnij się, że dla** źródła i filtru Nagios są komentarze.

    ```
    <source>
      type tail
      path /var/log/nagios/nagios.log
      format none
      tag oms.nagios
    </source>

    <filter oms.nagios>
      type filter_nagios_log
    </filter>
    ```

## <a name="issue-you-are-not-seeing-any-linux-data"></a>Problem: Nie widać żadnych danych z systemem Linux 

### <a name="probable-causes"></a>Prawdopodobne przyczyny
* Dołączanie do Azure Monitor nie powiodło się
* Połączenie z Azure Monitor zostało zablokowane
* Maszyna wirtualna została uruchomiona ponownie.
* Pakiet OMI ręcznie został uaktualniony do nowszej wersji w porównaniu do czego została zainstalowana przez agenta usługi Log Analytics dla pakietu systemu Linux
* Błąd podczas *znajdowania klasy* dzienników zasobów DSC w pliku dziennika `omsconfig.log`
* Agent analizy dziennika dla danych jest wykonywana kopia zapasowa
* *Bieżąca konfiguracja dzienników DSC nie istnieje. Wykonaj polecenie Start-DscConfiguration z parametrem-Path, aby określić plik konfiguracji i najpierw utworzyć bieżącą konfigurację.* w pliku dziennika `omsconfig.log`, ale nie istnieje żaden komunikat dziennika dotyczący `PerformRequiredConfigurationChecks` operacji.

### <a name="resolution"></a>Rozwiązanie
1. Zainstaluj wszystkie zależności, takie jak wykorzystują pakiet.
2. Sprawdź, czy dołączanie do Azure Monitor powiodło się, sprawdzając, czy następujący plik istnieje: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`.  Jeśli nie, reonboard za pomocą [instrukcji](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line)omsadmin.sh wiersza polecenia.
4. Jeśli używasz serwera proxy, sprawdź Rozwiązywanie problemów z powyższych czynności serwer proxy.
5. W niektórych systemach dystrybucji platformy Azure demon serwer OMI omid nie uruchamia się po uruchomieniu maszyny wirtualnej. W rezultacie zostanie nie widzisz danych inspekcji, śledzenia zmian lub UpdateManagement związane z rozwiązania. Obejście polega na ręcznym uruchomieniu serwera OMI przez uruchomienie `sudo /opt/omi/bin/service_control restart`.
6. Pakiet OMI jest ręcznie uaktualnione do nowszej wersji, musi on, należy ręcznie uruchomić ponownie agenta usługi Log Analytics kontynuować działanie. Ten krok jest wymagany dla niektórych dystrybucjach, w którym serwer OMI nie zostanie uruchomiona automatycznie po uaktualnieniu. Uruchom `sudo /opt/omi/bin/service_control restart`, aby ponownie uruchomić OMI.
7. Jeśli zostanie wyświetlona błąd *nie znaleziono klasy* zasobów DSC w omsconfig. log, uruchom `sudo /opt/omi/bin/service_control restart`.
8. W niektórych przypadkach, gdy Agent Log Analytics dla systemu Linux nie może nawiązać komunikacji z Azure Monitor, zostanie utworzona kopia zapasowa danych z agenta do pełnego rozmiaru buforu: 50 MB. Agent należy uruchomić ponownie, uruchamiając następujące polecenie `/opt/microsoft/omsagent/bin/service_control restart`.

    >[!NOTE]
    >Ten problem został rozwiązany w 1.1.0-28 wersji agenta lub nowszym
    >

* Jeśli plik dziennika `omsconfig.log` nie wskazuje, że operacje `PerformRequiredConfigurationChecks` są wykonywane okresowo w systemie, może wystąpić problem z zadaniem/usługą firmy cronus. Upewnij się, że zadanie firmy CRONUS istnieje w obszarze `/etc/cron.d/OMSConsistencyInvoker`. Jeśli to konieczne, uruchom następujące polecenia, aby utworzyć zadanie cron:

    ```
    mkdir -p /etc/cron.d/
    echo "*/15 * * * * omsagent /opt/omi/bin/OMSConsistencyInvoker >/dev/null 2>&1" | sudo tee /etc/cron.d/OMSConsistencyInvoker
    ```

    Ponadto upewnij się, że usługi cron jest uruchomiony. Aby sprawdzić stan tej usługi, można użyć `service cron status` z Debian, Ubuntu, SUSE lub `service crond status` z RHEL, Oracle Linux CentOS. Jeśli usługa nie istnieje, możesz zainstalować pliki binarne i uruchom usługę przy użyciu następujących:

    **Ubuntu/Debian**

    ```
    # To Install the service binaries
    sudo apt-get install -y cron
    # To start the service
    sudo service cron start
    ```

    **SZŁO**

    ```
    # To Install the service binaries
    sudo zypper in cron -y
    # To start the service
    sudo systemctl enable cron
    sudo systemctl start cron
    ```

    **RHEL/CeonOS**

    ```
    # To Install the service binaries
    sudo yum install -y crond
    # To start the service
    sudo service crond start
    ```

    **Oracle Linux**

    ```
    # To Install the service binaries
    sudo yum install -y cronie
    # To start the service
    sudo service crond start
    ```

## <a name="issue-when-configuring-collection-from-the-portal-for-syslog-or-linux-performance-counters-the-settings-are-not-applied"></a>Problem: Podczas konfigurowania kolekcji z poziomu portalu, liczniki wydajności protokołu Syslog lub Linux, ustawienia nie są stosowane

### <a name="probable-causes"></a>Prawdopodobne przyczyny
* Agenta usługi Log Analytics dla systemu Linux nie pobierane najnowszą konfiguracją
* Zmiany ustawień w portalu nie zostały zastosowane.

### <a name="resolution"></a>Rozwiązanie
W **tle:** `omsconfig` jest agentem log Analytics agenta konfiguracji systemu Linux, który wyszukuje nową konfigurację po stronie portalu co pięć minut. Ta konfiguracja jest następnie stosowane do agenta usługi Log Analytics dla systemu Linux plików konfiguracyjnych, znajdujący się w /etc/opt/microsoft/omsagent/conf/omsagent.conf.

* W niektórych przypadkach agenta usługi Log Analytics dla systemu Linux konfiguracji agenta może nie móc komunikować się z usługą konfiguracji portalu skutkuje najnowszą konfigurację, które nie są stosowane.
  1. Sprawdź, czy Agent `omsconfig` jest zainstalowany, uruchamiając `dpkg --list omsconfig` lub `rpm -qi omsconfig`.  Jeśli nie jest zainstalowany, zainstaluj ponownie najnowszą wersję agenta usługi Log Analytics dla systemu Linux.

  2. Sprawdź, czy Agent `omsconfig` może komunikować się z Azure Monitor, uruchamiając następujące polecenie `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`. To polecenie zwraca konfigurację agenta otrzymuje z usługi, w tym ustawień usługi Syslog, liczniki wydajności systemu Linux i dzienników niestandardowych. Jeśli to polecenie nie powiedzie się, uruchom następujące polecenie `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py'`. To polecenie wymusza, aby Agent omsconfig mógł komunikować się z Azure Monitor i pobrać najnowszą konfigurację.

## <a name="issue-you-are-not-seeing-any-custom-log-data"></a>Problem: Nie widać żadnych danych dzienników niestandardowych 

### <a name="probable-causes"></a>Prawdopodobne przyczyny
* Dołączanie do Azure Monitor nie powiodło się.
* Ustawienie **Zastosuj następującą konfigurację do serwerów z systemem Linux** nie zostało wybrane.
* omsconfig nie jest pobierane z ostatnią konfiguracją dzienników niestandardowych z usługi.
* `omsagent` użytkownika Log Analytics agenta dla systemu Linux nie może uzyskać dostępu do dziennika niestandardowego z powodu uprawnień lub nie można go znaleźć.  Może pojawić się następujące błędy:
 * `[DATETIME] [warn]: file not found. Continuing without tailing it.`
 * `[DATETIME] [error]: file not accessible by omsagent.`
* Znany problem z rozwiązane w agenta usługi Log Analytics dla systemu Linux w wersji 1.1.0-217 sytuacja wyścigu

### <a name="resolution"></a>Rozwiązanie
1. Sprawdź, czy dołączanie do Azure Monitor powiodło się, sprawdzając, czy następujący plik istnieje: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`. Jeśli nie, albo:  

  1. Reonboard przy użyciu [instrukcji](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line)omsadmin.sh wiersza polecenia.
  2. W obszarze **Ustawienia zaawansowane** w Azure Portal upewnij się, że ustawienie **Zastosuj następującą konfigurację do serwerów z systemem Linux** jest włączona.  

2. Sprawdź, czy Agent `omsconfig` może komunikować się z Azure Monitor, uruchamiając następujące polecenie `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`.  To polecenie zwraca konfigurację agenta otrzymuje z usługi, w tym ustawień usługi Syslog, liczniki wydajności systemu Linux i dzienników niestandardowych. Jeśli to polecenie nie powiedzie się, uruchom następujące polecenie `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py`. To polecenie wymusza, aby Agent omsconfig mógł komunikować się z Azure Monitor i pobrać najnowszą konfigurację.

**Tło:** Zamiast agenta Log Analytics dla systemu Linux działającego jako uprzywilejowany `root`użytkownika agent działa jako użytkownik `omsagent`. W większości przypadków można udzielić jawne uprawnienia tego użytkownika w kolejności, w przypadku niektórych plików do odczytu. Aby udzielić uprawnienia `omsagent` użytkownikowi, uruchom następujące polecenia:

1. Dodaj użytkownika `omsagent` do konkretnej grupy `sudo usermod -a -G <GROUPNAME> <USERNAME>`
2. Udziel uniwersalnego dostępu do odczytu do wymaganego pliku `sudo chmod -R ugo+rx <FILE DIRECTORY>`

Istnieje znany problem z sytuacja wyścigu przy użyciu agenta usługi Log Analytics dla systemu Linux w wersji wcześniejszej niż 1.1.0-217. Po zaktualizowaniu do najnowszego agenta Uruchom następujące polecenie, aby uzyskać najnowszą wersję wtyczki wyjściowej `sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`.

## <a name="issue-you-are-trying-to-reonboard-to-a-new-workspace"></a>Problem: Próbujesz reonboard z nowym obszarem roboczym
Podczas próby reonboard agenta pod kątem nowego obszaru roboczego, konfigurację agenta usługi Log Analytics musi być oczyszczone przed reonboarding. Aby wyczyścić starą konfigurację z agenta, Uruchom pakiet powłoki przy użyciu `--purge`

```
sudo sh ./omsagent-*.universal.x64.sh --purge
```
Lub

```
sudo sh ./onboard_agent.sh --purge
```

Możesz kontynuować reonboard po użyciu opcji `--purge`

## <a name="log-analytics-agent-extension-in-the-azure-portal-is-marked-with-a-failed-state-provisioning-failed"></a>Rozszerzenia log Analytics agent extension w witrynie Azure portal jest oznaczona stanem nie powiodło się: Inicjowanie obsługi administracyjnej nie powiodło się

### <a name="probable-causes"></a>Prawdopodobne przyczyny
* Log Analytics agent został usunięty z systemu operacyjnego
* Usługę agenta usługi log Analytics nie działa, wyłączone lub nieskonfigurowane

### <a name="resolution"></a>Rozwiązanie 
Wykonaj poniższe kroki, aby rozwiązać ten problem.
1. Usuń rozszerzenie z witryny Azure portal.
2. Zainstaluj agenta zgodnie z [instrukcjami](../../azure-monitor/learn/quick-collect-linux-computer.md).
3. Uruchom ponownie agenta, uruchamiając następujące polecenie: `sudo /opt/microsoft/omsagent/bin/service_control restart`.
* Poczekaj kilka minut, a stan aprowizacji zostanie zmieniony na **zainicjowanie pomyślnie**.


## <a name="issue-the-log-analytics-agent-upgrade-on-demand"></a>Problem: Uaktualnienie agenta usługi Log Analytics na żądanie

### <a name="probable-causes"></a>Prawdopodobne przyczyny

Pakiety agentów usługi Log Analytics na hoście są nieaktualne.

### <a name="resolution"></a>Rozwiązanie 
Wykonaj poniższe kroki, aby rozwiązać ten problem.

1. Sprawdź dostępność najnowszej wersji na [stronie](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/).
2. Pobierz skrypt instalacji (1.4.2-124 jako przykład wersję):

    ```
    wget https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.2-124/omsagent-1.4.2-124.universal.x64.sh
    ```

3. Uaktualnij pakiety, wykonując `sudo sh ./omsagent-*.universal.x64.sh --upgrade`.
