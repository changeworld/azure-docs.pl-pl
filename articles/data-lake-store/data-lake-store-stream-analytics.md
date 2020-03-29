---
title: Przesyłanie strumieniowe danych z usługi Stream Analytics do usługi Azure Data Lake Storage Gen1 | Dokumenty firmy Microsoft
description: Używanie usługi Azure Stream Analytics do przesyłania strumieniowego danych do usługi Azure Data Lake Storage Gen1
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60194947"
---
# <a name="stream-data-from-azure-storage-blob-into-azure-data-lake-storage-gen1-using-azure-stream-analytics"></a>Przesyłanie strumieniowe danych z obiektu blob usługi Azure Storage do usługi Azure Data Lake Storage Gen1 przy użyciu usługi Azure Stream Analytics
W tym artykule dowiesz się, jak używać usługi Azure Data Lake Storage Gen1 jako danych wyjściowych dla zadania usługi Azure Stream Analytics. W tym artykule przedstawiono prosty scenariusz, który odczytuje dane z obiektu blob usługi Azure Storage (dane wejściowe) i zapisuje dane do usługi Data Lake Storage Gen1 (dane wyjściowe).

## <a name="prerequisites"></a>Wymagania wstępne
Przed przystąpieniem do wykonania kroków opisanych w tym samouczku należy dysponować następującymi elementami:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Konto usługi Azure Storage**. Użyjesz kontenera obiektów blob z tego konta do wprowadzania danych dla zadania usługi Stream Analytics. W tym samouczku załóżmy, że masz konto magazynu o nazwie **storageforasa** i kontener na koncie o nazwie **storageforasacontainer**. Po utworzeniu kontenera przekaż do niego przykładowy plik danych. 
  
* **Konto Data Lake Storage Gen1**. Postępuj zgodnie z instrukcjami w [usłudze Wprowadzenie do usługi Azure Data Lake Storage Gen1 przy użyciu witryny Azure Portal.](data-lake-store-get-started-portal.md) Załóżmy, że masz konto Data Lake Storage Gen1 o nazwie **myadlsg1**. 

## <a name="create-a-stream-analytics-job"></a>Tworzenie zadania usługi Stream Analytics
Możesz rozpocząć od utworzenia zadania usługi Stream Analytics, które zawiera źródło wejściowe i miejsce docelowe danych wyjściowych. W tym samouczku źródłem jest kontener obiektów blob platformy Azure, a miejscem docelowym jest Data Lake Storage Gen1.

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com).

2. W lewym okienku kliknij pozycję **Zadania usługi Stream Analytics**, a następnie kliknij pozycję **Dodaj**.

    ![Tworzenie zadania analizy strumienia](./media/data-lake-store-stream-analytics/create.job.png "Tworzenie zadania usługi Stream Analytics")

    > [!NOTE]
    > Upewnij się, że tworzysz zadanie w tym samym regionie co konto magazynu lub poniesiesz dodatkowe koszty przenoszenia danych między regionami.
    >

## <a name="create-a-blob-input-for-the-job"></a>Tworzenie danych wejściowych obiektu blob dla zadania

1. Otwórz stronę zadania Usługi Stream Analytics, z lewego okienka kliknij kartę **Wejścia,** a następnie kliknij pozycję **Dodaj**.

    ![Dodawanie danych wejściowych do zadania](./media/data-lake-store-stream-analytics/create.input.1.png "Dodawanie danych wejściowych do zadania")

2. Na **New input** blade, podaj następujące wartości.

    ![Dodawanie danych wejściowych do zadania](./media/data-lake-store-stream-analytics/create.input.2.png "Dodawanie danych wejściowych do zadania")

   * W przypadku **aliasu wejściowego**wprowadź unikatową nazwę danych wejściowych zadania.
   * W obszarze **Typ źródła**wybierz opcję **Strumień danych**.
   * W obszarze **Źródło**wybierz pozycję **Magazyn obiektów Blob**.
   * W przypadku **subskrypcji**wybierz pozycję **Użyj magazynu obiektów blob z bieżącej subskrypcji**.
   * W przypadku **konta Magazyn**wybierz konto magazynu utworzone jako część wymagań wstępnych. 
   * W obszarze **Kontener**wybierz kontener utworzony na wybranym koncie magazynu.
   * W przypadku **formatu serializacji zdarzeń**wybierz pozycję **CSV**.
   * W przypadku **ogranicznika**wybierz **kartę**.
   * W przypadku **kodowania**wybierz **opcję UTF-8**.

     Kliknij przycisk **Utwórz**. Portal teraz dodaje dane wejściowe i testuje połączenie z nim.


## <a name="create-a-data-lake-storage-gen1-output-for-the-job"></a>Tworzenie danych wyjściowych gen1 magazynu usługi Data Lake dla zadania

