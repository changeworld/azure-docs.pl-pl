---
title: Narzędzia do diagnostyki do Rozwiązywanie problemów z urządzeniem StorSimple 8000 | Dokumentacja firmy Microsoft
description: W tym artykule opisano tryby urządzenia StorSimple i wyjaśniono, jak zmienianie trybu urządzenia za pomocą programu Windows PowerShell dla usługi StorSimple.
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
ms.openlocfilehash: 5cce4337e3ef95c6407d46d9b8b6401fe4f6600b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60576190"
---
# <a name="use-the-storsimple-diagnostics-tool-to-troubleshoot-8000-series-device-issues"></a>Używanie narzędzia do diagnostyki usługi StorSimple, aby rozwiązać problemy dotyczące urządzeń z serii 8000

## <a name="overview"></a>Omówienie

Narzędzia do diagnostyki usługi StorSimple diagnozuje problemy związane z systemu, wydajności, sieci i sprzętu kondycja składników dla urządzenia StorSimple. Narzędzia do diagnostyki może służyć w różnych scenariuszach. Te scenariusze obejmują obciążenia planowania, wdrażania urządzenia StorSimple, oceny środowiska sieciowego i określanie wydajność działającego urządzenia. Ten artykuł zawiera omówienie narzędzia do diagnostyki i w tym artykule opisano, jak używać narzędzia z urządzeniem StorSimple.

Narzędzia do diagnostyki jest przeznaczone głównie dla urządzeń StorSimple serii 8000 w środowisku lokalnym (8100 i 8600).

## <a name="run-diagnostics-tool"></a>Uruchamianie narzędzia do diagnostyki

To narzędzie można uruchomić za pomocą interfejsu programu Windows PowerShell urządzenia StorSimple. Istnieją dwa sposoby dostępu lokalnego interfejsu urządzenia:

