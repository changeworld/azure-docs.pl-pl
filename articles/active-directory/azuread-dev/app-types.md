---
title: Typy aplikacji w wersji 1.0 | Azure
description: Zawiera opis typów aplikacji i scenariuszy obsługiwanych przez punkt końcowy usługi Azure Active Directory w wersji 2.0.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: c290cbf36fd53d5afb5fd805cda896fb6879bb4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154954"
---
# <a name="application-types-in-v10"></a>Typy aplikacji w wersji 1.0

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Usługa Azure Active Directory (Azure AD) obsługuje uwierzytelnianie dla różnych nowoczesnych architektur aplikacji, wszystkie z nich oparte na standardowych protokołach OAuth 2.0 lub OpenID Connect.

Na poniższym diagramie przedstawiono scenariusze i typy aplikacji oraz sposób, w jaki można dodać różne składniki:

![Scenariusze i typy aplikacji](./media/authentication-scenarios/application-types-scenarios.png)

Oto pięć podstawowych scenariuszy aplikacji obsługiwanych przez usługę Azure AD:

- **[Aplikacja jednostronicowa (SPA)](single-page-application.md)**: Użytkownik musi zalogować się do aplikacji jednostronicowej, która jest zabezpieczona przez usługę Azure AD.
- **[Przeglądarka sieci Web do aplikacji sieci Web:](web-app.md)** Użytkownik musi zalogować się do aplikacji sieci web, która jest zabezpieczona przez usługę Azure AD.
- **[Natywna aplikacja do interfejsu API sieci Web:](native-app.md)** natywna aplikacja uruchamiana na telefonie, tablecie lub komputerze musi uwierzytelnić użytkownika, aby uzyskać zasoby z internetowego interfejsu API zabezpieczonego usługą Azure AD.
- **[Aplikacja sieci Web do interfejsu API sieci Web:](web-api.md)** Aplikacja sieci web musi uzyskać zasoby z internetowego interfejsu API zabezpieczonego przez usługę Azure AD.
- **[Daemon lub aplikacja serwera do interfejsu API sieci web:](service-to-service.md)** Aplikacja demona lub aplikacji serwera bez interfejsu użytkownika sieci web musi uzyskać zasoby z internetowego interfejsu API zabezpieczonego przez usługę Azure AD.

Skorzystaj z łączy, aby dowiedzieć się więcej o każdym typie aplikacji i zrozumieć scenariusze wysokiego poziomu przed rozpoczęciem pracy z kodem. Można również dowiedzieć się o różnicach, które należy wiedzieć podczas pisania określonej aplikacji, która działa z punktu końcowego w wersji 1.0 lub punktu końcowego w wersji 2.0.

