---
title: Narzędzie diagnostyczne do rozwiązywania problemów z urządzeniem StorSimple 8000 | Dokumenty firmy Microsoft
description: W tym artykule opisano tryby urządzenia StorSimple i wyjaśniono, jak zmienić tryb urządzenia za pomocą programu Windows PowerShell for StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 48bd909eefbaea15cf6ca2427e106ad9bc0ffbb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298742"
---
# <a name="use-the-storsimple-diagnostics-tool-to-troubleshoot-8000-series-device-issues"></a>Narzędzie diagnostyki StorSimple umożliwia rozwiązywanie problemów z urządzeniami serii 8000

## <a name="overview"></a>Omówienie

Narzędzie Diagnostyka StorSimple diagnozuje problemy związane ze zdrowiem systemu, wydajności, sieci i sprzętu dla urządzenia StorSimple. Narzędzie diagnostyczne może być używane w różnych scenariuszach. Scenariusze te obejmują planowanie obciążenia, wdrażanie urządzenia StorSimple, ocenę środowiska sieciowego i określanie wydajności urządzenia operacyjnego. W tym artykule przedstawiono omówienie narzędzia diagnostycznego i opisano, jak narzędzie może być używane z urządzeniem StorSimple.

Narzędzie diagnostyczne jest przeznaczone przede wszystkim dla urządzeń lokalnych serii StorSimple 8000 (8100 i 8600).

## <a name="run-diagnostics-tool"></a>Uruchom narzędzie diagnostyczne

To narzędzie można uruchomić za pośrednictwem interfejsu programu Windows PowerShell urządzenia StorSimple. Istnieją dwa sposoby uzyskiwania dostępu do interfejsu lokalnego urządzenia:

