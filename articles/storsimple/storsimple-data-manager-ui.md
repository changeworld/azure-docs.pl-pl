---
title: Interfejs użytkownika usługi Microsoft Azure StorSimple Data Manager
description: W tym artykule opisano sposób korzystania z interfejsu użytkownika usługi StorSimple Data Manager
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 85be49ad88ac62d90235c3da6b89b0da6a11487c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78933756"
---
# <a name="manage-the-storsimple-data-manager-service-in-azure-portal"></a>Zarządzanie usługą StorSimple Data Manager w witrynie Azure portal

W tym artykule wyjaśniono, jak można użyć interfejsu użytkownika StorSimple Data Manager do przekształcenia danych przechowywanych na urządzeniach z serii StorSimple 8000. Przekształcone dane mogą być następnie używane przez inne usługi platformy Azure, takie jak Usługi Azure Media Services, Usługi Azure HDInsight, Usługa Azure Machine Learning i Azure Cognitive Search.


## <a name="use-storsimple-data-transformation"></a>Użyj transformacji danych StorSimple

StorSimple Data Manager jest zasobem, w którym transformacja danych jest tworzone. Usługa przekształcania danych umożliwia przekształcanie danych z formatu StorSimple do formatu macierzystego w obiektach blob lub usługa Azure Files. Aby przekształcić storsimple danych formatu macierzystego, należy określić szczegóły dotyczące urządzenia storsimple serii 8000 i dane interesujące, które chcesz przekształcić.

### <a name="create-a-storsimple-data-manager-service"></a>Tworzenie usługi StorSimple Data Manager

Wykonaj następujące kroki, aby utworzyć usługę StorSimple Data Manager.

