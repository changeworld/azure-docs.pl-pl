---
title: Interfejs programu Data Manager platformy Microsoft Azure StorSimple | Dokumentacja firmy Microsoft
description: Informacje dotyczące używania usługi Menedżer StorSimple danych interfejsu użytkownika
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
ms.openlocfilehash: d704cf8e6840c6a7b0a637c404d421f9f1497c46
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2018
ms.locfileid: "27862261"
---
# <a name="manage-the-storsimple-data-manager-service-in-azure-portal"></a>Zarządzanie usługą Menedżer StorSimple danych w portalu Azure

W tym artykule opisano sposób korzystania interfejsu użytkownika Menedżera danych StorSimple do przekształcania danych znajdującej się na urządzeń z serii StorSimple 8000. Przekształcone dane mogą być następnie używane przez innych usług Azure, takich jak usługi Azure Media Services, Azure HDInsight uczenie maszynowe Azure i usługi Azure Search.


## <a name="use-storsimple-data-transformation"></a>Za pomocą przekształcania danych StorSimple

Menedżer StorSimple danych jest zasobów w ramach których dane transformacji zostanie uruchomiony. Usługi Data Transformation umożliwia przekształcanie danych z formatu StorSimple do formatu macierzystego w obiektach blob lub pliki Azure. Aby przekształcić danych formatu macierzystego StorSimple, należy określić szczegółowe informacje o urządzeniu serii StorSimple 8000 i potrzebne dane do przekształcania.

### <a name="create-a-storsimple-data-manager-service"></a>Utwórz usługę Menedżer StorSimple danych

Wykonaj poniższe kroki, aby utworzyć usługę Menedżer StorSimple danych.

