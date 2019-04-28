---
title: Instalowanie aktualizacji Update 0.5 na macierz wirtualną StorSimple | Dokumentacja firmy Microsoft
description: Opisuje sposób używania interfejsu użytkownika sieci web rozwiązania StorSimple Virtual Array do stosowania aktualizacji za pomocą usługi Azure portal i poprawki — metoda
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61445424"
---
# <a name="install-update-05-on-your-storsimple-virtual-array"></a>Instalowanie aktualizacji Update 0.5 na rozwiązania StorSimple Virtual Array

## <a name="overview"></a>Omówienie

W tym artykule opisano kroki wymagane do zainstalowania aktualizacji Update 0.5 na rozwiązania StorSimple Virtual Array przy użyciu witryny Azure portal i za pomocą lokalnego Interfejsu w przeglądarce. Należy zastosować aktualizacje oprogramowania lub poprawek, aby zapewnić aktualność rozwiązania StorSimple Virtual Array.

Przed zastosowaniem aktualizacji zaleca się wykonanie woluminy lub udziały w tryb offline na hoście pierwszy i następnie urządzenia. Minimalizuje to ryzyko uszkodzenia danych. Po woluminy lub udziały są w trybie offline, należy również wziąć ręcznego tworzenia kopii zapasowych urządzenia.

> [!IMPORTANT]
> - Odnosi się do aktualizacji Update 0.5 **10.0.10290.0** wersję oprogramowania na urządzeniu. Aby uzyskać informacji na temat nowości w tej aktualizacji, przejdź do [informacje o wersji dla aktualizacji Update 0.5](storsimple-virtual-array-update-05-release-notes.md).
>
> - Jeśli korzystasz z aktualizacji 0.2 lub nowszej, zaleca się, że możesz zainstalować aktualizacje za pośrednictwem witryny Azure portal. Jeśli korzystasz z aktualizacji 0.1 lub wersji oprogramowania, musi być instalowanie aktualizacji Update 0.5 metodę poprawkę, za pomocą lokalnego Interfejsu w przeglądarce.
>
> - Należy pamiętać, który, instalowania aktualizacji lub poprawki powoduje ponowne uruchomienie urządzenia. Biorąc pod uwagę, że macierz wirtualna StorSimple jest urządzenie o jednym węźle, jest przerwane wszelkie operacje We/Wy w toku i urządzenie, wystąpi Przestój.

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Jeśli z aktualizacją Update 0,2 lub nowszy, zaleca się zainstalowanie aktualizacji w witrynie Azure portal. Procedury portalu wymaga od użytkownika skanowania, pobieranie i instalowanie aktualizacji. Ta procedura trwa około 7 minut. Wykonaj następujące czynności w celu zainstalowania aktualizacji lub poprawki.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

Po zakończeniu instalacji przejdź do usługi Menedżer urządzeń StorSimple. Wybierz **urządzeń** a następnie wybierz i kliknij urządzenie właśnie został zaktualizowany. Przejdź do **Ustawienia > Zarządzaj > aktualizacje urządzeń**. Wersja oprogramowania wyświetlane powinna być **10.0.10290.0**.

## <a name="use-the-local-web-ui"></a>Użyj lokalnego internetowego interfejsu użytkownika

Istnieją dwa kroki, korzystając z lokalnego Interfejsu w przeglądarce:

* Pobieranie aktualizacji lub poprawki
* Zainstalowanie aktualizacji lub poprawki

### <a name="download-the-update-or-the-hotfix"></a>Pobieranie aktualizacji lub poprawki

Wykonaj następujące kroki, aby pobrać aktualizację oprogramowania z Wykazu usługi Microsoft Update.

#### <a name="to-download-the-update-or-the-hotfix"></a>Aby pobrać aktualizacji lub poprawki

