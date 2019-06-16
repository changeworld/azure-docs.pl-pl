---
title: Filtry zabezpieczeń, aby przyciąć wyniki za pomocą usługi Active Directory — usługa Azure Search
description: Kontrolę dostępu do zawartości usługi Azure Search przy użyciu filtrów zabezpieczeń i tożsamości usługi Azure Active Directory (AAD).
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.topic: conceptual
ms.date: 11/07/2017
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: 410727022b092e2dd8ab8b05e628e25fd60ab833
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61282217"
---
# <a name="security-filters-for-trimming-azure-search-results-using-active-directory-identities"></a>Filtry zabezpieczeń do przycinania wyniki usługi Azure Search przy użyciu tożsamości usługi Active Directory

W tym artykule przedstawiono sposób użycia tożsamości zabezpieczeń usługi Azure Active Directory (AAD) wraz z filtrów w usłudze Azure Search do przycięcia wyniki wyszukiwania na podstawie członkostwa w grupach użytkownika.

W tym artykule opisano następujące zadania:
> [!div class="checklist"]
> - Tworzenie grup usługi AAD i użytkowników
> - Kojarzenia użytkownika z grupy, które zostały utworzone
> - Nowe grupy w pamięci podręcznej
> - Indeksować dokumenty z skojarzone z nimi grupy
> - Wysłać żądanie wyszukiwania za pomocą grupy identyfikatorów filtru
> 
> [!NOTE]
> Przykładowe fragmenty kodu, w tym artykule są napisane w języku C#. Pełny kod źródłowy można znaleźć [w usłudze GitHub](https://aka.ms/search-dotnet-howto). 

## <a name="prerequisites"></a>Wymagania wstępne

Musi mieć indeksu w usłudze Azure Search [pole zabezpieczeń](search-security-trimming-for-azure-search.md) przechowywanie listy tożsamości grup mających dostęp do odczytu do dokumentu. Ten przypadek użycia zakłada relację między elementem zabezpieczanego (na przykład aplikacja college osoby) i pole zabezpieczeń, określania, kto ma dostęp do tego elementu (przyjęć odpowiadające ilości personelu).

Musi mieć uprawnienia administratora usługi AAD, wymagane w tym przewodniku pozwalający użytkownikom, grupom i skojarzenia w usłudze AAD.

Aplikację należy również zarejestrować przy użyciu usługi AAD, zgodnie z opisem w poniższej procedurze.

### <a name="register-your-application-with-aad"></a>Zarejestruj swoją aplikację przy użyciu usługi AAD

Ten krok integruje aplikacji przy użyciu usługi AAD na potrzeby akceptować logowania do kont użytkowników i grup. Jeśli nie jesteś administratorem usługi AAD w Twojej organizacji, konieczne może być [utworzyć nową dzierżawę](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant) można wykonać następujące czynności.

