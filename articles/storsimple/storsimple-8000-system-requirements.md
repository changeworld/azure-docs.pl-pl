---
title: StorSimple seria 8000 wymagania systemowe | Dokumenty firmy Microsoft
description: W tym artykule opisano wymagania dotyczące oprogramowania, sieci i wysokiej dostępności oraz najlepsze rozwiązania dotyczące rozwiązania Microsoft Azure StorSimple.
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
ms.openlocfilehash: 3032585c6f0a5cc6143eee06b12b6def50cd7cd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297705"
---
# <a name="storsimple-8000-series-software-high-availability-and-networking-requirements"></a>Oprogramowanie StorSimple serii 8000, wysoka dostępność i wymagania sieciowe

## <a name="overview"></a>Omówienie

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

Witamy w witrynie Microsoft Azure StorSimple. W tym artykule opisano ważne wymagania systemowe i najlepsze rozwiązania dla urządzenia StorSimple i dla klientów magazynu uzyskujących dostęp do urządzenia. Zaleca się dokładne przejrzenie informacji przed wdrożeniem systemu StorSimple, a następnie odwołać się do niego w razie potrzeby podczas wdrażania i późniejszej operacji.

Wymagania systemowe obejmują:

* **Wymagania dotyczące oprogramowania dla klientów pamięci masowej** - opisuje obsługiwane systemy operacyjne i wszelkie dodatkowe wymagania dla tych systemów operacyjnych.
* **Wymagania dotyczące sieci dla urządzenia StorSimple** — zawiera informacje o portach, które muszą być otwarte w zaporze, aby umożliwić iSCSI, chmura lub ruch zarządzania.
* **Wysokie wymagania dotyczące dostępności storsimple** — opisuje wysokie wymagania dotyczące dostępności i najlepsze rozwiązania dla urządzenia StorSimple i komputera hosta.

## <a name="software-requirements-for-storage-clients"></a>Wymagania dotyczące oprogramowania dla klientów pamięci masowej

Następujące wymagania dotyczące oprogramowania są dla klientów magazynu, którzy uzyskują dostęp do urządzenia StorSimple.

