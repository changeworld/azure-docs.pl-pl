---
title: Microsoft Azure StorSimple Data Manager w interfejsie użytkownika | Dokumentacja firmy Microsoft
description: Opisuje sposób używania interfejsu użytkownika dla usługi StorSimple Data Manager
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: fa897b4b77f7f5869eab2ba2e7db9afbd84febfa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60631502"
---
# <a name="manage-the-storsimple-data-manager-service-in-azure-portal"></a>Zarządzanie usługą StorSimple Data Manager w witrynie Azure portal

W tym artykule wyjaśniono, jak można użyć interfejsu użytkownika usługi StorSimple Data Manager do przekształcania danych znajdujących się na urządzeniach z serii StorSimple 8000. Przekształcone dane mogą być następnie używane przez inne usługi platformy Azure, takich jak Azure Media Services, Azure HDInsight, Azure Machine Learning i Azure Search.


## <a name="use-storsimple-data-transformation"></a>Użyj przekształcania danych w usłudze StorSimple

Usługa StorSimple Data Manager jest zasobów w ramach danych, które zostanie uruchomiony, przekształcania. Usługa Data Transformation umożliwia przekształcanie danych z formatu usługi StorSimple do natywnego formatu obiektów blob lub Azure Files. Do przekształcania danych StorSimple w formacie natywnym, należy określić szczegóły dotyczące urządzenia serii StorSimple 8000 oraz interesujących, który chcesz przekształcić danych.

### <a name="create-a-storsimple-data-manager-service"></a>Utwórz usługę StorSimple Data Manager

Wykonaj poniższe kroki, aby utworzyć usługę StorSimple Data Manager.

