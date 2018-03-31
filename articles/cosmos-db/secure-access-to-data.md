---
title: Dowiedz się, jak bezpieczny dostęp do danych w usłudze Azure DB rozwiązania Cosmos | Dokumentacja firmy Microsoft
description: Więcej informacji na temat pojęć dotyczących kontroli dostępu w usłudze Azure DB rozwiązania Cosmos, włączając klucz główny, tylko do odczytu klucze, użytkowników i uprawnienia.
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: ''
ms.assetid: 8641225d-e839-4ba6-a6fd-d6314ae3a51c
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2017
ms.author: mimig
ms.openlocfilehash: 771c4a539d240a6bfdc9770adc7cfada01571939
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="securing-access-to-azure-cosmos-db-data"></a>Zabezpieczanie dostępu do danych bazy danych Azure rozwiązania Cosmos
Ten artykuł zawiera omówienie zabezpieczanie dostępu do danych przechowywanych w [bazy danych programu Microsoft Azure rozwiązania Cosmos](https://azure.microsoft.com/services/cosmos-db/).

Azure DB rozwiązania Cosmos wykorzystuje dwa typy kluczy do uwierzytelniania użytkowników i zapewnienia dostępu do swoich danych i zasobów. 

|Typ klucza|Zasoby|
|---|---|
|[Klucze główne](#master-keys) |Używany do zasobów administracyjnych: baza danych kont, baz danych użytkowników i uprawnienia|
|[Tokeny zasobów](#resource-tokens)|Używany do zasobów aplikacji: kolekcje, dokumenty, załączniki, procedur składowanych, wyzwalaczy i funkcji UDF|

<a id="master-keys"></a>

## <a name="master-keys"></a>Klucze główne 

Klucze główne zapewniają dostęp do wszystkich zasobów administracyjne dla konta bazy danych. Klucze główne:  
- Zapewniają dostęp do konta, baz danych, użytkowników i uprawnienia. 
- Nie może służyć do zapewnienia szczegółowej dostępu do kolekcji i dokumentów.
- Są tworzone podczas tworzenia konta.
- Mogą być generowane w dowolnym momencie.

Każde konto składa się z dwóch kluczy głównych: klucz podstawowy i klucz pomocniczy. Celem dwa klucze jest tak, aby wygenerować lub Przywróć klucze, zapewniając ciągłego dostępu do danych i konta. 

Oprócz dwa klucze główne konto bazy danych rozwiązania Cosmos istnieją dwa klucze tylko do odczytu. Te klucze tylko do odczytu umożliwiają tylko operacji odczytu dla konta. Tylko do odczytu kluczy nie zapewnia dostępu do zasobów uprawnienia do odczytu.

Podstawowe i pomocnicze, tylko do odczytu, a klucze główne odczytu i zapisu mogą być pobierane i ponownie wygenerowane za pomocą portalu Azure. Aby uzyskać instrukcje, zobacz [wyświetlanie, kopiowanie i ponowne generowanie kluczy dostępu](manage-account.md#keys).

![Kontrola dostępu (IAM) w portalu Azure - prezentacja zabezpieczeń bazy danych NoSQL](./media/secure-access-to-data/nosql-database-security-master-key-portal.png)

Obracanie klucz główny proces jest prosty. Przejdź do portalu Azure, aby pobrać klucz pomocniczy, a następnie zastąp klucz podstawowy klucz pomocniczy w aplikacji, a następnie Obróć klucza podstawowego w portalu Azure.

![Obracanie klucza głównego w portalu Azure - prezentacja zabezpieczeń bazy danych NoSQL](./media/secure-access-to-data/nosql-database-security-master-key-rotate-workflow.png)

### <a name="code-sample-to-use-a-master-key"></a>Przykładowy kod, aby użyć klucza głównego

Poniższy przykładowy kod ilustruje sposób użycia DB rozwiązania Cosmos punkt końcowy konta i klucz główny wystąpienia DocumentClient i utworzyć bazę danych. 

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

## <a name="resource-tokens"></a>Tokeny zasobów

Tokeny zasobów zapewniają dostęp do zasobów aplikacji w bazie danych. Tokeny zasobów:
- Zapewniają dostęp do określonej kolekcji, kluczy partycji, dokumenty, załączniki, procedur składowanych, wyzwalaczy i funkcji UDF.
- Są tworzone, gdy [użytkownika](#users) otrzymuje [uprawnienia](#permissions) konkretnego zasobu.
- Są ponownie tworzone, gdy zasób uprawnienie jest reagować na przez wywołanie POST GET i PUT.
- Użyj tokenu zasób skrótu utworzone specjalnie dla użytkowników, zasobów i uprawnienia.
- Jest powiązany z okresem ważności można dostosowywać. Prawidłowy obiekt timespan domyślny to jedna godzina. Okres istnienia tokenu, jednak może być jawnie określona, maksymalnie pięć godzin.
- Zapewniają bezpieczne alternatywę do nadawania klucza głównego. 
- Włącz klientów do odczytu, zapisu i usuwania zasobów w ramach konta bazy danych rozwiązania Cosmos zgodnie z uprawnieniami, które zostały przyznane.

Można użyć tokenu zasobów (Tworzenie rozwiązania Cosmos bazy danych użytkowników i uprawnienia), aby zapewnić dostęp do zasobów na koncie rozwiązania Cosmos bazy danych do klienta, który nie może być zaufany za pomocą klucza głównego.  

Rozwiązania cosmos DB tokenów zasobów zapewniają bezpieczne alternatywę umożliwiająca klientom odczytu, zapisu i usuwania zasobów na koncie DB rozwiązania Cosmos zgodnie z uprawnieniami, które zostały przyznane i bez konieczności głównego lub odczytu tylko klucza.

Oto wzorca projektowego typowe zgodnie z którymi tokenów zasobów mogą być wymagane, generowane i dostarczeniem do klientów:

1. Skonfigurowano usługi warstwie pośredniej do obsługi aplikacji mobilnej, aby udostępnić zdjęcia użytkownika. 
2. Usługi w warstwie pośredniej posiada klucza głównego konta bazy danych rozwiązania Cosmos.
3. To zdjęcie aplikacja jest zainstalowana na urządzeniach przenośnych użytkownika końcowego. 
4. Podczas logowania aplikacja zdjęcia ustalana jest tożsamość użytkownika w usłudze warstwy środkowej. Ten mechanizm określania tożsamości jest wyłącznie do aplikacji.
5. Po nawiązaniu tożsamość usługi warstwie pośredniej żąda uprawnień na podstawie tożsamości.
6. Usługi w warstwie pośredniej wysyła token zasobów z powrotem do aplikacji telefonicznej.
7. Aplikacji phone można nadal używać token zasobu bezpośredni dostęp do zasobów DB rozwiązania Cosmos z uprawnieniami określonego przez token zasobu oraz interwału dozwolone przez token zasobu. 
8. Po wygaśnięciu tokenu zasobów, kolejne żądania odbierania 401 nieautoryzowane wyjątek.  W tym momencie aplikacji phone ponownie ustalana jest tożsamość i żąda nowy token zasobu.

    ![Tokeny zasobów Azure DB rozwiązania Cosmos przepływu pracy](./media/secure-access-to-data/resourcekeyworkflow.png)

Token generowania zasobów i zarządzanie odbywa się przy natywnych bibliotek klienckich DB rozwiązania Cosmos; Jednak jeśli używasz REST należy tworzyć nagłówki żądania/uwierzytelniania. Aby uzyskać więcej informacji na temat tworzenia nagłówki uwierzytelniania dla pozostałych, zobacz [kontroli dostępu do zasobów DB rozwiązania Cosmos](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources) lub [kod źródłowy dla nasze zestawy SDK](https://github.com/Azure/azure-documentdb-node/blob/master/source/lib/auth.js).

Na przykład usługa warstwy środkowej używana do generowania lub broker tokenów zasobów zobacz [aplikacji ResourceTokenBroker](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker/Controllers).

<a id="users"></a>

## <a name="users"></a>Użytkownicy
Rozwiązania cosmos bazy danych użytkowników są skojarzone z bazy danych DB rozwiązania Cosmos.  Każda baza danych może zawierać zero lub więcej użytkowników DB rozwiązania Cosmos.  Poniższy przykładowy kod przedstawia sposób tworzenia rozwiązania Cosmos DB użytkownika zasobu.

```csharp
//Create a user.
User docUser = new User
{
    Id = "mobileuser"
};

docUser = await client.CreateUserAsync(UriFactory.CreateDatabaseUri("db"), docUser);
```

> [!NOTE]
> Każdy użytkownik DB rozwiązania Cosmos ma właściwość PermissionsLink, która może służyć do pobierania listy [uprawnienia](#permissions) skojarzonych z użytkownikiem.
> 
> 

<a id="permissions"></a>

## <a name="permissions"></a>Uprawnienia
Zasób uprawnienia DB rozwiązania Cosmos jest powiązany z użytkownikiem DB rozwiązania Cosmos.  Każdy użytkownik może zawierać zero lub więcej uprawnień DB rozwiązania Cosmos.  Zasób uprawnienia zapewnia dostęp do tokenu zabezpieczającego, który użytkownik musi podczas próby uzyskania dostępu do zasobu określonej aplikacji.
Istnieją dwa poziomy dostępu, które mogą być udostępniane przez zasób uprawnienia:

* Wszystko: Użytkownik ma pełne uprawnienia w zasobie.
* Przeczytaj: Użytkownik może odczytywać tylko zawartość zasobu, ale nie można wykonać zapisu, aktualizacji lub usuwania operacji dla zasobu.

> [!NOTE]
> Aby można było uruchomić DB rozwiązania Cosmos przechowywane procedury, użytkownik musi mieć uprawnienie All w kolekcji, w którym będzie uruchamiany procedury składowanej.
> 
> 

### <a name="code-sample-to-create-permission"></a>Przykładowy kod, aby utworzyć uprawnień

Poniższy przykładowy kod przedstawia sposób tworzenia zasobu uprawnienia, token zasobu zasobu uprawnienia do odczytu i skojarzyć uprawnienia z [użytkownika](#users) utworzone powyżej.

```csharp
// Create a permission.
Permission docPermission = new Permission
{
    PermissionMode = PermissionMode.Read,
    ResourceLink = documentCollection.SelfLink,
    Id = "readperm"
};
  
docPermission = await client.CreatePermissionAsync(UriFactory.CreateUserUri("db", "user"), docPermission);
Console.WriteLine(docPermission.Id + " has token of: " + docPermission.Token);
```

Jeśli został określony klucz partycji dla kolekcji, w uprawnienia dla kolekcji, dokumentów i zasobów załącznika musi także obejmować ResourcePartitionKey oprócz ResourceLink.

### <a name="code-sample-to-read-permissions-for-user"></a>Przykładowy kod do odczytu uprawnienia użytkownika

Można łatwo uzyskać wszystkie uprawnienia zasoby skojarzone z określonego użytkownika, DB rozwiązania Cosmos udostępnianych uprawnienia dla każdego obiektu użytkownika.  Poniższy fragment kodu przedstawia sposób pobierania uprawnienie skojarzone z użytkownikiem utworzone powyżej, utworzenia listy uprawnień i Utwórz wystąpienie nowego wystąpienia klasy DocumentClient w imieniu użytkownika.

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

## <a name="next-steps"></a>Kolejne kroki
* Aby dowiedzieć się więcej na temat zabezpieczeń bazy danych DB rozwiązania Cosmos, zobacz [rozwiązania Cosmos bazy danych: baza danych zabezpieczeń](database-security.md).
* Aby dowiedzieć się więcej o zarządzaniu klucze główne i tylko do odczytu, zobacz [jak zarządzać konta bazy danych Azure rozwiązania Cosmos](manage-account.md#keys).
* Aby dowiedzieć się, jak utworzyć bazy danych Azure rozwiązania Cosmos autoryzacji tokenów, zobacz [kontroli dostępu do zasobów DB rozwiązania Cosmos Azure](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources).
