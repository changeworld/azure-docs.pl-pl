---
title: Dodawanie natywnej aplikacji klienckiej — Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodać natywnej aplikacji klienckiej do dzierżawy usługi Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.component: B2C
ms.openlocfilehash: ee3323ec23b37318dbd80c85d6dd7515ce1ce06b
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/06/2019
ms.locfileid: "55757676"
---
# <a name="add-a-native-client-application-to-your-azure-active-directory-b2c-tenant"></a>Dodawanie natywnej aplikacji klienckiej do dzierżawy usługi Azure Active Directory B2C

Zasoby natywnego klienta muszą być zarejestrowana w Twojej dzierżawie, zanim aplikacja może komunikować się za pomocą usługi Azure Active Directory B2C.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Pamiętaj, że używasz katalogu, który zawiera dzierżawy usługi Azure AD B2C, klikając **filtr katalogów i subskrypcji** w górnym menu i wybierając katalog, który zawiera Twojej dzierżawy.
3. Wybierz **wszystkich usług** w lewym górnym rogu witryny Azure portal, a następnie wyszukaj i wybierz **usługi Azure AD B2C**.
1. Wybierz **aplikacje**, a następnie wybierz pozycję **Dodaj**.
2. Wprowadź nazwę aplikacji. Na przykład *nativeapp1*.
3. Aby uzyskać **Uwzględnij aplikację sieci web / interfejs API sieci web**, wybierz opcję **nie**.
4. Aby uzyskać **Uwzględnij klienta natywnego**, wybierz opcję **tak**.
5. Aby uzyskać **identyfikator URI przekierowania**, wprowadź prawidłowy identyfikator URI przekierowania ze schematem niestandardowym. Istnieją dwie ważne uwagi podczas wybierania identyfikator URI przekierowania:

    - **Unikatowe** — schemat identyfikatora URI przekierowania powinien być unikatowy dla każdej aplikacji. W przykładzie `com.onmicrosoft.contoso.appname://redirect/path`, `com.onmicrosoft.contoso.appname` jest to schemat. Należy przestrzegać tego wzorca. Jeśli dwie aplikacje mają ten sam schemat, użytkownik może wskazać dostawcę o wybranie aplikacji. Jeśli użytkownik dokona nieprawidłowego wyboru, logowanie nie powiedzie się.
    - **Pełne** — identyfikator URI przekierowania musi mieć schemat i ścieżkę. Ścieżka musi zawierać co najmniej jeden ukośnik po nazwie domeny. Na przykład `//contoso/` działa i `//contoso` zakończy się niepowodzeniem. Upewnij się, że identyfikator URI przekierowania nie zawiera znaków specjalnych, takich jak podkreślenia.

6. Kliknij pozycję **Utwórz**.
7. Na stronie właściwości należy zarejestrować identyfikator aplikacji, która będzie używana podczas konfigurowania aplikacji klienta natywnego.
