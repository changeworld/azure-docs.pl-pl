---
title: Czas synchronizacji dla maszyn wirtualnych systemu Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Synchronizacja czasu dla maszyn wirtualnych systemu Linux.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: 3271804a80ededae650c3b6ad34fdb7f9f1e5b18
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67708620"
---
# <a name="time-sync-for-linux-vms-in-azure"></a>Synchronizacja czasu dla maszyn wirtualnych systemu Linux na platformie Azure

Synchronizacja czasu jest ważna dla bezpieczeństwa oraz korelacji zdarzeń. Czasami służy do wykonania transakcji rozproszonych. Czas dokładności między wieloma systemami komputerowymi odbywa się za pośrednictwem synchronizacji. Synchronizacja mogą mieć wpływ wiele rzeczy, w tym ponowne uruchamianie i ruch sieciowy między źródła czasu i komputera, czas pobierania. 

Azure jest wspierana przez infrastruktury z systemem Windows Server 2016. System Windows Server 2016 zawiera ulepszone algorytmy, używany do poprawnego czasu i warunek zegara lokalnego do synchronizacji z czasem UTC.  Funkcja systemu Windows Server 2016 dokładnego czasu znacznie poprawia sposób VMICTimeSync usługi, która reguluje maszyn wirtualnych z hostem dla dokładnego czasu. Ulepszenia obejmują dokładniejsze początkowego czasu na początek maszyny Wirtualnej lub przywracanie maszyny Wirtualnej i korektę opóźnienie przerwania. 

