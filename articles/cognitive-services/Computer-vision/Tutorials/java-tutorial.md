---
title: wykonywanie operacji na obrazach — Java
titlesuffix: Azure Cognitive Services
description: Zapoznaj się z podstawową aplikacją biblioteki Swing języka Java, która korzysta z interfejsu API przetwarzania obrazów w usłudze Azure Cognitive Services. Wykonaj optyczne rozpoznawanie znaków (OCR), twórz miniatury i korzystaj z funkcji wizualnych na obrazie.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.author: kefre
ms.custom: seodec18
ms.date: 04/30/2019
ms.openlocfilehash: a22308e0c7ff924205f715692d011a4572b2bdb8
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65232634"
---
# <a name="use-computer-vision-features-with-the-rest-api-and-java"></a>Użyj funkcji przetwarzania obrazów z interfejsu API REST i językiem Java

W tym samouczku przedstawiono funkcje interfejsu API REST przetwarzania obrazów usług Azure Cognitive Services.

Zapoznaj się z aplikacją biblioteki Swing języka Java, w której zastosowano interfejs API REST przetwarzania obrazów do optycznego rozpoznawania znaków (OCR), inteligentnego przycinania miniatur oraz wykrywania, kategoryzowania, tagowania i opisywania elementów wizualnych na obrazie (m.in. twarzy). Ten przykład umożliwia przesłanie adresu URL obrazu na potrzeby analizy lub przetwarzania. Ten przykład typu open source może posłużyć jako szablon do tworzenia własnej aplikacji w języku Java korzystającej z interfejsu API REST przetwarzania obrazów.

W tym samouczku omówiono, jak używać przetwarzania obrazów do następujących czynności:

> [!div class="checklist"]
> * Analizowanie obrazu
> * Identyfikowanie naturalnych lub sztucznych elementów krajobrazu na obrazie
> * Identyfikowanie osobistości na obrazie
> * Tworzenie wysokiej jakości miniatury na podstawie obrazu
> * Odczytywanie tekstu drukowanego z obrazu
> * Odczytywanie tekstu odręcznego z obrazu

Aplikacja języka Java typu Swing formularza został już zapisany, ale ma żadnych funkcji. W tym samouczku dodasz odpowiedni kod do obsługi interfejsu API REST przetwarzania obrazów w celu uzupełnienia funkcjonalności aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="platform-requirements"></a>Wymagania dotyczące platformy

