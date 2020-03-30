---
title: Filtry zabezpieczeń do przycinania wyników przy użyciu usługi Active Directory
titleSuffix: Azure Cognitive Search
description: Kontrola dostępu do zawartości usługi Azure Cognitive Search przy użyciu filtrów zabezpieczeń i tożsamości usługi Azure Active Directory (AAD).
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 01280b6ee9dda15af3c0fc707a385501580c624c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72794300"
---
# <a name="security-filters-for-trimming-azure-cognitive-search-results-using-active-directory-identities"></a>Filtry zabezpieczeń do przycinania wyników usługi Azure Cognitive Search przy użyciu tożsamości usługi Active Directory

W tym artykule pokazano, jak używać tożsamości zabezpieczeń usługi Azure Active Directory (AAD) wraz z filtrami w usłudze Azure Cognitive Search w celu przycinania wyników wyszukiwania na podstawie członkostwa w grupie użytkowników.

W tym artykule opisano następujące zadania:
> [!div class="checklist"]
> - Tworzenie grup IAD i użytkowników
> - Skojarzyć użytkownika z utworzoną grupą
> - Buforowanie nowych grup
> - Indeksuj dokumenty ze skojarzonymi grupami
> - Wystawianie żądania wyszukiwania z filtrem identyfikatorów grup
> 
> [!NOTE]
> Przykładowe fragmenty kodu w tym artykule są napisane w języku C#. Pełny kod źródłowy można znaleźć [w usłudze GitHub](https://aka.ms/search-dotnet-howto). 

## <a name="prerequisites"></a>Wymagania wstępne

Indeks w usłudze Azure Cognitive Search musi mieć [pole zabezpieczeń](search-security-trimming-for-azure-search.md) do przechowywania listy tożsamości grupy z dostępem do odczytu do dokumentu. Ten przypadek użycia zakłada korespondencję jeden do jednego między zabezpieczanym elementem (takim jak aplikacja uczelni) a polem zabezpieczeń określającym, kto ma dostęp do tego elementu (personel przyjęć).

Do tworzenia użytkowników, grup i skojarzeń w uad muszą być wymagane uprawnienia administratora usługi AAD.

Wniosek musi być również zarejestrowany w AAD, zgodnie z poniższą procedurą.

### <a name="register-your-application-with-aad"></a>Zarejestruj zgłoszenie w UAD

Ten krok integruje aplikację z usługą AAD w celu zaakceptowania logowania kont użytkowników i grup. Jeśli nie jesteś administratorem usługi AAD w organizacji, może być konieczne [utworzenie nowej dzierżawy,](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant) aby wykonać następujące kroki.

