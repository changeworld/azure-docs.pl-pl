---
title: Zainstaluj aktualizację 0,6 w macierzy wirtualnego StorSimple | Dokumentacja firmy Microsoft
description: Informacje dotyczące używania interfejsu użytkownika sieci web tablicy wirtualnego StorSimple do stosowania aktualizacji za pomocą metody Azure portal i poprawki
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
ms.date: 05/18/2017
ms.author: alkohli
ms.openlocfilehash: 111976cd684561f5bc63b92f09a20470fe3036d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875907"
---
# <a name="install-update-06-on-your-storsimple-virtual-array"></a>Zainstaluj aktualizację 0,6 na tablica wirtualnego StorSimple

## <a name="overview"></a>Omówienie

W tym artykule opisano kroki wymagane do zainstalowania aktualizacji 0,6 tablica wirtualnego StorSimple, za pośrednictwem lokalnego interfejsu użytkownika sieci web i portalu Azure. Należy zastosować aktualizacje oprogramowania lub poprawek, aby zapewnić aktualność tablica wirtualne StorSimple.

Przed zainstalowaniem aktualizacji zalecamy wykonanie woluminy lub udziały w trybie offline na hoście pierwszy, a następnie urządzenia. Pozwala to zmniejszyć możliwości uszkodzenie danych. Po woluminy lub udziały są w trybie offline, należy również wziąć ręcznego tworzenia kopii zapasowych urządzenia.

> [!IMPORTANT]
> - Aktualizacja 0,6 odpowiada **10.0.10293.0** wersji oprogramowania na urządzeniu. Aby uzyskać informacji na temat nowości w ramach tej aktualizacji, przejdź do [informacje o wersji dla aktualizacji 0,6](storsimple-virtual-array-update-06-release-notes.md).
>
> - Jeśli używasz wersji Update 0.2 lub później, zaleca się, że musisz zainstalować aktualizacje za pomocą portalu Azure. Jeśli używasz Update 0.1 lub GA wersje oprogramowania, należy użyć metody poprawek za pomocą lokalnego interfejsu użytkownika sieci web do zainstalowania aktualizacji 0,6.
>
> - Należy pamiętać, polegające na zainstalowanie aktualizacji lub poprawki ponownym uruchomieniu urządzenia. Biorąc pod uwagę, że tablica wirtualne StorSimple jest urządzeniem jeden węzeł, wszystkie operacje We/Wy w toku jest zakłócona i urządzenia napotyka przestoju.

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Z aktualizacji, 0,2 i nowszymi wersjami, zaleca się zainstalowanie aktualizacji za pośrednictwem portalu Azure. Procedury portalu wymaga od użytkownika skanowania, Pobierz i zainstaluj aktualizacje. Ta procedura trwa około 7 minut do wykonania. Wykonaj poniższe kroki, aby zainstalować aktualizacja lub poprawka.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

Po zakończeniu instalacji, przejdź do usługi Menedżer StorSimple urządzenia. Wybierz **urządzeń** , a następnie wybierz i kliknij przycisk po zaktualizowaniu urządzenia. Przejdź do **Ustawienia > Zarządzaj > aktualizacji urządzenia**. Wersja oprogramowania wyświetlane **10.0.10293.0**.

## <a name="use-the-local-web-ui"></a>Użyj lokalnego interfejsu użytkownika sieci web

Istnieją dwa kroki, korzystając z lokalnego interfejsu użytkownika sieci web:

* Pobieranie aktualizacji lub poprawek
* Zainstalowanie aktualizacji lub poprawek

### <a name="download-the-update-or-the-hotfix"></a>Pobieranie aktualizacji lub poprawek

Wykonaj następujące kroki, aby pobrać aktualizację oprogramowania z Wykazu usługi Microsoft Update.

#### <a name="to-download-the-update-or-the-hotfix"></a>Aby pobrać aktualizacji lub poprawek

1. Uruchom program Internet Explorer i przejdź pod adres [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com).

2. Jeśli korzystasz z wykazu usługi Microsoft Update po raz pierwszy na tym komputerze, kliknij przycisk **zainstalować** monit o zainstalowanie dodatku wykazu usługi Microsoft Update.

3. W polu wyszukiwania z wykazu usługi Microsoft Update wprowadź numer bazy wiedzy Knowledge Base (KB) poprawki, którą chcesz pobrać. Wprowadź **4023268** aktualizacja 0,6, a następnie kliknij przycisk **wyszukiwania**.
   
    Poprawka zostanie wyświetlona na liście, na przykład **aktualizacji tablicy wirtualne StorSimple 0,6**.
   
    ![Przeszukiwanie wykazu](./media/storsimple-virtual-array-install-update-06/download1.png)

4. Kliknij pozycję **Pobierz**.

5. Powinny pojawić się pięć plików do pobrania. Pobieranie każdej z tych plików w folderze. Folder można też skopiować do udziału sieciowego osiągalnego z urządzenia.

