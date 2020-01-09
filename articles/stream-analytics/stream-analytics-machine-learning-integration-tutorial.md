---
title: Integracja usługi Azure Stream Analytics z usługą Azure Machine Learning
description: W tym artykule opisano, jak szybko skonfigurować proste zadanie usługi Azure Stream Analytics, która integruje usługi Azure Machine Learning, za pomocą funkcji zdefiniowanej przez użytkownika.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.custom: seodec18
ms.openlocfilehash: c683cfeadcc13e5112a4687e18db9338d3574cd3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75459589"
---
# <a name="perform-sentiment-analysis-with-azure-stream-analytics-and-azure-machine-learning-studio-classic-preview"></a>Wykonywanie analizy tonacji z Azure Stream Analytics i Azure Machine Learning Studio (wersja zapoznawcza)
W tym artykule opisano sposób szybkiego skonfigurowania prostego zadania Azure Stream Analytics, które integruje Azure Machine Learning Studio (klasyczne). Używasz modelem analizy tonacji usługi Machine Learning w galerii Cortana Intelligence do analizowania danych przesyłanych strumieniowo tekstu i ustalić wyniku tonacji w czasie rzeczywistym. Przy użyciu pakietu Cortana Intelligence pozwala wykonać to zadanie, nie martwiąc się o niewymagającego tworzenia modelu analizy tonacji.

Możesz poprosić zdobytą wiedzę w tym artykule scenariuszy, takich jak te:

* Analiza tonacji w czasie rzeczywistym na strumieniu danych z usługi Twitter.
* Rozmowy dotyczące analizowania rekordów klienta z pracownikami działu pomocy technicznej.
* Ocena komentarze dotyczące forów, blogów i filmy wideo. 
* Wiele innych w czasie rzeczywistym, predykcyjne oceniania scenariuszy.

W rzeczywistych scenariuszy może uzyskać danych bezpośrednio z serwisu Twitter strumienia danych. Aby uprościć samouczka, jest napisany tak, aby zadanie usługi Stream Analytics pobiera tweety z pliku CSV w usłudze Azure Blob storage. Można utworzyć pliku CSV lub przykładowy plik CSV, można użyć, jak pokazano na poniższej ilustracji:

![Tweety przykładowych pokazano w pliku CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)  

Zadanie usługi Stream Analytics, które możesz utworzyć dotyczy modelu analizy tonacji w funkcji zdefiniowanej przez użytkownika (UDF) dotyczące przykładowych danych tekst z magazynu obiektów blob. Dane wyjściowe (wynik analizy tonacji) są zapisywane do tego samego magazynu obiektów blob w innym pliku CSV. 

