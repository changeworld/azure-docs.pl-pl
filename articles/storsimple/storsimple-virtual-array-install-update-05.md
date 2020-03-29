---
title: Zainstaluj aktualizację 0.5 na tablicy wirtualnej StorSimple | Dokumenty firmy Microsoft
description: W tym artykule opisano, jak używać interfejsu użytkownika sieci Web tablicy wirtualnej StorSimple do stosowania aktualizacji przy użyciu portalu Azure i metody poprawki
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
ms.date: 05/10/2017
ms.author: alkohli
ms.openlocfilehash: e09ff4bcbc141b1a1f80bc278918a291639c1885
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61445424"
---
# <a name="install-update-05-on-your-storsimple-virtual-array"></a>Instalowanie aktualizacji 0.5 na tablicy wirtualnej StorSimple

## <a name="overview"></a>Omówienie

W tym artykule opisano kroki wymagane do zainstalowania aktualizacji 0.5 w tablicy wirtualnej StorSimple za pośrednictwem lokalnego interfejsu użytkownika sieci web i za pośrednictwem witryny Azure portal. Aby mieć aktualną aktualizację wirtualną tablicy StorSimple, należy zastosować aktualizacje oprogramowania lub poprawki.

Przed zastosowaniem aktualizacji zaleca się najpierw przetraktować woluminy lub udziały w trybie offline na hoście, a następnie na urządzeniu. Minimalizuje to ryzyko uszkodzenia danych. Po woluminy lub udziały są w trybie offline, należy również wykonać ręczną kopię zapasową urządzenia.

> [!IMPORTANT]
> - Aktualizacja 0.5 odpowiada wersji oprogramowania **10.0.10290.0** na urządzeniu. Aby uzyskać informacje o nowościach w tej aktualizacji, przejdź do [informacji o wersji aktualizacji 0.5](storsimple-virtual-array-update-05-release-notes.md).
>
> - Jeśli korzystasz z aktualizacji 0.2 lub nowszej, zaleca się zainstalowanie aktualizacji za pośrednictwem witryny Azure portal. Jeśli używasz wersji oprogramowania Aktualizacji 0.1 lub GA, musisz użyć metody poprawki za pośrednictwem lokalnego interfejsu użytkownika sieci web, aby zainstalować aktualizację 0.5.
>
> - Należy pamiętać, że zainstalowanie aktualizacji lub poprawki uruchamia ponownie urządzenie. Biorąc pod uwagę, że StorSimple Virtual Array jest urządzeniem z jednym węzłem, wszelkie we/wy w toku jest zakłócony i urządzenie doświadcza przestojów.

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Jeśli jest uruchomiona aktualizacja 0.2 lub nowsza, zaleca się zainstalowanie aktualizacji za pośrednictwem witryny Azure portal. Procedura portalu wymaga od użytkownika skanowania, pobierania, a następnie instalowania aktualizacji. Ta procedura trwa około 7 minut. Wykonaj następujące kroki, aby zainstalować aktualizację lub poprawkę.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

Po zakończeniu instalacji przejdź do usługi StorSimple Device Manager. Wybierz **pozycję Urządzenia,** a następnie wybierz pozycję i kliknij właśnie zaktualizowane urządzenie. Przejdź do **ustawień > zarządzanie aktualizacjami urządzeń >**. Wyświetlana wersja oprogramowania powinna mieć **10.0.10290.0**.

## <a name="use-the-local-web-ui"></a>Korzystanie z lokalnego interfejsu użytkownika sieci Web

Istnieją dwa kroki podczas korzystania z lokalnego interfejsu użytkownika sieci Web:

* Pobierz aktualizację lub poprawkę
* Instalowanie aktualizacji lub poprawki

### <a name="download-the-update-or-the-hotfix"></a>Pobierz aktualizację lub poprawkę

Wykonaj następujące kroki, aby pobrać aktualizację oprogramowania z Wykazu usługi Microsoft Update.

#### <a name="to-download-the-update-or-the-hotfix"></a>Aby pobrać aktualizację lub poprawkę

1. Uruchom program Internet [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com)Explorer i przejdź do pliku .

2. Jeśli po raz pierwszy używasz Wykazu usługi Microsoft Update na danym komputerze, po wyświetleniu monitu o zainstalowanie dodatku Wykazu usługi Microsoft Update kliknij pozycję **Zainstaluj**.

3. W polu wyszukiwania katalogu Microsoft Update należy wprowadzić numer bazy wiedzy (KB) poprawki, którą chcesz pobrać. Wprowadź **4021576** dla aktualizacji 0.5, a następnie kliknij przycisk **Wyszukaj**.
   
    Zostanie wyświetlona lista poprawek, na przykład **StorSimple Virtual Array Update 0.5**.
   
    ![Przeszukiwanie wykazu](./media/storsimple-virtual-array-install-update-05/download1.png)

