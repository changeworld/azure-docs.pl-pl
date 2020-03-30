---
title: Dowiedz się, jak zabezpieczyć dostęp do danych w usłudze Azure Cosmos DB
description: Dowiedz się więcej o pojęciach kontroli dostępu w usłudze Azure Cosmos DB, w tym kluczy głównych, kluczy tylko do odczytu, użytkowników i uprawnień.
author: thomasweiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 448b14168e85e75b7ed19e189600186ce11c2902
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251819"
---
# <a name="secure-access-to-data-in-azure-cosmos-db"></a>Zabezpieczanie dostępu do danych w usłudze Azure Cosmos DB

Ten artykuł zawiera omówienie zabezpieczania dostępu do danych przechowywanych w [usłudze Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Usługa Azure Cosmos DB używa dwóch typów kluczy do uwierzytelniania użytkowników i zapewnienia dostępu do swoich danych i zasobów. 

|Typ klucza|Resources|
|---|---|
|[Klucze główne](#master-keys) |Używane dla zasobów administracyjnych: kont baz danych, baz danych, użytkowników i uprawnień|
|[Tokeny zasobów](#resource-tokens)|Używane do zasobów aplikacji: kontenerów, dokumentów, załączników, procedur przechowywanych, wyzwalaczy i plików UDF|

<a id="master-keys"></a>

## <a name="master-keys"></a>Klucze główne

Klucze główne zapewniają dostęp do wszystkich zasobów administracyjnych dla konta bazy danych. Klucze główne:

- Zapewnij dostęp do kont, baz danych, użytkowników i uprawnień. 
- Nie można użyć do zapewnienia szczegółowego dostępu do kontenerów i dokumentów.
- Są tworzone podczas tworzenia konta.
- Może być regenerowany w dowolnym momencie.

Każde konto składa się z dwóch kluczy głównych: klucza podstawowego i klucza pomocniczego. Celem dwóch kluczy jest możliwość ponownego generowania lub kluczy rolkowych, zapewniając ciągły dostęp do konta i danych.

Oprócz dwóch kluczy głównych dla konta usługi Cosmos DB istnieją dwa klucze tylko do odczytu. Te klucze tylko do odczytu zezwalają tylko na operacje odczytu na koncie. Klucze tylko do odczytu nie zapewniają dostępu do zasobów uprawnień do odczytu.

Podstawowe, pomocnicze, tylko do odczytu i odczytu i zapisu klucze główne mogą być pobierane i generowane ponownie przy użyciu witryny Azure portal. Aby uzyskać [instrukcje, zobacz Wyświetlanie, kopiowanie i ponowne generowanie kluczy dostępu](manage-with-cli.md#regenerate-account-key).

![Kontrola dostępu (IAM) w witrynie Azure portal — demonstrowanie zabezpieczeń bazy danych NoSQL](./media/secure-access-to-data/nosql-database-security-master-key-portal.png)

Proces obracania klucza głównego jest prosty. Przejdź do witryny Azure Portal, aby pobrać klucz pomocniczy, a następnie zastąp klucz podstawowy kluczem pomocniczym w aplikacji, a następnie obróć klucz podstawowy w witrynie Azure portal.

![Rotacja kluczy wzorca w witrynie Azure portal — demonstrowanie zabezpieczeń bazy danych NoSQL](./media/secure-access-to-data/nosql-database-security-master-key-rotate-workflow.png)

### <a name="code-sample-to-use-a-master-key"></a>Przykładowy kod do używania klucza głównego

Poniższy przykładowy kod ilustruje sposób używania punktu końcowego konta usługi Cosmos DB i klucza głównego do tworzenia wystąpienia dokumentu DocumentClient i tworzenia bazy danych.

```csharp
//Read the Azure Cosmos DB endpointUrl and authorization keys from config.
//These values are available from the Azure portal on the Azure Cosmos DB account blade under "Keys".
//Keep these values in a safe and secure location. Together they provide Administrative access to your Azure Cosmos DB account.

private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
private static readonly string authorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];

CosmosClient client = new CosmosClient(endpointUrl, authorizationKey);
```

## <a name="resource-tokens"></a>Tokeny zasobów<a id="resource-tokens"></a>

Tokeny zasobów zapewniają dostęp do zasobów aplikacji w bazie danych. Tokeny zasobów:

- Zapewnij dostęp do określonych kontenerów, kluczy partycji, dokumentów, załączników, procedur przechowywanych, wyzwalaczy i plików UDF.
- Są tworzone, gdy [użytkownikowi](#users) przyznano [uprawnienia](#permissions) do określonego zasobu.
- Są odtworzone, gdy zasób uprawnień jest uruchamiany przez wywołanie POST, GET lub PUT.
- Użyj tokenu zasobu mieszania specjalnie skonstruowanego dla użytkownika, zasobu i uprawnień.
- Czy czas jest związany z konfigurowalnym okresem ważności. Domyślny prawidłowy przedział czasu wynosi jedną godzinę. Okres istnienia tokenu, jednak mogą być jawnie określone, maksymalnie do pięciu godzin.
- Zapewnij bezpieczną alternatywę dla rozdawania klucza głównego.
- Włącz klientom do odczytu, zapisu i usuwania zasobów na koncie usługi Cosmos DB zgodnie z uprawnieniami, które zostały przyznane.

Token zasobu (przez utworzenie użytkowników i uprawnień usługi Cosmos DB), jeśli chcesz zapewnić dostęp do zasobów na koncie usługi Cosmos DB klientowi, któremu nie można ufać przy użyciu klucza głównego.  

Tokeny zasobów usługi Cosmos DB zapewniają bezpieczną alternatywę, która umożliwia klientom odczytywanie, zapisywanie i usuwanie zasobów na koncie usługi Cosmos DB zgodnie z przyznanymi uprawnieniami i bez konieczności posiadania klucza głównego lub tylko do odczytu.

Oto typowy wzorzec projektu, zgodnie z którym tokeny zasobów mogą być wymagane, generowane i dostarczane do klientów:

1. Usługa średniej warstwy jest skonfigurowana do obsługi aplikacji mobilnej do udostępniania zdjęć użytkowników.
2. Usługa klasy środkowej posiada klucz główny konta usługi Cosmos DB.
3. Aplikacja fotograficzna jest zainstalowana na urządzeniach mobilnych użytkowników końcowych.
4. Po zalogowaniu aplikacja fotograficzna ustala tożsamość użytkownika za pomocą usługi średniej warstwy. Ten mechanizm ustanawiania tożsamości zależy wyłącznie od wniosku.
5. Po ustaleniu tożsamości usługa klasy środkowej żąda uprawnień na podstawie tożsamości.
6. Usługa warstwy środkowej wysyła token zasobu z powrotem do aplikacji telefonu.
7. Aplikacja telefonu może nadal używać tokenu zasobu do bezpośredniego dostępu do zasobów usługi Cosmos DB z uprawnieniami zdefiniowanymi przez token zasobu i interwałem dozwolonym przez token zasobu.
8. Po wygaśnięciu tokenu zasobu kolejne żądania otrzymują 401 nieautoryzowany wyjątek.  W tym momencie aplikacja telefonu ponownie ustanawia tożsamość i żąda nowego tokenu zasobu.

    ![Przepływ pracy tokenów zasobów usługi Azure Cosmos DB](./media/secure-access-to-data/resourcekeyworkflow.png)

Generowanie tokenu zasobów i zarządzanie jest obsługiwane przez natywne biblioteki klientów usługi Cosmos DB; Jednak jeśli używasz REST należy skonstruować nagłówki żądania/uwierzytelniania. Aby uzyskać więcej informacji na temat tworzenia nagłówków uwierzytelniania dla REST, zobacz [Kontrola dostępu do zasobów usługi Cosmos DB](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources) lub kod źródłowy naszego [sdk .NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos/src/AuthorizationHelper.cs) lub [Node.js SDK](https://github.com/Azure/azure-cosmos-js/blob/master/src/auth.ts).

Na przykład usługi warstwy środkowej używanej do generowania lub brokera tokenów zasobów, zobacz [aplikację ResourceTokenBroker](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker/Controllers).

## <a name="users"></a>Użytkowników<a id="users"></a>

Użytkownicy usługi Azure Cosmos DB są skojarzone z bazy danych usługi Cosmos.  Każda baza danych może zawierać zero lub więcej użytkowników usługi Cosmos DB. W poniższym przykładzie kodu pokazano, jak utworzyć użytkownika usługi Cosmos DB przy użyciu [usługi Azure Cosmos DB .NET SDK w wersji 3](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/UserManagement).

```csharp
//Create a user.
Database database = benchmark.client.GetDatabase("SalesDatabase");

User user = await database.CreateUserAsync("User 1");
```

> [!NOTE]
> Każdy użytkownik usługi Cosmos DB ma metodę ReadAsync(), która może służyć do pobierania listy [uprawnień skojarzonych](#permissions) z użytkownikiem.

## <a name="permissions"></a>Uprawnienia<a id="permissions"></a>

Zasób uprawnień jest skojarzony z użytkownikiem i przypisany na poziomie kontenera, a także na poziomie klucza partycji. Każdy użytkownik może zawierać zero lub więcej uprawnień. Zasób uprawnień zapewnia dostęp do tokenu zabezpieczającego, który użytkownik potrzebuje podczas próby uzyskania dostępu do określonego kontenera lub danych w określonym kluczu partycji. Istnieją dwa dostępne poziomy dostępu, które mogą być dostarczane przez zasób uprawnień:

- Wszystkie: Użytkownik ma pełne uprawnienia do zasobu.
- Odczyt: Użytkownik może odczytywać tylko zawartość zasobu, ale nie może wykonywać operacji zapisu, aktualizacji lub usuwania zasobu.

> [!NOTE]
> Aby uruchomić procedury składowane użytkownik musi mieć wszystkie uprawnienia do kontenera, w którym zostanie uruchomiony procedura składowana.

### <a name="code-sample-to-create-permission"></a>Przykładowy kod do tworzenia uprawnień

Poniższy przykład kodu pokazuje, jak utworzyć zasób uprawnień, odczytać token zasobu zasobu uprawnień i skojarzyć uprawnienia z [użytkownikiem](#users) utworzonym powyżej.

```csharp
// Create a permission on a container and specific partition key value
Container container = client.GetContainer("SalesDatabase", "OrdersContainer");
user.CreatePermissionAsync(
    new PermissionProperties(
        id: "permissionUser1Orders",
        permissionMode: PermissionMode.All,
        container: benchmark.container,
        resourcePartitionKey: new PartitionKey("012345")));
```

### <a name="code-sample-to-read-permission-for-user"></a>Przykładowy kod do odczytu uprawnień dla użytkownika

Poniższy fragment kodu pokazuje, jak pobrać uprawnienia skojarzone z użytkownikiem utworzonym powyżej i utworzyć wystąpienie nowego cosmosclient w imieniu użytkownika, o zakresie do jednego klucza partycji.

```csharp
//Read a permission, create user client session.
PermissionProperties permissionProperties = await user.GetPermission("permissionUser1Orders")

CosmosClient client = new CosmosClient(accountEndpoint: "MyEndpoint", authKeyOrResourceToken: permissionProperties.Token);
```

## <a name="add-users-and-assign-roles"></a>Dodawanie użytkowników i przypisywanie ról

Aby dodać dostęp czytnika kont usługi Azure Cosmos DB do konta użytkownika, należy mieć właściciela subskrypcji wykonać następujące kroki w witrynie Azure portal.

1. Otwórz witrynę Azure portal i wybierz swoje konto usługi Azure Cosmos DB.
2. Kliknij kartę **Kontrola dostępu (IAM),** a następnie kliknij pozycję **+ Dodaj przypisanie roli**.
3. W okienku **Dodawanie przypisania roli** w polu **Rola** wybierz pozycję **Rola czytnika kont usługi Cosmos DB**.
4. W **polu Przypisz dostęp do wybierz**pozycję **Użytkownik, grupa lub aplikacja usługi Azure AD**.
5. Wybierz użytkownika, grupę lub aplikację w katalogu, do którego chcesz udzielić dostępu.  Katalog można przeszukiwać według nazwy wyświetlanej, adresu e-mail lub identyfikatorów obiektów.
    Wybrany użytkownik, grupa lub aplikacja pojawi się na liście wybranych członków.
6. Kliknij przycisk **Zapisz**.

Jednostka może teraz odczytywać zasoby usługi Azure Cosmos DB.

## <a name="delete-or-export-user-data"></a>Usuwanie lub eksportowanie danych użytkownika

Usługa Azure Cosmos DB umożliwia wyszukiwanie, wybieranie, modyfikowanie i usuwanie wszelkich danych osobowych znajdujących się w bazie danych lub kolekcjach. Usługa Azure Cosmos DB udostępnia interfejsy API do znajdowania i usuwania danych osobowych, jednak jest odpowiedzialny za korzystanie z interfejsów API i definiowanie logiki wymaganej do usunięcia danych osobowych. Każdy wielomodelowy interfejs API (SQL, MongoDB, Gremlin, Cassandra, Table) udostępnia zestawy SDK w innym języku, które zawierają metody wyszukiwania i usuwania danych osobowych. Można również włączyć funkcję [czasu wygaśnięcia (TTL),](time-to-live.md) aby automatycznie usuwać dane po upływie określonego czasu, bez ponoszenia dodatkowych kosztów.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o zabezpieczeniach bazy danych usługi Cosmos, zobacz [Zabezpieczenia bazy danych usługi Cosmos DB](database-security.md).
- Aby dowiedzieć się, jak konstruować tokeny autoryzacji usługi Azure Cosmos DB, zobacz [Kontrola dostępu w zasobach usługi Azure Cosmos DB.](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources)
- Przykłady zarządzania użytkownikami z użytkownikami i uprawnieniami, [przykłady zarządzania użytkownikami .NET SDK w wersji 3](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/UserManagement/UserManagementProgram.cs)
