---
title: Wymagania systemowe serii StorSimple 8000 | Dokumentacja firmy Microsoft
description: Zawiera opis oprogramowania, sieci i wymaganiami wysokiej dostępności i najlepsze rozwiązania dotyczące rozwiązania Microsoft Azure StorSimple.
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
ms.openlocfilehash: f05e3e85d36ffc23a193a6771a0271c71b2f8544
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60631910"
---
# <a name="storsimple-8000-series-software-high-availability-and-networking-requirements"></a>Oprogramowanie serii StorSimple 8000, wysoką dostępność i wymagania sieciowe

## <a name="overview"></a>Omówienie

Witamy na platformie Microsoft Azure StorSimple. W tym artykule opisano wymagania systemowe i najlepszych rozwiązań dla urządzenia StorSimple i klientów usług magazynowych korzystających urządzenia. Firma Microsoft zaleca, aby zapoznać się z informacjami dokładnie przed wdrożeniem systemu StorSimple i następnie wrócić do niego zgodnie z potrzebami podczas wdrażania i kolejna operacja.

Wymagania systemowe, obejmują:

* **Wymagania dotyczące oprogramowania dla klientów usług magazynowych korzystających** — w tym artykule opisano obsługiwane systemy operacyjne i wszelkie dodatkowe wymagania dotyczące tych systemów operacyjnych.
* **Wymagania dotyczące sieci dla urządzenia StorSimple** — informacje na temat portów, które muszą być otwarte w zaporze, aby umożliwić ruch iSCSI, chmury lub zarządzania.
* **Wymaganiami wysokiej dostępności dla usługi StorSimple** — w tym artykule opisano wymagania w zakresie wysokiej dostępności i najlepszych rozwiązań dotyczących komputera urządzenia i hosta usługi StorSimple.

## <a name="software-requirements-for-storage-clients"></a>Wymagania dotyczące oprogramowania dla klientów usług magazynowych korzystających

Następujące wymagania dotyczące oprogramowania dotyczą klientów magazynu, do których dostęp urządzenia StorSimple.

