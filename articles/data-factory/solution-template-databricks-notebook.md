---
title: Przekształcanie danych przy użyciu kostek datakostki
description: Dowiedz się, jak używać szablonu rozwiązania do przekształcania danych przy użyciu notesu datakostki w Azure Data Factory.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: abnarain
ms.reviewer: douglasl
ms.openlocfilehash: 5b39e354d503910d20141ce19c625eb79b4a7353
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74891001"
---
# <a name="transform-data-by-using-databricks-in-azure-data-factory"></a>Przekształcanie danych przy użyciu kostek datakostki w Azure Data Factory

W tym samouczku utworzysz kompleksowy potok zawierający działania w notesie **Wyszukiwanie**, **Kopiowanie**i **datakostki** w Data Factory.

-   Działanie **Lookup** lub GetMetadata służy do upewnienia się, że źródłowy zestaw danych jest gotowy do użycia na użytek podrzędny, przed wyzwoleniem zadania kopiowania i analizy.

-   **Działanie Copy** kopiuje plik źródłowy/zestaw danych do magazynu ujścia. Magazyn ujścia jest instalowany jako DBFS w notesie datakosteks, dzięki czemu zestaw danych może być bezpośrednio używany przez platformę Spark.

-   **Działanie notesu datakosteks** wyzwala Notes datakostks, który przekształca zestaw danych i dodaje go do przetworzonego folderu/DW programu SQL.

Aby zachować ten szablon jako prosty, szablon nie tworzy zaplanowanego wyzwalacza. W razie potrzeby można je dodać.

![1](media/solution-template-Databricks-notebook/Databricks-tutorial-image01.png)

## <a name="prerequisites"></a>Wymagania wstępne

1.  Utwórz **konto magazynu obiektów BLOB** i kontener o nazwie `sinkdata`, który ma być używany jako **obiekt sink**. Zanotuj **nazwę konta magazynu**, **nazwę kontenera**i **klucz dostępu**, ponieważ są one przywoływane później w szablonie.

2.  Upewnij się, że masz **obszar roboczy Azure Databricks** lub Utwórz nowy.

1.  **Zaimportuj Notes dla ETL**. Zaimportuj Poniższy Notes Przekształć do obszaru roboczego datakosteks. (Nie musi znajdować się w tej samej lokalizacji co poniżej, ale należy pamiętać, że ścieżka wybrana później). Zaimportuj Notes z następującego adresu URL, wprowadzając ten adres URL w polu adres URL: `https://adflabstaging1.blob.core.windows.net/share/Transformations.html`. Wybierz pozycję **Import** (Importuj).

    ![2](media/solution-template-Databricks-notebook/Databricks-tutorial-image02.png)

    ![3](media/solution-template-Databricks-notebook/Databricks-tutorial-image03.png)  

1.  Teraz zaktualizujmy Notes **transformacji** przy użyciu **informacji o połączeniu z magazynem** (nazwa i klucz dostępu). Przejdź do **polecenia 5** w zaimportowanym notesie, zastąp go następującym fragmentem kodu po zastąpieniu wyróżnionych wartości. Upewnij się, że to konto magazynu zostało utworzone wcześniej i zawiera kontener `sinkdata`.

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

1.  Generowanie **tokenu dostępu do datakostki** dla Data Factory w celu uzyskania dostępu do datakostki. **Zapisz token dostępu** do późniejszego użycia podczas tworzenia połączonej usługi datakostki, która wygląda podobnie jak "dapi32db32cbb4w6eee18b7d87e45exxxxxx"

    ![4](media/solution-template-Databricks-notebook/Databricks-tutorial-image04.png)

    ![5](media/solution-template-Databricks-notebook/Databricks-tutorial-image05.png)

## <a name="create-linked-services-and-datasets"></a>Tworzenie połączonych usług i zestawów danych

