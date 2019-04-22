---
title: Wymagania dotyczące systemu Microsoft Azure StorSimple Virtual Array | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o oprogramowaniu i wymaganiach sieciowych dotyczących rozwiązania StorSimple Virtual Array
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ea1d3bca-e71b-453d-aa82-440d2638f5e3
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/11/2019
ms.author: alkohli
ms.openlocfilehash: a6bea2b5447435930cb0e1f80073a11007e80415
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58876840"
---
# <a name="storsimple-virtual-array-system-requirements"></a>Wymagania systemowe macierzy wirtualnej StorSimple
## <a name="overview"></a>Omówienie
W tym artykule opisano wymagania systemowe dla usługi Microsoft Azure StorSimple Virtual Array i klientów usług magazynowych korzystających tablicy. Firma Microsoft zaleca, aby zapoznać się z informacjami dokładnie przed wdrożeniem systemu StorSimple i następnie wrócić do niego zgodnie z potrzebami podczas wdrażania i kolejna operacja.

Wymagania systemowe, obejmują:

* **Wymagania dotyczące oprogramowania dla klientów usług magazynowych korzystających** — w tym artykule opisano platform wirtualizacji obsługiwanych przeglądarek sieci web, inicjatorzy iSCSI, SMB klientów, wymagania minimalne urządzenia wirtualnego i wszelkie dodatkowe wymagania dotyczące tych systemów operacyjnych.
* **Wymagania dotyczące sieci dla urządzenia StorSimple** — informacje na temat portów, które muszą być otwarte w zaporze, aby umożliwić ruch iSCSI, chmury lub zarządzania.

Informacje wymagania dotyczące systemu StorSimple opublikowane w tym artykule dotyczy tylko macierze wirtualne StorSimple.