4. Kliknij **pozycję Pobierz**. 

5. Powinny być widoczne dwa pliki do pobrania, *plik .msu* i plik *cab.* Pobierz każdy z tych plików do folderu. Folder można też skopiować do udziału sieciowego osiągalnego z urządzenia.

6. Otwórz folder, w którym znajdują się pliki.
    ![Pliki w pakiecie](./media/storsimple-virtual-array-install-update-05/update05folder.png)

    Zobaczysz:
    -  Plik `WindowsTH-KB3011067-x64`pakietu autonomicznego usługi Microsoft Update . Ten plik służy do aktualizacji oprogramowania urządzenia.
    - Genewski `GenevaMonitoringAgentPackageInstaller`plik pakietu agenta monitorowania . Ten plik służy do aktualizacji agenta usługi monitorowania i diagnostyki (MDS). Kliknij dwukrotnie plik kabiny. Zostanie wyświetlony plik msi. Zaznacz plik, kliknij prawym przyciskiem myszy, a następnie **wyodrębnij** plik. Użyjesz pliku _msi,_ aby zaktualizować agenta.

        ![Wyodrębnianie pliku aktualizacji agenta MDS](./media/storsimple-virtual-array-install-update-05/extract-geneva-monitoring-agent-installer.png)
        
    

### <a name="install-the-update-or-the-hotfix"></a>Instalowanie aktualizacji lub poprawki

Przed instalacją aktualizacji lub poprawki upewnij się, że aktualizacja lub poprawka zostały pobrane lokalnie na hoście lub dostępne za pośrednictwem udziału sieciowego.

Ta metoda służy do instalowania aktualizacji na urządzeniu z systemem GA lub Aktualizacja 0.1 wersje oprogramowania. Ta procedura trwa mniej niż 2 minuty. Wykonaj następujące kroki, aby zainstalować aktualizację lub poprawkę.

#### <a name="to-install-the-update-or-the-hotfix"></a>Aby zainstalować aktualizację lub poprawkę

1. W lokalnym interfejsie użytkownika sieci Web przejdź do aktualizacji**oprogramowania** **konserwacji** > .
   
    ![aktualizowanie urządzenia](./media/storsimple-virtual-array-install-update-05/update1m.png)

2. W **polu Aktualizuj ścieżkę pliku**wprowadź nazwę pliku aktualizacji lub poprawki. Można również przejść do pliku instalacji aktualizacji lub poprawki, jeśli są umieszczane w udziale sieciowym. Kliknij przycisk **Zastosuj**.
   
    ![aktualizowanie urządzenia](./media/storsimple-virtual-array-install-update-05/update2m.png)

3. Zostanie wyświetlone ostrzeżenie. Biorąc pod uwagę, że jest to urządzenie z jednym węzłem, po zastosowaniu aktualizacji urządzenie zostanie ponownie uruchomione i nastąpi przestoje. Kliknij ikonę wyboru.
   
   ![aktualizowanie urządzenia](./media/storsimple-virtual-array-install-update-05/update3m.png)

4. Rozpocznie się aktualizacja. Po pomyślnym zaktualizowaniu urządzenia zostanie ponownie uruchomione. Lokalny interfejs użytkownika nie jest dostępny w tym czasie.
   
    ![aktualizowanie urządzenia](./media/storsimple-virtual-array-install-update-05/update5m.png)

5. Po zakończeniu ponownego uruchomienia zostaniesz przesuń do strony **Zaloguj się.** Aby sprawdzić, czy oprogramowanie urządzenia zostało zaktualizowane, w lokalnym interfejsie użytkownika sieci Web przejdź do **witryny Konserwacja** > **oprogramowania Update**. Wyświetlana wersja oprogramowania powinna być **10.0.0.0.0.10290.0** dla aktualizacji 0.5.
   
   > [!NOTE]
   > Firma Microsoft raportuje wersje oprogramowania w nieco inny sposób w lokalnym interfejsie użytkownika sieci web i witrynie Azure portal. Na przykład lokalny interfejs użytkownika sieci web raportuje **10.0.0.0.0.10290** i witryny Azure portal raporty **10.0.10290.0** dla tej samej wersji.
   
    ![aktualizowanie urządzenia](./media/storsimple-virtual-array-install-update-05/update6m.png)

6. Następnym krokiem jest zaktualizowanie agenta MDS. Na stronie **Aktualizacja oprogramowania** przejdź do **ścieżki aktualizacji** `GenevaMonitoringAgentPackageInstaller.msi` pliku i przejdź do pliku. Powtórz kroki 2-4. Po ponownym uruchomieniu tablicy wirtualnej zaloguj się do lokalnego interfejsu użytkownika sieci web.

Aktualizacja została zakończona.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [administrowaniu tablicą wirtualną StorSimple](storsimple-ova-web-ui-admin.md).