1. Otwórz stronę zadania Usługa Stream Analytics, kliknij kartę **Dane wyjściowe,** kliknij pozycję **Dodaj**i wybierz pozycję **Data Lake Storage Gen1**.

    ![Dodawanie danych wyjściowych do zadania](./media/data-lake-store-stream-analytics/create.output.1.png "Dodawanie danych wyjściowych do zadania")

2. Na **New output** blade, podaj następujące wartości.

    ![Dodawanie danych wyjściowych do zadania](./media/data-lake-store-stream-analytics/create.output.2.png "Dodawanie danych wyjściowych do zadania")

    * W przypadku **aliasu wyjściowego**wprowadź unikatową nazwę dla danych wyjściowych zadania. Jest to przyjazna nazwa używana w kwerendach do kierowania danych wyjściowych kwerendy do tego konta Data Lake Storage Gen1.
    * Zostanie wyświetlony monit o autoryzację dostępu do konta Data Lake Storage Gen1. Kliknij pozycję **Autoryzuj**.

3. Na **New output** blade, nadal podać następujące wartości.

    ![Dodawanie danych wyjściowych do zadania](./media/data-lake-store-stream-analytics/create.output.3.png "Dodawanie danych wyjściowych do zadania")

   * W **obszarze Nazwa konta**wybierz konto Data Lake Storage Gen1, które zostało już utworzone w miejscu, do którego ma zostać wysłane dane wyjściowe zadania.
   * W polu **Wzorzec prefiksu ścieżki**wprowadź ścieżkę pliku używaną do zapisywania plików w określonym koncie Data Lake Storage Gen1.
   * W przypadku **formatu Data**, jeśli użyto tokenu daty w ścieżce prefiksu, można wybrać format daty, w którym pliki są zorganizowane.
   * W przypadku **formatu Czas**, jeśli użyto tokenu czasu w ścieżce prefiksu, określ format czasu, w którym pliki są zorganizowane.
   * W przypadku **formatu serializacji zdarzeń**wybierz pozycję **CSV**.
   * W przypadku **ogranicznika**wybierz **kartę**.
   * W przypadku **kodowania**wybierz **opcję UTF-8**.
    
     Kliknij przycisk **Utwórz**. Portal teraz dodaje dane wyjściowe i testuje połączenie z nim.
    
## <a name="run-the-stream-analytics-job"></a>Uruchamianie zadania usługi Stream Analytics

1. Aby uruchomić zadanie usługi Stream Analytics, należy uruchomić kwerendę na karcie **Kwerenda.** W tym samouczku można uruchomić przykładowe zapytanie, zastępując symbole zastępcze aliasami wejściowymi i wyjściowymi zadania, jak pokazano na poniższym przechwycie ekranu.

    ![Uruchamianie kwerendy](./media/data-lake-store-stream-analytics/run.query.png "Uruchamianie zapytania")

2. Kliknij **pozycję Zapisz** u góry ekranu, a następnie na karcie **Przegląd** kliknij pozycję **Start**. W oknie dialogowym wybierz pozycję **Czas niestandardowy**, a następnie ustaw bieżącą datę i godzinę.

    ![Ustawianie czasu pracy](./media/data-lake-store-stream-analytics/run.query.2.png "Ustawianie czasu pracy")

    Kliknij **przycisk Start,** aby rozpocząć zadanie. Rozpoczęcie pracy może potrwać do kilku minut.

3. Aby wyzwolić zadanie pobrania danych z obiektu blob, skopiuj przykładowy plik danych do kontenera obiektu blob. Przykładowy plik danych można pobrać z [repozytorium Git usługi Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt). W tym samouczku skopiujmy plik **vehicle1_09142014.csv**. Można użyć różnych klientów, takich jak [Azure Storage Explorer](https://storageexplorer.com/), aby przekazać dane do kontenera obiektów blob.

4. Na karcie **Przegląd** w obszarze **Monitorowanie**zobacz sposób przetwarzania danych.

    ![Monitorowanie zadania](./media/data-lake-store-stream-analytics/run.query.3.png "Monitorowanie zadania")

5. Na koniec można sprawdzić, czy dane wyjściowe zadania są dostępne na koncie Data Lake Storage Gen1. 

    ![Weryfikacja danych wyjściowych](./media/data-lake-store-stream-analytics/run.query.4.png "Weryfikacja danych wyjściowych")

    W okienku Eksplorator danych zwróć uwagę, że dane wyjściowe są zapisywane w`streamanalytics/job/output/{date}/{time}`ścieżce folderu określonej w ustawieniach wyjściowych data lake storage gen1 ( ).  

## <a name="see-also"></a>Zobacz też
* [Tworzenie klastra usługi HDInsight w celu użycia pamięci masowej usługi Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
