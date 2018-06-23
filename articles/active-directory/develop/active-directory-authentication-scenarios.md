---
title: Scenariusze uwierzytelniania dla usługi Azure AD | Dokumentacja firmy Microsoft
description: Zawiera omówienie pięciu najbardziej typowych scenariuszy uwierzytelniania usługi Azure Active Directory (Azure AD)
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 0c84e7d0-16aa-4897-82f2-f53c6c990fd9
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: f85b97791c9e2d17417f82dae05d27838be63703
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2018
ms.locfileid: "36319017"
---
# <a name="authentication-scenarios-for-azure-ad"></a>Scenariusze uwierzytelniania dla usługi Azure AD

Azure Active Directory (Azure AD) ułatwia uwierzytelniania dla deweloperów, zapewniając tożsamości jako usługa z obsługą standardowych protokołów, takich jak OAuth 2.0 i OpenID Connect, a także open source biblioteki dla różnych platform ułatwić programować szybko. W tym artykule ułatwią zrozumienie różnych obsługuje scenariusze usługi Azure AD i opisano, jak rozpocząć pracę. Scenariusz jest podzielony na następujące sekcje:

* [Podstawy uwierzytelniania w usłudze Azure AD](#basics-of-authentication-in-azure-ad)
* [Oświadczenia w tokenach zabezpieczających usługi Azure AD](#claims-in-azure-ad-security-tokens)
* [Podstawowe informacje dotyczące rejestrowania aplikacji w usłudze Azure AD](#basics-of-registering-an-application-in-azure-ad)
* [Typy aplikacji i scenariusze](#application-types-and-scenarios)

  * [Przeglądarki sieci Web do aplikacji sieci web](#web-browser-to-web-application)
  * [Jednostronicowej aplikacji JEDNOSTRONICOWEJ](#single-page-application-spa)
  * [Aplikacji natywnej do interfejsu API sieci web](#native-application-to-web-api)
  * [Aplikacja sieci Web do interfejsu API sieci web](#web-application-to-web-api)
  * [Demon lub serwera aplikacji interfejsu API sieci web](#daemon-or-server-application-to-web-api)

## <a name="basics-of-authentication-in-azure-ad"></a>Podstawy uwierzytelniania w usłudze Azure AD

Jeśli znasz podstawowe koncepcje uwierzytelniania w usłudze Azure AD, do odczytu w tej sekcji. W przeciwnym razie można pominąć w dół do [typy aplikacji i scenariusze](#application-types-and-scenarios).

Teraz Rozważmy scenariusz najbardziej podstawowa, gdy wymagana jest tożsamość: użytkownik w przeglądarce sieci web musi zostać uwierzytelniona aplikacji sieci web. Ten scenariusz jest opisany bardziej szczegółowo w [przeglądarki sieci Web do aplikacji sieci web](#web-browser-to-web-application) sekcji, ale stanowi dobry punkt wyjścia ilustrują możliwości usługi Azure AD i conceptualize, jak działa scenariusza użytkownika. Na poniższym diagramie tego scenariusza należy wziąć pod uwagę:

![Omówienie logowania jednokrotnego do aplikacji sieci web](./media/active-directory-authentication-scenarios/basics_of_auth_in_aad.png)

Diagram powyżej pamiętać tutaj jest, co należy wiedzieć o jego różne składniki:

* Usługi Azure AD jest dostawcy tożsamości, odpowiada za weryfikację tożsamości użytkowników i aplikacje, które znajdują się w katalogu organizacji, a ostatecznie wystawiania tokenów zabezpieczających na pomyślne uwierzytelnienie tych użytkowników i aplikacji.
* Aplikacja, która chce zewnętrzny uwierzytelniania do usługi Azure AD musi być zarejestrowana w usłudze Azure AD, który rejestruje i unikatowo identyfikuje aplikację w katalogu.
* Deweloperzy mogą używać biblioteki uwierzytelniania usługi Azure AD open source, aby ułatwić uwierzytelniania dzięki obsłudze szczegółów protokołu dla Ciebie. Aby uzyskać więcej informacji, zobacz [bibliotek uwierzytelniania usługi Azure Active Directory](active-directory-authentication-libraries.md).
* Po uwierzytelnieniu użytkownika aplikacji muszą weryfikacji tokenu zabezpieczeń użytkownika, aby upewnić się, że uwierzytelnianie zakończyło się pomyślnie. Mamy przykłady aplikacji wykonaj wiele języków i struktur na [GitHub](https://github.com/Azure-Samples?q=active-directory). Jeśli tworzysz aplikację sieci web w programie ASP.NET, zobacz [dodać logowania dla przewodnika aplikacji sieci web ASP.NET](https://docs.microsoft.com/en-us/azure/active-directory/develop/guidedsetups/active-directory-aspnetwebapp). Jeśli tworzysz web API zasobów w programie ASP.NET, zobacz [interfejsu API sieci web wprowadzenie — przewodnik](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-devquickstarts-webapi-dotnet).
* Przepływ żądań i odpowiedzi do procesu uwierzytelniania jest określany przez protokół uwierzytelniania, który był używany, takich jak OAuth 2.0, OpenID Connect, WS-Federation oraz SAML 2.0. Te protokoły omówiono bardziej szczegółowo w [protokoły uwierzytelniania usługi Azure Active Directory](active-directory-authentication-protocols.md) artykułu w poniższych sekcjach.

> [!NOTE]
> Usługi Azure AD obsługuje OAuth 2.0 i standardów OpenID Connect, które szeroką gamę wykorzystanie tokenów elementu nośnego, łącznie z tokenów elementu nośnego reprezentowane jako tokenów Jwt. A *tokenu elementu nośnego* jest tokenem zabezpieczającym lekkie, która udziela dostępu "bearer" do chronionego zasobu. W tym sensie "bearer" jest każda strona, która może ona powodować tokenu. Jeśli strona muszą najpierw zostać uwierzytelnione z usługą Azure AD do odbierania tokenu elementu nośnego, jeśli wymagane kroki nie są brane do zabezpieczania token w transmisji i przechowywania, można przechwycony i używane przez firmę niezamierzone. Chociaż w niektórych tokeny zabezpieczające wbudowany mechanizm uniemożliwia ich użycie przez osoby nieupoważnione, tokenów elementu nośnego nie mają ten mechanizm i musi być transportowane bezpiecznego kanału, takie jak zabezpieczeń warstwy transportu (HTTPS). Jeśli token elementu nośnego są przesyłane bez zabezpieczeń, atak typu man-in--middle może służyć przez złośliwe stronę do uzyskania tokenu i użyć jej do nieautoryzowanego dostępu do chronionego zasobu. Te same zasady zabezpieczeń mają zastosowanie po zapisaniu lub buforowanie tokenów elementu nośnego do późniejszego użycia. Zawsze upewnij się, że aplikacja przesyła i przechowuje tokenów elementu nośnego w bezpieczny sposób. Aby uzyskać więcej zagadnienia dotyczące zabezpieczeń na tokenów elementu nośnego, zobacz [RFC 6750 sekcji 5](http://tools.ietf.org/html/rfc6750).

Teraz, gdy masz omówienie podstawy odczytu w sekcjach poniżej, aby zrozumieć sposób inicjowania obsługi administracyjnej działa w usłudze Azure AD i typowych scenariuszy, które obsługuje usługę Azure AD.

## <a name="claims-in-azure-ad-security-tokens"></a>Oświadczenia w tokenach zabezpieczających usługi Azure AD

Tokeny zabezpieczające (dostępu i tokeny Identyfikatora) wydanego przez usługę Azure AD zawiera oświadczenia lub potwierdzenia informacji na temat, który został uwierzytelniony. Te oświadczenia mogą posłużyć aplikacji dla różnych zadań. Na przykład aplikacje mogą używać oświadczeń sprawdzania poprawności tokenu, zidentyfikować dzierżawy directory podmiotu, wyświetlane informacje o użytkowniku, określa autoryzację podmiotu i tak dalej. W dowolnym tokenu zabezpieczeń oświadczeń są zależne od typu tokenu, typ poświadczeń używanych do uwierzytelniania użytkownika i konfiguracji aplikacji. W poniższej tabeli znajduje się krótki opis każdego typu oświadczenia emitowane przez usługę Azure AD. Aby uzyskać więcej informacji, zapoznaj się [obsługiwane typy tokenów i oświadczeń](active-directory-token-and-claims.md).

| Claim | Opis |
| --- | --- |
| Identyfikator aplikacji | Identyfikuje aplikację, która używa tokenu. |
| Grupy odbiorców | Określa zasób adresata, który token jest przeznaczony dla. |
| Application Authentication Context Class Reference | Wskazuje, jak klient został uwierzytelniony (publicznego klienta a poufne klienta). |
| Błyskawiczne uwierzytelniania | Rejestruje datę i godzinę wystąpienia uwierzytelniania. |
| Metoda uwierzytelniania | Wskazuje, jak został uwierzytelniony podmiot tokenu (hasło, certyfikat itp.). |
| Imię | Udostępnia imię użytkownika, zgodnie z ustawieniami w usłudze Azure AD. |
| Grupy | Zawiera grupy identyfikatorów Azure AD obiektów, których należy użytkownik. |
| Dostawca tożsamości | Rejestruje dostawcę tożsamości, który uwierzytelniony podmiot tokenu. |
| Wystawiony w | Rejestruje czas, w którym token został wystawiony, często używany do tokenu świeżości. |
| Wystawca | Identyfikuje Usługa tokenu Zabezpieczającego, które są emitowane token, a także dzierżawy usługi Azure AD. |
| Nazwisko | Udostępnia nazwisko użytkownika zgodnie z ustawieniami w usłudze Azure AD. |
| Name (Nazwa) | Udostępnia człowieka wartość do odczytu, która identyfikuje podmiotu tokenu. |
| Identyfikator obiektu | Zawiera niezmienne, unikatowy identyfikator tematu w usłudze Azure AD. |
| Role | Zawiera przyjaznych nazw Azure ról aplikacji AD użytkownikowi zostało udzielone. |
| Zakres | Wskazuje uprawnienia do aplikacji klienckiej. |
| Temat | Wskazuje o tym, które token deklaracji rozkazujących informacji podmiotu zabezpieczeń. |
| Identyfikator dzierżawy | Zawiera niezmienne, unikatowy identyfikator dzierżawy katalogu, który wystawił token. |
| Okres istnienia tokenu | Określa przedział czasu, w którym token jest prawidłowy. |
| Nazwa główna użytkownika | Zawiera główną nazwę użytkownika tematu. |
| Wersja | Zawiera numer wersji tokenu. |

## <a name="basics-of-registering-an-application-in-azure-ad"></a>Podstawowe informacje dotyczące rejestrowania aplikacji w usłudze Azure AD

Każda aplikacja, która outsources uwierzytelniania do usługi Azure AD musi być zarejestrowana w katalogu. Ten krok obejmuje informuje o aplikacji, w tym adres URL go ma lokalizację, adres URL do wysyłania odpowiedzi po uwierzytelnieniu identyfikator URI do identyfikowania Twojej aplikacji i inne usługi Azure AD. Te informacje są niezbędne dla kilka przyczyn, dla klucza:

* Usługi Azure AD wymaga do komunikacji z aplikacją podczas obsługi logowania jednokrotnego lub wymianę tokenów. Informacje przekazywane między usługą Azure AD i aplikacji obejmuje następujące elementy:
  
  * **Identyfikator URI Identyfikatora aplikacji** — identyfikator dla aplikacji. Ta wartość jest wysyłany do usługi Azure AD podczas uwierzytelniania wskaż, która aplikacja potrzebuje wywołującego token dla. Ponadto, aby aplikacja wiedziała, że był docelową tę wartość znajduje się w tokenie.
  * **Adres URL odpowiedzi** i **identyfikator URI przekierowania** — interfejs API sieci web lub aplikacji sieci web, adres URL odpowiedzi jest lokalizacji, w którym usługi Azure AD będzie wysyłać odpowiedzi uwierzytelniania, w tym tokenu, jeśli uwierzytelnianie zakończyło się pomyślnie. Dla natywnych aplikacji identyfikator URI przekierowania jest unikatowy identyfikator, do którego usługi Azure AD przekieruje agenta użytkownika żądania OAuth 2.0.
  * **Identyfikator aplikacji** — identyfikator dla aplikacji, który jest generowany przez usługę Azure AD, kiedy aplikacja jest zarejestrowany. Żądający kod autoryzacji lub tokenu aplikacji identyfikator i klucz są wysyłane do usługi Azure AD podczas uwierzytelniania.
  * **Klucz** — klucz, który jest przesyłany wraz identyfikator aplikacji podczas uwierzytelniania do usługi Azure AD do wywołania interfejsu API sieci web.
* Usługi Azure AD, należy upewnić się, że aplikacja ma wymagane uprawnienia dostępu do danych katalogu innych aplikacji w organizacji, i tak dalej.

Inicjowanie obsługi administracyjnej staje się jaśniejszy po zapoznaniu się, że istnieją dwie kategorie aplikacji, które mogą być opracowane i zintegrowane z usługą Azure AD:

* **Pojedyncza aplikacja dzierżawy** -aplikacji pojedynczej dzierżawy jest przeznaczony do użytku w jednej z organizacji. Są to zazwyczaj — biznesowych (LoB) aplikacje napisane przez autora przedsiębiorstwa. Stosowanie pojedynczej dzierżawy tylko musi mieć dostęp użytkownicy w jednym katalogu, a w związku z tym wymaga tylko na potrzeby aprowizacji w jednym katalogu. Te aplikacje zwykle są rejestrowane przez dewelopera w organizacji.
* **Wielodostępne aplikacji** -aplikacji wielodostępnych jest przeznaczony do użycia w wielu organizacjach nie tylko jednej z organizacji. Są to zazwyczaj oprogramowania jako — usługa (SaaS) aplikacje napisane przez niezależnego dostawcy oprogramowania (ISV). Aplikacje wielodostępne konieczne udostępniane w każdym katalogu gdzie będą one używane, wymaga zgody użytkownika lub administratora, aby je zarejestrować. Ten proces zgody uruchamiana, gdy aplikacja został zarejestrowany w katalogu i otrzyma dostęp do interfejsu API programu Graph lub być może inny interfejs API sieci web. Gdy użytkownik lub administrator z innej organizacji zarejestruje się w celu korzystania z aplikacji, mają być przedstawiane z okna dialogowego, które wyświetla uprawnienia wymagane przez aplikację. Użytkownik lub administrator może następnie zgodę na aplikacji, która zapewnia dostęp do aplikacji do określonych danych, a na koniec rejestruje aplikację w ich katalogu. Aby uzyskać więcej informacji, zobacz [omówienie Framework zgody](active-directory-integrating-applications.md#overview-of-the-consent-framework).

### <a name="additional-considerations-when-developing-single-tenant-or-multi-tenant-apps"></a>Dodatkowe uwagi dotyczące tworzenia z jednym lub dzierżawą usługi aplikacji
Kilka dodatkowych kwestii dotyczących wystąpić podczas opracowywania aplikacji wielodostępnych, zamiast aplikacji pojedynczej dzierżawy. Na przykład, jeśli tworzysz aplikację dostępne dla użytkowników w wielu katalogach, należy mechanizm, aby określić, które dzierżawy są one w. Stosowanie pojedynczej dzierżawy musi tylko do przeszukania własnego katalogu dla użytkownika, gdy aplikacja wielodostępne musi zidentyfikować określonego użytkownika ze wszystkich katalogów w usłudze Azure AD. Aby wykonać to zadanie, usługa Azure AD zapewnia wspólnego punktu końcowego uwierzytelniania gdzie wszelkie aplikacje wielodostępne może kierować żądań logowania, zamiast punktu końcowego specyficznego dla dzierżawy. Ten punkt końcowy jest https://login.microsoftonline.com/common dla wszystkich katalogów w usłudze Azure AD, punkt końcowy specyficznego dla dzierżawy może być https://login.microsoftonline.com/contoso.onmicrosoft.com. Typowe punkt końcowy jest szczególnie ważne podczas tworzenia aplikacji, ponieważ będą potrzebne logiki niezbędne do obsługi wielu dzierżawców podczas logowania, wylogowania i weryfikacji tokenu.

Jeśli obecnie opracowujesz aplikację pojedynczej dzierżawy, ale chcesz udostępnić wiele organizacji, można łatwo wprowadzić zmiany do aplikacji i jego konfiguracja w usłudze Azure AD, aby była wielodostępne stanie. Ponadto usługi Azure AD używa tego samego klucza podpisywania wszystkich tokenów we wszystkich katalogach czy udostępniasz uwierzytelniania w pojedynczej dzierżawy lub aplikacji wielu dzierżawców.

Każdy scenariusz wymienione w niniejszym dokumencie obejmuje podsekcji opisującą jego wymagania inicjowania obsługi administracyjnej. Aby uzyskać bardziej szczegółowe informacje dotyczące inicjowania obsługi aplikacji w usłudze Azure AD i różnice między aplikacjami pojedynczych i wielu dzierżawców, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](active-directory-integrating-applications.md) Aby uzyskać więcej informacji. Kontynuuj lekturę zrozumienie typowych scenariuszy aplikacji w usłudze Azure AD.

## <a name="application-types-and-scenarios"></a>Typy aplikacji i scenariusze

Każdy z opisanych tutaj scenariuszy należy opracować przy użyciu różnych języków i platform. One wszystkich bazują na pełną przykłady dostępne w [przewodnik przykłady kodu](active-directory-code-samples.md), lub bezpośrednio z odpowiadającego [repozytoriów GitHub próbki](https://github.com/Azure-Samples?q=active-directory). Ponadto jeśli aplikacja wymaga konkretne lub segment end-to-end scenariusz, w większości przypadków te funkcje można dodać niezależnie. Na przykład jeśli masz natywnych aplikacji, która wywołuje interfejs API sieci web, można łatwo dodać aplikacji sieci web, która również wywołuje interfejs API sieci web. Na poniższym diagramie przedstawiono te scenariusze i typy aplikacji i w jaki sposób można dodać różnych składników:

![Typy aplikacji i scenariusze](./media/active-directory-authentication-scenarios/application_types_and_scenarios.png)

Są to pięć scenariuszy głównej aplikacji obsługiwanych przez usługę Azure AD:

* [Przeglądarki sieci Web do aplikacji sieci web](#web-browser-to-web-application): użytkownik musi zalogować się do aplikacji sieci web chronionej przez usługę Azure AD.
* [Pojedynczy strony aplikacji JEDNOSTRONICOWEJ](#single-page-application-spa): użytkownik musi zalogować się do aplikacji jednej strony, która jest zabezpieczone przez usługę Azure AD.
* [Aplikacji natywnej do interfejsu API sieci web](#native-application-to-web-api): natywnych aplikacji, która działa na telefonie, tablecie lub komputerze wymaga uwierzytelnienia użytkownika można pobrać zasobów z składnika web API, który jest zabezpieczony przez usługę Azure AD.
* [Aplikacja sieci Web interfejsu API sieci Web](#web-application-to-web-api): aplikacja sieci web musi pobrać zasobów z składnika web API zabezpieczone przez usługę Azure AD.
* [Demon lub serwera aplikacji interfejsu API sieci web](#daemon-or-server-application-to-web-api): aplikację demona lub serwera bez interfejsu użytkownika sieci web musi pobrać zasobów z składnika web API zabezpieczone przez usługę Azure AD.

### <a name="web-browser-to-web-application"></a>Przeglądarki sieci Web do aplikacji sieci web

W tej sekcji opisano aplikację, która służy do uwierzytelniania użytkowników w przeglądarce sieci web do aplikacji sieci web. W tym scenariuszu aplikacja sieci web kieruje przeglądarki użytkownika do logowania do usługi Azure AD. Usługi Azure AD zwraca odpowiedź logowania za pośrednictwem przeglądarki użytkownika, który zawiera oświadczenia dotyczące użytkownika w tokenie zabezpieczającym. Ten scenariusz obsługuje logowania przy użyciu protokołów WS-Federation, SAML 2.0 i OpenID Connect.

#### <a name="diagram"></a>Diagram

![Przepływ uwierzytelniania dla przeglądarki do aplikacji sieci web](./media/active-directory-authentication-scenarios/web_browser_to_web_api.png)

#### <a name="description-of-protocol-flow"></a>Opis protokołu przepływu

1. Gdy użytkownik odwiedza aplikacji i musi się zalogować, zostanie przekierowany przy użyciu żądania logowania do punktu końcowego uwierzytelniania w usłudze Azure AD.
1. Użytkownik loguje się na stronie logowania.
1. Jeśli uwierzytelnianie zakończy się pomyślnie, usługi Azure AD umożliwia utworzenie tokenu uwierzytelniania i zwraca odpowiedź logowania do adresu URL odpowiedzi aplikacji, które zostały skonfigurowane w portalu Azure. W przypadku aplikacji produkcyjnej ten adres URL odpowiedzi powinna być HTTPS. Zwrócony token zawiera oświadczenia dotyczące użytkownika i Azure AD, które są wymagane przez aplikację do sprawdzania poprawności tokenu.
1. Aplikacja sprawdza poprawność tokenu przy użyciu publicznego klucza podpisywania i wystawcy informacji dostępnych w dokumencie metadanych Federacji dla usługi Azure AD. Po zgłoszeniu weryfikuje token, nowa sesja rozpoczyna się od użytkownika. Ta sesja zezwala użytkownikowi na dostęp do aplikacji, do momentu wygaśnięcia.

#### <a name="code-samples"></a>Przykłady kodu

Zobacz przykłady kodu dla przeglądarki sieci Web, aby scenariusze aplikacji sieci Web. A wrócić tu często — często są dodawane nowe próbki. [Aplikacja sieci Web](active-directory-code-samples.md#web-applications).

#### <a name="registering"></a>Rejestrowanie

* Pojedynczej dzierżawy: Jeśli tworzysz aplikację tylko dla organizacji, musi być zarejestrowana w katalogu firmy przy użyciu portalu Azure.
* Wielodostępne: Jeśli tworzysz aplikację, która może być używane przez użytkowników spoza organizacji, to musi być zarejestrowana w katalogu firmy, ale musi być zarejestrowana w poszczególnych organizacji w katalogu, który będzie używany w aplikacji. Aby udostępnić w ich katalogu aplikacji, można dołączyć procesu tworzenia konta dla klientów, które umożliwia im wyrażenia zgody na aplikację. Po zapisaniu się aplikacji one zostanie wyświetlone okno dialogowe, które zawiera uprawnienia wymagane przez aplikację, a następnie opcję wyrażenia zgody. W zależności od wymagane uprawnienia administratora w innej organizacji może być konieczna wyrażenia zgody. Po wyrażeniu zgody przez użytkownika lub administratora, aplikacja jest zarejestrowany w ich katalogu. Aby uzyskać więcej informacji, zobacz [integracji aplikacji z usługą Azure Active Directory](active-directory-integrating-applications.md).

#### <a name="token-expiration"></a>Wygaśnięcia tokenu

Sesja użytkownika wygasa po wygaśnięciu ważności tokenu wystawiony przez usługę Azure AD. Aplikację można zmniejszyć tym okresie, w razie potrzeby, takie jak wylogowywania użytkowników oparte na okresie braku aktywności. Po wygaśnięciu sesji użytkownika pojawi się monit, aby zalogować się ponownie.

### <a name="single-page-application-spa"></a>Jednostronicowej aplikacji JEDNOSTRONICOWEJ

W tej sekcji opisano uwierzytelnianie dla jednej aplikacji strony, którego używa usługi Azure AD i autoryzacji niejawne OAuth 2.0 są przyznawane do zabezpieczania jego zakończenia interfejsu API z powrotem w sieci web. Aplikacje jednostronicowe są zwykle struktury jako Warstwa prezentacji JavaScript (frontonu) działającym w przeglądarce i interfejsu API sieci Web wewnętrznej działa na serwerze, który implementuje logiki biznesowej aplikacji. Aby dowiedzieć się więcej o udzielania autoryzacji niejawne i pomóc zdecydować, czy jest odpowiednie dla danego scenariusza aplikacji, zobacz [opis OAuth2 niejawne Przyznaj przepływu w usłudze Azure Active Directory](active-directory-dev-understanding-oauth2-implicit-grant.md).

W tym scenariuszu, gdy użytkownik się zaloguje, JavaScript FrontPage używa zakończenia [biblioteki uwierzytelniania usługi Active Directory dla języka JavaScript (ADAL. JS)](https://github.com/AzureAD/azure-activedirectory-library-for-js) i przyznać niejawne autoryzacji można uzyskać Identyfikatora tokenu (żądaniu) z usługi Azure AD. Token jest buforowany i klient dołącza go do żądania jako token elementu nośnego wywołania do jego interfejsu API sieci Web zaplecza, która jest zabezpieczone przy użyciu oprogramowania pośredniczącego OWIN. 

#### <a name="diagram"></a>Diagram

![Jeden diagram strony aplikacji](./media/active-directory-authentication-scenarios/single_page_app.png)

#### <a name="description-of-protocol-flow"></a>Opis protokołu przepływu

1. Użytkownik przechodzi do aplikacji sieci web.
1. Aplikacja zwraca JavaScript frontonu (Warstwa prezentacji) do przeglądarki.
1. Użytkownik inicjuje logowania, na przykład przez kliknięcie łącza logowania. Przeglądarka wysyła GET do żądania tokenu identyfikator punktu końcowego autoryzacji usługi Azure AD. To żądanie zawiera adres URL Identyfikatora i odpowiedzi aplikacji w parametrach zapytania.
1. Usługi Azure AD weryfikuje adres URL odpowiedzi przed zarejestrowany adres URL odpowiedzi, który został skonfigurowany w portalu Azure.
1. Użytkownik loguje się na stronie logowania.
1. Jeśli uwierzytelnianie zakończy się pomyślnie, usługi Azure AD umożliwia utworzenie tokenu identyfikator i zwraca jako fragmentu adresu URL (#) do adresu URL odpowiedzi aplikacji. W przypadku aplikacji produkcyjnej ten adres URL odpowiedzi powinna być HTTPS. Zwrócony token zawiera oświadczenia dotyczące użytkownika i Azure AD, które są wymagane przez aplikację do sprawdzania poprawności tokenu.
1. Kod klienta JavaScript w przeglądarce token zostanie wyodrębniony z odpowiedzi do użycia w zabezpieczanie wywołań do aplikacji sieci web zakończenie interfejsu API z powrotem.
1. Przeglądarka wywołuje aplikacji sieci web interfejsu API z powrotem kończyć się przy użyciu tokenu dostępu w nagłówku autoryzacji.

#### <a name="code-samples"></a>Przykłady kodu

Zobacz przykłady kodu dla scenariuszy jednej strony aplikacji JEDNOSTRONICOWEJ. Pamiętaj wrócić tu często — często są dodawane nowe próbki. [Pojedynczy strony aplikacji JEDNOSTRONICOWEJ](active-directory-code-samples.md#single-page-applications).

#### <a name="registering"></a>Rejestrowanie

* Pojedynczej dzierżawy: Jeśli tworzysz aplikację tylko dla organizacji, musi być zarejestrowana w katalogu firmy przy użyciu portalu Azure.
* Wielodostępne: Jeśli tworzysz aplikację, która może być używane przez użytkowników spoza organizacji, to musi być zarejestrowana w katalogu firmy, ale musi być zarejestrowana w poszczególnych organizacji w katalogu, który będzie używany w aplikacji. Aby udostępnić w ich katalogu aplikacji, można dołączyć procesu tworzenia konta dla klientów, które umożliwia im wyrażenia zgody na aplikację. Po zapisaniu się aplikacji one zostanie wyświetlone okno dialogowe, które zawiera uprawnienia wymagane przez aplikację, a następnie opcję wyrażenia zgody. W zależności od wymagane uprawnienia administratora w innej organizacji może być konieczna wyrażenia zgody. Po wyrażeniu zgody przez użytkownika lub administratora, aplikacja jest zarejestrowany w ich katalogu. Aby uzyskać więcej informacji, zobacz [integracji aplikacji z usługą Azure Active Directory](active-directory-integrating-applications.md).

Po zarejestrowaniu aplikacji musi być skonfigurowana do używania protokołu OAuth 2.0 niejawne Przyznaj. Domyślnie ten protokół jest wyłączone dla aplikacji. Aby włączyć protokół OAuth2 niejawne Przyznaj aplikacji, Edytuj jej w manifeście aplikacji z portalu Azure i ustaw wartość "oauth2AllowImplicitFlow" na true. Aby uzyskać szczegółowe instrukcje, zobacz [włączenie OAuth 2.0 niejawne Przyznaj dla aplikacje jednostronicowe](active-directory-integrating-applications.md).

#### <a name="token-expiration"></a>Wygaśnięcia tokenu

Przy użyciu ADAL.js ułatwiające:

* odświeżanie wygasły token
* żądanie tokenu dostępu do wywołania interfejsu API zasobów sieci web

Po pomyślnym uwierzytelnieniu usługi Azure AD zapisuje plik cookie w przeglądarce użytkownika w celu ustanowienia sesji. Należy pamiętać, że sesja istnieje między użytkownikiem a usługą Azure AD (nie między użytkownikiem i aplikacji sieci web). Po wygaśnięciu tokenu ADAL.js używa tej sesji dyskretnie uzyskać inny token. ADAL.js używa ukryte iFrame do wysyłania i odbierania za pomocą protokołu OAuth niejawne Przyznaj żądania. ADAL.js umożliwia także ten sam mechanizm dyskretnie uzyskanie tokenów dostępu do innych zasobów interfejsu API sieci web, aplikacji wywołania tak długo, jak te zasoby obsługuje współużytkowanie zasobów między źródłami (CORS), do udostępniania są rejestrowane w katalogu użytkownika i wszystkie wymagane zgody został podane przez użytkownika podczas logowania.

### <a name="native-application-to-web-api"></a>Aplikacji natywnej do interfejsu API sieci web

W tej sekcji opisano natywnych aplikacji, która wywołuje interfejs API sieci web w imieniu użytkownika. Ten scenariusz jest zbudowany na typ grant kodu autoryzacji OAuth 2.0 za pomocą publicznego klienta zgodnie z opisem w sekcji 4.1 [Specyfikacja protokołu OAuth 2.0](http://tools.ietf.org/html/rfc6749). Aplikacji natywnej uzyskuje token dostępu dla użytkownika za pomocą protokołu OAuth 2.0. Ten token dostępu jest następnie wysyłane żądania sieci Web interfejsu API, które udzielają użytkownikowi autoryzacji i zwraca wartość żądanego zasobu.

#### <a name="diagram"></a>Diagram

![Aplikacji natywnej do diagramu interfejsu API w sieci Web](./media/active-directory-authentication-scenarios/native_app_to_web_api.png)

#### <a name="description-of-protocol-flow"></a>Opis protokołu przepływu

Jeśli korzystasz z biblioteki uwierzytelniania usługi AD, większość szczegóły protokołu opisanych poniżej są obsługiwane, takich jak okienko wyskakujące przeglądarki, buforowaniem tokena i Obsługa tokenów odświeżania.

1. Przy użyciu przeglądarki wyskakujących aplikacji natywnej zgłasza żądanie do punktu końcowego autoryzacji w usłudze Azure AD. To żądanie zawiera identyfikator aplikacji i Przekierowanie URI aplikacji natywnej, jak pokazano w portalu Azure i aplikacji identyfikator URI dla interfejsu API sieci web. Jeśli użytkownik nie już zalogowany, są monitowani o ponownie się zalogować
1. Usługi Azure AD uwierzytelnia użytkownika. Jeśli jest to aplikacja wielodostępnej i zgody jest wymagana do korzystania z aplikacji, użytkownika będą musieli zgody, jeśli ich nie zostało to jeszcze zrobione. Po przyznaniu zgody i po pomyślnym uwierzytelnieniu usługa Azure AD wystawia odpowiedź kodu autoryzacji do identyfikatora URI przekierowania aplikacji klienckiej.
1. Gdy usługa Azure AD wystawia odpowiedź kodu autoryzacji do identyfikatora URI przekierowania, aplikacja kliencka zatrzymuje interakcji z przeglądarką i wyodrębnia kod autoryzacji z odpowiedzi. Przy użyciu tego kodu autoryzacji, aplikacja kliencka wysyła żądanie do punktu końcowego tokenu usługi Azure AD, który zawiera kod autoryzacji, szczegółowych informacji o aplikacji klienta (identyfikator aplikacji i identyfikator URI przekierowania) i żądanego zasobu (aplikacji identyfikator URI dla Interfejs API sieci Web).
1. Kod autoryzacji i informacji na temat interfejsu API sieci web i aplikacji klienta są weryfikowane przez usługę Azure AD. Po pomyślnym zweryfikowaniem usługi Azure AD zwraca dwa tokeny: token JWT dostępu i token odświeżania JWT. Ponadto usługi Azure AD zwraca podstawowe informacje o użytkowniku, takie jak ich wyświetlaną nazwę i dzierżawy identyfikatorów.
1. Za pośrednictwem protokołu HTTPS aplikacja kliencka używa zwrócony tokenu dostępu JWT, aby dodać ciąg tokenu JWT z oznaczeniem "Bearer" w nagłówku autoryzacji żądania do interfejsu API sieci web. Interfejs API sieci web sprawdza poprawność tokenu JWT i Jeśli weryfikacja zakończy się pomyślnie, zwraca wartość żądanego zasobu.
1. Po wygaśnięciu tokenu dostępu, aplikacja kliencka zostanie wyświetlony błąd, który wskazuje, że użytkownik musi uwierzytelnić się ponownie. Aplikacja ma token odświeżania nieprawidłowy, może służyć uzyskać nowy token dostępu bez monitowania użytkownika, aby zalogować się ponownie. Jeśli token odświeżania, aplikacji należy ponownie interakcyjnego uwierzytelniania użytkownika.

> [!NOTE]
> Token odświeżania wystawiony przez usługę Azure AD można uzyskać dostęp do wielu zasobów. Na przykład jeśli masz aplikacji klienckiej, która ma uprawnienia do wywoływania dwóch interfejsów API sieci web token odświeżania można uzyskać dostęp do tokenu do innych sieci web interfejsu API oraz.

#### <a name="code-samples"></a>Przykłady kodu

Zobacz przykłady kodu dla aplikacji natywnej do scenariuszy interfejsu Web API. I wrócić tu często — często dodamy nowe próbki. [Aplikacji natywnej do interfejsu API sieci Web](active-directory-code-samples.md#desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api).

#### <a name="registering"></a>Rejestrowanie

* Pojedynczej dzierżawy: Natywnego aplikacji i interfejs API sieci web musi być zarejestrowana w tym samym katalogu w usłudze Azure AD. Interfejs API sieci web można skonfigurować do udostępnienia zestaw uprawnień, które są używane do ograniczenia dostępu aplikacji natywnej do jej zasobów. Aplikacja kliencka następnie wybiera odpowiednie uprawnienia, z menu rozwijanego "Uprawnień do innych aplikacji" w portalu Azure.
* Wielodostępne: Po raz pierwszy, aplikacji natywnej tylko zarejestrowane w katalogu wydawcy lub dewelopera. Po drugie natywnych aplikacji skonfigurowano wskazać uprawnienia, które wymaga, aby działała. Ta lista wymaganych uprawnień jest wyświetlany w oknie dialogowym, jeśli użytkownik lub administrator w katalogu docelowym daje zgody do aplikacji, która udostępnia w organizacji. Niektóre aplikacje wymagają tylko uprawnienia na poziomie użytkownika, które każdy użytkownik w organizacji można wyrazić zgodę na. Inne aplikacje wymagają uprawnień na poziomie administratora, które użytkownik w organizacji nie można wyrazić zgodę na. Tylko administrator katalogu można wyrazić zgodę dla aplikacji, które wymagają tego poziomu uprawnień. Po wyrażeniu zgody przez użytkownika lub administratora, tylko interfejs API sieci web jest zarejestrowany w ich katalogu. Aby uzyskać więcej informacji, zobacz [integracji aplikacji z usługą Azure Active Directory](active-directory-integrating-applications.md).

#### <a name="token-expiration"></a>Wygaśnięcia tokenu

Gdy aplikacji natywnej korzysta z jego kod autoryzacji, Uzyskaj token dostępu JWT, ponadto otrzymuje token odświeżania JWT. Po wygaśnięciu tokenu dostępu, token odświeżania może służyć do ponownego uwierzytelnienia użytkownika bez konieczności je, aby zalogować się ponownie. Ten token odświeżania jest następnie używany do uwierzytelniania użytkownika, który powoduje nowy token dostępu i token odświeżania.

### <a name="web-application-to-web-api"></a>Aplikacja sieci Web do interfejsu API sieci web

W tej sekcji opisano aplikacji sieci web, które należy uzyskać zasobów z interfejsu API sieci web. W tym scenariuszu istnieją dwa typy tożsamości, które aplikacji sieci web można użyć do uwierzytelniania i wywołania interfejsu API sieci web: tożsamość aplikacji lub tożsamości użytkownika delegowanego.

*Tożsamość aplikacji:* przyznania poświadczeń klienta OAuth 2.0 w tym scenariuszu są używane do uwierzytelnienia się jako aplikacji i dostępu do interfejsu API sieci web. Korzystając z tożsamością aplikacji sieci web interfejsu API tylko może wykryć, czy wywołanie aplikacji sieci web, co sieć web interfejsu API nie otrzymuje żadnych informacji o użytkowniku. Jeśli aplikacja odbiera informacje o użytkowniku, zostaną wysłane za pośrednictwem protokołu aplikacji, a nie jest podpisany przez usługę Azure AD. Interfejs API sieci web ufa, że aplikacja sieci web uwierzytelnił użytkownika. Z tego powodu ten wzorzec jest wywoływana zaufany podsystem.

*Delegowane tożsamości użytkownika:* w tym scenariuszu można przeprowadzić na dwa sposoby: OpenID Connect i udzielania kodu autoryzacji protokołu OAuth 2.0 z klientem poufne. Aplikacja sieci web uzyskuje token dostępu dla użytkownika, co gwarantuje sieci web interfejsu API, że użytkownik pomyślnie uwierzytelniony do aplikacji sieci web i aplikacji sieci web był w stanie uzyskać identyfikatora użytkownika delegowane do wywołania interfejsu API sieci web. Ten token dostępu jest wysyłane żądania sieci Web interfejsu API, które udzielają użytkownikowi autoryzacji i zwraca wartość żądanego zasobu.

#### <a name="diagram"></a>Diagram

![Aplikacja sieci Web do diagramu interfejsu API sieci Web](./media/active-directory-authentication-scenarios/web_app_to_web_api.png)

#### <a name="description-of-protocol-flow"></a>Opis protokołu przepływu

W poniższym przepływie omówiono tożsamości aplikacji i typów tożsamości użytkownika delegowanego. Najważniejsza różnica między nimi jest, że tożsamość użytkownika delegowanego najpierw muszą uzyskać kod autoryzacji, zanim użytkownik może zalogować i uzyskać dostęp do interfejsu API sieci web.

##### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Przyznaj tożsamość aplikacji przy użyciu poświadczeń klienta OAuth 2.0

1. Użytkownik jest zalogowany do usługi Azure AD w aplikacji sieci web (zobacz [przeglądarki sieci Web do aplikacji sieci Web](#web-browser-to-web-application) powyżej).
1. Aplikacja sieci web musi uzyskać token dostępu, który można uwierzytelniać się na interfejsie API sieci web i pobrać żądanego zasobu. Go żąda token punktu końcowego usługi Azure AD, podanie poświadczeń, identyfikator aplikacji i aplikacji sieci web interfejsu API identyfikator URI.
1. Usługi Azure AD uwierzytelnia aplikacji i zwraca token dostępu JWT, który służy do wywołania interfejsu API sieci web.
1. Przy użyciu protokołu HTTPS aplikacji sieci web używa zwróconych tokenu dostępu JWT Aby dodać ciąg tokenu JWT z oznaczeniem "Bearer" w nagłówku autoryzacji żądania do interfejsu API sieci web. Interfejs API sieci web sprawdza poprawność tokenu JWT i Jeśli weryfikacja zakończy się pomyślnie, zwraca wartość żądanego zasobu.

##### <a name="delegated-user-identity-with-openid-connect"></a>Tożsamość użytkownika delegowanego z OpenID Connect

1. Użytkownik jest zalogowany do aplikacji sieci web przy użyciu usługi Azure AD (zobacz [przeglądarki sieci Web do aplikacji sieci Web](#web-browser-to-web-application) powyższej sekcji). Jeśli użytkownik aplikacji sieci web nie ma jeszcze zgodę na zezwolenie aplikacji sieci web do wywołania interfejsu API sieci web w jego imieniu, użytkownik musi wyrazić zgodę. Aplikacja Wyświetla uprawnienia, które wymaga, a jeśli którakolwiek z tych uprawnień na poziomie administratora, zwykłego użytkownika w katalogu nie będzie mógł zgody. Ten proces zgody dotyczy tylko wielodostępne, aplikacje nie pojedynczej dzierżawy jako aplikacja będzie już ma niezbędne uprawnienia. Gdy użytkownik jest zalogowany, aplikacji sieci web odebrał identyfikator tokenu z informacjami o użytkownika, a także kod autoryzacji.
1. Przy użyciu kodu autoryzacji wystawiony przez usługę Azure AD, aplikacji sieci web wysyła żądanie do punktu końcowego tokenu usługi Azure AD, który zawiera kod autoryzacji, szczegółowe informacje o aplikacji klienta (identyfikator aplikacji i identyfikator URI przekierowania) i żądanego zasobu (identyfikator aplikacji Identyfikator URI dla interfejsu API sieci web).
1. Kod autoryzacji i informacji na temat interfejsu API sieci web i aplikacji sieci web są weryfikowane przez usługę Azure AD. Po pomyślnym zweryfikowaniem usługi Azure AD zwraca dwa tokeny: token JWT dostępu i token odświeżania JWT.
1. Przy użyciu protokołu HTTPS aplikacji sieci web używa zwróconych tokenu dostępu JWT Aby dodać ciąg tokenu JWT z oznaczeniem "Bearer" w nagłówku autoryzacji żądania do interfejsu API sieci web. Interfejs API sieci web sprawdza poprawność tokenu JWT i Jeśli weryfikacja zakończy się pomyślnie, zwraca wartość żądanego zasobu.

##### <a name="delegated-user-identity-with-oauth-20-authorization-code-grant"></a>Tożsamość użytkownika delegowanego z udzielania kodu autoryzacji protokołu OAuth 2.0

1. Użytkownik jest już zalogowany do aplikacji sieci web, w których mechanizm uwierzytelniania jest niezależna od usługi Azure AD.
1. Aplikacja sieci web wymaga kod autoryzacji do uzyskania tokenu dostępu, więc emituje żądanie za pomocą przeglądarki na punkt końcowy autoryzacji usługi Azure AD, podając identyfikator aplikacji i identyfikator URI przekierowania dla aplikacji sieci web po pomyślnym uwierzytelnieniu. Użytkownik loguje się do usługi Azure AD.
1. Jeśli użytkownik aplikacji sieci web nie ma jeszcze zgodę na zezwolenie aplikacji sieci web do wywołania interfejsu API sieci web w jego imieniu, użytkownik musi wyrazić zgodę. Aplikacja Wyświetla uprawnienia, które wymaga, a jeśli którakolwiek z tych uprawnień na poziomie administratora, zwykłego użytkownika w katalogu nie będzie mógł zgody. Tej zgody ma zastosowanie do aplikacji w jednym i wieloma dzierżawami. W przypadku pojedynczej dzierżawy administrator może wykonywać zgody na zgody administratora w imieniu użytkowników. Można to zrobić za pomocą `Grant Permissions` przycisk [Azure Portal](https://portal.azure.com). 
1. Po użytkownik zgodził, aplikacji sieci web otrzymuje kod autoryzacji, który należy uzyskać token dostępu.
1. Przy użyciu kodu autoryzacji wystawiony przez usługę Azure AD, aplikacji sieci web wysyła żądanie do punktu końcowego tokenu usługi Azure AD, który zawiera kod autoryzacji, szczegółowe informacje o aplikacji klienta (identyfikator aplikacji i identyfikator URI przekierowania) i żądanego zasobu (identyfikator aplikacji Identyfikator URI dla interfejsu API sieci web).
1. Kod autoryzacji i informacji na temat interfejsu API sieci web i aplikacji sieci web są weryfikowane przez usługę Azure AD. Po pomyślnym zweryfikowaniem usługi Azure AD zwraca dwa tokeny: token JWT dostępu i token odświeżania JWT.
1. Przy użyciu protokołu HTTPS aplikacji sieci web używa zwróconych tokenu dostępu JWT Aby dodać ciąg tokenu JWT z oznaczeniem "Bearer" w nagłówku autoryzacji żądania do interfejsu API sieci web. Interfejs API sieci web sprawdza poprawność tokenu JWT i Jeśli weryfikacja zakończy się pomyślnie, zwraca wartość żądanego zasobu.

#### <a name="code-samples"></a>Przykłady kodu

Zobacz przykłady kodu dla aplikacji sieci Web w scenariuszach interfejsu API sieci Web. A wrócić tu często — często są dodawane nowe próbki. Web [aplikacji sieci Web interfejsu API](active-directory-code-samples.md#web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity).

#### <a name="registering"></a>Rejestrowanie

* Pojedynczej dzierżawy: Dla tożsamości aplikacji i użytkownika delegowanej tożsamości przypadków, interfejsu API sieci web i aplikacji sieci web musi być zarejestrowany w tym samym katalogu w usłudze Azure AD. Interfejs API sieci web można skonfigurować do udostępnienia zestaw uprawnień, które służą do ograniczania dostępu do aplikacji sieci web do jej zasobów. Jeśli używany jest typ tożsamości użytkownika delegowanego, aplikacji sieci web musi wybrać żądane uprawnienia z menu rozwijanego "Uprawnień do innych aplikacji" w portalu Azure. Ten krok nie jest wymagany, jeśli jest używany typ tożsamości aplikacji.
* Wielodostępne: Najpierw aplikacji sieci web skonfigurowano wskazać uprawnienia, które wymaga, aby działała. Ta lista wymaganych uprawnień jest wyświetlany w oknie dialogowym, jeśli użytkownik lub administrator w katalogu docelowym daje zgody do aplikacji, która udostępnia w organizacji. Niektóre aplikacje wymagają tylko uprawnienia na poziomie użytkownika, które każdy użytkownik w organizacji można wyrazić zgodę na. Inne aplikacje wymagają uprawnień na poziomie administratora, które użytkownik w organizacji nie można wyrazić zgodę na. Tylko administrator katalogu można wyrazić zgodę dla aplikacji, które wymagają tego poziomu uprawnień. Po wyrażeniu zgody przez użytkownika lub administratora, interfejsu API sieci web i aplikacji sieci web są oba zarejestrowane w ich katalogu.

#### <a name="token-expiration"></a>Wygaśnięcia tokenu

Gdy aplikacja sieci web używa jej kod autoryzacji do Uzyskaj token dostępu JWT, ponadto otrzymuje token odświeżania JWT. Po wygaśnięciu tokenu dostępu, token odświeżania może służyć do ponownego uwierzytelnienia użytkownika bez konieczności je, aby zalogować się ponownie. Ten token odświeżania jest następnie używany do uwierzytelniania użytkownika, który powoduje nowy token dostępu i token odświeżania.

### <a name="daemon-or-server-application-to-web-api"></a>Demon lub serwera aplikacji interfejsu API sieci web

W tej sekcji opisano demon lub serwera aplikacji, która musi pobrać zasobów z interfejsu API sieci web. Istnieją dwa scenariusze podrzędne, które mają zastosowanie do tej sekcji: demon, który musi wywołać składnika web API oparty na typ przydziału poświadczeń klienta OAuth 2.0; i aplikacji serwera (np. interfejsu API sieci web), który musi wywołać składnika web API oparty na OAuth 2.0 On-Behalf-Of specyfikacji wersji roboczej.

Scenariusz podczas aplikacji demon musi wywołać składnika web API, jest wziąć pod uwagę kilka rzeczy. Po pierwsze interakcji z użytkownikiem nie jest możliwe z aplikacją demon, które wymaga, aby mieć własną tożsamość aplikacja. Przykładem aplikacji demon jest zadanie wsadowe lub usługa systemu operacyjnego działającej w tle. Aplikacje tego typu żądania tokenu dostępu przy użyciu tożsamości aplikacji i umożliwienie korzystania z jego identyfikator aplikacji, dostęp do poświadczeń (hasło lub certyfikat) i aplikacji identyfikator URI do usługi Azure AD. Po pomyślnym uwierzytelnieniu demona odbiera token dostępu z usługi Azure AD, które są następnie używane do wywoływania interfejsu API sieci web.

Dla tego scenariusza gdy aplikacja serwera wymaga wywoływanie składnika web API, warto użyć przykładu. Załóżmy, że użytkownik został uwierzytelniony w natywnej aplikacji i tej aplikacji natywnej musi wywołać interfejs API sieci web. Usługa Azure AD wystawia token JWT dostępu do wywołania interfejsu API sieci web. Jeśli interfejs API sieci web musi wywołać inny podrzędny interfejs API sieci web, imieniu-przepływ "w" można użyć do delegowania tożsamości użytkownika i uwierzytelniania na sekundę warstwy interfejsu API sieci web.

#### <a name="diagram"></a>Diagram

![Demon lub aplikacji serwera do interfejsu API sieci Web diagramu](./media/active-directory-authentication-scenarios/daemon_server_app_to_web_api.png)

#### <a name="description-of-protocol-flow"></a>Opis protokołu przepływu

##### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Przyznaj tożsamość aplikacji przy użyciu poświadczeń klienta OAuth 2.0

1. Najpierw aplikacji serwera wymaga uwierzytelniania za pomocą usługi Azure AD jako, bez interakcji ludzi, takich jak interakcyjne logowania jednokrotnego w oknie dialogowym. Go żąda token punktu końcowego usługi Azure AD, podanie poświadczeń, identyfikator aplikacji i identyfikator URI aplikacji.
1. Usługi Azure AD uwierzytelnia aplikacji i zwraca token dostępu JWT, który służy do wywołania interfejsu API sieci web.
1. Przy użyciu protokołu HTTPS aplikacji sieci web używa zwróconych tokenu dostępu JWT Aby dodać ciąg tokenu JWT z oznaczeniem "Bearer" w nagłówku autoryzacji żądania do interfejsu API sieci web. Interfejs API sieci web sprawdza poprawność tokenu JWT i Jeśli weryfikacja zakończy się pomyślnie, zwraca wartość żądanego zasobu.

##### <a name="delegated-user-identity-with-oauth-20-on-behalf-of-draft-specification"></a>Tożsamość użytkownika delegowanego z OAuth 2.0 On-Behalf-Of projekt specyfikacji

Przepływ omówiony poniżej założono, że użytkownik został uwierzytelniony w innej aplikacji (na przykład aplikacji natywnej) i tożsamości użytkownika został użyty do uzyskania tokenu dostępu do interfejsu API sieci web warstwy pierwszej.

1. Aplikacji natywnej wysyła ten token dostępu do interfejsu API sieci web warstwy pierwszej.
1. Interfejs API sieci web warstwy pierwszej wysyła żądanie do tokenu punktu końcowego usługi Azure AD, podając jego identyfikator aplikacji i poświadczenia, a także tokenu dostępu. Ponadto, żądanie zostanie wysłane z on_behalf_of parametrem, który wskazuje sieci web interfejsu API żąda nowe tokeny do wywołania podrzędne składnika web API imieniu pierwotnego użytkownika.
1. Usługi Azure AD sprawdza, czy interfejs API sieci web warstwy pierwszej ma uprawnienia dostępu do interfejsu API sieci web podrzędne i weryfikuje żądanie zwracanie tokenu JWT dostępu i token JWT odświeżać token do interfejsu API sieci web warstwy pierwszej.
1. Przy użyciu protokołu HTTPS interfejsu API sieci web warstwy pierwszej wywołuje interfejs API sieci web warstwy sekundę ciąg tokenu w nagłówku autoryzacji w żądaniu. Interfejs API sieci web warstwy pierwszej mogą w dalszym ciągu wywołania interfejsu API sieci web warstwy sekundę tak długo, jak uzyskać token dostępu i tokenów odświeżania są prawidłowe.

#### <a name="code-samples"></a>Przykłady kodu

Zobacz przykłady kodu dla demon lub aplikacji serwera do interfejsu API sieci Web scenariuszy. A wrócić tu często — często są dodawane nowe próbki. [Serwera lub demon aplikacji interfejsu API sieci Web](active-directory-code-samples.md#daemon-applications-accessing-web-apis-with-the-applications-identity)

#### <a name="registering"></a>Rejestrowanie

* Pojedynczej dzierżawy: Dla tożsamości aplikacji i użytkownika delegowanej tożsamości przypadków, demon lub serwera aplikacji musi być zarejestrowany w tym samym katalogu w usłudze Azure AD. Interfejs API sieci web można skonfigurować do udostępnienia zestaw uprawnień, które służą do ograniczania demona lub serwera dostępu do zasobów. Jeśli używany jest typ tożsamości użytkownika delegowanego, aplikacja serwera musi wybierz żądane uprawnienia z menu rozwijanego "Uprawnień do innych aplikacji" w portalu Azure. Ten krok nie jest wymagany, jeśli jest używany typ tożsamości aplikacji.
* Wielodostępne: Najpierw demon lub serwera aplikacji skonfigurowano wskazać uprawnienia, które wymaga, aby działała. Ta lista wymaganych uprawnień jest wyświetlany w oknie dialogowym, jeśli użytkownik lub administrator w katalogu docelowym daje zgody do aplikacji, która udostępnia w organizacji. Niektóre aplikacje wymagają tylko uprawnienia na poziomie użytkownika, które każdy użytkownik w organizacji można wyrazić zgodę na. Inne aplikacje wymagają uprawnień na poziomie administratora, które użytkownik w organizacji nie można wyrazić zgodę na. Tylko administrator katalogu można wyrazić zgodę dla aplikacji, które wymagają tego poziomu uprawnień. Po wyrażeniu zgody przez użytkownika lub administratora, zarówno z interfejsów API sieci web są rejestrowane w ich katalogu.

#### <a name="token-expiration"></a>Wygaśnięcia tokenu

Podczas pierwszej aplikacji korzysta z kodu autoryzacji, Uzyskaj token dostępu JWT, ponadto otrzymuje token odświeżania JWT. Po wygaśnięciu tokenu dostępu, token odświeżania może służyć do ponownego uwierzytelnienia użytkownika bez wyświetlania monitu o poświadczenia. Ten token odświeżania jest następnie używany do uwierzytelniania użytkownika, który powoduje nowy token dostępu i token odświeżania.

## <a name="see-also"></a>Zobacz też

[Przewodnik dewelopera usługi Azure Active Directory](active-directory-developers-guide.md)

[Przykłady kodu usługi Azure Active Directory](active-directory-code-samples.md)

[Ważne informacje dotyczące podpisywania przerzucania kluczy w usłudze Azure AD](active-directory-signing-key-rollover.md)

[OAuth 2.0 w usłudze Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx)
