---
title: Strumienia danych z usługi Stream Analytics do usługi Data Lake Store | Dokumentacja firmy Microsoft
description: Za pomocą usługi Azure Stream Analytics strumienia danych do usługi Azure Data Lake Store
services: data-lake-store,stream-analytics
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: edb58e0b-311f-44b0-a499-04d7e6c07a90
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: nitinme
ms.openlocfilehash: 8c045bed943427b5cb74291d70ee2eebcb90b2ef
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34625237"
---
# <a name="stream-data-from-azure-storage-blob-into-data-lake-store-using-azure-stream-analytics"></a>Stream data from Azure Storage Blob into Data Lake Store using Azure Stream Analytics (Strumieniowe przesyłanie danych z obiektu blob usługi Azure Storage do usługi Data Lake Store za pomocą usługi Azure Stream Analytics)
W tym artykule dowiesz się, jak używać usługi Azure Data Lake Store jako dane wyjściowe zadania usługi analiza strumienia Azure. W tym artykule przedstawiono prosty scenariusz, który będzie odczytywać dane z obiektu blob magazynu Azure (dane wejściowe) i zapisuje dane do usługi Data Lake Store (dane wyjściowe).

## <a name="prerequisites"></a>Wymagania wstępne
Przed przystąpieniem do wykonania kroków opisanych w tym samouczku należy dysponować następującymi elementami:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Konto usługi Azure Storage**. Kontener obiektów blob z tego konta zostaną użyte do wprowadzania danych w przypadku zadania Stream Analytics. W tym samouczku założono, że masz konto magazynu o nazwie **storageforasa** i kontener w ramach konta o nazwie **storageforasacontainer**. Po utworzeniu kontenera, Przekaż przykładowy plik danych do niego. 
  
* **Konto usługi Azure Data Lake Store**. Postępuj zgodnie z instrukcjami w temacie [Rozpoczynanie pracy z usługą Azure Data Lake Store za pomocą witryny Azure Portal](data-lake-store-get-started-portal.md). Załóżmy, że masz konto usługi Data Lake Store o nazwie **asadatalakestore**. 

## <a name="create-a-stream-analytics-job"></a>Tworzenie zadania usługi analiza strumienia
Rozpoczyna się od utworzenia zadanie usługi Stream Analytics, która zawiera źródło danych wejściowych i miejsce docelowe danych wyjściowych. W tym samouczku źródło jest kontenera obiektów blob platformy Azure i miejsce docelowe jest Data Lake Store.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. W okienku po lewej stronie kliknij **zadania usługi analiza strumienia**, a następnie kliknij przycisk **Dodaj**.

    ![Tworzenie zadania usługi analiza strumienia](./media/data-lake-store-stream-analytics/create.job.png "utworzyć zadania usługi analiza strumienia")

    > [!NOTE]
    > Upewnij się, możesz utworzyć zadania w tym samym regionie co konto magazynu lub spowoduje naliczenie dodatkowych kosztów przenoszenia danych między regionami.
    >

## <a name="create-a-blob-input-for-the-job"></a>Tworzenie obiektu Blob danych wejściowych dla zadania

1. Otwórz stronę do zadania usługi analiza strumienia, w lewym okienku kliknij **dane wejściowe** , a następnie kliknij pozycję **Dodaj**.

    ![Dodawanie danych wejściowych do zadania](./media/data-lake-store-stream-analytics/create.input.1.png "dodać danych wejściowych do zadania")

2. Na **wprowadzania nowych** bloku, podaj następujące wartości.

    ![Dodawanie danych wejściowych do zadania](./media/data-lake-store-stream-analytics/create.input.2.png "dodać danych wejściowych do zadania")

    * Dla **alias wejściowy**, wprowadź unikatową nazwę dla zadania danych wejściowych.
    * Aby uzyskać **typ źródła**, wybierz pozycję **strumienia danych**.
    * Aby uzyskać **źródła**, wybierz pozycję **magazynu obiektów Blob**.
    * Aby uzyskać **subskrypcji**, wybierz pozycję **Użyj magazynu obiektów blob z bieżącej subskrypcji**.
    * Aby uzyskać **konta magazynu**, wybierz konto magazynu, który został utworzony jako część wymagań wstępnych. 
    * Aby uzyskać **kontenera**, wybierz kontener, który został utworzony w wybranego konta magazynu.
    * Aby uzyskać **format serializacji zdarzeń**, wybierz pozycję **CSV**.
    * Aby uzyskać **ogranicznik**, wybierz pozycję **kartę**.
    * Aby uzyskać **kodowanie**, wybierz pozycję **UTF-8**.

    Kliknij przycisk **Utwórz**. Portal teraz dodaje dane wejściowe i testuje połączenie do niego.


