---
title: Zarejestrować aplikację w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarejestrować aplikację w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 8068c4a8f38cd33a1a0547f5db5079bc75c76ec1
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2018
ms.locfileid: "51013415"
---
# <a name="register-an-application-in-azure-active-directory-b2c"></a>Zarejestrować aplikację w usłudze Azure Active Directory B2C

Tworzenie [aplikacji](active-directory-b2c-apps.md) akceptujący konsumenta, zarejestruj się i zaloguj się, musisz najpierw zarejestrować aplikację w dzierżawie usługi Azure AD B2C. Ten artykuł pomoże Ci zarejestrować aplikację w dzierżawie usługi Azure Active Directory (Azure AD) B2C w ciągu kilku minut. Kiedy skończysz, Twoja aplikacja będzie zarejestrowana do użycia w dzierżawie usługi Azure AD B2C.

## <a name="prerequisites"></a>Wymagania wstępne

Utwórz własną dzierżawę wykonując kroki opisane w [tworzenie dzierżawy usługi Azure Active Directory B2C](tutorial-create-tenant.md).

Wybierz następne kroki, w oparciu o danego typu aplikacji:

- [Rejestrowanie aplikacji internetowej](#register-a-web-application)
- [Rejestrowanie internetowego interfejsu API](#register-a-web-api)
- [Rejestrowanie aplikacji mobilnej lub natywnej](#register-a-mobile-or-native-application)

## <a name="register-a-web-application"></a>Rejestrowanie aplikacji internetowej

1. Pamiętaj, że używasz katalogu, który zawiera dzierżawy usługi Azure AD B2C, klikając **filtr katalogów i subskrypcji** w górnym menu i wybierając katalog, który zawiera Twojej dzierżawy.
2. Wybierz **wszystkich usług** w lewym górnym rogu witryny Azure portal, a następnie wyszukaj i wybierz **usługi Azure AD B2C**.
3. Wybierz **aplikacje**, a następnie wybierz pozycję **Dodaj**.
4. Wprowadź nazwę aplikacji. Na przykład *testapp1*.
5. Aby uzyskać **Uwzględnij aplikację sieci web / interfejs API sieci web** i **Zezwalaj na niejawny przepływ**, wybierz opcję **tak**.
6. Aby uzyskać **adres URL odpowiedzi**, wprowadź punkt końcowy, w którym usługi Azure AD B2C powinna zwracać wszystkie tokeny żądane przez aplikację. Na przykład można ustawić go do lokalnego nasłuchiwania na `https://localhost:44316`. Jeśli jeszcze nie znasz numeru portu, można wprowadzić wartość symbolu zastępczego i go zmienić później.
7. Kliknij pozycję **Utwórz**.

### <a name="create-a-client-secret"></a>Utwórz klucz tajny klienta

Jeśli Twoja aplikacja wywołuje internetowy interfejs API zabezpieczony przez usługę Azure AD B2C, musisz utworzyć wpis tajny aplikacji.

1. Wybierz **klucze** a następnie kliknij przycisk **Wygeneruj klucz**. 
2. Wybierz **Zapisz** Aby wyświetlić klucz. Zanotuj wartość pola **Klucz aplikacji**. Użyj tej wartości jako wpisu tajnego aplikacji w kodzie Twojej aplikacji.
3. Wybierz **dostęp do interfejsu API**, kliknij przycisk **Dodaj**i wybierz internetowy interfejs API oraz zakresy (uprawnienia).

## <a name="register-a-web-api"></a>Rejestrowanie internetowego interfejsu API

1. Pamiętaj, że używasz katalogu, który zawiera dzierżawy usługi Azure AD B2C, klikając **filtr katalogów i subskrypcji** w górnym menu i wybierając katalog, który zawiera Twojej dzierżawy.
2. Wybierz **wszystkich usług** w lewym górnym rogu witryny Azure portal, a następnie wyszukaj i wybierz **usługi Azure AD B2C**.
3. Wybierz **aplikacje**, a następnie wybierz pozycję **Dodaj**.
4. Wprowadź nazwę aplikacji. Na przykład *testapp2*.
5. Aby uzyskać **Uwzględnij aplikację sieci web / interfejs API sieci web** i **Zezwalaj na niejawny przepływ**, wybierz opcję **tak**.
6. Aby uzyskać **adres URL odpowiedzi**, wprowadź punkt końcowy, w którym usługi Azure AD B2C powinna zwracać wszystkie tokeny żądane przez aplikację. Na przykład można ustawić go do lokalnego nasłuchiwania na `https://localhost:44316`. Jeśli jeszcze nie znasz numeru portu, można wprowadzić wartość symbolu zastępczego i go zmienić później.
7. Aby uzyskać **identyfikator URI Identyfikatora aplikacji**, wprowadź identyfikator używany na potrzeby interfejsu API sieci web. Zostanie wygenerowany pełny identyfikator URI łącznie z domeną. Na przykład `https://contosotenant.onmicrosoft.com/api`.
8. Kliknij pozycję **Utwórz**.
9. Wybierz **opublikowane zakresy** można dodać więcej zakresów, zgodnie z potrzebami. Domyślnie `user_impersonation` jest definiowany zakres. `user_impersonation` Zakresu daje innym aplikacjom możliwość dostępu do tego interfejsu API w imieniu zalogowanego użytkownika. Jeśli chcesz, `user_impersonation` zakres można usunąć.

## <a name="register-a-mobile-or-native-application"></a>Rejestrowanie aplikacji mobilnej lub natywnej

1. Pamiętaj, że używasz katalogu, który zawiera dzierżawy usługi Azure AD B2C, klikając **filtr katalogów i subskrypcji** w górnym menu i wybierając katalog, który zawiera Twojej dzierżawy.
2. Wybierz **wszystkich usług** w lewym górnym rogu witryny Azure portal, a następnie wyszukaj i wybierz **usługi Azure AD B2C**.
3. Wybierz **aplikacje**, a następnie wybierz pozycję **Dodaj**.
4. Wprowadź nazwę aplikacji. Na przykład *testapp3*.
5. Aby uzyskać **Uwzględnij aplikację sieci web / interfejs API sieci web**, wybierz opcję **nie**.
6. Aby uzyskać **Uwzględnij klienta natywnego**, wybierz opcję **tak**.
7. Aby uzyskać **identyfikator URI przekierowania**, wprowadź [identyfikator URI przekierowania ze schematem niestandardowym](active-directory-b2c-apps.md). Upewnij się, należy wybrać dobry identyfikator URI przekierowania i nie zawierają znaków specjalnych, takich jak podkreślenia.
8. Kliknij pozycję **Utwórz**.

### <a name="create-a-client-secret"></a>Utwórz klucz tajny klienta

Jeśli Twoja aplikacja wywołuje internetowy interfejs API zabezpieczony przez usługę Azure AD B2C, musisz utworzyć wpis tajny aplikacji.

1. Wybierz **klucze** a następnie kliknij przycisk **Wygeneruj klucz**. 
2. Wybierz **Zapisz** Aby wyświetlić klucz. Zanotuj wartość pola **Klucz aplikacji**. Użyj tej wartości jako wpisu tajnego aplikacji w kodzie Twojej aplikacji.
3. Wybierz **dostęp do interfejsu API**, kliknij przycisk **Dodaj**i wybierz internetowy interfejs API oraz zakresy (uprawnienia).

## <a name="next-steps"></a>Kolejne kroki

Po zarejestrowaniu aplikacji w usłudze Azure AD B2C możesz wykonać czynności opisane w jednym z [samouczków szybkiego startu](active-directory-b2c-overview.md), aby rozpocząć pracę.

> [!div class="nextstepaction"]
> [Tworzenie aplikacji internetowej platformy ASP.NET z rejestrowaniem, logowaniem i resetowaniem hasła](active-directory-b2c-devquickstarts-web-dotnet-susi.md)
