---
title: 'Azure Cosmos DB: Budowanie aplikacji sieci Web za pomocą oprogramowania Xamarin z uwierzytelnianiem serwisu Facebook | Microsoft Docs'
description: Przykładowy kod programu .NET, którego można używać do nawiązywania połączeń z usługą Azure Cosmos DB i wykonywania w niej zapytań
services: cosmos-db
documentationcenter: ''
author: mimig1
manager: jhubbard
editor: ''
ms.assetid: ''
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 11/29/2017
ms.author: mimig
ms.openlocfilehash: 5074034b18bdf842c34b1208e6cc6312d7a3e6b2
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2018
---
# <a name="azure-cosmos-db-build-a-web-app-with-net-xamarin-and-facebook-authentication"></a>Azure Cosmos DB: Budowanie aplikacji sieci Web za pomocą oprogramowania .NET, Xamarin i uwierzytelniania serwisu Facebook

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

Azure Cosmos DB to rozproszona globalnie wielomodelowa usługa bazy danych firmy Microsoft. Dzięki wykorzystaniu dystrybucji globalnej i możliwości skalowania poziomego opartego na usłudze Azure Cosmos DB, możesz szybko tworzyć i za pomocą zapytań badać bazy danych dokumentów, par klucz/wartość oraz grafów.

