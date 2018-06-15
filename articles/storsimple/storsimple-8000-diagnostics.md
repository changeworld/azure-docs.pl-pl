---
title: Narzędzia diagnostycznego do Rozwiązywanie problemów z urządzeniem StorSimple 8000 | Dokumentacja firmy Microsoft
description: W tym artykule opisano tryby urządzenia StorSimple i wyjaśniono, jak zmienić tryb urządzenia za pomocą programu Windows PowerShell dla urządzenia StorSimple.
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
ms.openlocfilehash: 7199009553eb7aae31db3f913fe4de87e03d74ba
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2018
ms.locfileid: "27744116"
---
# <a name="use-the-storsimple-diagnostics-tool-to-troubleshoot-8000-series-device-issues"></a>Użyj narzędzia Diagnostyka StorSimple rozwiązywać problemy dotyczące urządzenia z serii 8000

## <a name="overview"></a>Przegląd

Narzędzie diagnostyczne StorSimple diagnozuje problemy związane z kondycja składnika systemu, wydajności sieci i sprzętu dla urządzenia StorSimple. Narzędzie diagnostyczne można w różnych scenariuszach. Te scenariusze obejmują obciążenia planowania, wdrażania urządzenia StorSimple oceny środowiska sieciowego i określania wydajności działającego urządzenia. Ten artykuł zawiera omówienie narzędzia diagnostycznego i opisano, jak można użyć narzędzia z urządzeniem StorSimple.

Narzędzie diagnostyczne jest przeznaczone głównie dla urządzeń lokalnych, serii StorSimple 8000 (8100 i 8600).

## <a name="run-diagnostics-tool"></a>Uruchom narzędzie diagnostyczne

To narzędzie można uruchomić za pomocą interfejsu programu Windows PowerShell urządzenia StorSimple. Istnieją dwa sposoby dostępu lokalnego interfejsu urządzenia:

* [Łączenie z konsolą szeregową urządzenia przy użyciu programu PuTTY](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
* [Zdalny dostęp do narzędzia programu Windows PowerShell dla urządzenia StorSimple](storsimple-8000-remote-connect.md).

W tym artykule przyjęto założenie, że nawiązano połączenie z konsolą szeregową urządzenia przy użyciu programu PuTTY.

#### <a name="to-run-the-diagnostics-tool"></a>Aby uruchomić narzędzie diagnostyczne

Po podłączeniu do interfejsu programu Windows PowerShell, urządzenia, wykonaj następujące kroki, aby uruchomić polecenie cmdlet.
1. Zaloguj się do konsoli szeregowej urządzenia, wykonując kroki opisane w [przy użyciu programu PuTTY można nawiązać połączenia z konsolą szeregową urządzenia](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).

2. Wpisz następujące polecenie:

    `Invoke-HcsDiagnostics`

    Jeśli parametr zakresu nie jest określony, polecenie cmdlet wykonuje testów diagnostycznych. Te testy obejmują systemu, kondycja składnika sprzętu, sieci i wydajności. 
    
    Aby uruchomić specyficznego testu, należy określić parametr zakresu. Na przykład aby uruchomić tylko testy sieci, wpisz

    `Invoke-HcsDiagnostics -Scope Network`

3. Zaznacz i skopiuj dane wyjściowe z okna programu PuTTY do pliku tekstowego w celu dalszej analizy.

## <a name="scenarios-to-use-the-diagnostics-tool"></a>Scenariusze użycia narzędzia diagnostyczne

Użyj narzędzia Diagnostyka rozwiązywać kondycji sieci, wydajności systemu i sprzętu, systemu. Poniżej przedstawiono kilka możliwych scenariuszy:

* **Urządzenie w trybie offline** -Your StorSimple 8000 serii urządzenie jest w trybie offline. W interfejsie programu Windows PowerShell wydaje czy obu kontrolerów są uruchomione i działają prawidłowo.
    * Za pomocą tego narzędzia, aby określić, stan sieci.
         
         > [!NOTE]
         > Nie należy używać tego narzędzia do oceny wydajności oraz ustawienia sieci na urządzenie przed rejestracji (lub konfigurowanie za pomocą Kreatora instalacji). Prawidłowymi adresami IP są przypisywane do urządzenia podczas Kreatora konfiguracji i rejestracji. To polecenie cmdlet można uruchomić na urządzeniu, które nie jest zarejestrowane dla kondycji sprzętu i systemu. Parametr zakresu, na przykład:
         >
         > `Invoke-HcsDiagnostics -Scope Hardware`
         >
         > `Invoke-HcsDiagnostics -Scope System`

* **Problemy dotyczące urządzenia trwałe** — występują problemy dotyczące urządzenia, które wydają się do innej witryny. Na przykład kończy się niepowodzeniem rejestracji. Użytkownik może również występować problemy dotyczące urządzenia gdy urządzenie jest pomyślnie zarejestrowane i działa na chwilę.
    * W takim przypadku to narzędzie do wstępnego rozwiązywania problemów przed zalogowaniem się żądania obsługi z Support firmy Microsoft. Zalecamy uruchomienie tego narzędzia, a następnie przechwyć dane wyjściowe tego narzędzia. Te dane wyjściowe mogą udzielić im pomocy technicznej, aby przyspieszyć rozwiązywanie problemów.
    * W przypadku dowolnego składnika lub klastra awariami sprzętu należy rejestrować w żądaniu pomocy technicznej.

* **Niska wydajność urządzenia** -Your StorSimple urządzenia jest powolne.
    * W takim przypadku Uruchom to polecenie cmdlet z parametrem zakresu wartość wydajności. Przeanalizuj dane wyjściowe. Określ chmurę opóźnienia odczytu i zapisu. Użyj opóźnień jako maksymalną docelowy osiągalne, współczynnik pewne nadmiarowe obciążenie wewnętrzne przetwarzanie danych, a następnie wdrożyć obciążeń w systemie. Aby uzyskać więcej informacji, przejdź do [test sieci umożliwia rozwiązywanie problemów z wydajnością urządzenia](#network-test).


## <a name="diagnostics-test-and-sample-outputs"></a>Wyniki testów i przykładowych diagnostyki

### <a name="hardware-test"></a>Test sprzętu

Ten test określa stan składników sprzętu, oprogramowania układowego należy i oprogramowanie układowe dysku w systemie.

* Składniki sprzętowe zgłaszane są tych składników, które nie powiodło się uruchomienie testu lub nie są dostępne w systemie.
* Wersje oprogramowania układowego dysk i oprogramowania układowego należy są zgłaszane dla kontrolera 0 i kontrolera 1 i udostępnionych składników w systemie. Aby uzyskać pełną listę składników sprzętowych przejdź do:

    * [Składniki w obudowie podstawowego](storsimple-8000-monitor-hardware-status.md#component-list-for-primary-enclosure-of-storsimple-device)
    * [Składniki w obudowie EBOD](storsimple-8000-monitor-hardware-status.md#component-list-for-ebod-enclosure-of-storsimple-device)

> [!NOTE]
> Jeśli test sprzętu zgłasza błędne składniki [Zaloguj żądania obsługi ze Microsoft Support](storsimple-8000-contact-microsoft-support.md).

#### <a name="sample-output-of-hardware-test-run-on-an-8100-device"></a>Przykładowe dane wyjściowe testu sprzętu uruchamianie na urządzeniu 8100

Oto przykładowe dane wyjściowe z urządzenia StorSimple 8100. W urządzeniu 8100 modelu obudowa EBOD jest nieobecny. W związku z tym EBOD kontrolera składniki nie są zgłaszane.

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

Ten test raportuje informacje o systemie, dostępne aktualizacje, informacje o klastrze i informacje o usługi dla danego urządzenia.

* Informacje o systemie zawiera model, numer seryjny urządzenia strefy czasowej, stan kontrolera i wersji oprogramowania szczegółowe uruchomiona w systemie. Aby poznać różne parametry systemu zgłoszone jako dane wyjściowe, przejdź do [interpretacji informacji o systemie](#appendix-interpreting-system-information).

* Czy tryby zwykłych oraz obsługi są dostępne raporty dostępności aktualizacji i ich nazw skojarzonego pakietu. Jeśli `RegularUpdates` i `MaintenanceModeUpdates` są `false`, oznacza to, że aktualizacje nie są dostępne. Urządzenie jest aktualny.
* Informacje o klastrze zawiera informacje dotyczące różnych składników logicznych wszystkie grupy klastra moduł HCS oraz ich odpowiednich stanów. Jeśli widzisz grupy klastra w trybie offline, w tej sekcji raportu, [skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md).
* Informacje o usłudze zawiera nazwy i Stanami wszystkich usług moduł HCS i konfiguracji (ci) uruchomione na urządzeniu. Informacje te są przydatne do firmy Microsoft Support przy rozwiązywaniu problemu urządzenia.

#### <a name="sample-output-of-system-test-run-on-an-8100-device"></a>Przykładowe dane wyjściowe testu systemu uruchamianie na urządzeniu 8100

Oto przykładowe dane wyjściowe uruchomienia na urządzeniu 8100 testu systemu.

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
hcs_management_servic         Online HCS Cluster Group
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

Ten test sprawdza stan interfejsów sieciowych, porty, DNS i protokołu NTP łączności z serwerem, SSL certyfikatu, poświadczeń konta magazynu, łączność z serwerami aktualizacji i łączności serwera proxy sieci web na urządzeniu StorSimple.

#### <a name="sample-output-of-network-test-when-only-data0-is-enabled"></a>Przykładowe dane wyjściowe sieci testów, gdy jest włączona tylko DATA0

Oto przykładowe dane wyjściowe urządzenia 8100. Można zobaczyć w danych wyjściowych który:
* Tylko dane 0 i 1 danych interfejsy sieciowe są włączone i skonfigurowane.
* DANE 2 5 nie są włączone w portalu.
* Konfiguracja serwera DNS jest prawidłowa i urządzenia można połączyć za pośrednictwem serwera DNS.
* Połączenie serwera NTP również funkcjonuje prawidłowo.
* Otwartych portów 80 i 443. Jednak port 9354 jest zablokowany. Na podstawie [wymagania sieciowe systemu](storsimple-system-requirements.md), należy otworzyć tego portu do komunikacji magistrali usługi.
* Certyfikat SSL jest nieprawidłowy.
* Urządzenie można łączyć z kontem magazynu: _myss8000storageacct_.
* Łączność z serwerami aktualizacji jest prawidłowa.
* Nie skonfigurowano serwera proxy sieci web na tym urządzeniu.

#### <a name="sample-output-of-network-test-when-data0-and-data1-are-enabled"></a>Przykładowe dane wyjściowe testu sieci, gdy są włączone DATA0 i dane1

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

Ten test raportów wydajności chmury za pośrednictwem chmury opóźnienia odczytu i zapisu dla danego urządzenia. To narzędzie można ustalić linię bazową wydajności chmury, który można uzyskać z StorSimple. Narzędzie Raporty maksymalną wydajność (najlepsze scenariusz dla opóźnienia odczytu i zapisu) można uzyskać połączenia.

Jak narzędzie zgłasza maksymalną wydajność osiągalne, możemy użyć opóźnień odczytu i zapisu jako miejsca docelowe w przypadku wdrażania obciążeń.

Test symuluje rozmiarów obiektu blob skojarzony z typami inny wolumin na urządzeniu. Do warstwy standardowych i kopii zapasowych woluminów przypiętych lokalnie użyj rozmiar obiektu blob 64 KB. Woluminy warstwowe z zaznaczoną opcją archiwum Użyj rozmiar danych obiektu blob 512 KB. Jeśli urządzenie ma woluminów warstwowych i przypiętych lokalnie skonfigurowane, tylko testu odpowiadający blob 64 KB, rozmiar danych jest uruchomiony.

Aby użyć tego narzędzia, wykonaj następujące czynności:

1.  Najpierw utwórz mieszane woluminy warstwowe i woluminy warstwowe z zaznaczoną opcją zarchiwizowane. Ta akcja zapewnia, że narzędzie działa testy 64 KB i 512 KB rozmiarów obiektu blob.

2. Po utworzeniu i skonfigurowanych woluminach, uruchom polecenie cmdlet. Wpisz:

    `Invoke-HcsDiagnostics -Scope Performance`

3. Zanotuj opóźnienia zapisu i odczytu zgłoszone przez narzędzie. Ten test może potrwać kilka minut do uruchomienia przed rozpoczęciem zgłasza wyniki.

4. W przypadku opóźnienia połączenia pod oczekiwanego zakresu, następnie opóźnienia zgłoszone przez narzędzie może służyć jako maksymalną osiągalne docelowego podczas wdrażania obciążeń. Współczynnik niektórych obciążenie przetwarzania danych wewnętrznych.

    W przypadku wysokiej opóźnienia zapisu i odczytu zgłoszone przez narzędzie diagnostyczne:

    1. Skonfiguruj analityka magazynu dla usług obiektów blob i analizowanie danych wyjściowych, aby zrozumieć opóźnienia dla konta magazynu platformy Azure. Aby uzyskać szczegółowe instrukcje, przejdź do [włączyć i skonfigurować analityka magazynu](../storage/common/storage-enable-and-view-metrics.md). Jeśli te czasy oczekiwania są także wysokiej i porównywalnych numery otrzymany od narzędzia diagnostycznego StorSimple, następnie należy zalogować żądanie usługi z usługą Azure storage.

    2. Jeśli brakuje opóźnienia konta magazynu, skontaktuj się z administratorem sieci w celu zbadania problemów opóźnienie w sieci.

#### <a name="sample-output-of-performance-test-run-on-an-8100-device"></a>Przykładowe dane wyjściowe uruchamiać na urządzeniu 8100 testu wydajności

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

## <a name="appendix-interpreting-system-information"></a>Dodatku: informacje o systemie interpretowanie

Oto opisujące jaki różne parametry programu Windows PowerShell w mapie informacji systemu do tabeli. 

| Parametr programu PowerShell    | Opis  |
|-------------------------|------------------|
| Identyfikator wystąpienia             | Każdy kontroler ma unikatowego identyfikatora lub identyfikator GUID skojarzony z nim.|
| Name (Nazwa)                    | Przyjazna nazwa urządzenia, zgodnie z konfiguracją za pośrednictwem portalu Azure podczas wdrażania urządzenia. Przyjazna nazwa domyślna jest numer seryjny urządzenia. |
| Model                   | Model urządzenia serii StorSimple 8000. Model może być 8100 lub 8600.|
| numer seryjny            | Numer seryjny urządzenia jest przypisana w fabryce i 15 znaków. Wskazuje, na przykład 8600 SHX0991003G44HT:<br> 8600 — jest to model urządzenia.<br>SHX — jest miejsce produkcji.<br> 0991003 — jest określony produkt. <br> G44HT-5 ostatnich cyfr numeru są zwiększane, aby utworzyć unikatowe numery seryjne. Nie można sekwencyjnych zestawu.|
| Strefa czasowa                | Strefa czasowa urządzenia, zgodnie z konfiguracją w portalu Azure podczas wdrażania urządzenia.|
| CurrentController       | Kontroler, że masz połączenie za pośrednictwem interfejsu programu Windows PowerShell urządzenia StorSimple.|
| ActiveController        | Kontroler, który jest aktywna na urządzeniu i kontroluje wszystkie operacje sieci i dysku. Może to być kontrolera 0 i kontrolera 1.  |
| Controller0Status       | Stan kontrolera 0 na urządzeniu. Stan kontrolera może być normalna w trybie odzyskiwania lub jest niedostępny.|
| Controller1Status       | Stan kontrolera 1 na urządzeniu.  Stan kontrolera może być normalna w trybie odzyskiwania lub jest niedostępny.|
| SystemMode              | Ogólny stan urządzenia StorSimple. Stan urządzenia może zostać przeprowadzony normalnie, konserwacji lub wycofany z eksploatacji (odpowiada dezaktywowane w portalu Azure).|
| FriendlySoftwareVersion | Przyjazne ciąg, który odpowiada wersji oprogramowania na urządzeniu. Komputerze z systemem aktualizacji 4 wersja oprogramowania przyjazną będzie StorSimple 8000 serii aktualizacji 4.0.|
| HcsSoftwareVersion      | Wersja oprogramowania moduł HCS uruchomione na urządzeniu. Na przykład moduł HCS wersji oprogramowania odpowiadający StorSimple 8000 serii aktualizacji 4.0 jest 6.3.9600.17820. |
| ApiVersion              | Wersja oprogramowania Windows API środowiska PowerShell moduł HCS urządzenia.|
| VhdVersion              | Wersja oprogramowania dostarczonej urządzenia z obrazu. W przypadku zresetowania urządzenia do ustawień fabrycznych uruchomieniu tej wersji oprogramowania.|
| OSVersion               | Wersja oprogramowania systemu operacyjnego Windows Server uruchomionej na urządzeniu. Wyłączanie systemu Windows Server 2012 R2, który odpowiada 6.3.9600 opiera się urządzenia StorSimple.|
| CisAgentVersion         | Wersja agenta konfiguracji (ci) uruchomione na urządzeniu StorSimple. Ten agent pomaga komunikować się z usługą Menedżer StorSimple, działające na platformie Azure.|
| MdsAgentVersion         | Wersja odpowiadający Mds agenta uruchomionego na urządzeniu StorSimple. Ten agent przeniesienie danych do monitorowania i diagnostyki usługi (MDS).|
| Lsisas2Version          | Wersja odpowiadający sterowniki LSI na urządzeniu StorSimple.|
| Pojemność                | Całkowita pojemność urządzenia w bajtach.|
| RemoteManagementMode    | Wskazuje, czy urządzenia mogą być zarządzane zdalnie za pośrednictwem jej interfejsu programu Windows PowerShell. |
| FipsMode                | Wskazuje, czy tryb Stanów Zjednoczonych informacji przetwarzania Standard FIPS (Federal) jest włączony na twoim urządzeniu. Standard FIPS 140 definiuje zatwierdzone do użycia przez nas federalne dla instytucji rządowych systemów komputerowych do ochrony danych poufnych algorytmów kryptograficznych. W przypadku urządzeń programu Update 4 lub nowszego trybie FIPS jest domyślnie włączona. |

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się [składni polecenia cmdlet Invoke-HcsDiagnostics](https://technet.microsoft.com/library/mt795371.aspx).

* Dowiedz się więcej o sposobie [Rozwiązywanie problemów dotyczących wdrożenia](storsimple-troubleshoot-deployment.md) na urządzeniu StorSimple.
