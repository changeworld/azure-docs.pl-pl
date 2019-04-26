---
title: Stream dane z usługi Stream Analytics do usługi Azure Data Lake Storage Gen1 | Dokumentacja firmy Microsoft
description: Za pomocą usługi Azure Stream Analytics przesyłanie strumieniowe danych do usługi Azure Data Lake Storage Gen1
services: data-lake-store,stream-analytics
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: edb58e0b-311f-44b0-a499-04d7e6c07a90
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: twooley
ms.openlocfilehash: d3dbacd58b3bda3fbf8ee8ad5f175eccc2cb2a24
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60194947"
---
# <a name="stream-data-from-azure-storage-blob-into-azure-data-lake-storage-gen1-using-azure-stream-analytics"></a>Stream dane z rozszerzenia Azure Storage Blob do usługi Azure Data Lake Storage Gen1 przy użyciu usługi Azure Stream Analytics
W tym artykule dowiesz się, jak używać usługi Azure Data Lake Storage Gen1 jako dane wyjściowe dla zadania usługi Azure Stream Analytics. W tym artykule przedstawiono prosty scenariusz, który odczytuje dane z obiektu blob usługi Azure Storage (wejście) i zapisuje je w danych Data Lake Storage Gen1 (dane wyjściowe).

## <a name="prerequisites"></a>Wymagania wstępne
Przed przystąpieniem do wykonania kroków opisanych w tym samouczku należy dysponować następującymi elementami:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Konto usługi Azure Storage**. Kontener obiektów blob w ramach tego konta zostanie użyty do wprowadzania danych dla zadania usługi Stream Analytics. W tym samouczku założono, że konto magazynu o nazwie **storageforasa** oraz nazywany kontenerem w ramach konta **storageforasacontainer**. Po utworzeniu kontenera, a następnie Przekaż przykładowy plik danych do niego. 
  
* **Konta Data Lake Storage Gen1**. Postępuj zgodnie z instrukcjami w artykule [Rozpoczynanie pracy z usługą Azure Data Lake Storage Gen1 przy użyciu witryny Azure Portal](data-lake-store-get-started-portal.md). Załóżmy, że masz konta Data Lake Storage Gen1 o nazwie **myadlsg1**. 

## <a name="create-a-stream-analytics-job"></a>Utwórz zadanie Stream Analytics
Możesz rozpocząć od utworzenia zadania usługi Stream Analytics, która zawiera źródło danych wejściowych oraz miejsce docelowe danych wyjściowych. W tym samouczku źródła jest kontenerem obiektów blob platformy Azure i obiektem docelowym jest Data Lake Storage Gen1.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. W okienku po lewej stronie kliknij **zadania usługi Stream Analytics**, a następnie kliknij przycisk **Dodaj**.

    ![Utwórz zadanie Stream Analytics](./media/data-lake-store-stream-analytics/create.job.png "Tworzenie zadania usługi Stream Analytics")

    > [!NOTE]
    > Upewnij się, tworzenie zadania w tym samym regionie co konto magazynu lub zostaną naliczone dodatkowe koszt przenoszenia danych między regionami.
    >

## <a name="create-a-blob-input-for-the-job"></a>Tworzenie wejściowych obiektów Blob, dla zadania

1. Otwórz stronę do zadania usługi Stream Analytics, w okienku po lewej stronie kliknij **dane wejściowe** kartę, a następnie kliknij przycisk **Dodaj**.

    ![Dodaj dane wejściowe do zadania](./media/data-lake-store-stream-analytics/create.input.1.png "Dodaj dane wejściowe do zadania")

2. Na **nowe dane wejściowe** bloku, podaj następujące wartości.

    ![Dodaj dane wejściowe do zadania](./media/data-lake-store-stream-analytics/create.input.2.png "Dodaj dane wejściowe do zadania")

   * Aby uzyskać **alias danych wejściowych**, wprowadź unikatową nazwę dla danych wejściowych zadania.
   * Aby uzyskać **typ źródła**, wybierz opcję **strumienia danych**.
   * Aby uzyskać **źródła**, wybierz opcję **magazynu obiektów Blob**.
   * Aby uzyskać **subskrypcji**, wybierz opcję **usługa blob storage z bieżącej subskrypcji**.
   * Aby uzyskać **konta magazynu**, wybierz konto magazynu, który został utworzony jako część wymagań wstępnych. 
   * Aby uzyskać **kontenera**, wybierz kontener, który został utworzony w wybranym koncie magazynu.
   * Aby uzyskać **format serializacji zdarzeń**, wybierz opcję **CSV**.
   * Aby uzyskać **ogranicznik**, wybierz opcję **kartę**.
   * Aby uzyskać **kodowanie**, wybierz opcję **UTF-8**.

     Kliknij pozycję **Utwórz**. Portal teraz dodaje dane wejściowe i testuje połączenie do niego.


