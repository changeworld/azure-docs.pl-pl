---
title: Dowiedz się, jak zabezpieczyć dostęp do danych w usłudze Azure Cosmos DB
description: Więcej informacji na temat pojęć dotyczących kontroli dostępu w usłudze Azure Cosmos DB, m.in. klucze główne klucze tylko do odczytu, użytkowników i uprawnień.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.openlocfilehash: 6ccb9afb528edf67b29c781217ca1de97b81e017
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65969207"
---
# <a name="secure-access-to-data-in-azure-cosmos-db"></a>Zabezpieczanie dostępu do danych w usłudze Azure Cosmos DB

Ten artykuł zawiera omówienie zabezpieczania dostępu do danych przechowywanych w [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Usługa Azure Cosmos DB wykorzystuje dwa typy kluczy do uwierzytelniania użytkowników oraz zapewniają dostęp do swoich danych i zasobów. 

|Typ klucza|Zasoby|
|---|---|
|[Klucze główne](#master-keys) |Używany do zasobów administracyjnych: bazy danych, kont, baz danych, użytkowników i uprawnień|
|[Tokenów zasobów](#resource-tokens)|Używane dla zasobów aplikacji: kontenerów, dokumenty, załączniki, procedur składowanych, wyzwalaczy i funkcji zdefiniowanych przez użytkownika|

<a id="master-keys"></a>

## <a name="master-keys"></a>Klucze główne 

Klucze główne zapewniają dostęp do wszystkich zasobów administracyjnych dla konta bazy danych. Klucze główne:  
- Zapewniają dostęp do konta, bazy danych, użytkowników i uprawnienia. 
- Nie może służyć do zapewnienia szczegółowej dostęp do kontenerów i dokumentów.
- Są tworzone podczas tworzenia konta.
- Można ponownie wygenerować w dowolnym momencie.

Każde konto obejmuje dwa klucze główne: klucz podstawowy i klucz pomocniczy. Celem dwa klucze jest tak, aby wygenerować lub przywracanie kluczy, zapewniając ciągły dostęp do danych i konta. 

Oprócz dwa klucze główne konto usługi Cosmos DB istnieją dwa klucze tylko do odczytu. Klucze tylko do odczytu Zezwalaj tylko na operacje odczytu dla konta. Klucze tylko do odczytu nie zapewnia dostępu do odczytu zasobów uprawnień.

Podstawowa, pomocnicza, tylko do odczytu, a klucze główne odczytu i zapisu mogą być pobierane i ponownie wygenerowane za pomocą witryny Azure portal. Aby uzyskać instrukcje, zobacz [wyświetlanie, kopiowanie i ponowne generowanie kluczy dostępu](manage-with-cli.md#regenerate-account-key).

![Kontrola dostępu (IAM) w witrynie Azure portal — prezentacja zabezpieczeń bazy danych NoSQL](./media/secure-access-to-data/nosql-database-security-master-key-portal.png)

Proces rotacji Twój klucz główny jest proste. Przejdź do portalu Azure w celu pobrania klucza pomocniczego, a następnie zastąp klucz podstawowy klucz pomocniczy w aplikacji, a następnie wymienić główny klucz podstawowy w witrynie Azure portal.

![Obrót klucz główny w witrynie Azure portal — prezentacja zabezpieczeń bazy danych NoSQL](./media/secure-access-to-data/nosql-database-security-master-key-rotate-workflow.png)

### <a name="code-sample-to-use-a-master-key"></a>Przykładowy kod, aby użyć klucza głównego

Poniższy przykład kodu ilustruje sposób używania punkt końcowy konta usługi Cosmos DB i klucz główny można utworzyć wystąpienia DocumentClient i utworzyć bazę danych. 

```csharp
//Read the Azure Cosmos DB endpointUrl and authorization keys from config.
//These values are available from the Azure portal on the Azure Cosmos DB account blade under "Keys".
//NB > Keep these values in a safe and secure location. Together they provide Administrative access to your DocDB account.

private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
private static readonly SecureString authorizationKey = ToSecureString(ConfigurationManager.AppSettings["AuthorizationKey"]);

client = new DocumentClient(new Uri(endpointUrl), authorizationKey);

// Create Database
Database database = await client.CreateDatabaseAsync(
    new Database
    {
        Id = databaseName
    });
```

<a id="resource-tokens"></a>

## <a name="resource-tokens"></a>Tokenów zasobów

Tokeny zasobów zapewniają dostęp do zasobów aplikacji w bazie danych. Tokeny zasobów:
- Zapewniają dostęp do określonych kontenerów, klucze partycji, dokumentów, załączniki, procedur składowanych, wyzwalaczy i funkcji zdefiniowanych przez użytkownika.
- Są tworzone, gdy [użytkownika](#users) otrzymuje [uprawnienia](#permissions) do określonego zasobu.
- Podczas zasobów uprawnienie jest reagować na przez wywołanie POST, GET lub PUT zostaną odtworzone.
- Używają specjalnie skonstruowane dla użytkowników, zasobów i uprawnienia tokenu zasobów wyznaczania wartości skrótu.
- Jest powiązany z okresem ważności można dostosowywać. Prawidłowy przedział czasu domyślny to jedna godzina. Okres istnienia tokenu, jednak może być jawnie określone, maksymalnie pięć godzin.
- Podaj bezpieczne alternatywa ujawnianiem klucza głównego. 
- Włącz klientów do odczytu, zapisu i usuwania zasobów w ramach konta usługi Cosmos DB, zgodnie z uprawnieniami, które zostały przyznane.

Aby użyć tokenu zasobu (tworzenia użytkowników usługi Cosmos DB i uprawnienia) kiedy chcesz zapewnić dostęp do zasobów w ramach konta usługi Cosmos DB do klienta, który nie jest zaufany za pomocą klucza głównego.  

Tokenów zasobów usługi cosmos DB zapewniają bezpieczne alternatywę umożliwiająca klientom odczytu, zapisu i usuwania zasobów konta usługi Cosmos DB, zgodnie z uprawnieniami, które zostały przyznane i bez konieczności korzystania z wzorca lub odczytu tylko klucza.

Poniżej przedstawiono wzorzec projektowania typowe zgodnie z którą tokenów zasobów mogą być wymagane, wygenerowane i dostarczeniem do klientów:

1. Skonfigurowano usługi w warstwie pośredniej do obsługi mobilnej aplikacji do udostępniania zdjęć użytkownika. 
2. Usługi w warstwie pośredniej ma klucz główny konta usługi Cosmos DB.
3. Aplikacja zdjęcia jest zainstalowana na urządzeniach przenośnych użytkowników końcowych. 
4. Podczas logowania aplikacji photo ustanawia tożsamości użytkowników z usługą w warstwie pośredniej. Ten mechanizm ustanowienie tożsamości jest wyłącznie do aplikacji.
5. Po ustanowieniu tożsamości usługi w warstwie pośredniej żąda uprawnień na podstawie tożsamości.
6. Usługi w warstwie pośredniej wysyła token zasobu z powrotem do aplikacji telefonu.
7. Aplikację można w dalszym ciągu używać tokenu zasobu bezpośrednio dostępu do zasobów usługi Cosmos DB przy użyciu na uprawnienia zdefiniowane przez token zasobu i interwał dozwolone przez tokenu zasobów. 
8. Po wygaśnięciu ważności tokenu zasobów kolejne żądania otrzymują 401 nieautoryzowane wyjątek.  W tym momencie aplikację ponownie ustala tożsamość i żąda nowego tokenu zasobów.

    ![Tokeny zasobów usługi Azure Cosmos DB przepływu pracy](./media/secure-access-to-data/resourcekeyworkflow.png)

Generowania tokenów zasobów i zarządzanie odbywa się przez natywnych bibliotek klienta Cosmos DB; Jednak korzystanie z interfejsu REST należy tworzyć nagłówki żądania i uwierzytelniania. Aby uzyskać więcej informacji na temat tworzenia nagłówków uwierzytelniania dla REST, zobacz [kontrolę dostępu do zasobów usługi Cosmos DB](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources) lub [kod źródłowy dla naszych zestawów SDK](https://github.com/Azure/azure-documentdb-node/blob/master/source/lib/auth.js).

Na przykład usługi warstwy środkowej, używany do generowania lub brokera tokenów zasobów zobacz [aplikacji ResourceTokenBroker](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker/Controllers).

<a id="users"></a>

## <a name="users"></a>Użytkownicy
Cosmos DB użytkowników są skojarzone z bazą danych Cosmos DB.  Każda baza danych może zawierać zero lub więcej użytkowników usługi Cosmos DB.  Poniższy przykład kodu pokazuje, jak utworzyć zasób użytkownika usługi Cosmos DB.

```csharp
//Create a user.
User docUser = new User
{
    Id = "mobileuser"
};

docUser = await client.CreateUserAsync(UriFactory.CreateDatabaseUri("db"), docUser);
```

> [!NOTE]
> Każdy użytkownik usługi Cosmos DB ma właściwość PermissionsLink, który może służyć do pobierania listy [uprawnienia](#permissions) skojarzonych z użytkownikiem.
> 
> 

<a id="permissions"></a>

## <a name="permissions"></a>Uprawnienia
Zasób uprawnienia usługi Cosmos DB jest skojarzone z użytkownikami usługi Cosmos DB.  Każdy użytkownik może zawierać zero lub więcej uprawnień usługi Cosmos DB.  Zasób uprawnienia zapewnia dostęp do token zabezpieczający, który użytkownik musi podczas próby uzyskania dostępu do zasobu z określonej aplikacji.
Istnieją dwa poziomy dostępu, które mogą być udostępniane przez zasób uprawnień:

* Wszystkie: Użytkownik ma pełne uprawnienia w zasobie.
* Odczyt: Użytkownik może jedynie odczytywać zawartość zasobu, ale nie można wykonać zapisu, aktualizacji lub usuń operacje na zasobie.

> [!NOTE]
> Aby można było uruchomić usługi Cosmos DB procedury składowane użytkownik musi mieć uprawnienie All w kontenerze, w którym będzie uruchamiany procedury składowanej.
> 
> 

### <a name="code-sample-to-create-permission"></a>Przykładowy kod, aby utworzyć uprawnienie

Poniższy przykład kodu pokazuje, jak utworzyć zasób uprawnień, token zasobu zasobu uprawnienia odczytu i skojarz uprawnień za pomocą [użytkownika](#users) utworzonego powyżej.

```csharp
// Create a permission.
Permission docPermission = new Permission
{
    PermissionMode = PermissionMode.Read,
    ResourceLink = UriFactory.CreateDocumentCollectionUri("db", "collection"),
    Id = "readperm"
};
  
docPermission = await client.CreatePermissionAsync(UriFactory.CreateUserUri("db", "user"), docPermission);
Console.WriteLine(docPermission.Id + " has token of: " + docPermission.Token);
```

Jeśli podano klucza partycji dla kolekcji w uprawnienia dla kolekcji, dokumentów i załączników zasobów należy również uwzględnić ResourcePartitionKey oprócz ResourceLink.

### <a name="code-sample-to-read-permissions-for-user"></a>Przykładowy kod, aby uprawnienia do odczytu dla użytkownika

Aby łatwo uzyskać uprawnienie all zasoby skojarzone z określonym użytkownikiem, Cosmos DB udostępnia uprawnienia kanału informacyjnego dla każdego obiektu użytkownika.  Poniższy fragment kodu przedstawia sposób pobierania uprawnień skojarzonych z użytkownikiem, utworzony w poprzednim przykładzie, utworzenia listy uprawnień i utworzenia wystąpienia nowego wystąpienia klasy DocumentClient w imieniu użytkownika.

```csharp
//Read a permission feed.
FeedResponse<Permission> permFeed = await client.ReadPermissionFeedAsync(
  UriFactory.CreateUserUri("db", "myUser"));
 List<Permission> permList = new List<Permission>();

foreach (Permission perm in permFeed)
{
    permList.Add(perm);
}

DocumentClient userClient = new DocumentClient(new Uri(endpointUrl), permList);
```

## <a name="add-users-and-assign-roles"></a>Dodawanie użytkowników i przypisywania ról

Aby dodać dostęp czytelnika konta usługi Azure Cosmos DB z Twoim kontem użytkownika, należy mieć właścicielem subskrypcji, wykonaj następujące kroki w witrynie Azure portal.

1. Otwórz witrynę Azure portal, a następnie wybierz swoje konto usługi Azure Cosmos DB.
2. Kliknij przycisk **kontrola dostępu (IAM)** kartę, a następnie kliknij przycisk **+ Dodaj przypisanie roli**.
3. W **Dodaj przypisanie roli** okienku w obszarze **roli** wybierz opcję **Rola czytelnika konta bazy danych Cosmos**.
4. W **przypisywanie dostępu do usługi box**, wybierz opcję **użytkownika usługi Azure AD, grupa lub aplikacja**.
5. Wybierz użytkownika, grupę lub aplikację w katalogu, do którego chcesz udzielić dostępu.  Możesz przeszukiwać katalog według nazwy wyświetlanej, adresu e-mail lub identyfikatorów obiektów.
    Wybranego użytkownika, grupy lub aplikacji zostanie wyświetlony na liście wybranych członków.
6. Kliknij pozycję **Zapisz**.

Jednostka może teraz odczytywać zasoby usługi Azure Cosmos DB.

## <a name="delete-or-export-user-data"></a>Usuń lub eksportowanie danych użytkownika
Usługa Azure Cosmos DB umożliwia wyszukiwanie, wybierz, modyfikować i usuwać wszystkie dane osobowe znajdujące się w bazie danych lub kolekcji. Usługa Azure Cosmos DB udostępnia interfejsy API, aby znaleźć i usunąć dane osobowe, jednak, jest odpowiedzialny za przy użyciu interfejsów API i zdefiniuj logikę wymaganą do usunięcia danych osobowych. Każdego wielomodelowa interfejsu API (SQL, MongoDB, Gremlin, Cassandra, tabeli) zapewnia inny pakiet zestawów SDK, które zawierają metody, aby wyszukać i usunąć dane osobowe. Można również włączyć [czas wygaśnięcia (TTL)](time-to-live.md) funkcję, aby usunąć dane automatycznie po upływie określonego czasu, bez ponoszenia żadnych dodatkowych kosztów.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Kolejne kroki
* Aby dowiedzieć się więcej na temat zabezpieczeń bazy danych Cosmos DB, zobacz [Cosmos DB: Bazy danych zabezpieczeń](database-security.md).
* Aby dowiedzieć się, jak skonstruować tokenach autoryzacji usługi Azure Cosmos DB, zobacz [kontrolę dostępu do zasobów usługi Azure Cosmos DB](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources).
