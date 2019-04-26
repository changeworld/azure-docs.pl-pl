---
title: Typy aplikacji w wersji 1.0 | Azure
description: W tym artykule opisano typy aplikacji oraz scenariuszy obsługiwanych przez punkt końcowy usługi Azure Active Directory w wersji 2.0.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef180fb444e32e8b055837fd418e21162ff58339
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60411204"
---
# <a name="application-types-in-v10"></a>Typy aplikacji w wersji 1.0

Usługa Azure Active Directory (Azure AD) obsługuje uwierzytelnianie dla szerokiego zakresu nowoczesnych architekturach aplikacji, wszystkie z nich oparte na standardowych protokołach uwierzytelniania OAuth 2.0 lub OpenID Connect.

Na poniższym diagramie przedstawiono scenariusze i typy aplikacji, oraz w jaki sposób można dodawać różne składniki:

![Scenariusze i typy aplikacji](./media/authentication-scenarios/application_types_and_scenarios.png)

Poniżej przedstawiono pięciu scenariuszy głównej aplikacji obsługiwanych przez usługę Azure AD:

- **[Aplikacja jednostronicowa (SPA)](single-page-application.md)**: Użytkownik musi zalogować się do aplikacji jednostronicowej, która jest zabezpieczony przez usługę Azure AD.
- **[Przeglądarki sieci Web do aplikacji sieci web](web-app.md)**: Użytkownik musi zalogować się do aplikacji sieci web, która jest zabezpieczony przez usługę Azure AD.
- **[Aplikacja natywna do internetowego interfejsu API](native-app.md)**: Natywnych aplikacji, która działa na telefonie, tablecie lub komputerze wymaga uwierzytelnienia użytkownika można pobrać zasobów z internetowego interfejsu API, która jest zabezpieczony przez usługę Azure AD.
- **[Aplikacja do internetowego interfejsu API sieci Web](web-api.md)**: Aplikacja sieci web musi pobrać zasoby z internetowego interfejsu API zabezpieczony przez usługę Azure AD.
- **[Demon lub serwera aplikacji do internetowego interfejsu API](service-to-service.md)**: Aplikacji demona albo aplikacja serwera bez interfejsu użytkownika sieci web musi pobrać zasoby z internetowego interfejsu API zabezpieczony przez usługę Azure AD.

Skorzystaj z linków, aby dowiedzieć się więcej na temat każdego rodzaju aplikacji i zrozumienie ogólnych scenariuszy przed rozpoczęciem pracy z kodem. Można także Poznaj różnice, które należy znać podczas zapisywania danej aplikacji, która współdziała z punktu końcowego w wersji 1.0 lub punktu końcowego v2.0.

> [!NOTE]
> Punkt końcowy v2.0 nie obsługuje wszystkich scenariuszy usługi Azure AD i funkcji. Aby ustalić, czy należy używać punktu końcowego v2.0, przeczytaj temat [ograniczenia v2.0](active-directory-v2-limitations.md).