1. Uruchom program Internet Explorer i przejdź do [ https://catalog.update.microsoft.com ](https://catalog.update.microsoft.com).

2. Jeśli po raz pierwszy używasz Wykazu usługi Microsoft Update na danym komputerze, po wyświetleniu monitu o zainstalowanie dodatku Wykazu usługi Microsoft Update kliknij pozycję **Zainstaluj**.

3. W polu wyszukiwania wykazu usługi Microsoft Update wprowadź numer bazy wiedzy Knowledge Base (KB) poprawki, którą chcesz pobrać. Wprowadź **4021576** for Update 0.5, a następnie kliknij przycisk **wyszukiwania**.
   
    Lista poprawek zawierająca pojawia się na przykład **StorSimple Virtual Array Update 0,5**.
   
    ![Przeszukiwanie wykazu](./media/storsimple-virtual-array-install-update-05/download1.png)

4. Kliknij pozycję **Pobierz**. 

5. Powinien zostać wyświetlony dwa pliki do pobrania, *msu* i *cab* pliku. Pobierz każdego z tych plików do folderu. Folder można też skopiować do udziału sieciowego osiągalnego z urządzenia.

6. Otwórz folder, w którym znajdują się pliki.
    ![Pliki w pakiecie](./media/storsimple-virtual-array-install-update-05/update05folder.png)

    Zobaczysz:
    -  Plik pakietu autonomicznego usługi Microsoft Update `WindowsTH-KB3011067-x64`. Ten plik jest używany do aktualizacji oprogramowania urządzenia.
    - Plik pakietu agenta monitorowania w Genewie `GenevaMonitoringAgentPackageInstaller`. Ten plik jest używany do zaktualizowania agenta usługi (MDS) monitorowania i diagnostyki. Kliknij dwukrotnie plik cab. Msi jest wyświetlany. Wybierz plik, kliknij prawym przyciskiem myszy, a następnie **wyodrębnić** pliku. Użyjesz _.msi_ pliku do zaktualizowania agenta.

        ![Wyodrębnij plik aktualizacji agenta usług MDS](./media/storsimple-virtual-array-install-update-05/extract-geneva-monitoring-agent-installer.png)
        
    

### <a name="install-the-update-or-the-hotfix"></a>Zainstalowanie aktualizacji lub poprawki

Przed instalacją aktualizacji lub poprawki upewnij się, że aktualizacja lub poprawka pobierane lokalnie na hoście ani dostępne za pośrednictwem udziału sieciowego.

Ta metoda umożliwia instalowanie aktualizacji na urządzeniu z systemem GA lub zaktualizuj 0,1 wersje oprogramowania. Ta procedura ma mniej niż 2 minut. Wykonaj następujące czynności w celu zainstalowania aktualizacji lub poprawki.

#### <a name="to-install-the-update-or-the-hotfix"></a>Do zainstalowania aktualizacji lub poprawki

1. W lokalnym internetowym interfejsie użytkownika, przejdź do **konserwacji** > **aktualizacji oprogramowania**.
   
    ![aktualizowanie urządzenia](./media/storsimple-virtual-array-install-update-05/update1m.png)

2. W **ścieżka pliku aktualizacji**, wprowadź nazwę pliku aktualizacji lub poprawki. Możesz także przejść do lokalizacji pliku instalacyjnego aktualizacja lub poprawka umieszczony w udziale sieciowym. Kliknij przycisk **Zastosuj**.
   
    ![aktualizowanie urządzenia](./media/storsimple-virtual-array-install-update-05/update2m.png)

3. Zostanie wyświetlone ostrzeżenie. Po zastosowaniu aktualizacji i ponownym uruchomieniu urządzenia wystąpi Przestój, biorąc pod uwagę te jest urządzenie o jednym węźle. Kliknij ikonę znacznika wyboru.
   
   ![aktualizowanie urządzenia](./media/storsimple-virtual-array-install-update-05/update3m.png)

4. Aktualizacja zostanie uruchomiony. Po pomyślnym zaktualizowaniu urządzenia, jego uruchomieniu. Lokalny interfejs użytkownika jest niedostępny w tym czasie.
   
    ![aktualizowanie urządzenia](./media/storsimple-virtual-array-install-update-05/update5m.png)

5. Po ponownym uruchomieniu, nastąpi przekierowanie do **Zaloguj** strony. Aby zweryfikować, że oprogramowanie urządzenia został zaktualizowany w lokalnym internetowym interfejsie użytkownika, przejdź do **konserwacji** > **aktualizacji oprogramowania**. Wersja oprogramowania wyświetlane powinna być **10.0.0.0.0.10290.0** dla aktualizacji Update 0.5.
   
   > [!NOTE]
   > Firma Microsoft raport wersje oprogramowania w nieco inny sposób w lokalnym internetowym interfejsie użytkownika, jak i portalu Azure. Na przykład raporty lokalnego Interfejsu w przeglądarce **10.0.0.0.0.10290** i Azure portal raporty **10.0.10290.0** dla tej samej wersji.
   
    ![aktualizowanie urządzenia](./media/storsimple-virtual-array-install-update-05/update6m.png)

6. Następnym krokiem jest, aby zaktualizować agenta usług MDS. W **aktualizacji oprogramowania** strony, przejdź do **ścieżka pliku aktualizacji** i przejdź do `GenevaMonitoringAgentPackageInstaller.msi` pliku. Powtórz kroki 2 – 4. Po ponownym uruchomieniu macierzy wirtualnej, zaloguj się do lokalnego Interfejsu w przeglądarce.

Aktualizacja została zakończona.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [administrowanie rozwiązania StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

