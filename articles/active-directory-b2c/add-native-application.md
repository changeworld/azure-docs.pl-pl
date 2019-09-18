---
title: Dodawanie natywnej aplikacji klienckiej — Azure Active Directory B2C | Microsoft Docs
description: Dowiedz się, jak dodać natywną aplikację kliencką do dzierżawy Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 6fc953d5c6109fbc6eacbd946ecd112db4639fa5
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064587"
---
# <a name="add-a-native-client-application-to-your-azure-active-directory-b2c-tenant"></a>Dodawanie natywnej aplikacji klienckiej do dzierżawy Azure Active Directory B2C

Natywne zasoby klienta muszą być zarejestrowane w dzierżawie, zanim aplikacja będzie mogła komunikować się z Azure Active Directory B2C.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając pozycję **katalog i subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawcę.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz pozycję **Aplikacje**, a następnie wybierz polecenie **Dodaj**.
2. Wprowadź nazwę aplikacji. Na przykład *nativeapp1*.
3. Dla pozycji **Uwzględnij aplikację internetową/internetowy interfejs API** wybierz wartość **Nie**.
4. Dla pozycji **Uwzględnij klienta natywnego** wybierz wartość **Tak**.
5. Dla pozycji **Identyfikator URI przekierowania** wprowadź prawidłowy identyfikator URI przekierowania ze schematem niestandardowym. Istnieją dwie ważne kwestie do rozważenia podczas wybierania identyfikatora URI przekierowania:

    - **Unikatowość** — schemat identyfikatora URI przekierowania powinien być unikatowy dla każdej aplikacji. W przykładzie `com.onmicrosoft.contoso.appname://redirect/path` jest to schemat `com.onmicrosoft.contoso.appname`. Należy przestrzegać tego wzorca. Jeśli dwie aplikacje mają ten sam schemat, użytkownik ma możliwość wyboru aplikacji. Jeśli użytkownik dokona nieprawidłowego wyboru, logowanie nie powiedzie się.
    - **Kompletność** — identyfikator URI przekierowania musi mieć schemat i ścieżkę. Ścieżka musi zawierać co najmniej jeden ukośnik po nazwie domeny. Na przykład `//contoso/` działa a `//contoso` zakończy się niepowodzeniem. Upewnij się, że identyfikator URI przekierowania nie zawiera znaków specjalnych, takich jak podkreślenia.

6. Kliknij przycisk **Utwórz**.
7. Na stronie właściwości Zapisz identyfikator aplikacji, który będzie używany podczas konfigurowania natywnej aplikacji klienckiej.
