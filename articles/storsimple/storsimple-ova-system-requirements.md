---
title: Wymagania systemowe microsoft azure storsimple virtual array
description: Dowiedz się więcej o oprogramowaniu i wymaganiach sieciowych dla tablicy wirtualnej StorSimple
author: alkohli
ms.assetid: ea1d3bca-e71b-453d-aa82-440d2638f5e3
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 020208a8b67d248c02fc659d4dc48fa22d333839
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298818"
---
# <a name="storsimple-virtual-array-system-requirements"></a>Wymagania systemowe macierzy wirtualnej StorSimple

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

## <a name="overview"></a>Omówienie

W tym artykule opisano ważne wymagania systemowe dla tablicy wirtualnej usługi Microsoft Azure StorSimple i klientów magazynu uzyskujących dostęp do tablicy. Zaleca się dokładne przejrzenie informacji przed wdrożeniem systemu StorSimple, a następnie odwołać się do niego w razie potrzeby podczas wdrażania i późniejszej operacji.

Wymagania systemowe obejmują:

* **Wymagania programowe dla klientów magazynu** - opisuje obsługiwane platformy wirtualizacji, przeglądarki internetowe, inicjatorów iSCSI, klientów SMB, minimalne wymagania dotyczące urządzeń wirtualnych i wszelkie dodatkowe wymagania dla tych systemów operacyjnych.
* **Wymagania dotyczące sieci dla urządzenia StorSimple** — zawiera informacje o portach, które muszą być otwarte w zaporze, aby umożliwić iSCSI, chmura lub ruch zarządzania.

StorSimple informacje o wymaganiach systemowych opublikowane w tym artykule dotyczy tylko StorSimple tablice wirtualne.

