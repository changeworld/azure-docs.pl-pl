---
title: Filtry zabezpieczeń do przycinania wyników przy użyciu Active Directory-Azure Search
description: Kontrola dostępu do zawartości Azure Search przy użyciu filtrów zabezpieczeń i tożsamości Azure Active Directory (AAD).
author: brjohnstmsft
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 11/07/2017
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: 8bcc1dcd1d86c0ca18ed03dc60834884a42a39c9
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70186518"
---
# <a name="security-filters-for-trimming-azure-search-results-using-active-directory-identities"></a>Filtry zabezpieczeń do przycinania Azure Search wyniki przy użyciu tożsamości Active Directory

W tym artykule pokazano, jak używać tożsamości zabezpieczeń usługi Azure Active Directory (AAD) wraz z filtrami w Azure Search do przycinania wyników wyszukiwania na podstawie przynależności do grupy użytkowników.

W tym artykule opisano następujące zadania:
> [!div class="checklist"]
> - Tworzenie grup i użytkowników usługi AAD
> - Skojarz użytkownika z utworzoną grupą
> - Buforuj nowe grupy
> - Indeksuj dokumenty ze skojarzonymi grupami
> - Wygeneruj żądanie wyszukiwania z filtrem identyfikatorów grup
> 
> [!NOTE]
> Przykładowe fragmenty kodu w tym artykule są zapisywane w C#temacie. Pełny kod źródłowy można znaleźć [w usłudze GitHub](https://aka.ms/search-dotnet-howto). 

## <a name="prerequisites"></a>Wymagania wstępne

Indeks w Azure Search musi mieć [pole zabezpieczeń](search-security-trimming-for-azure-search.md) , aby można było przechowywać listę tożsamości grup z dostępem do odczytu do dokumentu. W tym przypadku użycia przyjęto, że założono, że jeden do jednego z elementów zabezpieczanych (takich jak aplikacja szkoły indywidualnej) i pole zabezpieczeń określają, kto ma dostęp do tego elementu (personel przyjęcia).

Musisz mieć uprawnienia administratora usługi AAD wymagane w tym instruktażu do tworzenia użytkowników, grup i skojarzeń w usłudze AAD.

Aplikacja musi być również zarejestrowana w usłudze AAD, zgodnie z opisem w poniższej procedurze.

### <a name="register-your-application-with-aad"></a>Zarejestruj swoją aplikację w usłudze AAD

Ten krok integruje aplikację z usługą AAD w celu zaakceptowania logowania użytkowników i kont grup. Jeśli nie jesteś administratorem usługi AAD w organizacji, może być konieczne [utworzenie nowej dzierżawy](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant) w celu wykonania poniższych kroków.

