---
title: Instalowanie aktualizacji Update 0.6 na macierz wirtualną StorSimple | Dokumentacja firmy Microsoft
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
ms.date: 05/18/2017
ms.author: alkohli
ms.openlocfilehash: 5f0be5d8378cd1640d3052f2e56c8161e2c0b203
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62116895"
---
# <a name="install-update-06-on-your-storsimple-virtual-array"></a>Instalowanie aktualizacji Update 0.6 na rozwiązania StorSimple Virtual Array

## <a name="overview"></a>Omówienie

W tym artykule opisano kroki wymagane do zainstalowania aktualizacji Update 0.6 na rozwiązania StorSimple Virtual Array przy użyciu witryny Azure portal i za pomocą lokalnego Interfejsu w przeglądarce. Możesz zastosować aktualizacje oprogramowania lub poprawek w celu zapewnienia aktualności rozwiązania StorSimple Virtual Array.

Przed zastosowaniem aktualizacji zaleca się wykonanie woluminy lub udziały w tryb offline na hoście pierwszy i następnie urządzenia. Minimalizuje to ryzyko uszkodzenia danych. Po woluminy lub udziały są w trybie offline, należy również wziąć ręcznego tworzenia kopii zapasowych urządzenia.

> [!IMPORTANT]
> - Odnosi się do aktualizacji Update 0.6 **10.0.10293.0** wersję oprogramowania na urządzeniu. Aby uzyskać informacji na temat nowości w tej aktualizacji, przejdź do [informacje o wersji dla aktualizacji Update 0.6](storsimple-virtual-array-update-06-release-notes.md).
>
> - Jeśli korzystasz z aktualizacji 0.2 lub nowszej, zaleca się, że możesz zainstalować aktualizacje za pośrednictwem witryny Azure portal. Jeśli korzystasz z aktualizacji 0.1 lub wersji oprogramowania, musi być instalowanie aktualizacji Update 0.6 metodę poprawkę, za pomocą lokalnego Interfejsu w przeglądarce.
>
> - Należy pamiętać, który, instalowania aktualizacji lub poprawki powoduje ponowne uruchomienie urządzenia. Biorąc pod uwagę, że macierz wirtualna StorSimple jest urządzenie o jednym węźle, jest przerwane wszelkie operacje We/Wy w toku i urządzenie, wystąpi Przestój.

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Jeśli z aktualizacją Update 0,2 lub nowszy, zaleca się zainstalowanie aktualizacji w witrynie Azure portal. Procedury portalu wymaga od użytkownika skanowania, pobieranie i instalowanie aktualizacji. Ta procedura trwa około 7 minut. Wykonaj następujące czynności w celu zainstalowania aktualizacji lub poprawki.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

Po zakończeniu instalacji przejdź do usługi Menedżer urządzeń StorSimple. Wybierz **urządzeń** a następnie wybierz i kliknij urządzenie właśnie został zaktualizowany. Przejdź do **Ustawienia > Zarządzaj > aktualizacje urządzeń**. Wersja oprogramowania wyświetlane powinna być **10.0.10293.0**.

## <a name="use-the-local-web-ui"></a>Użyj lokalnego internetowego interfejsu użytkownika

Istnieją dwa kroki, korzystając z lokalnego Interfejsu w przeglądarce:

* Pobieranie aktualizacji lub poprawki
* Zainstalowanie aktualizacji lub poprawki

### <a name="download-the-update-or-the-hotfix"></a>Pobieranie aktualizacji lub poprawki

Wykonaj następujące kroki, aby pobrać aktualizację oprogramowania z Wykazu usługi Microsoft Update.

#### <a name="to-download-the-update-or-the-hotfix"></a>Aby pobrać aktualizacji lub poprawki

