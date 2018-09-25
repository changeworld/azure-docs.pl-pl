---
title: Porównywanie punktu końcowego v2.0 usługi Azure AD z punktem końcowym v1.0 | Dokumentacja firmy Microsoft
description: Znasz różnice między punktu końcowego v2.0 usługi Azure AD i punktu końcowego w wersji 1.0
services: active-directory
documentationcenter: ''
author: andretms
manager: mtillman
editor: ''
ms.assetid: 5060da46-b091-4e25-9fa8-af4ae4359b6c
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2018
ms.author: andret
ms.reviewer: hirsin, celested
ms.custom: aaddev
ms.openlocfilehash: 02c7edc84d2ac3a91c33d8f266d022db5cd5cb40
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46948962"
---
# <a name="comparing-the-azure-ad-v20-endpoint-with-v10-endpoint"></a>Porównywanie punktu końcowego v2.0 usługi Azure AD przy użyciu punktu końcowego w wersji 1.0

Podczas tworzenia nowej aplikacji, należy znać różnice między punktami końcowymi w wersji 1.0 i 2.0. Poniżej przedstawiono główne różnice, a także niektóre istniejące ograniczenia dla punktu końcowego v2.0.

> [!NOTE]
> Nie wszystkie scenariusze usługi Azure AD i funkcje są obsługiwane przez punkt końcowy w wersji 2.0. Aby ustalić, należy użyć punktu końcowego v2.0, przeczytaj temat [ograniczenia v2.0](#limitations).

## <a name="who-can-sign-in"></a>Kto może się zarejestrować

![Kto może zalogować się przy użyciu punktów końcowych w wersji 1.0 i 2.0](media/azure-ad-endpoint-comparison/who-can-sign-in.png)

* Punkt końcowy v1.0 pozwala tylko kont służbowych zalogować się do aplikacji (Azure AD)

* Punkt końcowy v2.0 umożliwia służbowych i kont służbowych z usługi Azure Active Directory i osobistych kont (MSA) (hotmail.com, outlook.com, msn.com) do logowania.

* Punkty końcowe w wersji 1.0 i 2.0 także zaakceptować logowania z *[użytkowników-gości](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)* katalogu usługi Azure AD dla aplikacji skonfigurowanych jako *[pojedynczej dzierżawy](single-and-multi-tenant-apps.md)* lub *wielodostępnych* aplikacji skonfigurowanych do punktu z punktem końcowym specyficznym dla dzierżawy (`https://login.microsoftonline.com/{TenantId_or_Name}`).

Punktu końcowego v2.0 umożliwia pisanie aplikacji, które akceptują Zaloguj się za pomocą zarówno osobistych i kont służbowych, co daje możliwość pisania całkowicie konta niezależny od aplikacji. Na przykład jeśli aplikacja odwołuje się [programu Microsoft Graph](https://graph.microsoft.io), niektóre dodatkowe funkcje i dane będą dostępne dla konta, takie jak ich witryny programu SharePoint lub dane katalogu. Jednak wiele akcji takich jak [czytanie poczty użytkownika](https://graph.microsoft.io/docs/api-reference/v1.0/resources/message), ten sam kod mogą uzyskiwać dostęp do poczty e-mail dla zarówno osobistych i kont służbowych.

Dla punktu końcowego v2.0 można użyć pojedynczego library (MSAL) do uzyskania dostępu do konsumenta, edukacyjnych i rozwiązań enterprise.

 Punkt końcowy usługi Azure AD w wersji 1.0 akceptuje logowania z tylko kont służbowych.

## <a name="incremental-and-dynamic-consent"></a>Przyrostowe i dynamiczne zgody

Aplikacji przy użyciu punktu końcowego usługi Azure AD w wersji 1.0 są wymagane, aby określić ich wymaganych uprawnień OAuth 2.0 z wyprzedzeniem, na przykład:

![Uprawnienia rejestracji interfejsu użytkownika](./media/azure-ad-endpoint-comparison/app_reg_permissions.png)

Uprawnienia ustawione bezpośrednio na rejestrowanie aplikacji są **statyczne**. Statyczne uprawnienia aplikacji zdefiniowane w witrynie Azure portal i przechowywane kod nieuprzywilejowany i proste, jego może powodować kilka problemów dla deweloperów:

* Aplikacja musi wiedzieć, uprawnienia, które nigdy nie potrzebować w czasie tworzenia aplikacji. Dodawanie uprawnień wraz z upływem czasu było trudne procesu.

* Aplikację, musisz wiedzieć, zasoby, które nigdy nie dostęp do wcześniej. Trudno było je tworzyć aplikacje, które można uzyskać dostępu dowolną liczbę zasobów.

* Aplikacja muszą zażądać uprawnień, które nigdy nie będą potrzebne po użytkownik pierwsze logowanie. W niektórych przypadkach, które doprowadziło to do długą listę uprawnień, które nie zaleca się użytkownikom końcowym zatwierdzanie aplikacji dostęp do początkowego logowania.

Z punktem końcowym v2.0, można zignorować statycznie zdefiniowanych uprawnienia zdefiniowane w informacje o rejestracji aplikacji w witrynie Azure portal i określ uprawnienia wymagane przez aplikację **dynamicznie** w czasie wykonywania, podczas normalnego korzystania z usługi Aplikacja, niezależnie od statycznie zdefiniowanych uprawnień w informacje o rejestracji aplikacji.

Aby to zrobić, można określić zakresy, Twoja aplikacja wymaga w dowolnym danym momencie w czasie Twojej aplikacji przez nowe zakresy, w tym `scope` parametru podczas żądania tokenu dostępu — bez konieczności wstępnego definiowania ich w aplikacji informacje rejestracyjne .

Jeśli użytkownik jeszcze nie wyraził zgody na nowe zakresy dodawana do żądania, monitowany zgody tylko do nowych uprawnień. Aby dowiedzieć się więcej, użytkownik może Czytaj [uprawnienia, wyrażania zgody i zakresy](v2-permissions-and-consent.md).

Zezwolenie aplikacji, aby zażądać uprawnień dynamicznie za pomocą `scope` parametru daje deweloperom pełną kontrolę nad środowiskiem użytkownika. Jeśli chcesz, można też obciążenie frontonu swoją zgodę środowisko i poproś o wszystkie uprawnienia w jednym początkowej autoryzacji żądania. Jeśli aplikacja wymaga dużej liczby uprawnień, można też uzyskanie te uprawnienia od użytkownika przyrostowo, zgodnie z ich próbują użyć niektórych funkcji aplikacji wraz z upływem czasu.

Pamiętaj, że zgody administratora, wykonywane w imieniu organizacji nadal używa statycznego uprawnienia zarejestrowany dla aplikacji, dlatego zalecane jest ustawienie tych uprawnień dla aplikacji przy użyciu punktu końcowego v2.0, jeśli potrzebujesz administrator, aby wyrazić zgodę w imieniu całej organizacja. Zmniejsza to cykle wymaganego przez administratora organizacji, aby skonfigurować aplikację

## <a name="scopes-not-resources"></a>Zakresy, nie zasobów

W przypadku aplikacji przy użyciu punktu końcowego v1.0 aplikacji mogą zachowywać się jak **zasobów**, lub odbiorcy tokenów. Zasób można zdefiniować wiele **zakresy** lub **oAuth2Permissions** siebie, dzięki czemu klienta aplikacji, aby żądać tokenów do zasobu na potrzeby określone zakresy. Należy wziąć pod uwagę interfejsu API programu Graph usługi Azure AD, na przykład zasób:

* Identyfikator zasobu lub `AppID URI`: `https://graph.windows.net/`

* Zakresy, lub `OAuth2Permissions`: `Directory.Read`, `Directory.Write`i tak dalej.

Wszystko to prawdziwe dla punktu końcowego v2.0. Aplikacja, nadal może zachowywać się jak zasób, Definiowanie zakresów i być identyfikowany przez identyfikator URI. Aplikacje klienckie nadal może zażądać dostępu do tych zakresów. Jednak zmienił się sposób, że klient żąda te uprawnienia. Dla punktu końcowego w wersji 1.0, OAuth 2.0 autoryzować żądania do usługi Azure AD może być sprawdzono, takich jak:

```text
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https://graph.windows.net/
...
```

gdzie **zasobów** parametru wskazane zasobu, który aplikacja kliencka żąda zezwolenia na. Usługa Azure AD obliczane uprawnień wymaganych przez aplikację na podstawie konfiguracji statycznej w witrynie Azure portal i w związku z tym wystawionych tokenów. W przypadku aplikacji przy użyciu punktu końcowego v2.0 tego samego protokołu OAuth 2.0 zezwalają na żądanie wygląda:

```text
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https://graph.windows.net/directory.read%20https://graph.windows.net/directory.write
...
```

gdzie **zakres** parametr wskazuje zasobu, który i uprawnień aplikacja żąda autoryzacji dla. Żądany zasób jest nadal znajdują się na żądanie — po prostu ujęty w każdej wartości parametru zakresu. Za pomocą parametru zakresu w ten sposób umożliwia punktem końcowym v2.0 za bardziej zgodny ze specyfikacją protokołu OAuth 2.0 i lepiej wyrównane typowe rozwiązania w branży. Umożliwia aplikacji przeprowadzenie [przyrostowe zgody](#incremental-and-dynamic-consent), opisanego wcześniej.

## <a name="well-known-scopes"></a>Dobrze znane zakresów

### <a name="offline-access"></a>Dostęp w trybie offline

Aplikacje korzystające z punktem końcowym v2.0 może wymagać korzystanie z nowego uprawnienia dobrze znanych dla aplikacji — `offline_access` zakresu. Wszystkie aplikacje, należy zażądać tego uprawnienia, gdy potrzebują dostępu do zasobów w imieniu użytkownika przez dłuższy okres czasu, nawet wtedy, gdy użytkownik może nie być aktywnie przy użyciu aplikacji. `offline_access` Użytkownikowi w oknach dialogowych wyrażania zgody, jak będzie wyglądać zakres **uzyskiwać dostęp do danych w dowolnym momencie**, które użytkownik musi wyrazić zgodę na. Żądanie `offline_access` uprawnień spowoduje włączenie aplikacji sieci web do odbierania refresh_tokens OAuth 2.0 z punktem końcowym v2.0. Odśwież tokeny są długotrwałe i mogły być wymieniane przez dłuższy czas dostępu dla nowych tokenów dostępu protokołu OAuth 2.0.

Jeśli aplikacja nie żąda `offline_access` zakresu, nie będzie ona otrzymywać tokenów odświeżania. Oznacza to, że po wprowadzeniu kodu autoryzacji w przepływ kodu autoryzacji OAuth 2.0, subskrybent otrzyma tylko ponownie tokenu dostępu z `/token` punktu końcowego. Token dostępu pozostanie ważny przez krótki okres czasu (zazwyczaj jedna godzina), ale wygaśnie po pewnym czasie. W tym punktu w czasie Twojej aplikacji będą musieli przekieruje użytkownika z powrotem do `/authorize` punktu końcowego, aby pobrać nowy kod autoryzacji. Podczas tego przekierowania użytkownik może lub nie trzeba ponownie wprowadzić swoje poświadczenia lub ponownie wyrazić zgodę na uprawnienia, w zależności od typu aplikacji.

Aby dowiedzieć się więcej na temat protokołu OAuth 2.0 `refresh_tokens`, i `access_tokens`, zapoznaj się z [referencyjne protokołu v2.0](active-directory-v2-protocols.md).

### <a name="openid-profile-and-email"></a>OpenID, profilu i wiadomości e-mail

W przeszłości, najbardziej podstawowa przepływ protokołu OpenID Connect logowania w usłudze Azure AD będzie dostarczali mnóstwa informacji o użytkowniku w wynikowym *id_token*. Oświadczenia w *id_token* mogą obejmować nazwy, preferowany nazwy użytkownika, adres e-mail, identyfikator obiektu i więcej.

Informacje, `openid` zakresu daje aplikacji dostęp do jest teraz ograniczone. `openid` Zakresu zezwala tylko Twojej aplikacji logują użytkownika i odbieranie identyfikator specyficzny dla aplikacji dla użytkownika. Jeśli chcesz uzyskać danych osobowych użytkownika w aplikacji, aplikację należy zażądania dodatkowych uprawnień przez użytkownika. Dwa nowe zakresy — `email` i `profile` zakresy — umożliwi zażądania dodatkowych uprawnień.

`email` Zakresu umożliwia aplikacji dostęp do adres podstawowy adres e-mail użytkownika za pośrednictwem `email` oświadczenia w id_token. `profile` Zakresu daje aplikacji dostęp do wszystkich innych podstawowe informacje o użytkowniku — ich nazwy, preferowany nazwy użytkownika, identyfikator obiektu i tak dalej.

Dzięki temu kod aplikacji w sposób minimalny ujawnienie — można tylko prosisz użytkownika o zestawie informacji, że wymaganych przez aplikację, aby to zrobić. Aby uzyskać więcej informacji na temat tych zakresów, zobacz [dokumentacja zakresu v2.0](v2-permissions-and-consent.md).

## <a name="token-claims"></a>Oświadczenia tokenu

Oświadczenia w tokeny wystawione przez punkt końcowy v2.0, nie będzie taka sama jak tokeny wystawione przez ogólnie dostępne punkty końcowe usługi Azure AD. Migracja do nowej usługi aplikacji nie powinna przyjęto założenie, że danego oświadczenia będzie istnieć w id_tokens lub access_tokens. Dalsze szczegóły różnego rodzaju tokeny używane w punkcie końcowym v2.0 są dostępne w [token dostępu](access-tokens.md) odwołania i [ `id_token` odwołania](id-tokens.md)

## <a name="limitations"></a>Ograniczenia

Istnieje kilka ograniczeń pod uwagę podczas korzystania z wersji 2.0.

Podczas tworzenia aplikacji, które integrują się z platformą Microsoft identity należy zdecydować, czy punkt końcowy i uwierzytelniania protokoły w wersji 2.0 własnych potrzeb. Punkt końcowy w wersji 1.0 i platformy jest nadal w pełni obsługiwana i pod pewnymi względami jest więcej wyposażonym niż w wersji 2.0. Jednak w wersji 2.0 [wprowadzono znaczące korzyści związane](azure-ad-endpoint-comparison.md) dla deweloperów.

Oto uproszczony zalecenia dla deweloperów, w tym momencie:

* Jeśli w Twojej aplikacji, musi obsługiwać osobistych kont Microsoft, należy użyć w wersji 2.0. Jednak zanim to zrobisz, pamiętaj, że znasz ograniczenia omówionych w tym artykule.

* Jeśli aplikacja wymaga tylko do obsługi prac firmy Microsoft i kont służbowych, nie należy używać w wersji 2.0. Zamiast tego należy odwoływać się do [przewodnik v1.0](azure-ad-developers-guide.md).

Aby wyeliminować ograniczenia wymienione w tym miejscu, tak aby tylko nigdy nie będą musieli używać punktu końcowego v2.0 ewoluuje wraz z punktem końcowym v2.0. W międzyczasie Użyj w tym artykule, aby określić, czy punktem końcowym v2.0 jest odpowiedni dla Ciebie. Firma Microsoft nalicza się do aktualizacji w tym artykule, aby odzwierciedlić bieżący stan punktu końcowego v2.0. Sprawdź ponownie oceń wymagań względem możliwości w wersji 2.0.

### <a name="restrictions-on-app-types"></a>Ograniczenia dotyczące typów aplikacji

Obecnie następujące typy aplikacji nie są obsługiwane przez punkt końcowy w wersji 2.0. Opis typów obsługiwanych aplikacji, zobacz [typy aplikacji w wersji 2.0](v2-app-types.md).

#### <a name="standalone-web-apis"></a>Interfejsy API sieci Web autonomiczny

Można użyć do punktem końcowym v2.0 [Tworzenie internetowego interfejsu API, który jest zabezpieczony za pomocą protokołu OAuth 2.0](v2-app-types.md#web-apis). Jednak tego interfejsu API sieci Web może odbierać tokeny tylko z aplikacji, która ma ten sam identyfikator aplikacji. Nie można uzyskać dostępu do internetowego interfejsu API z klienta, który ma inny identyfikator aplikacji. Klient nie będzie mógł żądania lub uzyskania uprawnień do internetowego interfejsu API.

Aby zobaczyć, jak utworzyć internetowy interfejs API, który akceptuje tokeny od klienta, który ma ten sam identyfikator aplikacji, zobacz przykłady interfejsu API sieci Web punktu końcowego v2.0 w [v2.0 wprowadzenie](v2-overview.md#getting-started) sekcji.

### <a name="restrictions-on-app-registrations"></a>Ograniczenia dotyczące rejestracje aplikacji

Obecnie dla każdej aplikacji, którą chcesz zintegrować z punktem końcowym v2.0, należy utworzyć rejestracji aplikacji w nowym [portalu rejestracji aplikacji Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList). Istniejącej usługi Azure AD lub aplikacji konta Microsoft nie są zgodne z punktem końcowym v2.0. Aplikacje, które są zarejestrowane w dowolnym portalu, innych niż portalu rejestracji aplikacji nie są zgodne z punktem końcowym v2.0.

Ponadto rejestracje aplikacji, które tworzysz w [portalu rejestracji aplikacji](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) mają następujące zastrzeżenia:

* Tylko dwa wpisy tajne aplikacji są dozwolone na identyfikator aplikacji.

* Rejestracja aplikacji zarejestrowanych przez użytkownika za pomocą osobistego konta Microsoft można wyświetlać i zarządzane tylko przez konto jednego dewelopera. Nie można udostępniać między wielu deweloperów. Jeśli chcesz udostępnić swoją rejestrację aplikacji wśród wielu deweloperów, można utworzyć aplikacji, logując się do portalu rejestracji przy użyciu konta usługi Azure AD.

* Istnieje kilka ograniczeń dotyczących formatu adres URL przekierowania, który jest dozwolony. Aby uzyskać więcej informacji na temat adres URL przekierowania zobacz następną sekcję.

### <a name="restrictions-on-redirect-urls"></a>Ograniczenia dotyczące przekierowania adresów URL

Aplikacje, które są zarejestrowane w portalu rejestracji aplikacji są ograniczone do ograniczonego zestawu wartości adresu URL przekierowania. Przekierowywanie adresu URL dla aplikacji i usług internetowych musi zaczynać się od systemu `https`, a wszystkie wartości adresu URL przekierowania muszą współużytkować jedną domenę DNS. Na przykład nie można zarejestrować aplikacji sieci web, która zawiera jedną z tych adresów URL przekierowania:

* `https://login-east.contoso.com`  
* `https://login-west.contoso.com`

System rejestracji porównuje całą nazwę DNS istniejącego adresu URL przekierowania na nazwę DNS adresu URL przekierowania, który chcesz dodać. Żądanie dodania nazwy DNS zakończy się niepowodzeniem, jeśli będzie spełniony jeden z następujących warunków:  

* Cała nazwa DNS nowego adresu URL przekierowania jest niezgodny z nazwą DNS istniejącego adresu URL przekierowania.

* Cała nazwa DNS nowego adresu URL przekierowania nie jest poddomeną istniejącego adresu URL przekierowania.

Na przykład, jeśli aplikacja ma ten adres URL przekierowania:

`https://login.contoso.com`

Można dodać do niego adres w następujący sposób:

`https://login.contoso.com/new`

W takim przypadku nazwa DNS jest idealnie zgodna. Można też zrobić tak:

`https://new.login.contoso.com`

W takim przypadku przywoływana jest poddomena DNS domeny login.contoso.com. Jeśli chcesz mieć aplikację, która ma `login-east.contoso.com` i `login-west.contoso.com` jako przekierowania adresów URL, należy dodać te przekierowania adresów URL w następującej kolejności:

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

Dwa ostatnie adresy można dodać, ponieważ są poddomenami pierwszego adresu URL przekierowania contoso.com. To ograniczenie zostanie usunięte w kolejnej wersji.

Należy również zauważyć, może mieć tylko 20 adresów URL odpowiedzi dla określonej aplikacji.

Aby dowiedzieć się, jak zarejestrować aplikację w portalu rejestracji aplikacji, zobacz [jak zarejestrować aplikację za pośrednictwem punktu końcowego v2.0](quickstart-v2-register-an-app.md).

### <a name="restrictions-on-libraries-and-sdks"></a>Ograniczenia dotyczące bibliotek i zestawów SDK

Obecnie Obsługa bibliotek dla punktu końcowego v2.0 jest ograniczona. Jeśli chcesz używać punktu końcowego v2.0 w aplikacji produkcyjnej, masz następujące opcje:

* Jeśli tworzysz aplikację sieci web bezpiecznie służy oprogramowania pośredniczącego Microsoft jest ogólnie dostępna po stronie serwera do wykonywania sprawdzania poprawności logowania i tokenu. Obejmują one oprogramowania pośredniczącego OWIN Open ID Connect platformy ASP.NET i Node.js Passport wtyczki. Aby uzyskać przykłady kodu, które używają oprogramowania pośredniczącego Microsoft, zobacz [v2.0 wprowadzenie](v2-overview.md#getting-started) sekcji.

* Jeśli tworzysz aplikację na komputerze lub urządzeniu przenośnym, można użyć jednej z bibliotek uwierzytelniania firmy Microsoft (MSAL) w wersji zapoznawczej. Tych bibliotek znajdują się w obsługiwane w środowisku produkcyjnym wersji zapoznawczej, więc bezpiecznie z nich korzystać w aplikacjach produkcyjnych. Możesz dowiedzieć się więcej o warunkach korzystania z wersji zapoznawczej i dostępnych bibliotek w [dokumentacja bibliotek uwierzytelniania](reference-v2-libraries.md).

* W przypadku platform nie są objęte biblioteki Microsoft można zintegrować z punktem końcowym v2.0 przez bezpośrednie wysyłanie i odbieranie wiadomości protokołu w kodzie aplikacji. Protokoły OpenID Connect i OAuth 2.0 [są udokumentowane](active-directory-v2-protocols.md) ułatwiające wykonanie takiej integracji.

* Na koniec można użyć bibliotek typu open-source Otwórz ID Connect i OAuth do integracji z punktem końcowym v2.0. Protokół v2.0 powinien być zgodny z wielu bibliotek typu open-source protokołu bez znaczące zmiany. Dostępność tych rodzajów bibliotek zależy od języka i platformy. [Open ID Connect](http://openid.net/connect/) i [OAuth 2.0](http://oauth.net/2/) witryn sieci Web utrzymywać listę popularnych implementacji. Aby uzyskać więcej informacji, zobacz [biblioteki Azure Active Directory w wersji 2.0 i uwierzytelniania](reference-v2-libraries.md)i listy, biblioteki klienckie typu open source i przykładów, które zostały przetestowane z punktem końcowym v2.0.

* Odwołanie `.well-known` endpoint wspólnego punktu końcowego v2.0 jest `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration` .  Zastąp `common` identyfikatorem dzierżawy można pobrać danych określonego dla Twojej dzierżawy.  

### <a name="restrictions-on-protocols"></a>Ograniczenia dotyczące protokołów

Nie obsługuje punktu końcowego v2.0, SAML lub WS-Federation; obsługuje tylko Open ID Connect i OAuth 2.0. Nie wszystkie funkcje i możliwości protokołów OAuth zostały włączone do punktu końcowego v2.0.

Następujące funkcje protokołu i funkcje są obecnie *nie jest dostępna* w punkcie końcowym v2.0:

* Obecnie `email` oświadczeń jest zwracany tylko jeśli skonfigurowano opcjonalnego roszczenia, a zakres jest zakresem = wiadomość e-mail została określona w żądaniu. Jednak to zachowanie zmieni się zgodnie z punktem końcowym v2.0 jest aktualizowana w celu dalszego są zgodne ze standardami Open ID Connect i OAuth 2.0.

* Punktu końcowego v2.0 nie obsługuje wystawiającego oświadczeń roli lub grupy w tokeny Identyfikatora.

* [Przydział poświadczeń hasła właściciela zasobów OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.3) nie jest obsługiwana przez punktu końcowego v2.0.

Ponadto punktu końcowego v2.0 nie obsługuje dowolnej formie protokołów SAML lub WS-Federation.

Aby lepiej zrozumieć zakres protokołu funkcje obsługiwane w punkcie końcowym v2.0, zapoznaj się z artykułem naszych [referencyjne protokołu OpenID Connect i OAuth 2.0](active-directory-v2-protocols.md).

#### <a name="saml-restrictions"></a>Ograniczenia dotyczące języka SAML

Jeśli używano Active Directory Authentication Library (ADAL) w aplikacjach Windows, należy wprowadzić jakieś zalet zintegrowanego uwierzytelniania Windows, które używa grant asercji zabezpieczeń Assertion Markup Language (SAML). Z tym przydziałem użytkowników federacyjnych usługi Azure AD mogą dyskretnie uwierzytelnianie dzierżaw za pomocą swojego wystąpienia usługi Active Directory w środowisku lokalnym bez konieczności wprowadzania poświadczeń. Obecnie grant potwierdzenie SAML nie jest obsługiwana w punkcie końcowym v2.0.