> [!NOTE]
> Przykładowy kod dla całej kanonicznej przykładowej aplikacji platformy Xamarin z wieloma ofertami platformy Azure, w tym usługą CosmosDB, można znaleźć w witrynie GitHub [tutaj](https://github.com/xamarinhq/app-geocontacts). Ta aplikacja demonstruje wyświetlanie geograficznie rozproszonych kontaktów i umożliwianie tym kontaktom zaktualizowanie ich lokalizacji.

Ten przewodnik Szybki start przedstawia sposób tworzenia konta usługi Azure Cosmos DB, bazy danych dokumentów i kolekcji przy użyciu witryny Azure Portal. Następnie utworzysz i wdrożysz aplikację internetową z listą zadań do wykonania bazującą na [interfejsie API SQL platformy .NET](sql-api-sdk-dotnet.md), oprogramowaniu [Xamarin](https://www.xamarin.com/) oraz aparacie uwierzytelniania usługi Azure Cosmos DB. Aplikacja internetowa z listą zadań do wykonania implementuje wzorzec danych dla poszczególnych użytkowników, który umożliwia użytkownikom zalogowanie się przy użyciu uwierzytelniania serwisu Facebook i zarządzanie własnymi zadaniami do wykonania.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz jeszcze zainstalowanego programu Visual Studio 2017, możesz pobrać program [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) i używać go **bezpłatnie**. Podczas instalacji programu Visual Studio upewnij się, że jest włączona opcja **Programowanie na platformie Azure**.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="create-a-database-account"></a>Tworzenie konta bazy danych

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-collection"></a>Dodawanie kolekcji

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Teraz sklonujemy aplikację interfejsu API SQL z serwisu GitHub, ustawimy parametry połączenia i uruchomimy ją. Zobaczysz, jak łatwo jest pracować programowo z danymi. 

1. Otwórz okno terminalu usługi Git, na przykład git bash, i za pomocą polecenia `cd` przejdź do katalogu roboczego.

2. Uruchom następujące polecenie w celu sklonowania przykładowego repozytorium. 

    ```bash
    git clone https://github.com/Azure/azure-documentdb-dotnet.git
    ```

3. Następnie w programie Visual Studio otwórz plik DocumentDBTodo.sln z folderu samples/xamarin/UserItems/xamarin.forms.

## <a name="review-the-code"></a>Przeglądanie kodu

Kod w folderze Xamarin zawiera następujące elementy:

* Aplikacja platformy Xamarin. Ta aplikacja przechowuje zadania do wykonania użytkownika w partycjonowanej kolekcji o nazwie UserItems.
* Interfejs API brokera tokenu zasobów. Prosty interfejs API ASP.NET sieci Web brokera tokenów zasobów usługi Azure Cosmos DB dla zalogowanych użytkowników aplikacji. Tokeny zasobów to krótkotrwałe tokeny dostępu zapewniające aplikacji dostęp do danych zalogowanego użytkownika.

Proces uwierzytelniania i przepływu danych zilustrowano na poniższym diagramie.

* Kolekcja UserItems została utworzona za pomocą klucza partycji „/userid”. Określenie klucza partycji dla kolekcji umożliwia usłudze Azure Cosmos DB skalowanie w nieskończoność w miarę zwiększania się liczby użytkowników i zadań.
* Aplikacja Xamarin umożliwia użytkownikom logowanie się przy użyciu poświadczeń serwisu Facebook.
* Aplikacja Xamarin używa tokenu dostępu serwisu Facebook do uwierzytelniania za pomocą interfejsu API ResourceTokenBroker.
* Interfejs API brokera tokenu zasobów uwierzytelnia żądanie przy użyciu funkcji uwierzytelniania usługi App Service i żąda tokenu zasobów usługi Azure Cosmos DB z dostępem do odczytu i zapisu do wszystkich dokumentów korzystających z klucza partycji uwierzytelnionego użytkownika.
* Broker tokenu zasobów zwraca token zasobów dla aplikacji klienckiej.
* Aplikacja uzyskuje dostęp do zadań do wykonania użytkownika przy użyciu tokenu zasobów.

![Aplikacja z listą zadań do wykonania z przykładowymi danymi](./media/create-sql-api-xamarin-dotnet/tokenbroker.png)

## <a name="update-your-connection-string"></a>Aktualizowanie parametrów połączenia

Teraz wróć do witryny Azure Portal, aby uzyskać informacje o parametrach połączenia i skopiować je do aplikacji.

1. W witrynie [Azure Portal](http://portal.azure.com/), korzystając ze swojego konta usługi Azure Cosmos DB, kliknij na lewym panelu nawigacyjnym pozycję **Klucze**, a następnie pozycję **Klucze odczytu i zapisu**. W następnym kroku, korzystając z przycisków kopiowania dostępnych po prawej stronie ekranu, skopiujesz identyfikator URI i klucz podstawowy do pliku Web.config.

    ![Wyświetlanie i kopiowanie klucza dostępu w witrynie Azure Portal, blok Klucze](./media/create-sql-api-xamarin-dotnet/keys.png)

2. W programie Visual Studio 2017 otwórz plik Web.config z folderu azure-documentdb-dotnet/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker. 

3. Skopiuj wartość identyfikatora URI z portalu (przy użyciu przycisku kopiowania) i przypisz ją do klucza accountUrl w pliku Web.config. 

    `<add key="accountUrl" value="{Azure Cosmos DB account URL}"/>`

4. Następnie skopiuj wartość klucza podstawowego z portalu i przypisz ją do klucza accountKey w pliku Web.config.

    `<add key="accountKey" value="{Azure Cosmos DB secret}"/>`

Aplikacja została zaktualizowana i zawiera teraz wszystkie informacje potrzebne do nawiązania komunikacji z usługą Azure Cosmos DB. 

## <a name="build-and-deploy-the-web-app"></a>Budowanie i wdrażanie aplikacji sieci Web

1. W witrynie Azure Portal utwórz witrynę sieci Web usługi App Service, która będzie hostowała interfejs API brokera tokenu zasobów.
2. W witrynie Azure Portal otwórz blok Ustawienia aplikacji witryny sieci Web interfejsu API brokera tokenu zasobów. Wypełnij następujące ustawienia aplikacji:

    * accountUrl — adres URL konta usługi Azure Cosmos DB z karty Klucze konta usługi Azure Cosmos DB.
    * accountKey — klucz główny konta usługi Azure Cosmos DB z karty Klucze konta usługi Azure Cosmos DB.
    * Identyfikatory databaseId i collectionId utworzonej bazy danych oraz kolekcji.

3. Opublikuj rozwiązanie ResourceTokenBroker w utworzonej witrynie sieci Web.

4. Otwórz projekt Xamarin i przejdź do pliku TodoItemManager.cs. Podaj wartości dla właściwości accountURL, collectionId i databaseId, a także dla właściwości resourceTokenBrokerURL będącej podstawowym adresem URL HTTPS witryny sieci Web brokera tokenu zasobów.

5. Ukończ samouczek [Jak skonfigurować aplikację App Service, aby używała logowania do usługi Facebook](../app-service/app-service-mobile-how-to-configure-facebook-authentication.md), aby skonfigurować uwierzytelnianie serwisu Facebook i witrynę internetową ResourceTokenBroker.

    Uruchom aplikację platformy Xamarin.

## <a name="review-slas-in-the-azure-portal"></a>Przeglądanie umów SLA w witrynie Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie zamierzasz w przyszłości korzystać z tej aplikacji, wykonaj następujące czynności, aby usunąć wszystkie zasoby utworzone w witrynie Azure Portal w ramach tego przewodnika Szybki start:

1. W menu znajdującym się po lewej stronie w witrynie Azure Portal kliknij pozycję **Grupy zasobów**, a następnie kliknij nazwę utworzonego zasobu.
2. Na stronie grupy zasobów kliknij pozycję **Usuń**, wpisz w polu tekstowym nazwę zasobu do usunięcia, a następnie kliknij pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wyjaśniono sposób tworzenia konta usługi Azure Cosmos DB, tworzenia kolekcji za pomocą Eksploratora danych i budowania oraz wdrażania aplikacji platformy Xamarin. Teraz możesz zaimportować dodatkowe dane do swojego konta usługi Cosmos DB.

> [!div class="nextstepaction"]
> [Importowanie danych do usługi Azure Cosmos DB](import-data.md)
