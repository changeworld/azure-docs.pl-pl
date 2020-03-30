---
title: Dlaczego warto aktualizować platformę tożsamości firmy Microsoft (wersja 2.0) | Azure
description: Poznaj różnice między punktem końcowym platformy tożsamości firmy Microsoft (w wersji 2.0) a punktem końcowym usługi Azure Active Directory (Azure AD) w wersji 1.0 i poznaj korzyści wynikające z aktualizacji do wersji 2.0.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: ryanwi
ms.reviewer: saeeda, hirsin, jmprieur, sureshja, jesakowi, lenalepa, kkrishna, negoe
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 67a54a2cd4fa071fd47bcebb9aa53fd11fefd61e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154920"
---
# <a name="why-update-to-microsoft-identity-platform-v20"></a>Dlaczego warto wykonać aktualizację do platformy tożsamości Microsoft (wersja 2.0)?

Podczas tworzenia nowej aplikacji, ważne jest, aby znać różnice między platformą tożsamości firmy Microsoft (w wersji 2.0) i punktu końcowego usługi Azure Active Directory (w wersji 1.0). W tym artykule opisano główne różnice między punktami końcowymi i niektóre istniejące ograniczenia dla platformy tożsamości firmy Microsoft.

> [!NOTE]
> Punkt końcowy platformy tożsamości firmy Microsoft nie obsługuje wszystkich scenariuszy i funkcji usługi Azure AD. Aby ustalić, czy należy używać punktu końcowego platformy tożsamości firmy Microsoft, przeczytaj o [ograniczeniach platformy tożsamości firmy Microsoft](#limitations).

## <a name="who-can-sign-in"></a>Kto może się zalogować

![Kto może logować się za pomocą punktów końcowych w wersji 1.0 i v2.0](media/azure-ad-endpoint-comparison/who-can-signin.svg)

* Punkt końcowy w wersji 1.0 umożliwia tylko konta służbowe i szkolne logowanie się do aplikacji (Azure AD)
* Punkt końcowy platformy tożsamości firmy Microsoft umożliwia konto pracy i szkoły z usługi Azure AD i osobistych kont Microsoft (MSA), takich jak hotmail.com, outlook.com i msn.com, aby zalogować się.
* Oba punkty końcowe akceptują również logowania *[użytkowników-gościa katalogu](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)* usługi Azure AD dla aplikacji skonfigurowanych jako *[pojedyncza dzierżawa](../develop/single-and-multi-tenant-apps.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)* lub dla`https://login.microsoftonline.com/{TenantId_or_Name}`aplikacji z wieloma *dzierżawami* skonfigurowanych do wskazywania punktu końcowego specyficznego dla dzierżawy ( ).

Punkt końcowy platformy tożsamości firmy Microsoft umożliwia pisanie aplikacji, które akceptują logowania z osobistych kont Microsoft oraz kont służbowych i szkolnych. Daje to możliwość pisania aplikacji całkowicie niezależny od konta. Jeśli na przykład aplikacja wywołuje [program Microsoft Graph,](https://graph.microsoft.io)niektóre dodatkowe funkcje i dane będą dostępne dla kont służbowych, takich jak witryny programu SharePoint lub dane katalogu. Ale w przypadku wielu działań, takich jak [czytanie poczty użytkownika,](https://docs.microsoft.com/graph/api/user-list-messages?view=graph-rest-1.0)ten sam kod może uzyskać dostęp do wiadomości e-mail zarówno dla kont osobistych, jak i służbowych i szkolnych.

W przypadku punktu końcowego platformy tożsamości firmy Microsoft można użyć biblioteki uwierzytelniania firmy Microsoft (MSAL), aby uzyskać dostęp do świata konsumentów, instytucji edukacyjnych i korporacyjnych. Punkt końcowy usługi Azure AD w wersji 1.0 akceptuje logowania tylko z kont służbowych i szkolnych.

## <a name="incremental-and-dynamic-consent"></a>Zgoda przyrostowa i dynamiczna

Aplikacje korzystające z punktu końcowego usługi Azure AD w wersji 1.0 są wymagane do określenia z wyprzedzeniem wymaganych uprawnień OAuth 2.0:

![Przykład przedstawiający interfejs użytkownika rejestracji uprawnień](./media/azure-ad-endpoint-comparison/app-reg-permissions.png)

Uprawnienia ustawione bezpośrednio w rejestracji aplikacji są **statyczne**. Podczas statyczne uprawnienia aplikacji zdefiniowane w witrynie Azure portal zachować kod ładne i proste, przedstawia kilka możliwych problemów dla deweloperów:

* Aplikacja musi zażądać wszystkich uprawnień, które kiedykolwiek będzie potrzebować przy pierwszym loguchaniu użytkownika. Może to prowadzić do długiej listy uprawnień, które zniechęcają użytkowników końcowych do zatwierdzania dostępu aplikacji podczas początkowego logowania.

* Aplikacja musi znać wszystkie zasoby, do których kiedykolwiek miałaby dostęp z wyprzedzeniem. Trudno było utworzyć aplikacje, które mogłyby uzyskać dostęp do dowolnej liczby zasobów.

W punkcie końcowym platformy tożsamości firmy Microsoft można zignorować uprawnienia statyczne zdefiniowane w informacjach o rejestracji aplikacji w portalu Azure i zamiast tego żądać uprawnień, co oznacza, że z góry poprosić o minimalny zestaw uprawnień z góry. i rośnie coraz bardziej w czasie, gdy klient korzysta z dodatkowych funkcji aplikacji. Aby to zrobić, można określić zakresy aplikacji potrzebuje w dowolnym momencie, dołączając nowe zakresy w `scope` parametrze podczas żądania tokenu dostępu — bez konieczności wstępnego definiowania ich w informacjach rejestracyjnych aplikacji. Jeśli użytkownik nie wyraził jeszcze zgody na nowe zakresy dodane do żądania, zostanie poproszony o wyrażenie zgody tylko na nowe uprawnienia. Aby dowiedzieć się więcej, zobacz [uprawnienia, zgoda i zakresy](../develop/v2-permissions-and-consent.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

Zezwalanie aplikacji na dynamiczne żądanie `scope` uprawnień za pośrednictwem parametru daje deweloperom pełną kontrolę nad środowiskami użytkownika. Możesz również załadować z wyprzedzeniem swoje środowisko zgody i poprosić o wszystkie uprawnienia w jednym początkowym żądaniu autoryzacji. Jeśli aplikacja wymaga dużej liczby uprawnień, można zbierać te uprawnienia od użytkownika przyrostowo, gdy próbują korzystać z niektórych funkcji aplikacji w czasie.

Zgoda administratora wykonana w imieniu organizacji nadal wymaga uprawnień statycznych zarejestrowanych dla aplikacji, dlatego należy ustawić te uprawnienia dla aplikacji w portalu rejestracji aplikacji, jeśli potrzebujesz administratora do wyrażenia zgody w imieniu całej organizacji. Zmniejsza to cykle wymagane przez administratora organizacji do skonfigurowania aplikacji.

## <a name="scopes-not-resources"></a>Zakresy, a nie zasoby

W przypadku aplikacji korzystających z punktu końcowego w wersji 1.0 aplikacja może zachowywać się jako **zasób**lub odbiorca tokenów. Zasób można zdefiniować liczbę **zakresów** lub **oAuth2Permissions,** który rozumie, umożliwiając aplikacjom klienckim żądać tokenów z tego zasobu dla określonego zestawu zakresów. Należy wziąć pod uwagę interfejs API programu Microsoft Graph jako przykład zasobu:

* Identyfikator zasobu `AppID URI`lub:`https://graph.microsoft.com/`
* Zakresy, `oAuth2Permissions`lub `Directory.Read` `Directory.Write`: , i tak dalej.

Dotyczy to punktu końcowego platformy tożsamości firmy Microsoft. Aplikacja nadal może zachowywać się jak zasób, definiować zakresy i być identyfikowane przez identyfikator URI. Aplikacje klienckie nadal mogą żądać dostępu do tych zakresów. Jednak sposób, w jaki klient żąda tych uprawnień, uległ zmianie.

W przypadku punktu końcowego w wersji 1.0 żądanie autoryzacji OAuth 2.0 do usługi Azure AD mogło wyglądać następująco:

```text
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https://graph.microsoft.com/
...
```

W tym miejscu parametr **zasobu** wskazuje, który zasób aplikacja kliencka żąda autoryzacji. Usługa Azure AD obliczyła uprawnienia wymagane przez aplikację na podstawie konfiguracji statycznej w witrynie Azure portal i odpowiednio wydała tokeny.

W przypadku aplikacji korzystających z punktu końcowego platformy tożsamości firmy Microsoft wygląda następująco:

```text
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https://graph.microsoft.com/directory.read%20https://graph.microsoft.com/directory.write
...
```

W tym miejscu parametr **scope** wskazuje, który zasób i uprawnienia aplikacja żąda autoryzacji. Żądany zasób jest nadal obecny w żądaniu — jest objęty każdą z wartości parametru scope. Przy użyciu parametru scope w ten sposób umożliwia punkt końcowy platformy tożsamości firmy Microsoft być bardziej zgodne ze specyfikacją OAuth 2.0 i jest bardziej zgodne z typowych praktyk branżowych. Umożliwia również aplikacjom wykonywanie [przyrostowej zgody](#incremental-and-dynamic-consent) — tylko żądanie uprawnień, gdy aplikacja wymaga ich, a nie z góry.

## <a name="well-known-scopes"></a>Dobrze znane zakresy

### <a name="offline-access"></a>Dostęp w trybie offline

Aplikacje korzystające z punktu końcowego platformy tożsamości firmy Microsoft mogą `offline_access` wymagać użycia nowego, dobrze znanego uprawnienia dla aplikacji — zakresu. Wszystkie aplikacje będą musiały zażądać tego uprawnienia, jeśli muszą uzyskać dostęp do zasobów w imieniu użytkownika przez dłuższy czas, nawet jeśli użytkownik może nie aktywnie korzystać z aplikacji. Zakres `offline_access` pojawi się użytkownikowi w oknach dialogowych zgody jako **dostęp do danych w dowolnym momencie,** na co użytkownik musi wyrazić zgodę. Żądanie `offline_access` uprawnienia umożliwi aplikacji sieci web do odbierania OAuth 2.0 refresh_tokens z punktu końcowego platformy tożsamości firmy Microsoft. Tokeny odświeżania są długotrwałe i mogą być wymieniane na nowe tokeny dostępu OAuth 2.0 dla dłuższych okresów dostępu.

Jeśli aplikacja nie zażąda `offline_access` zakresu, nie otrzyma tokenów odświeżania. Oznacza to, że po zrealizowaniu kodu autoryzacji w przepływie kodu autoryzacji OAuth 2.0, otrzymasz tylko token dostępu z punktu końcowego. `/token` Ten token dostępu pozostaje ważny przez krótki okres czasu (zazwyczaj jedną godzinę), ale ostatecznie wygaśnie. W tym momencie aplikacja będzie musiał przekierować użytkownika `/authorize` z powrotem do punktu końcowego, aby pobrać nowy kod autoryzacji. Podczas tego przekierowania użytkownik może lub nie musi ponownie wprowadzać swoich poświadczeń lub ponownie konfigurować uprawnienia, w zależności od typu aplikacji.

Aby dowiedzieć się więcej o OAuth `refresh_tokens` `access_tokens`2.0 i zapoznaj się z [odwołaniem do protokołu platformy tożsamości firmy Microsoft](../develop/active-directory-v2-protocols.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

### <a name="openid-profile-and-email"></a>OpenID, profil i poczta e-mail

Historycznie najbardziej podstawowy przepływ logowania OpenID Connect z platformą tożsamości firmy Microsoft zapewniałby wiele informacji o użytkowniku w wynikowym *id_token*. Oświadczenia w id_token mogą zawierać nazwę użytkownika, preferowaną nazwę użytkownika, adres e-mail, identyfikator obiektu i inne.

Informacje, do `openid` których zapewnia dostęp do aplikacji, są teraz ograniczone. Zakres `openid` zezwala tylko aplikacji na logowanie się użytkownika i odbieranie identyfikatora specyficznego dla aplikacji dla użytkownika. Jeśli chcesz uzyskać dane osobowe o użytkowniku w aplikacji, aplikacja musi zażądać dodatkowych uprawnień od użytkownika. Dwa nowe zakresy `profile`i `email` , pozwoli ci zażądać dodatkowych uprawnień.

* Zakres `email` umożliwia aplikacji dostęp do podstawowego adresu e-mail użytkownika za pośrednictwem `email` oświadczenia w id_token, przy założeniu, że użytkownik ma adresowalny adres e-mail.
* Zakres `profile` zapewnia aplikacji dostęp do wszystkich innych podstawowych informacji o użytkowniku, takich jak jego nazwa, preferowana nazwa użytkownika, identyfikator obiektu i tak dalej, w id_token.

Te zakresy umożliwiają kodowanie aplikacji w sposób minimalnego ujawnienia, dzięki czemu można tylko poprosić użytkownika o zestaw informacji, które aplikacja musi wykonać swoje zadanie. Aby uzyskać więcej informacji na temat tych zakresów, zobacz [odwołanie do zakresu platformy tożsamości firmy Microsoft](../develop/v2-permissions-and-consent.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

## <a name="token-claims"></a>Oświadczenia tokenów

Punkt końcowy platformy tożsamości firmy Microsoft wystawia domyślnie mniejszy zestaw oświadczeń w swoich tokenach, aby zachować małe ładunki. Jeśli masz aplikacje i usługi, które mają zależność od określonego oświadczenia w tokenie w wersji 1.0, który nie jest już domyślnie dostarczany w tokenie platformy tożsamości firmy Microsoft, należy rozważyć użycie funkcji [oświadczeń opcjonalnych,](../develop/active-directory-optional-claims.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) aby uwzględnić to oświadczenie.

> [!IMPORTANT]
> Tokeny v1.0 i v2.0 mogą być wydawane zarówno przez punkty końcowe wersji 1.0, jak i v2.0! id_tokens *zawsze* odpowiadać punktowi końcowemu, od których są wymagane, a tokeny dostępu *zawsze* odpowiadają formatowi oczekiwanemu przez interfejs API sieci Web, który klient wywoła przy użyciu tego tokenu.  Więc jeśli aplikacja używa punktu końcowego w wersji 2.0, aby uzyskać token do wywołania programu Microsoft Graph, który oczekuje tokenów dostępu w formacie 1.0, aplikacja otrzyma token w formacie v1.0.  

## <a name="limitations"></a>Ograniczenia

Istnieje kilka ograniczeń, o których należy pamiętać podczas korzystania z platformy tożsamości firmy Microsoft.

Podczas tworzenia aplikacji, które integrują się z platformą tożsamości firmy Microsoft, należy zdecydować, czy punkt końcowy platformy tożsamości firmy Microsoft i protokoły uwierzytelniania spełniają Twoje potrzeby. Punkt końcowy i platforma w wersji 1.0 są nadal w pełni obsługiwane i pod pewnymi względami są bardziej bogate w funkcje niż platforma tożsamości firmy Microsoft. Jednak platforma tożsamości firmy Microsoft [wprowadza znaczące korzyści](azure-ad-endpoint-comparison.md) dla deweloperów.

Oto uproszczone zalecenie dla deweloperów:

* Jeśli chcesz lub chcesz obsługiwać osobiste konta Microsoft w aplikacji lub piszesz nową aplikację, użyj platformy tożsamości firmy Microsoft. Ale zanim to zrobisz, upewnij się, że rozumiesz ograniczenia omówione w tym artykule.
* Jeśli przeprowadzasz migrację lub aktualizujesz aplikację, która opiera się na SAML, nie możesz używać platformy tożsamości firmy Microsoft. Zamiast tego zapoznaj się z [przewodnikiem usługi Azure AD w wersji 1.0](v1-overview.md).

Punkt końcowy platformy tożsamości firmy Microsoft ewoluuje, aby wyeliminować ograniczenia wymienione w tym miejscu, dzięki czemu będziesz musiał używać punktu końcowego platformy tożsamości firmy Microsoft. W międzyczasie użyj tego artykułu, aby ustalić, czy punkt końcowy platformy tożsamości firmy Microsoft jest odpowiedni dla Ciebie. Będziemy nadal aktualizować ten artykuł, aby odzwierciedlić bieżący stan punktu końcowego platformy tożsamości firmy Microsoft. Sprawdź ponownie, aby ponownie ocenić wymagania w zależności od możliwości platformy tożsamości firmy Microsoft.

### <a name="restrictions-on-app-registrations"></a>Ograniczenia dotyczące rejestracji aplikacji

Dla każdej aplikacji, którą chcesz zintegrować z punktem końcowym platformy tożsamości firmy Microsoft, można utworzyć rejestrację aplikacji w nowym środowiska [ **rejestracji aplikacji** ](https://aka.ms/appregistrations) w witrynie Azure portal. Istniejące aplikacje konta Microsoft nie są zgodne z portalem, ale wszystkie aplikacje usługi Azure AD są, niezależnie od tego, gdzie i kiedy zostały zarejestrowane.

Rejestracje aplikacji, które obsługują konta służbowe i szkolne oraz konta osobiste, mają następujące zastrzeżenia:

* Tylko dwa wpisy tajne aplikacji są dozwolone na identyfikator aplikacji.
* Aplikacja, która nie została zarejestrowana w dzierżawie, może być zarządzana tylko przez konto, które ją zarejestrowało. Nie można go udostępnić innym deweloperom. Dotyczy to większości aplikacji, które zostały zarejestrowane przy użyciu osobistego konta Microsoft w portalu rejestracji aplikacji. Jeśli chcesz udostępnić rejestrację aplikacji wielu deweloperom, zarejestruj aplikację w dzierżawie przy użyciu nowej sekcji **rejestracji aplikacji** w witrynie Azure portal.
* Istnieje kilka ograniczeń dotyczących formatu adresu URL przekierowania, który jest dozwolony. Aby uzyskać więcej informacji na temat adresu URL przekierowania, zobacz następną sekcję.

### <a name="restrictions-on-redirect-urls"></a>Ograniczenia dotyczące adresów URL przekierowań

Aplikacje zarejestrowane na platformie tożsamości firmy Microsoft są ograniczone do ograniczonego zestawu wartości adresów URL przekierowania. Adres URL przekierowania dla aplikacji i `https`usług sieci Web musi zaczynać się od schematu, a wszystkie wartości adresów URL przekierowania muszą współużytkować jedną domenę DNS.  System rejestracji porównuje całą nazwę DNS istniejącego adresu URL przekierowania z nazwą DNS dodanego adresu URL przekierowania. `http://localhost`jest również obsługiwany jako adres URL przekierowania.  

Żądanie dodania nazwy DNS zakończy się niepowodzeniem, jeśli będzie spełniony jeden z następujących warunków:  

* Cała nazwa DNS nowego adresu URL przekierowania nie jest zgodna z nazwą DNS istniejącego adresu URL przekierowania.
* Cała nazwa DNS nowego adresu URL przekierowania nie jest poddomeną istniejącego adresu URL przekierowania.

#### <a name="example-1"></a>Przykład 1

Jeśli aplikacja ma adres `https://login.contoso.com`URL przekierowania , możesz dodać adres URL przekierowania, w którym nazwa DNS jest dokładnie zgodna, jak pokazano w poniższym przykładzie:

`https://login.contoso.com/new`

Można też odwołać się do poddomeny DNS login.contoso.com, jak pokazano w poniższym przykładzie:

`https://new.login.contoso.com`

#### <a name="example-2"></a>Przykład 2

Jeśli chcesz mieć aplikację, `login-east.contoso.com` która `login-west.contoso.com` ma i jako adresy URL przekierowania, musisz dodać te adresy URL przekierowania w następującej kolejności:

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

Możesz dodać dwa ostatnie, ponieważ są to poddomeny pierwszego adresu URL przekierowania, contoso.com.

Możesz mieć tylko 20 adresów URL odpowiedzi dla określonej aplikacji — ten limit ma zastosowanie do wszystkich typów aplikacji obsługiwanych przez rejestrację (aplikacja jednostronicowa (SPA), klient macierzysty, aplikacja sieci web i usługa).  

Aby dowiedzieć się, jak zarejestrować aplikację do użytku na platformie tożsamości firmy Microsoft, zobacz [Rejestrowanie aplikacji przy użyciu nowego środowiska rejestracji aplikacji](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

### <a name="restrictions-on-libraries-and-sdks"></a>Ograniczenia dotyczące bibliotek i SDK

Obecnie obsługa bibliotek dla punktu końcowego platformy tożsamości firmy Microsoft jest ograniczona. Jeśli chcesz użyć punktu końcowego platformy tożsamości firmy Microsoft w aplikacji produkcyjnej, masz następujące opcje:

* Jeśli budujesz aplikację sieci web, można bezpiecznie używać ogólnie dostępnego oprogramowania pośredniczącego po stronie serwera do logowania się i sprawdzania poprawności tokenu. Należą do nich oprogramowanie pośredniczące OWIN OpenID Connect dla ASP.NET i node.js Passport plug-in. Przykłady kodu korzystające z oprogramowania pośredniczącego firmy Microsoft można znaleźć w sekcji [Wprowadzenie do platformy tożsamości firmy Microsoft.](../develop/v2-overview.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json#getting-started)
* Jeśli budujesz aplikację komputerową lub mobilną, możesz użyć jednej z bibliotek uwierzytelniania firmy Microsoft (MSAL). Biblioteki te są ogólnie dostępne lub w wersji zapoznawczej obsługiwanej przez proces produkcyjny, dzięki czemu można bezpiecznie używać ich w aplikacjach produkcyjnych. Więcej informacji na temat warunków wersji zapoznawczej i dostępnych bibliotek w [bibliotekach uwierzytelniania](../develop/reference-v2-libraries.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).
* W przypadku platform nieobjętych bibliotekami firmy Microsoft można zintegrować z punktem końcowym platformy tożsamości firmy Microsoft, wysyłając bezpośrednio i odbierając komunikaty protokołu w kodzie aplikacji. Protokoły OpenID Connect i OAuth [są jawnie udokumentowane,](../develop/active-directory-v2-protocols.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) aby ułatwić taką integrację.
* Na koniec można użyć open-source OpenID Connect i OAuth bibliotek do integracji z punktem końcowym platformy tożsamości firmy Microsoft. Punkt końcowy platformy tożsamości firmy Microsoft powinien być zgodny z wieloma bibliotekami protokołów typu open source bez zmian. Dostępność tego rodzaju bibliotek zależy od języka i platformy. Witryny [OpenID Connect](https://openid.net/connect/) i [OAuth 2.0](https://oauth.net/2/) prowadzą listę popularnych implementacji. Aby uzyskać więcej informacji, zobacz [Platformę tożsamości firmy Microsoft i biblioteki uwierzytelniania](../develop/reference-v2-libraries.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)oraz listę bibliotek klienckich typu open source i przykładów, które zostały przetestowane przy użyciu punktu końcowego platformy tożsamości firmy Microsoft.
* W przypadku `.well-known` odwołania punktem końcowym dla wspólnego `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`punktu końcowego platformy tożsamości firmy Microsoft jest . Zamień `common` na identyfikator dzierżawy, aby uzyskać dane specyficzne dla dzierżawy.  

### <a name="protocol-changes"></a>Zmiany protokołu

Punkt końcowy platformy tożsamości firmy Microsoft nie obsługuje SAML lub Federacji WS; obsługuje tylko OpenID Connect i OAuth 2.0.  Istotne zmiany w protokołach OAuth 2.0 z punktu końcowego w wersji 1.0 to: 

* Oświadczenie `email` jest zwracane, jeśli skonfigurowano opcjonalne oświadczenie **lub** w żądaniu określono adres scope=email. 
* Parametr `scope` jest teraz obsługiwany zamiast `resource` parametru.  
* Wiele odpowiedzi zostały zmodyfikowane, aby uczynić je bardziej zgodne ze specyfikacją OAuth `expires_in` 2.0, na przykład poprawnie zwracany jako int zamiast ciągu.  

Aby lepiej zrozumieć zakres funkcji protokołu obsługiwanych w punkcie końcowym platformy tożsamości firmy Microsoft, zobacz [Odwołanie do protokołu OpenID Connect i OAuth 2.0](../develop/active-directory-v2-protocols.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

#### <a name="saml-restrictions"></a>Ograniczenia SAML

Jeśli w aplikacjach systemu Windows była używana biblioteka ADAL (Active Directory Authentication Library), być może korzystanie z uwierzytelniania zintegrowanego systemu Windows, które korzysta z potwierdzenia Języka znaczników oświadczeń zabezpieczeń (SAML). Dzięki tej dotacji użytkownicy federowanych dzierżaw usługi Azure AD mogą dyskretnie uwierzytelniać się za pomocą lokalnego wystąpienia usługi Active Directory bez wprowadzania poświadczeń. Grant potwierdzenia SAML nie jest obsługiwany w punkcie końcowym platformy tożsamości firmy Microsoft.