1. Użyj poświadczeń konta Microsoft, aby zalogować się do witryny [Azure Portal](https://portal.azure.com/).

2. Kliknij przycisk **+ Utwórz zasób** i wyszukiwania StorSimple Data Manager.

    ![Utwórz usługę Menedżer StorSimple danych 1](./media/storsimple-data-manager-ui/create-service-1.png)

3. Kliknij pozycję Menedżer StorSimple danych, a następnie kliknij przycisk **Utwórz**.
    
    ![Utwórz usługę Menedżer StorSimple dane 2](./media/storsimple-data-manager-ui/create-service-3.png)

3. Dla nowej usługi określ następujące ustawienia:

    1. Podaj unikatową **nazwa usługi** dla Menedżera StorSimple danych. To jest przyjazna nazwa, która może służyć do identyfikowania usługi. Nazwa może zawierać od 3 do 24 znaków, które mogą być litery, cyfry i łączniki. Nazwa musi zaczynać i kończyć się literą lub cyfrą.

    2. Wybierz **subskrypcji** z listy rozwijanej. Subskrypcja jest połączona z kontem rozliczeniowym. To pole jest automatycznie wypełnione (i bez możliwości zaznaczania) Jeśli masz tylko jedną subskrypcję.

    3. Wybierz istniejącą grupę zasobów lub Utwórz nową grupę. Aby uzyskać więcej informacji, zobacz [Grupy zasobów na platformie Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/).

    4. Określ **lokalizacji** dla Twojej usługi, która przechowuje kont magazynu i usługi Menedżer StorSimple danych. Usługi Menedżer StorSimple urządzenie, usługa Data Manager i skojarzonego konta magazynu powinny być w obsługiwanych regionów.
    
    5. Aby uzyskać link do tej usługi na pulpicie nawigacyjnym, wybierz **Przypnij do pulpitu nawigacyjnego**.
    
    6. Kliknij przycisk **Utwórz**.

    ![Utwórz usługę Menedżer StorSimple dane 3](./media/storsimple-data-manager-ui/create-service-4.png)

Tworzenie usługi potrwa kilka minut. Po pomyślnym utworzeniu usługi i nowej usługi zostanie wyświetlony zostanie wyświetlone powiadomienie.

### <a name="create-a-data-transformation-job-definition"></a>Tworzenie definicji zadania przekształcania danych

W ramach usługi Menedżer StorSimple danych należy utworzyć definicję zadania przekształcania danych. Definicji zadania określa szczegóły danych StorSimple, które planuje się do konta magazynu w formacie native. Po utworzeniu definicji zadania, następnie można uruchomić to zadanie ponownie przy użyciu ustawień innego środowiska wykonawczego.

Wykonaj poniższe kroki, aby utworzyć definicji zadania.

1. Przejdź do usługi, który został utworzony. Przejdź do **zarządzania > definicje zadań**.

2. Kliknij przycisk **+ definicji zadania**.

    ![Kliknij pozycję + definicji zadania](./media/storsimple-data-manager-ui/create-job-definition-1.png)

3. Podaj nazwę definicji zadania. Nazwa może być od 3 do 63 znaków. Nazwa może zawierać wielkie i małe litery, cyfry i łączniki.

4. Określ lokalizację, w którym zadanie jest uruchomiona. Ta lokalizacja może być inna niż lokalizacja, w której wdrażana jest usługa.

5. Kliknij przycisk **źródła** określić repozytorium źródła danych.

    ![Konfigurowanie źródła danych repozytorium](./media/storsimple-data-manager-ui/create-job-definition-2.png)

6. Ponieważ jest to nowa usługa Data Manager, są skonfigurowane nie repozytoria danych. W **Konfigurowanie źródła danych**, określ szczegóły urządzenia serii StorSimple 8000 i potrzebne dane.

   Aby dodać menedżera urządzenia StorSimple jako repozytorium danych, kliknij przycisk **Dodaj nowe** danych repozytorium z listy rozwijanej, a następnie kliknij polecenie **Dodaj repozytorium danych**.

    ![Dodaj nowe repozytorium danych](./media/storsimple-data-manager-ui/create-job-definition-3.png)
  
    1. Wybierz **serii StorSimple 8000 Menedżera** jako typ danych repozytorium.
    
    2. Wprowadź przyjazną nazwę dla repozytorium źródła danych.
    
    3. Z listy rozwijanej wybierz subskrypcję skojarzoną z usługą Menedżer urządzeń StorSimple.
    
    4. Podaj nazwę Menedżera urządzeń StorSimple dla **zasobów**.

    5. Wprowadź **szyfrowania danych usługi** klucza dla usługi Menedżer StorSimple urządzenia. 

    ![Konfigurowanie źródła danych repozytorium 1](./media/storsimple-data-manager-ui/create-job-definition-4.png)

    Kliknij przycisk **OK** po zakończeniu. Spowoduje to zapisanie danych repozytorium. Ponowne użycie tego Menedżera urządzeń StorSimple w inne definicje zadań bez konieczności ponownego wprowadzania tych parametrów. Trwa kilka sekund po kliknięciu **OK** dla nowo utworzonego źródła danych repozytorium wyświetlani na liście rozwijanej.

7. Z listy rozwijanej dla **repozytorium danych**, wybierz repozytorium danych został utworzony. 

    1. Wprowadź nazwę urządzenia serii StorSimple 8000, zawierający potrzebne dane.

    2. Określ nazwę woluminu znajdującej się na urządzeniu StorSimple, zawierający dane zainteresowań.

    3. W **filtru** podsekcji, wprowadź w katalogu głównym, który zawiera dane zainteresowanie _\MyRootDirectory\Data_ format. Takie litery dysków _\C:\Data_ nie są obsługiwane. Można również dodać wszystkie filtry plików.

    4. Działania usługi przekształcania danych na dane wypychana do platformy Azure za pomocą migawek. Po uruchomieniu tego zadania można wykonać kopię zapasową za każdym razem, gdy to zadanie jest uruchamiane (do pracy najnowszych danych) lub użyj istniejącej ostatniej kopii zapasowej w chmurze (Jeśli pracujesz nad niektórych danych archiwalnych).

    5. Kliknij przycisk **OK**.

    ![Konfigurowanie źródła danych repozytorium 2](./media/storsimple-data-manager-ui/create-job-definition-8.png)

8. Następnie należy skonfigurować repozytorium danych docelowych. Wybieranie kont magazynu, aby umieścić pliki do obiektów blob w tym kontem. Na liście rozwijanej wybierz **Dodaj nowe** , a następnie **skonfigurować ustawienia**.

9. Wybierz typ repozytorium docelowych, które chcesz dodać, a inne parametry skojarzone z repozytorium.

    W przypadku wybrania elementu docelowego typu konta magazynu można określić przyjazną nazwę subskrypcji (wybierz taki sam jak usługi lub innych), a konto magazynu.
        ![Konfigurowanie docelowej danych repozytorium 1](./media/storsimple-data-manager-ui/create-job-definition-10.png)

    Kolejki magazynu jest tworzone po uruchomieniu zadania. Jest ona wypełniana przy użyciu komunikatów dotyczących przekształconych obiektów blob, gdy będą gotowe. Nazwa tej kolejki jest taka sama jak nazwa definicji zadania.
    
10. Po dodaniu repozytorium danych, zaczekaj kilka minut.
    
    1. Wybierz jako lokalizację docelową z listy rozwijanej w repozytorium utworzony **nazwa konta docelowego**.

    2. Wybierz typ magazynu obiektów blob lub plików. Określ nazwę kontenera magazynu, w którym znajduje się przekształcone dane. Kliknij przycisk **OK**.

        ![Skonfiguruj docelowe konto magazynu repozytorium danych](./media/storsimple-data-manager-ui/create-job-definition-16.png)

11. Można również sprawdzić opcję, aby przedstawić szacunkową czas trwania zadania przed uruchomieniem zadania. Kliknij przycisk **OK** do tworzenia definicji zadania. Definicja zadania jest teraz ukończona. Można użyć tej definicji zadania wiele razy przy użyciu interfejsu użytkownika z ustawieniami innego środowiska wykonawczego.

    ![Definicja ukończenia zadania](./media/storsimple-data-manager-ui/create-job-definition-13.png)

    Definicja nowo utworzone zadanie jest dodane do listy definicji zadania dla tej usługi.

### <a name="run-the-job-definition"></a>Uruchom definicji zadania

Zawsze, gdy trzeba przenieść dane z StorSimple do konta magazynu, które zostały określone w definicji zadania, należy ją uruchomić. W czasie wykonywania niektóre parametry można określić inaczej. Dostępne są następujące kroki:

1. Wybierz usługę Menedżer StorSimple danych i przejdź do **zarządzania > definicje zadań**. Wybierz, a następnie kliknij przycisk definicji zadania, które chcesz uruchomić.
     
     ![Uruchom zadanie było uruchamiane 1](./media/storsimple-data-manager-ui/start-job-run1.png)

2. Kliknij przycisk **Uruchom teraz**.
     
     ![Uruchom zadanie było uruchamiane 2](./media/storsimple-data-manager-ui/start-job-run2.png)

3. Kliknij przycisk **parametrów uruchomieniowych** można zmodyfikować wszystkie ustawienia, które możesz chcieć zmienić wykonywania tego zadania. Kliknij przycisk **OK** , a następnie kliknij przycisk **Uruchom** można uruchomić zadania.

    ![Uruchom zadanie było uruchamiane 3](./media/storsimple-data-manager-ui/start-job-run3.png)

4. Aby monitorować zadanie, przejdź do **zadania** w Menedżera StorSimple danych. Oprócz monitorowania w **zadania** bloku można również wykrywać w kolejce magazynu, gdy wiadomość zostanie dodany za każdym razem, gdy plik zostanie przeniesiony z StorSimple do konta magazynu.

    ![Uruchom zadanie było uruchamiane 4](./media/storsimple-data-manager-ui/start-job-run4.png)


## <a name="next-steps"></a>Następne kroki

[Użyć zestawu .NET SDK w celu uruchomienia zadania Menedżer StorSimple danych](storsimple-data-manager-dotnet-jobs.md).