1. Użyj poświadczeń konta Microsoft, aby zalogować się do witryny [Azure Portal](https://portal.azure.com/).

2. Kliknij przycisk **+ Utwórz zasób** i wyszukiwania usługi StorSimple Data Manager.

    ![Utwórz usługę StorSimple Data Manager 1](./media/storsimple-data-manager-ui/create-service-1.png)

3. Usługa StorSimple Data Manager a następnie kliknij przycisk **Utwórz**.
    
    ![Utwórz usługę StorSimple Data Manager 2](./media/storsimple-data-manager-ui/create-service-3.png)

3. Dla nowej usługi określ następujące ustawienia:

   1. Podaj unikatową **nazwa usługi** dla usługi StorSimple Data Manager. To jest przyjazna nazwa, która może służyć do identyfikowania usługi. Nazwa może zawierać od 3 do 24 znaków, które mogą być literami, cyframi i łącznikami. Nazwa musi zaczynać i kończyć się literą lub cyfrą.

   2. Wybierz **subskrypcji** z listy rozwijanej. Subskrypcja jest połączona z kontem rozliczeniowym. To pole jest wypełniane automatycznie (i nie można wybierać) Jeśli masz tylko jedną subskrypcję.

   3. Wybierz istniejącą grupę zasobów lub Utwórz nową grupę. Aby uzyskać więcej informacji, zobacz [Grupy zasobów na platformie Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/).

   4. Określ **lokalizacji** dla Twojej usługi, która zawiera wszystkie konta magazynu i usługi StorSimple Data Manager. Usługi Menedżer urządzeń StorSimple, usługi Data Manager i skojarzonego konta magazynu powinny być w obsługiwanych regionach.
    
   5. Aby uzyskać link do tej usługi na pulpicie nawigacyjnym, wybierz **Przypnij do pulpitu nawigacyjnego**.
    
   6. Kliknij pozycję **Utwórz**.

      ![Utwórz usługę StorSimple Data Manager 3](./media/storsimple-data-manager-ui/create-service-4.png)

Tworzenie usługi potrwa kilka minut. Zostanie wyświetlone powiadomienie po pomyślnym utworzeniu usługi i Nowa usługa jest wyświetlana.

### <a name="create-a-data-transformation-job-definition"></a>Utwórz definicję zadania przekształcania danych

W ramach usługi StorSimple Data Manager musisz utworzyć definicję zadania przekształcania danych. Definicja zadania określa szczegółowych danych StorSimple, która Cię interesuje przeniesienie do konta magazynu w formacie natywnym. Po utworzeniu definicji zadania, następnie można uruchomić to zadanie ponownie ze środowiskiem uruchomieniowym w różnych ustawień.

Wykonaj poniższe kroki, aby utworzyć definicję zadania.

1. Przejdź do usługi, który został utworzony. Przejdź do **zarządzania > definicje zadań**.

2. Kliknij przycisk **+ definicji zadania**.

    ![Kliknij pozycję + definicja zadania](./media/storsimple-data-manager-ui/create-job-definition-1.png)

3. Podaj nazwę dla definicji zadania. Nazwa może zawierać od 3 do 63 znaków. Nazwa może zawierać wielkie i małe litery, cyfry i łączniki.

4. Określ lokalizację, w którym działa zadanie. Ta lokalizacja może być inny niż lokalizacji, w której wdrożona usługa.

5. Kliknij przycisk **źródła** określić repozytorium źródła danych.

    ![Konfigurowanie repozytorium danych źródła](./media/storsimple-data-manager-ui/create-job-definition-2.png)

6. Ponieważ jest to nowa usługa Data Manager, są skonfigurowane żadnych repozytoriów danych. W **Konfigurowanie źródła danych**, określ szczegółów urządzenia serii StorSimple 8000 oraz interesujących danych.

   Aby dodać Menedżera urządzeń StorSimple jako repozytorium danych, kliknij przycisk **Dodaj nowe** w liście rozwijanej repozytorium danych, a następnie kliknij przycisk **Dodaj repozytorium danych**.

    ![Dodawanie nowego repozytorium danych](./media/storsimple-data-manager-ui/create-job-definition-3.png)
  
   1. Wybierz **serii StorSimple 8000 Menedżera** jako typ repozytorium danych.
    
   2. Wprowadź przyjazną nazwę dla repozytorium źródła danych.
    
   3. Z listy rozwijanej wybierz subskrypcję skojarzoną z usługi Menedżer urządzeń StorSimple.
    
   4. Podaj nazwę Menedżer urządzeń StorSimple dla **zasobów**.

   5. Wprowadź **szyfrowania danych usługi** klucza dla usługi Menedżer urządzeń StorSimple. 

      ![Konfigurowanie repozytorium danych źródła 1](./media/storsimple-data-manager-ui/create-job-definition-4.png)

      Kliknij przycisk **OK** po zakończeniu. Spowoduje to zapisanie repozytorium danych. Ponowne użycie tego Menedżera urządzeń StorSimple w innych definicjach zadań bez konieczności ponownego wprowadzania tych parametrów. Zajmuje kilka sekund po kliknięciu **OK** repozytorium danych nowo utworzone źródło pojawienie się na liście rozwijanej.

7. Z listy rozwijanej dla **repozytorium danych prognostycznych**, wybierz repozytorium danych został utworzony. 

   1. Wprowadź nazwę urządzenia StorSimple 8000 series, zawierający potrzebne dane.

   2. Określ nazwę woluminu, znajdującej się na urządzeniu StorSimple, które ma Twoje dane.

   3. W **filtru** podsekcję, wprowadź katalogu głównego, który zawiera dane zainteresowanie _\MyRootDirectory\Data_ formatu. Litery dysków takie _\C:\Data_ nie są obsługiwane. Można również dodać wszelkie filtry pliku.

   4. Działa usługa przekształcania danych na dane, które wypchnięty na platformie Azure przy użyciu migawek. Po uruchomieniu tego zadania, można wybrać utworzyć kopię zapasową za każdym razem, gdy to zadanie zostanie uruchomione (nad najnowszych danych) lub za pomocą ostatniej kopii zapasowej istniejącej w chmurze (Jeśli pracujesz nad niektórych danych archiwalnych).

   5. Kliknij przycisk **OK**.

      ![Konfigurowanie repozytorium danych źródła 2](./media/storsimple-data-manager-ui/create-job-definition-8.png)

8. Następnie konieczne jest skonfigurowanie repozytorium danych docelowych. Wybieranie kont magazynu, aby umieścić pliki do obiektów blob w ramach tego konta. Z listy rozwijanej wybierz **Dodaj nowe** i następnie **konfigurowania ustawień**.

9. Wybierz typ repozytorium docelowym, który chcesz dodać, a inne parametry skojarzone z repozytorium.

    Jeśli wybierzesz docelowy typ konta magazynu, można określić przyjazną nazwę subskrypcji (wybierz taki sam jak usługi lub inne) oraz konto magazynu.
        ![Konfigurowanie repozytorium danych 1](./media/storsimple-data-manager-ui/create-job-definition-10.png)

    Gdy zadanie zostanie uruchomione, zostanie utworzona kolejka magazynu. Jest ona wypełniana przy użyciu komunikatów dotyczących przekształconych obiektów blob, gdy będą gotowe. Nazwa tej kolejki jest taka sama jak nazwa definicji zadania.
    
10. Po dodaniu repozytorium danych, poczekaj kilka minut.
    
    1. Wybierz repozytorium utworzone jako miejsce docelowe z listy rozwijanej w **nazwa konta docelowego**.

    2. Wybierz typ magazynu jako obiekty BLOB i plików. Określ nazwę kontenera magazynu, w którym znajduje się przekształcone dane. Kliknij przycisk **OK**.

        ![Konfigurowanie docelowego konta magazynu dla repozytorium danych](./media/storsimple-data-manager-ui/create-job-definition-16.png)

11. Możesz również sprawdzić możliwość przedstawienia szacowany czas trwania zadania przed uruchomieniem zadania. Kliknij przycisk **OK** do tworzenia definicji zadania. Definicja zadania zostało zakończone. Możesz użyć tej definicji zadania, wiele razy przy użyciu interfejsu użytkownika, przy użyciu środowiskiem uruchomieniowym w różnych ustawień.

    ![Definicja ukończonego zadania](./media/storsimple-data-manager-ui/create-job-definition-13.png)

    Definicja nowo utworzonego zadania jest dodawany do listy definicji zadań dla tej usługi.

### <a name="run-the-job-definition"></a>Uruchom definicję zadania

Zawsze, gdy potrzebujesz przenieść dane z rozwiązania StorSimple do konta magazynu, który określono w definicji zadania, należy ją uruchomić. W czasie wykonywania niektóre parametry można określić inaczej. Dostępne są następujące czynności:

1. Wybierz swoją usługę StorSimple Data Manager i przejdź do **zarządzania > definicje zadań**. Wybierz, a następnie kliknij przycisk definicji zadania, które chcesz uruchomić.
     
     ![Uruchamianie zadania Uruchom 1](./media/storsimple-data-manager-ui/start-job-run1.png)

2. Kliknij przycisk **Uruchom teraz**.
     
     ![Uruchamianie zadania, uruchom 2](./media/storsimple-data-manager-ui/start-job-run2.png)

3. Kliknij przycisk **parametrów uruchomieniowych** Aby zmodyfikować ustawienia, które możesz chcieć zmienić wykonywania tego zadania. Kliknij przycisk **OK** a następnie kliknij przycisk **Uruchom** do uruchomienia zadania.

    ![Uruchamianie zadania uruchamiania 3](./media/storsimple-data-manager-ui/start-job-run3.png)

4. Aby monitorować tego zadania, przejdź do **zadania** w sieci usługi StorSimple Data Manager. Oprócz monitorowania w **zadań** bloku można również wykrywać w kolejce magazynu, gdy komunikat zostanie dodany za każdym razem, gdy plik zostanie przeniesiony z rozwiązania StorSimple do konta magazynu.

    ![Uruchamianie zadania uruchamiania 4](./media/storsimple-data-manager-ui/start-job-run4.png)


## <a name="next-steps"></a>Kolejne kroki

[Użycie zestawu SDK platformy .NET w celu uruchomienia zadań usługi StorSimple Data Manager](storsimple-data-manager-dotnet-jobs.md).