| Obsługiwane systemy operacyjne | Wymagana wersja | Dodatkowe wymagania/uwagi |
| --- | --- | --- |
| Windows Server |2008 R2 SP1, 2012, 2012 R2, 2016 |Woluminy iSCSI storsimple są obsługiwane do użytku tylko w następujących typach dysków systemu Windows:<ul><li>Wolumin prosty na dysku podstawowym</li><li>Wolumin prosty i dublowany na dysku dynamicznym</li></ul>Obsługiwane są tylko inicjatory iSCSI oprogramowania obecne natywnie w systemie operacyjnym. Inicjatory iSCSI sprzętu nie są obsługiwane.<br></br>Funkcje obsługi administracyjnej w systemach Windows Server 2012 i 2016 oraz funkcje ODX są obsługiwane w przypadku korzystania z woluminu iSCSI StorSimple.<br><br>StorSimple można utworzyć woluminy cienko aprowizacji i w pełni aprowizowana. Nie można utworzyć woluminów aprowizowanych częściowo.<br><br>Formatowanie woluminu z cienko aprowizacji może zająć dużo czasu. Zaleca się usunięcie woluminu, a następnie utworzenie nowego zamiast formatowania. Jeśli jednak nadal wolisz sformatować wolumin:<ul><li>Uruchom następujące polecenie przed sformatem, aby uniknąć opóźnień rekultywacji miejsca: <br>`fsutil behavior set disabledeletenotify 1`</br></li><li>Po zakończeniu formatowania użyj następującego polecenia, aby ponownie włączyć rekultywację miejsca:<br>`fsutil behavior set disabledeletenotify 0`</br></li><li>Zastosuj poprawkę systemu Windows Server 2012 zgodnie z opisem w [kb 2878635](https://support.microsoft.com/kb/2870270) do komputera z systemem Windows Server.</li></ul></li></ul></ul> Jeśli konfigurujesz StorSimple Snapshot Manager lub StorSimple Adapter dla programu SharePoint, przejdź do [wymagań dotyczących oprogramowania dla składników opcjonalnych](#software-requirements-for-optional-components). |
| VMware ESX |5,5 i 6,0 |Obsługiwane z VMware vSphere jako klient iSCSI. Funkcja vaai-block jest obsługiwana przez VMware vSphere na urządzeniach StorSimple. |
| Linux RHEL/CentOS |5, 6 i 7 |Obsługa klientów iSCSI systemu Linux z inicjatorem open-iSCSI w wersjach 5, 6 i 7. |
| Linux |SUSE Linux 11 | |

> [!NOTE]
> Ibm AIX nie jest obecnie obsługiwany przez StorSimple.


## <a name="software-requirements-for-optional-components"></a>Wymagania dotyczące oprogramowania dla komponentów opcjonalnych

Następujące wymagania dotyczące oprogramowania są dla opcjonalnych składników StorSimple (StorSimple Snapshot Manager i StorSimple Adapter for SharePoint).

| Składnik | Platforma hosta | Dodatkowe wymagania/uwagi |
| --- | --- | --- |
| StorSimple Snapshot Manager |Windows Server 2008 R2 SP1, 2012, 2012 R2 |Użycie Menedżera migawek StorSimple w systemie Windows Server jest wymagane do tworzenia kopii zapasowych/przywracania dublowanych dysków dynamicznych oraz do tworzenia kopii zapasowych spójnych z aplikacjami.<br> Menedżer migawek StorSimple jest obsługiwany tylko w systemach Windows Server 2008 R2 z sp1 (64-bitowych), Windows Server 2012 R2 i Windows Server 2012.<ul><li>Jeśli używasz programu Window Server 2012, należy zainstalować program .NET 3.5–4.5 przed zainstalowaniem Menedżera migawek StorSimple.</li><li>Jeśli używasz dodatku SP1 dla systemu Windows Server 2008 R2, przed zainstalowaniem Menedżera migawek storsimple należy zainstalować program Windows Management Framework 3.0.</li></ul> |
| Adapter usługi StorSimple dla programu SharePoint |Windows Server 2008 R2 SP1, 2012, 2012 R2 |<ul><li>Karta StorSimple dla programu SharePoint jest obsługiwana tylko w programach SharePoint 2010 i SharePoint 2013.</li><li>SPZ wymaga programu SQL Server Enterprise Edition, wersja 2008 R2 lub 2012.</li></ul> |

## <a name="networking-requirements-for-your-storsimple-device"></a>Wymagania sieciowe dotyczące urządzenia StorSimple

Urządzenie StorSimple jest urządzeniem zablokowanym. Jednak porty muszą być otwierane w zaporze, aby umożliwić iSCSI, chmura i ruch zarządzania. W poniższej tabeli wymieniono porty, które należy otworzyć w zaporze. W tej tabeli *in* lub *inbound* odnosi się do kierunku, z którego przychodzące żądania klienta dostęp do urządzenia. *Wyjście* lub *wychodzące* odnosi się do kierunku, w którym urządzenie StorSimple wysyła dane zewnętrznie, poza wdrożeniem: na przykład wychodzących do Internetu.

| Port nr<sup>1,2</sup> | Wył. | Zakres portu | Wymagany | Uwagi |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP)<sup>3</sup> |Out |Sieć WAN |Nie |<ul><li>Port wychodzący jest używany do pobierania aktualizacji przez dostęp do Internetu.</li><li>Wychodzący serwer proxy sieci Web jest konfigurowany przez użytkownika.</li><li>Aby zezwolić na aktualizacje systemu, ten port musi być również otwarty dla kontrolera stałych usług IP.</li></ul> |
| TCP 443 (HTTPS)<sup>3</sup> |Out |Sieć WAN |Tak |<ul><li>Port wychodzący służy do uzyskiwania dostępu do danych w chmurze.</li><li>Wychodzący serwer proxy sieci Web jest konfigurowany przez użytkownika.</li><li>Aby zezwolić na aktualizacje systemu, ten port musi być również otwarty dla kontrolera stałych usług IP.</li><li>Ten port jest również używany na obu kontrolerach do wyrzucania elementów bezużytecznych.</li></ul> |
| UDP 53 (DNS) |Out |Sieć WAN |W niektórych przypadkach; patrz notatki. |Ten port jest wymagany tylko w przypadku korzystania z internetowego serwera DNS. |
| UDP 123 (NTP) |Out |Sieć WAN |W niektórych przypadkach; patrz notatki. |Ten port jest wymagany tylko w przypadku korzystania z internetowego serwera NTP. |
| TCP 9354 |Out |Sieć WAN |Tak |Port wychodzący jest używany przez urządzenie StorSimple do komunikowania się z usługą StorSimple Device Manager. |
| 3260 (iSCSI) |W |Sieć LAN |Nie |Ten port jest używany do uzyskiwania dostępu do danych za jego dostęp za 0SCSI. |
| 5985 |W |Sieć LAN |Nie |Port przychodzący jest używany przez StorSimple Snapshot Manager do komunikowania się z urządzeniem StorSimple.<br>Ten port jest również używany podczas zdalnego łączenia się z programem Windows PowerShell dla StorSimple za pośrednictwem protokołu HTTP. |
| 5986 |W |Sieć LAN |Nie |Ten port jest używany podczas zdalnego łączenia się z programem Windows PowerShell dla storproste za pośrednictwem protokołu HTTPS. |