6. Otwórz folder, w którym znajdują się pliki.
    ![Pliki w pakiecie](./media/storsimple-virtual-array-install-update-06/update06folder.png)

    Zobacz:
    -  Plik pakietu autonomicznego aktualizacji firmy Microsoft `WindowsTH-KB3011067-x64`. Ten plik jest używany do aktualizacji oprogramowania urządzenia.
    - Plik pakietu agenta monitorowania serwera Geneva `GenevaMonitoringAgentPackageInstaller`. Ten plik jest używany do aktualizacji agenta usługi (MDS) monitorowania i diagnostyki. Kliknij dwukrotnie plik cab. A _.msi_ jest wyświetlany. Wybierz plik, kliknij prawym przyciskiem myszy, a następnie **wyodrębnić** pliku. Możesz użyć _.msi_ plik, aby zaktualizować agenta.

        ![Wyodrębnij plik aktualizacji agenta usług MDS](./media/storsimple-virtual-array-install-update-06/extract-geneva-monitoring-agent-installer.png)

        > [!IMPORTANT]
        > Nie trzeba zaktualizować agenta MDS, jeśli używasz wersji StorSimple Update 0,5 (0.0.10293.0).

    - Trzy pliki, które zawierają krytycznych aktualizacji zabezpieczeń systemu Windows, `windows8.1-kb4012213-x64`,`windows8.1-kb3205400-x64`, i `windows8.1-kb4019213-x64`.


### <a name="install-the-update-or-the-hotfix"></a>Zainstalowanie aktualizacji lub poprawek

Przed instalacją aktualizacji lub poprawki upewnij się, że aktualizacja lub poprawka pobierane lokalnie na hoście lub dostępny za pośrednictwem udziału sieciowego.

Ta metoda umożliwia instalowanie aktualizacji na urządzeniu z systemem GA lub zaktualizuj 0,1 wersje oprogramowania. Ta procedura może zająć około 12 minut. Wykonaj poniższe kroki, aby zainstalować aktualizacja lub poprawka.

#### <a name="to-install-the-update-or-the-hotfix"></a>Do zainstalowania aktualizacji lub poprawek

1. W lokalnej sieci web interfejsu użytkownika, przejdź do **konserwacji** > **aktualizacji oprogramowania**. Zanotuj wersji oprogramowania, na którym są uruchomione. Jeśli używasz **10.0.10290.0**, nie musisz zaktualizować agenta usług MDS w kroku 6.
   
    ![aktualizowanie urządzenia](./media/storsimple-virtual-array-install-update-05/update1m.png)

2. W **ścieżka pliku aktualizacji**, wprowadź nazwę pliku dla aktualizacji lub poprawek. Możesz również przejść do lokalizacji pliku instalacyjnego aktualizacja lub poprawka umieszczony w udziale sieciowym. Kliknij przycisk **Zastosuj**.
   
    ![aktualizowanie urządzenia](./media/storsimple-virtual-array-install-update-05/update2m.png)

3. Zostanie wyświetlone ostrzeżenie. Podana tablica wirtualnego jest urządzeniem jednego węzła, po zastosowaniu aktualizacji, ponownym uruchomieniu urządzenia i brak przestojów. Kliknij ikonę znacznika wyboru.
   
   ![aktualizowanie urządzenia](./media/storsimple-virtual-array-install-update-05/update3m.png)

4. Aktualizacja zostanie uruchomiony. Po pomyślnym zaktualizowaniu urządzenia ponownego uruchomienia. Lokalnego interfejsu użytkownika nie jest dostępny w tym przedziale czasu.
   
    ![aktualizowanie urządzenia](./media/storsimple-virtual-array-install-update-05/update5m.png)

5. Po ponownym uruchomieniu komputera, zostają przeniesieni do **Zaloguj** strony. Aby zweryfikować, że oprogramowanie urządzenia został zaktualizowany w lokalnej sieci web interfejsu użytkownika, przejdź do **konserwacji** > **aktualizacji oprogramowania**. Wersja oprogramowania wyświetlane **10.0.0.0.0.10293** 0,6 aktualizacji.
   
   > [!NOTE]
   > Wersje oprogramowania Microsoft raport w sposób nieco inne w lokalnym interfejsu użytkownika sieci web i portalu Azure. Na przykład raporty lokalnego interfejsu użytkownika sieci web **10.0.0.0.0.10293** i raporty portalu Azure **10.0.10293.0** dla tej samej wersji.
   
    ![aktualizowanie urządzenia](./media/storsimple-virtual-array-install-update-06/update6m.png)

6. Pomiń ten krok w przypadku korzystania z aktualizacji tablicy wirtualne StorSimple 0,5 (**10.0.10290.0**) przed zastosowaniem tej aktualizacji. Należy zanotować wersji oprogramowania w kroku 1 przed rozpoczęciem aktualizacji. Podczas uruchamiania aktualizacji 0,5, agenta usług MDS jest już aktualny.

    Jeśli wersja oprogramowania przed aktualizacją 0,5, następnym krokiem dla Ciebie jest aktualizacja agenta usług MDS. W **aktualizacji oprogramowania** strony, przejdź do **ścieżka pliku aktualizacji** i przejdź do `GenevaMonitoringAgentPackageInstaller.msi` pliku. Powtórz kroki od 2 do 4. Po ponownym uruchomieniu wirtualnego tablicy, zaloguj się do lokalnego interfejsu użytkownika sieci web.

7. Powtórz kroki od 2 do 4 do zainstalowania zabezpieczenia systemu Windows poprawki przy użyciu plików `windows8.1-kb4012213-x64`,`windows8.1-kb3205400-x64`, i `windows8.1-kb4019213-x64`. Wirtualne tablicy ponownych uruchomień po zakończeniu każdej instalacji i należy zalogować się do lokalnego interfejsu użytkownika sieci web.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [administrowanie tablica wirtualnego StorSimple](storsimple-ova-web-ui-admin.md).

