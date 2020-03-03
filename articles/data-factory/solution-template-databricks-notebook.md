---
title: Przekształcanie za pomocą Azure Databricks
description: Dowiedz się, jak używać szablonu rozwiązania do przekształcania danych przy użyciu notesu datakostki w Azure Data Factory.
services: data-factory
ms.author: abnarain
author: nabhishek
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/10/2018
ms.openlocfilehash: 322f5306949b266958ded908e981ed530e8245c8
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2020
ms.locfileid: "78227729"
---
# <a name="transformation-with-azure-databricks"></a>Przekształcanie za pomocą Azure Databricks

W tym samouczku utworzysz kompleksowy potok zawierający działania **walidacji**, **kopiowania**i **notesu** w Data Factory.

-   Działanie **sprawdzania poprawności** służy do upewnienia się, że źródłowy zestaw danych jest gotowy do użycia w czasie podrzędnym, przed wyzwoleniem zadania kopiowania i analizy.

-   Działanie **copy** kopiuje plik źródłowy/zestaw danych do magazynu ujścia. Magazyn ujścia jest instalowany jako DBFS w notesie datakosteks, dzięki czemu zestaw danych może być bezpośrednio używany przez platformę Spark.

-   Działanie **notesu datakosteks** wyzwala Notes datakostks, który przekształca zestaw danych i dodaje go do przetworzonego folderu/DW programu SQL.

Aby zachować ten szablon jako prosty, szablon nie tworzy zaplanowanego wyzwalacza. W razie potrzeby można je dodać.

![1](media/solution-template-Databricks-notebook/Databricks-tutorial-image01.png)

## <a name="prerequisites"></a>Wymagania wstępne

1. Utwórz **konto magazynu obiektów BLOB** i kontener o nazwie `sinkdata`, który ma być używany jako **obiekt sink**. Zanotuj **nazwę konta magazynu**, **nazwę kontenera**i **klucz dostępu**, ponieważ są one przywoływane później w szablonie.

2. Upewnij się, że masz **obszar roboczy Azure Databricks** lub Utwórz nowy.

3. **Zaimportuj Notes do transformacji**. 
    1. W Azure Databricks odwołujesz się do poniższych zrzutów ekranu, aby zaimportować Notes **transformacji** do obszaru roboczego elementy danych. Nie musi znajdować się w tej samej lokalizacji co poniżej, ale należy pamiętać, że ścieżka wybrana w dalszej części.
   
       ![2](media/solution-template-Databricks-notebook/Databricks-tutorial-image02.png)    
    
    1. Wybierz pozycję "Importuj z: **adres URL**" i wprowadź następujący adres URL w polu tekstowym:
    
       * `https://adflabstaging1.blob.core.windows.net/share/Transformations.html`
        
       ![3](media/solution-template-Databricks-notebook/Databricks-tutorial-image03.png)    

4. Teraz zaktualizujmy Notes **transformacji** przy użyciu informacji o połączeniu z magazynem. Przejdź do **polecenia 5** (jak pokazano w poniższym fragmencie kodu) w zaimportowanym notesie, a następnie zastąp `<storage name>`i `<access key>` własnymi informacjami o połączeniu z magazynem. Upewnij się, że to konto magazynu zostało utworzone wcześniej i zawiera kontener `sinkdata`.

    ```python
    # Supply storageName and accessKey values  
    storageName = "<storage name>"  
    accessKey = "<access key>"  

    try:  
      dbutils.fs.mount(  
        source = "wasbs://sinkdata\@"+storageName+".blob.core.windows.net/",  
        mount_point = "/mnt/Data Factorydata",  
        extra_configs = {"fs.azure.account.key."+storageName+".blob.core.windows.net": accessKey})  

    except Exception as e:  
      # The error message has a long stack track. This code tries to print just the relevant line indicating what failed.

    import re
    result = re.findall(r"\^\s\*Caused by:\s*\S+:\s\*(.*)\$", e.message, flags=re.MULTILINE)
    if result:
      print result[-1] \# Print only the relevant error message
    else:  
      print e \# Otherwise print the whole stack trace.  
    ```

5.  Generowanie **tokenu dostępu do datakostki** dla Data Factory w celu uzyskania dostępu do datakostki. **Zapisz token dostępu** do późniejszego użycia podczas tworzenia połączonej usługi datakostki, która wygląda podobnie jak "dapi32db32cbb4w6eee18b7d87e45exxxxxx".

    ![4](media/solution-template-Databricks-notebook/Databricks-tutorial-image04.png)

    ![5](media/solution-template-Databricks-notebook/Databricks-tutorial-image05.png)

## <a name="how-to-use-this-template"></a>Jak używać tego szablonu