## <a name="create-a-data-lake-store-output-for-the-job"></a>Utwórz dane wyjściowe zadania usługi Data Lake Store

1. Otwórz stronę do zadania usługi analiza strumienia, kliknij przycisk **dane wyjściowe** , a następnie kliknij pozycję **Dodaj**.

    ![Dodawanie danych wyjściowych do zadania](./media/data-lake-store-stream-analytics/create.output.1.png "dodać danych wyjściowych do zadania")

2. Na **nowe dane wyjściowe** bloku, podaj następujące wartości.

    ![Dodawanie danych wyjściowych do zadania](./media/data-lake-store-stream-analytics/create.output.2.png "dodać danych wyjściowych do zadania")

    * Aby uzyskać **alias wyjściowy**, wprowadź unikatową nazwę dla danych wyjściowych zadania. Jest to przyjazna nazwa używana w zapytaniach do kierowania wyników zapytania do tej usługi Data Lake Store.
    * Aby uzyskać **Sink**, wybierz pozycję **usługi Data Lake Store**.
    * Pojawi się autoryzacja dostępu do konta usługi Data Lake Store. Kliknij przycisk **autoryzować**.

3. Na **nowe dane wyjściowe** bloku, podaj następujące wartości w dalszym ciągu.

    ![Dodawanie danych wyjściowych do zadania](./media/data-lake-store-stream-analytics/create.output.3.png "dodać danych wyjściowych do zadania")

    * Aby uzyskać **nazwa konta**, wybierz utworzone miejsce zadania dane wyjściowe do wysłania do konta usługi Data Lake Store.
    * Aby uzyskać **wzorzec prefiksu ścieżki**, wprowadź ścieżkę pliku używany do zapisywania plików w ramach określonego konta usługi Data Lake Store.
    * Aby uzyskać **format daty**, jeśli token daty jest używany w ścieżce prefiksu, można wybrać format daty, w którym pliki są organizowane.
    * Dla **format czasu**, jeśli token czasu używany w ścieżce prefiks określić format czasu, w którym pliki są organizowane.
    * Aby uzyskać **format serializacji zdarzeń**, wybierz pozycję **CSV**.
    * Aby uzyskać **ogranicznik**, wybierz pozycję **kartę**.
    * Aby uzyskać **kodowanie**, wybierz pozycję **UTF-8**.
    
    Kliknij przycisk **Utwórz**. Portal teraz dodaje dane wyjściowe i testuje połączenie do niego.
    
## <a name="run-the-stream-analytics-job"></a>Uruchamianie zadania usługi Stream Analytics

1. Aby uruchomić zadanie usługi Stream Analytics, należy uruchomić kwerendę, **zapytania** kartę. W tym samouczku, można uruchomić przykładowe zapytanie, zastępując symbole zastępcze zadania danych wejściowych i wyjściowych aliasy, jak pokazano w poniższym zrzucie ekranu.

    ![Uruchom zapytanie](./media/data-lake-store-stream-analytics/run.query.png ", uruchom zapytanie")

2. Kliknij przycisk **zapisać** od góry ekranu, a następnie z **omówienie** , kliknij pozycję **Start**. W oknie dialogowym wybierz **czasu niestandardowe**, a następnie ustaw bieżącą datę i godzinę.

    ![Ustawianie czasu zadania](./media/data-lake-store-stream-analytics/run.query.2.png "ustawić czas pracy")

    Kliknij przycisk **Start** można uruchomić zadania. Może potrwać kilka minut, aby uruchomić zadanie.

3. Aby uruchomić zadanie do pobrania danych z obiektu blob, skopiuj przykładowy plik danych do kontenera obiektów blob. Można pobrać przykładowy plik danych z [repozytorium Git programu Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt). W tym samouczku, skopiuj plik **vehicle1_09142014.csv**. Można użyć różnych klientów, takie jak [Eksploratora usługi Storage Azure](http://storageexplorer.com/), aby przekazać dane do kontenera obiektów blob.

4. Z **omówienie** , w obszarze **monitorowanie**, zobacz przetwarzaniu danych.

    ![Zadanie monitora](./media/data-lake-store-stream-analytics/run.query.3.png "zadania monitora")

5. Na koniec można sprawdzić, czy dane dane wyjściowe zadania są dostępne w ramach konta usługi Data Lake Store. 

    ![Sprawdź dane wyjściowe](./media/data-lake-store-stream-analytics/run.query.4.png "Sprawdź dane wyjściowe")

    W okienku Eksplorator danych ustawienia w danych wyjściowych Zwróć uwagę, że dane wyjściowe są zapisywane w ścieżce folderu określone w usłudze Data Lake Store (`streamanalytics/job/output/{date}/{time}`).  

## <a name="see-also"></a>Zobacz także
* [Tworzenie klastra usługi HDInsight do użycia usługi Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
