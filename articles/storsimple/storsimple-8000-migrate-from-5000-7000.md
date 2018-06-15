---
title: Migracja danych na StorSimple serii 5000 7000 do 8000 serii urządzenia | Dokumentacja firmy Microsoft
description: Zawiera omówienie i wymagania wstępne funkcji migracji.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/11/2017
ms.author: alkohli
ms.openlocfilehash: 36df62c4b01c623702707d39c6af59f4752ee6e0
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2017
ms.locfileid: "26639872"
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-8000-series-device"></a>Migracji danych z serii 5000 7000 StorSimple 8000 serii urządzenia

> [!IMPORTANT]
> - Migracja jest obecnie asystowaną operacji. Jeśli planujesz migrację danych z urządzenia StorSimple serii 5000 7000 do 8000 serii urządzenia, należy zaplanować migracji z programu Microsoft Support. Microsoft Support następnie umożliwi subskrypcji do migracji. Aby uzyskać więcej informacji, zobacz temat jak [Otwórz bilet pomocy technicznej](storsimple-8000-contact-microsoft-support.md).
> - Po pliku żądania obsługi może potrwać kilka tygodni, przez które wykonanie planu migracji i faktycznie rozpocząć migrację.
> - Przed skontaktowaniem się Support firmy Microsoft, należy koniecznie monitującymi i pełne [wymagania wstępne dotyczące migracji](#migration-prerequisites) wskazanych w artykule.

## <a name="overview"></a>Omówienie

W tym artykule przedstawiono funkcji migracji, które umożliwia klientom serii 5000 7000 StorSimple do przeprowadzenia migracji swoje dane z urządzenia fizycznego StorSimple 8000 serii lub urządzenia 8010/8020 chmury. Ten artykuł zawiera również linki do pobrania wskazówki krok po kroku kroki wymagane do przeprowadzenia migracji danych z serii starszych urządzeń do serii 8000 fizycznych 5000 7000 lub urządzenia chmury.

Ten artykuł dotyczy zarówno urządzenia serii 8000 lokalnymi, jak i urządzenia StorSimple w chmurze.


## <a name="migration-feature-versus-host-side-migration"></a>Funkcja migracji i migracji po stronie hosta

Można przenieść dane przy użyciu funkcji migracji lub przez wykonanie migracji po stronie hosta. W tej sekcji opisano szczegółowe informacje o tym zalet i wad każdej z metod. Dzięki tym informacjom można ustalić które metody chcesz wykonywać migrację danych.

Funkcja migracji symuluje proces awaryjnego odzyskiwania (DR) z serii 7000/5000 do 8000 serii. Ta funkcja umożliwia migrację danych z formatu serii 5000/7000 do formatu serii 8000 na platformie Azure. Proces migracji jest inicjowana przy użyciu narzędzia migracji StorSimple. Narzędzie uruchamia pobieranie i konwersji metadanych kopii zapasowej na urządzeniu serii 8000, a następnie używa najnowszej kopii zapasowej prezentują woluminy na urządzeniu.

|      | Specjaliści                                                                                                                                     |Wady                                                                                                                                                              |
|------|-------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1.   | Proces migracji zachowuje historię kopii zapasowych, które zostały wykonane w serii 5000/7000.                                               | Gdy użytkownicy próbują uzyskać dostęp do danych, migracja pobierze dane z platformy Azure, w związku z tym ponoszenia kosztów pobierania danych.                                     |
| 2.   | Dane nie są migrowane po stronie hosta.                                                                                                     | Proces musi przestoju od początku i najnowszej kopii zapasowej są udostępniane na 8000 serii (można oszacować, za pomocą narzędzia migracji). |
| 3.   | Ten proces zachowuje wszystkie zasady, szablony przepustowości, szyfrowania i innych ustawień na urządzeniach serii 8000.                      | Dostęp użytkownika będzie przywrócić tylko danych udostępnianych przez użytkowników i nie będzie rehydrate cały zestaw danych.                                                  |
| 4.   | Ten proces wymaga dodatkowego czasu, aby przekonwertować wszystkie starsze kopie zapasowe na platformie Azure, które jest realizowane asynchronicznie bez wpływu na produkcji | Migracja jest możliwe tylko na poziomie konfiguracji chmury.  Jeden z woluminów w konfiguracji chmury, które nie mogą być migrowane osobno                       |

Migracja po stronie hosta umożliwia skonfigurowanie serii 8000 niezależnie i kopiowanie danych z urządzenia z serii 5000/7000 urządzeń z serii 8000. Jest to równoważne migrację danych z jednego urządzenia pamięci masowej do innego. Różnych narzędzi, takich jak Diskboss, robocopy służą do kopiowania danych.

|      | Specjaliści                                                                                                                      |Wady                                                                                                                                                                                                      |
|------|---------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1.   | Migracja może osiągany w to etapowo na podstawie przez wolumin.                                               | Poprzednie kopie zapasowe (podejmowaną w odniesieniu do serii 5000/7000) nie będą dostępne na urządzeniu serii 8000.                                                                                                       |
| 2.   | Umożliwia do konsolidacji danych na jedno konto magazynu Azure.                                                       | Pierwsza kopia zapasowa w chmurze w serii 8000 będzie trwać dłużej jako wszystkich danych na potrzeby serii 8000 do wykonania kopii zapasowej Azure.                                                                     |
| 3.   | Po pomyślnej migracji wszystkich danych jest lokalny na urządzeniu. Nie ma żadnych opóźnienia podczas uzyskiwania dostępu do danych. | Spowoduje zwiększenie użycia magazynu Azure, aż do usunięcia danych z urządzenia 5000/7000.                                                                                                        |
| 4.   |                                                                                                                           | Jeśli urządzenia z serii 7000/5000 ma dużą ilość danych, podczas migracji dane muszą zostać pobrane z platformy azure, które będzie powodowało kosztów i opóźnienia związane z pobieranie danych z platformy Azure |

Ten artykuł dotyczy tylko funkcji migracji z 5000/7000 do 8000 serii urządzenia. Aby uzyskać więcej informacji o migracji po stronie hosta, przejdź do [migracji z innych urządzeń magazynujących](http://download.microsoft.com/download/9/4/A/94AB8165-CCC4-430B-801B-9FD40C8DA340/Migrating Data to StorSimple Volumes_09-02-15.pdf).

## <a name="migration-prerequisites"></a>Wymagania wstępne dotyczące migracji

Poniżej przedstawiono wymagania wstępne migracji dla starszej wersji urządzenia serii 5000 i 7000 i urządzenie StorSimple 8000 series.

> [!IMPORTANT]
> Przejrzyj i spełnić wymagania wstępne migracji przed pliku żądania obsługi ze Support firmy Microsoft.

### <a name="for-the-50007000-series-device-source"></a>Dla urządzenia serii 5000/7000 (źródło)

Przed rozpoczęciem migracji upewnij się, że:

* Masz Twojej 5000 lub urządzeń z serii 7000 źródła urządzeń. urządzenie może być aktywne lub w dół.

    > [!IMPORTANT]
    > Zaleca się, że masz dostęp szeregowy do tego urządzenia w trakcie procesu migracji. Powinno być żadnych problemów z urządzeniami, dostęp szeregowy może pomóc w rozwiązywaniu problemów.

* Urządzenia źródłowego serii 5000 i 7000 działa v2.1.1.518 wersji oprogramowania lub nowszym. Wcześniejszych wersji nie są obsługiwane.
* Aby sprawdzić wersję, która działa z serii 5000 i 7000, Szukaj w prawym górnym rogu interfejsu użytkownika sieci Web. To powinien być wyświetlany wersji oprogramowania, na urządzeniu jest uruchomiona. W przypadku migracji z serii 5000 i 7000 powinna działać v2.1.1.518.

    ![Sprawdź wersję oprogramowania na starszych urządzeń](media/storsimple-8000-migrate-from-5000-7000/check-version-legacy-device1.png)

    * Jeśli Twoje urządzenie na żywo jest nieuruchomiona v2.1.1.518 lub później, przeprowadź uaktualnienie do wymaganej minimalnej wersji systemu. Aby uzyskać szczegółowe instrukcje, zapoznaj się [uaktualnić system do v2.1.1.518](http://onlinehelp.storsimple.com/111_Appliance/6_System_Upgrade_Guides/Current_(v2.1.1)/000_Software_Patch_Upgrade_Guide_v2.1.1.518).
    * Jeśli używasz v2.1.1.518, przejdź do interfejsu użytkownika, aby sprawdzić, czy są wszystkie powiadomienia niepowodzeń przywracania rejestru w sieci web. Jeśli przywracania rejestru nie powiodło się, należy uruchomić przywracanie rejestru. Aby uzyskać więcej informacji, przejdź do sekcji [uruchomić przywracanie rejestru](http://onlinehelp.storsimple.com/111_Appliance/2_User_Guides/1_Current_(v2.1.1)/1_Web_UI_User_Guide_WIP/2_Configuration/4_Cloud_Accounts/1_Cloud_Credentials#Restoring_Backup_Registry).
    * Jeśli masz urządzenie działa, który nie został uruchomiony v2.1.1.518, należy wykonać przejściu w tryb failover działa v2.1.1.518 urządzenie zastąpienia. Aby uzyskać szczegółowe instrukcje zapoznaj się DR urządzenia StorSimple serii 5000/7000.
    * Kopii zapasowej danych na urządzeniu, wykonując migawka w chmurze.
    * Sprawdź, czy wszystkie inne aktywne zadania tworzenia kopii zapasowej uruchomionych na urządzenia źródłowego. Dotyczy to również zadania na hoście konsoli ochrony danych StorSimple. Poczekaj, aż bieżące zadania do wykonania.


### <a name="for-the-8000-series-physical-device-target"></a>8000 serii urządzenia fizycznego (docelowy)

Przed rozpoczęciem migracji upewnij się, że:

* Urządzenia docelowego 8000 serii jest zarejestrowany i uruchomiona. Aby uzyskać więcej informacji, zobacz temat jak [wdrożenia urządzenia StorSimple w usłudze Menedżer StorSimple](storsimple-8000-deployment-walkthrough-u2.md).
* Urządzenie serii 8000 ma najnowszą StorSimple 8000 serii aktualizacji 5 zainstalowany i działa 6.3.9600.17845 lub nowszej wersji. Jeśli urządzenie nie ma zainstalowane najnowsze aktualizacje, należy zainstalować najnowsze aktualizacje, zanim będzie można kontynuować migracji. Aby uzyskać więcej informacji, zobacz temat jak [zainstalowania najnowszej aktualizacji na urządzeniu serii 8000](storsimple-8000-install-update-5.md).
* Twoja subskrypcja platformy Azure jest włączona dla migracji. Jeśli nie włączono subskrypcję, skontaktuj się z Microsoft Support Aby włączyć swoją subskrypcję do migracji.

### <a name="for-the-80108020-cloud-appliance-target"></a>Dla urządzenia 8010/8020 chmury (docelowy)

Upewnij się przed rozpoczęciem migracji:

* Urządzenia docelowego chmury jest zarejestrowany i uruchomiona. Aby uzyskać więcej informacji, zobacz temat jak [wdrażanie i zarządzanie nimi urządzenia chmury StorSimple](storsimple-8000-cloud-appliance-u2.md).
* Urządzenia chmury działa najnowsze StorSimple 8000 serii aktualizacji 5 wersji oprogramowania 6.3.9600.17845. Jeśli urządzenia chmury nie działa aktualizacji 5, należy utworzyć nowe urządzenia chmury aktualizacji 5 przed przystąpieniem do migracji. Aby uzyskać więcej informacji, zobacz temat jak [tworzenia urządzenia 8010/8020 chmury](storsimple-8000-cloud-appliance-u2.md).

### <a name="for-the-computer-running-storsimple-migration-tool"></a>Dla komputera z uruchomionym narzędzia migracji StorSimple

Narzędzie migracji StorSimple jest oparty na interfejsie użytkownika narzędzia umożliwiającą migrację danych z StorSimple serii 5000 7000 do urządzenia z serii 8000. Aby zainstalować narzędzie do migracji StorSimple, użyj komputera, który spełnia następujące wymagania.

Komputer ma połączenie z Internetem oraz:

* Następujące systemu operacyjnego
    * Windows 10.
    * Windows Server 2012 R2 (lub nowszej) do zainstalowania narzędzia do migracji StorSimple.
* .NET 4.5.2 został zainstalowany.
* Zawiera co najmniej 5 GB wolnego miejsca, aby zainstalować i użyć narzędzia.

> [!TIP]
> Jeśli urządzenia StorSimple podłączonego do hosta systemu Windows Server, na hoście systemu Windows Server można zainstalować narzędzie do migracji.

#### <a name="to-install-storsimple-migration-tool"></a>Aby zainstalować narzędzia migracji StorSimple

Wykonaj poniższe kroki, aby zainstalować narzędzia migracji StorSimple na komputerze.

1. Skopiuj folder _StorSimple8000SeriesMigrationTool_ do komputera z systemem Windows. Upewnij się, że dysk, gdy oprogramowanie jest kopiowany ma wystarczającą ilość miejsca.

    Otwórz plik konfiguracji narzędzia _StorSimple8000SeriesMigrationTool.exe.config_ w folderze. Oto fragment pliku.
    
    ```
        <add key="UserName" value="username@xyz.com" />
        <add key="SubscriptionName" value="YourSubscriptionName" />
        <add key="SubscriptionId" value="YourSubscriptionId" />
        <add key="TenantId" value="YourTenantId" />
        <add key="ResourceName" value="YourResourceName" />
        <add key="ResourceGroupName" value="YourResourceGroupName" />

    ```
2. Edytuj wartości odpowiadających kluczy i zamień:

    * `UserName`— Nazwa użytkownika Zaloguj się do portalu Azure.
    * `SubscriptionName and SubscriptionId`— Nazwy i IDENTYFIKATORA dla subskrypcji platformy Azure. W usłudze Menedżer StorSimple urządzenia strona początkowa, w obszarze **ogólne**, kliknij przycisk **właściwości**. Skopiuj nazwę subskrypcji i identyfikator subskrypcji skojarzonych z usługą.
    * `ResourceName`— Nazwa usługi Menedżer StorSimple urządzenia w portalu Azure. Także wyświetlany w obszarze właściwości usługi.
    * `ResourceGroup`— Nazwa grupy zasobów skojarzonych z usługą Menedżer StorSimple urządzenia w portalu Azure. Także wyświetlany w obszarze właściwości usługi.
    ![Sprawdź właściwości usługi dla urządzenia](media/storsimple-8000-migrate-from-5000-7000/check-service-properties1.png)
    * `TenantId`— Identyfikator usługi azure Active Directory dzierżawy w portalu Azure. Zaloguj się do systemu Microsoft Azure jako administrator. W portalu Microsoft Azure, kliknij przycisk **usługi Azure Active Directory**. W obszarze **Zarządzaj**, kliknij przycisk **właściwości**. Dzierżawy identyfikator jest wyświetlany w obszarze **identyfikator katalogu** pole.
    ![Sprawdź identyfikator dzierżawy usługi Azure Active Directory](media/storsimple-8000-migrate-from-5000-7000/check-tenantid-aad.png)

3.  Zapisz zmiany wprowadzone w pliku konfiguracji.
4.  Uruchom _StorSimple8000SeriesMigrationTool.exe_ można uruchomić narzędzie. Po wyświetleniu monitu o poświadczenia, podaj poświadczenia skojarzone z subskrypcją w portalu Azure. 
5.  Narzędzie do migracji StorSimple interfejsu użytkownika jest wyświetlany.
  

## <a name="next-steps"></a>Następne kroki
Instrukcje krok po kroku dotyczące sposobu pobierania [migracji danych z StorSimple serii 5000 7000 na urządzenie 8000 series](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b).
