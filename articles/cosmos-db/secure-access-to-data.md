---
title: Dowiedz się, jak zabezpieczyć dostęp do danych w Azure Cosmos DB
description: Poznaj koncepcje kontroli dostępu w Azure Cosmos DB, w tym klucze główne, klucze tylko do odczytu, użytkowników i uprawnienia.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: ec32dfbe9aac13a9b3c08922b73b9a23d668d744
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73152277"
---
# <a name="secure-access-to-data-in-azure-cosmos-db"></a>Bezpieczny dostęp do danych w Azure Cosmos DB

Ten artykuł zawiera omówienie zabezpieczania dostępu do danych przechowywanych w [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Azure Cosmos DB używa dwóch typów kluczy do uwierzytelniania użytkowników i zapewnienia dostępu do jego danych i zasobów. 

|Typ klucza|Zasoby|
|---|---|
|[Klucze główne](#master-keys) |Używany do zasobów administracyjnych: konta bazy danych, bazy danych, użytkownicy i uprawnienia|
|[Tokeny zasobów](#resource-tokens)|Używany do zasobów aplikacji: kontenery, dokumenty, załączniki, procedury składowane, wyzwalacze i UDF|

<a id="master-keys"></a>

## <a name="master-keys"></a>Klucze główne 

Klucze główne zapewniają dostęp do wszystkich zasobów administracyjnych dla konta bazy danych. Klucze główne:  
- Zapewnianie dostępu do kont, baz danych, użytkowników i uprawnień. 
- Nie można użyć, aby zapewnić szczegółowy dostęp do kontenerów i dokumentów.
- Są tworzone podczas tworzenia konta.
- Można ją wygenerować w dowolnym momencie.

Każde konto składa się z dwóch kluczy głównych: klucza podstawowego i klucza pomocniczego. Dwa klucze polega na tym, że można generować lub przetwarzać klucze, zapewniając ciągły dostęp do konta i danych. 

Oprócz dwóch kluczy głównych dla konta Cosmos DB istnieją dwa klucze tylko do odczytu. Te klucze tylko do odczytu zezwalają na operacje odczytu tylko na tym koncie. Klucze tylko do odczytu nie zapewniają dostępu do uprawnień do odczytu zasobów.

Klucze główne, pomocnicze, tylko do odczytu i odczyt-zapis można pobrać i ponownie wygenerować przy użyciu Azure Portal. Aby uzyskać instrukcje, zobacz [Wyświetlanie, kopiowanie i ponowne generowanie kluczy dostępu](manage-with-cli.md#regenerate-account-key).

![Kontrola dostępu (IAM) w Azure Portal-demonstrowanie zabezpieczeń bazy danych NoSQL](./media/secure-access-to-data/nosql-database-security-master-key-portal.png)

Proces obracania klucza głównego jest prosty. Przejdź do Azure Portal, aby pobrać klucz pomocniczy, a następnie zastąp klucz podstawowy kluczem pomocniczym w aplikacji, a następnie obróć klucz podstawowy w Azure Portal.

![Rotacja klucza głównego w Azure Portal-demonstrowanie zabezpieczeń bazy danych NoSQL](./media/secure-access-to-data/nosql-database-security-master-key-rotate-workflow.png)

### <a name="code-sample-to-use-a-master-key"></a>Przykład kodu, aby użyć klucza głównego

Poniższy przykład kodu ilustruje, jak używać punktu końcowego konta Cosmos DB i klucza głównego do tworzenia wystąpienia DocumentClient i utworzenia bazy danych. 

```csharp
//Read the Azure Cosmos DB endpointUrl and authorization keys from config.
//These values are available from the Azure portal on the Azure Cosmos DB account blade under "Keys".
//NB > Keep these values in a safe and secure location. Together they provide Administrative access to your DocDB account.

private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
private static readonly string authorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];

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

Tokeny zasobów zapewniają dostęp do zasobów aplikacji w ramach bazy danych. Tokeny zasobów:
- Zapewnianie dostępu do określonych kontenerów, kluczy partycji, dokumentów, załączników, procedur składowanych, wyzwalaczy i UDF.
- Są tworzone, gdy [użytkownik](#users) uzyskuje [uprawnienia](#permissions) do określonego zasobu.
- Są ponownie tworzone, gdy zasób uprawnień jest podejmowany podczas działania przez POST, GET lub PUT.
- Użyj tokenu zasobu skrótu przeznaczonego dla użytkownika, zasobu i uprawnienia.
- Są powiązane z dostosowywanym okresem ważności. Domyślny prawidłowy przedział czasu to jedna godzina. Okres istnienia tokenu można jednak określić jawnie, maksymalnie pięć godzin.
- Podaj bezpieczną alternatywę do wydawania klucza głównego. 
- Umożliwianie klientom odczytywania, zapisywania i usuwania zasobów na koncie Cosmos DB zgodnie z przyznanymi uprawnieniami.

Możesz użyć tokenu zasobu (przez utworzenie Cosmos DB użytkowników i uprawnień), jeśli chcesz zapewnić dostęp do zasobów na koncie Cosmos DB do klienta, którego nie można zaufać z kluczem głównym.  

Tokeny zasobów Cosmos DB zapewniają bezpieczną alternatywę, która umożliwia klientom odczytywanie, zapisywanie i usuwanie zasobów na koncie Cosmos DB zgodnie z przyznanymi uprawnieniami i bez potrzeby dla klucza głównego lub tylko do odczytu.

Poniżej przedstawiono typowy Wzorzec projektowy, w którym tokeny zasobów mogą być żądane, generowane i dostarczane do klientów:

1. Usługa warstwy środkowej jest skonfigurowana do obsługi aplikacji mobilnej w celu udostępniania zdjęć użytkowników. 
2. Usługa warstwy środkowej dysponuje kluczem głównym konta Cosmos DB.
3. Aplikacja Photo jest instalowana na urządzeniach przenośnych użytkowników końcowych. 
4. W przypadku logowania Aplikacja Photo nawiązuje tożsamość użytkownika z usługą warstwy środkowej. Mechanizm tworzenia tożsamości jest całkowicie do aplikacji.
5. Po nawiązaniu tożsamości usługa warstwy średniej żąda uprawnień na podstawie tożsamości.
6. Usługa warstwy średniej wysyła token zasobu z powrotem do aplikacji na telefon.
7. Aplikacja na telefon może nadal używać tokenu zasobu do bezpośredniego dostępu do zasobów Cosmos DB z uprawnieniami zdefiniowanymi przez token zasobu i dla interwału dozwolonego przez token zasobu. 
8. Po wygaśnięciu tokenu zasobu kolejne żądania otrzymują nieautoryzowany wyjątek 401.  W tym momencie Aplikacja telefoniczna przetworzy tożsamość i zażąda nowego tokenu zasobu.

    ![Przepływ pracy tokenów zasobów Azure Cosmos DB](./media/secure-access-to-data/resourcekeyworkflow.png)

Generowanie tokenów zasobów i zarządzanie nimi jest obsługiwane przez natywne biblioteki klienckie Cosmos DB. Jeśli jednak używasz REST, musisz utworzyć nagłówki żądania/uwierzytelniania. Aby uzyskać więcej informacji na temat tworzenia nagłówków uwierzytelniania dla usługi REST, zobacz [Access Control Cosmos DB zasobów](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources) lub [kodu źródłowego dla naszych zestawów SDK](https://github.com/Azure/azure-documentdb-node/blob/master/source/lib/auth.js).

Przykład usługi warstwy środkowej używanej do generowania lub brokera tokenów zasobów można znaleźć w [aplikacji ResourceTokenBroker](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker/Controllers).

<a id="users"></a>

## <a name="users"></a>Użytkownicy
Cosmos DB użytkownicy są skojarzeni z bazą danych Cosmos.  Każda baza danych może zawierać co najmniej zero Cosmos DB użytkowników.  Poniższy przykład kodu pokazuje, jak utworzyć zasób użytkownika Cosmos DB.

```csharp
//Create a user.
User docUser = new User
{
    Id = "mobileuser"
};

docUser = await client.CreateUserAsync(UriFactory.CreateDatabaseUri("db"), docUser);
```

> [!NOTE]
> Każdy użytkownik Cosmos DB ma właściwość PermissionsLink, która może służyć do pobierania listy [uprawnień](#permissions) skojarzonych z użytkownikiem.
> 
> 

<a id="permissions"></a>

## <a name="permissions"></a>Uprawnienia
Zasób uprawnienia Cosmos DB jest skojarzony z Cosmos DB użytkownikiem.  Każdy użytkownik może zawierać co najmniej zero uprawnień Cosmos DB.  Zasób uprawnienia zapewnia dostęp do tokenu zabezpieczającego wymaganego przez użytkownika podczas próby dostępu do określonego zasobu aplikacji.
Dostępne są dwa poziomy dostępu, które mogą być udostępniane przez zasób uprawnienia:

* Wszystko: użytkownik ma pełne uprawnienia do zasobu.
* Odczytaj: użytkownik może odczytać tylko zawartość zasobu, ale nie może wykonywać operacji zapisu, aktualizowania ani usuwania dla zasobu.

> [!NOTE]
> Aby można było uruchomić Cosmos DB procedury składowane, użytkownik musi mieć uprawnienie wszystkie do kontenera, w którym zostanie uruchomiona procedura składowana.
> 
> 

### <a name="code-sample-to-create-permission"></a>Przykład kodu do utworzenia uprawnienia

Poniższy przykładowy kod przedstawia sposób tworzenia zasobu uprawnienia, odczytywania tokenu zasobu zasobu uprawnienia i kojarzenia uprawnień z utworzonym powyżej [użytkownikiem](#users) .

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

Jeśli określono klucz partycji dla kolekcji, wówczas uprawnienie do kolekcji, dokumentów i zasobów załączników również musi zawierać ResourcePartitionKey oprócz ResourceLink.

### <a name="code-sample-to-read-permissions-for-user"></a>Przykład kodu do odczytu uprawnień użytkownika

Aby łatwo uzyskać wszystkie uprawnienia skojarzone z określonym użytkownikiem, Cosmos DB udostępnia źródło uprawnień dla każdego obiektu użytkownika.  Poniższy fragment kodu przedstawia sposób pobierania uprawnienia skojarzonego z użytkownikiem utworzonym powyżej, konstruowania listy uprawnień i tworzenia wystąpienia nowego DocumentClient w imieniu użytkownika.

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

## <a name="add-users-and-assign-roles"></a>Dodawanie użytkowników i przypisywanie ról

Aby dodać do konta użytkownika dostęp do czytnika konta Azure Cosmos DB, należy wykonać następujące czynności w Azure Portal.

1. Otwórz Azure Portal i wybierz swoje konto Azure Cosmos DB.
2. Kliknij kartę **Kontrola dostępu (IAM)** , a następnie kliknij pozycję **+ Dodaj przypisanie roli**.
3. W okienku **Dodaj przypisanie roli** w polu **rola** wybierz pozycję **Cosmos DB rolę czytelnika konta**.
4. W **polu Przypisz dostęp do**wybierz pozycję **użytkownik, Grupa lub aplikacja usługi Azure AD**.
5. Wybierz użytkownika, grupę lub aplikację w katalogu, do którego chcesz udzielić dostępu.  Katalog można wyszukać według nazwy wyświetlanej, adresu e-mail lub identyfikatorów obiektów.
    Wybrany użytkownik, Grupa lub aplikacja pojawi się na liście wybranych członków.
6. Kliknij przycisk **Save** (Zapisz).

Jednostka może teraz odczytywać Azure Cosmos DB zasoby.

## <a name="delete-or-export-user-data"></a>Usuwanie lub eksportowanie danych użytkownika
Azure Cosmos DB pozwala wyszukiwać, wybierać, modyfikować i usuwać dane osobowe znajdujące się w bazie danych lub kolekcjach. Azure Cosmos DB udostępnia interfejsy API do znajdowania i usuwania danych osobowych, jednak odpowiedzialność za korzystanie z interfejsów API i Definiowanie logiki wymaganej do wymazania danych osobowych. Każdy interfejs API z obsługą wielu modeli (SQL, MongoDB, Gremlin, Cassandra, tabela) oferuje różne zestawy SDK języka, które zawierają metody do wyszukiwania i usuwania danych osobowych. Możesz również włączyć funkcję [Time to Live (TTL)](time-to-live.md) , aby automatycznie usuwać dane po upływie określonego czasu, bez ponoszenia dodatkowych kosztów.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Następne kroki
* Aby dowiedzieć się więcej na temat zabezpieczeń bazy danych Cosmos, zobacz [Cosmos DB: zabezpieczenia bazy danych](database-security.md).
* Aby dowiedzieć się, jak utworzyć tokeny autoryzacji Azure Cosmos DB, zobacz [Access Control dotyczące zasobów Azure Cosmos DB](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources).
