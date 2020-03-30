---
title: Integracja usługi Azure Stream Analytics z usługą Azure Machine Learning
description: W tym artykule opisano, jak szybko skonfigurować proste zadanie usługi Azure Stream Analytics, które integruje usługę Azure Machine Learning przy użyciu funkcji zdefiniowanej przez użytkownika.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/19/2020
ms.custom: seodec18
ms.openlocfilehash: b33aeeee03fa57d87a60fd4c1904d5e4a86dd004
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067090"
---
# <a name="perform-sentiment-analysis-with-azure-stream-analytics-and-azure-machine-learning-studio-classic"></a>Przeprowadzanie analizy tonacji za pomocą usługi Azure Stream Analytics i usługi Azure Machine Learning Studio (klasyczne)

W tym artykule opisano, jak szybko skonfigurować proste zadanie usługi Azure Stream Analytics, które integruje usługę Azure Machine Learning Studio (klasyczna). Model analizy opinii usługi Machine Learning z Galerii analizy analizy Cortany umożliwia analizowanie przesyłania strumieniowego danych tekstowych i określanie wyniku tonacji w czasie rzeczywistym. Korzystanie z pakietu Cortana Intelligence Suite umożliwia wykonanie tego zadania bez martwienia się o zawiłości tworzenia modelu analizy opinii.

> [!TIP]
> Zdecydowanie zaleca się używanie [plików UDF usługi Azure Machine Learning](machine-learning-udf.md) zamiast usługi Azure Machine Learning Studio (klasyczny) UDF w celu zwiększenia wydajności i niezawodności.

Możesz zastosować to, czego uczysz się z tego artykułu, do scenariuszy, takich jak:

* Analizowanie nastrojów w czasie rzeczywistym na przesyłaniu strumieniowe danych z Twittera.
* Analizowanie zapisów rozmów z klientami z personelem pomocy technicznej.
* Ocena komentarzy na forach, blogach i filmach. 
* Wiele innych scenariuszy punktacji predykcyjnej w czasie rzeczywistym.

W rzeczywistym scenariuszu można uzyskać dane bezpośrednio ze strumienia danych Twitter. Aby uprościć samouczek, jest napisany tak, aby zadanie usługi Streaming Analytics pobierać tweety z pliku CSV w magazynie obiektów Blob platformy Azure. Można utworzyć własny plik CSV lub użyć przykładowego pliku CSV, jak pokazano na poniższej ilustracji:

![Przykładowe tweety wyświetlane w pliku CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)  

Utworzone zadanie analizy przesyłania strumieniowego stosuje model analizy tonacji jako funkcję zdefiniowaną przez użytkownika (UDF) w przykładowych danych tekstowych z magazynu obiektów blob. Dane wyjściowe (wynik analizy tonacji) są zapisywane w tym samym magazynie obiektów blob w innym pliku CSV. 

