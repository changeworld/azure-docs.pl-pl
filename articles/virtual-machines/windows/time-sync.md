---
title: Czas synchronizacji dla maszyn wirtualnych Windows na platformie Azure | Dokumentacja firmy Microsoft
description: Synchronizacja czasu dla maszyn wirtualnych Windows.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: 1a2e75dcffe32c6f1aeaba8646b96bbc1500ffdf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61438214"
---
# <a name="time-sync-for-windows-vms-in-azure"></a>Synchronizacja czasu dla maszyn wirtualnych Windows na platformie Azure

Synchronizacja czasu jest ważna dla bezpieczeństwa oraz korelacji zdarzeń. Czasami służy do wykonania transakcji rozproszonych. Czas dokładności między wieloma systemami komputerowymi odbywa się za pośrednictwem synchronizacji. Synchronizacja mogą mieć wpływ wiele rzeczy, w tym ponowne uruchamianie i ruch sieciowy między źródła czasu i komputera, czas pobierania. 

Azure jest teraz objęta infrastruktury z systemem Windows Server 2016. System Windows Server 2016 zawiera ulepszone algorytmy, używany do poprawnego czasu i warunek zegara lokalnego do synchronizacji z czasem UTC.  System Windows Server 2016 również ulepszone VMICTimeSync usługi, które regulują, jak synchronizować maszyn wirtualnych z hostem dla dokładnego czasu. Ulepszenia obejmują dokładniejsze początkowego czasu na początek maszyny Wirtualnej lub przywracanie maszyny Wirtualnej i korektę opóźnienie przerwania przykłady przekazane do czasu Windows (W32time). 


