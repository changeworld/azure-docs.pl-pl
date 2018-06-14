---
title: Wymagania systemowe tablicy wirtualne Microsoft Azure StorSimple | Dokumentacja firmy Microsoft
description: Więcej informacji na temat oprogramowania i wymagania sieciowe związane z macierzy wirtualnego StorSimple
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
ms.date: 11/14/2017
ms.author: alkohli
ms.openlocfilehash: 5d01523f326bd7e2518bff06e62ae62db8f318d3
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2017
ms.locfileid: "24815232"
---
# <a name="storsimple-virtual-array-system-requirements"></a>Wymagania systemowe macierzy wirtualnej StorSimple
## <a name="overview"></a>Omówienie
W tym artykule opisano wymagania systemowe dla programu Microsoft Azure StorSimple wirtualnego tablicy i klientów magazynu podczas uzyskiwania dostępu do tablicy. Firma Microsoft zaleca, aby zapoznać się z informacjami dokładnie przed wdrożeniem systemu StorSimple i następnie odwołują się do niego w razie potrzeby podczas wdrażania kolejnych operacji.

Wymagania systemowe obejmują:

* **Wymagania dotyczące oprogramowania dla klientów magazynu** — w tym artykule opisano wirtualizacji obsługiwanych platform, przeglądarki sieci web, inicjatorzy iSCSI, SMB klientów, wymagania minimalne urządzenia wirtualnego i wszelkie dodatkowe wymagania dotyczące tych systemów operacyjnych.
* **Wymagania dotyczące sieci dla urządzenia StorSimple** — informacje na temat portów, które muszą być otwarte w zaporze, aby umożliwić ruch iSCSI, chmury lub zarządzania.

Informacje wymagania systemu StorSimple opublikowane w tym artykule dotyczy tylko tablice wirtualne StorSimple.