Ten samouczek opracowano przy użyciu środowiska NetBeans IDE. W szczególności chodzi o wersję **Java SE** środowiska NetBeans, którą można [pobrać tutaj](https://netbeans.org/downloads/index.html).

### <a name="subscribe-to-computer-vision-api-and-get-a-subscription-key"></a>Subskrybowanie interfejsu API przetwarzania obrazów i uzyskanie klucza subskrypcji

Przed utworzeniem w przykładzie, możesz zasubskrybować interfejs API przetwarzania obrazów, który jest częścią usług Azure Cognitive Services. Aby uzyskać szczegółowe informacje na temat subskrypcji i zarządzania kluczami, zobacz temat [Subskrypcje](https://azure.microsoft.com/try/cognitive-services/). Na potrzeby tego samouczka dopuszczalne są klucze podstawowe i pomocnicze.

## <a name="acquire-incomplete-tutorial-project"></a>Uzyskiwanie niekompletne projekcie samouczka

### <a name="download-the-project"></a>Pobieranie projektu

1. Przejdź do repozytorium [Cognitive Services Java Computer Vision Tutorial](https://github.com/Azure-Samples/cognitive-services-java-computer-vision-tutorial).
1. Kliknij przycisk **Clone or download** (Sklonuj lub pobierz).
1. Kliknij pozycję **Download ZIP** (Pobierz plik ZIP), aby pobrać plik zip z projektem samouczka.

Nie ma potrzeby wyodrębniania zawartości pliku zip, ponieważ NetBeans importuje projekt z pliku zip.

### <a name="import-the-tutorial-project"></a>Importowanie projektu samouczka

Zaimportuj plik **cognitive-services-java-computer-vision-tutorial-master.zip** do środowiska NetBeans.

1. W środowisku NetBeans kliknij kolejno opcje **File (Plik)** > **Import project (Importuj projekt)**  > **From ZIP...(Z pliku ZIP)**. Zostanie wyświetlone okno dialogowe **Import Project(s) from ZIP** (Importuj projekt(y) z pliku ZIP).
1. W polu **ZIP File:** (Plik ZIP:) kliknij przycisk **Browse** (Przeglądaj), aby zlokalizować plik **cognitive-services-java-computer-vision-tutorial-master.zip**, a następnie kliknij przycisk **Open** (Otwórz).
1. Kliknij przycisk **Import** (Importuj) w oknie dialogowym **Import Project(s) from ZIP** (Importuj projekt(y) z pliku ZIP).
1. W panelu **Projects** (Projekty) rozwiń do opcji **ComputerVision (Przetwarzanie obrazów)** > **Source Packages (Pakiety źródłowe)** > **&lt;pakiet domyślny&gt;**. 
   Niektóre wersje środowiska NetBeans używają opcji **src** zamiast **Source Packages (Pakiety źródłowe)** > **&lt;pakiet domyślny&gt;**. W takim przypadku rozwiń pozycję **src**.
1. Kliknij dwukrotnie pozycję **MainFrame.java**, aby załadować plik do edytora NetBeans. Zostaje wyświetlona karta **Design** (Projektowanie) pliku **MainFrame.java**.
1. Kliknij kartę **Source** (Źródło), aby wyświetlić kod źródłowy języka Java.

### <a name="build-and-run-the-tutorial-project"></a>Kompilowanie i uruchamianie projektu samouczka

1. Naciśnij klawisz **F6**, aby skompilować i uruchomić aplikację samouczka.

    W aplikacji samouczka kliknij kartę, aby wyświetlić okienko dla tej funkcji. Przyciski mają puste metody, więc nie wykonują żadnych operacji.

    W dolnej części okna znajdują się pola **Subscription Key** (Klucz subskrypcji) i **Subscription Region** (Region subskrypcji). W tych polach należy wpisać ważny klucz subskrypcji i prawidłowy region dla niego. Aby uzyskać klucz subskrypcji, zobacz [Subskrypcje](https://azure.microsoft.com/try/cognitive-services/). Jeśli klucz subskrypcji pochodzi z bezpłatnej wersji próbnej dostępnej pod tym linkiem, domyślny region **westcentralus** jest poprawnym regionem dla Twoich kluczy subskrypcji.

1. Zakończ aplikację samouczka.

## <a name="add-tutorial-code-to-the-project"></a>Dodawanie kodu z samouczka dotyczącego do projektu

Aplikacja biblioteki Swing języka Java została skonfigurowana z sześcioma kartami. Każda karta reprezentuje inną funkcję przetwarzania obrazów (analiza, OCR itp.). Sześć sekcji samouczka jest od siebie niezależnych, więc można dodać jedną sekcję, wszystkie sześć sekcji lub dowolny podzestaw. Sekcje można dodawać w dowolnej kolejności.

### <a name="analyze-an-image"></a>Analizowanie obrazu

Funkcja Analiza w ramach przetwarzania obrazów skanuje obraz pod kątem ponad 2000 rozpoznawalnych obiektów, istot żywych, scenerii i akcji. Po zakończeniu funkcja Analiza zwraca obiekt JSON, który opisuje obraz między innymi za pomocą opisowych znaczników, analizy koloru i podpisów.

Aby posłużyć się funkcją Analiza w aplikacji z samouczka, wykonaj następujące czynności:

#### <a name="add-the-event-handler-code-for-the-analyze-button"></a>Dodaj kod procedury obsługi zdarzeń dla przycisku analiza

Metoda obsługi zdarzeń **analyzeImageButtonActionPerformed** czyści formularz, wyświetla obraz podany w adresie URL, a następnie wywołuje metodę **AnalyzeImage**, aby przeprowadzić analizę obrazu. Po zwróceniu wyniku metody **AnalyzeImage** wyświetla odpowiedź w postaci sformatowanego ciągu JSON w obszarze tekstowym **Odpowiedź**, wyodrębnia pierwszy podpis z obiektu **JSONObject** oraz wyświetla podpis i poziom ufności określający, czy podpis jest poprawny.

Skopiuj i wklej poniższy kod do metody **analyzeImageButtonActionPerformed**.

> [!NOTE]
> Środowisko NetBeans nie pozwala wklejać kodu do linii definicji metody (```private void```) ani do zamykającego nawiasu klamrowego tej metody. Aby skopiować kod, skopiuj linie między definicją metody a zamykającym nawiasem klamrowym i wklej je do zawartości metody.

```java
    private void analyzeImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL analyzeImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        analyzeImage.setIcon(new ImageIcon());
        analyzeCaptionLabel.setText("");
        analyzeResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            analyzeImageUrl = new URL(analyzeImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(analyzeImageUrl);
            scaleAndShowImage(bImage, analyzeImage);
        } catch(IOException e) {
            analyzeResponseTextArea.setText("Error loading Analyze image: " + e.getMessage());
            return;
        }
        
        // Analyze the image.
        JSONObject jsonObj = AnalyzeImage(analyzeImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        analyzeResponseTextArea.setText(jsonObj.toString(2));
                
        // Extract the text and confidence from the first caption in the description object.
        if (jsonObj.has("description") && jsonObj.getJSONObject("description").has("captions")) {

            JSONObject jsonCaption = jsonObj.getJSONObject("description").getJSONArray("captions").getJSONObject(0);
            
            if (jsonCaption.has("text") && jsonCaption.has("confidence")) {
                
                analyzeCaptionLabel.setText("Caption: " + jsonCaption.getString("text") + 
                        " (confidence: " + jsonCaption.getDouble("confidence") + ").");
            }
        }
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Dodanie otoki dla wywołania interfejsu API REST

Metoda **AnalyzeImage** opakowuje wywołanie interfejsu API REST, aby analizować obraz. Metoda zwraca obiekt **JSONObject** opisujący obraz lub wartość **null**, jeśli wystąpił błąd.

Skopiuj i wklej metodę **AnalyzeImage** poniżej metody **analyzeImageButtonActionPerformed**.

```java
    /**
     * Encapsulates the Microsoft Cognitive Services REST API call to analyze an image.
     * @param imageUrl: The string URL of the image to analyze.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject AnalyzeImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call for Analyze Image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseAnalyze;
            URIBuilder builder = new URIBuilder(uriString);

            // Request parameters. All of them are optional.
            builder.setParameter("visualFeatures", "Categories,Description,Color,Adult");
            builder.setParameter("language", "en");

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
 ```

#### <a name="run-the-analyze-function"></a>Uruchamianie funkcji analizy

Naciśnij klawisz **F6**, aby uruchomić aplikację. W polu **Subscription Key** (Klucz subskrypcji) umieść swój klucz subskrypcji, a następnie sprawdź, czy w polu **Subscription Region** (Region subskrypcji) korzystasz z poprawnego regionu. Wprowadź adres URL obrazu do analizy, a następnie kliknij przycisk **Analyze Image** (Analizuj obraz), aby przeprowadzić analizę obrazu i wyświetlić jej wynik.

### <a name="recognize-a-landmark"></a>Rozpoznawanie punktów terenowych

Funkcja Punkt terenowy przetwarzania obrazów analizuje obraz pod kątem wykrywania naturalnych i sztucznych charakterystycznych elementów krajobrazu, takich jak góry lub znane budowle. Po zakończeniu analizy funkcja Punkt terenowy zwraca obiekt JSON, który identyfikuje znalezione na obrazie charakterystyczne elementy krajobrazu.

Aby posłużyć się funkcją Punkt terenowy w aplikacji z samouczka, wykonaj następujące czynności:

#### <a name="add-the-event-handler-code-for-the-form-button"></a>Dodanie kodu procedury obsługi zdarzeń do przycisku formularza

Metoda obsługi zdarzeń **landmarkImageButtonActionPerformed** czyści formularz, wyświetla obraz podany w adresie URL, a następnie wywołuje metodę **LandmarkImage**, aby przeprowadzić analizę obrazu. Po zwróceniu wyniku metody **LandmarkImage** wyświetla odpowiedź w postaci sformatowanego ciągu JSON w obszarze tekstowym **Odpowiedź**, następnie wyodrębnia nazwę pierwszego elementu krajobrazu z obiektu **JSONObject** i wyświetla ją w oknie razem z poziomem ufności określającym, czy element krajobrazu został prawidłowo zidentyfikowany.

Skopiuj i wklej poniższy kod do metody **landmarkImageButtonActionPerformed**.

> [!NOTE]
> Środowisko NetBeans nie pozwala wklejać kodu do linii definicji metody (```private void```) ani do zamykającego nawiasu klamrowego tej metody. Aby skopiować kod, skopiuj linie między definicją metody a zamykającym nawiasem klamrowym i wklej je do zawartości metody.

```java
    private void landmarkImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL landmarkImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        landmarkImage.setIcon(new ImageIcon());
        landmarkCaptionLabel.setText("");
        landmarkResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            landmarkImageUrl = new URL(landmarkImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(landmarkImageUrl);
            scaleAndShowImage(bImage, landmarkImage);
        } catch(IOException e) {
            landmarkResponseTextArea.setText("Error loading Landmark image: " + e.getMessage());
            return;
        }
        
        // Identify the landmark in the image.
        JSONObject jsonObj = LandmarkImage(landmarkImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        landmarkResponseTextArea.setText(jsonObj.toString(2));
                
        // Extract the text and confidence from the first caption in the description object.
        if (jsonObj.has("result") && jsonObj.getJSONObject("result").has("landmarks")) {

            JSONObject jsonCaption = jsonObj.getJSONObject("result").getJSONArray("landmarks").getJSONObject(0);
            
            if (jsonCaption.has("name") && jsonCaption.has("confidence")) {

                landmarkCaptionLabel.setText("Caption: " + jsonCaption.getString("name") + 
                        " (confidence: " + jsonCaption.getDouble("confidence") + ").");
            }
        }
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Dodanie otoki dla wywołania interfejsu API REST

Metoda **LandmarkImage** opakowuje wywołanie interfejsu API REST, aby analizować obraz. Metoda zwraca obiekt **JSONObject** opisujący elementy krajobrazu znalezione na obrazie lub wartość **null**, jeśli wystąpił błąd.

Skopiuj i wklej metodę **LandmarkImage** poniżej metody **landmarkImageButtonActionPerformed**.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to identify a landmark in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject LandmarkImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to identify a Landmark in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseLandmark;
            URIBuilder builder = new URIBuilder(uriString);

            // Request parameters. All of them are optional.
            builder.setParameter("visualFeatures", "Categories,Description,Color");
            builder.setParameter("language", "en");

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

#### <a name="run-the-landmark-function"></a>Uruchamianie funkcji charakterystycznych elementów krajobrazu

Naciśnij klawisz **F6**, aby uruchomić aplikację. W polu **Subscription Key** (Klucz subskrypcji) umieść swój klucz subskrypcji, a następnie sprawdź, czy w polu **Subscription Region** (Region subskrypcji) korzystasz z poprawnego regionu. Kliknij kartę **Landmark** (Punkt terenowy), wprowadź adres URL obrazu zawierającego element krajobrazu, a następnie kliknij przycisk **Analyze Image** (Analizuj obraz), aby przeprowadzić analizę obrazu i wyświetlić jej wynik.

### <a name="recognize-celebrities"></a>Rozpoznawanie osobistości

Funkcja Osobistości w ramach przetwarzania obrazów analizuje obraz pod kątem występowania na nim sławnych osób. Po zakończeniu analizy funkcja Osobistości zwraca obiekt JSON, który identyfikuje znalezione na obrazie znane osoby.

Aby skorzystać z funkcji Osobistości w aplikacji z samouczka, wykonaj następujące czynności:

#### <a name="add-the-event-handler-code-for-the-celebrities-button"></a>Dodaj kod procedury obsługi zdarzeń dla przycisku osobistości

Metoda obsługi zdarzeń **celebritiesImageButtonActionPerformed** czyści formularz, wyświetla obraz podany w adresie URL, a następnie wywołuje metodę **CelebritiesImage**, aby przeprowadzić analizę obrazu. Po zwróceniu wyniku metody **CelebritiesImage** wyświetla odpowiedź w postaci sformatowanego ciągu JSON w obszarze tekstowym **Odpowiedź**, następnie wyodrębnia nazwę pierwszej osobistości z obiektu **JSONObject** i wyświetla ją w oknie razem z poziomem ufności określającym, czy osobistość została prawidłowo zidentyfikowana.

Skopiuj i wklej poniższy kod do metody **celebritiesImageButtonActionPerformed**.

> [!NOTE]
> Środowisko NetBeans nie pozwala wklejać kodu do linii definicji metody (```private void```) ani do zamykającego nawiasu klamrowego tej metody. Aby skopiować kod, skopiuj linie między definicją metody a zamykającym nawiasem klamrowym i wklej je do zawartości metody.

```java
    private void celebritiesImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL celebritiesImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        celebritiesImage.setIcon(new ImageIcon());
        celebritiesCaptionLabel.setText("");
        celebritiesResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            celebritiesImageUrl = new URL(celebritiesImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(celebritiesImageUrl);
            scaleAndShowImage(bImage, celebritiesImage);
        } catch(IOException e) {
            celebritiesResponseTextArea.setText("Error loading Celebrity image: " + e.getMessage());
            return;
        }
        
        // Identify the celebrities in the image.
        JSONObject jsonObj = CelebritiesImage(celebritiesImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        celebritiesResponseTextArea.setText(jsonObj.toString(2));
                
        // Extract the text and confidence from the first caption in the description object.
        if (jsonObj.has("result") && jsonObj.getJSONObject("result").has("celebrities")) {

            JSONObject jsonCaption = jsonObj.getJSONObject("result").getJSONArray("celebrities").getJSONObject(0);
            
            if (jsonCaption.has("name") && jsonCaption.has("confidence")) {

                celebritiesCaptionLabel.setText("Caption: " + jsonCaption.getString("name") + 
                        " (confidence: " + jsonCaption.getDouble("confidence") + ").");
            }
        }
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Dodanie otoki dla wywołania interfejsu API REST

Metoda **CelebritiesImage** opakowuje wywołanie interfejsu API REST, aby analizować obraz. Metoda zwraca obiekt **JSONObject** opisujący osobistości znalezione na obrazie lub wartość **null**, jeśli wystąpił błąd.

Skopiuj i wklej metodę **CelebritiesImage** poniżej metody **celebritiesImageButtonActionPerformed**.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to identify celebrities in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject CelebritiesImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to identify celebrities in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseCelebrities;
            URIBuilder builder = new URIBuilder(uriString);

            // Request parameters. All of them are optional.
            builder.setParameter("visualFeatures", "Categories,Description,Color");
            builder.setParameter("language", "en");

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

#### <a name="run-the-celebrities-function"></a>Uruchom funkcję osobistości

Naciśnij klawisz **F6**, aby uruchomić aplikację. W polu **Subscription Key** (Klucz subskrypcji) umieść swój klucz subskrypcji, a następnie sprawdź, czy w polu **Subscription Region** (Region subskrypcji) korzystasz z poprawnego regionu. Kliknij kartę **Celebrities** (Osobistości), wprowadź adres URL obrazu zawierającego osobistość, a następnie kliknij przycisk **Analyze Image** (Analizuj obraz), aby przeprowadzić analizę obrazu i wyświetlić jej wynik.

### <a name="intelligently-generate-a-thumbnail"></a>Inteligentne generowanie miniatur

Funkcja Miniatura przetwarzania obrazów generuje miniaturę na podstawie obrazu. Korzystając z funkcji **Inteligentne przycinanie**, funkcja Miniatura zidentyfikuje obszar zainteresowania w obszarze i wycentruje miniaturę na ten obszar, aby wygenerować bardziej zadowalające wizualnie obrazy miniatur.

Aby skorzystać z funkcji Miniatura w aplikacji z samouczka, wykonaj następujące czynności:

#### <a name="add-the-event-handler-code-for-the-thumbnail-button"></a>Dodaj kod procedury obsługi zdarzeń dla przycisku miniatury

Metoda obsługi zdarzeń **thumbnailImageButtonActionPerformed** czyści formularz, wyświetla obraz podany w adresie URL, a następnie wywołuje metodę **getThumbnailImage**, aby utworzyć miniaturę. Po zwróceniu wyniku metody **getThumbnailImage** metoda wyświetla wygenerowaną miniaturę.

Skopiuj i wklej poniższy kod do metody **thumbnailImageButtonActionPerformed**.

> [!NOTE]
> Środowisko NetBeans nie pozwala wklejać kodu do linii definicji metody (```private void```) ani do zamykającego nawiasu klamrowego tej metody. Aby skopiować kod, skopiuj linie między definicją metody a zamykającym nawiasem klamrowym i wklej je do zawartości metody.

```java
    private void thumbnailImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL thumbnailImageUrl;
        JSONObject jsonError[] = new JSONObject[1];
        
        // Clear out the previous image, response, and thumbnail, if any.
        thumbnailSourceImage.setIcon(new ImageIcon());
        thumbnailResponseTextArea.setText("");
        thumbnailImage.setIcon(new ImageIcon());

        // Display the image specified in the text box.
        try {
            thumbnailImageUrl = new URL(thumbnailImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(thumbnailImageUrl);
            scaleAndShowImage(bImage, thumbnailSourceImage);
        } catch(IOException e) {
            thumbnailResponseTextArea.setText("Error loading image to thumbnail: " + e.getMessage());
            return;
        }
        
        // Get the thumbnail for the image.
        BufferedImage thumbnail = getThumbnailImage(thumbnailImageUrl.toString(), jsonError);
        
        // A non-null value indicates error.
        if (jsonError[0] != null) {
            // Format and display the JSON error.
            thumbnailResponseTextArea.setText(jsonError[0].toString(2));
            return;
        }
        
        // Display the thumbnail.
        if (thumbnail != null) {
            scaleAndShowImage(thumbnail, thumbnailImage);
        }
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Dodanie otoki dla wywołania interfejsu API REST

Metoda **getThumbnailImage** opakowuje wywołanie interfejsu API REST, aby analizować obraz. Metoda zwraca wynik **BufferedImage**, który zawiera miniaturę, lub **null**, jeśli wystąpił błąd. Komunikat o błędzie zostanie zwrócony w pierwszym elemencie tablicy ciągów **jsonError**.

Skopiuj i wklej następującą metodę **getThumbnailImage** poniżej metody **thumbnailImageButtonActionPerformed**.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to create a thumbnail for an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A BufferedImage containing the thumbnail, or null if a runtime error occurs. In the case
     * of an error, the error message will be returned in the first element of the jsonError string array.
     */
    private BufferedImage getThumbnailImage(String imageUrl, JSONObject[] jsonError) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to identify celebrities in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseThumbnail;
            URIBuilder uriBuilder = new URIBuilder(uriString);

            // Request parameters.
            uriBuilder.setParameter("width", "100");
            uriBuilder.setParameter("height", "150");
            uriBuilder.setParameter("smartCropping", "true");

            // Prepare the URI for the REST API call.
            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity requestEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(requestEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // Check for success.
            if (response.getStatusLine().getStatusCode() == 200)
            {
                // Return the thumbnail.
                return ImageIO.read(entity.getContent());
            }
            else
            {
                // Format and display the JSON error message.
                String jsonString = EntityUtils.toString(entity);
                jsonError[0] = new JSONObject(jsonString);
                return null;
            }
        }
        catch (Exception e)
        {
            String errorMessage = e.getMessage();
            System.out.println(errorMessage);
            jsonError[0] = new JSONObject(errorMessage);
            return null;
        }
    }
```

#### <a name="run-the-thumbnail-function"></a>Uruchom funkcję miniatury

Naciśnij klawisz **F6**, aby uruchomić aplikację. W polu **Subscription Key** (Klucz subskrypcji) umieść swój klucz subskrypcji, a następnie sprawdź, czy w polu **Subscription Region** (Region subskrypcji) korzystasz z poprawnego regionu. Kliknij kartę **Thumbnail** (Miniatura), wprowadź adres URL obrazu, a następnie kliknij przycisk **Generate Thumbnail** (Generuj miniaturę), aby przeanalizować obraz i zobaczyć wynik.

### <a name="read-printed-text-ocr"></a>Odczytywanie tekstu drukowanego (OCR)

Funkcja optycznego rozpoznawania znaków (OCR) w ramach przetwarzania obrazów analizuje obraz tekstu drukowanego. Po zakończeniu analizy funkcja OCR zwraca obiekt JSON, który zawiera tekst i jego lokalizację na obrazie.

Aby skorzystać z funkcji OCR w aplikacji z samouczka, wykonaj następujące czynności:

#### <a name="add-the-event-handler-code-for-the-ocr-button"></a>Dodaj kod procedury obsługi zdarzeń dla przycisku optyczne rozpoznawanie znaków

Metoda obsługi zdarzeń **ocrImageButtonActionPerformed** czyści formularz, wyświetla obraz podany w adresie URL, a następnie wywołuje metodę **OcrImage**, aby przeprowadzić analizę obrazu. Po zwróceniu wyniku metody **OcrImage** wyświetla wykryty tekst w formie sformatowanego ciągu JSON w obszarze tekstowym **Odpowiedź**.

Skopiuj i wklej poniższy kod do metody **ocrImageButtonActionPerformed**.

> [!NOTE]
> Środowisko NetBeans nie pozwala wklejać kodu do linii definicji metody (```private void```) ani do zamykającego nawiasu klamrowego tej metody. Aby skopiować kod, skopiuj linie między definicją metody a zamykającym nawiasem klamrowym i wklej je do zawartości metody.

```java
    private void ocrImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL ocrImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        ocrImage.setIcon(new ImageIcon());
        ocrResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            ocrImageUrl = new URL(ocrImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(ocrImageUrl);
            scaleAndShowImage(bImage, ocrImage);
        } catch(IOException e) {
            ocrResponseTextArea.setText("Error loading OCR image: " + e.getMessage());
            return;
        }
        
        // Read the text in the image.
        JSONObject jsonObj = OcrImage(ocrImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        ocrResponseTextArea.setText(jsonObj.toString(2));
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Dodanie otoki dla wywołania interfejsu API REST

Metoda **OcrImage** opakowuje wywołanie interfejsu API REST, aby analizować obraz. Metoda zwraca obiekt **JSONObject** danych JSON zwróconych z wywołania lub wartość **null**, jeśli wystąpił błąd.

Skopiuj i wklej następującą metodę **OcrImage** poniżej metody **ocrImageButtonActionPerformed**.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to read text in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject OcrImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to read text in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseOcr;
            URIBuilder uriBuilder = new URIBuilder(uriString);

            // Request parameters.
            uriBuilder.setParameter("language", "unk");
            uriBuilder.setParameter("detectOrientation ", "true");

            // Prepare the URI for the REST API call.
            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

#### <a name="run-the-ocr-function"></a>Uruchamianie funkcji optycznego rozpoznawania znaków

Naciśnij klawisz **F6**, aby uruchomić aplikację. W polu **Subscription Key** (Klucz subskrypcji) umieść swój klucz subskrypcji, a następnie sprawdź, czy w polu **Subscription Region** (Region subskrypcji) korzystasz z poprawnego regionu. Kliknij kartę **OCR**, wprowadź adres URL obrazu tekstu drukowanego, a następnie kliknij przycisk **Read Image** (Odczytaj obraz), aby przeprowadzić analizę obrazu i wyświetlić jej wynik.

### <a name="read-handwritten-text-handwriting-recognition"></a>Odczytywanie tekstu odręcznego (rozpoznawanie pisma ręcznego)

Funkcja Rozpoznawanie pisma ręcznego w ramach przetwarzania obrazów analizuje obraz tekstu odręcznego. Po zakończeniu analizy funkcja Rozpoznawanie pisma ręcznego zwraca obiekt JSON, który zawiera tekst i jego lokalizację na obrazie.

Aby skorzystać z funkcji Rozpoznawanie pisma ręcznego w aplikacji z samouczka, wykonaj następujące czynności:

#### <a name="add-the-event-handler-code-for-the-handwriting-button"></a>Dodaj kod procedury obsługi zdarzeń dla przycisku pisma odręcznego

Metoda obsługi zdarzeń **handwritingImageButtonActionPerformed** czyści formularz, wyświetla obraz podany w adresie URL, a następnie wywołuje metodę **HandwritingImage**, aby przeprowadzić analizę obrazu. Po zwróceniu wyniku metody **HandwritingImage** wyświetla wykryty tekst w postaci sformatowanego ciągu JSON w obszarze tekstowym **Odpowiedź**.

Skopiuj i wklej poniższy kod do metody **handwritingImageButtonActionPerformed**.

> [!NOTE]
> Środowisko NetBeans nie pozwala wklejać kodu do linii definicji metody (```private void```) ani do zamykającego nawiasu klamrowego tej metody. Aby skopiować kod, skopiuj linie między definicją metody a zamykającym nawiasem klamrowym i wklej je do zawartości metody.

```java
    private void handwritingImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL handwritingImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        handwritingImage.setIcon(new ImageIcon());
        handwritingResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            handwritingImageUrl = new URL(handwritingImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(handwritingImageUrl);
            scaleAndShowImage(bImage, handwritingImage);
        } catch(IOException e) {
            handwritingResponseTextArea.setText("Error loading Handwriting image: " + e.getMessage());
            return;
        }
        
        // Read the text in the image.
        JSONObject jsonObj = HandwritingImage(handwritingImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        handwritingResponseTextArea.setText(jsonObj.toString(2));
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Dodanie otoki dla wywołania interfejsu API REST

Metoda **HandwritingImage** opakowuje dwa wywołania interfejsu API REST niezbędne do analizy obrazu. Ponieważ rozpoznawanie pisma ręcznego jest procesem zajmującym dużo czasu, jest on podzielony na dwa etapy. Pierwsze wywołanie przesyła obraz do przetworzenia; drugie pobiera wykryty tekst po zakończeniu przetwarzania.

Po pobraniu tekstu metoda **HandwritingImage** zwraca obiekt **JSONObject** opisujący tekst i lokalizacje tekstu lub wartość **null**, jeśli wystąpił błąd.

Skopiuj i wklej następującą metodę **HandwritingImage** poniżej metody **handwritingImageButtonActionPerformed**.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to read handwritten text in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject HandwritingImage(String imageUrl) {
        try (CloseableHttpClient textClient = HttpClientBuilder.create().build();
             CloseableHttpClient resultClient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to read text in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseHandwriting;
            URIBuilder uriBuilder = new URIBuilder(uriString);
            
            // Request parameters.
            uriBuilder.setParameter("handwriting", "true");

            // Prepare the URI for the REST API call.
            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response.
            HttpResponse textResponse = textClient.execute(request);
            
            // Check for success.
            if (textResponse.getStatusLine().getStatusCode() != 202) {
                // An error occurred. Return the JSON error message.
                HttpEntity entity = textResponse.getEntity();
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            }
            
            String operationLocation = null;

            // The 'Operation-Location' in the response contains the URI to retrieve the recognized text.
            Header[] responseHeaders = textResponse.getAllHeaders();
            for(Header header : responseHeaders) {
                if(header.getName().equals("Operation-Location"))
                {
                    // This string is the URI where you can get the text recognition operation result.
                    operationLocation = header.getValue();
                    break;
                }
            }
            
            // NOTE: The response may not be immediately available. Handwriting recognition is an
            // async operation that can take a variable amount of time depending on the length
            // of the text you want to recognize. You may need to wait or retry this operation.
            //
            // This example checks once per second for ten seconds.
            
            JSONObject responseObj = null;
            int i = 0;
            do {
                // Wait one second.
                Thread.sleep(1000);
                
                // Check to see if the operation completed.
                HttpGet resultRequest = new HttpGet(operationLocation);
                resultRequest.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());
                HttpResponse resultResponse = resultClient.execute(resultRequest);
                HttpEntity responseEntity = resultResponse.getEntity();
                if (responseEntity != null)
                {
                    // Get the JSON response.
                    String jsonString = EntityUtils.toString(responseEntity);
                    responseObj = new JSONObject(jsonString);
                }
            }
            while (i < 10 && responseObj != null &&
                    !responseObj.getString("status").equalsIgnoreCase("Succeeded"));
            
            // If the operation completed, return the JSON object.
            if (responseObj != null) {
                return responseObj;
            } else {
                // Return null for timeout error.
                System.out.println("Timeout error.");
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

#### <a name="run-the-handwriting-function"></a>Uruchom funkcję pisma odręcznego

Aby uruchomić aplikację, naciśnij klawisz **F6**. W polu **Subscription Key** (Klucz subskrypcji) umieść swój klucz subskrypcji, a następnie sprawdź, czy w polu **Subscription Region** (Region subskrypcji) korzystasz z poprawnego regionu. Kliknij kartę **Read Handwritten Text** (Odczytywanie tekstu odręcznego), wprowadź adres URL do obrazu tekstu odręcznego, a następnie kliknij przycisk **Read Image** (Odczytaj obraz), aby przeprowadzić analizę obrazu i wyświetlić jej wynik.

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku użyto interfejsu API REST dla przetwarzania obrazów za pomocą języka Java do testowania z wielu funkcji analizy dostępnych obrazów. Następnie zobacz dokumentację referencyjną, aby dowiedzieć się więcej na temat interfejsów API zaangażowane.

- [Interfejs REST API wizualizacji komputerowej](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)