>[!NOTE]
>Aby uzyskać szybki przegląd usługi Czas Windows, zapoznaj się z tym [ogólne omówienie wideo](https://aka.ms/WS2016TimeVideo).
>
> Aby uzyskać więcej informacji, zobacz [dokładnego czasu systemu Windows Server 2016](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time). 

## <a name="overview"></a>Przegląd

Dokładność zegara komputera jest pomiarowym na blisko zegara komputera jest standardowy czas uniwersalny czas koordynowany (UTC). UTC jest definiowany przez wielonarodowych próbka dokładne zegary niepodzielność, które może być tylko wyłączyć jedną sekundę lat 300. Jednak bezpośrednio do czytania UTC wymaga specjalistycznego sprzętu. Zamiast tego należy serwery czasu UTC są synchronizowane i są dostępne z innych komputerów w celu zapewnienia skalowalności i niezawodności. Każdy komputer ma czas Usługa synchronizacji jest uruchomiona, wie, jakie serwery czasu, należy użyć i okresowo sprawdza, czy zegar komputera ma zostać poprawione i dostosowuje czas, w razie potrzeby. 

Hosty platformy Azure są synchronizowane wewnętrznych serwerów czasu firmy Microsoft, które przyjmują czasu z urządzeń należących do firmy Microsoft warstwy 1, przy użyciu anten GPS. Maszyny wirtualne na platformie Azure albo mogą być zależne od ich host do przekazania dokładnego czasu (*hosta czasu*) do maszyny Wirtualnej lub maszyny Wirtualnej może bezpośrednio pobierać czas z czasem serwera lub jako kombinację obu tych. 

Na sprzęcie autonomicznego systemu operacyjnego Linux odczytuje tylko sprzętu hosta zegara podczas rozruchu. Po tym zegara jest utrzymywany w jądrze systemu Linux przy użyciu czasomierz przerwanie. W tej konfiguracji będzie odstępstw zegara, wraz z upływem czasu. W nowszych dystrybucje systemu Linux na platformie Azure maszyn wirtualnych można użyć dostawcę VMICTimeSync objęte systemu Linux integration services (LIS), zapytania dotyczące aktualizacji zegara z większą częstotliwością hosta.

Maszyna wirtualna interakcji z hostem może również wpływać na zegara. Podczas [pamięci zachowywanie konserwacji](maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot), maszyny wirtualne są wstrzymane przez maksymalnie 30 sekund. Na przykład przed rozpoczęciem konserwacji zegar maszyny Wirtualnej zawiera 10:00:00 AM i obowiązuje 28 sekundach. Po wznowieniu działania maszyny Wirtualnej, zegar na maszynie Wirtualnej nadal będą wyświetlane 10:00:00 AM, który będzie 28 sekundach wyłączone. Prawidłowe w tym celu usługa VMICTimeSync monitoruje co dzieje się na hoście i wyświetla monit o zmiany do wykonania na maszynach wirtualnych wyrównania.

Bez pracy synchronizacji czasu, zegar na maszynie Wirtualnej może wzrosnąć błędy. Gdy istnieje tylko jedna maszyna wirtualna, efekt mogą nie być istotne, chyba że obciążenie wymaga bardzo dokładnych. Jednak w większości przypadków, firma Microsoft ma wielu, wzajemnie połączonych maszyn wirtualnych, które umożliwiają śledzenie transakcji i spójne całe wdrożenie wymaga czasu czasu. Gdy czas między maszynami wirtualnymi jest inny, można uzyskać następujące skutki:

- Uwierzytelnianie nie powiedzie się. Protokoły zabezpieczeń, takie jak protokół Kerberos lub certyfikatów zależnych od technologii zależą od czasu są spójne we wszystkich systemach.
- Jest to bardzo trudne ustalić, co wydarzyło, w systemie dzienników (lub innymi danymi) nie zgadzam się na czas. Te same zdarzenia będzie wyglądał jak pojawił się w różnym czasie, wprowadzania korelacji trudne.
- Jeśli zegar jest wyłączona, rozliczenia może niepoprawnie obliczone.



## <a name="configuration-options"></a>Opcje konfiguracji

Zazwyczaj są trzy sposoby konfigurowania synchronizacji dla maszyn wirtualnych systemu Linux hostowanych na platformie Azure:

- W domyślnej konfiguracji obrazów portalu Azure Marketplace używa czasu NTP i czasu hosta VMICTimeSync. 
- Host tylko przy użyciu VMICTimeSync.
- Użyj serwera czasu, zewnętrzne z lub bez użycia czasu hosta VMICTimeSync.


### <a name="use-the-default"></a>Użyj wartości domyślnej

Domyślnie większość obrazów portalu Azure Marketplace dla systemu Linux są skonfigurowane do synchronizacji z dwóch źródeł: 

- NTP jako podstawową, która pobiera czas z serwera NTP. Na przykład Ubuntu 16.04 LTS w portalu Marketplace, obrazy użyj **ntp.ubuntu.com**.
- Usługa VMICTimeSync jako pomocniczy, używana do komunikacji czasu hosta do maszyn wirtualnych i wprowadzić korekty po maszyny Wirtualnej jest wstrzymane w celu przeprowadzenia konserwacji. Hosty platformy Azure Użyj urządzeniami należącymi do firmy Microsoft warstwy 1, aby zachować dokładnego czasu.

W nowszych dystrybucje systemu Linux usługa VMICTimeSync używa dokładności czasu protokołu (PTP), ale mogą nie obsługiwać PTP wcześniej dystrybucje i będzie awaryjne na NTP do pobierania czasu z hosta.

Aby upewnić się, NTP jest synchronizowany poprawnie, uruchom `ntpq -p` polecenia.

### <a name="host-only"></a>Tylko do hosta 

Ponieważ serwery NTP, takich jak time.windows.com i ntp.ubuntu.com są publiczne, zsynchronizować czasu z ich wymaga, ruch wysyłany za pośrednictwem Internetu. Różnicowanie opóźnienia pakietów może negatywnie wpłynąć na jakości synchronizację czasu. Usuwanie NTP, przełączając się tylko do hosta synchronizacji skrócić czas synchronizacji wyników.

Przełączanie na tylko do hosta czas synchronizacji sprawia, że przez sens, jeśli wystąpią synchronizacja czasu za pomocą konfiguracji domyślnej. Wypróbuj usługę synchronizacji tylko do hosta aby zobaczyć, czy to mogłyby poprawić synchronizację czasu na maszynie Wirtualnej. 

### <a name="external-time-server"></a>Czas zewnętrzny serwer

Jeśli masz wymagania dotyczące synchronizacji w określonym czasie, istnieje również możliwość korzystania z serwerów czas zewnętrzny. Serwery zewnętrzne czasu może zapewnić określony czas, który może być przydatne w przypadku scenariuszy testowych, zapewniając zgodność czasie z maszynami, hostowana w centrach danych firmy Microsoft lub obsługi przestępnym sekund w specjalny sposób.

Czas zewnętrzny serwer można połączyć z usługą VMICTimeSync zapewnienie wyniki podobne do konfiguracji domyślnej. Połączenie serwera czas zewnętrzny z VMICTimeSync jest najlepszym rozwiązaniem do radzenia sobie z problemami, które mogą być przyczyną, gdy maszyny wirtualne są wstrzymane konserwacji. 

## <a name="tools-and-resources"></a>Narzędzia i zasoby

Istnieją pewne podstawowe polecenia umożliwiające sprawdzanie konfigurację synchronizacji czasu. Dokumentacja dla dystrybucji systemu Linux będą miały więcej szczegółów na najlepszym sposobem, aby skonfigurować synchronizację czasu dystrybucji.

### <a name="integration-services"></a>Usługi integracji

Sprawdź, czy usługa integracji (hv_utils) jest załadowany.

```bash
lsmod | grep hv_utils
```
Powinny zostać wyświetlone podobne do poniższego kodu:

```
hv_utils               24418  0
hv_vmbus              397185  7 hv_balloon,hyperv_keyboard,hv_netvsc,hid_hyperv,hv_utils,hyperv_fb,hv_storvsc
```

Zobacz, czy jest uruchomiony demon usługi integracji funkcji Hyper-V.

```bash
ps -ef | grep hv
```

Powinny zostać wyświetlone podobne do poniższego kodu:

```
root        229      2  0 17:52 ?        00:00:00 [hv_vmbus_con]
root        391      2  0 17:52 ?        00:00:00 [hv_balloon]
```


### <a name="check-for-ptp"></a>Wyszukaj PTP

Przy użyciu nowszych wersji systemu Linux źródła zegara dokładności czasu protokołu PTP () jest dostępna w ramach dostawcy VMICTimeSync. W starszych wersjach systemu Red Hat Enterprise Linux lub CentOS 7.x [Linux Integration Services](https://github.com/LIS/lis-next) można pobrać i użyty do zainstalowania zaktualizowanego sterownika. Korzystając z PTP, na urządzeniu z systemem Linux będzie tego formularza/dev/ptp*x*. 

Zobacz, jakie źródła zegara PTP są dostępne.

```bash
ls /sys/class/ptp
```

W tym przykładzie wartość zwracana jest *ptp0*, więc będziemy używać, aby sprawdzić nazwę zegara. Aby sprawdzić, czy urządzenie, sprawdź nazwę zegara.

```bash
cat /sys/class/ptp/ptp0/clock_name
```

Powinny zostać zwrócone **funkcji Hyper-v**.

### <a name="chrony"></a>chrony

W systemie Red Hat Enterprise Linux i CentOS 7.x, [chrony](https://chrony.tuxfamily.org/) skonfigurowany do używania PTP zegar źródła. Demon Network Management Protocol (ntpd) nie obsługuje PTP źródeł, za pomocą **chronyd** jest zalecane. Aby włączyć PTP, zaktualizuj **chrony.conf**.

```bash
refclock PHC /dev/ptp0 poll 3 dpoll -2 offset 0
```

Aby uzyskać więcej informacji na temat firmy Red Hat i NTP, zobacz [skonfigurować NTP](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/s1-configure_ntp). 

Aby uzyskać więcej informacji na temat chrony, zobacz [przy użyciu chrony](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/sect-using_chrony).

Jeśli chrony i TimeSync źródła są włączone jednocześnie, można oznaczyć jako jeden **Preferuj** który ustawia do przechowywania kopii zapasowych innego źródła. Ponieważ usługi NTP są uaktualniane zegar dużych wynikających z przesunięcia czasowego z wyjątkiem po długim czasie, VMICTimeSync odzyska zegara wstrzymanej maszyny Wirtualnej zdarzeń znacznie szybciej niż NTP narzędzi samodzielnie.


### <a name="systemd"></a>systemd 

Na Ubuntu i SUSE czas synchronizacji jest skonfigurowany przy użyciu [systemd](https://www.freedesktop.org/wiki/Software/systemd/). Aby uzyskać więcej informacji na temat systemu Ubuntu, zobacz [synchronizacji czasu](https://help.ubuntu.com/lts/serverguide/NTP.html). Aby uzyskać więcej informacji na temat SUSE, zawiera sekcja 4.5.8 w [SUSE Linux Enterprise Server 12 z dodatkiem SP3 wersji](https://www.suse.com/releasenotes/x86_64/SUSE-SLES/12-SP3/#InfraPackArch.ArchIndependent.SystemsManagement).



## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji, zobacz [dokładnego czasu systemu Windows Server 2016](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time).


