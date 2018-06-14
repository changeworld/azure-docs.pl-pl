---
title: Integracja usługi analiza strumienia Azure przy użyciu usługi Azure Machine Learning
description: W tym artykule opisano, jak szybko skonfigurować proste zadanie usługi analiza strumienia Azure, której zintegrowano usługi Azure Machine Learning, przy użyciu funkcji zdefiniowanych przez użytkownika.
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/16/2018
ms.openlocfilehash: 63648dfe02a0b5ed00d0a7206a6aabbe200f94c4
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
ms.locfileid: "31527916"
---
# <a name="performing-sentiment-analysis-by-using-azure-stream-analytics-and-azure-machine-learning"></a>Wykonywanie analiz wskaźniki nastrojów klientów przy użyciu usługi Azure Stream Analytics i usługi Azure Machine Learning
W tym artykule opisano, jak szybko skonfigurować proste zadanie usługi analiza strumienia Azure, której zintegrowano usługi Azure Machine Learning. Umożliwia modelu uczenia maszynowego analytics wskaźniki nastrojów klientów z Cortana Intelligence Gallery analizować dane przesyłane strumieniowo tekstu i określić wynik wskaźniki nastrojów klientów w czasie rzeczywistym. Przy użyciu pakietu Cortana Intelligence Suite pozwala wykonać to zadanie, nie martwiąc się o mogli dokładnie zapoznać się tworzenia modelu analizy wskaźniki nastrojów klientów.

Możesz zastosować uzyskanych informacji z tego artykułu do scenariuszy, takich jak te:

* Analizowanie w czasie rzeczywistym wskaźniki nastrojów klientów na przesyłanie strumieniowe danych Twitter.
* Analizowanie rekordy klienta rozmowy z pracownikami działu pomocy technicznej.
* Ocena komentarze dotyczące fora, blogi i wideo. 
* Wiele innych w czasie rzeczywistym, predykcyjnej oceniania scenariuszy.

W przypadku rzeczywistych jak dane bezpośrednio z serwisem Twitter strumienia danych. Aby uprościć samouczek, jest ona zapisywana tak, aby zadanie analizy przesyłania strumieniowego pobiera tweetów z pliku CSV w magazynie obiektów Blob Azure. Można utworzyć pliku CSV lub przykładowy plik CSV, można użyć, jak pokazano na poniższej ilustracji:

![Przykładowe tweetów w pliku CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)  

Zadanie analizy przesyłania strumieniowego, które możesz utworzyć stosuje modelu analizy wskaźniki nastrojów klientów w funkcji zdefiniowanej przez użytkownika (UDF) na przykładowych danych tekst z magazynu obiektów blob. Dane wyjściowe (wynik analizy wskaźniki nastrojów klientów) są zapisywane w tym samym magazynie obiektów blob w innym pliku CSV. 

