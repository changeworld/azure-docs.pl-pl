---
title: Wymagania systemowe serii StorSimple 8000 | Dokumentacja firmy Microsoft
description: Zawiera opis oprogramowania, sieci i wymagania dotyczące wysokiej dostępności i najlepsze rozwiązania dotyczące rozwiązania Microsoft Azure StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/28/2017
ms.author: alkohli
ms.openlocfilehash: 1a9cdf31c5924d22d968cd99383417ba371cd1c3
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2018
ms.locfileid: "28011065"
---
# <a name="storsimple-8000-series-software-high-availability-and-networking-requirements"></a>Oprogramowanie serii StorSimple 8000, wysokiej dostępności i wymagań sieciowych

## <a name="overview"></a>Przegląd

Zapraszamy do platformy Microsoft Azure StorSimple. W tym artykule opisano wymagania systemowe i najlepsze rozwiązania dla urządzenia StorSimple i magazynu klientów podczas uzyskiwania dostępu do urządzenia. Firma Microsoft zaleca, aby zapoznać się z informacjami dokładnie przed wdrożeniem systemu StorSimple i następnie odwołują się do niego w razie potrzeby podczas wdrażania kolejnych operacji.

Wymagania systemowe obejmują:

* **Wymagania dotyczące oprogramowania dla klientów magazynu** — w tym artykule opisano obsługiwane systemy operacyjne i wszelkie dodatkowe wymagania dotyczące tych systemów operacyjnych.
* **Wymagania dotyczące sieci dla urządzenia StorSimple** — informacje na temat portów, które muszą być otwarte w zaporze, aby umożliwić ruch iSCSI, chmury lub zarządzania.
* **Wymagania dotyczące wysokiej dostępności dla urządzenia StorSimple** — w tym artykule opisano wymagania dotyczące wysokiej dostępności i najlepszych praktyk komputera urządzenia i hosta StorSimple.

## <a name="software-requirements-for-storage-clients"></a>Wymagania dotyczące oprogramowania dla klientów magazynu

Następujące wymagania dotyczące oprogramowania dotyczą klientów pamięci masowej, które uzyskują dostęp do urządzenia StorSimple.

