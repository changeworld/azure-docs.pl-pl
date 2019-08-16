---
title: 'Szybki start: Tworzenie indeksu Azure Search w języku Java'
description: Wyjaśnia, jak utworzyć indeks, załadować dane i uruchamiać zapytania przy użyciu języka Java i interfejsów API REST Azure Search.
services: search
author: jj09
manager: jlembicz
ms.service: search
ms.topic: conceptual
ms.date: 08/26/2018
ms.author: jjed
ms.custom: seodec2018, seo-java-july2019
ms.openlocfilehash: 7deb9d2cf16aa82de7ce4ea163652c2936819063
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2019
ms.locfileid: "69533239"
---
# <a name="quickstart-create-an-azure-search-index-in-java"></a>Szybki start: Tworzenie indeksu Azure Search w języku Java
> [!div class="op_single_selector"]
> * [Portal](search-get-started-portal.md)
> * [.NET](search-howto-dotnet-sdk.md)
> 
> 

Dowiedz się, jak utworzyć niestandardową aplikację wyszukiwania w języku Java, która do wyszukiwania używa usługi Azure Search. Ten samouczek używa [interfejsu API REST usługi Azure Search](https://msdn.microsoft.com/library/dn798935.aspx), aby konstruować obiekty i operacje używane w tym ćwiczeniu.

Do uruchomienia tego przykładu jest potrzebna usługa Azure Search, do której możesz zarejestrować się w witrynie [Azure Portal](https://portal.azure.com). Aby uzyskać szczegółowe instrukcje, zobacz [Create an Azure Search service in the portal](search-create-service-portal.md) (Tworzenie usługi Azure Search w portalu).

Do utworzenia i przetestowania przedstawionego przykładu użyto następującego oprogramowania:

* [Eclipse IDE for Java EE Developers](https://www.eclipse.org/downloads/packages/release/photon/r/eclipse-ide-java-ee-developers). Upewnij się, że pobierasz wersję EE. Jeden z kroków weryfikacji wymaga funkcji, która znajduje się tylko w tej wersji.
* [JDK 8u181](https://aka.ms/azure-jdks)
* [Apache Tomcat 8.5.33](https://tomcat.apache.org/download-80.cgi#8.5.33)

## <a name="about-the-data"></a>Informacje o danych
Ta przykładowa aplikacja korzysta z danych agencji [United States Geological Services (USGS)](https://geonames.usgs.gov/domestic/download_data.htm) zawężonych do stanu Rhode Island w celu zmniejszenia rozmiaru zestawu danych. Użyjemy tych danych do utworzenia aplikacji wyszukiwania, która zwraca punkty orientacyjne, takie jak szpitale i szkoły, jak również formy geologiczne, takie jak strumienie, jeziora i szczyty.

W tej aplikacji program **SearchServlet. Java** kompiluje i ładuje indeks przy użyciu konstrukcji [indeksatora](https://msdn.microsoft.com/library/azure/dn798918.aspx) , pobierając przefiltrowany zestaw danych agencji USGS z Azure SQL Database. Wstępnie zdefiniowane poświadczenia oraz informacje o połączeniu ze źródłem danych w trybie online są zawarte w kodzie programu. W zakresie dostępu do danych nie jest konieczna dalsza konfiguracja.

> [!NOTE]
> Zastosowaliśmy filtr dla tego zestawu danych, aby nie przekroczyć limitu 10 000 dokumentów obowiązującego w warstwie cenowej Bezpłatna. Jeśli korzystasz z warstwy Standardowa, to ograniczenie nie obowiązuje i możesz zmodyfikować ten kod tak, aby móc używać większego zestawu danych. Aby uzyskać szczegółowe informacje o pojemności dla każdej warstwy cenowej, zobacz [Limits and constraints](search-limits-quotas-capacity.md) (Limity i ograniczenia).
> 
> 

## <a name="about-the-program-files"></a>Informacje o plikach programu
Na poniższej liście opisano pliki, które mają zastosowanie w tym przykładzie.

* Wyszukaj. jsp: Udostępnia interfejs użytkownika
* SearchServlet.java: Zapewnia metody (podobnie jak w przypadku kontrolera w MVC)
* SearchServiceClient.java: Obsługuje żądania HTTP
* SearchServiceHelper.java: Klasa pomocnika, która udostępnia metody statyczne
* Dokument. Java: Zapewnia model danych
* config. Properties: Ustawia adres URL usługi wyszukiwania i`api-key`
* pom.xml: Zależność Maven

<a id="sub-2"></a>

## <a name="find-the-service-name-and-api-key-of-your-azure-search-service"></a>Znajdź nazwę usługi i `api-key` usługi Azure Search
Wszystkie wywołania interfejsu API REST do Azure Search wymagają podania adresu URL usługi i `api-key`. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Na pasku szybkiego dostępu wybierz pozycję **Usługa wyszukiwania** , aby wyświetlić listę wszystkich usług Azure Search, dla których Zainicjowano obsługę subskrypcji.
3. Wybierz usługę, której chcesz użyć.
4. Na pulpicie nawigacyjnym usługi zobaczysz kafelki z istotnymi informacjami, jak również ikonę klucza, służącą do uzyskiwania dostępu do kluczy administratora.
   
      ![Zrzut ekranu przedstawiający sposób uzyskiwania dostępu do kluczy administratora z poziomu pulpitu nawigacyjnego usługi][3]
5. Skopiuj adres URL usługi i klucz administratora. Będą Ci potrzebne później podczas dodawania ich do pliku **config.properties**.

## <a name="download-the-sample-files"></a>Pobieranie plików przykładowych
1. Przejdź do elementu [search-java-indexer-demo](https://github.com/Azure-Samples/search-java-indexer-demo) w witrynie GitHub.
2. Wybierz pozycję **Pobierz kod pocztowy**, Zapisz plik zip na dysku, a następnie Wyodrębnij wszystkie pliki, które zawiera. Rozważ wyodrębnienie plików do obszaru roboczego Java, aby ułatwić znajdowanie projektu w przyszłości.
3. Pliki przykładowe są tylko do odczytu. Kliknij prawym przyciskiem myszy folder właściwości i usuń atrybut tylko do odczytu.

Wszystkie kolejne modyfikacje plików i instrukcje uruchamiania będą wykonywane względem plików w tym folderze.  

## <a name="import-project"></a>Importowanie projektu
1. W obszarze zaćmienie wybierz pozycję **plik** > **Importuj** > **Ogólne** > **istniejące projekty do obszaru roboczego**.
   
    ![Zrzut ekranu przedstawiający sposób importowania istniejącego projektu][4]
2. W obszarze **Wybierz katalog główny** przejdź do folderu zawierającego pliki przykładowe. Wybierz folder, który zawiera folder .project. Projekt powinien zostać wyświetlony na liście **Projekty** jako wybrany element.
   
    ![Zrzut ekranu przedstawiający listę projektów w oknie Importuj projekty][12]
3. Wybierz pozycję **Finish** (Zakończ).
4. W obszarze **Eksplorator projektów** możesz wyświetlać i edytować pliki. Jeśli nie jest jeszcze otwarty, wybierz kolejno opcje **okno** > **Pokaż widok** > **Eksplorator projektu** lub użyj skrótu, aby go otworzyć.

## <a name="configure-the-service-url-and-api-key"></a>Skonfiguruj adres URL usługi i`api-key`
1. W **Eksploratorze projektu**kliknij dwukrotnie **plik config. Properties** , aby edytować ustawienia konfiguracji zawierające nazwę serwera i `api-key`.
2. Zapoznaj się z krokami opisanymi w tym artykule, w którym znaleziono adres `api-key` URL usługi i w [Azure Portal](https://portal.azure.com), aby uzyskać wartości, które zostaną wprowadzone do **pliku config. Properties**.
3. W **pliku config. Properties**Zamień "klucz interfejsu API" `api-key` na dla usługi. Następnie nazwa usługi (pierwszy składnik adresu URL https://servicename.search.windows.net) zastępuje "nazwę usługi" w tym samym pliku.
   
    ![Zrzut ekranu przedstawiający sposób zamiany klucza interfejsu API][5]

## <a name="configure-the-project-build-and-runtime-environments"></a>Konfigurowanie środowisk projektowania i kompilacji oraz środowisk uruchomieniowych
1. W środowisku Eclipse, w Eksploratorze projektów kliknij prawym przyciskiem myszy pozycję Projekt > **Właściwości** > **Aspekty projektu**.
2. Wybierz pozycje: **Dynamiczny moduł WWW**, **Java** i **JavaScript**.
   
    ![Zrzut ekranu przedstawiający sposób wybierania aspektów projektu dla projektu][6]
3. Wybierz przycisk **Zastosuj**.
4. Wybierz pozycję **Okna** > **Preferencje** > **Serwer** > **Środowiska wykonawcze** > **Dodaj...** .
5. Rozwiń pozycję Apache i wybierz wersję serwera Apache Tomcat, która została wcześniej zainstalowana. W naszym systemie zainstalowano wersję 8.
   
    ![Zrzut ekranu przedstawiający miejsce w oknie środowisko uruchomieniowe możesz wybrać wersję platformy Apache Tomcat][7]
6. Na następnej stronie podaj katalog instalacyjny serwera Tomcat. Na komputerze z systemem Windows najprawdopodobniej będzie to katalog C:\Program Files\Apache Software Foundation\Tomcat *wersja*.
7. Wybierz pozycję **Finish** (Zakończ).
8. Wybierz pozycję **Okna** > **Preferencje** > **Java** > **Zainstalowane środowiska JRE** > **Dodaj**.
9. W obszarze **Dodawanie środowiska JRE** wybierz opcję **Standardowa maszyna VM**.
10. Wybierz opcję **Dalej**.
11. W definicji środowiska JRE w domu JRE wybierz pozycję **katalog**.
12. Przejdź do katalogu **Program Files** > **Java** i wybierz zestaw JDK, który został wcześniej zainstalowany. Należy wybrać zestaw JDK jako środowisko JRE.
13. W obszarze zainstalowane środowiska JRE wybierz **JDK**. Twoje ustawienia powinny być podobne do tych na poniższym zrzucie ekranu.
    
    ![Zrzut ekranu przedstawiający sposób wybierania JDK jako zainstalowanego środowiska JRE][9]
14. Opcjonalnie można wybrać pozycję **Okna** > **Przeglądarka WWW** > **Internet Explorer**, aby otworzyć aplikację w oknie zewnętrznej przeglądarki. Używanie zewnętrznej przeglądarki zapewnia lepsze środowisko aplikacji internetowej.
    
    ![Zrzut ekranu przedstawiający sposób wybierania programu Internet Explorer jako zewnętrznego okna przeglądania][8]

Zadania konfiguracji zostały zakończone. W dalszej części skompilujesz i uruchomisz projekt.

## <a name="build-the-project"></a>Skompiluj projekt
1. W Eksploratorze projektów kliknij prawym przyciskiem myszy nazwę projektu i wybierz polecenie **Uruchom jako** > **Maven kompilację** , aby skonfigurować projekt.
   
    ![Zrzut ekranu przedstawiający sposób wybierania Maven kompilacja w oknie Eksplorator projektu][10]
2. W obszarze edytuj konfigurację w obszarze cele wprowadź wartość "czysta instalacja", a następnie wybierz pozycję **Uruchom**.

Komunikaty o stanie zostaną wyświetlone w oknie konsoli. Powinien zostać wyświetlony komunikat BUDOWANIE POWIODŁO SIĘ wskazujący, że kompilowanie projektu zakończyło się bez błędów.

## <a name="run-the-app"></a>Uruchamianie aplikacji
W tym ostatnim kroku uruchomisz aplikację w środowisku uruchomieniowym serwera lokalnego.

Jeśli środowisko uruchomieniowe serwera nie zostało jeszcze określone w środowisku Eclipse, należy to zrobić w pierwszej kolejności.

1. W obszarze Eksplorator projektów rozwiń pozycję **WebContent**.
2. Kliknij prawym przyciskiem myszy pozycję **Search.jsp** >  wybierz polecenie **Wykonaj jako** > **Wykonaj na serwerze**. Wybierz serwer Apache Tomcat, a następnie wybierz pozycję **Uruchom**.

> [!TIP]
> Jeśli do przechowywania projektu używasz obszaru roboczego innego niż domyślny, zmodyfikuj pole **Konfiguracje wykonywania**, aby wskazać lokalizację projektu w celu uniknięcia błędu podczas uruchamiania serwera. W obszarze Eksplorator projektów kliknij prawym przyciskiem myszy pozycję **Search.jsp** >  wybierz polecenie **Wykonaj jako** > **Konfiguracje wykonywania**. Wybierz serwer Apache Tomcat. Wybierz **argumenty**. Wybierz pozycję **obszar roboczy** lub **system plików** , aby ustawić folder zawierający projekt.
> 
> 

Po uruchomieniu aplikacji powinno zostać wyświetlone okno przeglądarki zawierające pole wyszukiwania służące do wprowadzania terminów.

Odczekaj około jednej minuty przed wybraniem opcji **Wyszukaj** , aby podać czas usługi do utworzenia i załadowania indeksu. Jeśli wystąpi błąd 404 protokołu HTTP, wystarczy poczekać nieco dłużej przed podjęciem ponownej próby.

## <a name="search-on-usgs-data"></a>Przeszukiwanie danych agencji USGS
Zestaw danych agencji USGS zawiera rekordy, które odnoszą się do stanu Rhode Island. W przypadku wybrania opcji **Wyszukaj** w pustym polu wyszukiwania zostaną wyświetlone górne wpisy 50, które są domyślne.

Wprowadzenie terminu wyszukiwania zaangażuje do pracy wyszukiwarkę. Spróbuj wprowadzić nazwę regionalną. „Roger Williams” był pierwszym gubernatorem stanu Rhode Island. Liczne parki, budynki i szkoły są nazwane jego imieniem.

![Zrzut ekranu przedstawiający sposób wyszukiwania danych agencji USGS][11]

Możesz też wprowadzić jeden z poniższych terminów:

* Pawtucket
* Pembroke
* goose +cape

## <a name="next-steps"></a>Następne kroki
To jest pierwszy samouczek usługi Azure Search bazujący na języku Java i zestawie danych agencji USGS. Wraz z upływem czasu będziemy rozszerzać ten samouczek, aby zademonstrować dodatkowe funkcje wyszukiwania, których mogą być przydatne w rozwiązaniach niestandardowych.

Jeśli masz już pewną wiedzę na temat usługi Azure Search, możesz użyć tego przykładu jako podstawy do dalszych eksperymentów. Na przykład możesz rozszerzyć [stronę wyszukiwania](search-pagination-page-layout.md) lub zaimplementować [nawigację aspektową](search-faceted-navigation.md). Możesz również ulepszyć stronę wyników wyszukiwania przez dodanie liczników i łączenie dokumentów w partie, aby użytkownicy mogli przechodzić do kolejnych stron wyników.

Dopiero zaczynasz korzystać z usługi Azure Search? Zalecamy wypróbować inne samouczki, aby poznać możliwości tworzenia w usłudze. Aby znaleźć więcej zasobów, odwiedź naszą [stronę dokumentacji](https://azure.microsoft.com/documentation/services/search/). 

<!--Image references-->
[1]: ./media/search-get-started-java/create-search-portal-1.PNG
[2]: ./media/search-get-started-java/create-search-portal-21.PNG
[3]: ./media/search-get-started-java/create-search-portal-31.PNG
[4]: ./media/search-get-started-java/AzSearch-Java-Import1.PNG
[5]: ./media/search-get-started-java/AzSearch-Java-config1.PNG
[6]: ./media/search-get-started-java/AzSearch-Java-ProjectFacets1.PNG
[7]: ./media/search-get-started-java/AzSearch-Java-runtime1.PNG
[8]: ./media/search-get-started-java/AzSearch-Java-Browser1.PNG
[9]: ./media/search-get-started-java/AzSearch-Java-JREPref1.PNG
[10]: ./media/search-get-started-java/AzSearch-Java-BuildProject1.PNG
[11]: ./media/search-get-started-java/rogerwilliamsschool1.PNG
[12]: ./media/search-get-started-java/AzSearch-Java-SelectProject.png
