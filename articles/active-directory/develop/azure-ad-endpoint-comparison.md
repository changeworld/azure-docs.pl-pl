---
title: Dlaczego warto aktualizować oprogramowanie Microsoft Identity platform (v 2.0) | Azure
description: Poznaj różnice między punktem końcowym programu Microsoft Identity platform (v 2.0) i punktem końcowym Azure Active Directory (Azure AD) v 1.0, aby poznać zalety aktualizacji do wersji 2.0.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: ryanwi
ms.reviewer: saeeda, hirsin, jmprieur, sureshja, jesakowi, lenalepa, kkrishna, negoe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 882b4a8bfe951212d2d1fe081d16b25641dfa4d4
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74843890"
---
# <a name="why-update-to-microsoft-identity-platform-v20"></a>Dlaczego warto aktualizować oprogramowanie Microsoft Identity platform (v 2.0)?

Podczas tworzenia nowej aplikacji ważne jest, aby znać różnice między punktami końcowymi Microsoft Identity platform (v 2.0) i Azure Active Directory (v 1.0). W tym artykule opisano główne różnice między punktami końcowymi i niektóre istniejące ograniczenia dotyczące platformy tożsamości firmy Microsoft.

> [!NOTE]
> Punkt końcowy platformy tożsamości firmy Microsoft nie obsługuje wszystkich scenariuszy i funkcji usługi Azure AD. Aby określić, czy należy używać punktu końcowego platformy tożsamości firmy Microsoft, przeczytaj artykuł [ograniczenia dotyczące platformy tożsamości firmy Microsoft](#limitations).

## <a name="who-can-sign-in"></a>Kto może się zalogować

![Kto może logować się za pomocą punktów końcowych 1.0 i v 2.0](media/azure-ad-endpoint-comparison/who-can-sign-in.svg)

* Punkt końcowy v 1.0 zezwala tylko na konta służbowe do logowania się do aplikacji (Azure AD)
* Punkt końcowy platformy tożsamości firmy Microsoft umożliwia logowanie się przy użyciu kont służbowych z usługi Azure AD i osobistych kont Microsoft (MSA), takich jak hotmail.com, outlook.com i msn.com.
* Oba punkty końcowe również akceptują logowania *[użytkowników-Gości](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)* w katalogu usługi Azure AD dla aplikacji skonfigurowanych jako *[pojedyncze dzierżawy](single-and-multi-tenant-apps.md)* lub dla aplikacji *wielodostępnych* skonfigurowanych tak, aby wskazywały punkt końcowy specyficzny dla dzierżawy (`https://login.microsoftonline.com/{TenantId_or_Name}`).

Punkt końcowy platformy tożsamości firmy Microsoft umożliwia pisanie aplikacji akceptujących logowania z osobistych kont Microsoft oraz kont służbowych. Dzięki temu można napisać aplikację całkowicie Account-niezależny od. Na przykład jeśli aplikacja wywołuje [Microsoft Graph](https://graph.microsoft.io), niektóre dodatkowe funkcje i dane będą dostępne dla kont służbowych, takich jak witryny programu SharePoint lub dane katalogu. Jednak w przypadku wielu akcji, takich jak [odczytywanie wiadomości e-mail](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_messages), ten sam kod może uzyskać dostęp do poczty e-mail zarówno dla kont osobistych, jak i służbowych.

W przypadku punktu końcowego platformy tożsamości firmy Microsoft można korzystać z biblioteki Microsoft Authentication Library (MSAL) w celu uzyskania dostępu do przedsiębiorstw, programów edukacyjnych i korporacyjnych. Punkt końcowy usługi Azure AD v 1.0 akceptuje logowania tylko z kont służbowych.

## <a name="incremental-and-dynamic-consent"></a>Poprzednia i dynamiczna zgoda

Aplikacje korzystające z punktu końcowego usługi Azure AD v 1.0 są wymagane do wcześniejszego określenia wymaganych uprawnień uwierzytelniania OAuth 2,0 na przykład:

![Przykład przedstawiający interfejs użytkownika rejestracji uprawnień](./media/azure-ad-endpoint-comparison/app_reg_permissions.png)

Uprawnienia ustawione bezpośrednio w rejestracji aplikacji są **statyczne**. Podczas gdy uprawnienia statyczne aplikacji zdefiniowanej w Azure Portal zachować kod całkiem i prostoty, przedstawiamy pewne możliwe problemy dla deweloperów:

* Aplikacja musi zażądać wszystkich wymaganych uprawnień przy pierwszym logowaniu użytkownika. Może to prowadzić do długiej listy uprawnień, które nie odradzają użytkownikom końcowym zatwierdzania dostępu do aplikacji podczas początkowego logowania.

* Aplikacja musi znać wszystkie zasoby, do których kiedykolwiek wcześniej uzyskują dostęp. Tworzenie aplikacji, które mogą uzyskać dostęp do dowolnej liczby zasobów, było trudne.

Za pomocą punktu końcowego platformy tożsamości firmy Microsoft można zignorować uprawnienia statyczne zdefiniowane w informacjach rejestracyjnych aplikacji w Azure Portal i zażądać uprawnień przyrostowo, co oznacza, że z góry nie jest wyświetlany minimalny zestaw uprawnień. coraz więcej w czasie, gdy klient korzysta z dodatkowych funkcji aplikacji. W tym celu można określić zakresy wymagane przez aplikację w dowolnym momencie, dołączając nowe zakresy w parametrze `scope` podczas żądania tokenu dostępu — bez konieczności wstępnego definiowania ich w informacjach o rejestracji aplikacji. Jeśli użytkownik nie wyraził zgody na nowe zakresy dodane do żądania, zostanie wyświetlony monit o zgodę tylko na nowe uprawnienia. Aby dowiedzieć się więcej, zobacz [uprawnienia, wyrażanie zgody i zakresy](v2-permissions-and-consent.md).

Umożliwienie aplikacji dynamicznego żądania uprawnień za pośrednictwem parametru `scope` zapewnia deweloperom pełną kontrolę nad doświadczeniem użytkownika. Możesz również załadować swoje środowisko zgody i poprosić o wszystkie uprawnienia w jednym początkowym żądaniu autoryzacji. Jeśli aplikacja wymaga dużej liczby uprawnień, można zebrać te uprawnienia od użytkownika przyrostowo, ponieważ próbują użyć pewnych funkcji aplikacji w czasie.

Zgoda administratora w imieniu organizacji nadal wymaga uprawnień statycznych zarejestrowanych dla aplikacji, dlatego należy ustawić te uprawnienia dla aplikacji w portalu rejestracji aplikacji, jeśli potrzebujesz uprawnień administratora, aby wyrazić zgodę w imieniu całej organizacji. Pozwala to zmniejszyć liczbę cykli wymaganych przez administratora organizacji w celu skonfigurowania aplikacji.

## <a name="scopes-not-resources"></a>Zakresy, a nie zasoby

W przypadku aplikacji korzystających z punktu końcowego v 1.0 Aplikacja może zachowywać się jako **zasób**lub odbiorca tokenów. Zasób może definiować wiele **zakresów** lub **oAuth2Permissions** , które rozumie, dzięki czemu aplikacje klienckie mogą żądać tokenów z tego zasobu dla określonego zestawu zakresów. Rozważmy Microsoft Graph API jako przykładu zasobu:

* Identyfikator zasobu lub `AppID URI`: `https://graph.microsoft.com/`
* Zakresy lub `oAuth2Permissions`: `Directory.Read`, `Directory.Write`i tak dalej.

Ta wartość dotyczy punktu końcowego platformy tożsamości firmy Microsoft. Aplikacja może nadal działać jako zasób, definiować zakresy i być identyfikowane za pomocą identyfikatora URI. Aplikacje klienckie nadal mogą żądać dostępu do tych zakresów. Jednak sposób, w jaki klient żąda tych uprawnień, został zmieniony.

W przypadku punktu końcowego v 1.0 żądanie autoryzacji uwierzytelniania OAuth 2,0 do usługi Azure AD mogło wyglądać następująco:

```text
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https://graph.windows.net/
...
```

W tym miejscu parametr **zasobu** wskazuje, który zasób aplikacja kliencka żąda autoryzacji. Usługa Azure AD oblicza uprawnienia wymagane przez aplikację na podstawie konfiguracji statycznej w Azure Portal i wystawionych tokenów.

W przypadku aplikacji korzystających z punktu końcowego platformy tożsamości firmy Microsoft to samo żądanie autoryzacji OAuth 2,0 będzie wyglądać następująco:

```text
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https://graph.windows.net/directory.read%20https://graph.windows.net/directory.write
...
```

W tym miejscu parametr **SCOPE** wskazuje, który zasób i uprawnienia aplikacja żąda autoryzacji. Żądany zasób nadal występuje w żądaniu — obejmuje on każdą z wartości parametru scope. Użycie parametru scope w ten sposób umożliwia określenie, że punkt końcowy platformy tożsamości firmy Microsoft będzie bardziej zgodny ze specyfikacją OAuth 2,0 i bardziej ścisłe dostosowanie się do typowych praktyk branżowych. Umożliwia także aplikacjom przeprowadzenie [przyrostowej zgody](#incremental-and-dynamic-consent) na żądania tylko wtedy, gdy aplikacja wymaga ich, a nie do przodu.

## <a name="well-known-scopes"></a>Dobrze znane zakresy

### <a name="offline-access"></a>Dostęp w trybie offline

Aplikacje korzystające z punktu końcowego platformy tożsamości firmy Microsoft mogą wymagać użycia nowego dobrze znanego uprawnienia dla aplikacji — zakresu `offline_access`. Wszystkie aplikacje będą musieli zażądać tego uprawnienia, jeśli potrzebują dostępu do zasobów w imieniu użytkownika przez długi czas, nawet jeśli użytkownik nie może aktywnie korzystać z aplikacji. Zakres `offline_access` będzie widoczny dla użytkownika w oknach dialogowych wyrażania zgody na **dostęp do danych w dowolnym czasie**, który użytkownik musi wyrazić zgodę. Żądanie uprawnień `offline_access` umożliwi aplikacji sieci Web otrzymywanie refresh_tokens OAuth 2,0 z punktu końcowego platformy tożsamości firmy Microsoft. Tokeny odświeżania są długotrwałe i mogą być wymieniane z nowymi tokenami dostępu OAuth 2,0 dla rozszerzonych okresów dostępu.

Jeśli aplikacja nie zażąda zakresu `offline_access`, nie otrzyma tokenów odświeżania. Oznacza to, że po zrealizowaniu kodu autoryzacji w przepływie kodu autoryzacji OAuth 2,0 otrzymasz tylko token dostępu z punktu końcowego `/token`. Ten token dostępu pozostaje ważny przez krótki czas (zwykle godzinę), ale wkrótce wygaśnie. W tym momencie aplikacja będzie musiała przekierować użytkownika z powrotem do punktu końcowego `/authorize`, aby pobrać nowy kod autoryzacji. W trakcie tego przekierowania użytkownik może lub nie musi wprowadzać ponownie poświadczeń lub zgodzić się na uprawnienia, w zależności od typu aplikacji.

Aby dowiedzieć się więcej na temat uwierzytelniania OAuth 2,0, `refresh_tokens`i `access_tokens`, zapoznaj się z informacjami dotyczącymi [protokołu Microsoft Identity platform](active-directory-v2-protocols.md).

### <a name="openid-profile-and-email"></a>OpenID Connect, profile i wiadomości e-mail

Historycznie, najbardziej podstawowy przepływ logowania za pomocą programu OpenID Connect z platformą tożsamości firmy Microsoft zapewnia wiele informacji o użytkowniku w wyniku *id_token*. Oświadczenia w id_token mogą zawierać nazwę użytkownika, preferowaną nazwę użytkownika, adres e-mail, identyfikator obiektu i wiele innych.

Informacje, do których ten zakres `openid` zapewnia dostęp do aplikacji, są teraz ograniczone. Zakres `openid` będzie zezwalać aplikacji na logowanie użytkownika i odbieranie identyfikatora specyficznego dla aplikacji dla użytkownika. Jeśli chcesz uzyskać dane osobowe dotyczące użytkownika w aplikacji, aplikacja musi zażądać dodatkowych uprawnień od użytkownika. Dwa nowe zakresy, `email` i `profile`, umożliwią zażądanie dodatkowych uprawnień.

* Zakres `email` umożliwia aplikacji dostęp do podstawowego adresu e-mail użytkownika za pośrednictwem `email`ego żądania w id_token, przy założeniu, że użytkownik ma adres e-mail z adresami.
* Zakres `profile` zapewnia aplikacji dostęp do wszystkich innych podstawowych informacji o użytkowniku, takich jak ich nazwy, preferowana nazwa użytkownika, identyfikator obiektu itd., w id_token.

Te zakresy umożliwiają zakodowanie aplikacji w sposób minimalny, dzięki czemu użytkownik może uzyskać dostęp tylko do zestawu informacji wymaganych przez aplikację. Aby uzyskać więcej informacji na temat tych zakresów, zobacz [Dokumentacja zakresu platformy tożsamości firmy Microsoft](v2-permissions-and-consent.md).

## <a name="token-claims"></a>Oświadczenia tokenu

Punkt końcowy platformy tożsamości firmy Microsoft domyślnie wystawia mniejszą liczbę oświadczeń w tokenach, aby zachować małe ładunki. Jeśli masz aplikacje i usługi, które mają zależność od konkretnego oświadczenia w tokenie w wersji 1.0, która nie jest już świadczona domyślnie w tokenie platformy tożsamości firmy Microsoft, rozważ użycie funkcji [oświadczeń opcjonalnych](active-directory-optional-claims.md) w celu uwzględnienia tego oświadczenia.

> [!IMPORTANT]
> tokeny v 1.0 i v 2.0 mogą być wystawiane przez punkty końcowe w wersji 1.0 i 2.0. id_tokens *zawsze* pasować do punktu końcowego, z którego żądają, a tokeny dostępu są *zawsze* zgodne z formatem OCZEKIWANYM przez internetowy interfejs API, którego klient będzie wywoływał przy użyciu tego tokenu.  Dlatego jeśli aplikacja używa punktu końcowego v 2.0 do uzyskania tokenu wywołującego Microsoft Graph, który oczekuje tokenów dostępu do formatu v 1.0, aplikacja otrzyma token w formacie v 1.0.  

## <a name="limitations"></a>Ograniczenia

Istnieje kilka ograniczeń, które należy wziąć pod uwagę podczas korzystania z platformy tożsamości firmy Microsoft.

Podczas kompilowania aplikacji, które integrują się z platformą tożsamości firmy Microsoft, należy zdecydować, czy punkt końcowy platformy tożsamości firmy Microsoft i protokoły uwierzytelniania zaspokajają Twoje potrzeby. Punkt końcowy i platforma v 1.0 są nadal w pełni obsługiwane i, w niektórych przypadkach, są bardziej bogate w funkcje platformy tożsamości firmy Microsoft. Jednak platforma tożsamości firmy Microsoft [wprowadza znaczące korzyści](azure-ad-endpoint-comparison.md) dla deweloperów.

Poniżej przedstawiono uproszczone rekomendacje dla deweloperów:

* Jeśli potrzebujesz lub potrzebujesz obsłużyć osobiste konta Microsoft w aplikacji lub napisać nową aplikację, użyj platformy tożsamości firmy Microsoft. Jednak przed rozpoczęciem upewnij się, że rozumiesz ograniczenia omówione w tym artykule.
* Jeśli migrujesz lub aktualizujesz aplikację, która opiera się na protokole SAML, nie możesz używać platformy tożsamości firmy Microsoft. Zamiast tego zapoznaj się z [przewodnikiem dotyczącym usługi Azure AD v 1.0](v1-overview.md).

Punkt końcowy platformy tożsamości firmy Microsoft zostanie rozmieszczony, aby wyeliminować ograniczenia wymienione w tym miejscu, dzięki czemu będziesz musiał tylko korzystać z punktu końcowego platformy tożsamości firmy Microsoft. W międzyczasie Skorzystaj z tego artykułu, aby określić, czy punkt końcowy platformy tożsamości firmy Microsoft jest odpowiedni dla Ciebie. Będziemy nadal aktualizować ten artykuł, aby odzwierciedlał bieżący stan punktu końcowego platformy tożsamości firmy Microsoft. Zapoznaj się z powrotem, aby ponownie oszacować wymagania dotyczące możliwości platformy tożsamości firmy Microsoft.

### <a name="restrictions-on-app-registrations"></a>Ograniczenia dotyczące rejestracji aplikacji

Dla każdej aplikacji, którą chcesz zintegrować z punktem końcowym platformy tożsamości firmy Microsoft, możesz utworzyć rejestrację aplikacji w nowym [środowisku **Rejestracje aplikacji** ](https://aka.ms/appregistrations) w Azure Portal. Istniejące konto Microsoft aplikacje nie są zgodne z portalem, ale wszystkie aplikacje usługi Azure AD są, niezależnie od lokalizacji lub czasu ich rejestracji.

Rejestracje aplikacji, które obsługują konta służbowe i konta osobiste, mają następujące zastrzeżenia:

* Dla identyfikatora aplikacji dozwolone są tylko dwa wpisy tajne aplikacji.
* Aplikacja, która nie została zarejestrowana w dzierżawie, może być zarządzana tylko przez konto, które je zarejestrowało. Nie może być współużytkowana z innymi deweloperami. W tym przypadku większość aplikacji, które zostały zarejestrowane przy użyciu osobistego konto Microsoft w portalu rejestracji aplikacji. Jeśli chcesz podzielić swoją rejestrację aplikacji na wielu deweloperów, zarejestruj aplikację w dzierżawie przy użyciu nowej sekcji **rejestracje aplikacji** w Azure Portal.
* Istnieje kilka ograniczeń dotyczących formatu dozwolonego adresu URL przekierowania. Aby uzyskać więcej informacji o adresie URL przekierowania, zobacz następną sekcję.

### <a name="restrictions-on-redirect-urls"></a>Ograniczenia dotyczące adresów URL przekierowania

Aplikacje zarejestrowane dla platformy tożsamości firmy Microsoft są ograniczone do ograniczonego zestawu wartości adresu URL przekierowania. Adres URL przekierowania dla usługi Web Apps i usług musi rozpoczynać się od schematu `https`, a wszystkie wartości adresu URL przekierowania muszą współdzielić pojedynczą domenę DNS.  System rejestracji porównuje pełną nazwę DNS istniejącego adresu URL przekierowania z nazwą DNS adresu URL przekierowania, który jest dodawany. `http://localhost` jest również obsługiwany jako adres URL przekierowania.  

Żądanie dodania nazwy DNS zakończy się niepowodzeniem, jeśli będzie spełniony jeden z następujących warunków:  

* Cała nazwa DNS nowego adresu URL przekierowania nie jest zgodna z nazwą DNS istniejącego adresu URL przekierowania.
* Cała nazwa DNS nowego adresu URL przekierowania nie jest poddomeną istniejącego adresu URL przekierowania.

#### <a name="example-1"></a>Przykład 1

Jeśli aplikacja ma adres URL przekierowania `https://login.contoso.com`, można dodać adres URL przekierowania, gdzie nazwa DNS pasuje dokładnie, jak pokazano w następującym przykładzie:

`https://login.contoso.com/new`

Lub można odwołać się do domeny podrzędnej DNS login.contoso.com, jak pokazano w następującym przykładzie:

`https://new.login.contoso.com`

#### <a name="example-2"></a>Przykład 2

Jeśli chcesz mieć aplikację, która ma `login-east.contoso.com` i `login-west.contoso.com` jako adresy URL przekierowania, należy dodać te adresy URL przekierowania w następującej kolejności:

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

Można dodać dwa ostatnie z nich, ponieważ są poddomenami pierwszego adresu URL przekierowania, contoso.com.

Można mieć tylko 20 adresów URL odpowiedzi dla określonej aplikacji — ten limit dotyczy wszystkich typów aplikacji obsługiwanych przez rejestrację (jednostronicowej aplikacji (SPA), natywnego klienta, aplikacji sieci Web i usługi).  

Aby dowiedzieć się, jak zarejestrować aplikację do użycia z platformą tożsamości firmy Microsoft, zobacz [Rejestrowanie aplikacji przy użyciu nowego środowiska rejestracje aplikacji](quickstart-register-app.md).

### <a name="restrictions-on-libraries-and-sdks"></a>Ograniczenia dotyczące bibliotek i zestawów SDK

Obecnie obsługa biblioteki dla punktu końcowego platformy tożsamości firmy Microsoft jest ograniczona. Jeśli chcesz używać punktu końcowego platformy tożsamości firmy Microsoft w aplikacji produkcyjnej, możesz skorzystać z następujących opcji:

* Jeśli tworzysz aplikację sieci Web, możesz bezpiecznie używać powszechnie dostępnego oprogramowania pośredniczącego po stronie serwera do logowania się i weryfikacji tokenu. Obejmują one oprogramowanie OWIN OpenID Connect Connect Middle dla ASP.NET oraz wtyczkę typu Node. js usługi Passport. Przykłady kodu używające oprogramowania pośredniczącego firmy Microsoft można znaleźć w sekcji [wprowadzenie do platformy tożsamości firmy Microsoft](v2-overview.md#getting-started) .
* W przypadku kompilowania aplikacji klasycznej lub przenośnej można użyć jednej z bibliotek uwierzytelniania firmy Microsoft (MSAL). Te biblioteki są ogólnie dostępne lub w wersji zapoznawczej obsługiwanej w środowisku produkcyjnym, dzięki czemu można bezpiecznie używać ich w aplikacjach produkcyjnych. Więcej informacji na temat warunków wersji zapoznawczej i dostępnych bibliotek można znaleźć w [dokumentacji dotyczącej bibliotek uwierzytelniania](reference-v2-libraries.md).
* W przypadku platform, które nie są objęte bibliotekami firmy Microsoft, można zintegrować z punktem końcowym platformy tożsamości firmy Microsoft przez bezpośrednie wysyłanie i otrzymywanie komunikatów protokołu w kodzie aplikacji. Protokoły OpenID Connect Connect i OAuth [zostały jawnie udokumentowane](active-directory-v2-protocols.md) w celu ułatwienia takiej integracji.
* Na koniec możesz zintegrować z punktem końcowym platformy tożsamości firmy Microsoft, korzystając z bibliotek OpenID Connect Connect i OAuth programu Open Source. Punkt końcowy platformy tożsamości firmy Microsoft powinien być zgodny z wieloma bibliotekami protokołów open source bez zmian. Dostępność tych rodzajów bibliotek zależy od języka i platformy. Witryny sieci Web [OpenID Connect Connect](https://openid.net/connect/) i [OAuth 2,0](https://oauth.net/2/) obsługują listę popularnych implementacji. Aby uzyskać więcej informacji, zobacz [biblioteki Microsoft Identity platform i Authentication](reference-v2-libraries.md)librarys oraz listę bibliotek i przykładów klienta typu open source, które zostały przetestowane za pomocą punktu końcowego platformy tożsamości firmy Microsoft.
* W przypadku odwołania do `.well-known` punktu końcowego dla wspólnego punktu końcowego platformy tożsamości firmy Microsoft jest `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`. Zastąp `common` IDENTYFIKATORem dzierżawy, aby uzyskać dane specyficzne dla dzierżawy.  

### <a name="protocol-changes"></a>Zmiany protokołu

Punkt końcowy platformy tożsamości firmy Microsoft nie obsługuje protokołu SAML ani usługi WS-Federation. obsługuje tylko OpenID Connect Connect i OAuth 2,0.  Istotne zmiany w protokołach OAuth 2,0 z punktu końcowego v 1.0 są następujące: 

* W przypadku skonfigurowania opcjonalnego **lub** zakresu = adresu e-mail w żądaniu określono żądanie `email`. 
* Parametr `scope` jest teraz obsługiwany zamiast parametru `resource`.  
* Wiele odpowiedzi zostało zmodyfikowanych tak, aby były bardziej zgodne ze specyfikacją OAuth 2,0, na przykład prawidłowo zwracając `expires_in` jako int zamiast ciągu.  

Aby lepiej zrozumieć zakres funkcji protokołu obsługiwanych w punkcie końcowym platformy tożsamości firmy Microsoft, zobacz [informacje dotyczące protokołu OpenID Connect Connect i OAuth 2,0](active-directory-v2-protocols.md).

#### <a name="saml-restrictions"></a>Ograniczenia SAML

Jeśli użyto Active Directory Authentication Library (ADAL) w aplikacjach systemu Windows, być może korzystasz z uwierzytelniania zintegrowanego systemu Windows, które korzysta z metody udzielenia potwierdzenia SAML (SAML). Dzięki temu przydzieleniu użytkownicy federacyjnych dzierżawców usługi Azure AD mogą w trybie dyskretnym uwierzytelniać się przy użyciu lokalnego wystąpienia Active Directory bez wprowadzania poświadczeń. Przyznanie potwierdzenia SAML nie jest obsługiwane w punkcie końcowym platformy tożsamości firmy Microsoft.