1.  Utwórz nowe **połączone usługi** w Data Factory interfejsie użytkownika, przechodząc do *połączeń połączonych usług i nowych*

    1.  **Źródło** — w celu uzyskania dostępu do danych źródłowych. Możesz użyć publicznego magazynu obiektów BLOB zawierającego pliki źródłowe dla tego przykładu.

        Wybierz **BLOB Storage**, użyj poniższego **identyfikatora URI sygnatury dostępu współdzielonego** , aby nawiązać połączenie z magazynem źródłowym (dostęp tylko do odczytu).

        `https://storagewithdata.blob.core.windows.net/?sv=2017-11-09&ss=b&srt=sco&sp=rl&se=2019-12-31T21:40:53Z&st=2018-10-24T13:40:53Z&spr=https&sig=K8nRio7c4xMLnUV0wWVAmqr5H4P3JDwBaG9HCevI7kU%3D`

        ![6](media/solution-template-Databricks-notebook/Databricks-tutorial-image06.png)

    1.  **Ujścia** — do kopiowania danych do programu.

        Wybierz magazyn utworzony w ramach wymagania wstępnego 1 w połączonej usłudze ujścia.

        ![7](media/solution-template-Databricks-notebook/Databricks-tutorial-image07.png)

    1.  **Datakostki** — do łączenia się z klastrem datakostki

        Tworzenie połączonej usługi datakostki przy użyciu klucza dostępu wygenerowanego w wymaganiu wstępnym 2. c. Jeśli masz *interaktywny klaster*, możesz wybrać tę opcję. (W tym przykładzie jest stosowana opcja *nowy klaster zadania* ).

        ![8](media/solution-template-Databricks-notebook/Databricks-tutorial-image08.png)

2.  Tworzenie **zestawów danych**

    1.  Utwórz **element "sourceAvailability_Dataset"** , aby sprawdzić, czy dane źródłowe są dostępne

    ![9](media/solution-template-Databricks-notebook/Databricks-tutorial-image09.png)

    1.  **Źródłowy zestaw danych —** do kopiowania danych źródłowych (przy użyciu kopii binarnej)

    ![10](media/solution-template-Databricks-notebook/Databricks-tutorial-image10.png)

    1.  **Zestaw danych ujścia** — do kopiowania do lokalizacji ujścia/miejsce docelowe

        1.  Połączona usługa — wybierz pozycję "sinkBlob_LS" utworzoną w 1. b

        2.  Ścieżka pliku — "sinkdata/staged_sink"

        ![11](media/solution-template-Databricks-notebook/Databricks-tutorial-image11.png)

## <a name="create-activities"></a>Tworzenie działań

1.  Utwórz**flagę dostępności**działania Lookup, aby wykonać kontrolę dostępności źródła (można użyć funkcji Lookup lub GetMetadata). Wybierz pozycję "sourceAvailability_Dataset" utworzoną w 2. a.

    ![12](media/solution-template-Databricks-notebook/Databricks-tutorial-image12.png)

1.  Utwórz działanie kopiowania "**File-to-BLOB**" do kopiowania zestawu danych ze źródła do ujścia. W takim przypadku dane są plikami binarnymi. Odnosi się do poniższych zrzutów ekranu dla konfiguracji źródła i ujścia w działaniu kopiowania.

    ![13](media/solution-template-Databricks-notebook/Databricks-tutorial-image13.png)

    ![14](media/solution-template-Databricks-notebook/Databricks-tutorial-image14.png)

1.  Definiowanie **parametrów potoku**

    ![15](media/solution-template-Databricks-notebook/Databricks-tutorial-image15.png)

1.  Tworzenie **działania dotyczącego elementów datakostki**

    Wybierz połączoną usługę utworzoną w poprzednim kroku.

    ![16](media/solution-template-Databricks-notebook/Databricks-tutorial-image16.png)

    Skonfiguruj **Ustawienia**. Utwórz **parametry podstawowe** , jak pokazano na zrzucie ekranu, i utwórz parametry, które mają zostać przesłane do notesu Databases z Data Factory. Przeglądaj i **Wybierz** **poprawną ścieżkę notesu** przekazaną w **wymaganiu wstępnym 2**.

    ![17](media/solution-template-Databricks-notebook/Databricks-tutorial-image17.png)

1.  **Uruchamianie potoku**. Łącze do dzienników datakostek można znaleźć, aby poznać bardziej szczegółowe dzienniki platformy Spark.

    ![18](media/solution-template-Databricks-notebook/Databricks-tutorial-image18.png)

    Możesz również zweryfikować plik danych przy użyciu Eksploratora usługi Storage. (W celu skorelowania z uruchomieniami potoku Data Factory ten przykład dołącza identyfikator uruchomienia potoku z fabryki danych do folderu danych wyjściowych. W ten sposób można śledzić pliki wygenerowane przez poszczególne uruchomienia.

![19](media/solution-template-Databricks-notebook/Databricks-tutorial-image19.png)

## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie do usługi Azure Data Factory](introduction.md)
