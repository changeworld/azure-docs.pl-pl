---
title: 'Szybki start: tworzenie indeksu wyszukiwania w języku Java przy użyciu interfejsów API REST'
titleSuffix: Azure Cognitive Search
description: W tym przewodniku Szybki start języka Java dowiedz się, jak utworzyć indeks, załadować dane i uruchomić kwerendy przy użyciu interfejsów API REST usługi Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: java
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 02/25/2020
ms.openlocfilehash: 0b07e934625b09e7f6249dc00865465147f6f0ba
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77624018"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-java-using-rest-apis"></a>Szybki start: tworzenie indeksu usługi Azure Cognitive Search w języku Java przy użyciu interfejsów API REST
> [!div class="op_single_selector"]
> * [Javascript](search-get-started-nodejs.md)
> * [C #](search-get-started-dotnet.md)
> * [Java](search-get-started-java.md)
> * [Portal](search-get-started-portal.md)
> * [Powershell](search-create-index-rest-api.md)
> * [Python](search-get-started-python.md)
> * [Postman](search-get-started-postman.md)

Utwórz aplikację konsoli Java, która tworzy, ładuje i wysyła kwerendy do indeksu wyszukiwania przy użyciu [intellij,](https://www.jetbrains.com/idea/) [java 11 SDK](/java/azure/jdk/?view=azure-java-stable)i [interfejsu API REST usługi Azure Cognitive Search.](/rest/api/searchservice/) Ten artykuł zawiera instrukcje krok po kroku dotyczące tworzenia aplikacji. Alternatywnie, można [pobrać i uruchomić pełną aplikację](/samples/azure-samples/azure-search-java-samples/java-sample-quickstart/).

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Do stworzenia i przetestowania tego przewodnika Szybki start użyliśmy następującego oprogramowania i usług:

+ [IntelliJ IDEA](https://www.jetbrains.com/idea/)

+ [Java 11 SDK](/java/azure/jdk/?view=azure-java-stable)

+ [Utwórz usługę Azure Cognitive Search](search-create-service-portal.md) lub znajdź [istniejącą usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach bieżącej subskrypcji. Możesz skorzystać z bezpłatnej usługi dla tego szybkiego startu.

<a name="get-service-info"></a>

## <a name="get-a-key-and-url"></a>Uzyskaj klucz i adres URL

Wywołania usługi wymagają punktu końcowego adresu URL i klucza dostępu przy każdym żądaniu. Usługa wyszukiwania jest tworzona z obu, więc jeśli dodano usługę Azure Cognitive Search do subskrypcji, wykonaj następujące kroki, aby uzyskać niezbędne informacje:

1. [Zaloguj się do witryny Azure portal](https://portal.azure.com/), a na stronie **przegląd** usługi wyszukiwania pobierz adres URL. Przykładowy punkt końcowy może wyglądać podobnie jak `https://mydemo.search.windows.net`.

2. W **ustawieniach** > **klawiszy**pobierz klucz administratora, aby uzyskać pełne prawa do usługi. Istnieją dwa wymienne klucze administracyjne, przewidziane dla ciągłości biznesowej w przypadku, gdy trzeba przewrócić jeden. Klucz podstawowy lub pomocniczy można używać w żądaniach dodawania, modyfikowania i usuwania obiektów.

   Utwórz też klucz zapytania. Jest najlepszym rozwiązaniem do wystawiania żądań zapytań z dostępem tylko do odczytu.

![Pobierz nazwę usługi i klucze administratora i zapytania](media/search-get-started-nodejs/service-name-and-keys.png)

Każde żądanie wysłane do usługi wymaga klucza interfejsu API. Prawidłowy klucz ustanawia relację zaufania dla danego żądania między aplikacją wysyłającą żądanie i usługą, która je obsługuje.

## <a name="set-up-your-environment"></a>Konfigurowanie środowiska

Zacznij od otwarcia IntelliJ IDEA i utworzenia nowego projektu.

### <a name="create-the-project"></a>Tworzenie projektu

1. Otwórz intellij idea i wybierz pozycję **Utwórz nowy projekt**.
1. Wybierz **Maven**.
1. Na liście **SDK projektu** wybierz sdk Java 11.

    ![Tworzenie projektu maven](media/search-get-started-java/java-quickstart-create-new-maven-project.png) 

1. W przypadku **grupyId** i `AzureSearchQuickstart` **artifactid**wprowadź .
1. Zaakceptuj pozostałe wartości domyślne, aby otworzyć projekt.

### <a name="specify-maven-dependencies"></a>Określanie zależności Maven

1. Wybierz **pozycję Ustawienia plików** > **Settings**.
1. W oknie **Ustawienia** wybierz pozycję **Kompilacja, wykonanie,** > **Narzędzia** > kompilacji wdrażania**Maven** > **Importowanie**.
1. Zaznacz automatyczne pole wyboru **Importuj projekty Maven** i kliknij przycisk **OK,** aby zamknąć okno. Wtyczki Maven i inne zależności będą teraz automatycznie synchronizowane po zaktualizowaniu pliku pom.xml w następnym kroku.

    ![Opcje importowania maven w ustawieniach IntelliJ](media/search-get-started-java/java-quickstart-settings-import-maven-auto.png)

1. Otwórz plik pom.xml i zastąp zawartość następującymi szczegółami konfiguracji Maven. Należą do nich odniesienia do [wtyczki Exec Maven i](https://www.mojohaus.org/exec-maven-plugin/) [interfejsu JSON API](https://javadoc.io/doc/org.glassfish/javax.json/1.0.2)

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>
    
        <groupId>AzureSearchQuickstart</groupId>
        <artifactId>AzureSearchQuickstart</artifactId>
        <version>1.0-SNAPSHOT</version>
        <build>
            <sourceDirectory>src</sourceDirectory>
            <plugins>
                <plugin>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>3.1</version>
                    <configuration>
                        <source>11</source>
                        <target>11</target>
                    </configuration>
                </plugin>
                <plugin>
                    <groupId>org.codehaus.mojo</groupId>
                    <artifactId>exec-maven-plugin</artifactId>
                    <version>1.6.0</version>
                    <executions>
                        <execution>
                            <goals>
                                <goal>exec</goal>
                            </goals>
                        </execution>
                    </executions>
                    <configuration>
                        <mainClass>main.java.app.App</mainClass>
                        <cleanupDaemonThreads>false</cleanupDaemonThreads>
                    </configuration>
                </plugin>
            </plugins>
        </build>
        <dependencies>
            <dependency>
                <groupId>org.glassfish</groupId>
                <artifactId>javax.json</artifactId>
                <version>1.0.2</version>
            </dependency>
        </dependencies>   
    </project>
    ```

### <a name="set-up-the-project-structure"></a>Konfigurowanie struktury projektu

1. Wybierz pozycję**Struktura projektu** **pliku** > .
1. Wybierz **polecenie Moduły**i rozwiń drzewo źródłowe, aby uzyskać dostęp do zawartości folderu. `src`  >   `main`
1. W `src`  >   `main`  >  `java` folderze `app` dodaj `service` i foldery. Aby to zrobić, `java` zaznacz folder, naciśnij klawisze Alt + Wstaw, a następnie wprowadź nazwę folderu.
1. W `src`  >   `main`  > `resources` folderze `app` dodaj `service` i foldery.

    Po zakończeniu drzewo projektu powinno wyglądać jak na poniższej ilustracji.

    ![Struktura katalogów projektu](media/search-get-started-java/java-quickstart-basic-code-tree.png)

1. Kliknij przycisk **OK**, aby zamknąć okno.

### <a name="add-azure-cognitive-search-service-information"></a>Dodawanie informacji o usłudze Azure Cognitive Search

1. W oknie **Projekt** rozwiń drzewo źródłowe, aby uzyskać dostęp do folderu, `src`  >   `main`  > `resources`  >  `app` i dodaj `config.properties` plik. Aby to zrobić, `app` zaznacz folder, naciśnij klawisze Alt + Wstaw, wybierz pozycję **Plik**, a następnie wprowadź nazwę pliku.

1. Skopiuj następujące ustawienia `<YOUR-SEARCH-SERVICE-NAME>`do `<YOUR-ADMIN-KEY>`nowego `<YOUR-QUERY-KEY>` pliku i zastąp , a także nazwę usługi i klucze. Jeśli punktem końcowym `https://mydemo.search.windows.net`usługi jest , nazwa usługi będzie "mydemo".

    ```java
        SearchServiceName=<YOUR-SEARCH-SERVICE-NAME>
        SearchServiceAdminKey=<YOUR-ADMIN-KEY>
        SearchServiceQueryKey=<YOUR-QUERY-KEY>
        IndexName=hotels-quickstart
        ApiVersion=2019-05-06
    ```

### <a name="add-the-main-method"></a>Dodaj metodę główną

1. W `src`  >   `main`  >  `java` folderze  >  dodaj `App` klasę. `app` Aby to zrobić, `app` wybierz folder, naciśnij klawisze Alt + Insert, wybierz pozycję **Klasa Języka Java**, a następnie wprowadź nazwę klasy.
1. Otwórz `App` klasę i zastąp zawartość następującym kodem. Ten kod `main` zawiera metodę. 

    Nieskomentowany kod odczytuje parametry usługi wyszukiwania i używa ich do utworzenia wystąpienia klienta usługi wyszukiwania. Kod klienta usługi wyszukiwania zostanie dodany w następnej sekcji.

    Skomentowany kod w tej klasie będzie bezkomentowany w dalszej części tego przewodnika Szybki start.

    ```java
    package main.java.app;
    
    import main.java.service.SearchServiceClient;
    import java.io.IOException;
    import java.util.Properties;
    
    public class App {
    
        private static Properties loadPropertiesFromResource(String resourcePath) throws IOException {
            var inputStream = App.class.getResourceAsStream(resourcePath);
            var configProperties = new Properties();
            configProperties.load(inputStream);
            return configProperties;
        }
    
        public static void main(String[] args) {
            try {
                var config = loadPropertiesFromResource("/app/config.properties");
                var client = new SearchServiceClient(
                        config.getProperty("SearchServiceName"),
                        config.getProperty("SearchServiceAdminKey"),
                        config.getProperty("SearchServiceQueryKey"),
                        config.getProperty("ApiVersion"),
                        config.getProperty("IndexName")
                );
    
    
    //Uncomment the next 3 lines in the 1 - Create Index section of the quickstart
    //            if(client.indexExists()){ client.deleteIndex();}
    //            client.createIndex("/service/index.json");
    //            Thread.sleep(1000L); // wait a second to create the index
    
    //Uncomment the next 2 lines in the 2 - Load Documents section of the quickstart
    //            client.uploadDocuments("/service/hotels.json");
    //            Thread.sleep(2000L); // wait 2 seconds for data to upload
    
    //Uncomment the following 5 search queries in the 3 - Search an index section of the quickstart
    //            // Query 1
    //            client.logMessage("\n*QUERY 1****************************************************************");
    //            client.logMessage("Search for: Atlanta'");
    //            client.logMessage("Return: All fields'");
    //            client.searchPlus("Atlanta");
    //
    //            // Query 2
    //            client.logMessage("\n*QUERY 2****************************************************************");
    //            client.logMessage("Search for: Atlanta");
    //            client.logMessage("Return: HotelName, Tags, Address");
    //            SearchServiceClient.SearchOptions options2 = client.createSearchOptions();
    //            options2.select = "HotelName,Tags,Address";
    //            client.searchPlus("Atlanta", options2);
    //
    //            //Query 3
    //            client.logMessage("\n*QUERY 3****************************************************************");
    //            client.logMessage("Search for: wifi & restaurant");
    //            client.logMessage("Return: HotelName, Description, Tags");
    //            SearchServiceClient.SearchOptions options3 = client.createSearchOptions();
    //            options3.select = "HotelName,Description,Tags";
    //            client.searchPlus("wifi,restaurant", options3);
    //
    //            // Query 4 -filtered query
    //            client.logMessage("\n*QUERY 4****************************************************************");
    //            client.logMessage("Search for: all");
    //            client.logMessage("Filter: Ratings greater than 4");
    //            client.logMessage("Return: HotelName, Rating");
    //            SearchServiceClient.SearchOptions options4 = client.createSearchOptions();
    //            options4.filter="Rating%20gt%204";
    //            options4.select = "HotelName,Rating";
    //            client.searchPlus("*",options4);
    //
    //            // Query 5 - top 2 results, ordered by
    //            client.logMessage("\n*QUERY 5****************************************************************");
    //            client.logMessage("Search for: boutique");
    //            client.logMessage("Get: Top 2 results");
    //            client.logMessage("Order by: Rating in descending order");
    //            client.logMessage("Return: HotelId, HotelName, Category, Rating");
    //            SearchServiceClient.SearchOptions options5 = client.createSearchOptions();
    //            options5.top=2;
    //            options5.orderby = "Rating%20desc";
    //            options5.select = "HotelId,HotelName,Category,Rating";
    //            client.searchPlus("boutique", options5);
    
            } catch (Exception e) {
                System.err.println("Exception:" + e.getMessage());
                e.printStackTrace();
            }
        }
    }
    ```

### <a name="add-the-http-operations"></a>Dodawanie operacji HTTP

1. W `src`  >   `main`  >  `java` folderze  >  dodaj`SearchServiceClient` klasę. `service` Aby to zrobić, `service` wybierz folder, naciśnij klawisze Alt + Insert, wybierz pozycję **Klasa Języka Java**, a następnie wprowadź nazwę klasy.
1. Otwórz `SearchServiceClient` klasę i zastąp zawartość następującym kodem. Ten kod zawiera operacje HTTP wymagane do korzystania z interfejsu API REST usługi Azure Cognitive Search. Dodatkowe metody tworzenia indeksu, przekazywania dokumentów i wykonywania zapytań o indeks zostaną dodane w dalszej sekcji.

    ```java
    package main.java.service;

    import javax.json.Json;
    import javax.net.ssl.HttpsURLConnection;
    import java.io.IOException;
    import java.io.StringReader;
    import java.net.HttpURLConnection;
    import java.net.URI;
    import java.net.http.HttpClient;
    import java.net.http.HttpRequest;
    import java.net.http.HttpResponse;
    import java.nio.charset.StandardCharsets;
    import java.util.Formatter;
    import java.util.function.Consumer;
    
        /* This class is responsible for implementing HTTP operations for creating the index, uploading documents and searching the data*/
        public class SearchServiceClient {
            private final String _adminKey;
            private final String _queryKey;
            private final String _apiVersion;
            private final String _serviceName;
            private final String _indexName;
            private final static HttpClient client = HttpClient.newHttpClient();
    
        public SearchServiceClient(String serviceName, String adminKey, String queryKey, String apiVersion, String indexName) {
            this._serviceName = serviceName;
            this._adminKey = adminKey;
            this._queryKey = queryKey;
            this._apiVersion = apiVersion;
            this._indexName = indexName;
        }

        private static HttpResponse<String> sendRequest(HttpRequest request) throws IOException, InterruptedException {
            logMessage(String.format("%s: %s", request.method(), request.uri()));
            return client.send(request, HttpResponse.BodyHandlers.ofString());
        }

        private static URI buildURI(Consumer<Formatter> fmtFn)
                {
                    Formatter strFormatter = new Formatter();
                    fmtFn.accept(strFormatter);
                    String url = strFormatter.out().toString();
                    strFormatter.close();
                    return URI.create(url);
        }
    
        public static void logMessage(String message) {
            System.out.println(message);
        }
    
        public static boolean isSuccessResponse(HttpResponse<String> response) {
            try {
                int responseCode = response.statusCode();
    
                logMessage("\n Response code = " + responseCode);
    
                if (responseCode == HttpURLConnection.HTTP_OK || responseCode == HttpURLConnection.HTTP_ACCEPTED
                        || responseCode == HttpURLConnection.HTTP_NO_CONTENT || responseCode == HttpsURLConnection.HTTP_CREATED) {
                    return true;
                }
    
                // We got an error
                var msg = response.body();
                if (msg != null) {
                    logMessage(String.format("\n MESSAGE: %s", msg));
                }
    
            } catch (Exception e) {
                e.printStackTrace();
            }
    
            return false;
        }
    
        public static HttpRequest httpRequest(URI uri, String key, String method, String contents) {
            contents = contents == null ? "" : contents;
            var builder = HttpRequest.newBuilder();
            builder.uri(uri);
            builder.setHeader("content-type", "application/json");
            builder.setHeader("api-key", key);
    
            switch (method) {
                case "GET":
                    builder = builder.GET();
                    break;
                case "HEAD":
                    builder = builder.GET();
                    break;
                case "DELETE":
                    builder = builder.DELETE();
                    break;
                case "PUT":
                    builder = builder.PUT(HttpRequest.BodyPublishers.ofString(contents));
                    break;
                case "POST":
                    builder = builder.POST(HttpRequest.BodyPublishers.ofString(contents));
                    break;
                default:
                    throw new IllegalArgumentException(String.format("Can't create request for method '%s'", method));
            }
            return builder.build();
        }
    }
    
    ```

### <a name="build-the-project"></a>Kompilowanie projektu

1. Sprawdź, czy projekt ma następującą strukturę.

    ![Struktura katalogów projektu](media/search-get-started-java/java-quickstart-basic-code-tree-plus-classes.png)

1. Otwórz okno narzędzia **Maven** i wykonaj ten `verify exec:java` 
 ![cel maven: Wykonaj cel maven: sprawdź exec:java](media/search-get-started-java/java-quickstart-execute-maven-goal.png)

Po zakończeniu przetwarzania poszukaj komunikatu SUKCES KOMPILACJI, po którym następuje kod zakończenia zero (0).

## <a name="1---create-index"></a>1 - Tworzenie indeksu

Definicja indeksu hoteli zawiera proste pola i jedno złożone pole. Przykładami prostego pola są "HotelName" lub "Opis". Pole "Adres" jest złożonym polem, ponieważ ma podpola, takie jak "Adres ulicy" i "Miasto". W tym przewodniku Szybki start definicja indeksu jest określona przy użyciu usługi JSON.

1. W oknie **Projekt** rozwiń drzewo źródłowe, aby uzyskać dostęp do folderu, `src`  >   `main`  > `resources`  >  `service` i dodaj `index.json` plik. Aby to zrobić, `app` zaznacz folder, naciśnij klawisze Alt + Wstaw, wybierz pozycję **Plik**, a następnie wprowadź nazwę pliku.

1. Otwórz `index.json` plik i wstaw następującą definicję indeksu.

    ```json
    {
      "name": "hotels-quickstart",
      "fields": [
        {
          "name": "HotelId",
          "type": "Edm.String",
          "key": true,
          "filterable": true
        },
        {
          "name": "HotelName",
          "type": "Edm.String",
          "searchable": true,
          "filterable": false,
          "sortable": true,
          "facetable": false
        },
        {
          "name": "Description",
          "type": "Edm.String",
          "searchable": true,
          "filterable": false,
          "sortable": false,
          "facetable": false,
          "analyzer": "en.lucene"
        },
        {
          "name": "Description_fr",
          "type": "Edm.String",
          "searchable": true,
          "filterable": false,
          "sortable": false,
          "facetable": false,
          "analyzer": "fr.lucene"
        },
        {
          "name": "Category",
          "type": "Edm.String",
          "searchable": true,
          "filterable": true,
          "sortable": true,
          "facetable": true
        },
        {
          "name": "Tags",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "filterable": true,
          "sortable": false,
          "facetable": true
        },
        {
          "name": "ParkingIncluded",
          "type": "Edm.Boolean",
          "filterable": true,
          "sortable": true,
          "facetable": true
        },
        {
          "name": "LastRenovationDate",
          "type": "Edm.DateTimeOffset",
          "filterable": true,
          "sortable": true,
          "facetable": true
        },
        {
          "name": "Rating",
          "type": "Edm.Double",
          "filterable": true,
          "sortable": true,
          "facetable": true
        },
        {
          "name": "Address",
          "type": "Edm.ComplexType",
          "fields": [
            {
              "name": "StreetAddress",
              "type": "Edm.String",
              "filterable": false,
              "sortable": false,
              "facetable": false,
              "searchable": true
            },
            {
              "name": "City",
              "type": "Edm.String",
              "searchable": true,
              "filterable": true,
              "sortable": true,
              "facetable": true
            },
            {
              "name": "StateProvince",
              "type": "Edm.String",
              "searchable": true,
              "filterable": true,
              "sortable": true,
              "facetable": true
            },
            {
              "name": "PostalCode",
              "type": "Edm.String",
              "searchable": true,
              "filterable": true,
              "sortable": true,
              "facetable": true
            },
            {
              "name": "Country",
              "type": "Edm.String",
              "searchable": true,
              "filterable": true,
              "sortable": true,
              "facetable": true
            }
          ]
        }
      ]
    }
    ```

    Nazwa indeksu będzie "hotels-quickstart". Atrybuty w polach indeksu określają sposób wyszukiwania indeksowanych danych w aplikacji. Na przykład `IsSearchable` atrybut musi być przypisany do każdego pola, które powinno zostać uwzględnione w wyszukiwaniu pełnotekstowym. Aby dowiedzieć się więcej o atrybutach, zobacz [Zbieranie pól i atrybuty pól](search-what-is-an-index.md#fields-collection).
    
    Pole `Description` w tym indeksie `analyzer` używa właściwości opcjonalnej do zastąpienia domyślnego analizatora języka Lucene. Pole `Description_fr` jest przy użyciu francuskiego analizatora `fr.lucene` Lucene, ponieważ przechowuje tekst francuski. Jest `Description` przy użyciu opcjonalnego analizatora języka firmy Microsoft en.lucene. Aby dowiedzieć się więcej o analizatorach, zobacz [Analizatory przetwarzania tekstu w usłudze Azure Cognitive Search.](search-analyzers.md)

1. Dodaj następujący kod `SearchServiceClient` do klasy. Te metody tworzenia adresów URL usługi Azure Cognitive Search REST, które tworzą i usuwają indeks i które określają, czy istnieje indeks. Metody również zażądać HTTP.

    ```java
    public boolean indexExists() throws IOException, InterruptedException {
        logMessage("\n Checking if index exists...");
        var uri = buildURI(strFormatter -> strFormatter.format(
                "https://%s.search.windows.net/indexes/%s/docs?api-version=%s&search=*",
                _serviceName,_indexName,_apiVersion));
        var request = httpRequest(uri, _adminKey, "HEAD", "");
        var response = sendRequest(request);
        return isSuccessResponse(response);
    }
    
    public boolean deleteIndex() throws IOException, InterruptedException {
        logMessage("\n Deleting index...");
        var uri = buildURI(strFormatter -> strFormatter.format(
                "https://%s.search.windows.net/indexes/%s?api-version=%s",
                _serviceName,_indexName,_apiVersion));
        var request = httpRequest(uri, _adminKey, "DELETE", "*");
        var response = sendRequest(request);
        return isSuccessResponse(response);
    }
    
    
    public boolean createIndex(String indexDefinitionFile) throws IOException, InterruptedException {
        logMessage("\n Creating index...");
        //Build the search service URL
        var uri = buildURI(strFormatter -> strFormatter.format(
                "https://%s.search.windows.net/indexes/%s?api-version=%s",
                _serviceName,_indexName,_apiVersion));
        //Read in index definition file
        var inputStream = SearchServiceClient.class.getResourceAsStream(indexDefinitionFile);
        var indexDef = new String(inputStream.readAllBytes(), StandardCharsets.UTF_8);
        //Send HTTP PUT request to create the index in the search service
        var request = httpRequest(uri, _adminKey, "PUT", indexDef);
        var response = sendRequest(request);
        return isSuccessResponse(response);
    }
    ```

1. Odkomentuj następujący `App` kod w klasie. Ten kod usuwa indeks "hotels-quickstart", jeśli istnieje, i tworzy nowy indeks na podstawie definicji indeksu w pliku "index.json". 

    Jednosekundowa pauza jest wstawiana po żądaniu tworzenia indeksu. Ta pauza gwarantuje, że indeks jest tworzony przed przekazaniem dokumentów.

    ```java
        if (client.indexExists()) { client.deleteIndex();}
          client.createIndex("/service/index.json");
          Thread.sleep(1000L); // wait a second to create the index
    ```

1. Otwórz okno narzędzia **Maven** i wykonaj ten cel maven:`verify exec:java`

    Po uruchomieniu kodu poszukaj komunikatu "Tworzenie indeksu", po którym następuje kod odpowiedzi 201. Ten kod odpowiedzi potwierdza, że indeks został utworzony. Uruchomienie powinno zakończyć się komunikatem sukces kompilacji i zerowym (0) kodem zakończenia.
    
## <a name="2---load-documents"></a>2 - Ładowanie dokumentów

1. W oknie **Projekt** rozwiń drzewo źródłowe, aby uzyskać dostęp do folderu, `src`  >   `main`  > `resources`  >  `service` i dodaj `hotels.json` plik. Aby to zrobić, `app` zaznacz folder, naciśnij klawisze Alt + Wstaw, wybierz pozycję **Plik**, a następnie wprowadź nazwę pliku.
1. Wstaw następujące dokumenty hotelowe do pliku.

    ```json
    {
      "value": [
        {
          "@search.action": "upload",
          "HotelId": "1",
          "HotelName": "Secret Point Motel",
          "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
          "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
          "Category": "Boutique",
          "Tags": [ "pool", "air conditioning", "concierge" ],
          "ParkingIncluded": "false",
          "LastRenovationDate": "1970-01-18T00:00:00Z",
          "Rating": 3.60,
          "Address": {
            "StreetAddress": "677 5th Ave",
            "City": "New York",
            "StateProvince": "NY",
            "PostalCode": "10022",
            "Country": "USA"
          }
        },
        {
          "@search.action": "upload",
          "HotelId": "2",
          "HotelName": "Twin Dome Motel",
          "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
          "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
          "Category": "Boutique",
          "Tags": [ "pool", "free wifi", "concierge" ],
          "ParkingIncluded": "false",
          "LastRenovationDate": "1979-02-18T00:00:00Z",
          "Rating": 3.60,
          "Address": {
            "StreetAddress": "140 University Town Center Dr",
            "City": "Sarasota",
            "StateProvince": "FL",
            "PostalCode": "34243",
            "Country": "USA"
          }
        },
        {
          "@search.action": "upload",
          "HotelId": "3",
          "HotelName": "Triple Landscape Hotel",
          "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
          "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
          "Category": "Resort and Spa",
          "Tags": [ "air conditioning", "bar", "continental breakfast" ],
          "ParkingIncluded": "true",
          "LastRenovationDate": "2015-09-20T00:00:00Z",
          "Rating": 4.80,
          "Address": {
            "StreetAddress": "3393 Peachtree Rd",
            "City": "Atlanta",
            "StateProvince": "GA",
            "PostalCode": "30326",
            "Country": "USA"
          }
        },
        {
          "@search.action": "upload",
          "HotelId": "4",
          "HotelName": "Sublime Cliff Hotel",
          "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
          "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
          "Category": "Boutique",
          "Tags": [ "concierge", "view", "24-hour front desk service" ],
          "ParkingIncluded": "true",
          "LastRenovationDate": "1960-02-06T00:00:00Z",
          "Rating": 4.60,
          "Address": {
            "StreetAddress": "7400 San Pedro Ave",
            "City": "San Antonio",
            "StateProvince": "TX",
            "PostalCode": "78216",
            "Country": "USA"
          }
        }
      ]
    }
    ```

1. Wstaw następujący kod `SearchServiceClient` do klasy. Ten kod tworzy adres URL usługi REST, aby przekazać dokumenty hotelu do indeksu, a następnie sprawia, że żądanie HTTP POST.

    ```java
    public boolean uploadDocuments(String documentsFile) throws IOException, InterruptedException {
        logMessage("\n Uploading documents...");
        //Build the search service URL
        var endpoint = buildURI(strFormatter -> strFormatter.format(
                "https://%s.search.windows.net/indexes/%s/docs/index?api-version=%s",
                _serviceName,_indexName,_apiVersion));
        //Read in the data to index
        var inputStream = SearchServiceClient.class.getResourceAsStream(documentsFile);
        var documents = new String(inputStream.readAllBytes(), StandardCharsets.UTF_8);
        //Send HTTP POST request to upload and index the data
        var request = httpRequest(endpoint, _adminKey, "POST", documents);
        var response = sendRequest(request);
        return isSuccessResponse(response);
    }
    ```

1. Odkomentuj następujący `App` kod w klasie. Ten kod przesyła dokumenty w "hotels.json" do indeksu.

    ```java
    client.uploadDocuments("/service/hotels.json");
    Thread.sleep(2000L); // wait 2 seconds for data to upload
    ```

    Dwusekundowa pauza jest wstawiana po żądaniu przekazywania, aby upewnić się, że proces ładowania dokumentu zostanie zakończony przed zapytaniem indeksu.

1. Otwórz okno narzędzia **Maven** i wykonaj ten cel maven:`verify exec:java`

    Ponieważ w poprzednim kroku utworzono indeks "hotels-quickstart", kod zostanie teraz usunięty i ponownie utworzony przed załadowaniem dokumentów hotelowych.

    Po uruchomieniu kodu poszukaj komunikatu "Przekazywanie dokumentów", po którym następuje kod odpowiedzi 200. Ten kod odpowiedzi potwierdza, że dokumenty zostały przekazane do indeksu. Uruchomienie powinno zakończyć się komunikatem sukces kompilacji i zerowym (0) kodem zakończenia.

## <a name="3---search-an-index"></a>3 — Przeszukiwanie indeksu

Po załadowaniu dokumentów hoteli możesz utworzyć zapytania wyszukiwania, aby uzyskać dostęp do danych hoteli.

1. Dodaj następujący kod `SearchServiceClient` do klasy. Ten kod tworzy adresy URL usługi Azure Cognitive Search REST do wyszukiwania indeksowanych danych i drukuje wyniki wyszukiwania.

    Klasa `SearchOptions` i `createSearchOptions` metoda umożliwiają określenie podzbioru dostępnych opcji zapytania interfejsu API interfejsu API usługi Azure Cognitive Search REST. Aby uzyskać więcej informacji na temat opcji kwerendy interfejsu API REST, zobacz [Dokumenty wyszukiwania (interfejs API REST usługi Azure Cognitive Search).](/rest/api/searchservice/search-documents)

    Metoda `SearchPlus` tworzy adres URL kwerendy wyszukiwania, wysyła żądanie wyszukiwania, a następnie drukuje wyniki na konsoli. 

    ```java
    public SearchOptions createSearchOptions() { return new SearchOptions();}

    //Defines available search parameters that can be set
    public static class SearchOptions {

        public String select = "";
        public String filter = "";
        public int top = 0;
        public String orderby= "";
    }

    //Concatenates search parameters to append to the search request
    private String createOptionsString(SearchOptions options)
    {
        String optionsString = "";
        if (options != null) {
            if (options.select != "")
                optionsString = optionsString + "&$select=" + options.select;
            if (options.filter != "")
                optionsString = optionsString + "&$filter=" + options.filter;
            if (options.top != 0)
                optionsString = optionsString + "&$top=" + options.top;
            if (options.orderby != "")
                optionsString = optionsString + "&$orderby=" +options.orderby;
        }
        return optionsString;
    }
    
    public void searchPlus(String queryString)
    {
        searchPlus( queryString, null);
    }
    
    public void searchPlus(String queryString, SearchOptions options) {
    
        try {
            String optionsString = createOptionsString(options);
            var uri = buildURI(strFormatter -> strFormatter.format(
                    "https://%s.search.windows.net/indexes/%s/docs?api-version=%s&search=%s%s",
                    _serviceName, _indexName, _apiVersion, queryString, optionsString));
            var request = httpRequest(uri, _queryKey, "GET", null);
            var response = sendRequest(request);
            var jsonReader = Json.createReader(new StringReader(response.body()));
            var jsonArray = jsonReader.readObject().getJsonArray("value");
            var resultsCount = jsonArray.size();
            logMessage("Results:\nCount: " + resultsCount);
            for (int i = 0; i <= resultsCount - 1; i++) {
                logMessage(jsonArray.get(i).toString());
            }
    
            jsonReader.close();
    
        }
        catch (Exception e) {
            e.printStackTrace();
        }
    
    }
    ```

1. W `App` klasie odkomentuj następujący kod. Ten kod konfiguruje pięć różnych zapytań, w tym tekst wyszukiwania, parametry zapytania i pola danych do zwrócenia. 

    ```java
    // Query 1
    client.logMessage("\n*QUERY 1****************************************************************");
    client.logMessage("Search for: Atlanta");
    client.logMessage("Return: All fields'");
    client.searchPlus("Atlanta");

    // Query 2
    client.logMessage("\n*QUERY 2****************************************************************");
    client.logMessage("Search for: Atlanta");
    client.logMessage("Return: HotelName, Tags, Address");
    SearchServiceClient.SearchOptions options2 = client.createSearchOptions();
    options2.select = "HotelName,Tags,Address";
    client.searchPlus("Atlanta", options2);

    //Query 3
    client.logMessage("\n*QUERY 3****************************************************************");
    client.logMessage("Search for: wifi & restaurant");
    client.logMessage("Return: HotelName, Description, Tags");
    SearchServiceClient.SearchOptions options3 = client.createSearchOptions();
    options3.select = "HotelName,Description,Tags";
    client.searchPlus("wifi,restaurant", options3);

    // Query 4 -filtered query
    client.logMessage("\n*QUERY 4****************************************************************");
    client.logMessage("Search for: all");
    client.logMessage("Filter: Ratings greater than 4");
    client.logMessage("Return: HotelName, Rating");
    SearchServiceClient.SearchOptions options4 = client.createSearchOptions();
    options4.filter="Rating%20gt%204";
    options4.select = "HotelName,Rating";
    client.searchPlus("*",options4);

    // Query 5 - top 2 results, ordered by
    client.logMessage("\n*QUERY 5****************************************************************");
    client.logMessage("Search for: boutique");
    client.logMessage("Get: Top 2 results");
    client.logMessage("Order by: Rating in descending order");
    client.logMessage("Return: HotelId, HotelName, Category, Rating");
    SearchServiceClient.SearchOptions options5 = client.createSearchOptions();
    options5.top=2;
    options5.orderby = "Rating%20desc";
    options5.select = "HotelId,HotelName,Category,Rating";
    client.searchPlus("boutique", options5);
    ```



    Istnieją dwa [sposoby dopasowywania terminów w zapytaniu:](search-query-overview.md#types-of-queries)wyszukiwanie pełnotekstowe i filtry. Pełnotekstowe zapytanie wyszukiwania wyszukuje jeden lub więcej terminów w `IsSearchable` polach w indeksie. Filtr jest wyrażeniem logicznym, `IsFilterable` które jest oceniane przez pola w indeksie. Wyszukiwanie pełnotekstowe i filtry można używać razem lub osobno.

1. Otwórz okno narzędzia **Maven** i wykonaj ten cel maven:`verify exec:java`

    Poszukaj podsumowania każdej kwerendy i jej wyników. Uruchomienie powinno zakończyć się komunikatem sukces kompilacji i zerowym (0) kodem zakończenia.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Podczas pracy we własnej subskrypcji, na końcu projektu, to dobry pomysł, aby usunąć zasoby, które nie są już potrzebne. Nadal uruchomione zasoby mogą generować koszty. Zasoby możesz usuwać pojedynczo lub możesz usunąć grupę zasobów, aby usunąć cały ich zestaw.

Zasoby można znaleźć i zarządzać nimi w portalu, korzystając z łącza **Wszystkie zasoby** lub **Grupy zasobów** w lewym okienku nawigacji.

Jeśli korzystasz z bezpłatnej usługi, należy pamiętać, że są ograniczone do trzech indeksów, indeksatorów i źródeł danych. Możesz usunąć poszczególne elementy w portalu, aby pozostać poniżej limitu. 

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start w języku Java przepracowano szereg zadań w celu utworzenia indeksu, załadowania go z dokumentami i uruchomienia kwerend. Jeśli masz komfort z podstawowych pojęć, zaleca się następujący artykuł, który zawiera listę operacji indeksatora w REST.

> [!div class="nextstepaction"]
> [Operacje indeksatora](/rest/api/searchservice/indexer-operations)