1. Przejdź do >   [**portalu rejestracji aplikacji**](https://apps.dev.microsoft.com)**zbieżność aplikacji** > **Dodaj aplikację**.
2. Wprowadź nazwę aplikacji, a następnie kliknij przycisk **Utwórz**. 
3. Wybierz nowo zarejestrowanej aplikacji na stronie Moje aplikacje.
4. Na stronie Rejestracja aplikacji > **platformy** > **Dodaj platformę**, wybierz pozycję **interfejs API sieci Web**.
5. Na stronie Rejestracja aplikacji przejdź do pozycji > **Microsoft Graph uprawnienia** > **Dodaj**.
6. W obszarze Wybierz uprawnienia Dodaj następujące delegowane uprawnienia, a następnie kliknij przycisk **OK**:

   + **Directory.ReadWrite.All**
   + **Group.ReadWrite.All**
   + **User.ReadWrite.All**

Microsoft Graph udostępnia interfejs API, który umożliwia programistyczny dostęp do usługi AAD za pomocą interfejsu API REST. Przykładowy kod dla tego instruktażu używa uprawnień do wywoływania interfejsu API Microsoft Graph do tworzenia grup, użytkowników i skojarzeń. Interfejsy API są również używane do buforowania identyfikatorów grup do szybszego filtrowania.

## <a name="create-users-and-groups"></a>Tworzenie użytkowników i grup

W przypadku dodawania wyszukiwania do ustalonej aplikacji mogą istnieć identyfikatory użytkowników i grup w usłudze AAD. W takim przypadku można pominąć trzy następne kroki. 

Jeśli jednak nie masz istniejących użytkowników, możesz użyć Microsoft Graph interfejsów API do utworzenia podmiotów zabezpieczeń. Poniższe fragmenty kodu pokazują, jak generować identyfikatory, które stają się wartościami danych dla pola zabezpieczenia w indeksie Azure Search. W naszej hipotetycznej aplikacji do naliczania uczelni będą to identyfikatory zabezpieczeń dla personelu usługi Admission Control.

Członkostwo użytkowników i grup może być bardzo płynne, szczególnie w dużych organizacjach. Kod, który kompiluje tożsamości użytkowników i grup, powinien być uruchamiany często, aby można było wybrać zmiany w członkostwie w organizacji. Podobnie indeks Azure Search wymaga podobnego harmonogramu aktualizacji w celu odzwierciedlenia bieżącego stanu dozwolonych użytkowników i zasobów.

### <a name="step-1-create-aad-grouphttpsdocsmicrosoftcomgraphapigroup-post-groupsviewgraph-rest-10"></a>Krok 1: Utwórz [grupę usługi AAD](https://docs.microsoft.com/graph/api/group-post-groups?view=graph-rest-1.0) 
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

### <a name="step-3-associate-user-and-group"></a>Krok 3: Skojarz użytkownika i grupę
```csharp
await graph.Groups[newGroup.Id].Members.References.Request().AddAsync(newUser);
```

### <a name="step-4-cache-the-groups-identifiers"></a>Krok 4: Buforuj identyfikatory grup
Opcjonalnie, aby zmniejszyć opóźnienie sieci, można buforować skojarzenia grupy użytkowników, aby podczas wystawiania żądania wyszukiwania grupy były zwracane z pamięci podręcznej, co pozwala zaoszczędzić dostęp do usługi AAD. Korzystając z [interfejsu API usługi AAD Batch](https://developer.microsoft.com/graph/docs/concepts/json_batching) , można wysyłać pojedyncze żądanie HTTP z wieloma użytkownikami i tworzyć pamięć podręczną.

Microsoft Graph został zaprojektowany z myślą o obsłudze dużej liczby żądań. W przypadku przeciążania liczby żądań Microsoft Graph nie można wykonać żądania z kodem stanu HTTP 429. Aby uzyskać więcej informacji, zobacz [Microsoft Graph ograniczanie przepustowości](https://developer.microsoft.com/graph/docs/concepts/throttling).

## <a name="index-document-with-their-permitted-groups"></a>Indeksowanie dokumentu z ich dozwolonymi grupami

Operacje zapytań w Azure Search są wykonywane nad indeksem Azure Search. W tym kroku Operacja indeksowania importuje dane z możliwością wyszukiwania do indeksu, w tym identyfikatory używane jako filtry zabezpieczeń. 

Azure Search nie uwierzytelnia tożsamości użytkowników ani nie udostępnia logiki do ustanowienia zawartości, do której użytkownik ma uprawnienia do wyświetlania. W przypadku użycia w przypadku przycinania zabezpieczeń przyjęto założenie, że podajesz skojarzenie między poufnym dokumentem a identyfikatorem grupy mającym dostęp do tego dokumentu, który został zaimportowany do indeksu wyszukiwania. 

W hipotetycznym przykładzie treść żądania PUT na indeksie Azure Search będzie zawierać opis lub transkrypcję kolegium kandydata wraz z identyfikatorem grupy mającym uprawnienia do wyświetlania tej zawartości. 

W ogólnym przykładzie używanym w przykładowym kodzie dla tego przewodnika akcja indeksu może wyglądać następująco:

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

## <a name="issue-a-search-request"></a>Wydaj żądanie Search

W celu przycinania zabezpieczeń wartości w polu zabezpieczeń w indeksie są wartościami statycznymi używanymi do dołączania lub wykluczania dokumentów w wynikach wyszukiwania. Na przykład jeśli identyfikator grupy dla przyjmowania to "A11B22C33D44-E55F66G77-H88I99JKK", wszystkie dokumenty w indeksie Azure Search, które mają ten identyfikator w zgłoszonych zabezpieczeniach, są uwzględniane (lub wykluczone) w wynikach wyszukiwania wysłanych z powrotem do osoby żądającej.

Aby odfiltrować dokumenty zwrócone w wynikach wyszukiwania na podstawie grup użytkownika wystawiającego żądanie, należy zapoznać się z poniższymi instrukcjami.

### <a name="step-1-retrieve-users-group-identifiers"></a>Krok 1: Pobierz identyfikatory grup użytkowników

Jeśli grupy użytkownika nie zostały już zapisane w pamięci podręcznej lub pamięć podręczna wygasła, wystaw żądanie [grup](https://docs.microsoft.com/graph/api/directoryobject-getmembergroups?view=graph-rest-1.0)
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

### <a name="step-2-compose-the-search-request"></a>Krok 2: Utwórz żądanie wyszukiwania

Przy założeniu, że masz członkostwo w grupach użytkowników, możesz wydać żądanie wyszukiwania z odpowiednimi wartościami filtru.

```csharp
string filter = String.Format("groupIds/any(p:search.in(p, '{0}'))", string.Join(",", groups.Select(g => g.ToString())));
SearchParameters parameters = new SearchParameters()
             {
                 Filter = filter,
                 Select = new[] { "application essays" }
             };

DocumentSearchResult<SecuredFiles> results = _indexClient.Documents.Search<SecuredFiles>("*", parameters);
```
### <a name="step-3-handle-the-results"></a>Krok 3: Obsługuj wyniki

Odpowiedź obejmuje przefiltrowaną listę dokumentów składającą się z tych, do których użytkownik ma uprawnienia do wyświetlania. W zależności od sposobu konstruowania strony wyników wyszukiwania możesz chcieć uwzględnić podpowiedzi wizualne, aby odzwierciedlić filtrowany zestaw wyników.

## <a name="conclusion"></a>Wniosek

W tym instruktażu przedstawiono metody używania logowań usługi AAD do filtrowania dokumentów w Azure Search wyników, przycinania wyników dokumentów, które nie pasują do filtru dostarczonego w żądaniu.

## <a name="see-also"></a>Zobacz także

+ [Kontrola dostępu oparta na tożsamościch za pomocą filtrów Azure Search](search-security-trimming-for-azure-search.md)
+ [Filtry w Azure Search](search-filters.md)
+ [Bezpieczeństwo danych i kontrola dostępu w Azure Search operacjach](search-security-overview.md)
