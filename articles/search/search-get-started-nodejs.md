---
title: Rozpoczynanie pracy z usługą Azure Search w środowisku Node.js — Azure Search
description: Zapoznaj się z procesem tworzenia aplikacji wyszukiwania w hostowanej usłudze wyszukiwania w chmurze na platformie Azure przy użyciu języka programowania Node.js.
author: jj09
manager: jlembicz
services: search
ms.service: search
ms.topic: conceptual
ms.date: 04/26/2017
ms.author: jjed
ms.custom: seodec2018
ms.openlocfilehash: 1b37b3c52abd3750c3452a46bdf5b0c5954de4dd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61289207"
---
# <a name="get-started-with-azure-search-in-nodejs"></a>Wprowadzenie do usługi Azure Search w środowisku Node.js
> [!div class="op_single_selector"]
> * [Portal](search-get-started-portal.md)
> * [.NET](search-howto-dotnet-sdk.md)
> 
> 

Dowiedz się, jak utworzyć niestandardową aplikację wyszukiwania Node.js, która korzysta z usługi Azure Search jako środowiska wyszukiwania. Ten samouczek używa [interfejsu API REST usługi Azure Search](https://msdn.microsoft.com/library/dn798935.aspx), aby konstruować obiekty i operacje używane w tym ćwiczeniu.

Do napisania i przetestowania tego kodu zostało użyte środowisko [Node.js](https://Nodejs.org) i menedżer pakietów NPM oraz programy [Sublime Text 3](https://www.sublimetext.com/3) i Windows PowerShell w systemie Windows 8.1.

Do uruchomienia tego przykładu jest potrzebna usługa Azure Search, do której możesz zarejestrować się w witrynie [Azure Portal](https://portal.azure.com). Aby uzyskać szczegółowe instrukcje, zobacz [Create an Azure Search service in the portal](search-create-service-portal.md) (Tworzenie usługi Azure Search w portalu).

## <a name="about-the-data"></a>Informacje o danych
Ta przykładowa aplikacja korzysta z danych agencji [United States Geological Services (USGS)](https://geonames.usgs.gov/domestic/download_data.htm) zawężonych do stanu Rhode Island w celu zmniejszenia rozmiaru zestawu danych. Użyjemy tych danych do utworzenia aplikacji wyszukiwania, która zwraca punkty orientacyjne, takie jak szpitale i szkoły, jak również formy geologiczne, takie jak strumienie, jeziora i szczyty.

W tej aplikacji program **DataIndexer** tworzy i ładuje indeks, używając konstrukcji [indeksatora](https://msdn.microsoft.com/library/azure/dn798918.aspx), a zawężony zestaw danych z agencji USGS jest pobierany z publicznej usługi Azure SQL Database. Poświadczenia oraz informacje o połączeniu ze źródłem danych w trybie online są zawarte w kodzie programu. Nie jest konieczna żadna dodatkowa konfiguracja.

> [!NOTE]
> Zastosowaliśmy filtr dla tego zestawu danych, aby nie przekroczyć limitu 10 000 dokumentów obowiązującego w warstwie cenowej Bezpłatna. Jeśli korzystasz z warstwy cenowej Standardowa, ten limit nie ma zastosowania. Aby uzyskać szczegółowe informacje o pojemności dla każdej warstwy cenowej, zobacz [Search service limits](search-limits-quotas-capacity.md) (Limity usługi wyszukiwania).
> 
> 

<a id="sub-2"></a>

## <a name="find-the-service-name-and-api-key-of-your-azure-search-service"></a>Znajdowanie nazwy usługi oraz klucza interfejsu API usługi Azure Search
Po utworzeniu usługi wróć do portalu, aby uzyskać adres URL lub klucz `api-key`. Połączenia z usługą wyszukiwania wymagają zarówno adresu URL, jak i klucza `api-key` do uwierzytelnienia wywołania.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Na pasku przechodzenia kliknij pozycję **Usługa wyszukiwania**, aby wyświetlić listę wszystkich usług Azure Search aprowizowanych dla subskrypcji.
3. Wybierz usługę, której chcesz użyć.
4. Na pulpicie nawigacyjnym usługi zobaczysz kafelki z istotnymi informacjami, takimi jak ikona klucza, służąca do uzyskiwania dostępu do kluczy administratora.
5. Skopiuj adres URL usługi, klucz administratora i klucz zapytania. Wszystkie te elementy trzeba będzie później dodać do pliku config.js.

## <a name="download-the-sample-files"></a>Pobieranie plików przykładowych
Pobierz przykład za pomocą jednej z następujących metod.

1. Przejdź do elementu [search-node-indexer-demo](https://github.com/Azure-Samples/search-node-indexer-demo).
2. Kliknij przycisk **Download ZIP** (Pobierz ZIP), zapisz plik zip, a następnie wyodrębnij wszystkie pliki w nim zawarte.

Wszystkie kolejne modyfikacje plików i instrukcje uruchamiania będą wykonywane względem plików w tym folderze.

## <a name="update-the-configjs-with-your-search-service-url-and-api-key"></a>Aktualizowanie pliku config.js przy użyciu adresu URL usługi wyszukiwania i klucza api-key
Korzystając ze skopiowanego wcześniej adresu URL i klucza api-key, podaj adres URL, klucz administratora i klucz zapytania w pliku konfiguracyjnym.

Klucze administratora przyznają pełną kontrolę nad operacjami usługi, w tym nad tworzeniem i usuwaniem indeksu oraz ładowaniem dokumentów. Z kolei klucze zapytania są przeznaczone dla operacji tylko do odczytu i są zwykle używane przez aplikacje klienckie, które nawiązują połączenie z usługą Azure Search.

W tym przykładzie uwzględniamy klucz zapytania, aby pomóc w zapamiętaniu najlepszego rozwiązania, jakim jest używanie klucza zapytania w aplikacjach klienckich.

Poniższy zrzut ekranu przedstawia plik **config.js** otwarty w edytorze tekstu, z zaznaczonymi odpowiednimi wpisami, które należy zaktualizować przy użyciu wartości odpowiednich dla usługi wyszukiwania.

![][5]

## <a name="host-a-runtime-environment-for-the-sample"></a>Hostowanie środowiska uruchomieniowego dla przykładu
Przykład wymaga serwera HTTP, który można zainstalować globalnie za pomocą menedżera pakietów npm.

Dla poniższych poleceń użyj okna programu PowerShell.

1. Przejdź do folderu, który zawiera plik **package.json**.
2. Wpisz polecenie `npm install`.
3. Wpisz polecenie `npm install -g http-server`.

## <a name="build-the-index-and-run-the-application"></a>Tworzenie indeksu i uruchamianie aplikacji
1. Wpisz polecenie `npm run indexDocuments`.
2. Wpisz polecenie `npm run build`.
3. Wpisz polecenie `npm run start_server`.
4. Za pomocą przeglądarki przejdź do strony `http://localhost:8080/index.html`

## <a name="search-on-usgs-data"></a>Przeszukiwanie danych agencji USGS
Zestaw danych agencji USGS zawiera rekordy, które odnoszą się do stanu Rhode Island. Jeśli klikniesz przycisk **Szukaj**, pozostawiając pole wyszukiwania puste, zostanie wyświetlonych 50 pierwszych wpisów, co jest ustawieniem domyślnym.

Wprowadzenie terminu wyszukiwania zaangażuje do pracy wyszukiwarkę. Spróbuj wprowadzić nazwę regionalną. „Roger Williams” był pierwszym gubernatorem stanu Rhode Island. Liczne parki, budynki i szkoły są nazwane jego imieniem.

![][9]

Możesz też wprowadzić jeden z poniższych terminów:

* Pawtucket
* Pembroke
* goose +cape

## <a name="next-steps"></a>Kolejne kroki
To jest pierwszy samouczek usługi Azure Search oparty na środowisku Node.js i zestawie danych agencji USGS. Wraz z upływem czasu będziemy rozszerzać ten samouczek, aby zademonstrować dodatkowe funkcje wyszukiwania, których mogą być przydatne w rozwiązaniach niestandardowych.

Jeśli masz już jakieś doświadczenie z usługą Azure Search, możesz użyć tego przykładu jako punktu wyjścia do wypróbowania sugestorów (uzupełnianie przy wpisywaniu lub autouzupełnianie zapytań), filtrów i nawigacji aspektowej. Możesz również ulepszyć stronę wyników wyszukiwania przez dodanie liczników i łączenie dokumentów w partie, aby użytkownicy mogli przechodzić do kolejnych stron wyników.

Dopiero zaczynasz korzystać z usługi Azure Search? Zalecamy wypróbować inne samouczki, aby poznać możliwości tworzenia w usłudze. Aby znaleźć więcej zasobów, odwiedź naszą [stronę dokumentacji](https://azure.microsoft.com/documentation/services/search/). 

<!--Image references-->
[1]: ./media/search-get-started-Nodejs/create-search-portal-1.PNG
[2]: ./media/search-get-started-Nodejs/create-search-portal-2.PNG
[3]: ./media/search-get-started-Nodejs/create-search-portal-3.PNG
[5]: ./media/search-get-started-Nodejs/AzSearch-Nodejs-configjs.png
[9]: ./media/search-get-started-Nodejs/rogerwilliamsschool.png
