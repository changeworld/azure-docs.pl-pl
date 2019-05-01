---
title: Dodawanie natywnej aplikacji klienckiej — Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodać natywnej aplikacji klienckiej do dzierżawy usługi Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 772b6b8a4d71b7d1c6418651ee0a4ee7b03af0cc
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64703910"
---
# <a name="add-a-native-client-application-to-your-azure-active-directory-b2c-tenant"></a>Dodawanie natywnej aplikacji klienckiej do dzierżawy usługi Azure Active Directory B2C

Zasoby natywnego klienta muszą być zarejestrowana w Twojej dzierżawie, zanim aplikacja może komunikować się za pomocą usługi Azure Active Directory B2C.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Upewnij się, że używasz katalogu zawierającego Twoją dzierżawę usługi Azure AD B2C, klikając pozycję **Filtr katalogu i subskrypcji** w górnym menu i wybierając katalog zawierający Twoją dzierżawę.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz pozycję **Aplikacje**, a następnie wybierz polecenie **Dodaj**.
2. Wprowadź nazwę aplikacji. Na przykład *nativeapp1*.
3. Dla pozycji **Uwzględnij aplikację internetową/internetowy interfejs API** wybierz wartość **Nie**.
4. Dla pozycji **Uwzględnij klienta natywnego** wybierz wartość **Tak**.
5. Dla pozycji **Identyfikator URI przekierowania** wprowadź prawidłowy identyfikator URI przekierowania ze schematem niestandardowym. Istnieją dwie ważne kwestie do rozważenia podczas wybierania identyfikatora URI przekierowania:

    - **Unikatowość** — schemat identyfikatora URI przekierowania powinien być unikatowy dla każdej aplikacji. W przykładzie `com.onmicrosoft.contoso.appname://redirect/path` jest to schemat `com.onmicrosoft.contoso.appname`. Należy przestrzegać tego wzorca. Jeśli dwie aplikacje mają ten sam schemat, użytkownik ma możliwość wyboru aplikacji. Jeśli użytkownik dokona nieprawidłowego wyboru, logowanie nie powiedzie się.
    - **Kompletność** — identyfikator URI przekierowania musi mieć schemat i ścieżkę. Ścieżka musi zawierać co najmniej jeden ukośnik po nazwie domeny. Na przykład `//contoso/` działa a `//contoso` zakończy się niepowodzeniem. Upewnij się, że identyfikator URI przekierowania nie zawiera znaków specjalnych, takich jak podkreślenia.

6. Kliknij pozycję **Utwórz**.
7. Na stronie właściwości należy zarejestrować identyfikator aplikacji, która będzie używana podczas konfigurowania aplikacji klienta natywnego.