1. Użyj poświadczeń konta Microsoft, aby zalogować się do witryny [Azure Portal](https://portal.azure.com/).

2. Kliknij **+ Utwórz zasób** i wyszukaj Menedżera danych StorSimple.

    ![Tworzenie usługi Menedżera danych StorSimple 1](./media/storsimple-data-manager-ui/create-service-1.png)

3. Kliknij pozycję StorSimple Data Manager, a następnie kliknij pozycję **Utwórz**.
    
    ![Tworzenie usługi StorSimple Data Manager 2](./media/storsimple-data-manager-ui/create-service-3.png)

3. W przypadku nowej usługi należy określić następujące kwestie:

   1. Podaj unikatową **nazwę usługi** dla Menedżera danych StorSimple. To jest przyjazna nazwa, która może służyć do identyfikowania usługi. Nazwa może zawierać od 3 do 24 znaków, które mogą być literami, cyframi i łącznikami. Nazwa musi zaczynać i kończyć się literą lub cyfrą.

   2. Wybierz **subskrypcję** z listy rozwijanej. Subskrypcja jest połączona z kontem rozliczeniowym. To pole jest wypełniane automatycznie (i nie można ich wybrać), jeśli masz tylko jedną subskrypcję.

   3. Wybierz istniejącą grupę zasobów lub utwórz nową grupę. Aby uzyskać więcej informacji, zobacz [Grupy zasobów na platformie Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/).

   4. Określ **lokalizację** usługi, w której znajdują się konta magazynu i usługa StorSimple Data Manager. Usługa Menedżera urządzeń StorSimple, usługa Menedżer danych i skojarzone konto magazynu powinny znajdować się w obsługiwanych regionach.
    
   5. Aby uzyskać łącze do tej usługi na pulpicie nawigacyjnym, wybierz **pozycję Przypnij do pulpitu nawigacyjnego**.
    
   6. Kliknij przycisk **Utwórz**.

      ![Tworzenie usługi StorSimple Data Manager 3](./media/storsimple-data-manager-ui/create-service-4.png)

Tworzenie usługi potrwa kilka minut. Zostanie wyświetlone powiadomienie po pomyślnym utworzeniu usługi i wyświetleniu nowej usługi.

### <a name="create-a-data-transformation-job-definition"></a>Tworzenie definicji zadania transformacji danych

W ramach usługi StorSimple Data Manager należy utworzyć definicję zadania transformacji danych. Definicja zadania określa szczegóły storproste dane, które są zainteresowane przejściem do konta magazynu w formacie macierzystym. Po utworzeniu definicji zadania można ponownie uruchomić to zadanie z różnymi ustawieniami środowiska uruchomieniowego.

Wykonaj następujące kroki, aby utworzyć definicję zadania.

1. Przejdź do utworzonej usługi. Przejdź do **zarządzania > definicje zadań**.

2. Kliknij **przycisk + Definicja zadania**.

    ![Kliknij +Definicja zadania](./media/storsimple-data-manager-ui/create-job-definition-1.png)

3. Podaj nazwę definicji zadania. Nazwa może mieć od 3 do 63 znaków. Nazwa może zawierać wielkie i małe litery, cyfry i łączniki.

4. Określ lokalizację, w której działa zadanie. Ta lokalizacja może być inna niż lokalizacja, w której usługa jest wdrażana.

5. Kliknij **przycisk Źródło,** aby określić repozytorium danych źródłowych.

    ![Konfigurowanie repozytorium danych źródłowych](./media/storsimple-data-manager-ui/create-job-definition-2.png)

6. Ponieważ jest to nowa usługa Programu Data Manager, nie są skonfigurowane żadne repozytoria danych. W **obszarze Konfiguruj źródło danych**określ szczegóły urządzenia z serii StorSimple 8000 oraz dane będące przedmiotem zainteresowania.

   Aby dodać Menedżera urządzeń StorSimple jako repozytorium danych, kliknij pozycję **Dodaj nowy** w menu rozwijanym repozytorium danych, a następnie kliknij pozycję **Dodaj repozytorium danych**.

    ![Dodawanie nowego repozytorium danych](./media/storsimple-data-manager-ui/create-job-definition-3.png)
  
   1. Jako typ repozytorium danych wybierz **storprople 8000 series Manager.**
    
   2. Wprowadź przyjazną nazwę repozytorium danych źródłowych.
    
   3. Z listy rozwijanej wybierz subskrypcję skojarzoną z usługą Menedżer urządzeń StorSimple.
    
   4. Podaj nazwę Menedżera urządzeń StorSimple dla **zasobu**.

   5. Wprowadź klucz **szyfrowania danych usługi** StorSimple Device Manager. 

      ![Konfigurowanie repozytorium danych źródłowych 1](./media/storsimple-data-manager-ui/create-job-definition-4.png)

      Po zakończeniu kliknij **przycisk OK.** Spowoduje to zapisanie repozytorium danych. Ponownie użyć tego Menedżera urządzeń StorSimple w innych definicjach zadań bez wprowadzania tych parametrów ponownie. Trwa kilka sekund po kliknięciu **przycisku OK** dla nowo utworzonego repozytorium danych źródłowych, aby pokazać się w rozwijanej.

7. Z listy rozwijanej **repozytorium danych**wybierz utworzone repozytorium danych. 

   1. Wprowadź nazwę urządzenia z serii StorSimple 8000, które zawiera dane będące przedmiotem zainteresowania.

   2. Określ nazwę woluminu resididing na urządzeniu StorSimple, który ma dane interesujące.

   3. W podsekcji **Filtr** wprowadź katalog główny zawierający interesujące dane w formacie _\MyRootDirectory\Data._ Litery dysków takie _jak \C:\Data_ nie są obsługiwane. W tym miejscu można również dodać dowolne filtry plików.

   4. Usługa przekształcania danych działa tylko na najnowszej migawce danych, która jest wypychany do platformy Azure.

   5. Kliknij przycisk **OK**.

      ![Konfigurowanie repozytorium danych źródłowych 2](./media/storsimple-data-manager-ui/create-job-definition-8.png)

8. Następnie należy skonfigurować docelowe repozytorium danych. Wybierz konta magazynu, aby umieścić pliki w obiektach blob na tym koncie. Z listy rozwijanej wybierz pozycję **Dodaj nowe,** a następnie **pozycję Konfiguruj ustawienia**.

9. Wybierz typ repozytorium docelowego, które chcesz dodać, oraz inne parametry skojarzone z repozytorium.

    Jeśli wybierzesz miejsce docelowe typu konta magazynu, możesz określić przyjazną nazwę, subskrypcję (wybierz taką samą jak nazwę usługi lub innej) i konto magazynu.
        ![Konfigurowanie repozytorium danych docelowych 1](./media/storsimple-data-manager-ui/create-job-definition-10.png)

    Kolejka magazynu jest tworzona po uruchomieniu zadania. Jest ona wypełniana przy użyciu komunikatów dotyczących przekształconych obiektów blob, gdy będą gotowe. Nazwa tej kolejki jest taka sama jak nazwa definicji zadania.
    
10. Po dodaniu repozytorium danych odczekaj kilka minut.
    
    1. Wybierz repozytorium utworzone jako miejsce docelowe z listy rozwijanej w **nazwie konta docelowego**.

    2. Wybierz typ magazynu jako obiekty blob lub pliki. Określ nazwę kontenera magazynu, w którym znajdują się przekształcone dane. Kliknij przycisk **OK**.

        ![Konfigurowanie docelowego konta magazynu repozytorium danych](./media/storsimple-data-manager-ui/create-job-definition-16.png)

11. Można również sprawdzić opcję przedstawienia szacowanego czasu trwania zadania przed uruchomieniem zadania. Kliknij **przycisk OK,** aby utworzyć definicję zadania. Definicja zadania została zakończona. Tej definicji zadania można użyć wiele razy za pośrednictwem interfejsu użytkownika z różnymi ustawieniami środowiska uruchomieniowego.

    ![Pełna definicja zadania](./media/storsimple-data-manager-ui/create-job-definition-13.png)

    Nowo utworzona definicja zadania jest dodawana do listy definicji zadań dla tej usługi.

### <a name="run-the-job-definition"></a>Uruchamianie definicji zadania

Za każdym razem, gdy trzeba przenieść dane z StorSimple do konta magazynu, które zostały określone w definicji zadania, należy go uruchomić. W czasie wykonywania niektóre parametry można określić inaczej. Kroki tego procesu są następujące:

1. Wybierz usługę StorSimple Data Manager i przejdź do **definicji zadań zarządzanie >**. Zaznacz i kliknij definicję zadania, którą chcesz uruchomić.
     
     ![Uruchamianie zadania 1](./media/storsimple-data-manager-ui/start-job-run1.png)

2. Kliknij **przycisk Uruchom teraz**.
     
     ![Uruchamianie zadania 2](./media/storsimple-data-manager-ui/start-job-run2.png)

3. Kliknij **przycisk Uruchom ustawienia,** aby zmodyfikować wszystkie ustawienia, które można zmienić dla tego zadania. Kliknij **przycisk OK,** a następnie kliknij przycisk **Uruchom,** aby uruchomić zadanie.

    ![Uruchamianie zadania 3](./media/storsimple-data-manager-ui/start-job-run3.png)

4. Aby monitorować to zadanie, przejdź do **zadania** w menedżerze danych StorSimple. Oprócz monitorowania w **zadaniach** bloku, można również nasłuchiwać w kolejce magazynu, gdzie komunikat jest dodawany za każdym razem, gdy plik jest przenoszony z StorSimple do konta magazynu.

    ![Uruchamianie zadania 4](./media/storsimple-data-manager-ui/start-job-run4.png)

### <a name="view-logs-after-job-completion"></a>Wyświetlanie dzienników po zakończeniu zadania

Po zakończeniu zadania można wyświetlić stan zadania. Stan zadania można **zakończyć pomyślnie**, **częściowo powiodło się** i nie **powiodło się**. Można wyświetlić listę plików, które zostały pomyślnie skopiowane i pliki, które nie zostały skopiowane. Listy te są dostępne w kontenerze o nazwie **"storsimple-data-manager-joblogs"** w ramach konta magazynu docelowego. W tym kontenerze można wyszukać folder o takiej samej nazwie jak definicja zadania. W tym ramach zostanie utworzony folder dla każdego uruchomienia zadania, które będzie zawierać listy. Nazwa tego folderu będzie identyfikatorem GUID zadania, który można uzyskać ze strony szczegółów zadania. Alternatywnie w większości przypadków zostanie wyświetlony łącze do dzienników kopii na samej stronie zadań.
Istnieją 2 zestaw plików csv, które zobaczysz w tym folderze. Wszystkie pliki, które zaczynają się od **skopiowanej listy...** będą zawierać listę pomyślnie skopiowanych plików. Wszystkie pliki, które zaczynają się od **nieudanej listy...** zawierają pliki, których nie można było skopiować, wraz z komunikatem o błędzie.


## <a name="next-steps"></a>Następne kroki

[Użyj pliku .NET SDK do uruchamiania zadań programu StorSimple Data Manager](storsimple-data-manager-dotnet-jobs.md).
