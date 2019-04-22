---
title: Dodawanie aplikacji sieci web — Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodać aplikację sieci web, do dzierżawy usługi Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.author: davidmu
ms.date: 04/16/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: c1c640fa2c03f584aa9be62ae6ad123377ef53cd
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2019
ms.locfileid: "59680761"
---
# <a name="add-a-web-api-application-to-your-azure-active-directory-b2c-tenant"></a>Dodawanie aplikacji interfejsu API do dzierżawy usługi Azure Active Directory B2C w sieci web

 Zarejestrować zasoby internetowego interfejsu API w dzierżawie mogą akceptować i odpowiadać na żądania przez aplikacje klienckie, które przedstawiają tokenu dostępu. W tym artykule pokazano, jak zarejestrować aplikację w usłudze Azure Active Directory (Azure AD) B2C.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Upewnij się, że używasz katalogu, który zawiera dzierżawy usługi Azure AD B2C. Wybierz **filtr katalogów i subskrypcji** w górnym menu i wybierz katalog, który zawiera Twojej dzierżawy.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz pozycję **Aplikacje**, a następnie wybierz polecenie **Dodaj**.
5. Wprowadź nazwę aplikacji. Na przykład *webapi1*.
6. Dla pozycji **Uwzględnij aplikację internetową/internetowy interfejs API** i **Zezwalaj na niejawny przepływ** wybierz wartość **Tak**.
7. Dla pozycji **Adres URL odpowiedzi** wprowadź punkt końcowy, w którym usługa Azure AD B2C powinna zwracać wszelkie tokeny żądane przez Twoją aplikację. W aplikacji produkcyjnej, można ustawić adres URL odpowiedzi do wartości takie jak `https://localhost:44332`. Do celów testowych, Ustaw adres URL odpowiedzi `https://jwt.ms`.
8. Dla pozycji **Identyfikator URI identyfikatora aplikacji** wprowadź identyfikator używany na potrzeby internetowego interfejsu API. Zostanie wygenerowany pełny identyfikator URI łącznie z domeną. Na przykład `https://contosotenant.onmicrosoft.com/api`.
9. Kliknij pozycję **Utwórz**.
10. Na stronie właściwości zapisz identyfikator aplikacji, który będzie używany podczas konfigurowania aplikacji internetowej.

## <a name="configure-scopes"></a>Konfigurowanie zakresów

Zakresy umożliwiają zarządzanie dostępem do chronionych zasobów. Zakresy są używane przez internetowy interfejs API w celu implementowania kontroli dostępu opartej na zakresach. Na przykład użytkownicy internetowego interfejsu API mogą mieć dostęp zarówno do odczytu, jak i zapisu lub tylko dostęp do odczytu. Korzystając z zakresów, w tym samouczku zdefiniujesz uprawnienia do odczytu i zapisu dla internetowego interfejsu API.

1. Wybierz pozycję **Aplikacje**, a następnie wybierz pozycję *webapi1*.
2. Wybierz pozycję **Opublikowane zakresy**.
3. Dla pozycji **zakres** wprowadź `Read`, a dla opisu wprowadź `Read access to the application`.
4. Dla pozycji **zakres** wprowadź `Write`, a dla opisu wprowadź `Write access to the application`.
5. Kliknij pozycję **Zapisz**.

Opublikowane zakresy umożliwiają udzielenie aplikacji klienckiej uprawnień do internetowego interfejsu API.

## <a name="grant-permissions"></a>Udzielenie uprawnień

Aby wywoływać chroniony internetowy interfejs API z aplikacji, należy udzielić aplikacji uprawnień do tego interfejsu. Na przykład w artykule [Samouczek: Zarejestrować aplikację w usłudze Azure Active Directory B2C](tutorial-register-applications.md), aplikacji sieci web jest tworzony w usłudze Azure AD B2C o nazwie *webapp1*. Ta aplikacja służy do wywoływania interfejsu API sieci web.

1. Wybierz **aplikacje**, a następnie wybierz aplikację sieci web.
2. Wybierz pozycję **Dostęp do interfejsu API**, a następnie wybierz polecenie **Dodaj**.
3. Na liście rozwijanej **Wybierz interfejs API** wybierz pozycję *webapi1*.
4. W **wybierz zakresy** listy rozwijanej wybierz **odczytu** i **zapisu** zakresy, które zostały wcześniej zdefiniowane.
5. Kliknij przycisk **OK**.

Twoja aplikacja została zarejestrowana do wywoływania chronionego internetowego interfejsu API. Użytkownik uwierzytelnia się w usłudze Azure AD B2C, aby korzystać z aplikacji. Aplikacja uzyskuje autoryzację z usługi Azure AD B2C w celu uzyskiwania dostępu do chronionego internetowego interfejsu API.