| Obsługiwane systemy operacyjne | Wersja wymagana | Dodatkowe wymagania dotyczące/uwagi |
| --- | --- | --- |
| Windows Server |2008 R2 SP1, 2012, 2012 R2, 2016 |Woluminy iSCSI StorSimple można używać tylko następujących typów dysku systemu Windows:<ul><li>Wolumin prosty na dysku podstawowym</li><li>Proste i dublowane wolumin na dysku dynamicznym</li></ul>Obsługiwane są tylko oprogramowania inicjatorzy iSCSI natywnie w systemie operacyjnym. Inicjatorów iSCSI sprzętu nie są obsługiwane.<br></br>Windows Server 2012 oraz alokowanie 2016 i funkcji odciążonego transferu danych są obsługiwane, jeśli używasz woluminu StorSimple iSCSI.<br><br>StorSimple można tworzyć alokowane elastycznie i pełni woluminów. Nie można go tworzyć woluminów inicjowanych częściowo.<br><br>Ponowne formatowanie woluminu alokowane elastycznie może zająć dużo czasu. Zaleca się usunięcie woluminu, a następnie utworzenie nową zamiast ponownego formatowania. Jednak jeśli jednak preferować do ponownego formatowania woluminu:<ul><li>Uruchom następujące polecenie przed ponownego formatowania, aby uniknąć opóźnienia odzyskiwanie miejsca: <br>`fsutil behavior set disabledeletenotify 1`</br></li><li>Po zakończeniu formatowania, użyj następującego polecenia, aby ponownie włączyć odzyskiwanie miejsca:<br>`fsutil behavior set disabledeletenotify 0`</br></li><li>Zastosuj poprawkę systemu Windows Server 2012, zgodnie z opisem w [KB 2878635](https://support.microsoft.com/kb/2870270) do komputera z systemem Windows Server.</li></ul></li></ul></ul> Jeśli konfigurujesz StorSimple Snapshot Manager lub kartę StorSimple dla programu SharePoint, przejdź do [wymagań dotyczących oprogramowania dla składników opcjonalnych](#software-requirements-for-optional-components). |
| VMware ESX |5.5 i 6.0 |Obsługiwane z programem VMware vSphere jako klient. Funkcja VAAI bloku jest obsługiwana z programem VMware vSphere urządzenia StorSimple. |
| Linux RHEL/CentOS |5, 6 i 7 |Obsługa klientów iSCSI Linux z wersjami inicjatora iSCSI Otwórz 5, 6 i 7. |
| Linux |SUSE Linux 11 | |

> [!NOTE]
> IBM AIX nie jest obecnie obsługiwane z StorSimple.


## <a name="software-requirements-for-optional-components"></a>Wymagania dotyczące oprogramowania dla składników opcjonalnych

Następujące wymagania dotyczące oprogramowania dotyczą opcjonalne składniki StorSimple (StorSimple Snapshot Manager i karty StorSimple dla programu SharePoint).

| Składnik | Platforma hosta | Dodatkowe wymagania dotyczące/uwagi |
| --- | --- | --- |
| StorSimple Snapshot Manager |Windows Server 2008 R2 z dodatkiem SP1, 2012, 2012 R2 |Użycie programu StorSimple Snapshot Manager w systemie Windows Server jest wymagany dla kopii zapasowej/przywracania dublowany dysków dynamicznych i wszelkich spójnych z aplikacją kopii zapasowych.<br> StorSimple Snapshot Manager jest obsługiwana tylko w przypadku systemu Windows Server 2008 R2 z dodatkiem SP1 (64-bitowy), Windows Server 2012 R2 i Windows Server 2012.<ul><li>Jeśli używasz systemu Windows Server 2012, należy zainstalować .NET 3.5 — 4.5, przed zainstalowaniem programu StorSimple Snapshot Manager.</li><li>Jeśli korzystasz z systemu Windows Server 2008 R2 z dodatkiem SP1, należy zainstalować program Windows Management Framework 3.0, przed zainstalowaniem programu StorSimple Snapshot Manager.</li></ul> |
| Adapter usługi StorSimple dla programu SharePoint |Windows Server 2008 R2 z dodatkiem SP1, 2012, 2012 R2 |<ul><li>Karta StorSimple dla programu SharePoint jest obsługiwana tylko w programie SharePoint 2010 oraz SharePoint 2013.</li><li>SPZ wymaga programu SQL Server Enterprise Edition w wersji 2008 R2 lub 2012.</li></ul> |

## <a name="networking-requirements-for-your-storsimple-device"></a>Wymagań sieciowych dotyczących urządzenia StorSimple

Urządzenia StorSimple to urządzenie w trybie blokady. Jednak porty muszą być otwarte w zaporze, aby umożliwić iSCSI, chmur i ruch związany z zarządzaniem. W poniższej tabeli wymieniono porty, które muszą być otwarte w zaporze. W tej tabeli *w* lub *przychodzących* odwołuje się do kierunek, w którym przychodzące żądania klientów dostępu urządzenia. *Limit* lub *wychodzących* odwołuje się do kierunek, w którym urządzenia StorSimple wysyła dane zewnętrznie, poza wdrożenia: na przykład ruch wychodzący do Internetu.

| Nr portu<sup>1,2</sup> | Przychodzący lub wychodzący | Zakres portów | Wymagane | Uwagi |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP)<sup>3</sup> |limit |WAN |Nie |<ul><li>Port wyjściowy jest używana do dostępu do Internetu do pobierania aktualizacji.</li><li>Serwer proxy sieci web wychodzącego jest konfigurowane przez użytkownika.</li><li>Aby umożliwić aktualizacje systemu, ten port również musi być otwarty dla kontrolera, stałe adresy IP.</li></ul> |
| TCP 443 (HTTPS)<sup>3</sup> |limit |WAN |Yes |<ul><li>Wychodzący port jest używany do uzyskiwania dostępu do danych w chmurze.</li><li>Serwer proxy sieci web wychodzącego jest konfigurowane przez użytkownika.</li><li>Aby umożliwić aktualizacje systemu, ten port również musi być otwarty dla kontrolera, stałe adresy IP.</li><li>Port ten jest również używany na obu kontrolerów dla wyrzucanie elementów bezużytecznych.</li></ul> |
| UDP 53 (DNS) |limit |WAN |W niektórych przypadkach; Zobacz uwagi. |Ten port jest wymagany tylko wtedy, gdy używasz serwera DNS w Internecie. |
| UDP 123 (NTP) |limit |WAN |W niektórych przypadkach; Zobacz uwagi. |Ten port jest wymagany tylko wtedy, gdy używasz serwera NTP internetowego. |
| TCP 9354 |limit |WAN |Yes |Wychodzący port jest używany przez urządzenia StorSimple do komunikowania się z usługą Menedżera urządzeń StorSimple. |
| 3260 (iSCSI) |W |LAN |Nie |Port ten jest używany do dostępu do danych za pośrednictwem interfejsu iSCSI. |
| 5985 |W |LAN |Nie |Przychodzący port jest używany przez StorSimple Snapshot Manager do komunikowania się z urządzeniem StorSimple.<br>Port ten jest również używany podczas zdalnego nawiązywania połączenia programu Windows PowerShell dla urządzenia StorSimple za pośrednictwem protokołu HTTP. |
| 5986 |W |LAN |Nie |Port ten jest używany podczas zdalnego nawiązywania połączenia programu Windows PowerShell dla urządzenia StorSimple przy użyciu protokołu HTTPS. |

