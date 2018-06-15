---
title: Filtry zabezpieczeń przycinania w wynikach wyszukiwania Azure przy użyciu tożsamości usługi Active Directory | Dokumentacja firmy Microsoft
description: Kontrolę dostępu do zawartości usługi Azure Search przy użyciu filtrów zabezpieczeń i tożsamości usługi Active Directory.
author: revitalbarletz
manager: jlembicz
services: search
ms.service: search
ms.topic: conceptual
ms.date: 11/07/2017
ms.author: revitalb
ms.openlocfilehash: 7c1723e01c78132169d8975473a0e9f5466a066c
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
ms.locfileid: "31796537"
---
# <a name="security-filters-for-trimming-azure-search-results-using-active-directory-identities"></a>Filtry zabezpieczeń do przycinania wyników usługi Azure Search przy użyciu tożsamości usługi Active Directory

W tym artykule przedstawiono sposób użycia tożsamości zabezpieczeń usługi Azure Active Directory (AAD) wraz z filtrów w usłudze Azure Search przyciąć wyniki wyszukiwania na podstawie członkostwa grupy użytkownika.

W tym artykule opisano następujące zadania:
> [!div class="checklist"]
- Tworzenie grupy usługi AAD i użytkownicy.
- Kojarzenie użytkownika z grupy, które zostały utworzone
- Buforuj nowych grup
- Indeks dokumentów za pomocą skojarzone z nimi grupy
- Wysłać żądania wyszukiwania z filtrem identyfikatory grupy

>[!NOTE]
> Przykładowych fragmentów kodu w tym artykule są napisane w języku C#. Pełny kod źródłowy można znaleźć [w usłudze GitHub](http://aka.ms/search-dotnet-howto). 

## <a name="prerequisites"></a>Wymagania wstępne

Musi mieć indeksu w usłudze Azure Search [pola zabezpieczeń](search-security-trimming-for-azure-search.md) do przechowywania listy tożsamości grupy mające dostęp do odczytu do dokumentu. Ten przypadek użycia zakłada odpowiednika między elementem zabezpieczanego (na przykład osoba uczelni aplikacji) i określanie, kto ma dostęp do tego elementu (dopuszczania personelu) pola zabezpieczeń.

Musi mieć uprawnienia administratora usługi AAD, wymagane w ramach tego przewodnika tworzenia użytkowników, grup i skojarzenia w usłudze AAD.

Aplikacji musi również być zarejestrowane w usłudze AAD, zgodnie z opisem w poniższej procedurze.

### <a name="register-your-application-with-aad"></a>Zarejestrować aplikację w usłudze AAD

Ten krok integruje się aplikacji przy użyciu usługi AAD w celu akceptowania logowania konta użytkowników i grup. Jeśli nie jesteś administratorem usługi AAD w Twojej organizacji, konieczne może być [utworzyć nową dzierżawę](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant) można wykonać następujące czynności.