* Dla urządzeń z serii 8000, przejdź do [wymagania systemowe dla danego urządzenia z serii StorSimple 8000](storsimple-system-requirements.md).
* Dla urządzeń z serii 7000, przejdź do [wymagania systemowe dla urządzenia StorSimple 5000 7000 serii](http://onlinehelp.storsimple.com/1_StorSimple_System_Requirements).

## <a name="software-requirements"></a>Wymagania dotyczące oprogramowania
Wymagania dotyczące oprogramowania zawierają informacje obsługiwane przeglądarki sieci web, wersji protokołu SMB, platformami wirtualizacji i wymagania dotyczące minimalnej urządzenia wirtualnego.

### <a name="supported-virtualization-platforms"></a>Platformy obsługiwane wirtualizacji
| **Funkcja hypervisor** | **Wersja** |
| --- | --- |
| Funkcja Hyper-V |Windows Server 2008 R2 z dodatkiem SP1 lub nowszy |
| VMware ESXi |5.0, 5.5 i 6.0 <br> (6.5 nie jest obsługiwana.) |

> [!IMPORTANT]
> Nie instaluj narzędzi VMware tablica wirtualnego StorSimple; Spowoduje to nieobsługiwaną konfiguracją.

### <a name="virtual-device-requirements"></a>Wymagania dotyczące urządzeń wirtualnych
| **Składnik** | **Wymaganie** |
| --- | --- |
| Minimalna liczba procesorów wirtualnych (rdzenie) |4 |
| Minimalna ilość pamięci (RAM) |8 GB <br> Serwer plików, 8 GB mniej niż 2 miliony plików i 16 GB 2-4 miliony plików|
| Miejsce na dysku<sup>1</sup> |Dysk systemu operacyjnego - 80 GB <br></br>Dysk danych — 500 GB do 8 TB |
| Minimalna liczba interfejsów sieci |1 |
| Przepustowości połączenia z Internetem<sup>2</sup> |Przepustowość minimalna wymagana: 5 MB/s <br> Zalecane przepustowości: 100 MB/s <br> Szybkość transferu danych można skalować przepustowości połączenia z Internetem. Na przykład 100 GB danych ma 2 dni do transferu 5 MB/s, które może powodować awarie kopii zapasowych, ponieważ codzienne wykonywanie kopii zapasowych nie zostać ukończone w ciągu jednego dnia. O przepustowości 100 MB/s 100 GB danych można przenieść w wersji 2.5 godzinach.   |

<sup>1</sup> — elastycznej obsługi administracyjnej

<sup>2</sup> — wymagania dotyczące sieci może się różnić w zależności od częstotliwości dziennych zmian danych. Na przykład jeśli urządzenie musi utworzyć kopię zapasową 10 GB lub więcej zmian w ciągu dnia, następnie tworzenia codziennej kopii zapasowej za pośrednictwem połączenia 5 MB/s może zająć do 4,25 godzin (Jeśli nie można skompresować lub deduplikowany danych).

### <a name="supported-web-browsers"></a>Obsługiwane przeglądarki sieci web
| **Składnik** | **Wersja** | **Dodatkowe wymagania dotyczące/uwagi** |
| --- | --- | --- |
| Przeglądarka Microsoft Edge |Najnowsza wersja | |
| Program Internet Explorer |Najnowsza wersja |Z programu Internet Explorer 11 |
| Google Chrome |Najnowsza wersja |Przetestowana Chrome 46 |

### <a name="supported-storage-clients"></a>Klienci obsługiwanego magazynu
Następujące wymagania dotyczące oprogramowania dotyczą inicjatorów iSCSI, które uzyskują dostęp do macierzy wirtualnego StorSimple (skonfigurowany jako serwer iSCSI).

| **Obsługiwane systemy operacyjne** | **Wersja wymagana** | **Dodatkowe wymagania dotyczące/uwagi** |
| --- | --- | --- |
| Windows Server |2008 R2 Z DODATKIEM SP1, 2012, 2012 R2 |StorSimple można tworzyć alokowane elastycznie i pełni woluminów. Nie można go tworzyć woluminów inicjowanych częściowo. Woluminy StorSimple iSCSI są obsługiwane tylko w przypadku: <ul><li>Proste woluminy na dyskach podstawowych systemu Windows.</li><li>Windows NTFS formatowania woluminu.</li> |

Następujące wymagania dotyczące oprogramowania dotyczą klientów protokołu SMB, które uzyskują dostęp do macierzy wirtualnego StorSimple (skonfigurowany jako serwer plików).

| **Wersja protokołu SMB** |
| --- |
| SMB 2.x |
| SMB 3.0 |
| SMB 3.02 |

> [!IMPORTANT]
> Nie Kopiuj i przechowywania plików chronionych przez system Windows System szyfrowania plików (EFS) do serwera plików tablicy wirtualnego StorSimple; Spowoduje to nieobsługiwaną konfiguracją.


### <a name="supported-storage-format"></a>Obsługiwany format przechowywania
Jest obsługiwana tylko magazynu obiektów blob Azure bloku. Stronicowe obiekty BLOB nie są obsługiwane. Więcej informacji [o blokowe i stronicowe obiekty BLOB](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).

## <a name="networking-requirements"></a>Wymagania dotyczące sieci
W poniższej tabeli wymieniono porty, które muszą być otwarte w zaporze, aby umożliwić iSCSI, SMB, chmury oraz ruch związany z zarządzaniem. W tej tabeli *w* lub *przychodzących* odwołuje się do kierunek, w którym przychodzące żądania klientów dostępu urządzenia. *Limit* lub *wychodzących* odwołuje się do kierunek, w którym urządzenia StorSimple wysyła dane zewnętrznie, poza wdrożenia: na przykład ruch wychodzący do Internetu.

| **Nr portu<sup>1</sup>** | **Przychodzący lub wychodzący** | **Zakres portów** | **Wymagane** | **Uwagi** |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP) |limit |WAN |Nie |Port wyjściowy jest używana do dostępu do Internetu do pobierania aktualizacji. <br></br>Serwer proxy sieci web wychodzącego jest konfigurowane przez użytkownika. |
| TCP 443 (HTTPS) |limit |WAN |Tak |Wychodzący port jest używany do uzyskiwania dostępu do danych w chmurze. <br></br>Serwer proxy sieci web wychodzącego jest konfigurowane przez użytkownika. |
| UDP 53 (DNS) |limit |WAN |W niektórych przypadkach; Zobacz uwagi. |Ten port jest wymagany tylko wtedy, gdy używasz serwera DNS w Internecie. <br></br> Należy pamiętać, że wdrażania serwera plików, firma Microsoft zaleca używanie lokalnego serwera DNS. |
| UDP 123 (NTP) |limit |WAN |W niektórych przypadkach; Zobacz uwagi. |Ten port jest wymagany tylko wtedy, gdy używasz serwera NTP internetowego.<br></br> Należy zwrócić uwagę, jeżeli wdrażanie serwera plików, zaleca się synchronizowanie czasu z kontrolerów domeny usługi Active Directory. |
| TCP 80 (HTTP) |W |SIECI LAN |Tak |Jest to port wejściowy do lokalnego interfejsu użytkownika na urządzeniu StorSimple na potrzeby lokalnego zarządzania. <br></br> Należy pamiętać, uzyskiwanie dostępu do lokalnego interfejsu użytkownika za pośrednictwem protokołu HTTP spowoduje automatyczne przekierowanie HTTPS. |
| TCP 443 (HTTPS) |W |SIECI LAN |Tak |Jest to port wejściowy do lokalnego interfejsu użytkownika na urządzeniu StorSimple na potrzeby lokalnego zarządzania. |
| 3260 TCP (iSCSI) |W |SIECI LAN |Nie |Port ten jest używany do dostępu do danych za pośrednictwem interfejsu iSCSI. |