| Obsługiwane systemy operacyjne | Wymagana wersja | Dodatkowe wymagania dotyczące/uwagi |
| --- | --- | --- |
| Windows Server |2008 R2 SP1, 2012, 2012 R2, 2016 |Woluminy iSCSI StorSimple są obsługiwane do użytku na tylko następujące typy dysku Windows:<ul><li>Wolumin prosty na dysku podstawowym</li><li>Prostych i dublowanych woluminów na dysku dynamicznym</li></ul>Obsługiwane są tylko oprogramowania inicjatorów iSCSI natywnie w systemie operacyjnym. Inicjatorów iSCSI sprzętu nie są obsługiwane.<br></br>Windows Server 2012 i 2016 alokowanie elastyczne i funkcji odciążonego transferu danych są obsługiwane, jeśli używasz woluminu iSCSI StorSimple.<br><br>Usługa StorSimple można tworzyć alokowane elastycznie i inicjowane w pełni woluminów. Nie można go tworzyć woluminów inicjowanych częściowo.<br><br>Automatyczne formatowanie alokowane elastycznie woluminu może zająć dużo czasu. Zaleca się usunięcie woluminu, a następnie utworzenie nową zamiast ponownego formatowania. Jednak jeśli jednak chcesz ponownie sformatować wolumin:<ul><li>Uruchom następujące polecenie przed Formatuj ponownie w celu uniknięcia opóźnień odzyskiwanie miejsca: <br>`fsutil behavior set disabledeletenotify 1`</br></li><li>Po zakończeniu formatowania, użyj następującego polecenia, aby ponownie włączyć odzyskiwanie miejsca:<br>`fsutil behavior set disabledeletenotify 0`</br></li><li>Zastosuj poprawkę systemu Windows Server 2012, zgodnie z opisem w [KB 2878635](https://support.microsoft.com/kb/2870270) do komputera systemu Windows Server.</li></ul></li></ul></ul> Jeśli konfigurujesz programu StorSimple Snapshot Manager lub adaptera StorSimple dla programu SharePoint, przejdź do strony [wymagania programowe dotyczące składników opcjonalnych](#software-requirements-for-optional-components). |
| VMware ESX |5.5 i 6.0 |Obsługiwane z funkcją VMware vSphere jako klient. Blok VAAI funkcja jest obsługiwana z funkcją VMware vSphere na urządzeniach StorSimple. |
| Linux RHEL/CentOS |5, 6 i 7 |Obsługa klientów iSCSI systemu Linux z wersjami inicjatora open iSCSI 5, 6 i 7. |
| Linux |SUSE Linux 11 | |

> [!NOTE]
> IBM AIX nie jest obecnie obsługiwane za pomocą usługi StorSimple.


## <a name="software-requirements-for-optional-components"></a>Wymagania dotyczące oprogramowania dla składników opcjonalnych

Następujące wymagania dotyczące oprogramowania dotyczą opcjonalne składniki StorSimple (z programu StorSimple Snapshot Manager i adaptera StorSimple dla programu SharePoint).

| Składnik | Platforma hosta | Dodatkowe wymagania dotyczące/uwagi |
| --- | --- | --- |
| StorSimple Snapshot Manager |Windows Server 2008 R2 z dodatkiem SP1, 2012, 2012 R2 |Korzystanie z programu StorSimple Snapshot Manager w systemie Windows Server jest wymagane, i przywracania kopii zapasowych dysków dynamicznych, dublowane i wszelkich spójnych z aplikacją kopii zapasowych.<br> Przystawki StorSimple Snapshot Manager jest obsługiwana tylko dla systemu Windows Server 2008 R2 z dodatkiem SP1 (64-bitowy), Windows Server 2012 R2 i Windows Server 2012.<ul><li>Jeśli używane są Windows Server 2012, należy zainstalować .NET 3.5 — 4.5, przed zainstalowaniem programu StorSimple Snapshot Manager.</li><li>Jeśli używasz systemu Windows Server 2008 R2 z dodatkiem SP1, Windows Management Framework 3.0 należy zainstalować, przed zainstalowaniem programu StorSimple Snapshot Manager.</li></ul> |
| Adapter usługi StorSimple dla programu SharePoint |Windows Server 2008 R2 z dodatkiem SP1, 2012, 2012 R2 |<ul><li>Adapter usługi StorSimple dla programu SharePoint jest obsługiwany tylko w programie SharePoint 2010 i SharePoint 2013.</li><li>SPZ wymaga programu SQL Server Enterprise Edition w wersji 2008 R2 lub 2012.</li></ul> |

## <a name="networking-requirements-for-your-storsimple-device"></a>Wymagania dotyczące sieci dla urządzenia StorSimple

Urządzenie StorSimple to urządzenie w trybie blokady. Jednak porty muszą być otwierane w taki sposób, aby umożliwić iSCSI, chmury i ruch związany z zarządzaniem. Poniższa tabela zawiera listę portów, które muszą być otwarte w zaporze. W tej tabeli *w* lub *dla ruchu przychodzącego* Określa kierunek, w którym przychodzące żądania klientów dostęp do urządzenia. *Limit* lub *wychodzącego* Określa kierunek, w którym urządzenie StorSimple wysyła dane zewnętrznie, poza wdrożenia: na przykład, ruch wychodzący do Internetu.

| Nr portu<sup>1,2</sup> | Wewnątrz lub na zewnątrz | Zakres portów | Wymagane | Uwagi |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP)<sup>3</sup> |Wyjście |Sieć WAN |Nie |<ul><li>Wychodząca przez port służy do dostępu do Internetu do pobierania aktualizacji.</li><li>Serwer proxy ruchu wychodzącego w sieci web jest użytkownika.</li><li>Aby zezwolić na aktualizacje systemu, ten port musi być również otwarte na potrzeby stałe adresy IP kontrolera.</li></ul> |
| TCP 443 (HTTPS)<sup>3</sup> |Wyjście |Sieć WAN |Yes |<ul><li>Wychodząca przez port jest używany do uzyskiwania dostępu do danych w chmurze.</li><li>Serwer proxy ruchu wychodzącego w sieci web jest użytkownika.</li><li>Aby zezwolić na aktualizacje systemu, ten port musi być również otwarte na potrzeby stałe adresy IP kontrolera.</li><li>Port ten jest również używany na obu kontrolerach do wyrzucania elementów bezużytecznych.</li></ul> |
| UDP 53 (DNS) |Wyjście |Sieć WAN |W niektórych przypadkach; Zobacz uwagi. |Ten port jest wymagany tylko wtedy, gdy używasz serwera DNS internetowego. |
| UDP 123 (NTP) |Wyjście |Sieć WAN |W niektórych przypadkach; Zobacz uwagi. |Ten port jest wymagany tylko wtedy, gdy korzystają z serwera NTP oparty na Internecie. |
| TCP 9354 |Wyjście |Sieć WAN |Yes |Port ruchu wychodzącego jest używany przez urządzenia StorSimple do komunikowania się z usługą Menedżera urządzeń StorSimple. |
| 3260 (iSCSI) |W |Sieć LAN |Nie |Port ten jest używany do dostępu do danych za pośrednictwem interfejsu iSCSI. |
| 5985 |W |Sieć LAN |Nie |Przychodzący port jest używany przez programu StorSimple Snapshot Manager do komunikowania się z urządzeniem StorSimple.<br>Port ten jest również używany podczas zdalnego nawiązywania połączenia programu Windows PowerShell dla usługi StorSimple przy użyciu protokołu HTTP. |
| 5986 |W |Sieć LAN |Nie |Port ten jest używany podczas zdalnego nawiązywania połączenia programu Windows PowerShell dla usługi StorSimple przy użyciu protokołu HTTPS. |

