---
title: Scenariusze uwierzytelniania dla usługi Azure AD | Dokumentacja firmy Microsoft
description: Zawiera omówienie pięciu najbardziej typowe scenariusze uwierzytelniania usługi Azure Active Directory (Azure AD)
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
ms.date: 07/26/2018
ms.author: celested
ms.reviewer: jmprieur, andret, nacanuma, hirsin
ms.custom: aaddev
ms.openlocfilehash: 2ef92a6cd36f4d28aaf8b0b6b5061574d7ecf82d
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39444288"
---
# <a name="authentication-scenarios-for-azure-ad"></a>Scenariusze uwierzytelniania dla usługi Azure AD

Azure Active Directory (Azure AD) ułatwia uwierzytelnianie dla deweloperów, zapewniając tożsamość jako usługa z obsługą protokoły będące standardami branżowymi, takie jak OAuth 2.0 i OpenID Connect, a także typu open-source biblioteki dla różnych platform ułatwić Rozpocznij szybko kodowania. Ten artykuł pomoże Ci zrozumieć różne obsługuje scenariusze usługi Azure AD i dowiesz się, jak rozpocząć pracę. Jest ona podzielony na następujące sekcje:

* [Podstawowe informacje dotyczące uwierzytelniania w usłudze Azure AD](#basics-of-authentication-in-azure-ad)
* [Oświadczenia w tokeny zabezpieczeń usługi Azure AD](#claims-in-azure-ad-security-tokens)
* [Podstawowe informacje dotyczące rejestrowania aplikacji w usłudze Azure AD](#basics-of-registering-an-application-in-azure-ad)
* [Typy aplikacji i scenariuszy](#application-types-and-scenarios)

  * [Przeglądarki sieci Web do aplikacji sieci web](#web-browser-to-web-application)
  * [Aplikacja jednostronicowa (SPA)](#single-page-application-spa)
  * [Aplikacja natywna do internetowego interfejsu API](#native-application-to-web-api)
  * [Aplikacja sieci Web do internetowego interfejsu API](#web-application-to-web-api)
  * [Demon lub serwera aplikacji do internetowego interfejsu API](#daemon-or-server-application-to-web-api)

## <a name="basics-of-authentication-in-azure-ad"></a>Podstawowe informacje dotyczące uwierzytelniania w usłudze Azure AD

Jeśli znasz podstawowe pojęcia dotyczące środowiska uwierzytelniania w usłudze Azure AD, przeczytaj tę sekcję. W przeciwnym razie możesz chcieć od razu przejść w dół do [typów aplikacji i scenariuszy](#application-types-and-scenarios).

Rozważmy najprostszy scenariusz, w której wymagana jest tożsamość: użytkownika w przeglądarce sieci web wymaga uwierzytelnienia aplikacji sieci web. Ten scenariusz jest opisany bardziej szczegółowo w [przeglądarki sieci Web do aplikacji sieci web](#web-browser-to-web-application) sekcji, ale użytkownika stanowi dobry punkt wyjścia celu przedstawienia możliwości usługi Azure AD i wyobrażenie sposobu działania tego scenariusza. Poniższy diagram w tym scenariuszu należy wziąć pod uwagę:

![Omówienie logowania jednokrotnego do aplikacji sieci web](./media/active-directory-authentication-scenarios/basics_of_auth_in_aad.png)

Dzięki powyższym diagramie pamiętając poniżej przedstawiono, co musisz wiedzieć o jego składnikami:

* Usługa Azure AD jest dostawcy tożsamości, która odpowiada za weryfikację tożsamości użytkowników i aplikacje, które istnieją w katalogu organizacji, a ostatecznie wystawianie tokenów zabezpieczających po pomyślnym uwierzytelnieniu tych użytkowników i aplikacji.
* Aplikacja, która chce oddelegowania procesów uwierzytelniania do usługi Azure AD, musi być zarejestrowany w usłudze Azure AD, który rejestruje i unikatowo identyfikuje aplikację w katalogu.
* Deweloperzy mogą używać biblioteki uwierzytelniania typu open-source w usłudze Azure AD, aby ułatwić uwierzytelnianie dzięki obsłudze szczegółów protokołu dla Ciebie. Aby uzyskać więcej informacji, zobacz [bibliotek uwierzytelniania usługi Azure Active Directory](active-directory-authentication-libraries.md).
* Po uwierzytelnieniu użytkownika aplikacja musi go zweryfikować tokenu zabezpieczeń użytkownika, aby upewnić się, że uwierzytelnianie zakończyło się pomyślnie. Mamy przykłady, co musi robić aplikacja w różnych języków i struktur w [GitHub](https://github.com/Azure-Samples?q=active-directory). Jeśli tworzysz aplikację sieci web na platformie ASP.NET, zobacz [dodać logowanie dla przewodnika aplikacji sieci web platformy ASP.NET](https://docs.microsoft.com/en-us/azure/active-directory/develop/guidedsetups/active-directory-aspnetwebapp). Jeśli tworzysz zasobu internetowego interfejsu API na platformie ASP.NET, zobacz [interfejsu API sieci web — wprowadzenie](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-devquickstarts-webapi-dotnet).
* Przepływ żądań i odpowiedzi do procesu uwierzytelniania jest określana przez protokół uwierzytelniania, który został użyty, takich jak OAuth 2.0, OpenID Connect, WS-Federation oraz SAML 2.0. Te protokoły są omówione bardziej szczegółowo w [protokoły uwierzytelniania usługi Azure Active Directory](active-directory-authentication-protocols.md) artykułu w poniższych sekcjach.

> [!NOTE]
> Usługa Azure AD obsługuje protokołu OAuth 2.0 i OpenID Connect standardy, które rozbudowane użytkowania tokenów elementu nośnego, łącznie z tokenów elementu nośnego reprezentowane jako tokenów Jwt. A *tokenu elementu nośnego* jest tokenem zabezpieczającym uproszczone, która udziela dostępu "bearer" do chronionego zasobu. W tym sensie "bearer" to każda strona, która może powodować tokenu. Chociaż strona muszą najpierw zostać uwierzytelnione za pomocą usługi Azure AD, aby otrzymać token elementu nośnego, jeśli nie podejmuje kroki wymagane do zabezpieczania token w transmisji i przechowywania, można przechwycony i używane przez niezamierzone innych firm. Podczas gdy niektóre tokeny zabezpieczające mają wbudowany mechanizm uniemożliwia ich użycie przez osoby nieupoważnione, tokenów elementu nośnego nie mają ten mechanizm i musi być transportowane bezpiecznego kanału, takie jak transport layer security (HTTPS). Jeśli token elementu nośnego jest przekazywane w zabezpieczeniu, atak typu man-in--middle może służyć przez złośliwa strona do uzyskania tokenu i użyć jej do nieautoryzowanego dostępu do chronionego zasobu. Te same zasady zabezpieczeń stosowane, gdy przechowywania lub buforowanie tokenów elementu nośnego do późniejszego użycia. Zawsze upewnij się, że aplikacja przesyła i przechowuje tokenów elementu nośnego w bezpieczny sposób. Aby uzyskać więcej zagadnienia dotyczące zabezpieczeń na tokenów elementu nośnego, zobacz [RFC 6750 sekcji 5](http://tools.ietf.org/html/rfc6750).

Teraz, gdy masz Przegląd wykonywać podstawowe zadania, przeczytaj poniższe sekcje, aby zrozumieć sposób aprowizacji działa w usłudze Azure AD i typowych scenariuszy, które obsługuje usługę Azure AD.

## <a name="claims-in-azure-ad-security-tokens"></a>Oświadczenia w tokeny zabezpieczeń usługi Azure AD

Tokeny zabezpieczające (dostępu i identyfikator tokenów) wydane przez usługę Azure AD zawierają oświadczenia lub potwierdzenia informacji na temat, który został uwierzytelniony. Te oświadczenia może służyć przez aplikację dla różnych zadań. Na przykład aplikacji można użyć oświadczeń do weryfikacji tokenu, identyfikowania dzierżawy katalogu podmiotu, wyświetlaj informacje o użytkowniku, określa autoryzację podmiotu i tak dalej. Oświadczenia obecne w dowolnej tokenu zabezpieczeń są zależne od typu tokenu, typ poświadczenia używane do uwierzytelniania użytkownika i konfiguracji aplikacji. W poniższej tabeli znajduje się krótki opis każdego typu oświadczenia emitowane przez usługę Azure AD. Aby uzyskać więcej informacji, zobacz [obsługiwane typy tokenów i oświadczeń](active-directory-token-and-claims.md).

| Claim | Opis |
| --- | --- |
| Identyfikator aplikacji | Umożliwia określenie aplikacji, która jest przy użyciu tokenu. |
| Grupy odbiorców | Identyfikuje zasób odbiorcy, do którego token jest przeznaczony dla. |
| Application Authentication Context Class Reference | Wskazuje, jak klient został uwierzytelniony (publicznych klienta a poufne klienta). |
| Błyskawiczne uwierzytelniania | Rejestruje datę i godzinę wystąpienia uwierzytelniania. |
| Metoda uwierzytelniania | Wskazuje, jak został uwierzytelniony podmiot tokenu (hasła, certyfikat itp.). |
| Imię | Zawiera imię użytkownika jako zestaw w usłudze Azure AD. |
| Grupy | Zawiera grupy identyfikatorów programu Azure AD obiektów, które użytkownik jest członkiem. |
| Dostawca tożsamości | Rejestruje dostawcę tożsamości, którego uwierzytelniony podmiot tokenu. |
| Wydane w | Rejestruje czas, w którym token został wystawiony, często używane dla tokenu świeżości. |
| Wystawca | Identyfikuje usługi STS, które są emitowane token, a także dzierżawy usługi Azure AD. |
| Nazwisko | Zawiera nazwisko użytkownika jako zestaw w usłudze Azure AD. |
| Name (Nazwa) | Udostępnia ludzi, można odczytać wartość, która identyfikuje podmiotu tokenu. |
| Identyfikator obiektu | Zawiera niezmienne, unikatowy identyfikator tematu w usłudze Azure AD. |
| Role | Zawiera przyjazne nazwy aplikacji role usługi Azure AD, któremu udzielono użytkownika. |
| Zakres | Wskazuje uprawnienia przyznane aplikacji klienckiej. |
| Temat | Określa jednostkę o tym, które token określa informacje. |
| Identyfikator dzierżawy | Zawiera niezmienne, unikatowy identyfikator dzierżawy katalogu, który wystawił token. |
| Okres istnienia tokenu | Definiuje przedział czasu, w którym token jest prawidłowy. |
| Nazwa główna użytkownika | Zawiera nazwę główną użytkownika podmiotu. |
| Wersja | Zawiera numer wersji tokenu. |

## <a name="basics-of-registering-an-application-in-azure-ad"></a>Podstawowe informacje dotyczące rejestrowania aplikacji w usłudze Azure AD

Każda aplikacja, która outsources uwierzytelniania usługi Azure AD, musi być zarejestrowany w katalogu. Ten krok obejmuje informuje usługę Azure AD o swojej aplikacji, łącznie z adresem URL, w którym znajduje się, adres URL do wysyłania odpowiedzi po uwierzytelnieniu identyfikatora URI, aby zidentyfikować aplikację i nie tylko. Ta informacja jest wymagana w kilku kluczowych powodów:

* Usługa Azure AD wymaga do komunikacji z aplikacją podczas obsługi logowania jednokrotnego lub wymianę tokenów. Informacje przesyłane między usługą Azure AD i aplikacja obejmuje następujące elementy:
  
  * **Identyfikator URI Identyfikatora aplikacji** — identyfikator aplikacji. Ta wartość jest wysyłana do usługi Azure AD podczas uwierzytelniania w celu wskazania, która aplikacja obiekt wywołujący chce token dla. Ponadto tak, aby aplikacja wie, że jest zamierzonym obiektem docelowym było tę wartość znajduje się w tokenie.
  * **Adres URL odpowiedzi** i **identyfikator URI przekierowania** — interfejs API sieci web lub aplikacji sieci web, adres URL odpowiedzi to lokalizacja, w którym usługi Azure AD wysyła odpowiedź uwierzytelniania, w tym token, jeśli uwierzytelnianie zakończyło się pomyślnie. W przypadku aplikacji natywnej identyfikator URI przekierowania to unikatowy identyfikator, do którego usługa Azure AD przekierowuje agenta użytkownika w ramach żądania OAuth 2.0.
  * **Identyfikator aplikacji** — identyfikator aplikacji, który jest generowany przez usługę Azure AD, gdy aplikacja jest zarejestrowana. Żądający kod autoryzacji lub tokenu Identyfikatora aplikacji i klucz są wysyłane do usługi Azure AD podczas uwierzytelniania.
  * **Klucz** -key, przesyłany wraz z identyfikator aplikacji podczas uwierzytelniania w usłudze Azure AD do wywołania interfejsu API sieci web.
* Usługa Azure AD wymaga upewnić się, że aplikacja ma wymaganych uprawnień, aby uzyskiwać dostęp do danych katalogu, inne aplikacje w Twojej organizacji i tak dalej.

Inicjowanie obsługi administracyjnej staje się bardziej zrozumiały po zapoznaniu się, że istnieją dwie kategorie aplikacji, które mogą być opracowane i zintegrowane z usługą Azure AD:

* **Pojedyncza aplikacja dzierżawy** — aplikacja jednej dzierżawy jest przeznaczony do użytku w jednej z organizacji. Są to zazwyczaj line-of-business (LoB) aplikacji napisanych przez Deweloper w przedsiębiorstwie. Aplikacja jednej dzierżawy musi tylko uzyskiwać dostęp użytkownicy w jednym katalogu, a w rezultacie wymaga tylko do udostępnienia w pojedynczym katalogu. Te aplikacje zwykle są rejestrowane przez dewelopera w organizacji.
* **Aplikacja wielodostępna** -aplikacji z wieloma dzierżawami jest przeznaczona do użycia w wielu organizacjach nie tylko jednej z organizacji. Są to zazwyczaj software-as-a-service (SaaS) aplikacji napisanych przez niezależnego dostawcę oprogramowania (ISV). Wielodostępne aplikacje muszą być obsługiwana w każdym katalogu gdzie zostaną użyte, wymaga zgody użytkownika lub administratora, aby zarejestrować je. Ten proces zgody rozpoczyna się, gdy aplikacja została zarejestrowana w katalogu i otrzymuje dostęp do interfejsu API programu Graph lub może być inny interfejs API sieci web. Gdy użytkownik lub administrator z innej organizacji zarejestruje się w celu korzystania z aplikacji, zobaczą okno dialogowe, które wyświetla uprawnienia, których wymaga aplikacja. Użytkownik lub administrator, następnie mogą wyrazić zgodę na aplikację, która zapewnia dostęp do aplikacji do podanych danych, a na koniec rejestruje swojego katalogu aplikacji. Aby uzyskać więcej informacji, zobacz [Przegląd Framework zgody](active-directory-integrating-applications.md#overview-of-the-consent-framework).

### <a name="additional-considerations-when-developing-single-tenant-or-multi-tenant-apps"></a>Dodatkowe zagadnienia dotyczące opracowywania pojedynczego dzierżawy lub wielodostępnych aplikacji
Kilka dodatkowych kwestii dotyczących pojawiają się podczas tworzenia aplikacji z wieloma dzierżawami, zamiast aplikacji pojedynczej dzierżawy. Na przykład, jeśli udostępniasz aplikację dla użytkowników w wielu katalogach, potrzebujesz mechanizmu, aby określić, której dzierżawy znajdują się w. Aplikacja jednej dzierżawy musi się tylko do wyszukiwania w katalogu dla użytkownika, gdy aplikacja wielodostępna musi zidentyfikować określonego użytkownika ze wszystkich katalogów w usłudze Azure AD. Aby wykonać to zadanie, usługa Azure AD zapewnia wspólnego punktu końcowego uwierzytelniania gdzie dowolnej aplikacji wielodostępnych można kierować żądań logowania, zamiast punktem końcowym specyficznym dla dzierżawy. Ten punkt końcowy jest https://login.microsoftonline.com/common dla wszystkich katalogów w usłudze Azure AD może być punktem końcowym specyficznym dla dzierżawy https://login.microsoftonline.com/contoso.onmicrosoft.com. Wspólnego punktu końcowego jest szczególnie ważne należy wziąć pod uwagę podczas opracowywania aplikacji, ponieważ będziesz potrzebować logikę potrzebną do obsługi wielu dzierżaw podczas logowania, wylogowywania i walidacji tokenów.

Jeśli aktualnie tworzysz aplikację pojedynczej dzierżawy, ale chcesz udostępnić wiele organizacji, możesz łatwo wprowadzać zmiany do aplikacji, a jej konfiguracji w usłudze Azure AD, aby stał się wieloma dzierżawami stanie. Ponadto usługa Azure AD używa tego samego klucza podpisywania wszystkich tokenów we wszystkich katalogach czy udostępniasz uwierzytelniania w pojedynczej dzierżawy lub w aplikacji wielodostępnej.

Każdy scenariusz wymienione w niniejszym dokumencie zawiera podsekcja, który opisuje jego wymagania inicjowania obsługi administracyjnej. Aby uzyskać więcej szczegółowych informacji o aprowizacji aplikacji w usłudze Azure AD oraz różnice między jednym i wieloma dzierżawami aplikacji, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](active-directory-integrating-applications.md) Aby uzyskać więcej informacji. Kontynuuj czytanie, aby poznać typowe scenariusze aplikacji w usłudze Azure AD.

## <a name="application-types-and-scenarios"></a>Typy aplikacji i scenariuszy

Wszystkich scenariuszy opisanych w tym miejscu mogą być tworzone przy użyciu różnych języków i platform. One są wspierane przez kompletny kod przykładów dostępnych w [Przewodnik po próbkach kodu](active-directory-code-samples.md), lub bezpośrednio z odpowiednich [repozytoriów przykładowe GitHub](https://github.com/Azure-Samples?q=active-directory). Ponadto jeśli aplikacja wymaga konkretne lub segmentu scenariusz end-to-end, w większości przypadków te funkcje można dodać niezależnie. Na przykład w przypadku aplikacji natywnej, która wywołuje internetowy interfejs API łatwo dodać aplikację internetową, która także wywołania interfejsu API sieci web. Na poniższym diagramie przedstawiono te scenariusze i typy aplikacji, oraz w jaki sposób można dodawać różne składniki:

![Typy aplikacji i scenariuszy](./media/active-directory-authentication-scenarios/application_types_and_scenarios.png)

Poniżej przedstawiono pięciu scenariuszy głównej aplikacji obsługiwanych przez usługę Azure AD:

* [Przeglądarki sieci Web do aplikacji sieci web](#web-browser-to-web-application): użytkownik musi zalogować się do aplikacji sieci web, która jest zabezpieczony przez usługę Azure AD.
* [Pojedyncza strona aplikacji (SPA)](#single-page-application-spa): użytkownik musi zalogować się do aplikacji jednostronicowej, która jest zabezpieczony przez usługę Azure AD.
* [Aplikacja natywna do internetowego interfejsu API](#native-application-to-web-api): natywnych aplikacji, która działa na telefonie, tablecie lub komputerze wymaga uwierzytelnienia użytkownika można pobrać zasobów z internetowego interfejsu API, która jest zabezpieczony przez usługę Azure AD.
* [Aplikacja do internetowego interfejsu API sieci Web](#web-application-to-web-api): aplikacja sieci web musi pobrać zasoby z internetowego interfejsu API zabezpieczony przez usługę Azure AD.
* [Demon lub serwera aplikacji do internetowego interfejsu API](#daemon-or-server-application-to-web-api): aplikacji demona albo aplikacja serwera bez interfejsu użytkownika sieci web musi pobrać zasoby z internetowego interfejsu API zabezpieczony przez usługę Azure AD.

### <a name="web-browser-to-web-application"></a>Przeglądarki sieci Web do aplikacji sieci web

W tej sekcji opisano aplikację, która uwierzytelnia użytkownika w przeglądarce sieci web do aplikacji sieci web. W tym scenariuszu aplikacji sieci web poleca przeglądarkę użytkownika do logowania do usługi Azure AD. Usługa Azure AD, zwraca odpowiedź logowania za pośrednictwem przeglądarki przez użytkownika, który zawiera oświadczenia dotyczące użytkownika w tokenie zabezpieczającym. Ten scenariusz obsługuje logowanie jednokrotne przy użyciu protokołów WS-Federation, SAML 2.0 i OpenID Connect.

#### <a name="diagram"></a>Diagram

![Przebieg uwierzytelniania w przypadku aplikacji sieci web w przeglądarce](./media/active-directory-authentication-scenarios/web_browser_to_web_api.png)

#### <a name="description-of-protocol-flow"></a>Opis protokołu przepływu

1. Gdy użytkownik odwiedzi aplikacji i potrzeb, aby zalogować się, zostanie przekierowany za pośrednictwem żądania logowania do uwierzytelniania punktu końcowego w usłudze Azure AD.
1. Użytkownik loguje się na stronie logowania.
1. Jeśli uwierzytelnianie powiedzie się, tworzy token uwierzytelniania usługi Azure AD i zwraca odpowiedź Zaloguj się do adresu URL odpowiedzi aplikacji, który został skonfigurowany w witrynie Azure portal. W przypadku aplikacji produkcyjnych następujący adres URL odpowiedzi powinien być schemat HTTPS. Zwrócony token zawiera oświadczenia dotyczące użytkownika a usługą Azure AD, które są wymagane przez aplikację w celu zweryfikowania tokenu.
1. Aplikacja sprawdza poprawność tokenu przy użyciu publicznego klucza podpisywania i Wystawca informacji dostępnych w dokumentu metadanych Federacji usługi Azure AD. Po Aplikacja sprawdza poprawność tokenu, zaczyna nową sesję użytkownika. Ta sesja umożliwia użytkownikowi dostęp do aplikacji, dopóki nie wygaśnie.

#### <a name="code-samples"></a>Przykłady kodu

Zobacz przykłady kodu przeglądarki sieci Web, scenariusze aplikacji sieci Web. I, zajrzyj tu często, ponieważ często dodawane są nowe przykłady. [Aplikacja sieci Web](active-directory-code-samples.md#web-applications).

#### <a name="registering"></a>Rejestrowanie

* Pojedynczej dzierżawy: Jeśli tworzysz aplikację tylko dla Twojej organizacji, musi być zarejestrowana w katalogu firmy przy użyciu witryny Azure portal.
* Wielodostępne: Jeśli tworzysz aplikację, która może być używane przez użytkowników spoza organizacji, ten musi być zarejestrowana w katalogu firmy, ale musi być zarejestrowana w każdej organizacji katalogu, który będzie używany w aplikacji. Aby udostępnić aplikację w jego katalogu, możesz dołączyć procesu rejestracji dla klientów, co pozwala na wyrażanie zgody na aplikację. Po utworzeniu konta dla aplikacji, zobaczą okno dialogowe, które przedstawiono uprawnienia, których wymaga aplikacja i opcji do wyrażenia zgody. W zależności od wymagane uprawnienia administratora w innej organizacji może być konieczne wyrazić zgodę. Po użytkownik lub administrator wyraża zgodę, aplikacja jest zarejestrowana w ich katalogu. Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji w usłudze Azure Active Directory](active-directory-integrating-applications.md).

#### <a name="token-expiration"></a>Wygaśnięcie tokenu

Sesja użytkownika wygasa po upływie okresu istnienia tokenu wystawionego przez usługę Azure AD. Aplikację można skrócić tego okresu czasu, jeśli to konieczne, takich jak wylogowywanie użytkowników, w oparciu o okresie braku aktywności. Po wygaśnięciu sesji użytkownika zostanie wyświetlony monit Zaloguj się ponownie.

### <a name="single-page-application-spa"></a>Aplikacja jednostronicowa (SPA)

W tej sekcji opisano uwierzytelnianie dla pojedynczej aplikacji strony, który używa usługi Azure AD i OAuth 2.0 niejawna autoryzacja udzielić secure jego zakończenia interfejsu API z powrotem w sieci web. Aplikacje jednostronicowe zazwyczaj mają strukturę jako Warstwa prezentacji JavaScript (fronton), która działa w przeglądarce i internetowy interfejs API zaplecza jest uruchamiany na serwerze, który implementuje logikę biznesową aplikacji. Aby dowiedzieć się więcej o niejawnym przyznawaniu autoryzacji i pomóc w podjęciu decyzji, czy jest odpowiedni dla scenariusza aplikacji, zobacz [zrozumienie OAuth2 niejawne udzielić przepływ w usłudze Azure Active Directory](active-directory-dev-understanding-oauth2-implicit-grant.md).

W tym scenariuszu, gdy użytkownik się zaloguje, JavaScript frontonu używa zakończenia [Active Directory Authentication Library dla języka JavaScript (biblioteki ADAL. JS)](https://github.com/AzureAD/azure-activedirectory-library-for-js) i niejawnym przyznawaniu autoryzacji można uzyskać tokenu Identyfikatora (id_token) z usługi Azure AD. Token jest buforowana i klient dołącza go do żądania jako token elementu nośnego w wywołaniach jego interfejsu API sieci Web zaplecza, która jest zabezpieczony za pomocą oprogramowania pośredniczącego OWIN. 

#### <a name="diagram"></a>Diagram

![Pojedynczy diagramu strony aplikacji](./media/active-directory-authentication-scenarios/single_page_app.png)

#### <a name="description-of-protocol-flow"></a>Opis protokołu przepływu

1. Użytkownik przechodzi do aplikacji sieci web.
1. Aplikacja zwraca JavaScript frontonu (Warstwa prezentacji) w przeglądarce.
1. Zainicjowaniu logowania użytkownika, na przykład, klikając Link umożliwiający zalogowanie się. Przeglądarka wysyła GET do punktu końcowego autoryzacji usługi Azure AD do żądania tokenu Identyfikatora. To żądanie zawiera adres URL Identyfikatora i odpowiedź aplikacji w parametrach zapytania.
1. Usługa Azure AD sprawdza poprawność adresu URL odpowiedzi względem zarejestrowanych adres URL odpowiedzi, który został skonfigurowany w witrynie Azure portal.
1. Użytkownik loguje się na stronie logowania.
1. Jeśli uwierzytelnianie się powiedzie, usługi Azure AD tworzy token Identyfikatora i zwraca go jako fragmentu adresu URL (#), aby adres URL odpowiedzi aplikacji. W przypadku aplikacji produkcyjnych następujący adres URL odpowiedzi powinien być schemat HTTPS. Zwrócony token zawiera oświadczenia dotyczące użytkownika a usługą Azure AD, które są wymagane przez aplikację w celu zweryfikowania tokenu.
1. Kod klienta JavaScript w przeglądarce wyodrębnia token z odpowiedzi do użycia w zabezpieczanie wywołań do aplikacji sieci web, czy zakończyć interfejsu API z powrotem.
1. Przeglądarka wywołuje aplikacji sieci web interfejsu API z powrotem kończyć się znakiem tokenu Identyfikacyjnego w nagłówku autoryzacji. Usługa uwierzytelniania usługi Azure AD wystawia token Identyfikatora, który może służyć jako token elementu nośnego, jeśli zasób jest taki sam jak identyfikator klienta (w tym przypadku to wartość true, ponieważ internetowy interfejs API zaplecza połączonej aplikacji). 

#### <a name="code-samples"></a>Przykłady kodu

Zobacz przykłady kodu dla scenariuszy z jednej strony aplikacji (SPA). Pamiętaj wrócić tu często, ponieważ często dodawane są nowe przykłady. [Pojedyncza strona aplikacji (SPA)](active-directory-code-samples.md#single-page-applications).

#### <a name="registering"></a>Rejestrowanie

* Pojedynczej dzierżawy: Jeśli tworzysz aplikację tylko dla Twojej organizacji, musi być zarejestrowana w katalogu firmy przy użyciu witryny Azure portal.
* Wielodostępne: Jeśli tworzysz aplikację, która może być używane przez użytkowników spoza organizacji, ten musi być zarejestrowana w katalogu firmy, ale musi być zarejestrowana w każdej organizacji katalogu, który będzie używany w aplikacji. Aby udostępnić aplikację w jego katalogu, możesz dołączyć procesu rejestracji dla klientów, co pozwala na wyrażanie zgody na aplikację. Po utworzeniu konta dla aplikacji, zobaczą okno dialogowe, które przedstawiono uprawnienia, których wymaga aplikacja i opcji do wyrażenia zgody. W zależności od wymagane uprawnienia administratora w innej organizacji może być konieczne wyrazić zgodę. Po użytkownik lub administrator wyraża zgodę, aplikacja jest zarejestrowana w ich katalogu. Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji w usłudze Azure Active Directory](active-directory-integrating-applications.md).

Po zarejestrowaniu aplikacji musi być skonfigurowany do używania protokołu przyznawanie niejawne protokołu OAuth 2.0. Domyślnie ten protokół jest wyłączone dla aplikacji. Aby włączyć protokół przyznawanie niejawne protokołu OAuth2 dla aplikacji, Edytuj manifest aplikacji w witrynie Azure portal, a następnie ustaw wartość "oauth2AllowImplicitFlow" na true. Aby uzyskać szczegółowe instrukcje, zobacz [włączenie protokołu OAuth 2.0 przyznawanie niejawne dla aplikacje jednostronicowe](active-directory-integrating-applications.md).

#### <a name="token-expiration"></a>Wygaśnięcie tokenu

Za pomocą ADAL.js może ułatwić realizację:

* odświeżanie tokenu wygasłe
* żądania tokenu dostępu do wywołania zasobu internetowego interfejsu API

Po pomyślnym uwierzytelnieniu usługa Azure AD zapisuje plik cookie w przeglądarce użytkownika w celu ustanowienia sesji. Należy pamiętać, że sesja istnieje między użytkownikiem a usługa Azure AD (nie między użytkownikiem i aplikacji sieci web). Po wygaśnięciu token ADAL.js korzysta z tej sesji dyskretnie uzyskać inny token. ADAL.js używa ukrytego elementu iFrame, aby wysyłać i odbierać żądania przy użyciu protokołu przyznawanie niejawne protokołu OAuth. ADAL.js umożliwia również ten sam mechanizm dyskretnie uzyskiwanie tokenów dostępu dla innych zasobów interfejsu API sieci web, wywoływanych przez aplikację, tak długo, jak te zasoby pomocy technicznej współużytkowanie zasobów między źródłami (cors), są rejestrowane w katalogu użytkownika, a wszelkie wymagane zgody zostało podane przez użytkownika podczas logowania.

### <a name="native-application-to-web-api"></a>Aplikacja natywna do internetowego interfejsu API

W tej sekcji opisano aplikacji natywnej, która wywołuje internetowy interfejs API w imieniu użytkownika. Ten scenariusz jest oparty na typu przydziału kodu autoryzacji OAuth 2.0 przy użyciu publicznych klienta, zgodnie z opisem w sekcji 4.1 [specyfikację OAuth 2.0](http://tools.ietf.org/html/rfc6749). Aplikacji macierzystej uzyskuje token dostępu dla użytkownika przy użyciu protokołu OAuth 2.0. Ten token dostępu jest następnie wysyłana w żądaniu w internetowym interfejsie API, które udzielają użytkownikowi autoryzacji i zwraca żądanego zasobu.

#### <a name="diagram"></a>Diagram

![Natywną aplikację sieci Web diagramu interfejsu API](./media/active-directory-authentication-scenarios/native_app_to_web_api.png)

#### <a name="description-of-protocol-flow"></a>Opis protokołu przepływu

Jeśli używasz biblioteki uwierzytelniania AD Większość opisanych poniżej szczegółów ma protokołu są obsługiwane, takich jak okno podręczne z przeglądarki, buforowanie tokenów oraz obsługi tokenów odświeżania.

1. Za pomocą aplikacji natywnej wysyła żądanie do punktu końcowego autoryzacji w usłudze Azure AD wyskakujące przeglądarki. To żądanie zawiera identyfikator aplikacji oraz identyfikatora URI przekierowania aplikacji macierzystej, jak pokazano w witrynie Azure portal i identyfikator URI aplikacji interfejsu API sieci web. Jeśli użytkownik nie zostało to zrobione, są monitowani o Zaloguj się ponownie
1. Usługa Azure AD uwierzytelnia użytkownika. Jeśli jest to aplikacja wielodostępna i zgoda jest wymagana do korzystania z aplikacji, użytkownik będą musieli wyrazić zgodę, jeśli ich jeszcze tego nie zrobiłeś. Po przyznaniu zgody i po pomyślnym uwierzytelnieniu usługa Azure AD wystawia odpowiedzi kod autoryzacji do identyfikatora URI przekierowania aplikacji klienta.
1. Gdy usługa Azure AD wysyła odpowiedź kodu autoryzacji do identyfikatora URI przekierowania, aplikacja kliencka zatrzymuje interakcji z przeglądarką i wyodrębnia kod autoryzacji z odpowiedzi. Przy użyciu tego kodu autoryzacji, aplikacja kliencka wysyła żądanie do punktu końcowego tokenu usługi Azure AD, który zawiera kod autoryzacji, szczegółowe informacje o aplikacji klienta (identyfikator aplikacji i identyfikator URI przekierowania) i żądany zasób (identyfikator URI aplikacji dla internetowy interfejs API).
1. Kod autoryzacji i informacji na temat interfejsu API sieci web i aplikacji klienta są weryfikowane przez usługę Azure AD. Po pomyślnej weryfikacji usługi Azure AD zwraca dwa tokeny: token JWT dostępu i token odświeżania tokenu JWT. Ponadto usługa Azure AD zwraca podstawowe informacje o użytkowniku, takie jak ich wyświetlaną nazwę i dzierżawy identyfikator.
1. Przy użyciu protokołu HTTPS aplikacja kliencka używa zwrócony token dostępu tokenów JWT do dodawania ciągu JWT z oznaczeniem "Bearer" w nagłówku autoryzacji żądania do internetowego interfejsu API. Interfejs API sieci web sprawdza poprawność tokenu JWT i, jeśli weryfikacja zakończy się pomyślnie, zwraca żądanego zasobu.
1. Po wygaśnięciu token dostępu, aplikacja kliencka otrzyma błąd, który wskazuje, że użytkownik musi uwierzytelnić się ponownie. Aplikacja ma token odświeżania prawidłowe, może służyć uzyskać nowy token dostępu bez wyświetlania monitu użytkownikowi zalogowanie się ponownie. Jeśli wygaśnięciu ważności tokenu odświeżania aplikacji muszą interaktywnie ponownie uwierzytelnić użytkownika.

> [!NOTE]
> Token odświeżania wydane przez usługę Azure AD może służyć do dostępu do wielu zasobów. Na przykład jeśli masz aplikację kliencką, która ma uprawnienia do wywołania dwóch interfejsów API sieci web, token odświeżania może służyć do uzyskania dostępu do innych internetowy interfejs API oraz token.

#### <a name="code-samples"></a>Przykłady kodu

Zobacz przykłady kodu dla aplikacji natywnej do scenariuszy interfejsu Web API. I wrócić tu często — często dodajemy nowe przykłady. [Aplikacja natywna do internetowego interfejsu API](active-directory-code-samples.md#desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api).

#### <a name="registering"></a>Rejestrowanie

* Pojedynczej dzierżawy: Zarówno natywnych aplikacji i interfejsu API sieci web muszą być zarejestrowane w tym samym katalogu w usłudze Azure AD. Interfejs API sieci web można skonfigurować do udostępnienia zestaw uprawnień, które są używane do ograniczenia aplikacji natywnej dostęp do swoich zasobów. Następnie aplikacja kliencka wybiera odpowiednich uprawnień, z menu rozwijanego "Uprawnienia na inne aplikacje" w witrynie Azure portal.
* Wielodostępne: Najpierw aplikacji natywnej tylko zarejestrowane w dewelopera lub katalog wydawcy. Po drugie natywną aplikację skonfigurowano do wskazania uprawnienia, które wymaga, aby działała prawidłowo. Ta lista wymaganych uprawnień jest wyświetlany w oknie dialogowym po użytkownik lub administrator w katalogu docelowym powoduje zgody aplikacji, która udostępnia je do swojej organizacji. Niektóre aplikacje wymagają tylko uprawnienia na poziomie użytkownika, które każdy użytkownik w organizacji mogą wyrazić zgodę na. Inne aplikacje wymagają uprawnień na poziomie administratora, które użytkownik w organizacji nie może wyrażać zgody na. Tylko administrator katalogu mogą wyrazić zgodę, do aplikacji, które wymagają tego poziomu uprawnień. Gdy użytkownik lub administrator wyraża zgodę, tylko interfejs API sieci web jest zarejestrowany w swojego katalogu. Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji w usłudze Azure Active Directory](active-directory-integrating-applications.md).

#### <a name="token-expiration"></a>Wygaśnięcie tokenu

Gdy aplikacji natywnej używa jego kod autoryzacji można uzyskać tokenu dostępu JWT, również odbiera token odświeżania tokenu JWT. Po wygaśnięciu token dostępu token odświeżania może służyć do ponownego uwierzytelnienia użytkownika bez konieczności ich ponownego zalogowania. Ten token odświeżania jest następnie używany do uwierzytelniania użytkownika, który skutkuje nowy token dostępu i token odświeżania.

### <a name="web-application-to-web-api"></a>Aplikacja sieci Web do internetowego interfejsu API

W tej sekcji opisano aplikację internetową, która musi pobrać zasoby z internetowego interfejsu API. W tym scenariuszu istnieją dwa typy tożsamości, które aplikacji sieci web można użyć do uwierzytelniania i wywoływanie internetowego interfejsu API: tożsamość aplikacji lub tożsamości delegowany użytkownik.

*Tożsamość aplikacji:* w tym scenariuszu przyznanie poświadczenia klienta OAuth 2.0 do uwierzytelniania aplikacji i dostępu do interfejsu API sieci web. Podczas korzystania z tożsamości aplikacji sieci web interfejsu API tylko może wykryć, czy wywołanie aplikacji sieci web, internetowego interfejsu API nie otrzymywać żadnych informacji o użytkowniku. Jeśli aplikacja otrzyma informacje o użytkowniku, będą wysyłane za pośrednictwem protokołu aplikacji, a nie jest podpisany przez usługę Azure AD. Internetowy interfejs API zaufania, że aplikacja sieci web uwierzytelnił użytkownika. Z tego powodu ten wzorzec jest nazywany zaufany podsystem.

*Delegowana tożsamość użytkownika:* w tym scenariuszu można zrobić na dwa sposoby: OpenID Connect i przyznawanie kodu autoryzacji OAuth 2.0 przy użyciu poufne klienta. Aplikacja sieci web uzyskuje token dostępu dla użytkownika, który okazuje się do internetowego interfejsu API, że użytkownik pomyślnie uwierzytelniony do aplikacji sieci web i aplikacji sieci web był w stanie uzyskać tożsamość delegowany użytkownik w celu wywołania interfejsu API sieci web. Ten token dostępu wysyłane żądanie do internetowego interfejsu API, które udzielają użytkownikowi autoryzacji i zwraca żądanego zasobu.

#### <a name="diagram"></a>Diagram

![Aplikacja sieci Web do internetowego interfejsu API diagramu](./media/active-directory-authentication-scenarios/web_app_to_web_api.png)

#### <a name="description-of-protocol-flow"></a>Opis protokołu przepływu

Tożsamość aplikacji i typów tożsamości delegowany użytkownik zostały omówione w usłudze flow poniżej. Klucza różnica między nimi polega na tym, czy tożsamość delegowany użytkownik najpierw muszą uzyskać kod autoryzacji, zanim użytkownik może zarejestrować i uzyskać dostęp do interfejsu API sieci web.

##### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Udzielanie tożsamości aplikacji przy użyciu poświadczeń klienta OAuth 2.0

1. Użytkownik jest zalogowany do usługi Azure AD w aplikacji sieci web (zobacz [przeglądarki sieci Web do aplikacji sieci Web](#web-browser-to-web-application) powyżej).
1. Aplikacja sieci web musi uzyskiwanie tokenu dostępu, dzięki czemu mogą uwierzytelniania interfejsu API sieci web i pobrać żądanego zasobu. Kieruje żądanie do usługi Azure AD punktu końcowego tokenu, podając poświadczenia, identyfikator aplikacji i identyfikator URI aplikacji internetowego interfejsu API.
1. Usługa Azure AD uwierzytelnia aplikacji i zwraca token JWT token dostępu, które jest używane do wywołania interfejsu API sieci web.
1. Przy użyciu protokołu HTTPS aplikacja internetowa używa zwrócony token dostępu JWT do dodawania ciągu JWT z oznaczeniem "Bearer" w nagłówku autoryzacji żądania do internetowego interfejsu API. Interfejs API sieci web sprawdza poprawność tokenu JWT i, jeśli weryfikacja zakończy się pomyślnie, zwraca żądanego zasobu.

##### <a name="delegated-user-identity-with-openid-connect"></a>Tożsamość delegowany użytkownik za pomocą protokołu OpenID Connect

1. Użytkownik jest zalogowany do aplikacji sieci web przy użyciu usługi Azure AD (zobacz [przeglądarki sieci Web do aplikacji sieci Web](#web-browser-to-web-application) powyższej sekcji). Jeśli użytkownik aplikacji sieci web nie jeszcze wyraził zgodę na zezwolenie aplikacji sieci web do wywołania interfejsu API sieci web w jej imieniu, użytkownik musi wyrazić zgodę. Aplikacja wyświetli wymagane uprawnienia, a jeśli którakolwiek z tych uprawnień na poziomie administratora, zwykłego użytkownika w katalogu nie będzie można wyrazić zgodę. Ten proces zgody dotyczy tylko wielodostępne, aplikacje nie pojedynczej dzierżawy, jak aplikacja już mieć odpowiednie uprawnienia. Gdy użytkownik jest zalogowany, aplikacji sieci web odebrał tokenu Identyfikacyjnego informacje na temat użytkownika, a także kod autoryzacji.
1. Przy użyciu kodu autoryzacji, wystawiony przez usługę Azure AD, aplikacja sieci web wysyła żądanie do punktu końcowego tokenu usługi Azure AD, która zawiera kod autoryzacji, szczegółowe informacje o aplikacji klienta (identyfikator aplikacji i identyfikator URI przekierowania) i żądanego zasobu (identyfikator aplikacji Identyfikator URI dla interfejsu API sieci web).
1. Kod autoryzacji i informacji na temat interfejsu API sieci web i aplikacji sieci web są weryfikowane przez usługę Azure AD. Po pomyślnej weryfikacji usługi Azure AD zwraca dwa tokeny: token JWT dostępu i token odświeżania tokenu JWT.
1. Przy użyciu protokołu HTTPS aplikacja internetowa używa zwrócony token dostępu JWT do dodawania ciągu JWT z oznaczeniem "Bearer" w nagłówku autoryzacji żądania do internetowego interfejsu API. Interfejs API sieci web sprawdza poprawność tokenu JWT i, jeśli weryfikacja zakończy się pomyślnie, zwraca żądanego zasobu.

##### <a name="delegated-user-identity-with-oauth-20-authorization-code-grant"></a>Tożsamość delegowany użytkownik za pomocą przyznawanie kodu autoryzacji OAuth 2.0

1. Użytkownik jest już zalogowany do aplikacji sieci web, w których mechanizmu uwierzytelniania jest niezależna od usługi Azure AD.
1. Aplikacja sieci web wymaga kodu autoryzacji do uzyskania tokenu dostępu, więc emituje żądanie za pośrednictwem przeglądarki do punktu końcowego autoryzacji usługi Azure AD, podając identyfikator aplikacji i identyfikator URI przekierowania dla aplikacji sieci web po pomyślnym uwierzytelnieniu. Użytkownik loguje się do usługi Azure AD.
1. Jeśli użytkownik aplikacji sieci web nie jeszcze wyraził zgodę na zezwolenie aplikacji sieci web do wywołania interfejsu API sieci web w jej imieniu, użytkownik musi wyrazić zgodę. Aplikacja wyświetli wymagane uprawnienia, a jeśli którakolwiek z tych uprawnień na poziomie administratora, zwykłego użytkownika w katalogu nie będzie można wyrazić zgodę. To wyrażenie zgody ma zastosowanie do aplikacji pojedynczych i wielu dzierżawców. W przypadku pojedynczej dzierżawy Administrator można wykonywać administratora zgody i zgody w imieniu swoich użytkowników. Można to zrobić za pomocą `Grant Permissions` znajdujący się w [witryny Azure Portal](https://portal.azure.com). 
1. Po użytkownik wyraził zgodę, aplikacja sieci web otrzyma kod autoryzacji wymagane do uzyskania tokenu dostępu.
1. Przy użyciu kodu autoryzacji, wystawiony przez usługę Azure AD, aplikacja sieci web wysyła żądanie do punktu końcowego tokenu usługi Azure AD, która zawiera kod autoryzacji, szczegółowe informacje o aplikacji klienta (identyfikator aplikacji i identyfikator URI przekierowania) i żądanego zasobu (identyfikator aplikacji Identyfikator URI dla interfejsu API sieci web).
1. Kod autoryzacji i informacji na temat interfejsu API sieci web i aplikacji sieci web są weryfikowane przez usługę Azure AD. Po pomyślnej weryfikacji usługi Azure AD zwraca dwa tokeny: token JWT dostępu i token odświeżania tokenu JWT.
1. Przy użyciu protokołu HTTPS aplikacja internetowa używa zwrócony token dostępu JWT do dodawania ciągu JWT z oznaczeniem "Bearer" w nagłówku autoryzacji żądania do internetowego interfejsu API. Interfejs API sieci web sprawdza poprawność tokenu JWT i, jeśli weryfikacja zakończy się pomyślnie, zwraca żądanego zasobu.

#### <a name="code-samples"></a>Przykłady kodu

Zobacz przykłady kodu dla aplikacji sieci Web do internetowego interfejsu API scenariuszy. I, zajrzyj tu często, ponieważ często dodawane są nowe przykłady. Web [aplikacji do internetowego interfejsu API](active-directory-code-samples.md#web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity).

#### <a name="registering"></a>Rejestrowanie

* Pojedynczej dzierżawy: Tożsamość aplikacji i delegowany użytkownik tożsamości przypadków, interfejsu API sieci web i aplikacji sieci web musi być zarejestrowany w tym samym katalogu w usłudze Azure AD. Interfejs API sieci web można skonfigurować do udostępnienia zestaw uprawnień, które są używane do ograniczania dostępu do aplikacji sieci web, do jej zasobów. Jeśli jest używany typ tożsamości delegowany użytkownik aplikacji sieci web musi wybierz żądane uprawnienia z menu rozwijanego "Uprawnienia na inne aplikacje" w witrynie Azure portal. Ten krok nie jest wymagane, jeśli jest używany typ tożsamości aplikacji.
* Wielodostępne: Najpierw aplikacji sieci web jest skonfigurowany do wskazania uprawnienia, które wymaga, aby działała prawidłowo. Ta lista wymaganych uprawnień jest wyświetlany w oknie dialogowym po użytkownik lub administrator w katalogu docelowym powoduje zgody aplikacji, która udostępnia je do swojej organizacji. Niektóre aplikacje wymagają tylko uprawnienia na poziomie użytkownika, które każdy użytkownik w organizacji mogą wyrazić zgodę na. Inne aplikacje wymagają uprawnień na poziomie administratora, które użytkownik w organizacji nie może wyrażać zgody na. Tylko administrator katalogu mogą wyrazić zgodę, do aplikacji, które wymagają tego poziomu uprawnień. Gdy użytkownik lub administrator wyraża zgodę, interfejsu API sieci web i aplikacji sieci web są zarówno zarejestrowane w ich katalogu.

#### <a name="token-expiration"></a>Wygaśnięcie tokenu

Gdy aplikacja sieci web używa jego kod autoryzacji można uzyskać tokenu dostępu JWT, również odbiera token odświeżania tokenu JWT. Po wygaśnięciu token dostępu token odświeżania może służyć do ponownego uwierzytelnienia użytkownika bez konieczności ich ponownego zalogowania. Ten token odświeżania jest następnie używany do uwierzytelniania użytkownika, który skutkuje nowy token dostępu i token odświeżania.

### <a name="daemon-or-server-application-to-web-api"></a>Demon lub serwera aplikacji do internetowego interfejsu API

W tej sekcji opisano demona lub serwera przez użytkowników aplikacji, serwer musi pobrać zasoby z internetowego interfejsu API. Istnieją dwa scenariusze podrzędnych, które są stosowane do tej sekcji: demona, który musi wywołać internetowego interfejsu API, w oparciu o typ przydziału poświadczeń klienta OAuth 2.0; i aplikacji serwera (na przykład internetowy interfejs API), który musi wywołać internetowego interfejsu API, oparte na specyfikacji wersji roboczej OAuth 2.0 "w imieniu".

W ramach scenariusza opisywanego w przypadku aplikacji demona musi wywoływać internetowy interfejs API, jest należy zrozumieć kilka rzeczy. Po pierwsze interakcji z użytkownikiem nie jest możliwe za pomocą aplikacji demona, którego wymaga aplikacja miała własną tożsamości. Przykład aplikacji demona to zadanie wsadowe lub usługa systemu operacyjnego, który jest uruchomiona w tle. Aplikacje tego typu żądania tokenu dostępu przy użyciu tożsamości aplikacji i zaprezentowanie jej identyfikator aplikacji, poświadczenia (hasło lub certyfikat) i aplikacji identyfikator URI do usługi Azure AD. Po pomyślnym uwierzytelnieniu demona odbiera token dostępu z usługi Azure AD, która jest następnie używany do wywoływania interfejsu API sieci web.

W ramach scenariusza opisywanego gdy aplikacja serwera wymaga do wywołania internetowego interfejsu API, warto użyć przykładu. Załóżmy, że użytkownik został uwierzytelniony w aplikacji natywnej i tej aplikacji natywnej musi wywołać interfejs API sieci web. Usługa Azure AD wystawia token JWT dostępu do wywołania interfejsu API sieci web. Jeśli internetowy interfejs API musi wywołać inny podrzędny interfejs API sieci web, służy w imieniu użytkownika z usługi flow do delegowania tożsamości użytkownika i uwierzytelniania interfejsu API sieci web drugiej warstwy.

#### <a name="diagram"></a>Diagram

![Demon lub aplikację serwera do diagramu interfejsu API sieci Web](./media/active-directory-authentication-scenarios/daemon_server_app_to_web_api.png)

#### <a name="description-of-protocol-flow"></a>Opis protokołu przepływu

##### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Udzielanie tożsamości aplikacji przy użyciu poświadczeń klienta OAuth 2.0

1. Najpierw aplikacja serwera musi uwierzytelnić za pomocą usługi Azure AD w swoim imieniu, bez interwencji człowieka takich jak interakcyjne okno logowania jednokrotnego. Kieruje żądanie do usługi Azure AD punktu końcowego tokenu, podanie poświadczeń, Identyfikatora aplikacji i identyfikator URI aplikacji.
1. Usługa Azure AD uwierzytelnia aplikacji i zwraca token JWT token dostępu, które jest używane do wywołania interfejsu API sieci web.
1. Przy użyciu protokołu HTTPS aplikacja internetowa używa zwrócony token dostępu JWT do dodawania ciągu JWT z oznaczeniem "Bearer" w nagłówku autoryzacji żądania do internetowego interfejsu API. Interfejs API sieci web sprawdza poprawność tokenu JWT i, jeśli weryfikacja zakończy się pomyślnie, zwraca żądanego zasobu.

##### <a name="delegated-user-identity-with-oauth-20-on-behalf-of-draft-specification"></a>Tożsamość delegowany użytkownik za pomocą protokołu OAuth 2.0 "w imieniu" wersji roboczej specyfikacji

Przepływ omówiono poniżej przyjęto założenie, że użytkownik został uwierzytelniony w innej aplikacji (np. aplikacji natywnej), a ich tożsamość użytkownika został użyty do uzyskania tokenu dostępu do interfejsu API sieci web pierwszego rzędu.

1. Aplikacji natywnej wysyła ten token dostępu do interfejsu API sieci web pierwszego rzędu.
1. Pierwszego rzędu interfejsu API sieci web wysyła żądanie do usługi Azure AD punktu końcowego tokenu, podając jego Identyfikatora aplikacji i poświadczenia, a także tokenu dostępu. Ponadto, żądanie jest wysyłane za pomocą on_behalf_of parametrem, który wskazuje internetowy interfejs API żąda nowych tokenów do wywoływania podrzędnego internetowego interfejsu API w imieniu użytkownika oryginalnego.
1. Usługi Azure AD sprawdza pierwszego rzędu internetowy interfejs API ma uprawnienia do dostępu do drugiej warstwy sieci web interfejsu API i weryfikuje żądanie zwracania tokenu JWT dostępu i token JWT odświeżenia tokenu do pierwszego rzędu internetowego interfejsu API.
1. Przy użyciu protokołu HTTPS pierwszego rzędu internetowy interfejs API wywołuje internetowy interfejs API drugiej warstwy, dodając ciąg tokenu w nagłówku autoryzacji w żądaniu. Interfejsu API sieci web pierwszego rzędu, można w dalszym ciągu wywołania interfejsu API sieci web drugiej warstwy, tak długo, jak uzyskać token dostępu i tokenów odświeżania są prawidłowe.

#### <a name="code-samples"></a>Przykłady kodu

Demon lub aplikację serwera pod kątem scenariuszy interfejsu Web API można znaleźć przykłady kodu. I, zajrzyj tu często, ponieważ często dodawane są nowe przykłady. [Serwera lub demon aplikacji do internetowego interfejsu API](active-directory-code-samples.md#daemon-applications-accessing-web-apis-with-the-applications-identity)

#### <a name="registering"></a>Rejestrowanie

* Pojedynczej dzierżawy: Tożsamość aplikacji i delegowany użytkownik tożsamości przypadków, demon lub serwera aplikacji musi być zarejestrowany w tym samym katalogu w usłudze Azure AD. Interfejs API sieci web można skonfigurować do udostępnienia zestaw uprawnień, które są używane do ograniczenia demona lub serwera dostępu do jego zasobów. Jeśli jest używany typ tożsamości delegowany użytkownik aplikacji serwera musi wybierz żądane uprawnienia z menu rozwijanego "Uprawnienia na inne aplikacje" w witrynie Azure portal. Ten krok nie jest wymagane, jeśli jest używany typ tożsamości aplikacji.
* Wielodostępne: Najpierw demona lub serwera aplikacji jest skonfigurowany do wskazania uprawnienia, które wymaga, aby działała prawidłowo. Ta lista wymaganych uprawnień jest wyświetlany w oknie dialogowym po użytkownik lub administrator w katalogu docelowym powoduje zgody aplikacji, która udostępnia je do swojej organizacji. Niektóre aplikacje wymagają tylko uprawnienia na poziomie użytkownika, które każdy użytkownik w organizacji mogą wyrazić zgodę na. Inne aplikacje wymagają uprawnień na poziomie administratora, które użytkownik w organizacji nie może wyrażać zgody na. Tylko administrator katalogu mogą wyrazić zgodę, do aplikacji, które wymagają tego poziomu uprawnień. Gdy użytkownik lub administrator wyraża zgodę, oba interfejsy API sieci web są zarejestrowane w usłudze swojego katalogu.

#### <a name="token-expiration"></a>Wygaśnięcie tokenu

Podczas pierwszej aplikacji używa jego kod autoryzacji można uzyskać tokenu dostępu JWT, również odbiera token odświeżania tokenu JWT. Po wygaśnięciu token dostępu token odświeżania może służyć do ponownego uwierzytelnienia użytkownika bez monitowania użytkownika o podanie poświadczeń. Ten token odświeżania jest następnie używany do uwierzytelniania użytkownika, który skutkuje nowy token dostępu i token odświeżania.

## <a name="see-also"></a>Zobacz też

[Przewodnik dewelopera usługi Azure Active Directory](azure-ad-developers-guide.md)

[Przykłady kodu usługi Azure Active Directory](active-directory-code-samples.md)

[Ważne informacje na temat Przerzucanie klucza podpisywania w usłudze Azure AD](active-directory-signing-key-rollover.md)

[Uwierzytelnianie OAuth 2.0 w usłudze Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx)