1. Przejdź do [ **portalu rejestracji aplikacji**](https://apps.dev.microsoft.com) >  **Konwergentnej aplikacji** > **Dodaj aplikację**.
2. Wprowadź nazwę aplikacji, a następnie kliknij przycisk **Utwórz**. 
3. Wybierz aplikację do nowo zarejestrowanych na stronie Moje aplikacje.
4. Na stronie rejestracji aplikacji > **platform** > **dodać platformy**, wybierz **interfejsu API sieci Web**.
5. Pracując nadal na stronie rejestracji aplikacji, przejdź do > **Microsoft Graph uprawnienia** > **Dodaj**.
6. Wybierz uprawnienia, Dodaj następujące delegowane uprawnienia, a następnie kliknij przycisk **OK**:

   + **Directory.ReadWrite.All**
   + **Group.ReadWrite.All**
   + **User.ReadWrite.All**

Program Microsoft Graph udostępnia interfejs API, który zezwala na dostęp programistyczny do usługi AAD za pośrednictwem interfejsu API REST. Przykładowy kod w ramach tego przewodnika używa uprawnień do wywołania interfejsu API programu Microsoft Graph do tworzenia grup użytkowników i skojarzenia. Interfejsy API są również używane do pamięci podręcznej grupy identyfikatorów szybciej filtrowania.

## <a name="create-users-and-groups"></a>Tworzenie użytkowników i grup

Jeśli dodajesz wyszukiwania do ustalonych aplikacji, może być istniejącego użytkownika i identyfikatory grup w usłudze AAD. W takim przypadku można pominąć trzech kolejnych czynności. 

Jednak jeśli nie masz istniejących użytkowników, można użyć programu Microsoft Graph API można utworzyć podmiotów zabezpieczeń. Poniższe fragmenty kodu przedstawiają sposób generowania identyfikatorów, które stają się wartości danych dla pola zabezpieczeń w indeksie usługi wyszukiwanie Azure. W naszej aplikacji dopuszczania hipotetyczny uczelni to identyfikatorów zabezpieczeń dla personelu dopuszczania.

Może być bardzo płynne, szczególnie w dużych organizacjach użytkownika i członkostwa w grupie. Kod, który tworzy tożsamości użytkowników i grup należy uruchomić wystarczająco często wykrycie zmian w członkostwie w organizacji. Podobnie indeks usługi Azure Search wymaga podobne harmonogram aktualizacji, aby odzwierciedlały bieżący stan dozwolonych użytkowników i zasoby.

### <a name="step-1-create-aad-grouphttpsdevelopermicrosoftcomgraphdocsapi-referencev10apigrouppostgroups"></a>Krok 1: Tworzenie [grupy usługi AAD](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/group_post_groups) 
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
   
### <a name="step-2-create-aad-userhttpsdevelopermicrosoftcomgraphdocsapi-referencev10apiuserpostusers"></a>Krok 2: Tworzenie [użytkowników usługi AAD](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_post_users) 
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

### <a name="step-3-associate-user-and-group"></a>Krok 3: Skojarz użytkowników i grup
```csharp
await graph.Groups[newGroup.Id].Members.References.Request().AddAsync(newUser);
```

### <a name="step-4-cache-the-groups-identifiers"></a>Krok 4: Pamięci podręcznej identyfikatory grup
Opcjonalnie w celu zmniejszenia opóźnienia sieci, można buforować skojarzenia grupy użytkowników, aby podczas wystawiania żądania wyszukiwania grup są zwracane z pamięci podręcznej, zapisywanie obie strony do usługi AAD. Możesz użyć (API partii AAD) [https://developer.microsoft.com/graph/docs/concepts/json_batching] Aby wysłać pojedyncze żądanie Http z wieloma użytkownikami i Tworzenie pamięci podręcznej.

Program Microsoft Graph przeznaczony do obsługi dużej liczby żądań. Ewentualnych utrudnione liczba żądań Microsoft Graph kończy się niepowodzeniem z kodem stanu HTTP 429 żądania. Aby uzyskać więcej informacji, zobacz [ograniczania Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/throttling).

## <a name="index-document-with-their-permitted-groups"></a>W dokumencie indeksu z ich dozwolonych grup

Operacje zapytań w usłudze Azure Search są wykonywane za pośrednictwem indeksu usługi Azure Search. W tym kroku operacji indeksowania importuje dane z możliwością wyszukiwania do indeks, w tym identyfikatory używane jako filtry zabezpieczeń. 

Usługa Azure Search nie uwierzytelniania tożsamości użytkownika lub podaj logiki ustalania zawartość, która użytkownik ma uprawnienia do wyświetlania. Przypadek użycia przycinania zabezpieczeń przyjęto założenie, musisz zapewnić skojarzenie między poufnych dokumentów i identyfikatora grupy mające dostęp do tego dokumentu, nienaruszonej zaimportowane do indeksu wyszukiwania. 

W tym przykładzie hipotetyczny treści żądania PUT względem indeksu usługi Azure Search obejmuje opisowych uczelni kandydata lub zapis wraz z identyfikatorem grupy mających uprawnienia do wyświetlania tej zawartości. 

W przykładzie ogólnego używane w przykładowym kodzie w ramach tego przewodnika akcji indeksu może wyglądać następująco:

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

## <a name="issue-a-search-request"></a>Żądanie wyszukiwania

Ze względów bezpieczeństwa przycinanie wartości w polu zabezpieczeń w indeksie są statyczne wartości używanych dla uwzględniając lub wykluczając dokumentów w wynikach wyszukiwania. Na przykład jeśli identyfikator grupy dla dopuszczania "A11B22C33D44-E55F66G77-H88I99JKK", wszystkie dokumenty do indeksu usługi Azure Search o danym identyfikatorem zabezpieczeń usterki są włączone (lub wykluczone) w wynikach wyszukiwania wysyłane z powrotem do żądającego.

Aby filtrować dokumenty zwrócone w wynikach wyszukiwania na podstawie grup użytkowników wysyłania żądania, przejrzyj poniższe kroki.

### <a name="step-1-retrieve-users-group-identifiers"></a>Krok 1: Pobierz identyfikatory grupy użytkownika

Jeśli nie zostały już zapisane grupy użytkowników lub wygasł pamięci podręcznej, [grup](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/directoryobject_getmembergroups) żądania
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

### <a name="step-2-compose-the-search-request"></a>Krok 2: Utworzenie żądania wyszukiwania

Przy założeniu, że masz członkostwa grupy użytkownika, możesz wykonywać żądania wyszukiwania przy użyciu wartości odpowiedniego filtru.

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

Odpowiedź zawiera wyfiltrowanej listy dokumentów, składające się z tymi, które użytkownik ma uprawnienia do wyświetlania. W zależności od tego, jak można utworzyć strony wyników wyszukiwania możesz dołączyć wizualnych, aby odzwierciedlić zestawu wyników filtrowane.

## <a name="conclusion"></a>Podsumowanie

W tym przewodniku przedstawiono techniki do filtrowania dokumentów w wynikach usługi Azure Search przy użyciu usługi AAD logowania przycinanie wyniki dokumentów, które nie są zgodne z filtru na żądanie.

## <a name="see-also"></a>Zobacz także

+ [Kontrola dostępu oparta na tożsamości, za pomocą usługi Azure Search filtrów](search-security-trimming-for-azure-search.md)
+ [Filtry w usłudze Azure Search](search-filters.md)
+ [Dane zabezpieczeń i kontroli dostępu w ramach operacji usługi Azure Search](search-security-overview.md)
