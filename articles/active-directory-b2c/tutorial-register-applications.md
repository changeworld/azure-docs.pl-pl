---
title: Samouczek — rejestrowanie Azure Active Directory B2C aplikacji
description: Dowiedz się, jak zarejestrować aplikację sieci Web w Azure Active Directory B2C przy użyciu Azure Portal.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 08/23/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 651c15c8206f7956bb35520f9c5837cb0c9308f9
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69980706"
---
# <a name="tutorial-register-an-application-in-azure-active-directory-b2c"></a>Samouczek: Rejestrowanie aplikacji w Azure Active Directory B2C

Aby [aplikacje](active-directory-b2c-apps.md) mogły korzystać z usługi Azure Active Directory (Azure AD) B2C, muszą być zarejestrowane w dzierżawie, którą zarządzasz. W tym samouczku pokazano, jak zarejestrować aplikację sieci Web przy użyciu Azure Portal.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Rejestrowanie aplikacji internetowej
> * Tworzenie klucza tajnego klienta

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie utworzono jeszcze własnej dzierżawy [Azure AD B2C](tutorial-create-tenant.md), utwórz ją teraz. Możesz użyć istniejącej dzierżawy Azure AD B2C.

## <a name="register-a-web-application"></a>Rejestrowanie aplikacji internetowej

1. Pamiętaj, że używasz katalogu, który zawiera dzierżawy usługi Azure AD B2C, klikając **filtr katalogów i subskrypcji** w górnym menu i wybierając katalog, który zawiera Twojej dzierżawy.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz pozycję **Aplikacje**, a następnie wybierz polecenie **Dodaj**.
1. Wprowadź nazwę aplikacji. Na przykład *webapp1*.
1. Dla pozycji **Uwzględnij aplikację internetową/internetowy interfejs API** i **Zezwalaj na niejawny przepływ** wybierz wartość **Tak**.
1. Dla pozycji **Adres URL odpowiedzi** wprowadź punkt końcowy, w którym usługa Azure AD B2C powinna zwracać wszelkie tokeny żądane przez Twoją aplikację. Na przykład można ustawić, aby nasłuchiwać lokalnie w `https://localhost:44316`. Jeśli nie znasz jeszcze numeru portu, możesz wprowadzić wartość symbolu zastępczego i zmienić ją później.

    Do celów testowych, takich jak ten samouczek, można ustawić `https://jwt.ms` , aby wyświetlić zawartość tokenu do inspekcji. Na potrzeby tego samouczka Ustaw **adres URL odpowiedzi** na `https://jwt.ms`.

    Do adresów URL odpowiedzi są stosowane następujące ograniczenia:

    * Adres URL odpowiedzi musi rozpoczynać się od `https`schematu.
    * W adresie URL odpowiedzi jest rozróżniana wielkość liter. Jego wielkość liter musi być zgodna z wielkością liter w ścieżce URL działającej aplikacji. Na przykład jeśli aplikacja zawiera jako część swojej ścieżki `.../abc/response-oidc`, nie należy określać `.../ABC/response-oidc` jej w adresie URL odpowiedzi. Ponieważ przeglądarka sieci Web traktuje ścieżki w miarę uwzględniania wielkości liter, `.../abc/response-oidc` pliki cookie skojarzone z programem mogą zostać wykluczone w przypadku przekierowania do `.../ABC/response-oidc` niezgodnego z wielkością liter adresów URL.

1. Kliknij przycisk **Utwórz** , aby zakończyć rejestrację aplikacji.

## <a name="create-a-client-secret"></a>Tworzenie klucza tajnego klienta

Jeśli aplikacja wymienia kod dla tokenu, należy utworzyć klucz tajny aplikacji.

1. Na stronie **Azure AD B2C — aplikacje** Wybierz utworzoną aplikację, na przykład *webapp1*.
1. Wybierz pozycję **klucze** , a następnie pozycję **Generuj klucz**.
1. Wybierz pozycję **Zapisz** , aby wyświetlić klucz. Zanotuj wartość pola **Klucz aplikacji**. Ta wartość jest używana jako klucz tajny aplikacji w kodzie aplikacji.

## <a name="next-steps"></a>Następne kroki

W tym artykule zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Rejestrowanie aplikacji internetowej
> * Tworzenie klucza tajnego klienta

Następnie Dowiedz się, jak tworzyć przepływy użytkowników, aby umożliwić użytkownikom rejestrowanie i logowanie się oraz zarządzanie profilami.

> [!div class="nextstepaction"]
> [Tworzenie przepływów użytkowników w Azure Active Directory B2C >](tutorial-create-user-flows.md)