1.  Przejdź do **przekształcenia z** szablonem Azure Databricks. Utwórz nowe połączone usługi dla następujących połączeń. 
    
    ![Ustawienie połączeń](media/solution-template-Databricks-notebook/connections-preview.png)

    1.  **Połączenie źródłowego obiektu BLOB** — w celu uzyskania dostępu do danych źródłowych. 
        
        Możesz użyć publicznego magazynu obiektów BLOB zawierającego pliki źródłowe dla tego przykładu. Utwórz odwołanie do poniższego zrzutu ekranu na potrzeby konfiguracji. Użyj poniższego **adresu URL sygnatury dostępu współdzielonego** , aby połączyć się z magazynem źródłowym (dostęp tylko do odczytu): 
        * `https://storagewithdata.blob.core.windows.net/data?sv=2018-03-28&si=read%20and%20list&sr=c&sig=PuyyS6%2FKdB2JxcZN0kPlmHSBlD8uIKyzhBWmWzznkBw%3D`

        ![6](media/solution-template-Databricks-notebook/Databricks-tutorial-image06.png)

    1.  **Połączenie docelowego obiektu BLOB** — do kopiowania danych do programu. 
        
        W połączonej usłudze ujścia wybierz magazyn utworzony w ramach **wymagania wstępnego** 1.

        ![7](media/solution-template-Databricks-notebook/Databricks-tutorial-image07.png)

    1.  **Azure Databricks** — służy do łączenia się z klastrem datakostks.

        Tworzenie połączonej usługi datakostki przy użyciu klucza dostępu wygenerowanego w **wymaganiu wstępnym** 2. c. Jeśli masz *interaktywny klaster*, możesz wybrać tę opcję. (W tym przykładzie jest stosowana opcja *nowy klaster zadania* ).

        ![8](media/solution-template-Databricks-notebook/Databricks-tutorial-image08.png)

1. Wybierz pozycję **Użyj tego szablonu**, aby wyświetlić utworzony potok, jak pokazano poniżej:
    
    ![Tworzenie potoku](media/solution-template-Databricks-notebook/new-pipeline.png)   

## <a name="pipeline-introduction-and-configuration"></a>Wprowadzenie i konfiguracja potoku

W nowo utworzonym potoku większość ustawień zostało skonfigurowanych automatycznie z wartościami domyślnymi. Zapoznaj się z konfiguracjami i zaktualizuj je, jeśli jest to konieczne, aby dostosować je do własnych ustawień. Aby uzyskać szczegółowe informacje, możesz zapoznać się z poniższymi instrukcjami i zrzutami ekranu.

1.  **Flaga dostępności** działania weryfikacji jest tworzona na potrzeby wykonywania kontroli dostępności źródła. *SourceAvailabilityDataset* utworzony w poprzednim kroku jest wybierany jako zestaw danych.

    ![12](media/solution-template-Databricks-notebook/Databricks-tutorial-image12.png)

1.  Do kopiowania zestawu danych ze źródła do ujścia jest tworzony plik działania kopiowania **do obiektu BLOB** . Odnosi się do poniższych zrzutów ekranu dla konfiguracji źródła i ujścia w działaniu kopiowania.

    ![13](media/solution-template-Databricks-notebook/Databricks-tutorial-image13.png)

    ![14](media/solution-template-Databricks-notebook/Databricks-tutorial-image14.png)

1.  Zostanie utworzona **transformacja** działania notesu i zostanie wybrana połączona usługa utworzona w poprzednim kroku.
    ![16](media/solution-template-Databricks-notebook/Databricks-tutorial-image16.png)

     1. Wybierz kartę **Ustawienia** . W przypadku *ścieżki notesu*szablon domyślnie definiuje ścieżkę. Może być konieczne przeszukanie i wybranie poprawnej ścieżki notesu przekazanej w **wymaganiu wstępnym** 2. 

         ![17](media/solution-template-Databricks-notebook/databricks-tutorial-image17.png)
    
     1. Sprawdź *parametry podstawowe* utworzone, jak pokazano na zrzucie ekranu. Są one przesyłane do notesu datacegły z Data Factory. 

         ![Parametry podstawowe](media/solution-template-Databricks-notebook/base-parameters.png)

1.  **Parametry potoku** są zdefiniowane poniżej.

    ![15](media/solution-template-Databricks-notebook/Databricks-tutorial-image15.png)

1. Konfigurowanie zestawów danych.
    1.  **SourceAvailabilityDataset** jest tworzony w celu sprawdzenia, czy dane źródłowe są dostępne.

        ![9](media/solution-template-Databricks-notebook/Databricks-tutorial-image09.png)

    1.  **SourceFilesDataset** — do kopiowania danych źródłowych.

        ![10](media/solution-template-Databricks-notebook/Databricks-tutorial-image10.png)

    1.  **DestinationFilesDataset** — do kopiowania do lokalizacji ujścia/miejsce docelowe.

        1.  Połączona usługa — *sinkBlob_LS* utworzona w poprzednim kroku.

        2.  Ścieżka pliku — *sinkdata/staged_sink*.

            ![11](media/solution-template-Databricks-notebook/Databricks-tutorial-image11.png)


1.  Wybierz pozycję **Debuguj** , aby uruchomić potok. Łącze do dzienników datakostek można znaleźć, aby poznać bardziej szczegółowe dzienniki platformy Spark.

    ![18](media/solution-template-Databricks-notebook/Databricks-tutorial-image18.png)

    Możesz również zweryfikować plik danych przy użyciu Eksploratora usługi Storage. (W celu skorelowania z uruchomieniami potoku Data Factory ten przykład dołącza identyfikator uruchomienia potoku z fabryki danych do folderu danych wyjściowych. W ten sposób można śledzić pliki wygenerowane przez poszczególne uruchomienia.

    ![19](media/solution-template-Databricks-notebook/Databricks-tutorial-image19.png)

## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie do usługi Azure Data Factory](introduction.md)