1. Uruchom program Internet Explorer i przejdź do [ https://catalog.update.microsoft.com ](https://catalog.update.microsoft.com).

2. Jeśli korzystasz wykazu usługi Microsoft Update po raz pierwszy na tym komputerze, kliknij przycisk **zainstalować** monit o zainstalowanie dodatku wykazu usługi Microsoft Update.

3. W polu wyszukiwania wykazu usługi Microsoft Update wprowadź numer bazy wiedzy Knowledge Base (KB) poprawki, którą chcesz pobrać. Wprowadź **4023268** dla aktualizacji Update 0.6, a następnie kliknij przycisk **wyszukiwania**.
   
    Lista poprawek zawierająca pojawia się na przykład **StorSimple Virtual Array aktualizacji Update 0.6**.
   
    ![Przeszukiwanie wykazu](./media/storsimple-virtual-array-install-update-06/download1.png)

4. Kliknij pozycję **Pobierz**.

5. Powinieneś zobaczyć pięć plików do pobrania. Pobierz każdego z tych plików do folderu. Folder można też skopiować do udziału sieciowego osiągalnego z urządzenia.

6. Otwórz folder, w którym znajdują się pliki.
    ![Pliki w pakiecie](./media/storsimple-virtual-array-install-update-06/update06folder.png)

    Zobaczysz:
    -  Plik pakietu autonomicznego usługi Microsoft Update `WindowsTH-KB3011067-x64`. Ten plik jest używany do aktualizacji oprogramowania urządzenia.
    - Plik pakietu agenta monitorowania w Genewie `GenevaMonitoringAgentPackageInstaller`. Ten plik jest używany do zaktualizowania agenta usługi (MDS) monitorowania i diagnostyki. Kliknij dwukrotnie plik cab. A _.msi_ jest wyświetlana. Wybierz plik, kliknij prawym przyciskiem myszy, a następnie **wyodrębnić** pliku. Możesz użyć _.msi_ pliku do zaktualizowania agenta.

        ![Wyodrębnij plik aktualizacji agenta usług MDS](./media/storsimple-virtual-array-install-update-06/extract-geneva-monitoring-agent-installer.png)

        > [!IMPORTANT]
        > Nie trzeba zaktualizować agenta usług MDS, jeśli korzystasz z usługi StorSimple aktualizacji Update 0.5 (0.0.10293.0).

    - Trzy pliki, które zawierają krytyczne aktualizacje zabezpieczeń Windows, `windows8.1-kb4012213-x64`,`windows8.1-kb3205400-x64`, i `windows8.1-kb4019213-x64`.


### <a name="install-the-update-or-the-hotfix"></a>Zainstalowanie aktualizacji lub poprawki

Przed instalacją aktualizacji lub poprawki upewnij się, że aktualizacja lub poprawka pobierane lokalnie na hoście ani dostępne za pośrednictwem udziału sieciowego.

Ta metoda umożliwia instalowanie aktualizacji na urządzeniu z systemem GA lub zaktualizuj 0,1 wersje oprogramowania. Procedura ta zajmuje około 12 minut. Wykonaj następujące czynności w celu zainstalowania aktualizacji lub poprawki.

#### <a name="to-install-the-update-or-the-hotfix"></a>Do zainstalowania aktualizacji lub poprawki

1. W lokalnym internetowym interfejsie użytkownika, przejdź do **konserwacji** > **aktualizacji oprogramowania**. Zanotuj wersji oprogramowania, które są uruchomione. Jeśli używasz **10.0.10290.0**, nie musisz zaktualizować agenta usług MDS w kroku 6.
   
    ![aktualizowanie urządzenia](./media/storsimple-virtual-array-install-update-05/update1m.png)

2. W **ścieżka pliku aktualizacji**, wprowadź nazwę pliku aktualizacji lub poprawki. Możesz także przejść do lokalizacji pliku instalacyjnego aktualizacja lub poprawka umieszczony w udziale sieciowym. Kliknij przycisk **Zastosuj**.
   
    ![aktualizowanie urządzenia](./media/storsimple-virtual-array-install-update-05/update2m.png)

3. Zostanie wyświetlone ostrzeżenie. Biorąc pod uwagę macierz wirtualna jest urządzenie o jednym węźle, po zastosowaniu aktualizacji i ponownym uruchomieniu urządzenia wystąpi Przestój. Kliknij ikonę znacznika wyboru.
   
   ![aktualizowanie urządzenia](./media/storsimple-virtual-array-install-update-05/update3m.png)

4. Aktualizacja zostanie uruchomiony. Po pomyślnym zaktualizowaniu urządzenia, jego uruchomieniu. Lokalny interfejs użytkownika jest niedostępny w tym czasie.
   
    ![aktualizowanie urządzenia](./media/storsimple-virtual-array-install-update-05/update5m.png)

5. Po ponownym uruchomieniu, nastąpi przekierowanie do **Zaloguj** strony. Aby zweryfikować, że oprogramowanie urządzenia został zaktualizowany w lokalnym internetowym interfejsie użytkownika, przejdź do **konserwacji** > **aktualizacji oprogramowania**. Wersja oprogramowania wyświetlane powinna być **10.0.0.0.0.10293** dla aktualizacji Update 0.6.
   
   > [!NOTE]
   > Firma Microsoft raport wersje oprogramowania w nieco inny sposób w lokalnym internetowym interfejsie użytkownika, jak i portalu Azure. Na przykład raporty lokalnego Interfejsu w przeglądarce **10.0.0.0.0.10293** i Azure portal raporty **10.0.10293.0** dla tej samej wersji.
   
    ![aktualizowanie urządzenia](./media/storsimple-virtual-array-install-update-06/update6m.png)

6. Pomiń ten krok, jakby były uruchamiane StorSimple Virtual Array Update 0,5 (**10.0.10290.0**) przed zastosowaniem tej aktualizacji. Należy zanotować wersji oprogramowania w kroku 1 przed rozpoczęciem aktualizacji. W przypadku uruchomienia aktualizacji Update 0.5 agenta usług MDS jest już aktualne.

    Wersja oprogramowania przed aktualizacją 0,5, następnym krokiem dla Ciebie czy do zaktualizowania agenta usług MDS. W **aktualizacji oprogramowania** strony, przejdź do **ścieżka pliku aktualizacji** i przejdź do `GenevaMonitoringAgentPackageInstaller.msi` pliku. Powtórz kroki 2 – 4. Po ponownym uruchomieniu macierzy wirtualnej, zaloguj się do lokalnego Interfejsu w przeglądarce.

7. Powtórz krok 2 – 4 w celu zainstalowania zabezpieczeń Windows poprawki, przy użyciu plików `windows8.1-kb4012213-x64`,`windows8.1-kb3205400-x64`, i `windows8.1-kb4019213-x64`. Macierz wirtualna ponownego uruchomienia po zakończeniu każdej instalacji i musisz zalogować się do lokalnego Interfejsu w przeglądarce.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [administrowanie rozwiązania StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