* W przypadku urządzeń z serii 8000, przejdź do [wymagania systemowe dla Twojego urządzenia StorSimple 8000 series](storsimple-system-requirements.md).
* Dla urządzeń z serii 7000, przejdź do [wymagania systemowe dla urządzenia StorSimple 5000 – 7000 series](http://onlinehelp.storsimple.com/1_StorSimple_System_Requirements).

## <a name="software-requirements"></a>Wymagania dotyczące oprogramowania
Wymagania dotyczące oprogramowania zawierają informacje na obsługiwanych przeglądarek internetowych, wersji protokołu SMB, platform wirtualizacji i wymagania minimalne urządzenia wirtualnego.

### <a name="supported-virtualization-platforms"></a>Platformy obsługiwane wirtualizacji
| **Funkcja hypervisor** | **Wersja** |
| --- | --- |
| Funkcja Hyper-V |Windows Server 2008 R2 z dodatkiem SP1 lub nowszy |
| VMware ESXi |w wersji 5.0, 5.5, 6.0 i 6.5. |

> [!IMPORTANT]
> Nie należy instalować oprogramowania VMware na rozwiązania StorSimple Virtual Array; Spowoduje to nieobsługiwaną konfigurację.

### <a name="virtual-device-requirements"></a>Wymagania dotyczące urządzeń wirtualnych
| **Składnik** | **Wymaganie** |
| --- | --- |
| Minimalna liczba procesorów wirtualnych (rdzeni) |4 |
| Minimalna ilość pamięci (RAM) |8 GB <br> Dla serwera plików, 8 GB w mniej niż 2 miliony plików i 16 GB 2-4 miliony plików|
| Miejsce na dysku<sup>1</sup> |Dysk systemu operacyjnego — 80 GB <br></br>Dysk z danymi — od 500 GB do 8 TB |
| Minimalna liczba interfejsy sieciowe |1 |
| Przepustowością Internetu<sup>2</sup> |Minimalna przepustowość wymagana na: 5 MB/s <br> Zalecaną przepustowość tak: 100 Mb/s <br> Szybkość danych transferu skaluje się przy użyciu przepustowości Internetu. Na przykład 100 GB danych ma 2 dni do transferu na 5 MB/s, co może prowadzić do błędów kopii zapasowych, ponieważ nie wykonają codzienne kopie zapasowe w ciągu dnia. O przepustowości 100 MB/s 100 GB danych można przenieść w 2,5 godziny.   |

<sup>1</sup> — alokowania elastycznego zainicjowano obsługę administracyjną

<sup>2</sup> — wymagania dotyczące sieci mogą się różnić w zależności od dziennych zmian danych. Na przykład jeśli urządzenie musi utworzyć kopię zapasową 10 GB lub więcej zmian w ciągu dnia, następnie codzienne wykonywanie kopii zapasowych za pośrednictwem połączenia 5 MB/s może zająć maksymalnie 4,25 godzin (Jeśli dane nie można skompresować lub deduplikowany).

### <a name="supported-web-browsers"></a>Obsługiwane przeglądarki sieci web
| **Składnik** | **Wersja** | **Dodatkowe wymagania dotyczące/uwagi** |
| --- | --- | --- |
| Microsoft Edge |Najnowsza wersja | |
| Internet Explorer |Najnowsza wersja |Przetestowane za pomocą programu Internet Explorer 11 |
| Google Chrome |Najnowsza wersja |Przetestowane za pomocą przeglądarki Chrome 46 |

### <a name="supported-storage-clients"></a>Klienci usług magazynowych obsługiwane
Są następujące wymagania dotyczące oprogramowania dla inicjatorów iSCSI, uzyskujących dostęp do rozwiązania StorSimple Virtual Array (skonfigurowany jako serwer iSCSI).

| **Obsługiwane systemy operacyjne** | **Wymagana wersja** | **Dodatkowe wymagania dotyczące/uwagi** |
| --- | --- | --- |
| Windows Server |2008R2 SP1, 2012, 2012R2 |Usługa StorSimple można tworzyć alokowane elastycznie i inicjowane w pełni woluminów. Nie można go tworzyć woluminów inicjowanych częściowo. Woluminy iSCSI StorSimple są obsługiwane tylko w przypadku: <ul><li>Woluminów prostych na dyskach podstawowych Windows.</li><li>Windows System plików NTFS do formatowania woluminu.</li> |

Są następujące wymagania dotyczące oprogramowania dla klientów protokołu SMB uzyskujących dostęp do rozwiązania StorSimple Virtual Array (skonfigurowana jako serwer plików).

| **Wersja protokołu SMB** |
| --- |
| SMB 2.x |
| SMB 3.0 |
| SMB 3.02 |

> [!IMPORTANT]
> Nie Kopiuj i przechowywać pliki chronione przez Windows szyfrowania pliku System (EFS) do serwera plików rozwiązania StorSimple Virtual Array; Spowoduje to nieobsługiwaną konfigurację.


### <a name="supported-storage-format"></a>Obsługiwany formatu przechowywania
Tylko do magazynu obiektów blob Azure block jest obsługiwane. Stronicowe obiekty BLOB nie są obsługiwane. Więcej informacji na [o blokowych obiektów blob i stronicowe obiekty BLOB](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).

## <a name="networking-requirements"></a>Wymagania dotyczące sieci
Poniższa tabela zawiera listę portów, które muszą być otwarte w zaporze, aby umożliwić iSCSI, SMB, chmury lub ruch związany z zarządzaniem. W tej tabeli *w* lub *dla ruchu przychodzącego* Określa kierunek, w którym przychodzące żądania klientów dostęp do urządzenia. *Limit* lub *wychodzącego* Określa kierunek, w którym urządzenie StorSimple wysyła dane zewnętrznie, poza wdrożenia: na przykład, ruch wychodzący do Internetu.

| **Nr portu<sup>1</sup>** | **Wewnątrz lub na zewnątrz** | **Zakres portów** | **Wymagane** | **Uwagi** |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP) |Wyjście |Sieć WAN |Nie |Wychodząca przez port służy do dostępu do Internetu do pobierania aktualizacji. <br></br>Serwer proxy ruchu wychodzącego w sieci web jest użytkownika. |
| TCP 443 (HTTPS) |Wyjście |Sieć WAN |Yes |Wychodząca przez port jest używany do uzyskiwania dostępu do danych w chmurze. <br></br>Serwer proxy ruchu wychodzącego w sieci web jest użytkownika. |
| UDP 53 (DNS) |Wyjście |Sieć WAN |W niektórych przypadkach; Zobacz uwagi. |Ten port jest wymagany tylko wtedy, gdy używasz serwera DNS internetowego. <br></br> Należy pamiętać o tym, jeśli wdrożenie serwera plików, zalecamy używanie lokalnego serwera DNS. |
| UDP 123 (NTP) |Wyjście |Sieć WAN |W niektórych przypadkach; Zobacz uwagi. |Ten port jest wymagany tylko wtedy, gdy korzystają z serwera NTP oparty na Internecie.<br></br> Należy pamiętać, że jeśli wdrażanie serwera plików, zaleca się synchronizowanie czasu z kontrolerów domeny usługi Active Directory. |
| TCP 80 (HTTP) |W |Sieć LAN |Yes |Jest to port wejściowy dla lokalnego interfejsu użytkownika na urządzeniu StorSimple na potrzeby zarządzania lokalnego. <br></br> Należy pamiętać, uzyskiwanie dostępu do lokalnego interfejsu użytkownika za pośrednictwem protokołu HTTP spowoduje automatyczne przekierowanie do protokołu HTTPS. |
| TCP 443 (HTTPS) |W |Sieć LAN |Yes |Jest to port wejściowy dla lokalnego interfejsu użytkownika na urządzeniu StorSimple na potrzeby zarządzania lokalnego. |
| TCP 3260 (iSCSI) |W |Sieć LAN |Nie |Port ten jest używany do dostępu do danych za pośrednictwem interfejsu iSCSI. |