<sup>1</sup> W publicznym Internecie nie trzeba otwierać portów przychodzących.

<sup>2</sup> Jeśli konfiguracja bramy ma wiele portów, kolejność kierowanego ruchu wychodzącego zostanie określona na podstawie kolejności routingu portów opisanej w [poniżej w poniżej.](#routing-metric)

<sup>3</sup> Kontroler stałych adresów IP na urządzeniu StorSimple musi być rutowalny i może łączyć się z Internetem bezpośrednio lub za pośrednictwem skonfigurowanego serwera proxy sieci Web. Stałe adresy IP są używane do obsługi aktualizacji urządzenia i wyrzucania elementów bezużytecznych. Jeśli kontrolery urządzeń nie mogą połączyć się z Internetem za pośrednictwem stałych adresów IP, nie będzie można zaktualizować urządzenia StorSimple, a wyrzucanie elementów bezużytecznych nie będzie działać poprawnie.

> [!IMPORTANT]
> Upewnij się, że zapora nie modyfikuje ani nie odszyfrowuje żadnego ruchu TLS między urządzeniem StorSimple a platformą Azure.


### <a name="url-patterns-for-firewall-rules"></a>Wzorce adresów URL dla reguł zapory

Administratorzy sieci często mogą konfigurować zaawansowane reguły zapory na podstawie wzorców adresów URL w celu filtrowania ruchu przychodzącego i wychodzącego. Urządzenie StorSimple i usługa StorSimple Device Manager zależą od innych aplikacji firmy Microsoft, takich jak usługa Azure Service Bus, kontrola dostępu usługi Azure Active Directory, konta magazynu i serwery usługi Microsoft Update. Wzorce adresów URL skojarzone z tymi aplikacjami mogą służyć do konfigurowania reguł zapory. Ważne jest, aby zrozumieć, że wzorce adresów URL skojarzone z tymi aplikacjami mogą ulec zmianie. To z kolei będzie wymagać administrator sieci do monitorowania i aktualizacji reguł zapory dla StorSimple w razie potrzeby.

Zaleca się ustawienie reguł zapory dla ruchu wychodzącego, na podstawie storsimple stałych adresów IP, obficie w większości przypadków. Można jednak użyć poniższych informacji, aby ustawić zaawansowane reguły zapory, które są potrzebne do tworzenia bezpiecznych środowisk.

> [!NOTE]
> Interfejsy IP urządzenia (źródła) powinny być zawsze ustawione na wszystkie włączone interfejsy sieciowe. Docelowe adresy IP powinny być ustawione na [zakresy adresów IP centrum danych platformy Azure](https://www.microsoft.com/en-us/download/confirmation.aspx?id=41653).


#### <a name="url-patterns-for-azure-portal"></a>Wzorce adresów URL dla witryny Azure portal

| Wzorzec adresu URL | Komponent/funkcjonalność | Ustawienia IP urządzenia |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*`<br>`https://login.windows.net` |Usługa Menedżer urządzeń StorSimple<br>Usługa kontroli dostępu<br>Azure Service Bus<br>Usługa uwierzytelniania |Interfejsy sieciowe z obsługą chmury |
| `https://*.backup.windowsazure.com` |Rejestracja urządzenia |Tylko dane 0 |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Odwołanie certyfikatu |Interfejsy sieciowe z obsługą chmury |
| `https://*.core.windows.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Konta magazynu platformy Azure i monitorowanie |Interfejsy sieciowe z obsługą chmury |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Serwery usługi Microsoft Update<br> |Tylko stałe ip kontrolera |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |Tylko stałe ip kontrolera |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |Pakiet pomocy technicznej |Interfejsy sieciowe z obsługą chmury |

#### <a name="url-patterns-for-azure-government-portal"></a>Wzorce adresów URL dla portalu azure dla instytucji rządowych

| Wzorzec adresu URL | Komponent/funkcjonalność | Ustawienia IP urządzenia |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.us/*`<br>`https://*.accesscontrol.usgovcloudapi.net/*`<br>`https://*.servicebus.usgovcloudapi.net/*`<br>`https://login.microsoftonline.us` |Usługa Menedżer urządzeń StorSimple<br>Usługa kontroli dostępu<br>Azure Service Bus<br>Usługa uwierzytelniania |Interfejsy sieciowe z obsługą chmury |
| `https://*.backup.windowsazure.us` |Rejestracja urządzenia |Tylko dane 0 |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Odwołanie certyfikatu |Interfejsy sieciowe z obsługą chmury |
| `https://*.core.usgovcloudapi.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Konta magazynu platformy Azure i monitorowanie |Interfejsy sieciowe z obsługą chmury |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Serwery usługi Microsoft Update<br> |Tylko stałe ip kontrolera |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |Tylko stałe ip kontrolera |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |Pakiet pomocy technicznej |Interfejsy sieciowe z obsługą chmury |

### <a name="routing-metric"></a>Metryka routingu

Metryka routingu jest skojarzona z interfejsami i bramą, która kieruje dane do określonych sieci. Metryka routingu jest używana przez protokół routingu do obliczania najlepszej ścieżki do danego miejsca docelowego, jeśli dowie się, że istnieje wiele ścieżek do tego samego miejsca docelowego. Im niższa metryka routingu, tym wyższe preferencje.

W kontekście StorSimple, jeśli wiele interfejsów sieciowych i bram są skonfigurowane do kanału ruchu, metryki routingu wejdzie w grę w celu określenia względnej kolejności, w jakiej interfejsy będą używane. Metryki routingu nie mogą być zmieniane przez użytkownika. Można jednak użyć `Get-HcsRoutingTable` polecenia cmdlet, aby wydrukować tabelę routingu (i metryki) na urządzeniu StorSimple. Więcej informacji na temat polecenia cmdlet Get-HcsRoutingTable w [rozwiązywaniu problemów z wdrożeniem StorSimple](storsimple-troubleshoot-deployment.md).

Algorytm metryki routingu używany dla aktualizacji 2 i nowszych wersji można wyjaśnić w następujący sposób.

* Zestaw wstępnie określonych wartości zostały przypisane do interfejsów sieciowych.
* Należy wziąć pod uwagę przykładową tabelę pokazaną poniżej z wartościami przypisanymi do różnych interfejsów sieciowych, gdy są one włączone w chmurze lub wyłączone w chmurze, ale ze skonfigurowaną bramą. Należy zauważyć, że wartości przypisane w tym miejscu są tylko przykładowe wartości.

    | Interfejs sieciowy | Z obsługą chmury | Wyłączona w chmurze z bramą |
    |-----|---------------|---------------------------|
    | Dane 0  | 1            | -                        |
    | Dane 1  | 2            | 20                       |
    | Dane 2  | 3            | 30                       |
    | Dane 3  | 4            | 40                       |
    | Dane 4  | 5            | 50                       |
    | Dane 5  | 6            | 60                       |


* Kolejność, w jakiej ruch w chmurze będzie kierowany za pośrednictwem interfejsów sieciowych jest:
  
    *Dane 0 > Dane 1 > Data 2 > Dane 3 dane > 4 > Dane 5*
  
    Można to wyjaśnić w poniższym przykładzie.
  
    Należy wziąć pod uwagę urządzenie StorSimple z dwoma interfejsami sieciowymi obsługującymi chmurę, Dane 0 i Dane 5. Dane od 1 do 4 są wyłączone w chmurze, ale mają skonfigurowaną bramę. Kolejność, w jakiej ruch będzie kierowany dla tego urządzenia, będzie:
  
    *Dane 0 (1) > Dane 5 (6) > Dane 1 (20) > Dane 2 (30) > Dane 3 (40) > Dane 4 (50)*
  
    *Liczby w nawiasach wskazują odpowiednie metryki routingu.*
  
    Jeśli dane 0 nie powiedzie się, ruch w chmurze zostanie przekierowany za pośrednictwem danych 5. Biorąc pod uwagę, że brama jest skonfigurowana w całej innej sieci, jeśli zarówno dane 0, jak i dane 5 uległy awarii, ruch w chmurze przejdzie przez dane 1.
* Jeśli interfejs sieciowy z obsługą chmury ulegnie awarii, zostaną 3 ponownych prób z 30-sekundowym opóźnieniem, aby połączyć się z interfejsem. Jeśli wszystkie ponownych prób zakończyć się niepowodzeniem, ruch jest kierowany do następnego dostępnego interfejsu włączonego w chmurze, zgodnie z tabelą routingu. Jeśli wszystkie interfejsy sieciowe z obsługą chmury nie powiodą się, urządzenie przejdzie awaryjnie do innego kontrolera (w tym przypadku nie ma ponownego uruchomienia).
* Jeśli występuje błąd adresu VIP dla interfejsu sieciowego obsługującego interfejs sieciowy z włączoną funkcją iSCSI, nastąpi 3 ponownych prób z opóźnieniem 2 sekund. To zachowanie pozostało takie samo z poprzednich wersji. Jeśli wszystkie interfejsy sieciowe iSCSI uchodzą awaryjnie, nastąpi praca awaryjna kontrolera (wraz z ponownym uruchomieniem).
* Alert jest również wywoływany na urządzeniu StorSimple, gdy występuje awaria adresu VIP. Aby uzyskać więcej informacji, przejdź do [alertu podręczny .](storsimple-8000-manage-alerts.md)
* Pod względem ponownych prób iSCSI będzie miał pierwszeństwo przed chmurą.
  
    Rozważmy następujący przykład: Urządzenie StorSimple ma dwa interfejsy sieciowe włączone, Dane 0 i Dane 1. Dane 0 są włączone w chmurę, podczas gdy dane 1 są zarówno w chmurze, jak i z włączoną funkcją iSCSI. Żadne inne interfejsy sieciowe na tym urządzeniu nie są włączone dla chmury lub iSCSI.
  
    Jeśli dane 1 nie powiedzie się, biorąc pod uwagę, że jest to ostatni interfejs sieciowy iSCSI, spowoduje to, że kontroler przejdzie w stan failover do danych 1 na innym kontrolerze.

### <a name="networking-best-practices"></a>Najważniejsze wskazówki dotyczące sieci kontaktów

Oprócz powyższych wymagań dotyczących sieci, aby uzyskać optymalną wydajność rozwiązania StorSimple, należy przestrzegać następujących najlepszych rozwiązań:

* Upewnij się, że urządzenie StorSimple ma dedykowaną przepustowość 40 Mb/s (lub więcej) dostępną przez cały czas. Ta przepustowość nie powinna być współużytkowana (lub alokacja powinna być gwarantowana za pomocą zasad QoS) z innymi aplikacjami.
* Upewnij się, że łączność sieciowa z Internetem jest zawsze dostępna. Sporadyczne lub zawodne połączenia internetowe z urządzeniami, w tym brak łączności z Internetem, spowoduje nieobsługiwania konfiguracji.
* Izoluj iSCSI i ruch w chmurze, mając dedykowane interfejsy sieciowe na urządzeniu dla iSCSI i dostępu do chmury. Aby uzyskać więcej informacji, zobacz jak [zmodyfikować interfejsy sieciowe](storsimple-8000-modify-device-config.md#modify-network-interfaces) na urządzeniu StorSimple.
* Nie należy używać konfiguracji protokołu LACP (Link Aggregation Control Protocol) dla interfejsów sieciowych. Taka konfiguracja nie jest obsługiwana.

## <a name="high-availability-requirements-for-storsimple"></a>Wysokie wymagania dotyczące dostępności storproste

Platforma sprzętowa dołączona do rozwiązania StorSimple ma funkcje dostępności i niezawodności, które stanowią podstawę dla wysoce dostępnej, odpornej na uszkodzenia infrastruktury pamięci masowej w centrum danych. Istnieją jednak wymagania i najlepsze rozwiązania, które należy spełnić, aby zapewnić dostępność rozwiązania StorSimple. Przed wdrożeniem StorSimple należy dokładnie zapoznać się z następującymi wymaganiami i najlepszymi rozwiązaniami dotyczącymi urządzenia StorSimple i podłączonych komputerów-hostów.

Aby uzyskać więcej informacji na temat monitorowania i konserwacji składników sprzętowych urządzenia StorSimple, przejdź do [witryny Korzystanie z usługi StorSimple Device Manager do monitorowania składników sprzętowych i stanu](storsimple-8000-monitor-hardware-status.md) oraz [zastępowanie składnika sprzętowego StorSimple](storsimple-8000-hardware-component-replacement.md).

### <a name="high-availability-requirements-and-procedures-for-your-storsimple-device"></a>Wysokie wymagania dotyczące dostępności i procedury dotyczące urządzenia StorSimple

Przejrzyj uważnie następujące informacje, aby zapewnić wysoką dostępność urządzenia StorSimple.

#### <a name="pcms"></a>Komputery PCM

Urządzenia StorSimple obejmują nadmiarowe moduły zasilania i chłodzenia (PCM) z możliwością wymiany podczas pracy. Każdy PCM ma wystarczającą pojemność, aby zapewnić obsługę całej obudowy. Aby zapewnić wysoką dostępność, należy zainstalować oba pcm.

* Podłącz komputery PCM do różnych źródeł zasilania, aby zapewnić dostępność w przypadku awarii źródła zasilania.
* Jeśli pcm nie powiedzie się, natychmiast zażądaj wymiany.
* Usuń nieudany pcm tylko wtedy, gdy masz zamiennik i jesteś gotowy do zainstalowania.
* Nie należy usuwać obu pcms jednocześnie. Moduł PCM zawiera moduł baterii zapasowej. Usunięcie obu pcms spowoduje zamknięcie bez ochrony baterii, a stan urządzenia nie zostanie zapisany. Aby uzyskać więcej informacji na temat baterii, przejdź do [strony Obsługa modułu baterii zapasowej](storsimple-8000-battery-replacement.md#maintain-the-backup-battery-module).

#### <a name="controller-modules"></a>Moduły kontrolerów

Urządzenia StorSimple zawierają nadmiarowe moduły kontrolerów wymieniane podczas pracy. Moduły sterowników działają w sposób aktywny/pasywny. W danym momencie jeden moduł kontrolera jest aktywny i świadczy usługi, podczas gdy drugi moduł kontrolera jest pasywny. Moduł kontrolera pasywnego jest włączony i działa, jeśli aktywny moduł kontrolera ulegnie awarii lub zostanie usunięty. Każdy moduł kontrolera ma wystarczającą pojemność, aby zapewnić obsługę całej obudowy. Oba moduły kontrolera muszą być zainstalowane, aby zapewnić wysoką dostępność.

* Upewnij się, że oba moduły kontrolera są zainstalowane przez cały czas.
* Jeśli moduł kontrolera ulegnie awarii, natychmiast zażądaj wymiany.
* Usuń moduł kontrolera, który uległ awarii tylko wtedy, gdy masz zamiennik i jesteś gotowy do jego zainstalowania. Wyjęcie modułu przez dłuższy czas wpłynie na przepływ powietrza, a tym samym na chłodzenie systemu.
* Upewnij się, że połączenia sieciowe z obu modułów kontrolera są identyczne, a podłączone interfejsy sieciowe mają identyczną konfigurację sieci.
* Jeśli moduł kontrolera ulegnie awarii lub wymaga wymiany, upewnij się, że drugi moduł kontrolera jest w stanie aktywnym przed wymianą modułu kontrolera nie powiodło się. Aby sprawdzić, czy kontroler jest aktywny, przejdź do [strony Identyfikowanie aktywnego kontrolera na urządzeniu](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
* Nie należy jednocześnie usuwać obu modułów kontrolera. Jeśli trwa tryb failover kontrolera, nie należy wyłączać modułu kontrolera rezerwowego ani usuwać go z obudowy.
* Po przemyceniu kontrolera należy odczekać co najmniej pięć minut przed usunięciem modułu kontrolera.

#### <a name="network-interfaces"></a>Interfejsy sieciowe

Moduły kontrolera urządzeń StorSimple mają po cztery 1 gigabitowe i dwa 10 gigabitowe interfejsy sieciowe Ethernet.

* Upewnij się, że połączenia sieciowe z obu modułów kontrolera są identyczne i interfejsów sieciowych, które interfejsy modułu kontrolera są podłączone do identycznej konfiguracji sieci.
* Jeśli to możliwe, wdrażaj połączenia sieciowe na różnych przełącznikach, aby zapewnić dostępność usługi w przypadku awarii urządzenia sieciowego.
* Podczas odłączania jedynego lub ostatniego pozostałego interfejsu obsługującego interfejs iSCSI (z przypisanymi plikami IP), należy najpierw wyłączyć interfejs, a następnie odłączyć kable. Jeśli interfejs jest najpierw odłączony, spowoduje to, że aktywny kontroler przejdzie awaryjnie do kontrolera pasywnego. Jeśli kontroler pasywny ma również odpowiednie interfejsy odłączone, oba kontrolery uruchomią się ponownie wiele razy przed osiedleniem się na jednym kontrolerze.
* Podłącz co najmniej dwa interfejsy DANYCH do sieci z każdego modułu kontrolera.
* Jeśli włączono dwa interfejsy 10 GbE, należy wdrożyć je w różnych przełącznikach.
* Jeśli to możliwe, użyj mpio na serwerach, aby upewnić się, że serwery mogą tolerować awarię łącza, sieci lub interfejsu.

Aby uzyskać więcej informacji na temat sieci urządzenia w celu uzyskania wysokiej dostępności i wydajności, przejdź do [witryny Instalowanie urządzenia StorSimple 8100](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) lub [Instalowanie urządzenia StorSimple 8600](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

#### <a name="ssds-and-hdds"></a>Dyski SSD i HDD

Urządzenia StorSimple obejmują dyski PÓŁPRZEWODNIKOWE (SSD) i dyski twarde (HDD), które są chronione za pomocą przestrzeni lustrzanych. Korzystanie z lustrzanych przestrzeni zapewnia, że urządzenie jest w stanie tolerować awarię jednego lub więcej dysków SSD lub hdd.

* Upewnij się, że wszystkie moduły SSD i HDD są zainstalowane.
* Jeśli dysk SSD lub HDD nie powiedzie się, należy natychmiast poprosić o wymianę.
* Jeśli dysk SSD lub HDD ulegnie awarii lub wymaga wymiany, należy usunąć tylko dysk SSD lub HDD, który wymaga wymiany.
* W żadnym momencie nie należy usuwać z systemu więcej niż jednego dysku SSD lub dysku twardego.
  Awaria 2 lub więcej dysków określonego typu (HDD, SSD) lub kolejna awaria w krótkim czasie może spowodować awarię systemu i potencjalną utratę danych. W takim przypadku [skontaktuj się z pomocą techniczną firmy Microsoft](storsimple-8000-contact-microsoft-support.md) w celu uzyskania pomocy.
* Podczas wymiany należy monitorować **składniki udostępnione** w bloku **kondycji sprzętu** dla dysków w dyskach SSD i HDD. Zielony stan sprawdzania wskazuje, że dyski są w dobrej kondycji lub OK, podczas gdy czerwony wykrzyknik wskazuje nie powiodło się SSD lub HDD.
* Zaleca się skonfigurowanie migawek w chmurze dla wszystkich woluminów, które należy chronić w przypadku awarii systemu.

#### <a name="ebod-enclosure"></a>Obudowa EBOD

StorSimple model urządzenia 8600 zawiera rozszerzony bunch of Disks (EBOD) obudowy oprócz obudowy podstawowej. EBOD zawiera kontrolery EBOD i dyski twarde (HDD), które są chronione za pomocą przestrzeni lustrzanych. Korzystanie z lustrzanych przestrzeni zapewnia, że urządzenie jest w stanie tolerować awarię jednego lub więcej dysków twardych. Obudowa EBOD jest podłączona do obudowy podstawowej za pomocą nadmiarowych kabli SAS.

* Upewnij się, że oba moduły kontrolerów obudowy EBOD, zarówno kable SAS, jak i wszystkie dyski twarde są zainstalowane przez cały czas.
* Jeśli moduł kontrolera obudowy EBOD ulegnie awarii, należy natychmiast zażądać wymiany.
* Jeśli moduł kontrolera obudowy EBOD ulegnie awarii, upewnij się, że drugi moduł kontrolera jest aktywny przed wymianą modułu, który uległ awarii. Aby sprawdzić, czy kontroler jest aktywny, przejdź do [strony Identyfikowanie aktywnego kontrolera na urządzeniu](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
* Podczas wymiany modułu kontrolera EBOD stale monitoruj stan składnika w usłudze StorSimple Device Manager, uzyskując dostęp do**kondycji sprzętu** **monitorującego** > .
* Jeśli kabel SAS ulegnie awarii lub wymaga wymiany (w celu dokonania takiego ustalenia należy zaangażować obsługę techniczną firmy Microsoft), należy usunąć tylko kabel SAS, który wymaga wymiany.
* W żadnym momencie nie należy jednocześnie usuwać obu kabli SAS z systemu.

### <a name="high-availability-recommendations-for-your-host-computers"></a>Zalecenia dotyczące wysokiej dostępności dla komputerów-hostów

Dokładnie zapoznaj się z tymi najlepszymi rozwiązaniami, aby zapewnić wysoką dostępność hostów połączonych z urządzeniem StorSimple.

* Skonfiguruj storproste za pomocą [konfiguracji klastra serwerów plików z dwoma węzłami][1]. Usuwając pojedyncze punkty awarii i budując nadmiarowość po stronie hosta, całe rozwiązanie staje się wysoce dostępne.
* Aby uzyskać wysoką dostępność podczas pracy awaryjnej kontrolerów magazynu, użyj udziałów stale dostępnych (CA) dostępnych w systemie Windows Server 2012 (SMB 3.0). Aby uzyskać dodatkowe informacje dotyczące konfigurowania klastrów serwerów plików i stale dostępnych udziałów w systemie Windows Server 2012, zobacz [prezentację wideo](https://channel9.msdn.com/Events/IT-Camps/IT-Camps-On-Demand-Windows-Server-2012/DEMO-Continuously-Available-File-Shares).

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o limitach systemowych StorSimple](storsimple-8000-limits.md).
* [Dowiedz się, jak wdrożyć rozwiązanie StorSimple.](storsimple-8000-deployment-walkthrough-u2.md)

<!--Reference links-->
[1]: https://technet.microsoft.com/library/cc731844(v=WS.10).aspx
