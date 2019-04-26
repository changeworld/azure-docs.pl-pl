---
title: Przekształcanie danych za pomocą usługi Databricks w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przekształcić dane za pomocą notesu usługi Databricks w usłudze Azure Data Factory za pomocą szablonu rozwiązania.
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
ms.openlocfilehash: 562ce675acc43002ce468d60f8a8c412410be86c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60395403"
---
# <a name="transform-data-by-using-databricks-in-azure-data-factory"></a>Przekształcanie danych za pomocą usługi Databricks w usłudze Azure Data Factory

W tym samouczku utworzysz potok end-to-end, zawierający **wyszukiwania**, **kopiowania**, i **notesu usługi Databricks** działań w usłudze Data Factory.

-   **Wyszukiwanie** lub działanie GetMetadata służy do upewnij się, zestaw danych źródłowych jest gotowe do użycia transmisji, przed wyzwoleniem zadania kopiowania i analizy.

-   **Działanie kopiowania, które** kopiuje plik źródłowy / zestawu danych z magazynem ujścia. Magazynu ujścia jest zainstalowany jako DBFS notesu usługi Databricks, tak aby zestaw danych, które mogą być bezpośrednio używane przez rozwiązanie Spark.

-   **Działania notesu usługi Databricks** wyzwala notesu usługi Databricks, które przekształca zestawu danych i dodaje go do folderu przetworzonych / SQL data Warehouse.

W celu uproszczenia tego szablonu, szablon nie tworzy zaplanowane wyzwalanie. Możesz dodać, jeśli to konieczne.

![1](media/solution-template-Databricks-notebook/Databricks-tutorial-image01.png)

## <a name="prerequisites"></a>Wymagania wstępne

1.  Tworzenie **konta magazynu obiektów blob** oraz nazywany kontenerem `sinkdata` ma być używany jako **ujścia**. Zapisz **nazwa konta magazynu**, **nazwa kontenera**, i **klucz dostępu**, ponieważ odwołuje się w dalszej części szablonu.

2.  Upewnij się, że **obszaru roboczego usługi Azure Databricks** lub utworzyć nowy.

1.  **Importuj Notes ETL**. Importuj poniżej Notes przekształcenia do obszaru roboczego usługi Databricks. (Go nie ma znajdować się w tej samej lokalizacji, tak jak pokazano poniżej, ale należy pamiętać ścieżkę, wybierz dla później). Importuj Notes z następującego adresu URL, wprowadzając ten adres URL w polu adres URL: `https://adflabstaging1.blob.core.windows.net/share/Transformations.html`. Wybierz **importu**.

    ![2](media/solution-template-Databricks-notebook/Databricks-tutorial-image02.png)

    ![3](media/solution-template-Databricks-notebook/Databricks-tutorial-image03.png)  

1.  Teraz zaktualizujmy **przekształcania** notesu za pomocą usługi **informacje o połączeniu magazynu** (nazwy i klucza dostępu). Przejdź do **polecenia 5** w notesie importowanych powyżej, zastąp go wartością poniżej fragment kodu po zastąpieniu wartości wyróżnione. Upewnij się, to konto jest tego samego konta magazynu utworzonego wcześniej i zawiera `sinkdata` kontenera.

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

1.  Generowanie **token dostępu usługi Databricks** dla usługi Data Factory dostęp do usługi Databricks. **Zapisywanie tokenu dostępu** do późniejszego użycia w tworzeniu usługi Databricks połączone usługi, która wygląda podobnie "dapi32db32cbb4w6eee18b7d87e45exxxxxx"

    ![4](media/solution-template-Databricks-notebook/Databricks-tutorial-image04.png)

    ![5](media/solution-template-Databricks-notebook/Databricks-tutorial-image05.png)

## <a name="create-linked-services-and-datasets"></a>Tworzenie połączonych usług i zestawów danych