<sup>1</sup> nie portów przychodzących, które muszą być otwarte w publicznym Internecie.

> [!IMPORTANT]
> Upewnij się, zapory nie modyfikować ani odszyfrować cały ruch protokołu SSL między urządzeniem StorSimple i platformą Azure.
> 
> 

### <a name="url-patterns-for-firewall-rules"></a>Wzorce adresów URL dla reguł zapory
Administratorzy sieci często można skonfigurować reguły zapory zaawansowane, na podstawie wzorców adresów URL, do filtrowania przychodzącego i wychodzącego ruchu. Macierz wirtualna i usługi Menedżer urządzeń StorSimple są zależne od innych aplikacji firmy Microsoft, takich jak usługi Azure Service Bus, Azure Active Directory Access Control, kont magazynu i serwerami usługi Microsoft Update. Wzorce adresów URL skojarzone z tymi aplikacjami umożliwia konfigurowanie reguł zapory. Jest ważne dowiedzieć się, że wzorce adresów URL skojarzone z tymi aplikacjami można zmienić. Wymaga to co z kolei administrator sieci monitorować i aktualizować reguły zapory dla usługi StorSimple, jak i potrzebny. 

Firma Microsoft zaleca ustawienie reguły zapory dla ruchu wychodzącego, w oparciu o usługi StorSimple, stałe adresy IP, liberally, w większości przypadków. Jednak można użyć poniższych informacji do ustawiania reguły zapory zaawansowanych, które są potrzebne do tworzenia bezpiecznych środowisk.

> [!NOTE]
> 
> * Urządzenia (źródło) adresy IP powinny być zawsze ustawiony wszystkie interfejsy sieciowe z obsługą chmury. 
> * Miejsce docelowe adresy IP powinien być ustawiony na [zakresy IP centrów danych platformy Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653).
> 
> 

| Wzorzec URL | Składnik/funkcji |
| --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*` <br>`https://login.windows.net`|Usługa Menedżer urządzeń StorSimple<br>Usługa kontroli dostępu<br>Azure Service Bus<br>Usługa uwierzytelniania|
| `http://*.backup.windowsazure.com` |Rejestracja urządzenia |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Odwoływanie certyfikatów |
| `https://*.core.windows.net/*`<br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Konta usługi Azure storage i monitorowania |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Serwerami usługi Microsoft Update<br> |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |
| `https://*.partners.extranet.microsoft.com/*` |Pakiet dla pomocy technicznej |
| `https://*.data.microsoft.com` |Usługa telemetrii w Windows, zobacz [aktualizacja dla programu obsługi klienta i diagnostycznych telemetrii](https://support.microsoft.com/en-us/kb/3068708) |

## <a name="next-steps"></a>Kolejne kroki
* [Przygotowywanie portalu, aby wdrożyć rozwiązania StorSimple Virtual Array](storsimple-virtual-array-deploy1-portal-prep.md)