<sup>1</sup> nie portów przychodzących muszą być otwarte na publiczny Internet.

<sup>2</sup> Jeśli wiele portów przenoszenia konfiguracji bramy, kolejność kierowany ruch wychodzący będzie ustalana na podstawie opisanych w kolejności routingu portów [routingu portu](#routing-metric), poniżej.

<sup>3</sup> kontrolera, stałe adresy IP na urządzeniu StorSimple muszą być rutowalne i możliwość połączenia się z Internetem bezpośrednio lub za pośrednictwem serwera proxy sieci web skonfigurowana. Stałe adresy IP są używane do obsługi aktualizacji urządzenia i wyrzucanie elementów bezużytecznych. Jeśli kontrolery urządzeń nie można połączyć się z Internetem za pośrednictwem stałe adresy IP, nie można zaktualizować urządzenia StorSimple i wyrzucanie elementów bezużytecznych nie będzie działać prawidłowo.

> [!IMPORTANT]
> Upewnij się, czy zapory nie zmodyfikować lub odszyfrować żaden ruch protokołu SSL między urządzenia StorSimple i Azure.


### <a name="url-patterns-for-firewall-rules"></a>Wzorce adresu URL dla reguł zapory

Administratorzy sieci często można skonfigurować reguł zapory zaawansowane, na podstawie wzorców adresów URL do filtrowania ruchu przychodzącego i wychodzącego ruchu sieciowego. Urządzenia StorSimple i usługi Menedżer StorSimple urządzenia są zależne od innych aplikacji firmy Microsoft, takich jak usługi Azure Service Bus, Azure Active Directory kontroli dostępu konta magazynu i serwerach Microsoft Update. Wzorce adresu URL skojarzone z tymi aplikacjami można skonfigurować reguł zapory. Należy zrozumieć, że wzorców adresów URL skojarzone z tymi aplikacjami można zmienić. Z kolei wymaga administratorem sieci, aby monitorować i zaktualizować reguł zapory dla Twojego urządzenia StorSimple, jak i w razie potrzeby.

Firma Microsoft zaleca, aby ustawić regułach zapory dla ruchu wychodzącego, oparte na StorSimple liberally stałe adresy IP, w większości przypadków. Jednak można użyć poniższe informacje, można ustawić reguły zapory zaawansowane, które są potrzebne do utworzenia bezpiecznego środowiska.

> [!NOTE]
> Urządzenia (źródło) adresów IP powinien być zawsze ustawiony na wszystkich interfejsów sieciowych włączone. Miejsce docelowe adresy IP należy wybrać opcję [zakresy IP centrum danych Azure](https://www.microsoft.com/en-us/download/confirmation.aspx?id=41653).


#### <a name="url-patterns-for-azure-portal"></a>Wzorce adresu URL dla portalu Azure

| Wzorzec URL | Składnik/funkcji | Adresy IP urządzeń |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*`<br>`https://login.windows.net` |Usługa menedżera urządzeń StorSimple<br>Usługa kontroli dostępu<br>Azure Service Bus<br>Usługa uwierzytelniania |Interfejsy sieciowe z obsługą chmury |
| `https://*.backup.windowsazure.com` |Rejestracja urządzenia |Tylko dane 0 |
| `http://crl.microsoft.com/pki/*`<br>`http://www.microsoft.com/pki/*` |Odwoływanie certyfikatów |Interfejsy sieciowe z obsługą chmury |
| `https://*.core.windows.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Monitorowanie i kontami magazynu Azure |Interfejsy sieciowe z obsługą chmury |
| `http://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`http://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`http://download.microsoft.com`<br>`http://wustat.windows.com`<br>`http://ntservicepack.microsoft.com` |Serwerów Microsoft Update<br> |Tylko stałe adresy IP kontrolera |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |Tylko stałe adresy IP kontrolera |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |Pakiet pomocy technicznej |Interfejsy sieciowe z obsługą chmury |

#### <a name="url-patterns-for-azure-government-portal"></a>Wzorce adresu URL portalu Azure dla instytucji rządowych

| Wzorzec URL | Składnik/funkcji | Adresy IP urządzeń |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.us/*`<br>`https://*.accesscontrol.usgovcloudapi.net/*`<br>`https://*.servicebus.usgovcloudapi.net/*`<br>`https://login.microsoftonline.us` |Usługa menedżera urządzeń StorSimple<br>Usługa kontroli dostępu<br>Azure Service Bus<br>Usługa uwierzytelniania |Interfejsy sieciowe z obsługą chmury |
| `https://*.backup.windowsazure.us` |Rejestracja urządzenia |Tylko dane 0 |
| `http://crl.microsoft.com/pki/*`<br>`http://www.microsoft.com/pki/*` |Odwoływanie certyfikatów |Interfejsy sieciowe z obsługą chmury |
| `https://*.core.usgovcloudapi.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Monitorowanie i kontami magazynu Azure |Interfejsy sieciowe z obsługą chmury |
| `http://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`http://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`http://download.microsoft.com`<br>`http://wustat.windows.com`<br>`http://ntservicepack.microsoft.com` |Serwerów Microsoft Update<br> |Tylko stałe adresy IP kontrolera |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |Tylko stałe adresy IP kontrolera |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |Pakiet pomocy technicznej |Interfejsy sieciowe z obsługą chmury |

### <a name="routing-metric"></a>Metryka routingu

Metryka routingu jest skojarzony z interfejsów i bramy, która kierować je do określonych sieci. Metryki routingu jest używany przez protokół routingu do obliczenia najlepsze ścieżka do danego przeznaczenia, jeśli dowie się, że istnieje wiele ścieżek do tego samego miejsca docelowego. Niższe metryki routingu, tym wyższy priorytet.

W kontekście StorSimple Jeśli wiele interfejsów sieciowych i bram, które są skonfigurowane do ruchu kanału metryki routingu wejdzie w Odtwórz, aby określić względną kolejność, w której używane Pobierz interfejsy. Nie można zmienić metryki routingu przez użytkownika. Można jednak użyć `Get-HcsRoutingTable` polecenia cmdlet, aby wydrukować tabeli routingu (i metryk) na urządzeniu StorSimple. Więcej informacji na temat polecenia cmdlet Get-HcsRoutingTable w [StorSimple Rozwiązywanie problemów z wdrożenia](storsimple-troubleshoot-deployment.md).

Algorytm metryki routingu dla Update 2 lub nowszym można wyjaśnić w następujący sposób.

* Zestaw wartości ustalonej zostały przypisane do interfejsów sieciowych.
* Należy wziąć pod uwagę tabeli przykładzie pokazano poniżej wartości przypisane do różnych interfejsów sieciowych, gdy są one chmury — włączone lub wyłączone chmury, ale skonfigurowanej bramy. Należy pamiętać, że wartości w tym miejscu są tylko przykładowe wartości.

    | Interfejs sieciowy | Włączoną obsługę chmury | Chmura wyłączonymi z bramy |
    |-----|---------------|---------------------------|
    | Dane 0  | 1            | -                        |
    | Dane 1  | 2            | 20                       |
    | Dane 2  | 3            | 30                       |
    | Dane 3  | 4            | 40                       |
    | Dane 4  | 5            | 50                       |
    | Dane 5  | 6            | 60                       |


* Kolejność, w której będą kierowane ruchu w chmurze za pośrednictwem interfejsów sieciowych jest:
  
    *Dane 0 > danych 1 > Data 2 > danych 3 > dane 4 > dane 5*
  
    Można to wyjaśnić na poniższym przykładzie.
  
    Należy wziąć pod uwagę urządzenia StorSimple z dwoma interfejsami sieciowymi włączoną obsługę chmury, dane 0 do dane 5. Dane od 1 do 4 danych są wyłączone chmury, ale ma skonfigurowanej bramy. Kolejność, w którym ruch będą kierowane do tego urządzenia będą:
  
    *Dane 0 (1) > dane 5 (6) > danych 1 (20) > dane 2 (30) > danych 3 (40) > dane 4 (50)*
  
    *Liczby w nawiasach wskazują odpowiednich metryk routingu.*
  
    W przypadku niepowodzenia dane 0 ruchu w chmurze będzie pobrać kierowane do dane 5. Biorąc pod uwagę, że brama jest skonfigurowana w innych sieciach, jeżeli zarówno dane 0 i dane 5 się nie powieść, ruchu w chmurze będzie przejście przez 1 danych.
* Jeśli interfejs sieciowy z obsługą chmury nie powiedzie się, to 3 ponowne próby z opóźnieniem drugi 30 do nawiązania połączenia interfejsu. W przypadku awarii ponownych prób, ruch jest kierowany do następnej dostępnej włączoną obsługę chmury interfejsu zgodnie z ustaleniami tabeli routingu. Jeśli wszystkie sieci korzystających z chmury interfejsy kończyć się niepowodzeniem, następnie urządzenia zakończy się niepowodzeniem przez do innego kontrolera (ponowny rozruch w tym przypadku).
* W przypadku awarii VIP dla interfejsu sieci iSCSI, będzie 3 ponowne próby z opóźnieniem 2 sekundy. To zachowanie jest przebywała niż w poprzednich wersjach. Jeśli wszystkie interfejsy sieciowe iSCSI zakończą się niepowodzeniem, kontroler trybu failover zostanie przeprowadzona (wraz z ponownego uruchomienia).
* Alert jest zgłaszany również na urządzeniu StorSimple, po awarii adresu VIP. Aby uzyskać więcej informacji, przejdź do [alertów krótkimi opisami](storsimple-8000-manage-alerts.md).
* W postaci liczby ponownych prób iSCSI ma pierwszeństwo przed chmury.
  
    Rozważmy następujący przykład: A StorSimple, urządzenie ma włączone dwa interfejsy sieci, dane 0 i 1 danych. Dane 0 jest włączoną obsługę chmury danych 1 jest zarówno chmury i włączono interfejs iSCSI. Żadne inne interfejsy sieciowe na tym urządzeniu są włączone dla chmury lub iSCSI.
  
    1 danych kończy się niepowodzeniem, podane go w przypadku ostatniego interfejsu sieci iSCSI, spowoduje awarię kontrolera 1 danych na innym kontrolerze.

### <a name="networking-best-practices"></a>Najlepsze rozwiązania w zakresie sieci

Oprócz powyższych wymagań sieciowych, aby uzyskać optymalną wydajność rozwiązania StorSimple należy stosować poniższe najlepsze rozwiązania:

* Upewnij się, że urządzenia StorSimple ma dedykowany 40 MB/s przepustowości (lub więcej) dostępne przez cały czas. Nie należy udostępniać tego przepustowości (lub alokacji, należy zagwarantować przy użyciu zasad QoS) z innymi aplikacjami.
* Upewnij się, że połączenie sieciowe z Internetem jest dostępna przez cały czas. Sporadyczne lub zawodnych połączenia internetowego z urządzeń, takich jak bez łączności z Internetem, spowoduje nieobsługiwaną konfiguracją.
* Izolowanie ruchu iSCSI i chmury przez używania dedykowanych interfejsów sieciowych na twoim urządzeniu dostęp iSCSI i chmury. Aby uzyskać więcej informacji, zobacz temat jak [zmodyfikować interfejsów sieciowych](storsimple-8000-modify-device-config.md#modify-network-interfaces) na urządzeniu StorSimple.
* Nie należy używać konfiguracji Link Aggregation Control Protocol (LACP) interfejsów sieciowych. To jest nieobsługiwana konfiguracja.

## <a name="high-availability-requirements-for-storsimple"></a>Wymagania dotyczące wysokiej dostępności dla urządzenia StorSimple

Platforma sprzętowa, która jest zawarta w rozwiązaniu StorSimple zapewnia dostępność i niezawodność funkcje, które zapewnia podstawę dla infrastruktury magazynu o wysokiej dostępności, odpornej na uszkodzenia w centrum danych. Istnieją wymagania i najlepsze rozwiązania, które powinny być zgodne z do zapewnienia dostępności rozwiązania StorSimple. Przed wdrożeniem StorSimple dokładnie przejrzeć następujące wymagania i najlepsze rozwiązania dotyczące urządzenia StorSimple i komputerach-hostach połączonych.

Aby uzyskać więcej informacji dotyczących monitorowania i konserwowania składniki sprzętowe urządzenia StorSimple, przejdź do [korzystania z usługi Menedżera urządzeń StorSimple, monitorem składniki sprzętowe i stan](storsimple-8000-monitor-hardware-status.md) i [StorSimple Wymiana sprzętu składnika](storsimple-8000-hardware-component-replacement.md).

### <a name="high-availability-requirements-and-procedures-for-your-storsimple-device"></a>Wymagania dotyczące wysokiej dostępności i procedury dotyczące urządzenia StorSimple

Przejrzyj następujące informacje uważnie, aby zapewnić wysoką dostępność urządzenia StorSimple.

#### <a name="pcms"></a>PCMs

Urządzenia StorSimple obejmują nadmiarowe, wyłączania zasilania i chłodzenia modułów (PCMs). Każdy PCM ma wystarczającej wydajności, aby zapewnić usługi dla całej obudowy. Aby zapewnić wysoką dostępność, należy zainstalować zarówno PCMs.

* Twoje PCMs nawiązać połączenia z różnych źródeł napędu zapewnić dostępność, jeśli źródła zasilania nie powiedzie się.
* W przypadku niepowodzenia PCM żądanie zastępczy natychmiast.
* Usunięcie nie powiodło się PCM tylko wtedy, gdy masz zastąpienia i są gotowe do zainstalowania go.
* Nie usuwaj PCMs obu jednocześnie. Moduł PCM zawiera moduł baterii kopii zapasowej. Usunięcie zarówno PCMs spowoduje zamknięcie bez ochrony baterii, a stan urządzenia nie zostaną zapisane. Aby uzyskać więcej informacji na temat baterii, przejdź do [Obsługa modułu kopii zapasowej baterii](storsimple-8000-battery-replacement.md#maintain-the-backup-battery-module).

#### <a name="controller-modules"></a>Moduły kontrolera

Urządzenia StorSimple obejmują moduły nadmiarowe, wyłączania kontrolera. Moduły kontrolera działać w sposób aktywny/pasywny. W dowolnym momencie jeden moduł kontrolera jest aktywna i udostępnia usługi, gdy inny moduł kontroler jest w stanie pasywnym. Moduł pasywnym kontrolera jest włączona i jest aktywna, jeśli moduł aktywnym kontrolerze nie powiedzie się lub jest usunięte. Każdy moduł kontroler ma wystarczającej wydajności, aby zapewnić usługi dla całej obudowy. Oba moduły kontrolera muszą być zainstalowane, aby zapewnić wysoką dostępność.

* Upewnij się, czy obu modułów kontrolera są zainstalowane przez cały czas.
* Jeśli moduł kontrolera nie powiedzie się, żądanie zastępczy natychmiast.
* Usuń moduł kontrolera nie powiodło się tylko wtedy, gdy masz zastąpienia i są gotowe do zainstalowania go. Usuwania modułu dłuższy czas będzie miało wpływ na przepływ powietrza i dlatego chłodzenia systemu.
* Upewnij się, że połączenia sieciowe do obu modułów kontrolera są identyczne i interfejsów sieciowych podłączonych ma konfiguracji sieciowej identyczne.
* Jeśli moduł kontrolera awarią lub koniecznością wymiany, upewnij się, że moduł kontroler jest w stanie aktywnym przed wymianą moduł kontrolera nie powiodło się. Aby sprawdzić, czy kontroler jest aktywny, przejdź do [Zidentyfikuj active kontroler na urządzeniu](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
* Nie usuwaj obu modułów kontrolera w tym samym czasie. Jeśli kontroler trybu failover jest w toku, wyłączony moduł rezerwy kontrolera lub nie usunąć go z obudowy.
* Przejściu w tryb failover kontrolera Zaczekaj co najmniej pięć minut przed usunięciem albo moduł kontrolera.

#### <a name="network-interfaces"></a>Interfejsy sieciowe

StorSimple urządzenia kontrolera modules ma cztery 1 GB i 10 dwa interfejsy sieci Gigabit Ethernet.

* Upewnij się, że połączenia sieciowe do obu modułów kontrolera są identyczne i interfejsy sieci czy interfejsy modułu kontrolera są podłączone do konfiguracji sieci identyczne.
* Jeśli to możliwe, należy wdrożyć połączenia sieciowe w różnych przełączników, aby zapewnić dostępność usługi awarii urządzenia sieciowe.
* W przypadku odłączania tylko lub ostatniego pozostałego interfejsu włączono interfejs iSCSI (z adresy IP przypisane), najpierw wyłącz interfejs, a następnie odłącz kable. Jeśli interfejs zostanie odłączone najpierw, następnie spowoduje active kontrolera do trybu failover pasywnym kontrolera. Jeśli kontroler pasywny ma również odpowiedniego odłączone interfejsy, obu kontrolerów zostanie uruchomiony wielokrotnie przed rozpoczęciem na jeden kontroler.
* Połączenie z siecią co najmniej dwa interfejsy danych z każdego modułu kontrolera.
* Jeśli włączono dwa interfejsy 10 GbE, wdrożyć w różnych przełączników.
* Jeśli to możliwe, użyj funkcji MPIO na serwerach, aby upewnić się, że serwery tolerują błędy łącza, sieci lub interfejsu.

Aby uzyskać więcej informacji o sieci Twojego urządzenia pod kątem wysokiej dostępności i wydajności, przejdź do [zainstalować do urządzenia StorSimple 8100](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) lub [zainstalować do urządzenia StorSimple 8600](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

#### <a name="ssds-and-hdds"></a>Dyski SSD i dysków twardych

Urządzenia StorSimple obejmują dysków półprzewodnikowych (SSD) oraz stacje dysków twardych (HDD), które są chronione przy użyciu dublowanych miejsc do. Użyj miejsc dublowanych gwarantuje, że urządzenie jest w stanie tolerowanie awarii jednego lub więcej dysków SSD i dysków twardych.

* Upewnij się, że zainstalowano wszystkie moduły SSD i HDD.
* W przypadku niepowodzenia dysków SSD i HDD żądanie zastępczy natychmiast.
* Jeśli dysków SSD i HDD nie powiedzie się lub wymagana jest wymiana, upewnij się, należy usunąć tylko dysków SSD i HDD, która wymaga zastąpienia.
* Nie usuwaj więcej niż jeden dysków SSD i HDD z systemu w dowolnym momencie w czasie.
  Awarii 2 lub więcej dysków określonego typu (dysk twardy, dysk SSD) lub niepowodzenie kolejnych w krótkim czasie może skutkować utratą danych nieprawidłowego działania i możliwości systemu. W takim przypadku [skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md) Aby uzyskać pomoc.
* Podczas wymiany, monitorować **udostępnione składniki** w **kondycji sprzętu** bloku dla dysków SSD i dysków twardych. Stan zielony wyboru wskazuje, że dyski są w dobrej kondycji lub OK punktu czerwony wykrzyknik wskazuje, nie powiodło się dysków SSD i HDD.
* Zaleca się skonfigurowanie migawki w chmurze dla wszystkich woluminów, które należy chronić w przypadku awarii systemu.

#### <a name="ebod-enclosure"></a>Obudowa EBOD

Model urządzenia StorSimple 8600 zawiera załącznik rozszerzony Bunch dla dysków (EBOD) oprócz podstawowego obudowy. EBOD zawiera kontrolery EBOD i stacje dysków twardych (HDD), które są chronione przy użyciu dublowane spacji. Użyj miejsc dublowanych gwarantuje, że urządzenie jest w stanie tolerowanie awarii jednego lub więcej dysków twardych. Obudowa EBOD jest podłączona do głównej obudowy poprzez nadmiarowych kabli SAS.

* Upewnij się, że oba EBOD obudowa kontrolera modułów zarówno kabli SAS, jak i wszystkie stacje dysków twardych zainstalowanych przez cały czas.
* Jeśli moduł EBOD obudowa kontrolera nie powiedzie się, żądanie zastępczy natychmiast.
* Jeśli moduł EBOD obudowa kontrolera nie powiedzie się, upewnij się, moduł kontrolera jest aktywna, zanim zastąpienie modułu nie powiodło się. Aby sprawdzić, czy kontroler jest aktywny, przejdź do [Zidentyfikuj active kontroler na urządzeniu](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
* Podczas wymiany modułu EBOD kontroler na stałe monitorowanie stanu składnika w usłudze Menedżer StorSimple urządzeń po zalogowaniu się do **Monitor** > **kondycji sprzętu**.
* Jeśli kabel SAS nie powiedzie się lub wymagana jest wymiana (Microsoft Support powinny być wykonywane dokonanie takiego określenia), upewnij się, usunięcie tylko wymagana jest wymiana kabel SAS.
* Nie należy jednocześnie usuwać zarówno kabli SAS z systemu w dowolnym momencie w czasie.

### <a name="high-availability-recommendations-for-your-host-computers"></a>Zalecenia dotyczące wysokiej dostępności dla komputerów hostów

Uważnie przejrzyj następujące najlepsze rozwiązania zapewniające wysoką dostępność hostów podłączonych do urządzenia StorSimple.

* Konfigurowanie StorSimple z [konfiguracje klastrów serwera plików z dwoma węzłami][1]. Przez usunięcie pojedynczego punktu awarii i tworzenia w nadmiarowość po stronie hosta, całe rozwiązanie staje się wysokiej dostępności.
* Użyj stale dostępnych udziałów (CA) z systemem Windows Server 2012 (protokół SMB 3.0) wysokiej dostępności podczas pracy w trybie failover kontrolerów magazynu. Aby uzyskać dodatkowe informacje dotyczące konfigurowania klastrów serwera plików i stale dostępnych udziałów w systemie Windows Server 2012, zapoznaj się to [pokaz wideo](http://channel9.msdn.com/Events/IT-Camps/IT-Camps-On-Demand-Windows-Server-2012/DEMO-Continuously-Available-File-Shares).

## <a name="next-steps"></a>Kolejne kroki

* [Więcej informacji na temat limitów systemu StorSimple](storsimple-8000-limits.md).
* [Dowiedz się, jak wdrażanie rozwiązania StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

<!--Reference links-->
[1]: https://technet.microsoft.com/library/cc731844(v=WS.10).aspx
