---
title: Migrowanie danych na urządzeniu z serii StorSimple serii 5000-7000 do urządzenia z serii 8000| Dokumenty firmy Microsoft
description: Zawiera omówienie i wymagania wstępne funkcji migracji.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60631694"
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-8000-series-device"></a>Migrowanie danych z urządzenia z serii StorSimple serii 5000-7000 do urządzenia z serii 8000

> [!IMPORTANT]
> - 31 lipca 2019 r. seria StorSimple 5000/7000 osiągnie status końca wsparcia (EOS). Firma Microsoft zaleca, aby klienci serii StorSimple 5000/7000 migrować do jednej z alternatyw opisanych w dokumencie.
> - Migracja jest obecnie operacją wspomaganą. Jeśli zamierzasz przeprowadzić migrację danych z urządzenia z serii StorSimple 5000-7000 na urządzenie z serii 8000, musisz zaplanować migrację za pomocą pomocy technicznej firmy Microsoft. Pomoc techniczna firmy Microsoft włączy następnie subskrypcję do migracji. Aby uzyskać więcej informacji, zobacz jak [otworzyć bilet pomocy technicznej](storsimple-8000-contact-microsoft-support.md).
> - Po złożeniu żądania usługi może upłynąć kilka tygodni, aby wykonać plan migracji i faktycznie rozpocząć migrację.
> - Przed skontaktowaniem się z pomocą techniczną firmy Microsoft należy przejrzeć i ukończyć [wymagania wstępne dotyczące migracji wskazane](#migration-prerequisites) w tym artykule.

## <a name="overview"></a>Omówienie

W tym artykule przedstawiono funkcję migracji, która umożliwia klientom serii StorSimple 5000-7000 migrację danych do urządzenia fizycznego z serii StorSimple 8000 lub urządzenia w chmurze 8010/8020. W tym artykule znajduje się również łącze do przewodnika krok po kroku do pobrania krok po kroku wymaganych do migracji danych ze starszego urządzenia z serii 5000-7000 do urządzenia fizycznego lub urządzenia w chmurze z serii 8000.

Ten artykuł dotyczy zarówno lokalnego urządzenia z serii 8000, jak i urządzenia StorSimple Cloud Appliance.


## <a name="migration-feature-versus-host-side-migration"></a>Funkcja migracji a migracja po stronie hosta

Dane można przenosić za pomocą funkcji migracji lub migracji po stronie hosta. W tej sekcji opisano specyfikę każdej metody, w tym plusy i minusy. Te informacje służą do ustalenia, którą metodę chcesz przeprowadzić w celu migracji danych.

Funkcja migracji symuluje proces odzyskiwania po awarii (DR) z serii 7000/5000 do serii 8000. Ta funkcja umożliwia migrację danych z formatu serii 5000/7000 do formatu serii 8000 na platformie Azure. Proces migracji jest inicjowany za pomocą narzędzia StorSimple Migration. Narzędzie rozpoczyna pobieranie i konwersję metadanych kopii zapasowej na urządzeniu z serii 8000, a następnie używa najnowszej kopii zapasowej, aby odsłonić woluminy na urządzeniu.

|      | Zalety                                                                                                                                     |Wady                                                                                                                                                              |
|------|-------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1.   | Proces migracji zachowuje historię kopii zapasowych, które zostały wykonane w serii 5000/7000.                                               | Gdy użytkownicy próbują uzyskać dostęp do danych, ta migracja spowoduje pobranie danych z platformy Azure, co poniesie koszty pobierania danych.                                     |
| 2.   | Żadne dane nie są migrowane po stronie hosta.                                                                                                     | Proces wymaga przestojów między rozpoczęciem kopii zapasowej a najnowszą tworzeniem kopii zapasowych na powierzchni serii 8000 (można oszacować za pomocą narzędzia migracji). |
| 3.   | Ten proces zachowuje wszystkie zasady, szablony przepustowości, szyfrowanie i inne ustawienia na urządzeniach z serii 8000.                      | Dostęp użytkownika przywróci tylko dane, do które uzyskują dostęp użytkownicy i nie będzie ponownie nawadniać całego zestawu danych.                                                  |
| 4.   | Ten proces wymaga dodatkowego czasu na konwersję wszystkich starszych kopii zapasowych na platformie Azure, które są wykonywane asynchronicznie bez wpływu na produkcję | Migrację można przeprowadzić tylko na poziomie konfiguracji chmury.  Pojedynczych woluminów w konfiguracji chmury nie można migrować oddzielnie                       |

Migracja po stronie hosta umożliwia samodzielne konfigurowanie serii 8000 i kopiowanie danych z urządzenia z serii 5000/7000 do urządzenia z serii 8000. Jest to równoważne migracji danych z jednego urządzenia magazynującego do innego. Do kopiowania danych służy wiele narzędzi, takich jak Diskboss, robocopy.

|      | Zalety                                                                                                                      |Wady                                                                                                                                                                                                      |
|------|---------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1.   | Do migracji można podchodzić etapowo na zasadzie "wolumin po woluminie".                                               | Poprzednie kopie zapasowe (wykonane z serii 5000/7000) nie będą dostępne na urządzeniu z serii 8000.                                                                                                       |
| 2.   | Umożliwia konsolidację danych na jednym koncie magazynu na platformie Azure.                                                       | Pierwsza kopia zapasowa w chmurze w serii 8000 zajmie więcej czasu, ponieważ wszystkie dane z serii 8000 muszą być archiwizowane na platformie Azure.                                                                     |
| 3.   | Po pomyślnej migracji wszystkie dane są lokalne na urządzeniu. Nie ma żadnych opóźnień podczas uzyskiwania dostępu do danych. | Zużycie magazynu platformy Azure wzrośnie, dopóki dane nie zostaną usunięte z urządzenia 5000/7000.                                                                                                        |
| 4.   |                                                                                                                           | Jeśli urządzenie z serii 7000/5000 ma dużą ilość danych, podczas migracji dane te muszą zostać pobrane z platformy Azure, co spowoduje koszty i opóźnienia związane z pobieraniem danych z platformy Azure |

W tym artykule skupiono się tylko na funkcji migracji z urządzenia z serii 5000/7000 do 8000. Aby uzyskać więcej informacji na temat migracji po stronie hosta, przejdź do [przejścia Migracja z innych urządzeń pamięci masowej](https://download.microsoft.com/download/9/4/A/94AB8165-CCC4-430B-801B-9FD40C8DA340/Migrating%20Data%20to%20StorSimple%20Volumes_09-02-15.pdf).

## <a name="migration-prerequisites"></a>Wymagania wstępne dotyczące migracji

Poniżej przedstawiono wymagania wstępne dotyczące migracji starszego urządzenia z serii 5000 lub 7000 oraz urządzenia StorSimple z serii 8000.

> [!IMPORTANT]
> Przejrzyj i uzupełnij wymagania wstępne dotyczące migracji przed złożeniem żądania usługi w pomocy technicznej firmy Microsoft.

### <a name="for-the-50007000-series-device-source"></a>Dla urządzenia serii 5000/7000 (źródło)

Przed rozpoczęciem migracji upewnij się, że:

* Masz urządzenie źródłowe serii 5000 lub 7000; urządzenie może być aktywne lub w dół.

    > [!IMPORTANT]
    > Zaleca się, że masz dostęp szeregowy do tego urządzenia w całym procesie migracji. W przypadku jakichkolwiek problemów z urządzeniem, dostęp szeregowy może pomóc w rozwiązywaniu problemów.

* Urządzenie źródłowe serii 5000 lub 7000 jest uruchomione w wersji oprogramowania w wersji 2.1.1.518 lub nowszej. Starsze wersje nie są obsługiwane.
* Aby sprawdzić wersję, że seria 5000 lub 7000 jest uruchomiona, spójrz w prawym górnym rogu interfejsu użytkownika sieci Web. Powinna ona wyświetlać wersję oprogramowania, w którą działa urządzenie. W przypadku migracji seria 5000 lub 7000 powinna być uruchomiona w wersji 2.1.1.518.

    ![Sprawdzanie wersji oprogramowania na starszym urządzeniu](media/storsimple-8000-migrate-from-5000-7000/check-version-legacy-device1.png)

    * Jeśli na urządzeniu na żywo nie jest uruchomiony w wersji 2.1.1.518 lub nowszej, uaktualnij system do wymaganej wersji minimalnej. Szczegółowe instrukcje można znaleźć w [: Uaktualnianie systemu do wersji 2.1.1.518](http://onlinehelp.storsimple.com/111_Appliance/6_System_Upgrade_Guides/Current_(v2.1.1)/000_Software_Patch_Upgrade_Guide_v2.1.1.518).
    * Jeśli używasz wersji 2.1.1.518, przejdź do interfejsu użytkownika sieci Web, aby sprawdzić, czy są jakieś powiadomienia o niepowodzeniach przywracania rejestru. Jeśli przywracanie rejestru nie powiodło się, uruchom przywracanie rejestru. Aby uzyskać więcej informacji, przejdź do sposobu [uruchamiania przywracania rejestru](http://onlinehelp.storsimple.com/111_Appliance/2_User_Guides/1_Current_(v2.1.1)/1_Web_UI_User_Guide_WIP/2_Configuration/4_Cloud_Accounts/1_Cloud_Credentials#Restoring_Backup_Registry).
    * Jeśli masz urządzenie w dół, który nie był uruchomiony w wersji 2.1.1.518, należy wykonać pracy awaryjnej do urządzenia zastępczego, który jest uruchomiony w wersji 2.1.1.518. Szczegółowe instrukcje można znaleźć w dr urządzenia StorSimple serii 5000/7000.
    * Tworzenie kopii zapasowej danych urządzenia przez zrobienie migawki w chmurze.
    * Sprawdź, czy nie ma innych aktywnych zadań tworzenia kopii zapasowych, które są uruchomione na urządzeniu źródłowym. Obejmuje to zadania na hoście Konsoli ochrony danych StorSimple. Poczekaj na zakończenie bieżących zadań.


### <a name="for-the-8000-series-physical-device-target"></a>Dla urządzenia fizycznego serii 8000 (cel)

Przed rozpoczęciem migracji upewnij się, że:

* Urządzenie docelowe z serii 8000 jest zarejestrowane i uruchomione. Aby uzyskać więcej informacji, zobacz jak [wdrożyć urządzenie StorSimple za pomocą usługi StorSimple Manager](storsimple-8000-deployment-walkthrough-u2.md).
* Urządzenie z serii 8000 ma zainstalowaną najnowszą aktualizację 5 z serii StorSimple 8000 i jest dostępne w wersji 6.3.9600.17845 lub nowszej. Jeśli urządzenie nie ma zainstalowanych najnowszych aktualizacji, należy zainstalować najnowsze aktualizacje, zanim będzie można kontynuować migrację. Aby uzyskać więcej informacji, zobacz, jak [zainstalować najnowszą aktualizację na urządzeniu z serii 8000](storsimple-8000-install-update-5.md).
* Twoja subskrypcja platformy Azure jest włączona do migracji. Jeśli subskrypcja nie jest włączona, skontaktuj się z pomocą techniczną firmy Microsoft, aby włączyć subskrypcję w celu migracji.

### <a name="for-the-80108020-cloud-appliance-target"></a>Dla urządzenia w chmurze 8010/8020 (docelowe)

Przed rozpoczęciem migracji upewnij się, że:

* Docelowe urządzenie w chmurze jest zarejestrowane i uruchomione. Aby uzyskać więcej informacji, zobacz jak [wdrożyć urządzenie StorSimple Cloud Appliance i zarządzać nim.](storsimple-8000-cloud-appliance-u2.md)
* Urządzenie w chmurze działa w najnowszej wersji oprogramowania StorSimple 8000 Series Update 5 w wersji 6.3.9600.17845. Jeśli na urządzeniu w chmurze nie jest uruchomiona aktualizacja 5, przed kontynuowaniem migracji należy utworzyć nowe urządzenie w chmurze aktualizacji 5. Aby uzyskać więcej informacji, zobacz jak [utworzyć urządzenie w chmurze 8010/8020](storsimple-8000-cloud-appliance-u2.md).

### <a name="for-the-computer-running-storsimple-migration-tool"></a>Dla komputera z uruchomionym narzędziem StorSimple Migration

Narzędzie StorSimple Migration to narzędzie oparte na interfejsie użytkownika, które umożliwia migrację danych z serii StorSimple 5000-7000 do urządzenia z serii 8000. Aby zainstalować narzędzie StorSimple Migration, użyj komputera spełniającego następujące wymagania.

Komputer ma łączność z Internetem i:

* Działa następujący system operacyjny
    * Windows 10.
    * System Windows Server 2012 R2 (lub nowszy) w celu zainstalowania narzędzia Do migracji StorSimple.
* Ma zainstalowany plik .NET 4.5.2.
* Ma co najmniej 5 GB wolnego miejsca do zainstalowania i korzystania z narzędzia.

> [!TIP]
> Jeśli urządzenie StorSimple jest połączone z hostem systemu Windows Server, narzędzie do migracji można zainstalować na komputerze-hoście systemu Windows Server.

#### <a name="to-install-storsimple-migration-tool"></a>Aby zainstalować narzędzie Do migracji StorSimple

Wykonaj następujące czynności, aby zainstalować narzędzie StorSimple Migration na komputerze.

1. Skopiuj folder _StorSimple8000SeriesMigrationTool_ na komputer z systemem Windows. Upewnij się, że dysk, na którym jest kopiowane oprogramowanie, ma wystarczająco dużo miejsca.

    Otwórz plik konfiguracyjny narzędzia _StorSimple8000SeriesMigrationTool.exe.config_ w folderze. Oto fragment pliku.
    
    ```xml
        <add key="UserName" value="username@xyz.com" />
        <add key="SubscriptionName" value="YourSubscriptionName" />
        <add key="SubscriptionId" value="YourSubscriptionId" />
        <add key="TenantId" value="YourTenantId" />
        <add key="ResourceName" value="YourResourceName" />
        <add key="ResourceGroupName" value="YourResourceGroupName" />

    ```
2. Edytuj wartości odpowiadające klawiszom i zastąp na:

    * `UserName`– Nazwa użytkownika, aby zalogować się do witryny Azure portal.
    * `SubscriptionName and SubscriptionId`– Nazwa i identyfikator subskrypcji platformy Azure. Na stronie docelowej usługi Menedżer urządzeń StorSimple w obszarze **Ogólne**kliknij pozycję **Właściwości**. Skopiuj nazwę subskrypcji i identyfikator subskrypcji skojarzony z usługą.
    * `ResourceName`– Nazwa usługi StorSimple Device Manager w witrynie Azure portal. Pokazano również w obszarze właściwości usługi.
    * `ResourceGroup`– Nazwa grupy zasobów skojarzonych z usługą StorSimple Device Manager w witrynie Azure portal. Pokazano również w obszarze właściwości usługi.
    ![Sprawdź właściwości usługi dla urządzenia docelowego](media/storsimple-8000-migrate-from-5000-7000/check-service-properties1.png)
    * `TenantId`— identyfikator dzierżawy usługi Azure Active Directory w witrynie Azure portal. Zaloguj się do platformy Microsoft Azure jako administrator. W portalu microsoft azure kliknij pozycję **Azure Active Directory**. W obszarze **Zarządzanie** kliknij przycisk **Właściwości**. Identyfikator dzierżawy jest wyświetlany w polu **Identyfikator katalogu.**
    ![Sprawdzanie identyfikatora dzierżawy usługi Azure Active Directory](media/storsimple-8000-migrate-from-5000-7000/check-tenantid-aad.png)

3.  Zapisz zmiany wprowadzone w pliku konfiguracyjnym.
4.  Uruchom _storsimple8000SeriesMigrationTool.exe,_ aby uruchomić narzędzie. Po wyświetleniu monitu o poświadczenia podaj poświadczenia skojarzone z subskrypcją w witrynie Azure portal. 
5.  Zostanie wyświetlony interfejs użytkownika narzędzia StorSimple Migration.
  

## <a name="next-steps"></a>Następne kroki
Pobierz instrukcje krok po kroku dotyczące [migracji danych z serii StorSimple 5000-7000 do urządzenia z serii 8000.](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b)