Na poniższym rysunku pokazano tej konfiguracji. Jak wspomniano, bardziej realistyczny scenariusz, można zastąpić przesyłanie strumieniowe danych z usługi Twitter z danych wejściowych usługi Azure Event Hubs magazynu obiektów blob. Ponadto można zbudować [Microsoft Power BI](https://powerbi.microsoft.com/) wizualizacji w czasie rzeczywistym z agregacji wskaźniki nastrojów klientów.    

![Omówienie integracji usługi Stream Analytics Machine Learning](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)  

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem upewnij się, że dysponujesz następującymi elementami:

* Aktywna subskrypcja platformy Azure.
* Plik CSV z danymi w nim. Możesz pobrać plik przedstawionej wcześniej z [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/sampleinput.csv), lub można utworzyć własny plik. W tym artykule założono, że używany jest plik z repozytorium GitHub.

Na wysokim poziomie Aby wykonać zadania przedstawione w tym artykule wykonano następujące czynności:

1. Tworzenie konta usługi Azure storage i kontenera magazynu obiektów blob i przekaż plik wejściowy formacie CSV do kontenera.
3. Dodaj model analizy tonacji z Cortana Intelligence Gallery do obszaru roboczego Azure Machine Learning Studio (klasyczny) i Wdróż ten model jako usługę sieci Web w obszarze roboczym Machine Learning.
5. Utwórz zadanie usługi Stream Analytics, które wywołuje ta usługa sieci web jako funkcję w celu określenia wskaźniki nastrojów klientów dotyczące wprowadzania tekstu.
6. Uruchamianie zadania usługi Stream Analytics i sprawdzanie danych wyjściowych.

## <a name="create-a-storage-container-and-upload-the-csv-input-file"></a>Utwórz kontener magazynu i przekazywanie pliku wejściowego pliku CSV
W tym kroku można użyć dowolnego pliku CSV, takiego jak dostępnego w witrynie GitHub.

1. W witrynie Azure portal kliknij pozycję **Utwórz zasób** > **magazynu** > **konta magazynu**.

2. Podaj nazwę (`samldemo` w przykładzie). Nazwę można użyć tylko małe litery i cyfry, a na platformie Azure musi być unikatowa. 

3. Określ istniejącą grupę zasobów, a następnie określ lokalizację. Dla lokalizacji zaleca się, że wszystkie zasoby utworzone w ramach tego samouczka użyj tej samej lokalizacji.

    ![Podaj szczegóły konta magazynu](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account1.png)

4. W witrynie Azure portal wybierz konto magazynu. W bloku kont magazynu kliknij **kontenery** a następnie kliknij przycisk  **+ &nbsp;kontenera** do utworzenia magazynu obiektów blob.

    ![Tworzenie kontenera magazynu obiektów blob na dane wejściowe](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account2.png)

5. Podaj nazwę dla kontenera (`azuresamldemoblob` w przykładzie) i sprawdź, czy **dostęp typu** ustawiono **Blob**. Gdy skończysz, kliknij przycisk **OK**.

    ![Określ szczegóły kontenera obiektów blob](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account3.png)

6. W **kontenery** bloku wybierz nowy kontener, który zostanie otwarty blok dla tego kontenera.

7. Kliknij pozycję **Przekaż**.

    ![Przycisk "Przekaż" dla kontenera](./media/stream-analytics-machine-learning-integration-tutorial/create-sa-upload-button.png)

8. W **przekazywanie obiektu blob** bloku, przekazywania **sampleinput.csv** pliku, który został wcześniej pobrany. Dla **typu Blob**, wybierz opcję **blokowych obiektów blob** i ustaw rozmiar bloku do 4 MB, w której jest wystarczająca na potrzeby tego samouczka.

9. Kliknij przycisk **przekazywanie** znajdujący się u dołu bloku.

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Dodawanie modelu analizy tonacji z galerii Cortana Intelligence

Teraz, gdy jest przykładowe dane w obiekcie blob, aby umożliwić modelu analizy tonacji w galerii Cortana Intelligence.

1. Przejdź do [modelu analizy predykcyjnej tonacji](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) strony w galerii Cortana Intelligence.  

2. Kliknij przycisk **Otwórz w programie Studio**.  
   
   ![Stream Analytics Machine Learning, otwórz Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. Zaloguj się przejść do obszaru roboczego. Wybierz lokalizację.

4. Kliknij przycisk **Uruchom** w dolnej części strony. Uruchamia proces, który trwa około minuty.

   ![Uruchom eksperyment w usłudze Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-run-experiment.png)  

5. Po pomyślnym uruchomieniu procesu wybierz **wdrażanie usługi sieci Web** w dolnej części strony.

   ![Wdrażanie eksperymentu w usłudze Machine Learning Studio, jako usługę sieci web](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-deploy-web-service.png)  

6. Aby zweryfikować, że model analizy tonacji jest gotowy do użycia, kliknij pozycję **testu** przycisku. Podaj tekst, dane wejściowe, takie jak "Uwielbiam Microsoft". 

   ![Test eksperymentu w usłudze Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test.png)  

    Jeśli test działa, zostanie wyświetlony wynik podobny do poniższego przykładu:

   ![wyniki testu w usłudze Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test-results.png)  

7. W **aplikacje** kolumny, kliknij przycisk **Excel 2010 lub starszej skoroszytu** link, aby pobrać skoroszyt programu Excel. Skoroszyt zawiera klucz interfejsu API i adres URL, które będą potrzebne później do skonfigurowania zadania usługi Stream Analytics.

    ![Stream Analytics Machine Learning, szybkiego dostępu](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  


## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>Utwórz zadanie usługi Stream Analytics, które korzysta z modelu usługi Machine Learning

Można teraz utworzyć zadanie usługi Stream Analytics, które odczytuje tweetów przykładowe z pliku CSV w magazynie obiektów blob. 

### <a name="create-the-job"></a>Tworzenie zadania

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).  

2. Kliknij przycisk **Utwórz zasób** > **Internet of Things** > **zadania usługi Stream Analytics**. 

3. Nadaj nazwę zadaniu `azure-sa-ml-demo`, określ subskrypcję, określ istniejącą grupę zasobów lub Utwórz nową i wybierz lokalizację dla zadania.

   ![Określ ustawienia dla nowego zadania usługi Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/create-stream-analytics-job-1.png)
   

### <a name="configure-the-job-input"></a>Konfigurowanie danych wejściowych zadania
To zadanie pobiera dane wejściowe z pliku CSV, który został wcześniej przekazany do magazynu obiektów blob.

1. Po utworzeniu zadania, w obszarze **topologia zadań** w bloku zadania kliknij **dane wejściowe** opcji.    

2. W **dane wejściowe** bloku kliknij **Dodaj dane wejściowe Stream** >**magazynu obiektów Blob**

3. Wypełnij **magazynu obiektów Blob** bloku następującymi wartościami:

   
   |Pole  |Wartość  |
   |---------|---------|
   |**Alias wejściowy** | Użyj nazwy `datainput` i wybierz **wybierz usługi blob storage z subskrypcji**       |
   |**Konto magazynu**  |  Wybierz konto magazynu, która została utworzona wcześniej.  |
   |**Kontener**  | Wybierz wcześniej utworzonego kontenera (`azuresamldemoblob`)        |
   |**Format serializacji zdarzeń**  |  Wybierz **CSV**       |

   ![Ustawienia dla nowe dane wejściowe zadania usługi Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-create-sa-input-new-portal.png)

1. Kliknij pozycję **Zapisz**.

### <a name="configure-the-job-output"></a>Konfigurowanie danych wyjściowych zadania
Zadanie wysyła wyniki do tego samego magazynu obiektów blob, których pobiera dane wejściowe. 

1. W obszarze **topologia zadań** w bloku zadania kliknij **dane wyjściowe** opcji.  

2. W **dane wyjściowe** bloku kliknij **Dodaj** >**magazynu obiektów Blob**, a następnie dodaj dane wyjściowe z aliasem `datamloutput`. 

3. Wypełnij **magazynu obiektów Blob** bloku następującymi wartościami:

   |Pole  |Wartość  |
   |---------|---------|
   |**Alias wyjściowy** | Użyj nazwy `datamloutput` i wybierz **wybierz usługi blob storage z subskrypcji**       |
   |**Konto magazynu**  |  Wybierz konto magazynu, która została utworzona wcześniej.  |
   |**Kontener**  | Wybierz wcześniej utworzonego kontenera (`azuresamldemoblob`)        |
   |**Format serializacji zdarzeń**  |  Wybierz **CSV**       |

   ![Ustawienia dla nowe dane wyjściowe zadania usługi Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/create-stream-analytics-output.png) 

4. Kliknij pozycję **Zapisz**.   


### <a name="add-the-machine-learning-function"></a>Dodawanie funkcji usługi Machine Learning 
Wcześniej publikowane modelu usługi Machine Learning z usługą sieci web. W tym scenariuszu po uruchomieniu zadania analizy Stream wysyła każdego tweetu próbki z danych wejściowych do usługi sieci web do analizy tonacji. Usługa sieci web Machine Learning zwraca tonacji (`positive`, `neutral`, lub `negative`) i prawdopodobieństwo tweet jest dodatni. 

W tej części samouczka należy zdefiniować funkcję w zadaniu Stream analizy. Funkcja może być użyta do wysłania tweetu z usługą sieci web i otrzymać zwrotnie odpowiedź. 

1. Upewnij się, że masz adres sieci web usługi adresu URL i klucz API pobranego wcześniej w skoroszycie programu Excel.

2. Przejdź do bloku zadania > **funkcje** >  **+ Dodaj** > **usługi Azure ml**

3. Wypełnij **funkcji usługi Azure Machine Learning** bloku następującymi wartościami:

   |Pole  |Wartość  |
   |---------|---------|
   | **Alias funkcji** | Użyj nazwy `sentiment` i wybierz **ustawienia funkcji zapewniają usługi Azure Machine Learning ręcznie** które zapewnia możliwość wprowadź adres URL i klucz.      |
   | **Adres URL**| Wklej adres URL usługi sieci web.|
   |**Klucz** | Wklej klucz interfejsu API. |
  
   ![Ustawienia, aby dodać funkcji usługi Machine Learning do zadania usługi Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/add-machine-learning-function.png)  
    
4. Kliknij pozycję **Zapisz**.

### <a name="create-a-query-to-transform-the-data"></a>Utwórz zapytanie do przekształcania danych

Stream Analytics używa zapytania deklaratywne, oparte na SQL Sprawdź dane wejściowe i przetworzyć te dane. W tej sekcji utworzysz zapytanie, które odczytuje każdego tweetu z danych wejściowych, a następnie wywołuje funkcję usługi Machine Learning, aby przeprowadzić analizę tonacji. Zapytanie wysyła następnie wynik w danych wyjściowych zdefiniowane (magazyn obiektów blob).

1. Wróć do bloku przeglądu zadania.

2.  W obszarze **topologia zadań**, kliknij przycisk **zapytania** pole.

3. Wprowadź następujące zapytanie:

    ```SQL
    WITH sentiment AS (  
    SELECT text, sentiment1(text) as result 
    FROM datainput  
    )  

    SELECT text, result.[Score]  
    INTO datamloutput
    FROM sentiment  
    ```    

    Zapytanie wywołuje funkcję, została utworzona wcześniej (`sentiment`) w celu przeprowadzania analizy tonacji dla każdego tweetu w danych wejściowych. 

4. Kliknij przycisk **Zapisz** Aby zapisać kwerendę.


## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Uruchamianie zadania usługi Stream Analytics i sprawdzanie danych wyjściowych

Teraz można uruchomić zadania usługi Stream Analytics.

### <a name="start-the-job"></a>Uruchamianie zadania
1. Wróć do bloku przeglądu zadania.

2. Kliknij przycisk **Start** w górnej części bloku.

3. W **zadanie rozpoczęcia**, wybierz opcję **niestandardowe**, a następnie wybierz jeden dzień wcześniejszą od przekazania pliku CSV do usługi blob storage. Gdy wszystko będzie gotowe, kliknij przycisk **Start**.  


### <a name="check-the-output"></a>Sprawdź dane wyjściowe
1. Pozwól zadanie było uruchamiane przez kilka minut, aż zobaczysz aktywność w **monitorowanie** pole. 

2. Jeśli masz narzędzie, które zwykle umożliwia zbadać zawartość magazynu obiektów blob, należy używać tego narzędzia, aby zbadać `azuresamldemoblob` kontenera. Alternatywnie wykonaj następujące czynności w witrynie Azure portal:

    1. W portalu Znajdź `samldemo` magazynu konta, a w ramach konta, Znajdź `azuresamldemoblob` kontenera. Zostaną wyświetlone dwa pliki w kontenerze: plik, który zawiera przykładowe tweety i pliku CSV, generowane przez zadania usługi Stream Analytics.
    2. Kliknij prawym przyciskiem myszy wygenerowany plik, a następnie wybierz pozycję **Pobierz**. 

   ![Pobieranie danych wyjściowych zadania CSV z magazynu obiektów Blob](./media/stream-analytics-machine-learning-integration-tutorial/download-output-csv-file.png)  

3. Otwórz wygenerowany plik CSV. Może wyglądać następująco:  
   
   ![Wyświetl uczenia maszynowego usługi Stream Analytics, CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  


### <a name="view-metrics"></a>Wyświetlanie metryk
Możesz również wyświetlić metryki dotyczące funkcji usługi Azure Machine Learning. Następujące metryki dotyczące funkcji są wyświetlane w **monitorowanie** pola w bloku zadania:

* **Funkcja żądania** wskazuje liczbę żądań wysyłanych do usługi sieci web Machine Learning.  
* **Funkcja zdarzenia** wskazuje liczbę zdarzeń w żądaniu. Domyślnie każde żądanie do usługi sieci web Machine Learning zawiera maksymalnie 1000 zdarzeń.  


## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Integracja interfejsu API REST i uczenia maszynowego](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)



