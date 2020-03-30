---
title: Dodawanie aplikacji interfejsu API sieci Web — Usługa Azure Active Directory B2C | Dokumenty firmy Microsoft
description: Dowiedz się, jak dodać aplikację interfejsu API sieci Web do dzierżawy usługi Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/16/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: e6dbf3d6fd5a43ab2d075c193c5bc589dc3566a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190181"
---
# <a name="add-a-web-api-application-to-your-azure-active-directory-b2c-tenant"></a>Dodawanie aplikacji interfejsu API sieci Web do dzierżawy usługi Azure Active Directory B2C

 Rejestruj zasoby interfejsu API sieci Web w dzierżawie, aby mogły akceptować żądania i odpowiadać na nie przez aplikacje klienckie prezentując token dostępu. W tym artykule pokazano, jak zarejestrować internetowy interfejs API w usłudze Azure Active Directory B2C (Azure AD B2C).

Aby zarejestrować aplikację w dzierżawie usługi Azure AD B2C, można użyć **bieżącego środowiska aplikacji** lub naszego nowego środowiska rejestracji aplikacji ujednoliconej **(w wersji zapoznawczej).** [Dowiedz się więcej na temat nowego środowiska](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Aplikacje](#tab/applications/)

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD B2C. Wybierz filtr **Katalog + subskrypcja** w górnym menu i wybierz katalog zawierający dzierżawę.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz **pozycję Aplikacje**, a następnie wybierz pozycję **Dodaj**.
5. Wprowadź nazwę aplikacji. Na przykład *webapi1*.
6. Dla pozycji **Uwzględnij aplikację internetową/internetowy interfejs API** i **Zezwalaj na niejawny przepływ** wybierz wartość **Tak**.
7. Dla pozycji **Adres URL odpowiedzi** wprowadź punkt końcowy, w którym usługa Azure AD B2C powinna zwracać wszelkie tokeny żądane przez Twoją aplikację. W aplikacji produkcyjnej można ustawić adres URL odpowiedzi `https://localhost:44332`na wartość, taką jak . Do celów testowych ustaw adres `https://jwt.ms`URL odpowiedzi na .
8. Dla pozycji **Identyfikator URI identyfikatora aplikacji** wprowadź identyfikator używany na potrzeby internetowego interfejsu API. Zostanie wygenerowany pełny identyfikator URI łącznie z domeną. Na przykład `https://contosotenant.onmicrosoft.com/api`.
9. Kliknij przycisk **Utwórz**.
10. Na stronie właściwości zapisz identyfikator aplikacji, który będzie używany podczas konfigurowania aplikacji internetowej.

#### <a name="app-registrations-preview"></a>[Rejestracje aplikacji (wersja zapoznawcza)](#tab/app-reg-preview/)

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Wybierz filtr **subskrypcja katalog +** w górnym menu, a następnie wybierz katalog zawierający dzierżawę usługi Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Możesz też wybrać **wszystkie usługi** i wyszukać i wybrać pozycję Azure **AD B2C**.
1. Wybierz **pozycję Rejestracje aplikacji (Wersja zapoznawcza),** a następnie wybierz pozycję **Nowa rejestracja**.
1. Wprowadź **nazwę** aplikacji. Na przykład *webapi1*.
1. W obszarze **Przekierowanie identyfikatora URI**wybierz pozycję **Sieć Web**, a następnie wprowadź punkt końcowy, w którym usługa Azure AD B2C powinna zwracać wszystkie tokeny żądane przez aplikację. W aplikacji produkcyjnej można ustawić przekierowanie identyfikatora `https://localhost:5000`URI punkt końcowy, takich jak . Podczas tworzenia lub testowania można `https://jwt.ms`ustawić go na , należącej do firmy Microsoft aplikacji sieci web, która wyświetla zdekodowany zawartość tokenu (zawartość tokenu nigdy nie opuszcza przeglądarki). Identyfikatory URI przekierowania można w dowolnym momencie dodawać i modyfikować w zarejestrowanych aplikacjach.
1. Wybierz pozycję **Zarejestruj**.
1. Zarejestruj **identyfikator aplikacji (klienta)** do użycia w kodzie internetowego interfejsu API.

Jeśli masz aplikację, która implementuje niejawny przepływ dotacji, na przykład aplikacji jednostronicowej opartej na języku JavaScript (SPA), można włączyć przepływ, wykonując następujące kroki:

1. W obszarze **Zarządzanie**wybierz pozycję **Uwierzytelnianie**.
1. Wybierz **wypróbuj nowe środowisko** (jeśli jest to pokazane).
1. W obszarze **Niejawna dotacja**wybierz pola wyboru **Tokeny dostępu** i **Tokeny Identyfikatorów.**
1. Wybierz **pozycję Zapisz**.

* * *

## <a name="configure-scopes"></a>Konfigurowanie zakresów

Zakresy umożliwiają zarządzanie dostępem do chronionych zasobów. Zakresy są używane przez internetowy interfejs API w celu implementowania kontroli dostępu opartej na zakresach. Na przykład użytkownicy internetowego interfejsu API mogą mieć dostęp zarówno do odczytu, jak i zapisu lub tylko dostęp do odczytu. Korzystając z zakresów, w tym samouczku zdefiniujesz uprawnienia do odczytu i zapisu dla internetowego interfejsu API.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>Udzielenie uprawnień

Aby wywoływać chroniony internetowy interfejs API z aplikacji, należy udzielić aplikacji uprawnień do tego interfejsu. Na przykład w [samouczku: Zarejestruj aplikację w usłudze Azure Active Directory B2C](tutorial-register-applications.md), aplikacja sieci web o nazwie *webapp1* jest zarejestrowana w usłudze Azure AD B2C. Za pomocą tej aplikacji można wywołać internetowy interfejs API.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Twoja aplikacja została zarejestrowana do wywoływania chronionego internetowego interfejsu API. Użytkownik uwierzytelnia się w usłudze Azure AD B2C, aby korzystać z aplikacji. Aplikacja uzyskuje autoryzację z usługi Azure AD B2C w celu uzyskiwania dostępu do chronionego internetowego interfejsu API.
