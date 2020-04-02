---
title: Rozwiązywanie problemów z agentem systemu Linux usługi Azure Log Analytics | Dokumenty firmy Microsoft
description: Opisz symptomy, przyczyny i rozwiązanie najczęstszych problemów z agentem usługi Log Analytics dla systemu Linux w usłudze Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/21/2019
ms.openlocfilehash: 2343de97d06abdefed2c2977a7341aa411429319
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520738"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-linux"></a>Jak rozwiązywać problemy z agentem usługi Log Analytics dla systemu Linux 

Ten artykuł zawiera pomoc w rozwiązywaniu problemów z błędami, które mogą wystąpić z agentem usługi Log Analytics dla systemu Linux w usłudze Azure Monitor i sugeruje możliwe rozwiązania, aby je rozwiązać.

Jeśli żaden z tych kroków nie działa, dostępne są również następujące kanały pomocy technicznej:

* Klienci korzystający z pomocy technicznej Premier mogą otworzyć żądanie pomocy technicznej w [u. Premier](https://premier.microsoft.com/).
* Klienci korzystający z umów pomocy technicznej platformy Azure mogą otworzyć żądanie pomocy technicznej [w witrynie Azure.](https://manage.windowsazure.com/?getsupport=true)
* Diagnozuj problemy OMI w [przewodniku rozwiązywania problemów omi](https://github.com/Microsoft/omi/blob/master/Unix/doc/diagnose-omi-problems.md).
* Złóż problem z [githubem](https://github.com/Microsoft/OMS-Agent-for-Linux/issues).
* Odwiedź stronę Opinii usługi Log Analytics, aby [https://aka.ms/opinsightsfeedback](https://aka.ms/opinsightsfeedback) przejrzeć przesłane pomysły i błędy lub złożyć nową.  

## <a name="important-log-locations-and-log-collector-tool"></a>Ważne lokalizacje dzienników i narzędzie Moduł zbierający dzienniki

 Plik | Ścieżka
 ---- | -----
 Agent usługi Log Analytics dla pliku dziennika systemu Linux | `/var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`
 Plik dziennika agenta usługi Log Analytics | `/var/opt/microsoft/omsconfig/omsconfig.log`

 Zalecamy użycie naszego narzędzia do tworzenia modułów zbierających dzienniki w celu pobrania ważnych dzienników w celu rozwiązania problemu lub przed przesłaniem problemu z githubem. Możesz przeczytać więcej o narzędziu i jak go uruchomić [tutaj](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md).

## <a name="important-configuration-files"></a>Ważne pliki konfiguracyjne

 Kategoria | Lokalizacja pliku
 ----- | -----
 Dziennik systemu | `/etc/syslog-ng/syslog-ng.conf`lub `/etc/rsyslog.conf``/etc/rsyslog.d/95-omsagent.conf`
 Wydajność, Nagios, Zabbix, dane wyjściowe analizy dzienników i agent ogólny | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`
 Dodatkowe konfiguracje | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/*.conf`

 >[!NOTE]
 >Edytowanie plików konfiguracyjnych dla liczników wydajności i Syslog jest zastępowany, jeśli kolekcja jest skonfigurowana z [menu danych Log Analytics Zaawansowane ustawienia](../../azure-monitor/platform/agent-data-sources.md#configuring-data-sources) w witrynie Azure portal dla obszaru roboczego. Aby wyłączyć konfigurację dla wszystkich agentów, wyłącz zbieranie z **ustawień zaawansowanych** usługi Log Analytics lub dla jednego agenta uruchom następujące czynności:  
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`

## <a name="installation-error-codes"></a>Kody błędów instalacji

| Kod błędu | Znaczenie |
| --- | --- |
| NOT_DEFINED | Ponieważ niezbędne zależności nie są zainstalowane, skontrolowana wtyczka auoms nie zostanie zainstalowana | Instalacja auomów nie powiodła się, instalacja pakietu została poddana inspekcji. |
| 2 | Nieprawidłowa opcja udostępniona do pakietu powłoki. Uruchom `sudo sh ./omsagent-*.universal*.sh --help` do użycia |
| 3 | Nie ma opcji przewidzianej w pakiecie pocisków. Uruchom `sudo sh ./omsagent-*.universal*.sh --help` do użycia. |
| 4 | Nieprawidłowy typ pakietu LUB nieprawidłowe ustawienia serwera proxy; Pakiety omsagent-rpm .sh można instalować tylko w systemach opartych na RPM, a pakiety omsagent-*deb*.sh można instalować tylko w systemach opartych na Debbianie.*rpm* Zaleca się użycie uniwersalnego instalatora z [najnowszej wersji](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux). Sprawdź również ustawienia serwera proxy. |
| 5 | Pakiet powłoki musi być wykonany jako główny lub wystąpił błąd 403 zwrócony podczas dołączania. Uruchom polecenie `sudo`za pomocą programu . |
| 6 | Nieprawidłowa architektura pakietu LUB wystąpił błąd 200 błąd zwrócony podczas dołączania; pakiety omsagent-*x64.sh mogą być instalowane tylko w systemach 64-bitowych, a pakiety omsagent-* x86.sh mogą być instalowane tylko w systemach 32-bitowych. Pobierz odpowiedni pakiet dla swojej architektury z [najnowszej wersji](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/latest). |
| 17 | Instalacja pakietu OMS nie powiodła się. Przejrzyj dane wyjściowe polecenia dla błędu głównego. |
| 19 | Instalacja pakietu OMI nie powiodła się. Przejrzyj dane wyjściowe polecenia dla błędu głównego. |
| 20 | Instalacja pakietu SCX nie powiodła się. Przejrzyj dane wyjściowe polecenia dla błędu głównego. |
| 21 | Instalacja zestawów dostawcy nie powiodła się. Przejrzyj dane wyjściowe polecenia dla błędu głównego. |
| 22 | Instalacja pakietu pakietowego nie powiodła się. Przejrzyj dane wyjściowe polecenia dla błędu głównego |
| 23 | Pakiet SCX lub OMI jest już zainstalowany. Użyj `--upgrade` zamiast `--install` zainstalować pakiet powłoki. |
| 30 | Wewnętrzny błąd pakietu. Plik [GitHub problem](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) ze szczegółami z danych wyjściowych. |
| 55 | Nieobsługiwała wersja openssl LUB nie można połączyć się z usługą Azure Monitor lub dpkg jest zablokowany lub brak programu curl. |
| 61 | Brak biblioteki ctypes języka Python. Zainstaluj bibliotekę lub pakiet ctypes języka Python (python-ctypes). |
| 62 | Brak programu smoły, zainstalować smołę. |
| 63 | Brak programu sed, zainstalować sed. |
| 64 | Brak programu curl, zainstalować curl. |
| 65 | Brak programu gpg, zainstaluj gpg. |

## <a name="onboarding-error-codes"></a>Kody błędów dołączania

| Kod błędu | Znaczenie |
| --- | --- |
| 2 | Nieprawidłowa opcja podana do skryptu omsadmin. Uruchom `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` do użycia. |
| 3 | Nieprawidłowa konfiguracja dostarczona do skryptu omsadmin. Uruchom `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` do użycia. |
| 4 | Nieprawidłowy serwer proxy dostarczony do skryptu omsadmin. Sprawdź serwer proxy i zapoznaj się z naszą [dokumentacją dotyczącą korzystania z serwera proxy HTTP](log-analytics-agent.md#firewall-requirements). |
| 5 | 403 Błąd HTTP odebrany z usługi Azure Monitor. Szczegółowe informacje można znaleźć w pełnym pliku wyjściowym skryptu omsadmin. |
| 6 | Błąd HTTP nieobjęty 200 odebrany z usługi Azure Monitor. Szczegółowe informacje można znaleźć w pełnym pliku wyjściowym skryptu omsadmin. |
| 7 | Nie można połączyć się z usługą Azure Monitor. Szczegółowe informacje można znaleźć w pełnym pliku wyjściowym skryptu omsadmin. |
| 8 | Błąd dołączania do obszaru roboczego usługi Log Analytics. Szczegółowe informacje można znaleźć w pełnym pliku wyjściowym skryptu omsadmin. |
| 30 | Wewnętrzny błąd skryptu. Plik [GitHub problem](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) ze szczegółami z danych wyjściowych. |
| 31 | Identyfikator agenta generującego błąd. Plik [GitHub problem](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) ze szczegółami z danych wyjściowych. |
| 32 | Błąd generowania certyfikatów. Szczegółowe informacje można znaleźć w pełnym pliku wyjściowym skryptu omsadmin. |
| 33 | Błąd generowania metakonfiguracji dla omsconfig. Plik [GitHub problem](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) ze szczegółami z danych wyjściowych. |
| 34 | Skrypt generowania metakonfiguracji nie jest obecny. Ponów próbę `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key>`przy dołączania za pomocą pliku . |

## <a name="enable-debug-logging"></a>Włączanie rejestrowania debugowania
### <a name="oms-output-plugin-debug"></a>Debugowanie wtyczki wyjściowej OMS
 FluentD pozwala na poziomy rejestrowania specyficzne dla wtyczek, co pozwala określić różne poziomy dziennika dla wejść i wyjść. Aby określić inny poziom dziennika dla danych wyjściowych `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`systemu OMS, należy edytować ogólną konfigurację agenta w programie .  

 W wtyczce wyjściowej systemu OMS przed zakończeniem `info` pliku `debug`konfiguracyjnego zmień `log_level` właściwość z:

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

Rejestrowanie debugowania umożliwia wyświetlanie wsadowych przekazywania do usługi Azure Monitor oddzielone według typu, liczby elementów danych i czasu poświęconego do wysłania:

*Przykładowy dziennik z włączonym debugowaniem:*

```
Success sending oms.nagios x 1 in 0.14s
Success sending oms.omi x 4 in 0.52s
Success sending oms.syslog.authpriv.info x 1 in 0.91s
```

### <a name="verbose-output"></a>Pełne dane wyjściowe
Zamiast używać wtyczki wyjściowej OMS można również wyprowadzać elementy danych bezpośrednio do `stdout`, co jest widoczne w agencie usługi Log Analytics dla pliku dziennika systemu Linux.

W pliku konfiguracyjnym `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`agenta ogólnego usługi Log Analytics `#` w programie , skomentuj wtyczkę wyjściową OMS, dodając przed każdym wierszem:

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

Poniżej wtyczki wyjściowej, odkomentuj `#` następującą sekcję, usuwając przed każdą linię:

```
<match **>
  type stdout
</match>
```

## <a name="issue--unable-to-connect-through-proxy-to-azure-monitor"></a>Problem: nie można połączyć się za pośrednictwem serwera proxy z usługą Azure Monitor

### <a name="probable-causes"></a>Prawdopodobne przyczyny
* Serwer proxy określony podczas dołączania był niepoprawny
* Punkty końcowe usługi Azure Monitor i usługa Azure Automation nie są umieszczane na białej liście w centrum danych 

### <a name="resolution"></a>Rozwiązanie
1. Reonboard do usługi Azure Monitor z agentem usługi Log `-v` Analytics dla systemu Linux przy użyciu następującego polecenia z włączoną opcją. Umożliwia pełne dane wyjściowe agenta łączącego się za pośrednictwem serwera proxy z usługą Azure Monitor. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key> -p <Proxy Conf> -v`

2. Przejrzyj sekcję [Aktualizuj ustawienia serwera proxy,](agent-manage.md#update-proxy-settings) aby sprawdzić, czy agent został prawidłowo skonfigurowany do komunikowania się za pośrednictwem serwera proxy.    

3. Sprawdź, czy punkty końcowe opisane na liście [wymagań zapory sieciowej](log-analytics-agent.md#firewall-requirements) usługi Azure Monitor są poprawnie dodawane do listy dozwolonych. Jeśli używasz usługi Azure Automation, kroki konfiguracji sieci niezbędne są połączone powyżej, jak również.

## <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>Problem: Podczas próby wsuń na pokład pojawia się błąd 403

### <a name="probable-causes"></a>Prawdopodobne przyczyny
* Data i godzina są niepoprawne na serwerze Linux 
* Identyfikator obszaru roboczego i używany klucz obszaru roboczego są nieprawidłowe

### <a name="resolution"></a>Rozwiązanie

1. Sprawdź godzinę na serwerze Linux z datą polecenia. Jeśli czas wynosi +/- 15 minut od bieżącego czasu, dołączanie nie powiedzie się. Aby poprawić tę aktualizację, datę i/lub strefę czasową serwera Linux. 
2. Sprawdź, czy zainstalowano najnowszą wersję agenta usługi Log Analytics dla systemu Linux.  Najnowsza wersja powiadamia teraz, jeśli pochylenie czasu powoduje błąd dołączania.
3. Reonboard przy użyciu poprawnego identyfikatora obszaru roboczego i klucza obszaru roboczego zgodnie z instrukcjami instalacji we wcześniejszej części tego artykułu.

## <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>Problem: Po wydzieleniu pliku dziennika jest widoczny błąd 500 i 404
Jest to znany problem, który występuje przy pierwszym przekazywaniu danych systemu Linux do obszaru roboczego usługi Log Analytics. Nie ma to wpływu na wysyłane dane ani na środowisko usługi.


## <a name="issue-you-see-omiagent-using-100-cpu"></a>Problem: Widzisz omiagent przy użyciu 100% CPU

### <a name="probable-causes"></a>Prawdopodobne przyczyny
Regresja w pakiecie nss-pem [v1.0.3-5.el7](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-7.el7.x86_64.rpm.html) spowodowała poważny problem z wydajnością, który widzieliśmy w dystrybucji Redhat/Centos 7.x. Aby dowiedzieć się więcej na temat tego problemu, sprawdź następującą dokumentację: Bug [1667121 Regresja wydajności w libcurl](https://bugzilla.redhat.com/show_bug.cgi?id=1667121).

Błędy związane z wydajnością nie zdarzają się cały czas i są bardzo trudne do odtworzenia. Jeśli wystąpi taki problem z omiagentem, należy użyć skryptu omiHighCPUDiagnostics.sh który będzie zbierać ślad stosu omiagenta po przekroczeniu pewnego progu.

1. Pobierz skrypt <br/>
`wget https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/tools/LogCollector/source/omiHighCPUDiagnostics.sh`

2. Uruchom diagnostykę przez 24 godziny z 30% progiem procesora <br/>
`bash omiHighCPUDiagnostics.sh --runtime-in-min 1440 --cpu-threshold 30`

3. Callstack będzie po cenach dumpingowych w pliku omiagent_trace, Jeśli zauważysz wiele curl i NSS wywołania funkcji, wykonaj kroki rozdzielczości poniżej.

### <a name="resolution-step-by-step"></a>Rozdzielczość (krok po kroku)

1. Uaktualnienie pakietu nss-pem do [wersji 1.0.3-5.el7_6.1](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-7.el7.x86_64.rpm.html). <br/>
`sudo yum upgrade nss-pem`

2. Jeśli nss-pem nie jest dostępny do uaktualnienia (najczęściej dzieje się na Centos), a następnie downgrade curl do 7.29.0-46. Jeśli przez pomyłkę uruchomisz "aktualizację yum", następnie curl zostanie uaktualniony do 7.29.0-51 i problem powtórzy się. <br/>
`sudo yum downgrade curl libcurl`

3. Uruchom ponownie OMI: <br/>
`sudo scxadmin -restart`

## <a name="issue-you-are-not-seeing-any-data-in-the-azure-portal"></a>Problem: nie widzisz żadnych danych w witrynie Azure portal

### <a name="probable-causes"></a>Prawdopodobne przyczyny

- Dołączanie do usługi Azure Monitor nie powiodło się
- Połączenie z usługą Azure Monitor jest zablokowane
- Kopia zapasowa agenta usługi Log Analytics dla danych systemu Linux

### <a name="resolution"></a>Rozwiązanie
1. Sprawdź, czy dołączanie usługi Azure Monitor zakończyło się pomyślnie, sprawdzając, czy istnieje następujący plik:`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`
2. Reonboard przy `omsadmin.sh` użyciu instrukcji wiersza polecenia
3. Jeśli używasz serwera proxy, zapoznaj się z krokami rozpoznawania serwera proxy podanymi wcześniej.
4. W niektórych przypadkach, gdy agent usługi Log Analytics dla systemu Linux nie może komunikować się z usługą, dane agenta są umieszczane w kolejce do pełnego rozmiaru buforu, który wynosi 50 MB. Agent powinien zostać ponownie uruchomiony, uruchamiając następujące polecenie: `/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]`. 

    >[!NOTE]
    >Ten problem został rozwiązany w wersji agenta 1.1.0-28 i nowszych.


## <a name="issue-you-are-not-seeing-forwarded-syslog-messages"></a>Problem: Nie widzisz przesłanych dalej wiadomości Syslog 

### <a name="probable-causes"></a>Prawdopodobne przyczyny
* Konfiguracja zastosowana do serwera Linux nie zezwala na zbieranie wysyłanych obiektów i/lub poziomów dzienników
* Syslog nie jest poprawnie przesyłany dalej do serwera Linux
* Liczba wiadomości przesyłanych dalej na sekundę jest zbyt duża, aby podstawowa konfiguracja agenta usługi Log Analytics dla systemu Linux obsługiwała

### <a name="resolution"></a>Rozwiązanie
* Sprawdź konfigurację w obszarze roboczym usługi Log Analytics dla Syslog ma wszystkie udogodnienia i poziomy dziennika poprawne. Przeglądanie [konfigurowania kolekcji Syslog w witrynie Azure portal](../../azure-monitor/platform/data-sources-syslog.md#configure-syslog-in-the-azure-portal)
* Sprawdź, czy natywne demony`rsyslog` `syslog-ng`wiadomości syslog ( , ) są w stanie odbierać przekazywane wiadomości
* Sprawdź ustawienia zapory na serwerze Syslog, aby upewnić się, że wiadomości nie są blokowane
* Symulowanie wiadomości Syslog do `logger` usługi Log Analytics przy użyciu polecenia
  * `logger -p local0.err "This is my test message"`

## <a name="issue-you-are-receiving-errno-address-already-in-use-in-omsagent-log-file"></a>Problem: Otrzymujesz adres Errno już używany w pliku dziennika omsagent
Jeśli widzisz `[error]: unexpected error error_class=Errno::EADDRINUSE error=#<Errno::EADDRINUSE: Address already in use - bind(2) for "127.0.0.1" port 25224>` w omsagent.log.

### <a name="probable-causes"></a>Prawdopodobne przyczyny
Ten błąd wskazuje, że rozszerzenie diagnostyki systemu Linux (LAD) jest instalowane obok rozszerzenia maszyny Wirtualnej systemu Windows usługi Log Analytics linux i używa tego samego portu do zbierania danych syslog jako omsagent.

### <a name="resolution"></a>Rozwiązanie
1. Jako katalog główny wykonaj następujące polecenia (należy pamiętać, że 25224 jest przykładem i możliwe jest, że w twoim środowisku zostanie wyświetlony inny numer portu używany przez LAD):

    ```
    /opt/microsoft/omsagent/bin/configure_syslog.sh configure LAD 25229

    sed -i -e 's/25224/25229/' /etc/opt/microsoft/omsagent/LAD/conf/omsagent.d/syslog.conf
    ```

    Następnie należy edytować `rsyslogd` poprawny lub `syslog_ng` konfiguracyjny plik i zmienić konfigurację związaną z LAD, aby zapisać na port 25229.

2. Jeśli maszyna wirtualna `rsyslogd`jest uruchomiona, plik, `/etc/rsyslog.d/95-omsagent.conf` który ma zostać zmodyfikowany, to: (jeśli istnieje, w inny sposób). `/etc/rsyslog` Jeśli maszyna wirtualna `syslog_ng`jest uruchomiona, plik, `/etc/syslog-ng/syslog-ng.conf`który ma zostać zmodyfikowany, to: .
3. Uruchom ponownie omsagent `sudo /opt/microsoft/omsagent/bin/service_control restart`.
4. Uruchom ponownie usługę syslog.

## <a name="issue-you-are-unable-to-uninstall-omsagent-using-purge-option"></a>Problem: Nie można odinstalować omsagenta przy użyciu opcji przeczyszczać

### <a name="probable-causes"></a>Prawdopodobne przyczyny

* Jest zainstalowane rozszerzenie diagnostyczne systemu Linux
* Rozszerzenie diagnostyczne Linuksa zostało zainstalowane i odinstalowane, ale nadal widzisz błąd dotyczący omsagent używany przez mdsd i nie można go usunąć.

### <a name="resolution"></a>Rozwiązanie
1. Odinstaluj rozszerzenie diagnostyczne systemu Linux (LAD).
2. Usuń pliki rozszerzenia diagnostycznego systemu Linux z urządzenia, `/var/lib/waagent/Microsoft.Azure.Diagnostics.LinuxDiagnostic-<version>/` `/var/opt/microsoft/omsagent/LAD/`jeśli znajdują się w następującej lokalizacji: i .

## <a name="issue-you-cannot-see-data-any-nagios-data"></a>Problem: Nie widać żadnych danych Nagios 

### <a name="probable-causes"></a>Prawdopodobne przyczyny
* Użytkownik Omsagent nie ma uprawnień do odczytu z pliku dziennika Nagios
* Źródło i filtr Nagios nie zostały odkomentowane z pliku omsagent.conf

### <a name="resolution"></a>Rozwiązanie
1. Dodaj użytkownika omsagent do odczytu z pliku Nagios, postępując zgodnie z tymi [instrukcjami](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#nagios-alerts).
2. W agencie usługi Log Analytics `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`dla ogólnego pliku konfiguracyjnego systemu Linux w programie upewnij się, że zarówno źródło Nagios, **jak** i filtr są niekomentowane.

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

## <a name="issue-you-are-not-seeing-any-linux-data"></a>Problem: Nie widzisz żadnych danych systemu Linux 

### <a name="probable-causes"></a>Prawdopodobne przyczyny
* Dołączanie do usługi Azure Monitor nie powiodło się
* Połączenie z usługą Azure Monitor jest zablokowane
* Maszyna wirtualna została ponownie uruchomiona
* Pakiet OMI został ręcznie uaktualniony do nowszej wersji w porównaniu do tego, co zostało zainstalowane przez agenta usługi Log Analytics dla pakietu Linux
* *Nie znaleziono* błędu błędu w `omsconfig.log` pliku dziennika dsc
* Kopia zapasowa agenta usługi Log Analytics dla danych
* Rejestry DSC *Bieżąca konfiguracja nie istnieje. Wykonaj polecenie Start-DscConfiguration z parametrem -Path, aby określić plik konfiguracyjny i najpierw utworzyć bieżącą konfigurację.* w `omsconfig.log` pliku dziennika, ale nie `PerformRequiredConfigurationChecks` istnieje komunikat dziennika o operacjach.

### <a name="resolution"></a>Rozwiązanie
1. Zainstaluj wszystkie zależności, takie jak pakiet poddany inspekcji.
2. Sprawdź, czy dołączanie do usługi Azure Monitor zakończyło się `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`pomyślnie, sprawdzając, czy istnieje następujący plik: .  Jeśli tak nie było, ponownie za pomocą omsadmin.sh [instrukcji](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line)wiersza polecenia .
4. Jeśli używasz serwera proxy, sprawdź kroki rozwiązywania problemów z serwerem proxy powyżej.
5. W niektórych systemach dystrybucji platformy Azure demon serwera Omi omi nie uruchamia się po ponownym uruchomieniu maszyny wirtualnej. Spowoduje to, że nie będzie widać inspekcji, ChangeTracking lub UpdateManagement danych związanych z rozwiązaniem. Obejście polega na ręcznym uruchomieniu serwera `sudo /opt/omi/bin/service_control restart`omi przez uruchomienie .
6. Po ręcznie uaktualnieniu pakietu OMI do nowszej wersji należy go ręcznie uruchomić, aby agent usługi Log Analytics kontynuował działanie. Ten krok jest wymagany dla niektórych dystrybucji, w których serwer OMI nie uruchamia się automatycznie po uaktualnieniu. Uruchom, `sudo /opt/omi/bin/service_control restart` aby ponownie uruchomić OMI.
7. Jeśli w pliku omsconfig.log *zostanie wyświetlony* komunikat `sudo /opt/omi/bin/service_control restart`Nrd klasa zasobów nie znaleziono błędu, uruchom plik .
8. W niektórych przypadkach, gdy agent usługi Log Analytics dla systemu Linux nie może rozmawiać z usługą Azure Monitor, kopia zapasowa danych agenta jest archiwizowany do pełnego rozmiaru buforu: 50 MB. Agent powinien zostać ponownie uruchomiony, `/opt/microsoft/omsagent/bin/service_control restart`uruchamiając następujące polecenie .

    >[!NOTE]
    >Ten problem został rozwiązany w wersji agenta 1.1.0-28 lub nowszej
    >

* Jeśli `omsconfig.log` plik dziennika nie `PerformRequiredConfigurationChecks` wskazuje, że operacje są uruchamiane okresowo w systemie, może to być problem z cron zadanie/usługa. Upewnij się, że `/etc/cron.d/OMSConsistencyInvoker`zadanie cron istnieje w obszarze . W razie potrzeby uruchom następujące polecenia, aby utworzyć zadanie cron:

    ```
    mkdir -p /etc/cron.d/
    echo "*/15 * * * * omsagent /opt/omi/bin/OMSConsistencyInvoker >/dev/null 2>&1" | sudo tee /etc/cron.d/OMSConsistencyInvoker
    ```

    Upewnij się również, że usługa cron jest uruchomiona. Możesz użyć `service cron status` z Debian, Ubuntu, `service crond status` SUSE lub z RHEL, CentOS, Oracle Linux, aby sprawdzić status tej usługi. Jeśli usługa nie istnieje, można zainstalować pliki binarne i uruchomić usługę, korzystając z następujących czynności:

    **Ubuntu/Debian**

    ```
    # To Install the service binaries
    sudo apt-get install -y cron
    # To start the service
    sudo service cron start
    ```

    **SUSE**

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

## <a name="issue-when-configuring-collection-from-the-portal-for-syslog-or-linux-performance-counters-the-settings-are-not-applied"></a>Problem: Podczas konfigurowania kolekcji z portalu dla liczników wydajności Syslog lub Linux ustawienia nie są stosowane

### <a name="probable-causes"></a>Prawdopodobne przyczyny
* Agent analizy dzienników dla systemu Linux nie odebrał najnowszej konfiguracji
* Zmienione ustawienia w portalu nie zostały zastosowane

### <a name="resolution"></a>Rozwiązanie
**Tło:** `omsconfig` jest agentem usługi Log Analytics dla agenta konfiguracji systemu Linux, który szuka nowej konfiguracji po stronie portalu co pięć minut. Ta konfiguracja jest następnie stosowana do agenta usługi Log Analytics dla plików konfiguracyjnych systemu Linux znajdujących się pod adresem /etc/opt/microsoft/omsagent/conf/omsagent.conf.

* W niektórych przypadkach agent usługi Log Analytics dla agenta konfiguracji systemu Linux może nie być w stanie komunikować się z usługą konfiguracji portalu, co powoduje, że najnowsza konfiguracja nie jest stosowana.
  1. Sprawdź, `omsconfig` czy agent jest `dpkg --list omsconfig` `rpm -qi omsconfig`zainstalowany przez uruchomienie lub .  Jeśli nie jest zainstalowany, zainstaluj ponownie najnowszą wersję agenta usługi Log Analytics dla systemu Linux.

  2. Sprawdź, `omsconfig` czy agent może komunikować się z `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`usługą Azure Monitor, uruchamiając następujące polecenie . To polecenie zwraca konfigurację, którą agent otrzymuje z usługi, w tym ustawienia Sysloga, liczniki wydajności systemu Linux i dzienniki niestandardowe. Jeśli to polecenie nie powiedzie się, uruchom następujące polecenie `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py'`. To polecenie wymusza agenta omsconfig do rozmowy z usługą Azure Monitor i pobrać najnowszą konfigurację.

## <a name="issue-you-are-not-seeing-any-custom-log-data"></a>Problem: Nie widzisz żadnych niestandardowych danych dziennika 

### <a name="probable-causes"></a>Prawdopodobne przyczyny
* Dołączanie do usługi Azure Monitor nie powiodło się.
* Ustawienie **Zastosuj następującą konfigurację do moich serwerów Linuksa** nie zostało wybrane.
* omsconfig nie odebrał najnowszej konfiguracji dziennika niestandardowego z usługi.
* Agent usługi Log `omsagent` Analytics dla użytkownika systemu Linux nie może uzyskać dostępu do dziennika niestandardowego z powodu uprawnień lub nie został znaleziony.  Mogą pojawić się następujące błędy:
 * `[DATETIME] [warn]: file not found. Continuing without tailing it.`
 * `[DATETIME] [error]: file not accessible by omsagent.`
* Znany problem z funkcją wyścigu rozwiązany w agencie usługi Log Analytics dla systemu Linux w wersji 1.1.0-217

### <a name="resolution"></a>Rozwiązanie
1. Sprawdź, czy dołączanie do usługi Azure Monitor zakończyło się `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`pomyślnie, sprawdzając, czy istnieje następujący plik: . Jeśli nie, albo:  

  1. Reonboard przy użyciu [instrukcji](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line)wiersza polecenia omsadmin.sh .
  2. W obszarze **Ustawienia zaawansowane** w witrynie Azure portal upewnij się, że ustawienie **Zastosuj następującą konfigurację do moich serwerów systemu Linux** jest włączone.  

2. Sprawdź, `omsconfig` czy agent może komunikować się z `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`usługą Azure Monitor, uruchamiając następujące polecenie .  To polecenie zwraca konfigurację, którą agent otrzymuje z usługi, w tym ustawienia Sysloga, liczniki wydajności systemu Linux i dzienniki niestandardowe. Jeśli to polecenie nie powiedzie się, uruchom następujące polecenie `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py'`. To polecenie wymusza agenta omsconfig do rozmowy z usługą Azure Monitor i pobrać najnowszą konfigurację.

**Tło:** Zamiast agenta usługi Log Analytics dla systemu Linux `root`działającego jako `omsagent` uprzywilejowany użytkownik — agent działa jako użytkownik. W większości przypadków użytkownik musi udzielić wyraźnego uprawnienia, aby niektóre pliki były odczytywane. Aby udzielić `omsagent` uprawnień użytkownikowi, uruchom następujące polecenia:

1. Dodawanie `omsagent` użytkownika do określonej grupy`sudo usermod -a -G <GROUPNAME> <USERNAME>`
2. Udzielanie uniwersalnego dostępu do odczytu do wymaganego pliku`sudo chmod -R ugo+rx <FILE DIRECTORY>`

Istnieje znany problem z chorobą wyścigu z agentem usługi Log Analytics dla wersji systemu Linux wcześniej niż 1.1.0-217. Po aktualizacji do najnowszego agenta, uruchom następujące polecenie, aby `sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`uzyskać najnowszą wersję wtyczki wyjściowej .

## <a name="issue-you-are-trying-to-reonboard-to-a-new-workspace"></a>Problem: próbujesz ponownie zakuć w nowy obszar roboczy
Podczas próby ponownego zaoczniwania agenta do nowego obszaru roboczego, konfiguracja agenta usługi Log Analytics musi zostać oczyszczona przed reonboardingiem. Aby wyczyścić starą konfigurację z agenta, uruchom pakiet powłoki za pomocą`--purge`

```
sudo sh ./omsagent-*.universal.x64.sh --purge
```
Lub

```
sudo sh ./onboard_agent.sh --purge
```

Po skorzystaniu z opcji `--purge` można kontynuować ponowne zaoszerchicienie

## <a name="log-analytics-agent-extension-in-the-azure-portal-is-marked-with-a-failed-state-provisioning-failed"></a>Rozszerzenie agenta usługi Log Analytics w witrynie Azure portal jest oznaczone stanem nie powiodło się: inicjowanie obsługi administracyjnej nie powiodło się

### <a name="probable-causes"></a>Prawdopodobne przyczyny
* Agent usługi Log Analytics został usunięty z systemu operacyjnego
* Usługa agenta usługi usługi Usługi usługi Log Analytics jest wyłączona, wyłączona lub nieskonfigurowana

### <a name="resolution"></a>Rozwiązanie 
Wykonaj następujące kroki, aby rozwiązać problem.
1. Usuń rozszerzenie z witryny Azure portal.
2. Zainstaluj agenta zgodnie z [instrukcjami](../../azure-monitor/learn/quick-collect-linux-computer.md).
3. Uruchom ponownie agenta, uruchamiając następujące polecenie: `sudo /opt/microsoft/omsagent/bin/service_control restart`.
* Poczekaj kilka minut, a zmiany stanu inicjowania obsługi **administracyjnej powiodły się**.


## <a name="issue-the-log-analytics-agent-upgrade-on-demand"></a>Problem: Uaktualnienie agenta usługi Log Analytics na żądanie

### <a name="probable-causes"></a>Prawdopodobne przyczyny

Pakiety agenta usługi Log Analytics na hoście są nieaktualne.

### <a name="resolution"></a>Rozwiązanie 
Wykonaj następujące kroki, aby rozwiązać problem.

1. Sprawdź najnowszą wersję na [stronie](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/).
2. Pobierz skrypt instalacji (1.4.2-124 jako przykładowa wersja):

    ```
    wget https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.2-124/omsagent-1.4.2-124.universal.x64.sh
    ```

3. Uaktualnij pakiety, `sudo sh ./omsagent-*.universal.x64.sh --upgrade`wykonując plik .