1. Przejdź do**aplikacji** > Portal [**rejestracji**](https://apps.dev.microsoft.com) >  aplikacji**Dodaj aplikację**.
2. Wprowadź nazwę aplikacji, a następnie kliknij przycisk **Utwórz**. 
3. Wybierz nowo zarejestrowaną aplikację na stronie Moje aplikacje.
4. Na stronie rejestracji aplikacji > **Platform** > **Dodaj platformę**, wybierz **web API**.
5. Nadal na stronie rejestracji aplikacji przejdź do > **Microsoft Graph Permissions** > **Add**.
6. W obszarze Wybierz uprawnienia dodaj następujące uprawnienia delegowane, a następnie kliknij przycisk **OK:**

   + **Katalog.ReadWrite.All**
   + **Group.ReadWrite.All**
   + **Użytkownik.ReadWrite.All**

Program Microsoft Graph udostępnia interfejs API umożliwiający programowy dostęp do usługi AAD za pośrednictwem interfejsu API REST. Przykładowy kod dla tego przewodnika używa uprawnień do wywoływania interfejsu API programu Microsoft Graph do tworzenia grup, użytkowników i skojarzeń. Interfejsy API są również używane do buforowania identyfikatorów grup w celu szybszego filtrowania.

## <a name="create-users-and-groups"></a>Tworzenie użytkowników i grup

Jeśli dodajesz wyszukiwanie do ustalonej aplikacji, może mieć istniejące identyfikatory użytkowników i grup w UAD. W takim przypadku można pominąć następne trzy kroki. 

Jeśli jednak nie masz istniejących użytkowników, możesz użyć interfejsów API programu Microsoft Graph do utworzenia podmiotów zabezpieczeń. Poniższe fragmenty kodu pokazują, jak wygenerować identyfikatory, które stają się wartościami danych dla pola zabezpieczeń w indeksie usługi Azure Cognitive Search. W naszym hipotetycznym wniosku o przyjęcie na studia, byłoby to identyfikatory bezpieczeństwa dla personelu przyjęć.

Członkostwo użytkowników i grup może być bardzo płynne, szczególnie w dużych organizacjach. Kod, który tworzy tożsamości użytkowników i grup powinny działać wystarczająco często, aby odebrać zmiany w członkostwie w organizacji. Podobnie indeks usługi Azure Cognitive Search wymaga podobnego harmonogramu aktualizacji, aby odzwierciedlić bieżący stan dozwolonych użytkowników i zasobów.

### <a name="step-1-create-aad-group"></a>Krok 1: Tworzenie [grupy AAD](https://docs.microsoft.com/graph/api/group-post-groups?view=graph-rest-1.0) 
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
   
### <a name="step-2-create-aad-user"></a>Krok 2: Tworzenie [użytkownika AAD](https://docs.microsoft.com/graph/api/user-post-users?view=graph-rest-1.0)
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

### <a name="step-3-associate-user-and-group"></a>Krok 3: Kojarzenie użytkownika i grupy
```csharp
await graph.Groups[newGroup.Id].Members.References.Request().AddAsync(newUser);
```

### <a name="step-4-cache-the-groups-identifiers"></a>Krok 4: Buforowanie identyfikatorów grup
Opcjonalnie, aby zmniejszyć opóźnienia sieci, można buforować skojarzenia grupy użytkowników, tak aby po wystawieniu żądania wyszukiwania grupy były zwracane z pamięci podręcznej, zapisując w obie strony do usługi AAD. Za pomocą [interfejsu API partii usługi AAD](https://developer.microsoft.com/graph/docs/concepts/json_batching) można wysłać pojedyncze żądanie http z wieloma użytkownikami i zbudować pamięć podręczną.

Program Microsoft Graph został zaprojektowany do obsługi dużej liczby żądań. Jeśli wystąpi przytłaczająca liczba żądań, program Microsoft Graph nie powiedzie się żądanie z kodem stanu HTTP 429. Aby uzyskać więcej informacji, zobacz [Ograniczanie programu Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/throttling).

## <a name="index-document-with-their-permitted-groups"></a>Indeksowanie dokumentu z dozwolonymi grupami

Operacje kwerend w usłudze Azure Cognitive Search są wykonywane za pomocą indeksu usługi Azure Cognitive Search. W tym kroku operacja indeksowania importuje dane z przeszukiwania do indeksu, w tym identyfikatory używane jako filtry zabezpieczeń. 

Usługa Azure Cognitive Search nie uwierzytelnia tożsamości użytkowników ani nie udostępnia logiki do ustalania zawartości, do wyświetlania, do której użytkownik ma uprawnienia. Przypadek użycia przycinania zabezpieczeń zakłada, że należy podać skojarzenie między poufnym dokumentem a identyfikatorem grupy mającym dostęp do tego dokumentu, importowanym w stanie nienaruszonym do indeksu wyszukiwania. 

W hipotetycznym przykładzie treść żądania PUT w indeksie usługi Azure Cognitive Search będzie zawierać esej lub transkrypcję uczelni wnioskodawcy wraz z identyfikatorem grupy, który ma uprawnienia do wyświetlania tej zawartości. 

W przykładzie ogólnym używanym w przykładzie kodu dla tego przewodnika akcja indeksu może wyglądać następująco:

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

## <a name="issue-a-search-request"></a>Wystawianie żądania wyszukiwania

Do celów przycinania zabezpieczeń wartości w polu zabezpieczeń w indeksie są wartościami statycznymi używanymi do uwzględniania lub wykluczania dokumentów w wynikach wyszukiwania. Na przykład jeśli identyfikator grupy dla przyjęć jest "A11B22C33D44-E55F66G77-H88I99JKK", wszystkie dokumenty w indeksie usługi Azure Cognitive Search o tym identyfikatorze w zabezpieczeniach złożony są uwzględniane (lub wykluczone) w wynikach wyszukiwania odesłanych do żądacza.

Aby filtrować dokumenty zwracane w wynikach wyszukiwania na podstawie grup użytkownika wystawiającego żądanie, zapoznaj się z poniższymi krokami.

### <a name="step-1-retrieve-users-group-identifiers"></a>Krok 1: Pobieranie identyfikatorów grup użytkownika

Jeśli grupy użytkownika nie zostały jeszcze buforowane lub pamięć podręczna wygasła, wyestosuj żądanie [grup](https://docs.microsoft.com/graph/api/directoryobject-getmembergroups?view=graph-rest-1.0)
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

### <a name="step-2-compose-the-search-request"></a>Krok 2: Skomponowanie żądania wyszukiwania

Zakładając, że masz członkostwo w grupach użytkownika, możesz wystawić żądanie wyszukiwania z odpowiednimi wartościami filtru.

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

Odpowiedź zawiera filtrowane listy dokumentów, składający się z tych, które użytkownik ma uprawnienia do wyświetlania. W zależności od sposobu konstruowania strony wyników wyszukiwania można dołączyć wskazówki wizualne odzwierciedlające zestaw wyników filtrowanych.

## <a name="conclusion"></a>Podsumowanie

W tym instruktażu zostały nauczonych technik używania logowania usługi AAD do filtrowania dokumentów w wynikach usługi Azure Cognitive Search, przycinanie wyników dokumentów, które nie pasują do filtru podanego w żądaniu.

## <a name="see-also"></a>Zobacz też

+ [Kontrola dostępu oparta na tożsamościach przy użyciu filtrów usługi Azure Cognitive Search](search-security-trimming-for-azure-search.md)
+ [Filtry w usłudze Azure Cognitive Search](search-filters.md)
+ [Bezpieczeństwo i kontrola dostępu do danych w operacjach usługi Azure Cognitive Search](search-security-overview.md)