* W przypadku urządzeń z serii 8000 przejdź do [witryny Wymagania systemowe urządzenia z serii StorSimple 8000](storsimple-system-requirements.md).
* W przypadku urządzeń z serii 7000 przejdź do [witryny Wymagania systemowe urządzenia z serii StorSimple 5000-7000](http://onlinehelp.storsimple.com/1_StorSimple_System_Requirements).

## <a name="software-requirements"></a>Wymagania dotyczące oprogramowania
Wymagania dotyczące oprogramowania obejmują informacje o obsługiwanych przeglądarkach internetowych, wersjach SMB, platformach wirtualizacji i minimalnych wymaganiach dotyczących urządzeń wirtualnych.

### <a name="supported-virtualization-platforms"></a>Obsługiwane platformy wirtualizacji
| **Funkcja hypervisor** | **Wersja** |
| --- | --- |
| Funkcja Hyper-V |Dodatek SP1 dla systemu Windows Server 2008 R2 lub nowszych |
| VMware ESXi |5,0, 5,5, 6,0 i 6,5. |

> [!IMPORTANT]
> Nie należy instalować narzędzi VMware na tablicy wirtualnej StorSimple; spowoduje to nieobsługiwał konfiguracji.

### <a name="virtual-device-requirements"></a>Wymagania dotyczące urządzeń wirtualnych
| **Składnik** | **Wymaganie** |
| --- | --- |
| Minimalna liczba procesorów wirtualnych (rdzeni) |4 |
| Minimalna ilość pamięci (RAM) |8 GB <br> W przypadku serwera plików 8 GB dla mniej niż 2 milionów plików i 16 GB dla 2 - 4 milionów plików|
| Miejsce na dysku<sup>1</sup> |Dysk systemu operacyjnego - 80 GB <br></br>Dysk danych - 500 GB do 8 TB |
| Minimalna liczba interfejsów sieciowych |1 |
| Przepustowość Internetu<sup>2</sup> |Wymagana minimalna przepustowość: 5 Mb/s <br> Zalecana przepustowość: 100 Mb/s <br> Szybkość transferu danych jest skalowana z przepustowością Internetu. Na przykład 100 GB danych trwa 2 dni, aby przenieść z prędkością 5 Mbps, co może prowadzić do awarii kopii zapasowej, ponieważ codzienne kopie zapasowe nie zostaną ukończone w ciągu jednego dnia. Dzięki przepustowości 100 Mb/s 100 GB danych można przesyłać w ciągu 2,5 godziny.   |

<sup>1</sup> - Cienkie aprowizacji

<sup>2</sup> - Wymagania sieciowe mogą się różnić w zależności od dziennej szybkości zmiany danych. Jeśli na przykład urządzenie musi wykonać kopię zapasową 10 GB lub więcej zmian w ciągu dnia, codzienna kopia zapasowa za pomocą połączenia 5 Mb/s może potrwać do 4,25 godziny (jeśli nie można skompresować lub deduplikować danych).

### <a name="supported-web-browsers"></a>Obsługiwane przeglądarki sieci Web
| **Składnik** | **Wersja** | **Dodatkowe wymagania/uwagi** |
| --- | --- | --- |
| Microsoft Edge |Najnowsza wersja | |
| Internet Explorer |Najnowsza wersja |Testowany z programem Internet Explorer 11 |
| Google Chrome |Najnowsza wersja |Testowany z Chrome 46 |

### <a name="supported-storage-clients"></a>Obsługiwani klienci magazynu
Poniższe wymagania dotyczące oprogramowania są dla inicjatorów iSCSI, którzy uzyskują dostęp do tablicy wirtualnej StorSimple (skonfigurowanej jako serwer iSCSI).

| **Obsługiwane systemy operacyjne** | **Wymagana wersja** | **Dodatkowe wymagania/uwagi** |
| --- | --- | --- |
| Windows Server |2008R2 SP1, 2012, 2012R2 |StorSimple można utworzyć woluminy cienko aprowizacji i w pełni aprowizowana. Nie można utworzyć woluminów aprowizowanych częściowo. Woluminy iSCSI storsimple są obsługiwane tylko dla: <ul><li>Proste woluminy na dyskach podstawowych systemu Windows.</li><li>System NTFS systemu Windows do formatowania woluminu.</li> |

Następujące wymagania dotyczące oprogramowania są dla klientów SMB, którzy uzyskują dostęp do tablicy wirtualnej StorSimple (skonfigurowany jako serwer plików).

| **Wersja protokołu SMB** |
| --- |
| SMB 2.x |
| SMB 3.0 |
| SMB 3.02 |

> [!IMPORTANT]
> Nie należy kopiować ani przechowywać plików chronionych przez system szyfrowania plików systemu Windows (EFS) na serwerze plików StorSimple Virtual Array; spowoduje to nieobsługiwał konfiguracji.


### <a name="supported-storage-format"></a>Obsługiwany format pamięci masowej
Obsługiwane jest tylko magazyn obiektów blob bloku platformy Azure. Obiekty BLOB strony nie są obsługiwane. Więcej informacji [o blokowych obiektach blob i stronicowych obiektach blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).

## <a name="networking-requirements"></a>Wymagania dotyczące sieci
W poniższej tabeli wymieniono porty, które muszą zostać otwarte w zaporze, aby umożliwić ruch iSCSI, SMB, cloud lub zarządzanie. W tej tabeli *in* lub *inbound* odnosi się do kierunku, z którego przychodzące żądania klienta dostęp do urządzenia. *Wyjście* lub *wychodzące* odnosi się do kierunku, w którym urządzenie StorSimple wysyła dane zewnętrznie, poza wdrożeniem: na przykład wychodzących do Internetu.

| **Port nr<sup>1</sup>** | **Wył.** | **Zakres portu** | **Wymagane** | **Uwagi** |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP) |Out |Sieć WAN |Nie |Port wychodzący jest używany do pobierania aktualizacji przez dostęp do Internetu. <br></br>Wychodzący serwer proxy sieci Web jest konfigurowany przez użytkownika. |
| Protokół TCP 443 (HTTPS) |Out |Sieć WAN |Tak |Port wychodzący służy do uzyskiwania dostępu do danych w chmurze. <br></br>Wychodzący serwer proxy sieci Web jest konfigurowany przez użytkownika. |
| UDP 53 (DNS) |Out |Sieć WAN |W niektórych przypadkach; patrz notatki. |Ten port jest wymagany tylko w przypadku korzystania z internetowego serwera DNS. <br></br> Należy zauważyć, że w przypadku wdrażania serwera plików zaleca się użycie lokalnego serwera DNS. |
| UDP 123 (NTP) |Out |Sieć WAN |W niektórych przypadkach; patrz notatki. |Ten port jest wymagany tylko w przypadku korzystania z internetowego serwera NTP.<br></br> Należy zauważyć, że w przypadku wdrażania serwera plików zaleca się synchronizację czasu z kontrolerami domeny usługi Active Directory. |
| TCP 80 (HTTP) |W |Sieć LAN |Tak |Jest to port przychodzący dla lokalnego interfejsu użytkownika na urządzeniu StorSimple do zarządzania lokalnego. <br></br> Należy zauważyć, że dostęp do lokalnego interfejsu użytkownika za pośrednictwem protokołu HTTP zostanie automatycznie przekierowany do protokołu HTTPS. |
| Protokół TCP 443 (HTTPS) |W |Sieć LAN |Tak |Jest to port przychodzący dla lokalnego interfejsu użytkownika na urządzeniu StorSimple do zarządzania lokalnego. |
| TCP 3260 (iSCSI) |W |Sieć LAN |Nie |Ten port jest używany do uzyskiwania dostępu do danych za jego dostęp za 0SCSI. |