* [Łączenie z konsolą szeregową urządzenia przy użyciu programu PuTTY](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
* [Zdalny dostęp do narzędzia, za pomocą programu Windows PowerShell dla usługi StorSimple](storsimple-8000-remote-connect.md).

W tym artykule przyjęto założenie, że łączysz się z konsolą szeregową urządzenia przy użyciu programu PuTTY.

#### <a name="to-run-the-diagnostics-tool"></a>Aby uruchomić narzędzia do diagnostyki

Po połączeniu się z interfejsu programu Windows PowerShell urządzenia, wykonaj następujące kroki, aby uruchomić polecenie cmdlet.
1. Zaloguj się do konsoli szeregowej urządzenia, wykonując kroki opisane w [przy użyciu programu PuTTY, aby nawiązać połączenie z konsolą szeregową urządzenia](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).

2. Wpisz następujące polecenie:

    `Invoke-HcsDiagnostics`

    Jeśli parametr zakresu nie jest określony, polecenie cmdlet wykonuje testy diagnostyczne. Te testy obejmują systemu, kondycja składników sprzętowych, sieci i wydajności. 
    
    Aby uruchomić określonego testu, określ parametr zakresu. Na przykład aby uruchomić tylko testy sieci, wpisz

    `Invoke-HcsDiagnostics -Scope Network`

3. Zaznacz i skopiuj dane wyjściowe z okna programu PuTTY w pliku tekstowym w celu dalszej analizy.

## <a name="scenarios-to-use-the-diagnostics-tool"></a>Scenariusze korzystania z narzędzia do diagnostyki

Używanie narzędzia do diagnostyki rozwiązywać problemy z sieci, wydajność, system i sprzętu kondycji systemu. Poniżej przedstawiono kilka możliwych scenariuszy:

* **Urządzenie w trybie offline** — urządzenia Your StorSimple 8000 series jest w trybie offline. Z poziomu interfejsu programu Windows PowerShell wydaje czy oba kontrolery są gotowe do działania.
    * To narzędzie służy do następnie ustalenia stanu sieci.
         
         > [!NOTE]
         > Nie należy używać tego narzędzia do oceny wydajności oraz ustawienia sieci na urządzeniu, przed przystąpieniem do rejestracji (lub konfigurowanie za pomocą Kreatora instalacji). Prawidłowy adres IP jest przypisany do urządzenia podczas Kreator instalacji i rejestracji. To polecenie cmdlet można uruchomić na urządzeniu, która nie jest zarejestrowana dla kondycja sprzętu i systemu. Użyj parametru zakresu, na przykład:
         >
         > `Invoke-HcsDiagnostics -Scope Hardware`
         >
         > `Invoke-HcsDiagnostics -Scope System`

* **Problemy z urządzeniem trwałego** — występują problemy dotyczące urządzeń, które wydaje się, że utrwalanie. Na przykład kończy się niepowodzeniem rejestracji. Możesz może również występować problemy z urządzeniem po urządzenia został pomyślnie zarejestrowany i operacyjne od pewnego czasu.
    * W tym przypadku to narzędzie do wstępnego rozwiązywania problemów przed zalogowaniem się żądania obsługi przy użyciu Microsoft Support. Firma Microsoft zaleca, możesz uruchomić to narzędzie i Przechwyć dane wyjściowe tego narzędzia. Te dane wyjściowe mogą udzielić im pomocy technicznej, aby przyspieszyć, rozwiązywania problemów.
    * W przypadku wszelkich awarii składnika lub klastra sprzętu należy rejestrować się w żądaniu pomocy technicznej.

* **Niską wydajność urządzenia** — urządzenia z usługi StorSimple działa wolno.
    * W takim przypadku należy uruchomić to polecenie cmdlet z parametrem zakresu równa wydajności. Analizuj dane wyjściowe. Możesz uzyskać chmury opóźnienia odczytu i zapisu. Użyj opóźnień zawartych w raporcie jako maksymalną osiągalny cel, wziąć pod uwagę pewne nadmiarowe obciążenie do wewnętrznego przetwarzania danych, a następnie wdrażania obciążeń w systemie. Aby uzyskać więcej informacji, przejdź do [Użyj testu sieci, aby rozwiązywać problemy z wydajnością urządzeń](#network-test).


## <a name="diagnostics-test-and-sample-outputs"></a>Diagnostyka testów i przykładowych danych wyjściowych

### <a name="hardware-test"></a>Testu sprzętu

Ten test określa stan składników sprzętowych, USM oprogramowanie układowe i oprogramowanie układowe dysku w systemie.

* Składniki sprzętowe, zgłaszane są tych składników, które test nie powiódł się lub nie są obecne w systemie.
* Wersje oprogramowania układowego i oprogramowania układowego dysku USM są zgłaszane dla kontrolera 0 i Controller 1 i udostępnionych składników w systemie. Aby uzyskać pełną listę składników sprzętowych przejdź do:

    * [Składniki w obudowie podstawowego](storsimple-8000-monitor-hardware-status.md#component-list-for-primary-enclosure-of-storsimple-device)
    * [Składniki w obudowie EBOD](storsimple-8000-monitor-hardware-status.md#component-list-for-ebod-enclosure-of-storsimple-device)

> [!NOTE]
> Jeśli zgłasza testu sprzętu, składników, [Zaloguj żądania obsługi przy użyciu Microsoft Support](storsimple-8000-contact-microsoft-support.md).

#### <a name="sample-output-of-hardware-test-run-on-an-8100-device"></a>Przykładowe dane wyjściowe testu sprzętu do urządzenia 8100

Oto przykładowe dane wyjściowe z urządzenia StorSimple 8100. W polu urządzenia model 8100 obudowy EBOD nie występuje. W związku z tym składniki kontrolera EBOD nie są zgłaszane.

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

Ten test raportuje informacje o systemie, dostępne aktualizacje, informacje o klastrze i informacji usługi dla urządzenia.

* Informacje o systemie obejmuje model, numer seryjny urządzenia, strefę czasową, stan kontrolera i wersji oprogramowania szczegółowe uruchomionych w systemie. Aby zapoznać się z różnych parametrami systemu, które są zgłaszane jako dane wyjściowe, przejdź do [interpretowanie informacje o systemie](#appendix-interpreting-system-information).

* Dostępność aktualizacji, raporty, czy dostępne są trybami zwykłych i konserwacji i ich nazwy skojarzonych pakietów. Jeśli `RegularUpdates` i `MaintenanceModeUpdates` są `false`, oznacza to, że aktualizacje nie są dostępne. Urządzenie jest aktualne.
* Informacje o klastrze zawiera informacje na temat różnych składników logicznych wszystkie grupy klastra magazynu HCS i ich odpowiednie stany. Jeśli widzisz grupy klastra w trybie offline, w tej sekcji raportu, [skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md).
* Informacje o usłudze obejmuje nazwy i Stanami wszystkich usług magazynu HCS lub CiS działających na urządzeniu z systemem. Te informacje są przydatne w przypadku Support firmy Microsoft w rozwiązaniu problemu urządzenia.

#### <a name="sample-output-of-system-test-run-on-an-8100-device"></a>Przykładowe dane wyjściowe testu systemu do urządzenia 8100

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

Ten test sprawdza stan interfejsów sieciowych, portów, DNS i NTP łączność z serwerem, SSL certyfikatów, poświadczeń konta magazynu, łączność z serwerami aktualizacji i łączności serwera proxy sieci web na urządzeniu StorSimple.

#### <a name="sample-output-of-network-test-when-only-data0-is-enabled"></a>Przykładowe dane wyjściowe sieci testów, gdy jest włączona tylko DATA0

Oto przykładowe dane wyjściowe do urządzenia 8100. Widoczne w danych wyjściowych który:
* Tylko DATA 0 i 1 dane interfejsy sieciowe są włączone i skonfigurowane.
* DANE 2 – 5 nie są włączone w portalu.
* Konfiguracja serwera DNS jest prawidłowa, a urządzenie może połączyć się za pośrednictwem serwera DNS.
* Łączność z serwerem NTP jest również dobrym rozwiązaniem.
* Porty 80 i 443 są otwarte. Jednak port 9354 jest zablokowany. Na podstawie [wymagania sieciowe systemu](storsimple-system-requirements.md), musisz otworzyć ten port komunikacji usługi Service bus.
* Certyfikat SSL jest prawidłowy.
* Urządzenie może połączyć się z kontem usługi storage: _myss8000storageacct_.
* Łączność z serwerami aktualizacji jest prawidłowa.
* Nie skonfigurowano serwer proxy sieci web na tym urządzeniu.

#### <a name="sample-output-of-network-test-when-data0-and-data1-are-enabled"></a>Przykładowe dane wyjściowe testu sieci po włączeniu DATA0 i dane1

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

Ten test raportów wydajności chmury, za pośrednictwem opóźnienia odczytu i zapisu chmury dla Twojego urządzenia. To narzędzie można ustalić punkt odniesienia wydajności chmury, który można osiągnąć za pomocą usługi StorSimple. Zgłaszanej przez narzędzie maksymalnej wydajności (Scenariusz najlepszego przypadku dla opóźnienia odczytu i zapisu) można uzyskać w celu nawiązania połączenia.

Zgodnie z maksymalną wydajność osiągalna zgłaszanej przez narzędzie, możemy użyć zgłoszonych opóźnienia odczytu i zapisu jako elementy docelowe, w przypadku wdrażania obciążeń.

Zasymulowano rozmiarów obiektów blob skojarzony z typami inny wolumin na urządzeniu. Zwykłych warstwowe i kopie zapasowe woluminów przypiętych lokalnie, użyj obiektu blob o rozmiarze 64 KB. Woluminy warstwowe z zaznaczoną opcją archiwum Użyj rozmiar danych obiektu blob 512 KB. Jeśli urządzenie ma woluminy warstwowe i przypiętego lokalnie skonfigurowana, tylko testy, odpowiadający blob 64 KB, rozmiar danych jest uruchomiony.

Aby użyć tego narzędzia, wykonaj następujące czynności:

1.  Najpierw utwórz kombinacji woluminy warstwowe woluminy warstwowe z zaznaczoną opcją zarchiwizowane. Ta akcja zagwarantuje, że to narzędzie uruchamia testy dla 64 KB i 512 KB rozmiarów obiektów blob.

2. Uruchom polecenie cmdlet, po użytkownik utworzeniu i skonfigurowaniu woluminów. Wpisz:

    `Invoke-HcsDiagnostics -Scope Performance`

3. Zanotuj opóźnienia odczytu i zapisu, zgłoszonej przez narzędzie. Ten test może potrwać kilka minut przed zgłasza wyniki.

4. Jeśli wszystko dzieje się w oczekiwanym zakresem opóźnienia połączenia, następnie opóźnienia zgłoszonej przez narzędzie może służyć jako maksymalną osiągalny cel podczas wdrażania obciążeń. Wziąć pod uwagę pewne nadmiarowe obciążenie do wewnętrznego przetwarzania danych.

    W przypadku wysokiej wartości opóźnienia odczytu i zapisu, zgłoszonej przez narzędzie Diagnostyka:

    1. Skonfigurowanie usługi Storage Analytics do usług obiektów blob i analizowanie danych wyjściowych, aby zrozumieć opóźnień dla konta usługi Azure storage. Aby uzyskać szczegółowe instrukcje, przejdź do [włączyć i skonfigurować usługi Storage Analytics](../storage/common/storage-enable-and-view-metrics.md). Jeśli te wartości opóźnienia są również wysoką i porównywalne do numerów otrzymany od narzędzia do diagnostyki usługi StorSimple, należy się żądanie usługi z usługą Azure storage.

    2. W przypadku niskie opóźnienia konta magazynu, skontaktuj się z administratorem sieci, aby zbadać wszelkie problemy opóźnienie w sieci.

#### <a name="sample-output-of-performance-test-run-on-an-8100-device"></a>Przykładowe dane wyjściowe testu wydajności uruchamiania na urządzeniu 8100

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

## <a name="appendix-interpreting-system-information"></a>Dodatek: interpretowanie informacje o systemie

Poniżej przedstawiono tabelę opisującą różne parametry programu Windows PowerShell na mapie informacje systemowe, aby. 

| Parametr środowiska PowerShell    | Opis  |
|-------------------------|------------------|
| Identyfikator wystąpienia             | Każdy kontroler ma unikatowy identyfikator lub identyfikator GUID skojarzony z nim.|
| Name (Nazwa)                    | Przyjazna nazwa urządzenia, zgodnie z konfiguracją w witrynie Azure portal podczas wdrażania urządzenia. Przyjazna nazwa domyślna jest numer seryjny urządzenia. |
| Modelowanie                   | Model urządzenia serii StorSimple 8000. Może on być 8100 lub 8600.|
| numer seryjny            | Numer seryjny urządzenia jest przypisywany w fabryce i jest 15 znaków. Wskazuje, na przykład 8600 SHX0991003G44HT:<br> 8600 — jest to model urządzenia.<br>SHX — to witryna produkcji.<br> 0991003 - jest określony produkt. <br> G44HT-5 ostatnich cyfr są zwiększane, aby utworzyć unikatowe numery seryjne. To może nie być sekwencyjne zestawu.|
| Strefa czasowa                | Strefę czasową urządzenia, zgodnie z konfiguracją w witrynie Azure portal podczas wdrażania urządzenia.|
| CurrentController       | Kontroler, że nawiązano połączenie za pośrednictwem interfejsu programu Windows PowerShell urządzenia StorSimple.|
| ActiveController        | Kontroler, który jest aktywna na twoim urządzeniu i kontroluje wszystkie operacje dysku i sieci. Może to być kontrolera 0 i kontrolera 1.  |
| Controller0Status       | Stan kontrolera 0 na urządzeniu. Stan kontrolera może być normalne, w trybie odzyskiwania lub jest niedostępny.|
| Controller1Status       | Stan kontrolera 1 na urządzeniu.  Stan kontrolera może być normalne, w trybie odzyskiwania lub jest niedostępny.|
| SystemMode              | Ogólny stan urządzenia StorSimple. Stan urządzenia może być normalne, konserwacji lub zlikwidowane (odpowiada dezaktywowane w witrynie Azure portal).|
| FriendlySoftwareVersion | Przyjazne ciąg, który odnosi się do wersji oprogramowania urządzenia. W przypadku systemu uruchomić aktualizację Update 4 wersja oprogramowania przyjazna będzie StorSimple 8000 Series Update 4.0.|
| HcsSoftwareVersion      | Wersja oprogramowania magazynu HCS uruchomiony na twoim urządzeniu. Na przykład wersja oprogramowania magazynu HCS odpowiadający StorSimple 8000 Series Update 4.0 jest 6.3.9600.17820. |
| ApiVersion              | Wersja oprogramowania interfejsu API programu Windows PowerShell urządzenia magazynu HCS.|
| VhdVersion              | Wersja oprogramowania obrazu fabryki, dostarczanej urządzenia z programem. W przypadku zresetowania urządzenia do ustawień fabrycznych, działa ta wersja oprogramowania.|
| OSVersion               | Wersja oprogramowania systemu operacyjnego Windows Server uruchomionej na urządzeniu. Windows Server 2012 R2, który odpowiada 6.3.9600 na podstawie urządzenia StorSimple.|
| CisAgentVersion         | Wersja agenta konfiguracji (ci) uruchomione na urządzeniu StorSimple. Ten agent pomaga komunikować się z usługą StorSimple Manager, działające na platformie Azure.|
| MdsAgentVersion         | Wersja odpowiadający agenta usług Mds uruchomionego na urządzeniu StorSimple. Ten agent jest przeniesienie danych do monitorowania i diagnostyki usługi (MDS).|
| Lsisas2Version          | Wersja odpowiadający sterowniki LSI na urządzeniu StorSimple.|
| Pojemność                | Całkowita pojemność urządzenia w bajtach.|
| RemoteManagementMode    | Wskazuje, czy urządzenia mogą być zarządzane zdalnie za pośrednictwem jego interfejsu programu Windows PowerShell. |
| FipsMode                | Wskazuje, czy tryb Stanów Zjednoczonych informacji przetwarzania Standard FIPS (Federal) jest włączony na twoim urządzeniu. Standard FIPS 140 definiuje zatwierdzone do użycia przez nas federalnych instytucji rządowych systemów komputerowych ochrony poufnych danych algorytmów kryptograficznych. W przypadku urządzeń z aktualizacją Update 4 lub nowszego trybu FIPS jest domyślnie włączona. |

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, [składni polecenia cmdlet Invoke-HcsDiagnostics](https://technet.microsoft.com/library/mt795371.aspx).

* Dowiedz się więcej na temat [Rozwiązywanie problemów z wdrożeniem](storsimple-troubleshoot-deployment.md) na urządzeniu StorSimple.