Można tworzyć dowolne aplikacje i scenariuszy opisanych w tym miejscu przy użyciu różnych języków i platform. Są one wszystko wspierane przez przykłady kodu kompletny przewodnik przykłady kodu: [v1.0 przykłady kodu według scenariusza](sample-v1-code.md) i [przykłady kodu w wersji 2.0 według scenariusza](sample-v2-code.md). Możesz również pobrać przykłady kodu, bezpośrednio od odpowiadających im [repozytoriów przykładowe GitHub](https://github.com/Azure-Samples?q=active-directory).

Ponadto jeśli aplikacja wymaga konkretne lub segmentu scenariusz end-to-end, w większości przypadków te funkcje można dodać niezależnie. Na przykład w przypadku aplikacji natywnej, która wywołuje internetowy interfejs API łatwo dodać aplikację internetową, która także wywołania interfejsu API sieci web.

## <a name="app-registration"></a>Rejestracja aplikacji

### <a name="registering-an-app-that-uses-the-azure-ad-v10-endpoint"></a>Rejestrowanie aplikacji, która używa punktu końcowego usługi Azure AD w wersji 1.0

Każda aplikacja, która outsources uwierzytelniania usługi Azure AD, musi być zarejestrowany w katalogu. Ten krok obejmuje informuje usługę Azure AD o swojej aplikacji, łącznie z adresem URL, w którym znajduje się, adres URL do wysyłania odpowiedzi po uwierzytelnieniu identyfikatora URI, aby zidentyfikować aplikację i nie tylko. Ta informacja jest wymagana w kilku kluczowych powodów:

* Usługa Azure AD wymaga do komunikacji z aplikacją podczas obsługi logowania jednokrotnego lub wymianę tokenów. Informacje przesyłane między usługą Azure AD i aplikacja obejmuje następujące elementy:
  
  * **Identyfikator URI Identyfikatora aplikacji** — identyfikator aplikacji. Ta wartość jest wysyłana do usługi Azure AD podczas uwierzytelniania w celu wskazania, która aplikacja obiekt wywołujący chce token dla. Ponadto tak, aby aplikacja wie, że jest zamierzonym obiektem docelowym było tę wartość znajduje się w tokenie.
  * **Adres URL odpowiedzi** i **identyfikator URI przekierowania** — interfejs API sieci web lub aplikacji sieci web, adres URL odpowiedzi to lokalizacja, w którym usługi Azure AD wysyła odpowiedź uwierzytelniania, w tym token, jeśli uwierzytelnianie zakończyło się pomyślnie. W przypadku aplikacji natywnej identyfikator URI przekierowania to unikatowy identyfikator, do którego usługa Azure AD przekierowuje agenta użytkownika w ramach żądania OAuth 2.0.
  * **Identyfikator aplikacji** — identyfikator aplikacji, który jest generowany przez usługę Azure AD, gdy aplikacja jest zarejestrowana. Żądający kod autoryzacji lub tokenu Identyfikatora aplikacji i klucz są wysyłane do usługi Azure AD podczas uwierzytelniania.
  * **Klucz** -key, przesyłany wraz z identyfikator aplikacji podczas uwierzytelniania w usłudze Azure AD do wywołania interfejsu API sieci web.
* Usługa Azure AD wymaga upewnić się, że aplikacja ma wymaganych uprawnień, aby uzyskiwać dostęp do danych katalogu, inne aplikacje w Twojej organizacji i tak dalej.

Aby uzyskać szczegółowe informacje, Dowiedz się, jak [rejestrowanie aplikacji z punktem końcowym usługi Azure AD w wersji 1.0](quickstart-v1-add-azure-ad-app.md).

## <a name="single-tenant-and-multi-tenant-apps"></a>Aplikacje jednej dzierżawy i wieloma dzierżawcami

Inicjowanie obsługi administracyjnej staje się bardziej zrozumiały po zapoznaniu się, że istnieją dwie kategorie aplikacji, które mogą być opracowane i zintegrowane z usługą Azure AD:

* **Pojedyncza aplikacja dzierżawy** — aplikacja jednej dzierżawy jest przeznaczony do użytku w jednej z organizacji. Są to zazwyczaj line-of-business (LoB) aplikacji napisanych przez Deweloper w przedsiębiorstwie. Aplikacja jednej dzierżawy musi tylko uzyskiwać dostęp użytkownicy w jednym katalogu, a w rezultacie wymaga tylko do udostępnienia w pojedynczym katalogu. Te aplikacje zwykle są rejestrowane przez dewelopera w organizacji.
* **Aplikacja wielodostępna** -aplikacji z wieloma dzierżawami jest przeznaczona do użycia w wielu organizacjach nie tylko jednej z organizacji. Są to zazwyczaj aplikacje typu SaaS (software-as-a-service) napisane przez niezależnych dostawców oprogramowania (ISV). Wielodostępne aplikacje muszą być obsługiwana w każdym katalogu gdzie zostaną użyte, wymaga zgody użytkownika lub administratora, aby zarejestrować je. Proces wyrażania zgody rozpoczyna się po zarejestrowaniu aplikacji w katalogu i udzieleniu jej dostępu do interfejsu API programu Graph lub innego internetowego interfejsu API. Gdy użytkownik lub administrator z innej organizacji zarejestruje się w celu korzystania z aplikacji, zobaczą okno dialogowe, które wyświetla uprawnienia, których wymaga aplikacja. Użytkownik lub administrator, następnie mogą wyrazić zgodę na aplikację, która zapewnia dostęp do aplikacji do podanych danych, a na koniec rejestruje swojego katalogu aplikacji. Aby uzyskać więcej informacji, zobacz [Przegląd Framework zgody](consent-framework.md).

### <a name="additional-considerations-when-developing-single-tenant-or-multi-tenant-apps"></a>Dodatkowe zagadnienia dotyczące opracowywania pojedynczego dzierżawy lub wielodostępnych aplikacji

Kilka dodatkowych kwestii dotyczących pojawiają się podczas tworzenia aplikacji z wieloma dzierżawami, zamiast aplikacji pojedynczej dzierżawy. Na przykład, jeśli udostępniasz aplikację dla użytkowników w wielu katalogach, potrzebujesz mechanizmu, aby określić, której dzierżawy znajdują się w. Aplikacja jednej dzierżawy musi się tylko do wyszukiwania w katalogu dla użytkownika, gdy aplikacja wielodostępna musi zidentyfikować określonego użytkownika ze wszystkich katalogów w usłudze Azure AD. Aby wykonać to zadanie, usługa Azure AD zapewnia wspólnego punktu końcowego uwierzytelniania gdzie dowolnej aplikacji wielodostępnych można kierować żądań logowania, zamiast punktem końcowym specyficznym dla dzierżawy. Ten punkt końcowy jest https://login.microsoftonline.com/common dla wszystkich katalogów w usłudze Azure AD może być punktem końcowym specyficznym dla dzierżawy https://login.microsoftonline.com/contoso.onmicrosoft.com. Wspólnego punktu końcowego jest szczególnie ważne należy wziąć pod uwagę podczas opracowywania aplikacji, ponieważ będziesz potrzebować logikę potrzebną do obsługi wielu dzierżaw podczas logowania, wylogowywania i walidacji tokenów.

Jeśli aktualnie tworzysz aplikację pojedynczej dzierżawy, ale chcesz udostępnić wiele organizacji, możesz łatwo wprowadzać zmiany do aplikacji, a jej konfiguracji w usłudze Azure AD, aby stał się wieloma dzierżawami stanie. Ponadto usługa Azure AD używa tego samego klucza podpisywania wszystkich tokenów we wszystkich katalogach czy udostępniasz uwierzytelniania w pojedynczej dzierżawy lub w aplikacji wielodostępnej.

Każdy scenariusz wymienione w niniejszym dokumencie zawiera podsekcja, który opisuje jego wymagania inicjowania obsługi administracyjnej. Aby uzyskać więcej szczegółowych informacji o aprowizacji aplikacji w usłudze Azure AD oraz różnice między jednym i wieloma dzierżawami aplikacji, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](quickstart-v1-integrate-apps-with-azure-ad.md) Aby uzyskać więcej informacji. Kontynuuj czytanie, aby poznać typowe scenariusze aplikacji w usłudze Azure AD.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o innych usługi Azure AD [podstawowe informacje o uwierzytelnianiu](authentication-scenarios.md)