<sup>1</sup> W publicznym Internecie nie trzeba otwierać portów przychodzących.

> [!IMPORTANT]
> Upewnij się, że zapora nie modyfikuje ani nie odszyfrowuje żadnego ruchu TLS między urządzeniem StorSimple a platformą Azure.
> 
> 

### <a name="url-patterns-for-firewall-rules"></a>Wzorce adresów URL dla reguł zapory
Administratorzy sieci często mogą konfigurować zaawansowane reguły zapory na podstawie wzorców adresów URL w celu filtrowania ruchu przychodzącego i wychodzącego. Tablica wirtualna i usługa Menedżer urządzeń StorSimple zależą od innych aplikacji firmy Microsoft, takich jak usługa Azure Service Bus, kontrola dostępu usługi Azure Active Directory, konta magazynu i serwery usługi Microsoft Update. Wzorce adresów URL skojarzone z tymi aplikacjami mogą służyć do konfigurowania reguł zapory. Ważne jest, aby zrozumieć, że wzorce adresów URL skojarzone z tymi aplikacjami mogą ulec zmianie. To z kolei będzie wymagać administrator sieci do monitorowania i aktualizacji reguł zapory dla StorSimple w razie potrzeby. 

Zaleca się ustawienie reguł zapory dla ruchu wychodzącego, na podstawie storsimple stałych adresów IP, obficie w większości przypadków. Można jednak użyć poniższych informacji, aby ustawić zaawansowane reguły zapory, które są potrzebne do tworzenia bezpiecznych środowisk.

> [!NOTE]
> 
> * Interfejsy IP urządzenia (źródła) powinny być zawsze ustawione na wszystkie interfejsy sieciowe z obsługą chmury. 
> * Docelowe adresy IP powinny być ustawione na [zakresy adresów IP centrum danych platformy Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653).
> 
> 

| Wzorzec adresu URL | Komponent/funkcjonalność |
| --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*` <br>`https://login.windows.net`|Usługa Menedżer urządzeń StorSimple<br>Usługa kontroli dostępu<br>Azure Service Bus<br>Usługa uwierzytelniania|
| `http://*.backup.windowsazure.com` |Rejestracja urządzenia |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Odwołanie certyfikatu |
| `https://*.core.windows.net/*`<br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Konta magazynu platformy Azure i monitorowanie |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Serwery usługi Microsoft Update<br> |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |
| `https://*.partners.extranet.microsoft.com/*` |Pakiet pomocy technicznej |
| `https://*.data.microsoft.com` |Usługa telemetrii w systemie Windows, zobacz [aktualizację dla obsługi klienta i danych diagnostycznych telemetrycznych](https://support.microsoft.com/en-us/kb/3068708) |

## <a name="next-steps"></a>Następne kroki
* [Przygotowanie portalu do wdrożenia tablicy wirtualnej StorSimple](storsimple-virtual-array-deploy1-portal-prep.md)