Na poniższej ilustracji przedstawiono tę konfigurację. Jak wspomniano w bardziej realistycznym scenariuszu można zastąpić magazynu obiektów blob przesyłania strumieniowego danych Twitter z usługi Azure Event Hubs danych wejściowych. Ponadto można utworzyć wizualizację w czasie rzeczywistym usługi [Microsoft Power BI](https://powerbi.microsoft.com/) zagregowanych tonacji.    

![Omówienie integracji usługi Stream Analytics Machine Learning](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)  

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem upewnij się, że dysponujesz następującymi elementami:

* Aktywna subskrypcja platformy Azure.
* Plik CSV z pewnymi danymi. Możesz pobrać plik pokazany wcześniej z [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/sampleinput.csv)lub możesz utworzyć własny plik. W tym artykule zakłada się, że używasz pliku z gitHub.

Na wysokim poziomie, aby wykonać zadania zademonstrowane w tym artykule, wykonaj następujące czynności:

1. Utwórz konto magazynu platformy Azure i kontener magazynu obiektów blob i przekaż plik wejściowy w formacie CSV do kontenera.
3. Dodaj model analizy opinii z Galerii Analizy Cortany do klasycznego obszaru roboczego usługi Azure Machine Learning Studio i wdrażaj ten model jako usługę sieci web w obszarze roboczym uczenia maszynowego.
5. Utwórz zadanie usługi Stream Analytics, które wywołuje tę usługę sieci web jako funkcję w celu określenia tonacji dla wprowadzania tekstu.
6. Uruchom zadanie usługi Stream Analytics i sprawdź dane wyjściowe.

## <a name="create-a-storage-container-and-upload-the-csv-input-file"></a>Tworzenie kontenera magazynu i przekazywanie pliku wejściowego CSV
W tym kroku można użyć dowolnego pliku CSV, takiego jak dostępny w usłudze GitHub.

1. W witrynie Azure portal kliknij pozycję **Utwórz** > **konto magazynu****magazynu** > zasobów .

2. Podaj`samldemo` nazwę ( w przykładzie). Nazwa może używać tylko małych liter i cyfr i musi być unikatowa na platformie Azure. 

3. Określ istniejącą grupę zasobów i określ lokalizację. W przypadku lokalizacji zaleca się, aby wszystkie zasoby utworzone w tym samouczku używały tej samej lokalizacji.

    ![podaj szczegóły konta magazynu](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account1.png)

4. W witrynie Azure portal wybierz konto magazynu. W bloku konta magazynu kliknij pozycję **Kontenery,** a następnie kliknij pozycję ** + &nbsp;Kontener,** aby utworzyć magazyn obiektów blob.

    ![Tworzenie kontenera magazynu obiektów blob dla danych wejściowych](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account2.png)

5. Podaj nazwę kontenera (w`azuresamldemoblob` przykładzie) i sprawdź, czy typ programu **Access** jest ustawiony na Obiekt **Blob**. Gdy skończysz, kliknij przycisk **OK**.

    ![określanie szczegółów kontenera obiektów blob](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account3.png)

6. W **kontenery** bloku wybierz nowy kontener, który otwiera blok dla tego kontenera.

7. Kliknij pozycję **Przekaż**.

    ![Przycisk "Prześlij" dla kontenera](./media/stream-analytics-machine-learning-integration-tutorial/create-sa-upload-button.png)

8. W bloku **Przekaż obiekt blob** przekaż plik **sampleinput.csv,** który został pobrany wcześniej. W przypadku **typu obiektu Blob**wybierz pozycję **Blokuj obiekt blob** i ustaw rozmiar bloku na 4 MB, co jest wystarczające dla tego samouczka.

9. Kliknij przycisk **Przekaż** u dołu ostrza.

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Dodawanie modelu analizy opinii z Galerii analizy Cortany

Teraz, gdy przykładowe dane są w obiekcie blob, można włączyć model analizy tonacji w Cortana Intelligence Gallery.

1. Przejdź do strony [modelu analizy predykcyjnej analizy tonacji](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) w Galerii analizy Cortany.  

2. Kliknij **przycisk Otwórz w Studio**.  
   
   ![Usługa Stream Analytics Machine Learning, otwórz studio machine learning](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. Zaloguj się, aby przejść do obszaru roboczego. Wybierz lokalizację.

4. Kliknij **pozycję Uruchom** u dołu strony. Proces jest uruchamiany, co trwa około minuty.

   ![uruchamianie eksperymentu w udiocieli Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-run-experiment.png)  

5. Po pomyślnym uruchomieniu procesu wybierz pozycję **Wdrażanie usługi sieci Web** u dołu strony.

   ![wdrażanie eksperymentu w usłudze Machine Learning Studio jako usłudze sieci Web](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-deploy-web-service.png)  

6. Aby sprawdzić, czy model analizy tonacji jest gotowy do użycia, kliknij przycisk **Testuj.** Podaj wprowadzanie tekstu, takie jak "Kocham Microsoft". 

   ![eksperyment testowy w studio machine learning](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test.png)  

    Jeśli test działa, zostanie wyświetlony wynik podobny do następującego przykładu:

   ![wyniki testów w Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test-results.png)  

7. W kolumnie **Aplikacje** kliknij **łącze programu Excel 2010 lub wcześniejszy,** aby pobrać skoroszyt programu Excel. Skoroszyt zawiera klucz interfejsu API i adres URL, który jest potrzebny później do skonfigurowania zadania usługi Stream Analytics.

    ![Usługa Stream Analytics Machine Learning, szybki rzut oka](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  


## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>Tworzenie zadania usługi Stream Analytics korzystającego z modelu uczenia maszynowego

Teraz można utworzyć zadanie usługi Stream Analytics, które odczytuje przykładowe tweety z pliku CSV w magazynie obiektów blob. 

### <a name="create-the-job"></a>Tworzenie zadania

1. Przejdź do [witryny Azure portal](https://portal.azure.com).  

2. Kliknij **pozycję Utwórz zasób** > **Zadanie usługi Stream Analytics**w > **internecie**. 

3. Nazwij `azure-sa-ml-demo`zadanie, określ subskrypcję, określ istniejącą grupę zasobów lub utwórz nową i wybierz lokalizację zadania.

   ![określanie ustawień dla nowego zadania usługi Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/create-stream-analytics-job-1.png)
   

### <a name="configure-the-job-input"></a>Konfigurowanie danych wejściowych zadania
Zadanie pobiera swoje dane wejściowe z pliku CSV, który został przekazany wcześniej do magazynu obiektów blob.

1. Po utworzeniu zadania w obszarze **Topologia zadań** w bloku zadania kliknij opcję **Wejścia.**    

2. W bloku **Wejścia** kliknij pozycję Dodaj**magazyn obiektów blob** **wejściowych** >strumienia

3. Wypełnij blok **Magazyn obiektów blob** tymi wartościami:

   
   |Pole  |Wartość  |
   |---------|---------|
   |**Alias wejściowy** | Use the name `datainput` and select **Select blob storage from your subscription**       |
   |**Konto magazynu**  |  Wybierz konto usługi magazynu utworzone wcześniej.  |
   |**Kontener**  | Wybierz utworzony wcześniej kontener`azuresamldemoblob`( )        |
   |**Format serializacji zdarzeń**  |  Wybierz **CSV**       |

   ![Ustawienia nowego zadania usługi Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-create-sa-input-new-portal.png)

1. Kliknij przycisk **Zapisz**.

### <a name="configure-the-job-output"></a>Konfigurowanie danych wyjściowych zadania
Zadanie wysyła wyniki do tego samego magazynu obiektów blob, gdzie pobiera dane wejściowe. 

1. W obszarze **Topologia zadań** w bloku zadania kliknij opcję **Wyjścia.**  

2. W bloku **Wyjścia** kliknij pozycję **Dodaj** >**magazyn obiektów Blob**, `datamloutput`a następnie dodaj dane wyjściowe z aliasem . 

3. Wypełnij blok **Magazyn obiektów blob** tymi wartościami:

   |Pole  |Wartość  |
   |---------|---------|
   |**Alias wyjściowy** | Use the name `datamloutput` and select **Select blob storage from your subscription**       |
   |**Konto magazynu**  |  Wybierz konto usługi magazynu utworzone wcześniej.  |
   |**Kontener**  | Wybierz utworzony wcześniej kontener`azuresamldemoblob`( )        |
   |**Format serializacji zdarzeń**  |  Wybierz **CSV**       |

   ![Ustawienia dla nowego zadania usługi Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/create-stream-analytics-output.png) 

4. Kliknij przycisk **Zapisz**.   


### <a name="add-the-machine-learning-function"></a>Dodawanie funkcji Uczenie maszynowe 
Wcześniej opublikowano model usługi uczenia maszynowego w usłudze sieci web. W tym scenariuszu po uruchomieniu zadania analizy strumienia wysyła każdy przykładowy tweet z danych wejściowych do usługi sieci web do analizy tonacji. Usługa sieci web uczenia maszynowego `neutral`zwraca `negative`tonację (`positive`, , lub ) i prawdopodobieństwo, że tweet jest dodatni. 

W tej sekcji samouczka można zdefiniować funkcję w pracy analizy strumienia. Funkcja może być wywołana, aby wysłać tweet do usługi sieci web i uzyskać odpowiedź z powrotem. 

1. Upewnij się, że masz adres URL usługi sieci Web i klucz interfejsu API pobrany wcześniej w skoroszycie programu Excel.

2. Przejdź do bloku zadań > **funkcje** > **+ Dodaj** > **usługę AzureML**

3. Wypełnij blok **funkcji usługi Azure Machine Learning** za pomocą następujących wartości:

   |Pole  |Wartość  |
   |---------|---------|
   | **Alias funkcji** | Użyj nazwy `sentiment` i wybierz **pozycję Podaj ustawienia funkcji usługi Azure Machine Learning ręcznie,** co daje możliwość wprowadzenia adresu URL i klucza.      |
   | **Adres URL**| Wklej adres URL usługi sieci Web.|
   |**Klucz** | Wklej klucz interfejsu API. |
  
   ![Ustawienia, aby dodać funkcję uczenia maszynowego do zadania usługi Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/add-machine-learning-function.png)  
    
4. Kliknij przycisk **Zapisz**.

### <a name="create-a-query-to-transform-the-data"></a>Tworzenie kwerendy w celu przekształcenia danych

Usługa Stream Analytics używa kwerendy deklaratywnej opartej na języku SQL do zbadania danych wejściowych i przetworzenia go. W tej sekcji utworzysz kwerendę, która odczytuje każdy tweet z danych wejściowych, a następnie wywołuje funkcję uczenia maszynowego w celu przeprowadzenia analizy tonacji. Następnie kwerenda wysyła wynik do danych wyjściowych, które zostały zdefiniowane (magazyn obiektów blob).

1. Powrót do bloku przeglądu zadań.

2.  W obszarze **Topologia zadań**kliknij pole **Kwerenda.**

3. Wprowadź następującą kwerendę:

    ```SQL
    WITH sentiment AS (  
    SELECT text, sentiment1(text) as result 
    FROM datainput  
    )  

    SELECT text, result.[Score]  
    INTO datamloutput
    FROM sentiment  
    ```    

    Kwerenda wywołuje funkcję utworzoną`sentiment`wcześniej ( ) w celu przeprowadzenia analizy tonacji na każdym tweecie w danych wejściowych. 

4. Kliknij przycisk **Zapisz**, aby zapisać zapytanie.


## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Uruchamianie zadania usługi Stream Analytics i sprawdzanie danych wyjściowych

Teraz możesz rozpocząć zadanie analizy strumienia.

### <a name="start-the-job"></a>Uruchamianie zadania
1. Powrót do bloku przeglądu zadań.

2. Kliknij **przycisk Start** w górnej części bloku.

3. W **polu Zadanie Start**wybierz pozycję **Niestandardowe**, a następnie wybierz jeden dzień przed przekazaniem pliku CSV do magazynu obiektów blob. Po zakończeniu kliknij przycisk **Start**.  


### <a name="check-the-output"></a>Sprawdź dane wyjściowe
1. Pozwól pracy uruchomić przez kilka minut, aż zobaczysz aktywność w polu **Monitorowanie.** 

2. Jeśli masz narzędzie, które zwykle używasz do badania zawartości magazynu obiektów blob, użyj tego narzędzia do zbadania kontenera. `azuresamldemoblob` Alternatywnie wykonaj następujące kroki w witrynie Azure portal:

    1. W portalu znajdź `samldemo` konto magazynu, a na koncie znajdź `azuresamldemoblob` kontener. W kontenerze są widoczne dwa pliki: plik zawierający przykładowe tweety i plik CSV wygenerowany przez zadanie usługi Stream Analytics.
    2. Kliknij prawym przyciskiem myszy wygenerowany plik, a następnie wybierz polecenie **Pobierz**. 

   ![Pobieranie danych wyjściowych zadania CSV z magazynu obiektów Blob](./media/stream-analytics-machine-learning-integration-tutorial/download-output-csv-file.png)  

3. Otwórz wygenerowany plik CSV. Zobaczysz coś takiego jak w poniższym przykładzie:  
   
   ![Usługa Uczenia maszynowego usługi Stream Analytics, widok CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  


### <a name="view-metrics"></a>Wyświetlanie metryk
Można również wyświetlić metryki związane z funkcjami usługi Azure Machine Learning. Następujące metryki związane z funkcjami są wyświetlane w polu **Monitorowanie** w bloku zadań:

* **Żądania funkcji** wskazuje liczbę żądań wysłanych do usługi sieci web uczenia maszynowego.  
* **Zdarzenia funkcji** wskazuje liczbę zdarzeń w żądaniu. Domyślnie każde żądanie do usługi sieci web usługi uczenia maszynowego zawiera maksymalnie 1000 zdarzeń.  


## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Integracja interfejsu API REST i uczenia maszynowego](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)