> [!NOTE]
> Punkt końcowy w wersji 2.0 nie obsługuje wszystkich scenariuszy i funkcji usługi Azure AD. Aby ustalić, czy należy użyć punktu końcowego w wersji 2.0, przeczytaj o [ograniczeniach wersji 2.0](../develop/active-directory-v2-limitations.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

Można tworzyć dowolną z aplikacji i scenariuszy opisanych w tym miejscu przy użyciu różnych języków i platform. Wszystkie są poparte kompletnymi przykładami kodu dostępnymi w przewodniku po przykładach kodu: [przykłady kodu wersji 1.0 według scenariusza](sample-v1-code.md) i [przykłady kodu wersji 2.0 według scenariusza.](../develop/sample-v2-code.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) Przykłady kodu można również pobrać bezpośrednio z odpowiednich [przykładowych repozytoriów GitHub.](https://github.com/Azure-Samples?q=active-directory)

Ponadto jeśli aplikacja potrzebuje określonego elementu lub segmentu scenariusza end-to-end, w większości przypadków, że funkcje mogą być dodawane niezależnie. Na przykład jeśli masz natywną aplikację, która wywołuje internetowy interfejs API, można łatwo dodać aplikację sieci web, która również wywołuje internetowy interfejs API.

## <a name="app-registration"></a>Rejestracja aplikacji

### <a name="registering-an-app-that-uses-the-azure-ad-v10-endpoint"></a>Rejestrowanie aplikacji korzystającej z punktu końcowego usługi Azure AD AD w wersji 1.0

Każda aplikacja, która zleca uwierzytelnianie do usługi Azure AD musi być zarejestrowana w katalogu. Ten krok obejmuje informowanie usługi Azure AD o aplikacji, w tym adres URL, w którym się znajduje, adres URL do wysyłania odpowiedzi po uwierzytelnieniu, identyfikator URI do identyfikowania aplikacji i inne. Informacje te są wymagane z kilku kluczowych powodów:

* Usługa Azure AD musi komunikować się z aplikacją podczas obsługi logowania lub wymiany tokenów. Informacje przekazywane między usługą Azure AD a aplikacją obejmują następujące informacje:
  
  * **Identyfikator aplikacji identyfikator URI** — identyfikator aplikacji. Ta wartość jest wysyłana do usługi Azure AD podczas uwierzytelniania, aby wskazać, dla której aplikacji obiekt wywołujący chce tokenu. Ponadto ta wartość jest uwzględniona w tokenie, dzięki czemu aplikacja wie, że był zamierzonym obiektem docelowym.
  * **Adres URL odpowiedzi** i **identyfikator URI przekierowania** — w przypadku interfejsu API sieci Web lub aplikacji sieci Web adres URL odpowiedzi jest lokalizacją, w której usługa Azure AD wyśle odpowiedź uwierzytelniania, w tym token, jeśli uwierzytelnianie zakończyło się pomyślnie. Dla aplikacji macierzystej redirect URI jest unikatowy identyfikator, do którego usługa Azure AD przekieruje agenta użytkownika w żądaniu OAuth 2.0.
  * **Identyfikator aplikacji** — identyfikator aplikacji, który jest generowany przez usługę Azure AD, gdy aplikacja jest zarejestrowana. Podczas żądania kodu autoryzacji lub tokenu identyfikator aplikacji i klucz są wysyłane do usługi Azure AD podczas uwierzytelniania.
  * **Klucz** — klucz, który jest wysyłany wraz z identyfikatorem aplikacji podczas uwierzytelniania w usłudze Azure AD w celu wywołania internetowego interfejsu API.
* Usługa Azure AD musi upewnić się, że aplikacja ma wymagane uprawnienia dostępu do danych katalogu, innych aplikacji w organizacji i tak dalej.

Aby uzyskać szczegółowe informacje, dowiedz się, jak [zarejestrować aplikację](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

## <a name="single-tenant-and-multi-tenant-apps"></a>Aplikacje dla jednej i wielu dzierżawców

Inicjowanie obsługi administracyjnej staje się jaśniejsze, gdy zrozumiesz, że istnieją dwie kategorie aplikacji, które można opracować i zintegrować z usługą Azure AD:

* **Aplikacja pojedynczej dzierżawy** — aplikacja pojedynczej dzierżawy jest przeznaczona do użycia w jednej organizacji. Są to zazwyczaj aplikacje biznesowe (LoB) napisane przez dewelopera przedsiębiorstwa. Tylko pojedyncza aplikacja dzierżawy musi być dostępna tylko dla użytkowników w jednym katalogu, a w rezultacie musi być aprowizowana tylko w jednym katalogu. Te aplikacje są zazwyczaj rejestrowane przez dewelopera w organizacji.
* **Aplikacja wielodostępna** — aplikacja wielodostępna jest przeznaczona do użytku w wielu organizacjach, a nie tylko w jednej organizacji. Są to zazwyczaj aplikacje typu SaaS (software-as-a-service) napisane przez niezależnych dostawców oprogramowania (ISV). Aplikacje z wieloma dzierżawami muszą być aprowiowane w każdym katalogu, w którym będą używane, co wymaga zgody użytkownika lub administratora, aby je zarejestrować. Proces wyrażania zgody rozpoczyna się po zarejestrowaniu aplikacji w katalogu i udzieleniu jej dostępu do interfejsu API programu Graph lub innego internetowego interfejsu API. Gdy użytkownik lub administrator z innej organizacji zarejestruje się, aby korzystać z aplikacji, zostanie wyświetlone okno dialogowe, w którym są wyświetlane uprawnienia wymagane przez aplikację. Użytkownik lub administrator może następnie wyrazić zgodę na aplikację, która daje aplikacji dostęp do podanych danych, a na koniec rejestruje aplikację w swoim katalogu. Aby uzyskać więcej informacji, zobacz [Omówienie struktury zgody](../develop/consent-framework.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

### <a name="additional-considerations-when-developing-single-tenant-or-multi-tenant-apps"></a>Dodatkowe zagadnienia podczas tworzenia aplikacji dla jednej dzierżawy lub aplikacji z wieloma dzierżawami

Niektóre dodatkowe zagadnienia pojawiają się podczas tworzenia aplikacji wielodostępnej zamiast aplikacji pojedynczej dzierżawy. Na przykład jeśli udostępniasz aplikację użytkownikom w wielu katalogach, potrzebujesz mechanizmu do określenia, w której dzierżawie się znajdują. Pojedyncza aplikacja dzierżawy tylko musi szukać w swoim katalogu dla użytkownika, podczas gdy aplikacja z wieloma dzierżawami musi zidentyfikować określonego użytkownika ze wszystkich katalogów w usłudze Azure AD. Aby wykonać to zadanie, usługa Azure AD udostępnia wspólny punkt końcowy uwierzytelniania, w którym każda aplikacja z wieloma dzierżawami może kierować żądania logowania zamiast punktu końcowego specyficznego dla dzierżawy. Ten punkt `https://login.microsoftonline.com/common` końcowy jest dla wszystkich katalogów w usłudze Azure `https://login.microsoftonline.com/contoso.onmicrosoft.com`AD, podczas gdy punkt końcowy specyficzny dla dzierżawy może być. Wspólny punkt końcowy jest szczególnie ważne, aby wziąć pod uwagę podczas tworzenia aplikacji, ponieważ trzeba będzie logiki niezbędne do obsługi wielu dzierżaw podczas logowania, wylogowywania i sprawdzania poprawności tokenu.

Jeśli obecnie tworzysz jedną aplikację dzierżawy, ale chcesz udostępnić ją wielu organizacjom, możesz łatwo wprowadzić zmiany w aplikacji i jej konfiguracji w usłudze Azure AD, aby była w stanie wielu dzierżawców. Ponadto usługa Azure AD używa tego samego klucza podpisywania dla wszystkich tokenów we wszystkich katalogach, niezależnie od tego, czy zapewniasz uwierzytelnianie w aplikacji pojedynczej dzierżawy, czy z wieloma dzierżawcami.

Każdy scenariusz wymieniony w tym dokumencie zawiera podsekcję, która opisuje jego wymagania dotyczące inicjowania obsługi administracyjnej. Aby uzyskać bardziej szczegółowe informacje na temat inicjowania obsługi administracyjnej aplikacji w usłudze Azure AD i różnic między aplikacjami jedno- i wielodostępowymi, zobacz [integrowanie aplikacji z usługą Azure Active Directory,](../develop/single-and-multi-tenant-apps.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) aby uzyskać więcej informacji. Kontynuuj czytanie, aby zrozumieć typowe scenariusze aplikacji w usłudze Azure AD.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o innych [podstawach uwierzytelniania](v1-authentication-scenarios.md) usługi Azure AD