Na poniższym rysunku pokazano tej konfiguracji. Jak wspomniano, dla scenariusza bardziej realistyczne można zastąpić strumienia danych Twitter z wejściem Azure Event Hubs magazynu obiektów blob. Ponadto można zbudować [Microsoft Power BI](https://powerbi.microsoft.com/) w czasie rzeczywistym wizualizację agregacji wskaźniki nastrojów klientów.    

![Omówienie integracji uczenia maszynowego usługi analiza strumienia](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)  

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem upewnij się, że należy dysponować następującymi elementami:

* Aktywna subskrypcja platformy Azure.
* Plik CSV z niektórych danych. Możesz pobrać plik przedstawiona wcześniej z [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/sampleinput.csv), lub można utworzyć własny plik. W tym artykule zakłada się, że używany jest plik z usługi GitHub.

Na wysokim poziomie Aby wykonać zadania zostało to pokazane w tym artykule wykonano następujące czynności:

1. Tworzenie konta magazynu platformy Azure i kontener magazynu obiektów blob i przekaż plik wejściowy formacie CSV do kontenera.
3. Dodaj modelu analizy wskaźniki nastrojów klientów Cortana Intelligence Gallery do swojego obszaru roboczego uczenia maszynowego Azure i wdrożyć ten model jako usługę sieci web w obszaru roboczego uczenia maszynowego.
5. Utwórz zadanie usługi analiza strumienia, który wywołuje tej usługi sieci web jako funkcję w celu ustalenia wskaźniki nastrojów klientów dla pola tekstowego.
6. Uruchom zadanie usługi analiza strumienia i sprawdź dane wyjściowe.

## <a name="create-a-storage-container-and-upload-the-csv-input-file"></a>Tworzenie kontenera magazynu i przekazywanie pliku wejściowego CSV
W tym kroku można użyć dowolnego pliku CSV, takie jak dostępne z usługi GitHub.

1. W portalu Azure kliknij **Utwórz zasób** > **magazynu** > **konta magazynu**.

2. Podaj nazwę (`samldemo` w przykładzie). Nazwę można użyć tylko małe litery i cyfry, i między Azure musi być unikatowa. 

3. Wybierz istniejącą grupę zasobów i określ lokalizację. Lokalizacja zaleca się, że wszystkie zasoby, które są tworzone w tym samouczku używają tej samej lokalizacji.

    ![Podaj szczegóły konta magazynu](./media/stream-analytics-machine-learning-integration-tutorial/create-sa1.png)

4. W portalu Azure wybierz konto magazynu. W bloku konto magazynu, kliknij przycisk **kontenery** , a następnie kliknij przycisk  **+ &nbsp;kontenera** do utworzenia magazynu obiektów blob.

    ![Tworzenie kontenera obiektów blob](./media/stream-analytics-machine-learning-integration-tutorial/create-sa2.png)

5. Podaj nazwę kontenera (`azuresamldemoblob` w przykładzie) i sprawdź, czy **dostęp typu** ustawiono **obiektu Blob**. Gdy skończysz, kliknij przycisk **OK**.

    ![Określ szczegóły kontenera obiektów blob](./media/stream-analytics-machine-learning-integration-tutorial/create-sa3.png)

6. W **kontenery** bloku, wybierz nowego kontenera, który spowoduje otwarcie bloku dla tego kontenera.

7. Kliknij pozycję **Przekaż**.

    ![Przekaż przycisk kontenera](./media/stream-analytics-machine-learning-integration-tutorial/create-sa-upload-button.png)

8. W **przekazywanie obiektu blob** bloku, Przekaż **sampleinput.csv** wcześniej pobranego pliku. Dla **typu obiektu Blob**, wybierz pozycję **blokowych obiektów blob** i ustaw rozmiar bloku 4 MB, co wystarcza w tym samouczku.

9. Kliknij przycisk **przekazać** przycisk w dolnej części bloku.

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Dodawanie modelu analizy wskaźniki nastrojów klientów z Cortana Intelligence Gallery

Teraz, dane przykładowe są w obiekcie blob, można włączyć model analizy wskaźniki nastrojów klientów w programie Cortana Intelligence Gallery.

1. Przejdź do [modelu analizy predykcyjnej wskaźniki nastrojów klientów](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) strony w Cortana Intelligence Gallery.  

2. Kliknij przycisk **Otwórz w Studio**.  
   
   ![Strumienia uczenia maszynowego analizy, otwórz Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. Zaloguj się przejść do obszaru roboczego. Wybierz lokalizację.

4. Kliknij przycisk **Uruchom** w dolnej części strony. Proces wykonywany, co trwa około minuty.

   ![Uruchom eksperyment w usłudze Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-run-experiment.png)  

5. Po pomyślnym uruchomieniu procesu wybierz **wdrażanie usługi sieci Web** w dolnej części strony.

   ![Wdrażanie eksperymentu w usłudze Machine Learning Studio jako usługę sieci web](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-deploy-web-service.png)  

6. Aby sprawdzić, czy wskaźniki nastrojów klientów modelu analytics jest gotowa do użycia, kliknij przycisk **testu** przycisku. Przekaż dane wejściowe, takie jak "Świetnie Microsoft". 

   ![Test eksperymentu w usłudze Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test.png)  

    Jeśli test działa, zostanie wyświetlony wynik jest podobny do poniższego przykładu:

   ![wyniki testu w usłudze Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test-results.png)  

7. W **aplikacje** kolumny, kliknij przycisk **programu Excel 2010 lub starszych skoroszytu** łącze, aby pobrać skoroszytu programu Excel. Skoroszyt zawiera klucz interfejsu API i adres URL, który trzeba później skonfigurować zadanie usługi Stream Analytics.

    ![Stream Analytics Machine Learning, szybkiego dostępu](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  


## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>Utwórz zadanie usługi Stream Analytics, która korzysta z modelu uczenia maszynowego

Można teraz utworzyć zadanie usługi Stream Analytics odczytujący tweetów przykładowe z pliku CSV w magazynie obiektów blob. 

### <a name="create-the-job"></a>Utwórz zadanie

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).  

2. Kliknij przycisk **Utwórz zasób** > **Internetu rzeczy** > **zadanie usługi Stream Analytics**. 

3. Nazwa zadania `azure-sa-ml-demo`, określ subskrypcję, określ istniejącą grupę zasobów lub Utwórz nową i wybierz lokalizację dla zadania.

   ![Określ ustawienia nowego zadania usługi analiza strumienia](./media/stream-analytics-machine-learning-integration-tutorial/create-job-1.png)
   

### <a name="configure-the-job-input"></a>Konfigurowanie danych wejściowych zadania
To zadanie pobiera jej danych wejściowych z pliku CSV, który został wcześniej przekazany do magazynu obiektów blob.

1. Po utworzeniu zadania, w obszarze **topologii zadania** w bloku zadania kliknij **dane wejściowe** opcji.    

2. W **dane wejściowe** bloku, kliknij przycisk **Dodawanie elementu wejściowego strumienia** >**magazynu obiektów Blob**

3. Wypełnianie **magazynu obiektów Blob** bloku następującymi wartościami:

   
   |Pole  |Wartość  |
   |---------|---------|
   |**Alias wejściowy** | Użyj nazwy `datainput` i wybierz **wybierz obiektu blob magazynu z subskrypcji**       |
   |**Konto magazynu**  |  Wybierz utworzone wcześniej konto magazynu.  |
   |**Kontener**  | Wybierz kontener utworzonym wcześniej (`azuresamldemoblob`)        |
   |**Format serializacji zdarzeń**  |  Wybierz **CSV**       |

   ![Ustawienia dla nowego zadania danych wejściowych.](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-create-sa-input-new-portal.png)

4. Kliknij pozycję **Zapisz**.

### <a name="configure-the-job-output"></a>Skonfiguruj dane wyjściowe zadania
Zadanie wysyła wyniki do tego samego magazynu obiektów blob, których pobiera dane wejściowe. 

1. W obszarze **topologii zadania** w bloku zadania kliknij **dane wyjściowe** opcji.  

2. W **dane wyjściowe** bloku, kliknij przycisk **Dodaj** >**magazynu obiektów Blob**, a następnie dodaj wyjście z aliasem `datamloutput`. 

3. Wypełnianie **magazynu obiektów Blob** bloku następującymi wartościami:

   |Pole  |Wartość  |
   |---------|---------|
   |**Alias wyjściowy** | Użyj nazwy `datamloutput` i wybierz **wybierz obiektu blob magazynu z subskrypcji**       |
   |**Konto magazynu**  |  Wybierz utworzone wcześniej konto magazynu.  |
   |**Kontener**  | Wybierz kontener utworzonym wcześniej (`azuresamldemoblob`)        |
   |**Format serializacji zdarzeń**  |  Wybierz **CSV**       |

   ![Ustawienia dla nowego dane wyjściowe zadania](./media/stream-analytics-machine-learning-integration-tutorial/create-output2.png) 

4. Kliknij pozycję **Zapisz**.   


### <a name="add-the-machine-learning-function"></a>Dodawanie funkcji Machine Learning 
Wcześniej publikowane modelu usługi Machine Learning z usługą sieci web. W tym scenariuszu gdy zostanie uruchomione zadanie analizy strumienia, wysyła każdego tweet próbki z danych wejściowych z usługą sieci web do analizy wskaźniki nastrojów klientów. Usługa sieci web uczenie maszynowe zwraca wskaźniki nastrojów klientów (`positive`, `neutral`, lub `negative`) i prawdopodobieństwo tweet jest dodatnia. 

W tej części samouczka można zdefiniować funkcję zadanie analizy strumienia. Funkcja może być wywoływany publikowała tweet z usługą sieci web i wrócić odpowiedzi. 

1. Upewnij się, że masz adres sieci web usługi adresu URL i klucz API pobranego wcześniej w skoroszycie programu Excel.

2. Przejdź do bloku z zadania > **funkcje** > **+ Dodaj** > **uczenie maszynowe Azure**

3. Wypełnianie **funkcji usługi Azure Machine Learning** bloku następującymi wartościami:

   |Pole  |Wartość  |
   |---------|---------|
   | **Alias — funkcja** | Użyj nazwy `sentiment` i wybierz **ustawień funkcji Podaj usługi Azure Machine Learning ręcznie** które zapewnia opcję, aby wprowadzić adres URL i klucza.      |
   | **Adres URL**| Wklej adres URL usługi sieci web.|
   |**Klucz** | Wklej klucz interfejsu API. |
  
   ![Ustawienia dla Dodawanie funkcji Machine Learning do zadania usługi analiza strumienia](./media/stream-analytics-machine-learning-integration-tutorial/add-function.png)  
    
4. Kliknij pozycję **Zapisz**.

### <a name="create-a-query-to-transform-the-data"></a>Utwórz kwerendę do przekształcania danych

Analiza strumienia używa deklaratywne, SQL na podstawie zapytania do badania danych wejściowych i go przetworzyć. W tej sekcji utworzysz kwerendę, która odczytuje tweet każdej z danych wejściowych, a następnie wywołuje funkcję uczenie maszynowe, aby przeprowadzić analizę wskaźniki nastrojów klientów. Zapytanie wysyła następnie wynik do danych wyjściowych zdefiniowania (magazynu obiektów blob).

1. Wróć do bloku omówienie zadania.

2.  W obszarze **topologii zadania**, kliknij przycisk **zapytania** pole.

3. Wprowadź następujące zapytanie:

    ```
    WITH sentiment AS (  
    SELECT text, sentiment(text) as result 
    FROM datainput  
    )  

    SELECT text, result.[Score]  
    INTO datamloutput
    FROM sentiment  
    ```    

    Zapytanie wywołuje funkcję utworzonym wcześniej (`sentiment`) w celu wykonywania analizy wskaźniki nastrojów klientów na każdym tweet w danych wejściowych. 

4. Kliknij przycisk **zapisać** Aby zapisać kwerendę.


## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Uruchom zadanie usługi analiza strumienia i sprawdź dane wyjściowe

Można teraz uruchomić zadania Stream Analytics.

### <a name="start-the-job"></a>Uruchom zadanie
1. Wróć do bloku omówienie zadania.

2. Kliknij przycisk **Start** w górnej części bloku.

3. W **rozpoczęcia zadania**, wybierz pozycję **niestandardowe**, a następnie wybierz jeden dzień przed po przekazaniu pliku CSV do magazynu obiektów blob. Gdy wszystko będzie gotowe, kliknij przycisk **Start**.  


### <a name="check-the-output"></a>Sprawdź dane wyjściowe
1. Let zadanie było uruchamiane kilka minut, aż zostanie wyświetlony działania w **monitorowanie** pole. 

2. Jeśli masz narzędzie, które zwykle jest używana do sprawdzenia zawartość magazynu obiektów blob, użyj tego narzędzia do sprawdzenia `azuresamldemoblob` kontenera. Alternatywnie wykonaj następujące kroki w portalu Azure:

    1. W portalu, Znajdź `samldemo` magazynu konta, a w ramach konta, Znajdź `azuresamldemoblob` kontenera. Zobacz dwa pliki w kontenerze: plik, który zawiera przykładowy tweetów i plik CSV wygenerowany przez zadanie usługi Stream Analytics.
    2. Kliknij prawym przyciskiem myszy wygenerowanego pliku, a następnie wybierz **Pobierz**. 

   ![Pobierz dane wyjściowe zadania CSV z magazynu obiektów Blob](./media/stream-analytics-machine-learning-integration-tutorial/download-output-csv-file.png)  

3. Otwórz wygenerowany plik CSV. Zostanie wyświetlony ekran podobny do następującego:  
   
   ![Wyświetl Stream Analytics Machine Learning, CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  


### <a name="view-metrics"></a>Wyświetlaj metryki
Można również wyświetlić metryk powiązanych funkcji usługi Azure Machine Learning. Następujące metryki dotyczące funkcji są wyświetlane w **monitorowanie** pole w bloku zadania:

* **Funkcja żądania** wskazuje liczbę żądań wysyłanych do usługi sieci web usługi Machine Learning.  
* **Działania zdarzenia** wskazuje liczbę zdarzeń w żądaniu. Domyślnie każde żądanie usługi sieci web usługi Machine Learning zawiera zdarzenia do 1000.  


## <a name="next-steps"></a>Kolejne kroki

* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Integracja interfejsu API REST i uczenia maszynowego](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)