<sup>1</sup> nie portów przychodzących muszą być otwarte na publiczny Internet.

> [!IMPORTANT]
> Upewnij się, czy zapory nie zmodyfikować lub odszyfrować żaden ruch protokołu SSL między urządzenia StorSimple i Azure.
> 
> 

### <a name="url-patterns-for-firewall-rules"></a>Wzorce adresu URL dla reguł zapory
Administratorzy sieci często można skonfigurować reguł zapory zaawansowane, na podstawie wzorców adresów URL do filtrowania ruchu przychodzącego i wychodzącego ruchu sieciowego. Tablica wirtualne i usługi Menedżer StorSimple urządzenia są zależne od innych aplikacji firmy Microsoft, takich jak usługi Azure Service Bus, Azure Active Directory kontroli dostępu konta magazynu i serwerach Microsoft Update. Wzorce adresu URL skojarzone z tymi aplikacjami można skonfigurować reguł zapory. Należy zrozumieć, że wzorców adresów URL skojarzone z tymi aplikacjami można zmienić. Z kolei wymaga administratorem sieci, aby monitorować i zaktualizować reguł zapory dla Twojego urządzenia StorSimple, jak i w razie potrzeby. 

Firma Microsoft zaleca, aby ustawić regułach zapory dla ruchu wychodzącego, oparte na StorSimple liberally stałe adresy IP, w większości przypadków. Jednak można użyć poniższe informacje, można ustawić reguły zapory zaawansowane, które są potrzebne do utworzenia bezpiecznego środowiska.

> [!NOTE]
> 
> * Urządzenia (źródło) adresów IP powinien być zawsze ustawiony na wszystkich interfejsów sieciowych z obsługą chmury. 
> * Miejsce docelowe adresy IP należy wybrać opcję [zakresy IP centrum danych Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653).
> 
> 

| Wzorzec URL | Składnik/funkcji |
| --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*` <br>`https://login.windows.net`|Usługa menedżera urządzenia StorSimple<br>Usługa kontroli dostępu<br>Azure Service Bus<br>Usługa uwierzytelniania|
| `http://*.backup.windowsazure.com` |Rejestracja urządzenia |
| `http://crl.microsoft.com/pki/*`<br>`http://www.microsoft.com/pki/*` |Odwoływanie certyfikatów |
| `https://*.core.windows.net/*`<br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Monitorowanie i kontami magazynu Azure |
| `http://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`http://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`http://download.microsoft.com`<br>`http://wustat.windows.com`<br>`http://ntservicepack.microsoft.com` |Serwerów Microsoft Update<br> |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |
| `https://*.partners.extranet.microsoft.com/*` |Pakiet pomocy technicznej |
| `http://*.data.microsoft.com ` |Usługa telemetrii w systemie Windows, zobacz [aktualizacja obsługi klienta i dane telemetryczne diagnostycznych](https://support.microsoft.com/en-us/kb/3068708) |

## <a name="next-steps"></a>Następne kroki
* [Przygotowanie portalu, aby wdrożyć tablica wirtualnego StorSimple](storsimple-virtual-array-deploy1-portal-prep.md)