<sup>1</sup> nie portów przychodzących, które muszą być otwarte w publicznym Internecie.

<sup>2</sup> Jeśli wiele portów, posiadają konfiguracji bramy, kolejność ruch wychodzący trasowane będzie ustalana na podstawie kolejności routingu portów opisane w [routingu portu](#routing-metric)poniżej.

<sup>3</sup> stałe adresy IP na urządzeniu StorSimple kontrolera muszą być routowalne i mieć możliwość połączenia z Internetu, bezpośrednio lub za pośrednictwem serwera proxy sieci web skonfigurowany. Stałe adresy IP są używane do obsługi aktualizacji urządzenia i wyrzucania elementów bezużytecznych. Jeśli kontrolery urządzeń nie może połączyć się z Internetem za pośrednictwem stałe adresy IP, nie będziesz w stanie zaktualizować urządzenia StorSimple i wyrzucania elementów bezużytecznych nie będzie działać prawidłowo.

> [!IMPORTANT]
> Upewnij się, zapory nie modyfikować ani odszyfrować cały ruch protokołu SSL między urządzeniem StorSimple i platformą Azure.


### <a name="url-patterns-for-firewall-rules"></a>Wzorce adresów URL dla reguł zapory

Administratorzy sieci często można skonfigurować reguły zapory zaawansowane, na podstawie wzorców adresów URL, do filtrowania przychodzącego i wychodzącego ruchu. Urządzenie StorSimple i usługi Menedżer urządzeń StorSimple są zależne od innych aplikacji firmy Microsoft, takich jak usługi Azure Service Bus, Azure Active Directory Access Control, kont magazynu i serwerami usługi Microsoft Update. Wzorce adresów URL skojarzone z tymi aplikacjami umożliwia konfigurowanie reguł zapory. Jest ważne dowiedzieć się, że wzorce adresów URL skojarzone z tymi aplikacjami można zmienić. Wymaga to co z kolei administrator sieci monitorować i aktualizować reguły zapory dla usługi StorSimple, jak i potrzebny.

Firma Microsoft zaleca ustawienie reguły zapory dla ruchu wychodzącego, w oparciu o usługi StorSimple, stałe adresy IP, liberally, w większości przypadków. Jednak można użyć poniższych informacji do ustawiania reguły zapory zaawansowanych, które są potrzebne do tworzenia bezpiecznych środowisk.

> [!NOTE]
> Urządzenia (źródło) adresy IP powinien zawsze ustawiony na wszystkich interfejsach sieciowych włączone. Miejsce docelowe adresy IP powinien być ustawiony na [zakresy IP centrów danych platformy Azure](https://www.microsoft.com/en-us/download/confirmation.aspx?id=41653).


#### <a name="url-patterns-for-azure-portal"></a>Wzorce adresów URL dla witryny Azure portal

| Wzorzec URL | Składnik/funkcji | Adresy IP urządzeń |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*`<br>`https://login.windows.net` |Usługa Menedżer urządzeń StorSimple<br>Usługa kontroli dostępu<br>Azure Service Bus<br>Usługa uwierzytelniania |Interfejsy sieciowe z obsługą chmury |
| `https://*.backup.windowsazure.com` |Rejestracja urządzenia |Tylko dane 0 |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Odwoływanie certyfikatów |Interfejsy sieciowe z obsługą chmury |
| `https://*.core.windows.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Konta usługi Azure storage i monitorowania |Interfejsy sieciowe z obsługą chmury |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Serwerami usługi Microsoft Update<br> |Tylko stałe adresy IP kontrolera |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |Tylko stałe adresy IP kontrolera |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |Pakiet dla pomocy technicznej |Interfejsy sieciowe z obsługą chmury |

#### <a name="url-patterns-for-azure-government-portal"></a>Wzorce adresów URL dla portalu Azure dla instytucji rządowych

| Wzorzec URL | Składnik/funkcji | Adresy IP urządzeń |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.us/*`<br>`https://*.accesscontrol.usgovcloudapi.net/*`<br>`https://*.servicebus.usgovcloudapi.net/*`<br>`https://login.microsoftonline.us` |Usługa Menedżer urządzeń StorSimple<br>Usługa kontroli dostępu<br>Azure Service Bus<br>Usługa uwierzytelniania |Interfejsy sieciowe z obsługą chmury |
| `https://*.backup.windowsazure.us` |Rejestracja urządzenia |Tylko dane 0 |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Odwoływanie certyfikatów |Interfejsy sieciowe z obsługą chmury |
| `https://*.core.usgovcloudapi.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Konta usługi Azure storage i monitorowania |Interfejsy sieciowe z obsługą chmury |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Serwerami usługi Microsoft Update<br> |Tylko stałe adresy IP kontrolera |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |Tylko stałe adresy IP kontrolera |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |Pakiet dla pomocy technicznej |Interfejsy sieciowe z obsługą chmury |

### <a name="routing-metric"></a>Metryki routingu

Metryki routingu jest skojarzony z interfejsów i bramy, która kierować dane do określonej sieci. Metryki routingu jest używane przez protokół routingu do obliczania najlepszą ścieżkę do zadanego miejsca docelowego, jeśli dowie się, że istnieje wiele ścieżek, do tego samego miejsca docelowego. Dolna metryki routingu, tym wyższy priorytet.

W kontekście usługi StorSimple Jeśli wiele interfejsów sieciowych i bram, które są skonfigurowane do ruchu w kanale routingu metryki wejdzie w play, aby określić względną kolejność, w którym używane Pobierz interfejsy. Nie można zmienić metryki routingu przez użytkownika. Można jednak użyć `Get-HcsRoutingTable` polecenia cmdlet, aby wydrukować tabelę routingu (i metryki) na urządzeniu StorSimple. Więcej informacji na temat polecenia cmdlet Get-HcsRoutingTable, w [StorSimple Rozwiązywanie problemów z wdrożenia](storsimple-troubleshoot-deployment.md).

Metryki algorytm routingu, używany do aktualizacji 2 lub nowszym można wyjaśnić w następujący sposób.

* Zestaw wstępnie zdefiniowanych wartości zostały przypisane do interfejsów sieciowych.
* Należy wziąć pod uwagę Przykładowa tabela poniżej wartości przypisane do różnych interfejsów sieciowych, gdy są one chmury — włączone lub wyłączone chmury, ale za pomocą skonfigurowanej bramy. Należy pamiętać, że wartości przypisane w tym miejscu są przykładowe wartości.

    | Interfejs sieciowy | Z obsługą chmury | Chmura — wyłączone za pomocą bramy |
    |-----|---------------|---------------------------|
    | Interfejs Data 0  | 1            | -                        |
    | Dane 1  | 2            | 20                       |
    | Dane 2  | 3            | 30                       |
    | Dane 3  | 4            | 40                       |
    | Dane 4  | 5            | 50                       |
    | Dane 5  | 6            | 60                       |


* Kolejność, w którym ruchu w chmurze będą kierowane za pośrednictwem interfejsów sieciowych jest:
  
    *Interfejs Data 0 > dane 1 > Data 2 > Data 3 > 4 danych > dane 5*
  
    Można to wyjaśnione w poniższym przykładzie.
  
    Należy wziąć pod uwagę urządzenia StorSimple przy użyciu dwa interfejsy sieciowe z obsługą chmury, interfejsu dane 0 do dane 5. Dane od 1 do 4 danych są wyłączone chmury, ale mają skonfigurowanej bramy. Kolejność, w którym ruch będzie kierowany dla tego urządzenia będzie:
  
    *Interfejs Data 0 (1) > dane 5 (6) > dane 1 (20) > dane 2 (30) > Data 3 (40) > Data 4 (50)*
  
    *Liczby w nawiasach wskazują odpowiednich metryk routingu.*
  
    Jeśli interfejs Data 0 nie powiedzie się, ruchu w chmurze będą Pobierz kierowane do dane 5. Biorąc pod uwagę, że brama jest skonfigurowana na innych sieci, gdyby zarówno interfejs Data 0, jak i dane 5 nie powiedzie się, za pośrednictwem 1 dane zaczną się ruchu w chmurze.
* W przypadku niepowodzenia interfejsu sieciowego z obsługą chmury są następnie 3 ponownych prób przy użyciu 30 półsekundowym opóźnieniu połączyć się z interfejsu. W przypadku awarii wszystkich ponownych prób, ruch jest kierowany do następny dostępny włączoną obsługę chmury interfejsu zgodnie z ustaleniami tabeli routingu. Jeśli włączoną obsługę chmury sieci interfejsy kończyć się niepowodzeniem, urządzenie będzie Failover się do innego kontrolera (bez ponownego rozruchu w tym przypadku).
* W przypadku awarii adresów VIP dla interfejsu sieci iSCSI, nastąpi 3 ponowne próby z opóźnieniem 2 sekundy. To zachowanie ma zostali takie same, z poprzednich wersji. Jeśli nie wszystkie interfejsy sieciowe iSCSI, praca awaryjna kontrolera zostanie przeprowadzona (wraz z ponownego uruchomienia).
* Alert jest zgłaszany w urządzeniu StorSimple również, gdy wystąpi awaria adresów VIP. Aby uzyskać więcej informacji, przejdź do [alert krótki](storsimple-8000-manage-alerts.md).
* Pod względem ponownych prób iSCSI ma pierwszeństwo przed chmury.
  
    Rozważmy następujący przykład: StorSimple, urządzenie ma dwa interfejsy sieciowe, włączone, interfejs Data 0 i 1 danych. Interfejs Data 0 ma włączoną obsługę chmury 1 danych jest zarówno w chmurze i włączono interfejs iSCSI. Brak interfejsów sieciowych na tym urządzeniu są włączone dla chmury lub iSCSI.
  
    Jeśli Data 1 zakończy się niepowodzeniem, biorąc pod uwagę jej ostatniego interfejsu sieci iSCSI, spowoduje praca awaryjna kontrolera 1 danych na innym kontrolerze.

### <a name="networking-best-practices"></a>Najlepsze rozwiązania w sieci

Oprócz powyższych wymagań sieciowych, aby uzyskać optymalną wydajność rozwiązania StorSimple można stosować następujące najlepsze rozwiązania:

* Upewnij się, że urządzenia StorSimple ma dedykowanej przepustowości 40 MB/s (lub więcej) dostępnych przez cały czas. Nie należy udostępniać tego przepustowości (lub alokacji, należy zagwarantować przy użyciu zasad QoS) z innymi aplikacjami.
* Upewnij się, że połączenie sieciowe z Internetem jest dostępny przez cały czas. Sporadyczne i nieprzewidywalne połączeń internetowych na urządzeniach, w tym bez połączenia internetowego, wynikiem będzie nieobsługiwaną konfigurację.
* Izolowanie ruchu iSCSI i chmury przez używania dedykowanych interfejsów sieciowych na twoim urządzeniu dostępu iSCSI i chmury. Aby uzyskać więcej informacji, zobacz instrukcje [zmodyfikować interfejsów sieciowych](storsimple-8000-modify-device-config.md#modify-network-interfaces) na urządzeniu StorSimple.
* Nie używaj konfiguracji protokołu Link Aggregation Control Protocol (LACP) interfejsów sieciowych. Jest to nieobsługiwaną konfigurację.

## <a name="high-availability-requirements-for-storsimple"></a>Wymaganiami wysokiej dostępności dla usługi StorSimple

Platformie sprzętowej, która jest dołączana do rozwiązania StorSimple ma funkcje dostępności i niezawodności, które stanowią podstawę utworzenie na wysoko dostępnej i odpornej na uszkodzenia infrastruktury magazynu w centrum danych. Jednakże istnieją wymagania i najlepsze rozwiązania, które muszą być zgodne z do zapewnienia dostępności rozwiązania StorSimple. Przed wdrożeniem usługi StorSimple uważnie przeczytaj następujące wymagania i najlepsze rozwiązania dotyczące komputerów połączonych hostów i urządzeń StorSimple.

Aby uzyskać więcej informacji dotyczących monitorowania i utrzymywania składniki sprzętowe w urządzeniu StorSimple, przejdź do [korzystania z usługi Menedżer urządzeń StorSimple, składników sprzętowych monitora i stanu](storsimple-8000-monitor-hardware-status.md) i [StorSimple wymiana składników sprzętowych](storsimple-8000-hardware-component-replacement.md).

### <a name="high-availability-requirements-and-procedures-for-your-storsimple-device"></a>Wymaganiami wysokiej dostępności i procedury dotyczące urządzenia StorSimple

Przejrzyj następujące informacje, aby zapewnić wysoką dostępność urządzenia StorSimple.

#### <a name="pcms"></a>PCMs

Urządzenia StorSimple obejmują, nadmiarowe, wyłączania zasilania i chłodzenia modułów (PCMs). Każdy PCM ma za małą pojemność, aby zapewnić obsługę dla całej obudowy. Aby zapewnić wysoką dostępność, należy zainstalować zarówno PCMs.

* Połącz swoje PCMs z różnymi źródłami zasilania w celu zapewnienia dostępności, jeśli źródło zasilania nie powiedzie się.
* W przypadku niepowodzenia PCM, żądania mogą zastąpić natychmiast.
* Usuń zakończone niepowodzeniem PCM, tylko wtedy, gdy masz zastąpienia i są gotowe do zainstalowania go.
* Nie usuwaj PCMs obu jednocześnie. W module PCM obejmuje moduł baterii zapasowej. Usunięcie zarówno PCMs spowoduje zamknięcie bez ochrony baterii i stan urządzenia nie zostaną zapisane. Aby uzyskać więcej informacji na temat baterii, przejdź do [Obsługa modułu baterii zapasowej](storsimple-8000-battery-replacement.md#maintain-the-backup-battery-module).

#### <a name="controller-modules"></a>Moduły kontrolera

Urządzenia StorSimple obejmują moduły nadmiarowe, wyłączania kontrolera. Moduły kontrolera działają w sposób aktywny/pasywny. W dowolnym momencie jeden moduł kontrolera jest aktywny i udostępnia usługi, podczas gdy inny moduł kontroler jest w stanie pasywnym. Moduł kontroler pasywny zostanie włączone i staje się operacyjnej, jeśli moduł aktywny kontroler ulegnie awarii lub zostanie usunięta. Każdy moduł kontroler ma za małą pojemność, aby zapewnić obsługę dla całej obudowy. Oba moduły kontrolera muszą być zainstalowane, aby zapewnić wysoką dostępność.

* Upewnij się, że obu modułów kontrolera są instalowane przez cały czas.
* Jeśli moduł kontrolera nie powiedzie się, żądania mogą zastąpić natychmiast.
* Usuń moduł kontrolera nie powiodło się tylko wtedy, gdy masz zastąpienia i są gotowe do zainstalowania go. Usuwanie modułu dla dłuższy czas będzie wpływać na przepływ powietrza i dlatego chłodzenia systemu.
* Upewnij się, że połączenia sieciowe w obu modułów kontrolera są identyczne i interfejsów sieciowych podłączonych ma konfiguracji sieciowej identyczne.
* Jeśli moduł kontrolera ulegnie awarii lub konieczność wymiany, upewnij się, że moduł kontroler jest w stanie aktywnym przed zastąpieniem moduł kontrolera nie powiodło się. Aby sprawdzić, czy kontroler jest aktywny, przejdź do [identyfikowanie aktywnego kontrolera na urządzeniu z systemem](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
* Nie usuwaj obu modułów kontrolera w tym samym czasie. W przypadku przejścia w tryb failover kontrolera w toku, wyłączony moduł kontroler zapasowy lub nie usunąć go z obudowy.
* Po włączeniu trybu failover kontrolera Poczekaj co najmniej pięć minut przed usunięciem albo moduł kontrolera.

#### <a name="network-interfaces"></a>Interfejsy sieciowe

StorSimple urządzenia kontrolera moduły każdego mają cztery 1 GB lub 10 dwa interfejsy sieci Gigabit Ethernet.

* Upewnij się, że są identyczne, połączeń sieciowych do obu modułów kontrolera i interfejsów sieci, czy interfejsy modułów kontrolera są podłączone do sieci identycznych konfiguracji.
* Jeśli to możliwe, można wdrożyć połączenia sieciowe różnych przełączników, aby zapewnić dostępność usługi awarii urządzeń sieciowych.
* W przypadku odłączania tylko lub ostatnim pozostałe interfejs włączono interfejs iSCSI (z adresy IP przypisane), najpierw wyłącz interfejs, a następnie odłącz kable. Jeśli interfejs jest odłączony najpierw, a następnie go spowoduje, że aktywnego kontrolera do trybu failover kontrolera pasywnego. Jeśli kontrolera pasywnego ma również odpowiedni odłączone interfejsy, oba kontrolery zostanie uruchomiony wielokrotnie przed rozpoczęciem na jednym kontrolerze.
* Połączenie z siecią co najmniej dwa interfejsy danych z każdego modułu kontrolera.
* Po włączeniu dwóch interfejsów 10 GbE, wdrożyć w różnych przełączników.
* Jeśli to możliwe, użyj wielościeżkowego wejścia/wyjścia na serwerach, aby upewnić się, że serwery tolerują błędy łącza, sieci lub interfejsu.

Aby uzyskać więcej informacji na temat sieci urządzenia pod kątem wysokiej dostępności i wydajności, przejdź do [instalowania urządzenia StorSimple 8100](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) lub [instalowania urządzenia StorSimple 8600](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

#### <a name="ssds-and-hdds"></a>Dyski SSD i HDD w celu

Urządzenia StorSimple obejmują dyskach półprzewodnikowych (SSD) i dysków twardych (HDD), które są chronione przy użyciu dublowanych miejsc do magazynowania. Użyj dublowanych miejsc do magazynowania gwarantuje, że urządzenie jest w stanie tolerowanie awarii jednego lub więcej dysków SSD lub HDD.

* Upewnij się, że wszystkie dyski SSD i HDD moduły są zainstalowane.
* W przypadku niepowodzenia SSD lub HDD żądania zamiennika natychmiast.
* Jeśli dysk SSD lub dysk twardy nie powiedzie się lub wymagana jest wymiana, upewnij się, można usunąć tylko dyski SSD lub dysk twardy, który wymaga zastąpienia.
* Nie usuwaj więcej niż jednego dysku SSD lub dysk twardy z systemu w dowolnym momencie w czasie.
  Błąd co najmniej 2 dyski określonego typu (dysk twardy, dysk SSD) lub kolejne niepowodzenia w krótkim okresie może spowodować system usterek i ryzyko utraty danych. W takim przypadku [skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md) uzyskać pomoc.
* Podczas zastąpienia, należy monitorować **udostępnione składniki** w **kondycja sprzętu** bloku dla dysków SSD i HDD. Stan zielony znacznik wyboru wskazuje, czy dyski są w dobrej kondycji lub OK, punkt czerwony wykrzyknik wskazuje, zakończone niepowodzeniem SSD lub HDD.
* Firma Microsoft zaleca skonfigurowanie migawki w chmurze dla wszystkich woluminów, które należy chronić w razie awarii systemu.

#### <a name="ebod-enclosure"></a>Obudowa EBOD

Model urządzenia StorSimple 8600 obejmuje obudowy rozszerzone wiele z dysków (EBOD) oprócz podstawowego obudowy. EBOD zawiera EBOD kontrolery i dysków twardych (HDD), które są chronione przy użyciu dublowanych miejsc do magazynowania. Użyj dublowanych miejsc do magazynowania gwarantuje, że urządzenie jest w stanie tolerowanie awarii jednego lub więcej dysków twardych. Obudowa EBOD jest podłączony do głównej obudowy za pomocą nadmiarowych kabli SAS.

* Upewnij się, że oba modułów kontrolera EBOD obudowy zarówno kabli SAS, tak, jak i wszystkie stacje dysków twardych zainstalowanych przez cały czas.
* Jeśli moduł kontrolera EBOD obudowy zakończy się niepowodzeniem, żądania mogą zastąpić natychmiast.
* Jeśli moduł kontrolera EBOD obudowy nie powiedzie się, upewnij się, czy moduł kontrolera jest aktywny, zanim zastąpienie modułu nie powiodło się. Aby sprawdzić, czy kontroler jest aktywny, przejdź do [identyfikowanie aktywnego kontrolera na urządzeniu z systemem](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
* Podczas zastępowania modułu kontrolera EBOD na stałe monitorowanie stanu składnika w usłudze Menedżer urządzeń StorSimple, uzyskując dostęp do **Monitor** > **kondycja sprzętu**.
* Jeśli kabla sygnatury dostępu Współdzielonego nie powiedzie się lub wymagana jest wymiana (Microsoft Support powinny uczestniczyć dokonanie takiego określenia), upewnij się, usunięcie kabel sygnatury dostępu Współdzielonego, który wymaga zastąpienia.
* Nie należy jednocześnie usuwać zarówno kabli SAS z systemu w dowolnym momencie w czasie.

### <a name="high-availability-recommendations-for-your-host-computers"></a>Zaleceń dotyczących wysokiej dostępności dla komputerów hosta

Uważnie przeczytaj te najlepsze rozwiązania zapewniające wysoką dostępność hostów podłączonych do urządzenia StorSimple.

* Konfigurowanie usługi StorSimple przy użyciu [konfiguracje klastra serwera plików składający się z dwóch węzłów][1]. Usuwając pojedynczych punktów awarii i tworzenie na platformie nadmiarowości po stronie hosta, całe rozwiązanie staje się o wysokiej dostępności.
* Na użytek stale dostępnych udziałów (CA) w systemie Windows Server 2012 (protokół SMB 3.0) o wysokiej dostępności podczas pracy w trybie failover kontrolerów magazynu. Aby uzyskać dodatkowe informacje dotyczące konfigurowania klastrów serwera plików i stale dostępnych udziałów w systemie Windows Server 2012, zapoznaj się z tym [pokaz wideo](https://channel9.msdn.com/Events/IT-Camps/IT-Camps-On-Demand-Windows-Server-2012/DEMO-Continuously-Available-File-Shares).

## <a name="next-steps"></a>Kolejne kroki

* [Więcej informacji na temat ograniczeń systemowych StorSimple](storsimple-8000-limits.md).
* [Dowiedz się, jak wdrażać rozwiązanie StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

<!--Reference links-->
[1]: https://technet.microsoft.com/library/cc731844(v=WS.10).aspx
