---
title: Dodawanie aplikacji sieci Web — Azure Active Directory B2C | Microsoft Docs
description: Dowiedz się, jak dodać aplikację sieci Web do dzierżawy Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 04/16/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 3114f082a402fd005797b270cfdcd7527784dbe1
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679222"
---
# <a name="add-a-web-api-application-to-your-azure-active-directory-b2c-tenant"></a>Dodawanie aplikacji internetowego interfejsu API do dzierżawy Azure Active Directory B2C

 Zarejestruj zasoby interfejsu API sieci Web w dzierżawie, aby umożliwić im akceptowanie i odpowiadanie na żądania przez aplikacje klienckie, które zawierają token dostępu. W tym artykule pokazano, jak zarejestrować aplikację w programie Azure Active Directory B2C (Azure AD B2C).

1. Zaloguj się do [portalu Azure](https://portal.azure.com).
2. Upewnij się, że używasz katalogu zawierającego dzierżawcę Azure AD B2C. W górnym menu wybierz pozycję **katalog i subskrypcja** , a następnie wybierz katalog, w którym znajduje się Twoja dzierżawa.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz pozycję **Aplikacje**, a następnie wybierz polecenie **Dodaj**.
5. Wprowadź nazwę aplikacji. Na przykład *webapi1*.
6. Dla pozycji **Uwzględnij aplikację internetową/internetowy interfejs API** i **Zezwalaj na niejawny przepływ** wybierz wartość **Tak**.
7. Dla pozycji **Adres URL odpowiedzi** wprowadź punkt końcowy, w którym usługa Azure AD B2C powinna zwracać wszelkie tokeny żądane przez Twoją aplikację. W aplikacji produkcyjnej można ustawić adres URL odpowiedzi na wartość taką jak `https://localhost:44332`. Dla celów testowych Ustaw adres URL odpowiedzi na `https://jwt.ms`.
8. Dla pozycji **Identyfikator URI identyfikatora aplikacji** wprowadź identyfikator używany na potrzeby internetowego interfejsu API. Zostanie wygenerowany pełny identyfikator URI łącznie z domeną. Na przykład `https://contosotenant.onmicrosoft.com/api`.
9. Kliknij przycisk **Utwórz**.
10. Na stronie właściwości zapisz identyfikator aplikacji, który będzie używany podczas konfigurowania aplikacji internetowej.

## <a name="configure-scopes"></a>Konfigurowanie zakresów

Zakresy umożliwiają zarządzanie dostępem do chronionych zasobów. Zakresy są używane przez internetowy interfejs API w celu implementowania kontroli dostępu opartej na zakresach. Na przykład użytkownicy internetowego interfejsu API mogą mieć dostęp zarówno do odczytu, jak i zapisu lub tylko dostęp do odczytu. Korzystając z zakresów, w tym samouczku zdefiniujesz uprawnienia do odczytu i zapisu dla internetowego interfejsu API.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>Udzielenie uprawnień

Aby wywoływać chroniony internetowy interfejs API z aplikacji, należy udzielić aplikacji uprawnień do tego interfejsu. Na przykład w [samouczku: rejestrowanie aplikacji w Azure Active Directory B2C](tutorial-register-applications.md), aplikacja sieci Web jest tworzona w Azure AD B2C o nazwie *webapp1*. Za pomocą tej aplikacji można wywołać internetowy interfejs API.

1. Wybierz pozycję **aplikacje**, a następnie wybierz aplikację sieci Web.
1. Wybierz pozycję **Dostęp do interfejsu API**, a następnie wybierz polecenie **Dodaj**.
1. Na liście rozwijanej **Wybierz interfejs API** wybierz pozycję *webapi1*.
1. Z listy rozwijanej **Wybierz zakresy** wybierz zdefiniowane wcześniej zakresy. Na przykład *Demonstracja. odczyt* i *Demonstracja. Write*.
1. Kliknij przycisk **OK**.

Twoja aplikacja została zarejestrowana do wywoływania chronionego internetowego interfejsu API. Użytkownik uwierzytelnia się w usłudze Azure AD B2C, aby korzystać z aplikacji. Aplikacja uzyskuje autoryzację z usługi Azure AD B2C w celu uzyskiwania dostępu do chronionego internetowego interfejsu API.