>[!NOTE]
>Aby uzyskać szybki przegląd usługi Czas Windows, zapoznaj się z tym [ogólne omówienie wideo](https://aka.ms/WS2016TimeVideo).
>
> Aby uzyskać więcej informacji, zobacz [dokładnego czasu systemu Windows Server 2016](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time). 

## <a name="overview"></a>Omówienie

Dokładność zegara komputera jest pomiarowym na blisko zegara komputera jest standardowy czas uniwersalny czas koordynowany (UTC). UTC jest definiowany przez wielonarodowych próbka dokładne zegary niepodzielność, które może być tylko wyłączyć jedną sekundę lat 300. Jednak bezpośrednio do czytania UTC wymaga specjalistycznego sprzętu. Zamiast tego należy serwery czasu UTC są synchronizowane i są dostępne z innych komputerów w celu zapewnienia skalowalności i niezawodności. Każdy komputer ma czas Usługa synchronizacji jest uruchomiona, wie, jakie serwery czasu, należy użyć i okresowo sprawdza, czy zegar komputera ma zostać poprawione i dostosowuje czas, w razie potrzeby. 

Hosty platformy Azure są synchronizowane wewnętrznych serwerów czasu firmy Microsoft, które przyjmują czasu z urządzeń należących do firmy Microsoft warstwy 1, przy użyciu anten GPS. Maszyny wirtualne na platformie Azure albo mogą być zależne od ich host do przekazania dokładnego czasu (*hosta czasu*) do maszyny Wirtualnej lub maszyny Wirtualnej może bezpośrednio pobierać czas z czasem serwera lub jako kombinację obu tych. 

Maszyna wirtualna interakcji z hostem może również wpływać na zegara. Podczas [pamięci zachowywanie konserwacji](maintenance-and-updates.md#maintenance-not-requiring-a-reboot), maszyny wirtualne są wstrzymane przez maksymalnie 30 sekund. Na przykład przed rozpoczęciem konserwacji zegar maszyny Wirtualnej zawiera 10:00:00 AM i obowiązuje 28 sekundach. Po wznowieniu działania maszyny Wirtualnej, zegar na maszynie Wirtualnej nadal będą wyświetlane 10:00:00 AM, który będzie 28 sekundach wyłączone. Prawidłowe w tym celu usługa VMICTimeSync monitoruje co dzieje się na hoście i wyświetla monit o zmiany do wykonania na maszynach wirtualnych wyrównania.

Usługa VMICTimeSync działa w trybie próbki lub synchronizacji i wpływają tylko do przodu zegara. W trybie próbki, wymagająca W32time, należy uruchomić usługę VMICTimeSync sonduje hosta co 5 sekund i zawiera przykłady czasu W32time. Co około 30 sekund, usługę W32time przyjmuje najnowszej próbki czasu i używa go do wywierania wpływu na zegarze gościa. Trybu synchronizacji aktywuje, jeśli zostało wznowione gościa lub zegara gościa drifts więcej niż 5 sekund za zegara hosta. W przypadkach, w którym jest prawidłowo uruchomiona usługa W32time ostatnim przypadku powinno nigdy się wydarzyć.

Bez pracy synchronizacji czasu, zegar na maszynie Wirtualnej może wzrosnąć błędy. Gdy istnieje tylko jedna maszyna wirtualna, efekt mogą nie być istotne, chyba że obciążenie wymaga bardzo dokładnych. Jednak w większości przypadków, firma Microsoft ma wielu, wzajemnie połączonych maszyn wirtualnych, które umożliwiają śledzenie transakcji i spójne całe wdrożenie wymaga czasu czasu. Gdy czas między maszynami wirtualnymi jest inny, można uzyskać następujące skutki:

- Uwierzytelnianie nie powiedzie się. Protokoły zabezpieczeń, takie jak protokół Kerberos lub certyfikatów zależnych od technologii zależą od czasu są spójne we wszystkich systemach. 
- Jest to bardzo trudne ustalić, co wydarzyło, w systemie dzienników (lub innymi danymi) nie zgadzam się na czas. Te same zdarzenia będzie wyglądał jak pojawił się w różnym czasie, wprowadzania korelacji trudne.
- Jeśli zegar jest wyłączona, rozliczenia może niepoprawnie obliczone.

Najlepsze wyniki, w przypadku wdrożeń Windows są osiągane przy użyciu systemu Windows Server 2016 jako system operacyjny gościa, który gwarantuje, że używasz najnowszych usprawnień w synchronizacji czasu.

## <a name="configuration-options"></a>Opcje konfiguracji

Istnieją trzy opcje dotyczące konfigurowania synchronizacji dla maszyn wirtualnych Windows hostowanych na platformie Azure:

- Host czas i time.windows.com. Jest to domyślna konfiguracja używana w portalu Azure Marketplace, obrazy.
- Tylko do hosta.
- Użyj serwer czasu, zewnętrzne z lub bez niego przy użyciu czasu hosta.


### <a name="use-the-default"></a>Użyj wartości domyślnej

Domyślnie obrazy maszyn wirtualnych systemu operacyjnego Windows są skonfigurowane dla w32time do synchronizacji z dwóch źródeł: 

- Dostawca NtpClient pobiera informacje z time.windows.com.
- Usługa VMICTimeSync używany do komunikacji czasu hosta do maszyn wirtualnych i wprowadzić korekty po maszyny Wirtualnej jest wstrzymane w celu przeprowadzenia konserwacji. Hosty platformy Azure Użyj urządzeniami należącymi do firmy Microsoft warstwy 1, aby zachować dokładnego czasu.

Usługa W32Time wolisz Dostawca czasu w następującej kolejności priorytetu: poziom warstwy, opóźnienie głównego, rozproszenia głównego, przesunięcie czasu. W większości przypadków w32time wolisz time.windows.com do hosta, ponieważ time.windows.com raportuje dolnej warstwie. 

W przypadku komputerów przyłączonych do domeny samej domeny ustanawia hierarchii synchronizacji czasu, ale w katalogu głównym lasu nadal powinien trwać z lokalizacji i następujące zagadnienia dotyczące nadal będzie przechowywało wartość true.


### <a name="host-only"></a>Tylko do hosta 

Ponieważ time.windows.com publiczny serwer NTP, zsynchronizować czasu z nim wymaga wysyłania ruchu za pośrednictwem Internetu, różnymi opóźnienia pakietów może negatywnie wpłynąć na jakości synchronizację czasu. Usuwanie time.windows.com, przełączając się tylko do hosta synchronizacji skrócić czas synchronizacji wyników.

Przełączanie na tylko do hosta czas synchronizacji sprawia, że przez sens, jeśli wystąpią synchronizacja czasu za pomocą konfiguracji domyślnej. Wypróbuj usługę synchronizacji tylko do hosta aby zobaczyć, czy to mogłyby poprawić synchronizację czasu na maszynie Wirtualnej. 

### <a name="external-time-server"></a>Czas zewnętrzny serwer

Jeśli masz wymagania dotyczące synchronizacji w określonym czasie, istnieje również możliwość korzystania z serwerów czas zewnętrzny. Serwery zewnętrzne czasu może zapewnić określony czas, który może być przydatne w przypadku scenariuszy testowych, zapewniając zgodność czasie z maszynami, hostowana w centrach danych firmy Microsoft lub obsługi przestępnym sekund w specjalny sposób.

Zewnętrzne serwery można połączyć z usługą VMICTimeSync i VMICTimeProvider zapewnienie wyniki podobne do konfiguracji domyślnej. 

## <a name="check-your-configuration"></a>Sprawdź konfigurację


Sprawdź, czy dostawca czasu NtpClient jest skonfigurowany do używania jawnych serwerów NTP (NTP) lub synchronizacji czasu domeny (NT5DS).

```
w32tm /dumpreg /subkey:Parameters | findstr /i "type"
```

Jeśli maszyna wirtualna używa NTP, zobaczysz następujące dane wyjściowe:

```
Value Name                 Value Type          Value Data
Type                       REG_SZ              NTP
```

Aby zobaczyć, jakie serwer czasu Dostawca czasu NtpClient używa, wpisz w wierszu polecenia z podwyższonym poziomem uprawnień:

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

Jeśli maszyna wirtualna używa domyślnie, dane wyjściowe będą wyglądać następująco:

```
NtpServer                  REG_SZ              time.windows.com,0x8
```


Aby zobaczyć, jakie Dostawca czasu jest obecnie używany.

```
w32tm /query /source
```


Oto dane wyjściowe, które można uzyskać i co oznaczałoby to:
    
- **Time.Windows.com** — w konfiguracji domyślnej w32time może uzyskać czasu z time.windows.com. Jakość synchronizacja czasu zależy od łącznością internetową, aby go i jest zależna od opóźnień pakietów. Jest to zwykłe dane wyjściowe z konfiguracji domyślnej.
- **Dostawca synchronizacji czasu IC maszyny Wirtualnej** — maszyna wirtualna jest synchronizacja czasu z hosta. Jeśli użytkownik zgody na uczestnictwo w celu synchronizacji czasu tylko do hosta lub NtpServer nie jest dostępny w tej chwili zwykle jest to wynik. 
- *Serwer domeny* — bieżący komputer znajduje się w domenie i domenie definiuje hierarchię synchronizacji czasu.
- *Innym serwerze* — usługa w32time nie skonfigurowano jawnie uzyskać czasu z tego innego serwera. Jakość synchronizacja czasu zależy od jakości serwera czasu.
- **Zegar CMOS lokalnego** — zegar jest zsynchronizowany. Możesz uzyskać te dane wyjściowe, jeśli usługa w32time nie ma wystarczająco dużo czasu, aby uruchomić po ponownym uruchomieniu lub źródeł skonfigurowanym czasie nie są dostępne.


## <a name="opt-in-for-host-only-time-sync"></a>Uczestnictwa w synchronizacji czasu tylko do hosta

Platforma Azure jest stale pracuje nad poprawianiem synchronizację czasu na hostach i może zagwarantować, że cała infrastruktura synchronizacja czasu jest zlokalizowana w centrach danych na należącymi do firmy Microsoft. Jeśli masz problemy z konfiguracją domyślną, preferowany do używania time.windows.com jako źródło czasu podstawowego synchronizacji czasu, umożliwia następujące polecenia wyrazić zgodę na synchronizację czasu tylko do hosta.

Oznacz dostawcy VMIC, jako włączone. 

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\VMICTimeProvider /v Enabled /t REG_DWORD /d 1 /f
```

Dostawca NTPClient zostać oznaczone jako wyłączone.

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\NtpClient /v Enabled /t REG_DWORD /d 0 /f
```

Uruchom ponownie usługę w32time.

```
net stop w32time && net start w32time
```


## <a name="windows-server-2012-and-r2-vms"></a>Windows Server 2012 i R2 maszyny wirtualne 

Windows Server 2012 i Windows Server 2012 R2 mają różne domyślne ustawienia synchronizacji czasu. W32time domyślnie jest skonfigurowana w taki sposób, że preferuje niskie obciążenie usługi dokładny czas. 

Jeśli chcesz przenieść, usługi systemu Windows Server 2012 i 2012 R2 wdrożenia, użyj nowszej ustawienia domyślne, które wolą dokładny czas, można zastosować następujące ustawienia.

Sondowania w32time przedaktualizacyjnych i odstępach czasu, aby dopasować ustawienia systemu Windows Server 2016.

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MinPollInterval /t REG_DWORD /d 6 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MaxPollInterval /t REG_DWORD /d 10 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v UpdateInterval /t REG_DWORD /d 100 /f
w32tm /config /update
```

Dla w32time można było używać nowych interwałów sondowania NtpServers można oznaczyć jako korzystanie z nich. Jeśli serwery są oznaczony za pomocą maski flag bitowych 0x1, który przesłonić ten mechanizm i w32time czy zamiast tego użyj SpecialPollInterval. Upewnij się, że określonych serwerów NTP są za pomocą flagi 0x8 lub nie flagi na wszystkich:

Sprawdź, jakie flagi są używane dla używanych serwerów NTP.

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

## <a name="next-steps"></a>Kolejne kroki

Poniżej podano linki do szczegółowe informacje o synchronizacji czasu:

- [Narzędzia usługi Czas Windows i ustawienia](https://docs.microsoft.com/windows-server/networking/windows-time-service/Windows-Time-Service-Tools-and-Settings)
- [Windows Server 2016 ulepszenia ](https://docs.microsoft.com/windows-server/networking/windows-time-service/windows-server-2016-improvements)
- [Dokładnego czasu dla systemu Windows Server 2016](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)
- [Granic Obsługa konfigurowania usługi Czas Windows dla środowisk wysokiej dokładności](https://docs.microsoft.com/windows-server/networking/windows-time-service/support-boundary)