* [Użyj PuTTY, aby połączyć się z konsolą szeregową urządzenia](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
* [Zdalny dostęp do narzędzia za pośrednictwem programu Windows PowerShell for StorSimple](storsimple-8000-remote-connect.md).

W tym artykule zakładamy, że masz podłączony do konsoli szeregowej urządzenia za pośrednictwem PuTTY.

#### <a name="to-run-the-diagnostics-tool"></a>Aby uruchomić narzędzie diagnostyczne

Po nawiązaniu połączenia z interfejsem programu Windows PowerShell urządzenia wykonaj następujące czynności, aby uruchomić polecenie cmdlet.
1. Zaloguj się do konsoli szeregowej urządzenia, wykonując czynności opisane w [aplikacji Użyj putty, aby połączyć się z konsolą szeregową urządzenia](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).

2. Wpisz następujące polecenie:

    `Invoke-HcsDiagnostics`

    Jeśli parametr zakresu nie jest określony, polecenie cmdlet wykonuje wszystkie testy diagnostyczne. Testy te obejmują system, kondycję składnika sprzętowego, sieć i wydajność. 
    
    Aby uruchomić tylko określony test, należy określić parametr zakresu. Na przykład, aby uruchomić tylko test sieci, wpisz

    `Invoke-HcsDiagnostics -Scope Network`

3. Zaznacz i skopiuj dane wyjściowe z okna PuTTY do pliku tekstowego w celu dalszej analizy.

## <a name="scenarios-to-use-the-diagnostics-tool"></a>Scenariusze użycia narzędzia diagnostycznego

Narzędzie diagnostyczne służy do rozwiązywania problemów z siecią, wydajnością, kondycją systemu i sprzętu systemu. Oto kilka możliwych scenariuszy:

* **Urządzenie w trybie offline** — urządzenie z serii StorSimple 8000 jest w trybie offline. Jednak z interfejsu programu Windows PowerShell wydaje się, że oba kontrolery są uruchomione.
    * Za pomocą tego narzędzia można określić stan sieci.
         
         > [!NOTE]
         > Nie należy używać tego narzędzia do oceny wydajności i ustawień sieciowych na urządzeniu przed rejestracją (lub konfigurowaniem za pomocą kreatora konfiguracji). Prawidłowy adres IP jest przypisany do urządzenia podczas kreatora konfiguracji i rejestracji. Można uruchomić to polecenie cmdlet, na urządzeniu, które nie jest zarejestrowane, dla kondycji sprzętu i systemu. Użyj parametru scope, na przykład:
         >
         > `Invoke-HcsDiagnostics -Scope Hardware`
         >
         > `Invoke-HcsDiagnostics -Scope System`

* **Trwałe problemy z urządzeniem** — występują problemy z urządzeniem, które wydają się być nadal. Na przykład rejestracja nie jest w stanie. Mogą również występować problemy z urządzeniem po pomyślnym zarejestrowaniu urządzenia i uruchomieniu przez pewien czas.
    * W takim przypadku należy użyć tego narzędzia do wstępnego rozwiązywania problemów przed zalogowaniem żądania usługi za pomocą pomocy technicznej firmy Microsoft. Zaleca się uruchomienie tego narzędzia i przechwycenie danych wyjściowych tego narzędzia. Następnie można podać to dane wyjściowe do pomocy technicznej, aby przyspieszyć rozwiązywanie problemów.
    * Jeśli występują błędy dowolnego składnika sprzętowego lub klastra, należy zalogować się do żądania pomocy technicznej.

* **Niska wydajność urządzenia** — urządzenie StorSimple jest powolne.
    * W takim przypadku uruchom to polecenie cmdlet z parametrem zakresu ustawionym na wydajność. Analizowanie danych wyjściowych. Otrzymasz opóźnienia odczytu i zapisu w chmurze. Użyj zgłoszonych opóźnień jako maksymalnego osiągalne gołym celem, czynnik pewne obciążenie dla wewnętrznego przetwarzania danych, a następnie wdrożyć obciążeń w systemie. Aby uzyskać więcej informacji, przejdź do strony [Użyj testu sieciowego, aby rozwiązać problem z wydajnością urządzenia](#network-test).


## <a name="diagnostics-test-and-sample-outputs"></a>Testy diagnostyczne i wyjścia próbek

### <a name="hardware-test"></a>Test sprzętu

Ten test określa stan składników sprzętowych, oprogramowania układowego USM i oprogramowania układowego dysku uruchomionego w systemie.

* Zgłoszone składniki sprzętowe to składniki, które nie przeszły testu lub nie są obecne w systemie.
* Wersje oprogramowania układowego i oprogramowania układowego USM są zgłaszane dla kontrolera 0, kontrolera 1 i współużytkowanych składników w systemie. Aby uzyskać pełną listę składników sprzętu, przejdź do:

    * [Komponenty w obudowie podstawowej](storsimple-8000-monitor-hardware-status.md#component-list-for-primary-enclosure-of-storsimple-device)
    * [Komponenty w obudowie EBOD](storsimple-8000-monitor-hardware-status.md#component-list-for-ebod-enclosure-of-storsimple-device)

> [!NOTE]
> Jeśli raporty testów sprzętu nie powiodły się, [zaloguj się do żądania usługi za pomocą pomocy technicznej firmy Microsoft](storsimple-8000-contact-microsoft-support.md).

#### <a name="sample-output-of-hardware-test-run-on-an-8100-device"></a>Przykładowe dane wyjściowe testu sprzętowego na urządzeniu 8100

Oto przykładowe dane wyjściowe z urządzenia StorSimple 8100. W urządzeniu modelu 8100 obudowa EBOD nie jest obecna. W związku z tym składniki sterownika EBOD nie są zgłaszane.

```
Controller0>Invoke-HcsDiagnostics -Scope Hardware
Running hardware diagnostics ...
--------------------------------------------------
Hardware components failed or not present
----------------------

           Type           State      Controller           Index     EnclosureId
           ----           -----      ----------           -----     -----------
...rVipResource      NotPresent            None               1            None
...rVipResource      NotPresent            None               2            None
...rVipResource      NotPresent            None               3            None
...rVipResource      NotPresent            None               4            None
...rVipResource      NotPresent            None               5            None
...rVipResource      NotPresent            None               6            None
...rVipResource      NotPresent            None               7            None
...rVipResource      NotPresent            None               8            None
...rVipResource      NotPresent            None               9            None
...rVipResource      NotPresent            None              10            None
...rVipResource      NotPresent            None              11            None

Firmware information
----------------------
TalladegaController : ActiveBIOS:0.45.0010
                      BackupBIOS:0.45.0006
                      MainCPLD:17.0.000b
                      ActiveBMCRoot:2.0.001F
                      BackupBMCRoot:2.0.001F
                      BMCBoot:2.0.0002
                      LsiFirmware:20.00.04.00
                      LsiBios:07.37.00.00
                      Battery1Firmware:06.2C
                      Battery2Firmware:06.2C
                      DomFirmware:X231600
                      CanisterFirmware:3.5.0.56
                      CanisterBootloader:5.03
                      CanisterConfigCRC:0x9134777A
                      CanisterVPDStructure:0x06
                      CanisterGEMCPLD:0x19
                      CanisterVPDCRC:0x142F7DC2
                      MidplaneVPDStructure:0x0C
                      MidplaneVPDCRC:0xA6BD4F64
                      MidplaneCPLD:0x10
                      PCM1Firmware:1.00|1.05
                      PCM1VPDStructure:0x05
                      PCM1VPDCRC:0x41BEF99C
                      PCM2Firmware:1.00|1.05
                      PCM2VPDStructure:0x05
                      PCM2VPDCRC:0x41BEF99C

EbodController      :
DisksFirmware       : SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08

TalladegaController : ActiveBIOS:0.45.0010
                      BackupBIOS:0.45.0006
                      MainCPLD:17.0.000b
                      ActiveBMCRoot:2.0.001F
                      BackupBMCRoot:2.0.001F
                      BMCBoot:2.0.0002
                      LsiFirmware:20.00.04.00
                      LsiBios:07.37.00.00
                      Battery1Firmware:06.2C
                      Battery2Firmware:06.2C
                      DomFirmware:X231600
                      CanisterFirmware:3.5.0.56
                      CanisterBootloader:5.03
                      CanisterConfigCRC:0x9134777A
                      CanisterVPDStructure:0x06
                      CanisterGEMCPLD:0x19
                      CanisterVPDCRC:0x142F7DC2
                      MidplaneVPDStructure:0x0C
                      MidplaneVPDCRC:0xA6BD4F64
                      MidplaneCPLD:0x10
                      PCM1Firmware:1.00|1.05
                      PCM1VPDStructure:0x05
                      PCM1VPDCRC:0x41BEF99C
                      PCM2Firmware:1.00|1.05
                      PCM2VPDStructure:0x05
                      PCM2VPDCRC:0x41BEF99C

EbodController      :
DisksFirmware       : SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08

--------------------------------------------------
```

### <a name="system-test"></a>Test systemu

Ten test raportuje informacje o systemie, dostępne aktualizacje, informacje klastra i informacje o usłudze urządzenia.

* Informacje o systemie obejmują model, numer seryjny urządzenia, strefę czasową, stan kontrolera i szczegółową wersję oprogramowania działającą w systemie. Aby zrozumieć różne parametry systemu zgłaszane jako dane [wyjściowe,](#appendix-interpreting-system-information)przejdź do strony Interpreting system information .

* Dostępność aktualizacji informuje, czy tryby regularne i konserwacji są dostępne i ich nazwy skojarzonych pakietów. Jeśli `RegularUpdates` `MaintenanceModeUpdates` i `false`są , oznacza to, że aktualizacje nie są dostępne. Urządzenie jest aktualne.
* Informacje klastra zawierają informacje o różnych składnikach logicznych wszystkich grup klastra HCS i ich odpowiednich stanach. Jeśli w tej sekcji raportu jest widoczna grupa klastrów trybu offline, [skontaktuj się z pomocą techniczną firmy Microsoft](storsimple-8000-contact-microsoft-support.md).
* Informacje o usłudze obejmują nazwy i stany wszystkich usług HCS i CiS uruchomionych na urządzeniu. Te informacje są przydatne dla pomocy technicznej firmy Microsoft w rozwiązywaniu problemu z urządzeniem.

#### <a name="sample-output-of-system-test-run-on-an-8100-device"></a>Przykładowe wyjście testu systemowego na urządzeniu 8100

Oto przykładowe dane wyjściowe testu systemu na urządzeniu 8100.

```
Controller0>Invoke-HcsDiagnostics -Scope System
Running system diagnostics ...
--------------------------------------------------

System information
----------------------
Controller0:

InstanceId              : 7382407f-a56b-4622-8f3f-756fe04cfd38
Name                    : 8100-SHX0991003G467K
Model                   : 8100
SerialNumber            : SHX0991003G467K
TimeZone                : (UTC-08:00) Pacific Time (US & Canada)
CurrentController       : Controller0
ActiveController        : Controller0
Controller0Status       : Normal
Controller1Status       : Normal
SystemMode              : Normal
FriendlySoftwareVersion : StorSimple 8000 Series Update 4.0
HcsSoftwareVersion      : 6.3.9600.17820
ApiVersion              : 9.0.0.0
VhdVersion              : 6.3.9600.17759
OSVersion               : 6.3.9600.0
CisAgentVersion         : 1.0.9441.0
MdsAgentVersion         : 35.2.2.0
Lsisas2Version          : 2.0.78.0
Capacity                : 219902325555200
RemoteManagementMode    : Disabled
FipsMode                : Enabled

Controller1:
InstanceId              : 7382407f-a56b-4622-8f3f-756fe04cfd38
Name                    : 8100-SHX0991003G467K
Model                   : 8100
SerialNumber            : SHX0991003G467K
TimeZone                :
CurrentController       : Controller0
ActiveController        : Controller0
Controller0Status       : Normal
Controller1Status       : Normal
SystemMode              : Normal
FriendlySoftwareVersion : StorSimple 8000 Series Update 4.0
HcsSoftwareVersion      : 6.3.9600.17820
ApiVersion              : 9.0.0.0
VhdVersion              : 6.3.9600.17759
OSVersion               : 6.3.9600.0
CisAgentVersion         : 1.0.9441.0
MdsAgentVersion         : 35.2.2.0
Lsisas2Version          : 2.0.78.0
Capacity                : 219902325555200
RemoteManagementMode    : HttpsAndHttpEnabled
FipsMode                : Enabled

Update availability
----------------------
RegularUpdates              : False
MaintenanceModeUpdates      : False
RegularUpdatesTitle         : {}
MaintenanceModeUpdatesTitle : {}

Cluster information
----------------------
Name                          State OwnerGroup
----                          ----- ----------
ApplicationHostRLUA           Online HCS Cluster Group
Data0v4                       Online HCS Cluster Group
HCS Vnic Resource             Online HCS Cluster Group
hcs_cloud_connectivity_...    Online HCS Cluster Group
hcs_controller_replacement    Online HCS Cluster Group
hcs_datapath_service          Online HCS Cluster Group
hcs_management_service        Online HCS Cluster Group
hcs_nvram_service             Online HCS Cluster Group
hcs_passive_datapath          Online HCS Passive Cluster Group
hcs_platform_service          Online HCS Cluster Group
hcs_saas_agent_service        Online HCS Cluster Group
HddDataClusterDisk            Online HCS Cluster Group
HddMgmtClusterDisk            Online HCS Cluster Group
HddReplClusterDisk            Online HCS Cluster Group
iSCSI Target Server           Online HCS Cluster Group
NvramClusterDisk              Online HCS Cluster Group
SSAdminRLUA                   Online HCS Cluster Group
SsdDataClusterDisk            Online HCS Cluster Group
SsdNvramClusterDisk           Online HCS Cluster Group

Service information
----------------------
Name                                          Status DisplayName
----                                          ------ -----------
CiSAgentSvc                                   Stopped CiS Service Agent
hcs_cloud_connectivity_...                    Running hcs_cloud_connectivity...
hcs_controller_replacement                    Running HCS Controller Replace...
hcs_datapath_service                          Running HCS Datapath Service
hcs_management_service                        Running HCS Management Service
hcs_minishell                                 Running hcs_minishell
HCS_NVRAM_Service                             Running HCS NVRAM Service
hcs_passive_datapath                          Stopped HCS Passive Datapath S...
hcs_platform_service                          Running HCS Platform Monitor S...
hcs_saas_agent_service                        Running hcs_saas_agent_service
hcs_startup                                   Stopped hcs_startup
--------------------------------------------------
```

### <a name="network-test"></a>Test sieci

Ten test sprawdza stan interfejsów sieciowych, portów, łączności z serwerem DNS i NTP, certyfikatu TLS/SSL, poświadczeń konta magazynu, łączności z serwerami aktualizacji i łączności serwera proxy sieci Web na urządzeniu StorSimple.

#### <a name="sample-output-of-network-test-when-only-data0-is-enabled"></a>Przykładowe dane wyjściowe testu sieciowego, gdy włączono tylko funkcję DATA0

Oto przykładowe dane wyjściowe urządzenia 8100. Możesz zobaczyć na wyjściu, że:
* Tylko interfejsy sieciowe DATA 0 i DATA 1 są włączone i skonfigurowane.
* DATA 2 - 5 nie są włączone w portalu.
* Konfiguracja serwera DNS jest prawidłowa, a urządzenie może łączyć się za pośrednictwem serwera DNS.
* Łączność z serwerem NTP jest również w porządku.
* Porty 80 i 443 są otwarte. Jednak port 9354 jest zablokowany. W zależności od [wymagań sieci systemowej](storsimple-system-requirements.md)należy otworzyć ten port dla komunikacji magistrali usług.
* Certyfikat TLS/SSL jest ważny.
* Urządzenie może połączyć się z kontem pamięci masowej: _myss8000storageacct_.
* Łączność z serwerami aktualizacji jest prawidłowa.
* Serwer proxy sieci Web nie jest skonfigurowany na tym urządzeniu.

#### <a name="sample-output-of-network-test-when-data0-and-data1-are-enabled"></a>Przykładowe dane wyjściowe testu sieciowego po włączeniu funkcji DATA0 i DATA1

```
Controller0>Invoke-HcsDiagnostics -Scope Network
Running network diagnostics ....
--------------------------------------------------
Validating networks .....
Name                Entity              Result              Details
----                ------              ------              -------
Network interface   Data0               Valid
Network interface   Data1               Valid
Network interface   Data2               Not enabled
Network interface   Data3               Not enabled
Network interface   Data4               Not enabled
Network interface   Data5               Not enabled
DNS                 10.222.118.154      Valid
NTP                 time.windows.com    Valid
Port                80                  Open
Port                443                 Open
Port                9354                Blocked
SSL certificate     https://myss8000... Valid
Storage account ... myss8000storageacct Valid
URL                 http://download.... Valid
URL                 http://download.... Valid
Web proxy                               Not enabled         Web proxy is not...
--------------------------------------------------
```

### <a name="performance-test"></a>Test wydajnościowy

Ten test raportuje wydajność chmury za pośrednictwem opóźnienia odczytu i zapisu w chmurze dla urządzenia. To narzędzie może służyć do ustanowienia linii bazowej wydajności chmury, które można osiągnąć za pomocą StorSimple. Narzędzie raportuje maksymalną wydajność (najlepszy scenariusz dla opóźnień odczytu i zapisu), które można uzyskać dla połączenia.

Ponieważ narzędzie raportuje maksymalną osiągalną wydajność, możemy użyć zgłoszonych opóźnień odczytu i zapisu jako obiektów docelowych podczas wdrażania obciążeń.

Test symuluje rozmiary obiektów blob skojarzone z różnymi typami woluminów na urządzeniu. Regularne warstwowe i kopie zapasowe woluminów przypiętych lokalnie używają rozmiaru obiektu blob 64 KB. Woluminy warstwowe z zaznaczoną opcją archiwum używają rozmiaru danych obiektów blob o rozmiarze 512 KB. Jeśli urządzenie ma warstwowe i lokalnie przypięte woluminy skonfigurowane, tylko test odpowiadający rozmiarowi danych obiektów blob 64 KB jest uruchamiany.

Aby użyć tego narzędzia, wykonaj następujące czynności:

1.  Najpierw utwórz kombinację woluminów warstwowych i woluminów warstwowych z zaznaczoną opcją zarchiwizowanej. Ta akcja gwarantuje, że narzędzie uruchamia testy dla rozmiarów obiektów blob 64 KB i 512 KB.

2. Uruchom polecenie cmdlet po utworzeniu i skonfigurowaniu woluminów. Wpisz:

    `Invoke-HcsDiagnostics -Scope Performance`

3. Zanotuj opóźnienia odczytu i zapisu zgłoszone przez narzędzie. Ten test może potrwać kilka minut, zanim zgłosi wyniki.

4. Jeśli opóźnienia połączenia są w oczekiwanym zakresie, a następnie opóźnienia zgłaszane przez narzędzie może służyć jako maksymalny osiągalny cel podczas wdrażania obciążeń. Uwzględnij pewne obciążenie związane z wewnętrznym przetwarzaniem danych.

    Jeśli opóźnienia odczytu i zapisu zgłaszane przez narzędzie diagnostyczne są wysokie:

    1. Skonfiguruj usługę Analizy magazynu dla usług obiektów blob i przeanalizuj dane wyjściowe, aby zrozumieć opóźnienia dla konta magazynu platformy Azure. Aby uzyskać szczegółowe instrukcje, przejdź do [włączania i konfigurowania analizy pamięci masowej](../storage/common/storage-enable-and-view-metrics.md). Jeśli te opóźnienia są również wysokie i porównywalne z liczbami otrzymanymi z narzędzia Diagnostyka StorSimple, należy zarejestrować żądanie usługi za pomocą magazynu platformy Azure.

    2. Jeśli opóźnienia na koncie magazynu są małe, skontaktuj się z administratorem sieci, aby zbadać wszelkie problemy z opóźnieniami w sieci.

#### <a name="sample-output-of-performance-test-run-on-an-8100-device"></a>Przykładowe wyjście testu wydajności na urządzeniu 8100

```
Controller0>Invoke-HcsDiagnostics -Scope Performance
Running performance diagnostics...
--------------------------------------------------
Cloud performance: writing blobs
Cloud write latency: 194 ms using credential 'myss8000storageacct', blob size '64KB'
Cloud performance: reading blobs..
Cloud read latency: 544 ms using credential 'myss8000storageacct', blob size '64KB'
Cloud performance: writing blobs.
Cloud write latency: 369 ms using credential 'myss8000storageacct', blob size '512KB'
Cloud performance: reading blobs...
Cloud read latency: 4924 ms using credential 'myss8000storageacct', blob size '512KB'
--------------------------------------------------
Controller0>
```

## <a name="appendix-interpreting-system-information"></a>Załącznik: informacje o systemie tłumaczeń ustnych

Oto tabela opisująca, co różne parametry programu Windows PowerShell w mapie informacji o systemie. 

| Parametr programu PowerShell    | Opis  |
|-------------------------|------------------|
| Identyfikator wystąpienia             | Każdy kontroler ma unikatowy identyfikator lub identyfikator GUID skojarzony z nim.|
| Nazwa                    | Przyjazna nazwa urządzenia skonfigurowana za pośrednictwem witryny Azure portal podczas wdrażania urządzenia. Domyślną przyjazną nazwą jest numer seryjny urządzenia. |
| Model                   | Model urządzenia z serii StorSimple 8000. Model może być 8100 lub 8600.|
| SerialNumber            | Numer seryjny urządzenia jest przypisany fabrycznie i ma 15 znaków. Na przykład 8600-SHX0991003G44HT wskazuje:<br> 8600 – Czy model urządzenia.<br>SHX – Jest zakładem produkcyjnym.<br> 0991003 - Jest to produkt szczególny. <br> G44HT- ostatnie 5 cyfr są zwiększane, aby utworzyć unikalne numery seryjne. Może to nie być zestaw sekwencyjny.|
| TimeZone                | Strefa czasowa urządzenia skonfigurowana w witrynie Azure portal podczas wdrażania urządzenia.|
| PrądKontroler       | Kontroler, z którego masz połączenie za pośrednictwem interfejsu programu Windows PowerShell urządzenia StorSimple.|
| Aktywny kontroler        | Kontroler, który jest aktywny na urządzeniu i kontroluje wszystkie operacje sieciowe i dyskowe. Może to być kontroler 0 lub kontroler 1.  |
| Controller0Status (Sterownik0Status)       | Stan kontrolera 0 na urządzeniu. Stan kontrolera może być normalny, w trybie odzyskiwania lub nieosiągalny.|
| Controller1Status (Status)       | Stan kontrolera 1 w urządzeniu.  Stan kontrolera może być normalny, w trybie odzyskiwania lub nieosiągalny.|
| Tryb systemowy              | Ogólny stan urządzenia StorSimple. Stan urządzenia może być normalny, konserwacji lub likwidowane (odpowiada dezaktywowane w witrynie Azure portal).|
| Wersja friendlysoftware | Przyjazny ciąg odpowiadający wersji oprogramowania urządzenia. W przypadku systemu z aktualizacją 4 przyjazną wersją oprogramowania będzie StorSimple 8000 Series Update 4.0.|
| HcsSoftwareVersion      | Wersja oprogramowania HCS działająca na urządzeniu. Na przykład wersja oprogramowania HCS odpowiadająca aktualizacji 4.0 z serii StorSimple 8000 to 6.3.9600.17820. |
| ApiVersion (Wersja apiwersa              | Wersja oprogramowania interfejsu API programu Windows PowerShell urządzenia HCS.|
| VhdVersion ( VhdVersion )              | Wersja oprogramowania obrazu fabrycznego, z którą urządzenie zostało dostarczone. Jeśli urządzenie zostanie zresetowane do ustawień fabrycznych, spowoduje to przekroczeniu tej wersji oprogramowania.|
| OSVersion               | Wersja oprogramowania systemu operacyjnego Windows Server działającego na urządzeniu. Urządzenie StorSimple jest oparte na systemie Windows Server 2012 R2, który odpowiada wartości 6.3.9600.|
| CisAgentVersion (Wersja cisagent)         | Wersja dla twojego agenta Cis działającego na urządzeniu StorSimple. Ten agent pomaga komunikować się z usługą StorSimple Manager działającą na platformie Azure.|
| MdsAgentVersion         | Wersja odpowiadająca agentowi Mds uruchomionemu na urządzeniu StorSimple. Ten agent przenosi dane do usługi monitorowania i diagnostyki (MDS).|
| Lsisas2Wersja          | Wersja odpowiadająca sterownikom LSI na urządzeniu StorSimple.|
| Pojemność                | Całkowita pojemność urządzenia w bajtach.|
| RemoteManagementMode    | Wskazuje, czy urządzenie może być zdalnie zarządzane za pośrednictwem interfejsu programu Windows PowerShell. |
| Tryb FipsMode                | Wskazuje, czy w urządzeniu jest włączony tryb FIPS (Federal Information Processing Standard) Stanów Zjednoczonych. Standard FIPS 140 definiuje algorytmy kryptograficzne zatwierdzone do użytku przez amerykańskie systemy komputerowe rządu federalnego w celu ochrony poufnych danych. W przypadku urządzeń z aktualizacją 4 lub nowszą tryb FIPS jest domyślnie włączony. |

## <a name="next-steps"></a>Następne kroki

* Poznaj [składnię polecenia cmdlet Invoke-HcsDiagnostics](https://technet.microsoft.com/library/mt795371.aspx).

* Dowiedz się więcej o [rozwiązywaniu problemów z wdrażaniem](storsimple-troubleshoot-deployment.md) na urządzeniu StorSimple.
