---
title: Migrowanie danych na StorSimple serii 5000 – 7000 do urządzenia z serii 8000 | Dokumentacja firmy Microsoft
description: Zawiera przegląd i wymagania wstępne funkcji migracji.
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/23/2018
ms.author: alkohli
ms.openlocfilehash: 967c03f3c4201bdcf1529fdda93717b6eb74e771
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60631694"
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-8000-series-device"></a>Migracja danych z serii StorSimple 5000 – 7000 do urządzenia z serii 8000

> [!IMPORTANT]
> - Na 31 lipca 2019 koniec stanie pomocy technicznej (EOS) będzie korzystał z serii StorSimple 5000/7000. Zaleca się, że klienci serii StorSimple 5000/7000 migrację do jednej z alternatyw opisanych w dokumencie.
> - Migracja jest obecnie asystowanej operacji. Aby przeprowadzić migrację danych z urządzenia StorSimple serii 5000 – 7000 do urządzenia z serii 8000, należy zaplanować migrację, korzystając z programu Microsoft Support. Microsoft Support następnie umożliwi subskrypcji dla migracji. Aby uzyskać więcej informacji, zobacz instrukcje [Otwórz bilet pomocy technicznej](storsimple-8000-contact-microsoft-support.md).
> - Po pliku żądania obsługi może potrwać kilka tygodni, aby wykonać plan migracji i faktycznie rozpocząć migrację.
> - Aby skontaktować się z Microsoft Support, pamiętaj Przejrzyj i ukończ [wymagania wstępne dotyczące migracji](#migration-prerequisites) wskazano w artykule.

## <a name="overview"></a>Omówienie

W tym artykule przedstawiono funkcji migracji, która umożliwia klientom serii StorSimple 5000 – 7000 migrować swoje dane z urządzenia fizycznego StorSimple 8000 series lub urządzenie w chmurze 8010/8020. Ten artykuł zawiera również linki do pobrania przewodnik krok po kroku kroków wymaganych do przeprowadzenia migracji danych z 5000 – 7000 series starszych urządzeń fizycznych serii 8000 lub urządzenie w chmurze.

Ten artykuł dotyczy zarówno urządzenia serii 8000 lokalnych, jak i urządzenia StorSimple w chmurze.


## <a name="migration-feature-versus-host-side-migration"></a>Funkcja migracji w stosunku do migracji po stronie hosta

Można przenieść dane przy użyciu funkcji migracji lub przez wykonanie migracji po stronie hosta. W tej sekcji opisano szczegóły łącznie z zalet i wad każdej z tych metod. Dzięki tym informacjom ustalenie metody do wykonywania, aby przeprowadzić migrację danych.

Funkcja migracji symuluje proces odzyskiwania po awarii z serii 7000/5000 serii 8000. Ta funkcja umożliwia migrację danych z formatu serii 5000/7000 do formatu serii 8000 na platformie Azure. Proces migracji jest inicjowane z użyciem narzędzia migracji usługi StorSimple. Narzędzie rozpoczyna pobieranie i konwersji metadanych kopii zapasowej na urządzeniu z serii 8000, a następnie używa najnowszej kopii zapasowej w celu udostępnienia woluminów na urządzeniu.

|      | Specjaliści                                                                                                                                     |Wady                                                                                                                                                              |
|------|-------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1.   | Proces migracji zachowuje historię kopii zapasowych, które zostały wykonane na serii 5000 i 7000.                                               | Gdy użytkownicy próbują uzyskać dostęp do danych, ta migracja pobierze dane z platformy Azure, w związku z tym ponoszenia kosztów pobierania danych.                                     |
| 2.   | Dane nie są migrowane po stronie hosta.                                                                                                     | Ten proces wymaga przestojów między rozpoczęciem i najnowszej kopii zapasowej są udostępniane z serii 8000 (może być oszacowane przy użyciu narzędzia migracji). |
| 3.   | Ten proces zachowuje wszystkie zasady, szablony przepustowości, szyfrowania i innych ustawień na urządzeniach z serii 8000.                      | Dostęp użytkownika zostanie ponownie Pobieraj tylko te dane, które są dostępne dla użytkowników i będą nie przywrócenia z magazynu trwałego cały zestaw danych.                                                  |
| 4.   | Ten proces wymaga dodatkowego czasu, aby przekonwertować wszystkie starsze kopie zapasowe na platformie Azure, które są wykonywane asynchronicznie bez wywierania wpływu na produkcyjne | Migracja jest możliwe tylko na poziomie konfiguracji chmury.  Jeden z woluminów konfiguracji chmury nie mogą być migrowane osobno                       |

Migracja po stronie hosta pozwala na konfigurowanie z serii 8000 niezależnie i kopiowanie danych z urządzeń serii 5000 i 7000 do urządzenia z serii 8000. Jest to równoważne migrowania danych z jednego urządzenia magazynu do innego. Szeroką gamą narzędzi, takich jak Diskboss, robocopy służą do kopiowania danych.

|      | Specjaliści                                                                                                                      |Wady                                                                                                                                                                                                      |
|------|---------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1.   | Na podstawie woluminów — migracji może być zwróciła się w jest realizowany etapami.                                               | Poprzednie kopie zapasowe (wykonywanych na serii 5000 i 7000) nie będzie dostępny dla urządzeń z serii 8000.                                                                                                       |
| 2.   | Umożliwia konsolidację danych na jedno konto magazynu na platformie Azure.                                                       | Pierwsza kopia zapasowa w chmurze w serii 8000 potrwa dłużej to wszystkie dane na potrzeby serii 8000 do wykonania kopii zapasowej na platformie Azure.                                                                     |
| 3.   | Po pomyślnej migracji wszystkie dane są lokalne na urządzeniu. Nie ma żadnych opóźnienia podczas uzyskiwania dostępu do danych. | Spowoduje zwiększenie użycia magazynu platformy Azure, dopóki dane zostaną usunięte z urządzenia 5000/7000.                                                                                                        |
| 4.   |                                                                                                                           | Jeśli urządzenie z serii 7000/5000 ma dużą ilość danych, podczas migracji te dane muszą zostać pobrane z platformy azure, które zostaną naliczone koszty oraz opóźnienia związane z pobieranie danych z platformy Azure |

Ten artykuł dotyczy tylko funkcji migracji z 5000/7000 do urządzenia z serii 8000. Aby uzyskać więcej informacji na temat migracji po stronie hosta, przejdź do [migracja z innych urządzeń magazynujących](https://download.microsoft.com/download/9/4/A/94AB8165-CCC4-430B-801B-9FD40C8DA340/Migrating%20Data%20to%20StorSimple%20Volumes_09-02-15.pdf).

## <a name="migration-prerequisites"></a>Wymagania wstępne dotyczące migracji

Poniżej przedstawiono wymagania wstępne dotyczące migracji, starsze urządzenia serii 5000 i 7000 oraz urządzenia StorSimple serii 8000.

> [!IMPORTANT]
> Przejrzyj i ukończ wymagania wstępne dotyczące migracji, zanim plik żądania obsługi przy użyciu Microsoft Support.

### <a name="for-the-50007000-series-device-source"></a>Dla urządzeń serii 5000 i 7000 (źródło)

Przed rozpoczęciem migracji upewnij się, że:

* Możesz mieć swoje 5000 lub serii 7000 źródła urządzenia; urządzenie może pozostawać na żywo i w dół.

    > [!IMPORTANT]
    > Zaleca się, że masz dostęp szeregowy, na tym urządzeniu podczas procesu migracji. Powinno być żadnych problemów z urządzeniami, dostęp szeregowy może pomóc w rozwiązywaniu problemów.

* Urządzenie źródłowe serii 5000 i 7000 jest uruchomiona v2.1.1.518 wersji oprogramowania lub nowszej. Wcześniejsze wersje nie są obsługiwane.
* Aby sprawdzić wersję, która działa z serii 5000 i 7000, Znajdź w prawym górnym rogu internetowego interfejsu użytkownika. W rezultacie wyświetlona wersja oprogramowania, na urządzeniu jest uruchomiona. W przypadku migracji z serii 5000 i 7000 powinna być uruchomiona v2.1.1.518.

    ![Sprawdź wersję oprogramowania na urządzeniu starszej wersji](media/storsimple-8000-migrate-from-5000-7000/check-version-legacy-device1.png)

    * Jeśli urządzenie na żywo jest nieuruchomiona v2.1.1.518 lub później, Uaktualnij system do wymaganej wersji minimalnej. Aby uzyskać szczegółowe instrukcje, zapoznaj się [uaktualnić system do v2.1.1.518](http://onlinehelp.storsimple.com/111_Appliance/6_System_Upgrade_Guides/Current_(v2.1.1)/000_Software_Patch_Upgrade_Guide_v2.1.1.518).
    * Jeśli używasz v2.1.1.518, przejdź do interfejsu użytkownika, aby sprawdzić, czy są wszelkie powiadomienia w przypadku niepowodzenia przywracania rejestru w sieci web. Jeśli przywracania rejestru nie powiodło się — uruchom przywracania rejestru. Aby uzyskać więcej informacji, przejdź do sposobu [Uruchom przywracania rejestru](http://onlinehelp.storsimple.com/111_Appliance/2_User_Guides/1_Current_(v2.1.1)/1_Web_UI_User_Guide_WIP/2_Configuration/4_Cloud_Accounts/1_Cloud_Credentials#Restoring_Backup_Registry).
    * Jeśli masz urządzenie dół, który nie został uruchomiony v2.1.1.518, należy wykonać przejściu w tryb failover na urządzenie zastępcze uruchomioną v2.1.1.518. Aby uzyskać szczegółowe instrukcje zapoznaj się z odzyskiwania po awarii urządzenia StorSimple serii 5000 i 7000.
    * Utwórz kopię zapasową danych dla danego urządzenia, wyświetlając migawki w chmurze.
    * Sprawdź, czy wszystkie inne aktywne zadania tworzenia kopii zapasowej, które są uruchomione na urządzeniu źródłowym. Obejmuje to zadania na hoście konsoli ochrony danych StorSimple. Poczekaj, aż bieżące zadania do wykonania.


### <a name="for-the-8000-series-physical-device-target"></a>Urządzeń fizycznych serii 8000 (docelowy)

Przed rozpoczęciem migracji upewnij się, że:

* Urządzenie serii 8000 docelowy jest zarejestrowany i uruchomiona. Aby uzyskać więcej informacji, zobacz instrukcje [wdrażanie urządzenia StorSimple z usługą StorSimple Manager](storsimple-8000-deployment-walkthrough-u2.md).
* Urządzenia z serii 8000 ma najnowsze StorSimple 8000 Series Update 5 zainstalowana i działa 6.3.9600.17845 lub nowszej wersji. Jeśli urządzenie nie ma zainstalowanych najnowszych aktualizacji, należy zainstalować najnowsze aktualizacje, przed rozpoczęciem migracji. Aby uzyskać więcej informacji, zobacz instrukcje [instalowanie najnowszych aktualizacji na urządzeniu z serii 8000](storsimple-8000-install-update-5.md).
* Twoja subskrypcja platformy Azure jest włączona na potrzeby migracji. Twoja subskrypcja nie jest włączona, należy skontaktować się z Microsoft Support włączenie subskrypcji do migracji.

### <a name="for-the-80108020-cloud-appliance-target"></a>Dla urządzenia w chmurze 8010/8020 (docelowy)

Przed rozpoczęciem migracji należy zapewnić:

* Urządzenie w chmurze docelowy jest zarejestrowany i uruchomiona. Aby uzyskać więcej informacji, zobacz instrukcje [wdrażanie i zarządzanie nimi urządzenie StorSimple w chmurze](storsimple-8000-cloud-appliance-u2.md).
* Urządzenie w chmurze działa najnowsze StorSimple 8000 Series Update 5 wersji oprogramowania 6.3.9600.17845. Jeśli urządzenie w chmurze nie jest uruchomiona aktualizacja Update 5, należy utworzyć nowe urządzenie w chmurze z aktualizacją 5 przed przystąpieniem do migracji. Aby uzyskać więcej informacji, zobacz instrukcje [utworzyć urządzenie w chmurze 8010/8020](storsimple-8000-cloud-appliance-u2.md).

### <a name="for-the-computer-running-storsimple-migration-tool"></a>Komputera, w którym działa narzędzie migracji usługi StorSimple

Narzędzie migracji usługi StorSimple jest oparty na interfejsie użytkownika narzędzia, która umożliwia migrację danych z magazynu StorSimple serii 5000 – 7000 do urządzenia z serii 8000. Aby zainstalować narzędzie migracji usługi StorSimple, użyj komputera, który spełnia następujące wymagania.

Komputer ma łączność z Internetem oraz:

* Działa następujące system operacyjny
    * Windows 10.
    * Windows Server 2012 R2 (lub nowszy) Aby zainstalować narzędzie do migracji usługi StorSimple.
* .NET 4.5.2 jest zainstalowany.
* Ma co najmniej 5 GB wolnego miejsca na dysku do zainstalowania i użyć narzędzia.

> [!TIP]
> Jeśli urządzenie StorSimple podłączonego do hosta systemu Windows Server, można zainstalować narzędzia migracji na komputerze-hoście systemu Windows Server.

#### <a name="to-install-storsimple-migration-tool"></a>Aby zainstalować narzędzie do migracji usługi StorSimple

Wykonaj poniższe kroki, aby zainstalować narzędzie do migracji usługi StorSimple na komputerze.

1. Skopiuj folder _StorSimple8000SeriesMigrationTool_ na komputer Windows. Upewnij się, że dysk, którego został skopiowany oprogramowania ma wystarczająco dużo miejsca.

    Otwórz plik konfiguracji narzędzia _StorSimple8000SeriesMigrationTool.exe.config_ w folderze. Poniżej przedstawiono fragment kodu w pliku.
    
    ```xml
        <add key="UserName" value="username@xyz.com" />
        <add key="SubscriptionName" value="YourSubscriptionName" />
        <add key="SubscriptionId" value="YourSubscriptionId" />
        <add key="TenantId" value="YourTenantId" />
        <add key="ResourceName" value="YourResourceName" />
        <add key="ResourceGroupName" value="YourResourceGroupName" />

    ```
2. Edytuj wartości odpowiadające kluczy i Zastąp:

    * `UserName` — Nazwa użytkownika Zaloguj się do witryny Azure portal.
    * `SubscriptionName and SubscriptionId` — Nazwy i IDENTYFIKATORA subskrypcji platformy Azure. W usłudze Menedżer urządzeń StorSimple strona docelowa, w obszarze **ogólne**, kliknij przycisk **właściwości**. Skopiuj nazwę subskrypcji i identyfikator subskrypcji skojarzonych z Twoją usługą.
    * `ResourceName` — Nazwa usługi Menedżer urządzeń StorSimple w witrynie Azure portal. Także wyświetlany w obszarze właściwości usługi.
    * `ResourceGroup` — Nazwa grupy zasobów skojarzonych z usługą Menedżera urządzeń StorSimple w witrynie Azure portal. Także wyświetlany w obszarze właściwości usługi.
    ![Sprawdź właściwości usługi dla urządzenia](media/storsimple-8000-migrate-from-5000-7000/check-service-properties1.png)
    * `TenantId` — Usługa azure Active Directory identyfikator dzierżawy w witrynie Azure portal. Zaloguj się do systemu Microsoft Azure jako administrator. W witrynie Microsoft Azure portal kliknij **usługi Azure Active Directory**. W obszarze **Zarządzaj**, kliknij przycisk **właściwości**. Dzierżawy, identyfikator jest wyświetlany w **identyfikator katalogu** pole.
    ![Sprawdź identyfikator dzierżawy usługi Azure Active Directory](media/storsimple-8000-migrate-from-5000-7000/check-tenantid-aad.png)

3.  Zapisz zmiany wprowadzone w pliku konfiguracji.
4.  Uruchom _StorSimple8000SeriesMigrationTool.exe_ Aby uruchomić narzędzie. Po wyświetleniu monitu o poświadczenia, podaj poświadczenia, o których skojarzonych z subskrypcją w witrynie Azure portal. 
5.  Narzędzie migracji usługi StorSimple interfejsu użytkownika jest wyświetlany.
  

## <a name="next-steps"></a>Kolejne kroki
Pobierz instrukcje krok po kroku dotyczące [migracji danych z magazynu StorSimple serii 5000 – 7000 do urządzenia z serii 8000](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b).