1.  Utwórz nową **połączonych usług** w interfejs użytkownika usługi Data Factory, przechodząc do *połączeń połączonych usług + nowe*

    1.  **Źródło** — służy do uzyskiwania dostępu do źródła danych. Można użyć magazynu publicznego obiektu blob zawierającego pliki źródłowe dla tego przykładu.

        Wybierz **magazynu obiektów Blob**, użyj poniżej **identyfikatora URI połączenia SAS** do połączenia z magazynem źródła (dostęp tylko do odczytu).

        `https://storagewithdata.blob.core.windows.net/?sv=2017-11-09&ss=b&srt=sco&sp=rl&se=2019-12-31T21:40:53Z&st=2018-10-24T13:40:53Z&spr=https&sig=K8nRio7c4xMLnUV0wWVAmqr5H4P3JDwBaG9HCevI7kU%3D`

        ![6](media/solution-template-Databricks-notebook/Databricks-tutorial-image06.png)

    1.  **Obiekt sink** — służy do kopiowania danych do.

        Wybierz magazyn, utworzone w ramach warunku wstępnego 1, w usłudze połączonej ujścia.

        ![7](media/solution-template-Databricks-notebook/Databricks-tutorial-image07.png)

    1.  **Usługi Databricks** — służy do łączenia się z klastrem usługi Databricks

        Utwórz połączoną usługę Databricks przy użyciu klucza dostępu wygenerowanych w 2.c wymagań wstępnych. Jeśli masz *klastra interaktywne*, może wybrać, który. (W tym przykładzie użyto *nowego klastra zadań* opcję.)

        ![8](media/solution-template-Databricks-notebook/Databricks-tutorial-image08.png)

2.  Utwórz **zestawów danych**

    1.  Tworzenie **"sourceAvailability_Dataset"** do sprawdzenia, czy źródło danych jest dostępna

    ![9](media/solution-template-Databricks-notebook/Databricks-tutorial-image09.png)

    1.  **Zestaw danych źródłowych —** do kopiowania danych źródłowych (przy użyciu kopia binarna)

    ![10](media/solution-template-Databricks-notebook/Databricks-tutorial-image10.png)

    1.  **Zestaw danych będący ujściem** — służy do kopiowania do zlewu / lokalizacja docelowa

        1.  Połączona usługa — wybierz pozycję "sinkBlob_LS" utworzone w 1.b

        2.  Ścieżka pliku - "sinkdata/staged_sink"

        ![11](media/solution-template-Databricks-notebook/Databricks-tutorial-image11.png)

## <a name="create-activities"></a>Tworzenie działań

1.  Utwórz działanie Lookup "**flagi dostępności**" do wykonywania sprawdzania dostępności źródła (wyszukiwania lub GetMetadata mogą być używane). Wybierz utworzony w 2.a "sourceAvailability_Dataset".

    ![12](media/solution-template-Databricks-notebook/Databricks-tutorial-image12.png)

1.  Utwórz działanie Copy "**pliku do obiektu blob**" kopiowania zestaw danych ze źródła do ujścia. W tym przypadku dane są pliku binarnego. Dokumentacja poniższe zrzuty ekranu w przypadku konfiguracji źródła i ujścia w działaniu kopiowania.

    ![13](media/solution-template-Databricks-notebook/Databricks-tutorial-image13.png)

    ![14](media/solution-template-Databricks-notebook/Databricks-tutorial-image14.png)

1.  Zdefiniuj **potoku parametrów**

    ![15](media/solution-template-Databricks-notebook/Databricks-tutorial-image15.png)

1.  Utwórz **działania usługi Databricks**

    Wybieranie połączonej usługi, utworzony w poprzednim kroku.

    ![16](media/solution-template-Databricks-notebook/Databricks-tutorial-image16.png)

    Konfigurowanie **ustawienia**. Tworzenie **parametry Base** jak pokazano na zrzucie ekranu i utworzyć parametry do przekazania do notesu usługi Databricks z fabryką danych. Przeglądaj i **wybierz** **ścieżkę notesu poprawne** przekazane **wymaganie wstępne 2**.

    ![17](media/solution-template-Databricks-notebook/Databricks-tutorial-image17.png)

1.  **Uruchamianie potoku**. Aby uzyskać bardziej szczegółowe dzienniki platformy Spark można znaleźć link do dzienników usługi Databricks.

    ![18](media/solution-template-Databricks-notebook/Databricks-tutorial-image18.png)

    Można również sprawdzić plik danych, za pomocą Eksploratora usługi storage. (Do korelacji z uruchomienia potoku fabryki danych, w tym przykładzie dołącza identyfikator uruchomienia z fabryki danych do folderu wyjściowego potoku. Dzięki temu można śledzić ponownie wygenerowanych plików za pomocą każdego uruchomienia.)

![19](media/solution-template-Databricks-notebook/Databricks-tutorial-image19.png)

## <a name="next-steps"></a>Kolejne kroki

- [Wprowadzenie do usługi Azure Data Factory](introduction.md)
