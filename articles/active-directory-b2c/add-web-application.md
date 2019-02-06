---
title: Dodawanie aplikacji sieci web — Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodać aplikację sieci web, do dzierżawy usługi Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.component: B2C
ms.openlocfilehash: c20f455a0a325dadd3eeeb77dea7026de4834c56
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/06/2019
ms.locfileid: "55757673"
---
# <a name="add-a-web-api-application-to-your-azure-active-directory-b2c-tenant"></a>Dodawanie aplikacji interfejsu API do dzierżawy usługi Azure Active Directory B2C w sieci web

Zasobów interfejsu API sieci Web muszą być zarejestrowani w dzierżawie usługi przed on mógł akceptować i odpowiadać na żądania zasobów chronionych przez aplikacje klienckie, które przedstawiają token dostępu.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Pamiętaj, że używasz katalogu, który zawiera dzierżawy usługi Azure AD B2C, klikając **filtr katalogów i subskrypcji** w górnym menu i wybierając katalog, który zawiera Twojej dzierżawy.
3. Wybierz **wszystkich usług** w lewym górnym rogu witryny Azure portal, a następnie wyszukaj i wybierz **usługi Azure AD B2C**.
4. Wybierz **aplikacje**, a następnie wybierz pozycję **Dodaj**.
5. Wprowadź nazwę aplikacji. Na przykład *webapi1*.
6. Aby uzyskać **Uwzględnij aplikację sieci web / interfejs API sieci web** i **Zezwalaj na niejawny przepływ**, wybierz opcję **tak**.
7. Aby uzyskać **adres URL odpowiedzi**, wprowadź punkt końcowy, w którym usługi Azure AD B2C należy zwracać wszystkie tokeny żądań aplikacji. W tym samouczku przykład jest uruchamiany lokalnie i nasłuchuje na `https://localhost:44332`.
8. Aby uzyskać **identyfikator URI Identyfikatora aplikacji**, wprowadź identyfikator używany na potrzeby interfejsu API sieci web. Zostanie wygenerowany pełny identyfikator URI łącznie z domeną. Na przykład `https://contosotenant.onmicrosoft.com/api`.
9. Kliknij pozycję **Utwórz**.
10. Na stronie właściwości należy zarejestrować identyfikator aplikacji, która będzie używana podczas konfigurowania aplikacji sieci web.

## <a name="configure-scopes"></a>Konfigurowanie zakresów

Zakresy umożliwiają zarządzanie dostępem do chronionych zasobów. Zakresy są używane przez internetowy interfejs API w celu implementowania kontroli dostępu opartej na zakresach. Na przykład użytkownicy internetowego interfejsu API mogą mieć dostęp zarówno do odczytu, jak i zapisu lub tylko dostęp do odczytu. Korzystając z zakresów, w tym samouczku zdefiniujesz uprawnienia do odczytu i zapisu dla internetowego interfejsu API.

1. Wybierz **aplikacje**, a następnie wybierz pozycję *webapi1*.
2. Wybierz **opublikowane zakresy**.
3. Aby uzyskać **zakres**, wprowadź `Hello.Read`i opis, wprowadź `Read access to hello`.
4. Aby uzyskać **zakres**, wprowadź `Hello.Write`i opis, wprowadź `Write access to hello`.
5. Kliknij pozycję **Zapisz**.

Opublikowane zakresy mogą służyć do udzielenia klienta aplikacji uprawnienia do interfejsu API sieci web.

## <a name="grant-permissions"></a>Udzielenie uprawnień

Aby wywoływać chroniony internetowy interfejs API z aplikacji, musisz przyznać uprawnienia aplikacji interfejsu API. W ramach wymagań wstępnych samouczka utworzono aplikacji sieci web w usłudze Azure AD B2C o nazwie *webapp1*. Wywoływanie internetowego interfejsu API za pomocą tej aplikacji.

1. Wybierz **aplikacje**, a następnie wybierz aplikację sieci web.
2. Wybierz **dostęp do interfejsu API**, a następnie wybierz pozycję **Dodaj**.
3. W **wybierz interfejs API** listy rozwijanej wybierz *webapi1*.
4. W **wybierz zakresy** listy rozwijanej wybierz **Hello.Read** i **Hello.Write** zakresy, które zostały wcześniej zdefiniowane.
5. Kliknij przycisk **OK**.

Twoja aplikacja będzie zarejestrowana do wywołania chronionego internetowego interfejsu API. Użytkownik uwierzytelnia się za pomocą usługi Azure AD B2C w aplikacji. Aplikacja uzyskuje przydziału autoryzacji z usługi Azure AD B2C, aby dostęp do chronionego internetowego interfejsu API.