## <a name="create-a-data-lake-storage-gen1-output-for-the-job"></a>Utwórz Data Lake Storage Gen1 dane wyjściowe zadania

1. Otwórz stronę do zadania usługi Stream Analytics, kliknij przycisk **dane wyjściowe** kliknij pozycję **Dodaj**i wybierz **Data Lake Storage Gen1**.

    ![Dodaj dane wyjściowe zadania](./media/data-lake-store-stream-analytics/create.output.1.png "Dodaj dane wyjściowe zadania")

2. Na **nowe dane wyjściowe** bloku, podaj następujące wartości.

    ![Dodaj dane wyjściowe zadania](./media/data-lake-store-stream-analytics/create.output.2.png "Dodaj dane wyjściowe zadania")

    * Aby uzyskać **alias wyjściowy**, wprowadź unikatową nazwę dla danych wyjściowych zadania. Jest to przyjazna nazwa używana w zapytaniach do kierowania wyników zapytania do tego konta Data Lake Storage Gen1.
    * Zostanie wyświetlony monit Autoryzowanie dostępu do konta Data Lake Storage Gen1. Kliknij przycisk **autoryzować**.

3. Na **nowe dane wyjściowe** bloku, podaj następujące wartości w dalszym ciągu.

    ![Dodaj dane wyjściowe zadania](./media/data-lake-store-stream-analytics/create.output.3.png "Dodaj dane wyjściowe zadania")

   * Aby uzyskać **nazwa konta**, wybierz konta Data Lake Storage Gen1 utworzone miejsce wyjścia do wysłania do zadania.
   * Aby uzyskać **wzorzec prefiksu ścieżki**, wprowadź ścieżkę do pliku używany do zapisywania plików w ramach określonego konta Data Lake Storage Gen1.
   * Aby uzyskać **format daty**, jeśli token daty jest używany w ścieżce prefiksu, można wybrać format daty, w której pliki są uporządkowane.
   * Dla **format godziny**, jeśli token czasu jest używany w ścieżce prefiks określić format czasu, w której pliki są uporządkowane.
   * Aby uzyskać **format serializacji zdarzeń**, wybierz opcję **CSV**.
   * Aby uzyskać **ogranicznik**, wybierz opcję **kartę**.
   * Aby uzyskać **kodowanie**, wybierz opcję **UTF-8**.
    
     Kliknij pozycję **Utwórz**. Portal teraz dodaje dane wyjściowe i testuje połączenie do niego.
    
## <a name="run-the-stream-analytics-job"></a>Uruchamianie zadania usługi Stream Analytics

1. Aby uruchomić zadanie usługi Stream Analytics, należy uruchomić kwerendę z **zapytania** kartę. Na potrzeby tego samouczka można uruchomić przykładowe zapytanie, zastępując symbole zastępcze zadania, dane wejściowe i wyjściowe aliasy, jak pokazano na poniższym zrzucie ekranu.

    ![Uruchom zapytanie](./media/data-lake-store-stream-analytics/run.query.png "uruchom zapytanie")

2. Kliknij przycisk **Zapisz** w górnej części ekranu, a następnie **Przegląd** kliknij pozycję **Start**. W oknie dialogowym wybierz **czasu niestandardowe**, a następnie ustaw bieżącą datę i godzinę.

    ![Ustaw czas zadania](./media/data-lake-store-stream-analytics/run.query.2.png "ustaw czas zadania")

    Kliknij przycisk **Start** Aby uruchomić zadanie. Może potrwać kilka minut. Aby uruchomić zadanie.

3. Aby wyzwolić zadanie do pobrania danych z obiektu blob, skopiuj przykładowy plik danych do kontenera obiektów blob. Możesz pobrać przykładowy plik danych z [repozytorium Git usługi Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt). W tym samouczku, skopiuj plik **vehicle1_09142014.csv**. Można użyć różnych klientów, takie jak [Eksploratora usługi Azure Storage](https://storageexplorer.com/), aby przekazać dane do kontenera obiektów blob.

4. Z **Przegląd** , w obszarze **monitorowanie**, zobacz przetwarzaniu danych.

    ![Monitorowanie zadania](./media/data-lake-store-stream-analytics/run.query.3.png "monitorowanie zadania")

5. Na koniec można sprawdzić, czy dane wyjściowe zadania jest dostępna w ramach konta Data Lake Storage Gen1. 

    ![Weryfikacja danych wyjściowych](./media/data-lake-store-stream-analytics/run.query.4.png "Weryfikacja danych wyjściowych")

    W okienku Eksplorator danych ustawienia w danych wyjściowych należy zauważyć, że plik wyjściowy zostanie zapisany do ścieżki folderu, jak to określono w Gen1 magazynu programu Data Lake (`streamanalytics/job/output/{date}/{time}`).  

## <a name="see-also"></a>Zobacz także
* [Tworzenie klastra usługi HDInsight, aby użyć Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