1. Przejdź do [ **portalu rejestracji aplikacji**](https://apps.dev.microsoft.com) >  **zbieżnych aplikacji** > **Dodaj aplikację**.
2. Wprowadź nazwę aplikacji, a następnie kliknij przycisk **Utwórz**. 
3. Wybierz nowo zarejestrowana aplikacja, na stronie Moje aplikacje.
4. Na stronie rejestracji aplikacji > **platform** > **Dodaj platformy**, wybierz **interfejsu API sieci Web**.
5. Pracując nadal na stronie rejestracji aplikacji, przejdź do > **Microsoft Graph uprawnienia** > **Dodaj**.
6. W polu Wybierz uprawnienia, Dodaj następujące delegowane uprawnienia, a następnie kliknij przycisk **OK**:

   + **Directory.ReadWrite.All**
   + **Group.ReadWrite.All**
   + **User.ReadWrite.All**

Program Microsoft Graph udostępnia interfejs API, który zezwala na dostęp programistyczny do usługi AAD za pomocą interfejsu API REST. Przykładowy kod w tym przewodniku używa uprawnień do wywoływania interfejsu API programu Microsoft Graph do tworzenia grup, użytkowników i skojarzenia. Interfejsy API są również używane do pamięci podręcznej grupy identyfikatorów w celu szybszego filtrowania.

## <a name="create-users-and-groups"></a>Tworzenie użytkowników i grup

W przypadku dodawania wyszukiwania do aplikacji ustanowionych może mieć istniejącego użytkownika i identyfikatory grup w usłudze AAD. W takim przypadku można pominąć kolejne trzy kroki. 

Jednak jeśli nie masz istniejących użytkowników, można użyć interfejsów API programu Microsoft Graph do tworzenia podmiotów zabezpieczeń. Poniższe fragmenty kodu przedstawiają sposób generowania identyfikatorów, które stają się wartości danych dla pola zabezpieczeń w indeksie usługi Azure Search. W naszej aplikacji przyjęć odpowiadające ilości hipotetyczny college to identyfikatory zabezpieczeń dla personelu przyjęć odpowiadające ilości.

Użytkownika i członkostwa w grupie może być bardzo płynne, szczególnie w dużych organizacjach. Kod, który tworzy tożsamości użytkowników i grup należy uruchamiać wystarczająco często zmian w członkostwie w organizacji. Podobnie indeksu usługi Azure Search wymaga podobne harmonogram aktualizacji, aby odzwierciedlić bieżący stan dozwolonych użytkowników i zasobów.

### <a name="step-1-create-aad-grouphttpsdocsmicrosoftcomgraphapigroup-post-groupsviewgraph-rest-10"></a>Krok 1: Utwórz [grupy usługi AAD](https://docs.microsoft.com/graph/api/group-post-groups?view=graph-rest-1.0) 
```csharp
// Instantiate graph client 
GraphServiceClient graph = new GraphServiceClient(new DelegateAuthenticationProvider(...));
Group group = new Group()
{
    DisplayName = "My First Prog Group",
    SecurityEnabled = true,
    MailEnabled = false,
    MailNickname = "group1"
}; 
Group newGroup = await graph.Groups.Request().AddAsync(group);
```
   
### <a name="step-2-create-aad-userhttpsdocsmicrosoftcomgraphapiuser-post-usersviewgraph-rest-10"></a>Krok 2: Utwórz [użytkownika usługi AAD](https://docs.microsoft.com/graph/api/user-post-users?view=graph-rest-1.0)
```csharp
User user = new User()
{
    GivenName = "First User",
    Surname = "User1",
    MailNickname = "User1",
    DisplayName = "First User",
    UserPrincipalName = "User1@FirstUser.com",
    PasswordProfile = new PasswordProfile() { Password = "********" },
    AccountEnabled = true
};
User newUser = await graph.Users.Request().AddAsync(user);
```

### <a name="step-3-associate-user-and-group"></a>Krok 3: Kojarzenie użytkowników i grup
```csharp
await graph.Groups[newGroup.Id].Members.References.Request().AddAsync(newUser);
```

### <a name="step-4-cache-the-groups-identifiers"></a>Krok 4: Identyfikatory grup w pamięci podręcznej
Opcjonalnie Aby zmniejszyć opóźnienia sieci, można buforować skojarzenia grupy użytkowników tak, aby podczas wystawiania żądania wyszukiwania grup są zwracane z pamięci podręcznej, zapisywanie w obie strony do usługi AAD. Możesz użyć [interfejsu API usługi Batch AAD](https://developer.microsoft.com/graph/docs/concepts/json_batching) wysyłanie pojedynczego żądania Http, w których wielu użytkowników i Tworzenie pamięci podręcznej.

Program Microsoft Graph jest przeznaczony do obsługi dużej liczby żądań. Jeśli wystąpi zbyt dużej liczby żądań, programu Microsoft Graph kończy się niepowodzeniem żądania z kodem stanu HTTP 429. Aby uzyskać więcej informacji, zobacz [ograniczania programu Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/throttling).

## <a name="index-document-with-their-permitted-groups"></a>Dokument indeksu z ich dozwolonych grup

Operacje zapytań w usłudze Azure Search są wykonywane za pośrednictwem indeksu usługi Azure Search. W tym kroku operacji indeksowania importuje dane z możliwością wyszukiwania do indeksu, w tym identyfikatory używane jako filtry zabezpieczeń. 

Usługa Azure Search nie uwierzytelniania tożsamości użytkownika lub zapewnić logikę do ustalenia, które treści użytkownik ma uprawnienia do wyświetlania. Przypadek użycia dla dostosowanie do zabezpieczeń przyjęto założenie, które podasz skojarzenie między poufnych dokumentów i identyfikatora grupy mające dostęp do tego dokumentu, zaimportowane opublikowane do indeksu wyszukiwania. 

W tym przykładzie hipotetyczny treści żądania PUT względem indeksu usługi Azure Search obejmuje opisowych college kandydata lub transkrypcję, oraz identyfikator grupy, mając uprawnienia do wyświetlania tej zawartości. 

W przykładzie ogólne używane w przykładowym kodzie, w tym przewodniku akcji indeksu może wyglądać w następujący sposób:

```csharp
var actions = new IndexAction<SecuredFiles>[]
              {
                  IndexAction.Upload(
                  new SecuredFiles()
                  {
                      FileId = "1",
                      Name = "secured_file_a",
                      GroupIds = new[] { groups[0] }
                  }),
              ...
             };

var batch = IndexBatch.New(actions);

_indexClient.Documents.Index(batch);  
```

## <a name="issue-a-search-request"></a>Żądania wyszukiwania

Ze względów bezpieczeństwa przycinania wartości w polu zabezpieczeń w indeksie są statyczne wartości używanych dla uwzględniając lub wykluczając dokumentów w wynikach wyszukiwania. Na przykład jeśli identyfikator grupy dla przyjęć odpowiadające ilości "A11B22C33D44-E55F66G77 H88I99JKK", wszelkie dokumenty do indeksu usługi Azure Search o identyfikatora zabezpieczeń wprowadzone są uwzględnione (lub wykluczone) w wynikach wyszukiwania, wysyłanych z powrotem do osoby zgłaszającej żądanie.

Aby filtrować dokumenty zwrócone w wynikach wyszukiwania, na podstawie grup użytkowników, wysyłania żądania, przejrzyj poniższe kroki.

### <a name="step-1-retrieve-users-group-identifiers"></a>Krok 1: Pobieranie identyfikatorów grupy użytkownika

Grupy użytkowników nie zostały jeszcze zapisane lub wygasła pamięć podręczną, należy wydać [grup](https://docs.microsoft.com/graph/api/directoryobject-getmembergroups?view=graph-rest-1.0) żądania
```csharp
private static void RefreshCacheIfRequired(string user)
{
    if (!_groupsCache.ContainsKey(user))
    {
        var groups = GetGroupIdsForUser(user).Result;
        _groupsCache[user] = groups;
    }
}

private static async Task<List<string>> GetGroupIdsForUser(string userPrincipalName)
{
    List<string> groups = new List<string>();
    var allUserGroupsRequest = graph.Users[userPrincipalName].GetMemberGroups(true).Request();

    while (allUserGroupsRequest != null) 
    {
        IDirectoryObjectGetMemberGroupsRequestBuilder allUserGroups = await allUserGroupsRequest.PostAsync();
        groups = allUserGroups.ToList();
        allUserGroupsRequest = allUserGroups.NextPageRequest;
    }
    return groups;
}
``` 

### <a name="step-2-compose-the-search-request"></a>Krok 2: Tworzenie żądania wyszukiwania

Przy założeniu, że członkostwa grupy użytkownika, może wysłać żądanie wyszukiwania przy użyciu wartości odpowiedni filtr.

```csharp
string filter = String.Format("groupIds/any(p:search.in(p, '{0}'))", string.Join(",", groups.Select(g => g.ToString())));
SearchParameters parameters = new SearchParameters()
             {
                 Filter = filter,
                 Select = new[] { "application essays" }
             };

DocumentSearchResult<SecuredFiles> results = _indexClient.Documents.Search<SecuredFiles>("*", parameters);
```
### <a name="step-3-handle-the-results"></a>Krok 3: Obsługa wyników

Odpowiedź zawiera filtrowana lista dokumentów, składający się z tymi, których użytkownik ma uprawnienia do wyświetlania. W zależności od sposobu konstruowania strony wyników wyszukiwania możesz chcieć dołączyć podpowiedzi wizualne, aby odzwierciedlić zestawu przefiltrowanych wyników.

## <a name="conclusion"></a>Podsumowanie

W tym instruktażu przedstawiono techniki filtrowanie dokumentów w wynikach usługi Azure Search przy użyciu usługi AAD logowania przycinania wyniki dokumentów, które nie pasują do filtru, dostępnym na żądanie.

## <a name="see-also"></a>Zobacz także

+ [Kontrola dostępu na podstawie tożsamości przy użyciu filtrów usługi Azure Search](search-security-trimming-for-azure-search.md)
+ [Filtry w usłudze Azure Search](search-filters.md)
+ [Danych zabezpieczeń i kontroli dostępu w ramach operacji usługi Azure Search](search-security-overview.md)
