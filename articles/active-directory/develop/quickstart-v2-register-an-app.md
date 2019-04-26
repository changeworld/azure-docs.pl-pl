---
title: Rejestrowanie aplikacji przy użyciu punktu końcowego usługi Azure AD w wersji 2.0 | Microsoft Docs
description: Dowiedz się, jak zarejestrować aplikację na platformie firmy Microsoft, aby umożliwić jej logowanie i uzyskiwanie dostępu do usług firmy Microsoft przy użyciu punktu końcowego usługi Azure AD w wersji 2.0.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: bb2f701f-3bc3-4759-94a5-8b9d53a8a0b6
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/02/2018
ms.author: celested
ms.reviewer: lenalepa
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6221fb7575fc267030929b449cba48cff8563f27
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60298788"
---
# <a name="quickstart-register-an-app-with-the-azure-active-directory-v20-endpoint"></a>Szybki start: Rejestrowanie aplikacji z punktem końcowym usługi Azure Active Directory w wersji 2.0

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Aby utworzyć aplikację obsługującą logowanie zarówno za pomocą osobistych kont Microsoft (MSA), jak i kont służbowych (Azure AD), należy zarejestrować aplikację przy użyciu punktu końcowego usługi Azure Active Directory (Azure AD) w wersji 2.0. Obecnie nie ma istniejących aplikacji, których możesz używać zarówno z kontami Azure AD, jak i MSA — musisz utworzyć nową aplikację.

Nie wszystkie scenariusze i funkcje usługi Azure AD są obsługiwane przez punkt końcowy w wersji 2.0. Aby określić, czy punkt końcowy w wersji 2.0 będzie odpowiedni, zapoznaj się z [ograniczeniami punktu końcowego w wersji 2.0](active-directory-v2-limitations.md).

> [!NOTE]
> Rejestrujesz nową aplikację? Wypróbuj nowe środowisko **Rejestracje aplikacji (wersja zapoznawcza)** w witrynie Azure Portal. Aby rozpocząć pracę, zobacz [Rejestrowanie aplikacji (wersja zapoznawcza)](quickstart-register-app.md).

## <a name="step-1-sign-in-to-the-microsoft-application-registration-portal"></a>Krok 1: Logowanie do portalu rejestracji aplikacji firmy Microsoft

1. Przejdź do portalu rejestracji aplikacji firmy Microsoft pod adresem [https://apps.dev.microsoft.com/](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).
1. Zaloguj się, używając osobistego lub służbowego konta Microsoft. Jeśli nie masz żadnego z nich, załóż nowe konto osobiste.
1. Gotowe? Powinna być teraz widoczna lista aplikacji firmy Microsoft, prawdopodobnie pusta. Zmienimy to teraz.

## <a name="step-2-register-an-app"></a>Krok 2: Rejestracja aplikacji

1. Wybierz pozycję **Dodaj aplikację** i wprowadź nazwę aplikacji.
    W portalu do aplikacji zostanie przypisany globalnie unikatowy identyfikator aplikacji, który będzie później używany w kodzie. Jeśli aplikacja zawiera składnik po stronie serwera, który wymaga tokenów dostępu w celu wywoływania interfejsów API (np.: pakiet Office, platforma Azure lub Twój własny internetowy interfejs API), w tym miejscu należy również utworzyć **klucz tajny aplikacji**.
1. Następnie dodaj **Platformy** używane przez aplikację.
    * W przypadku aplikacji internetowych podaj **Identyfikator URI przekierowania**, do którego mogą być wysyłane komunikaty dotyczące logowania.
    * W przypadku aplikacji mobilnych skopiuj automatycznie utworzony domyślny identyfikator URI przekierowania.
    * W przypadku internetowych interfejsów API zostanie automatycznie utworzony domyślny zakres na potrzeby dostępu do internetowego interfejsu API.
        Możesz dodać dodatkowe zakresy za pomocą przycisku **Dodaj zakres**. Możesz też dodać wszelkie aplikacje wstępnie autoryzowane do korzystania z internetowego interfejsu API za pomocą formularza **Wstępnie autoryzowane aplikacje**.
1. Opcjonalnie możesz też dostosować wygląd i działanie strony logowania w sekcji **Profil**. 
1. **Zapisz** zmiany, zanim przejdziesz dalej.

> [!NOTE]
> Gdy rejestrujesz aplikację w portalu [https://apps.dev.microsoft.com/](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), jest ona rejestrowana w głównej dzierżawie konta, którego użyto do zalogowania się w portalu. Oznacza to, że nie można zarejestrować aplikacji w dzierżawie usługi Azure AD przy użyciu osobistego konta Microsoft. Jeśli chcesz zarejestrować aplikację w konkretnej dzierżawie, zaloguj się za pomocą konta pierwotnie utworzonego w tej dzierżawie.

## <a name="next-steps"></a>Kolejne kroki

Ponieważ masz już aplikację firmy Microsoft, możesz przejść do jednego z przewodników Szybki start dotyczących punktu końcowego w wersji 2.0. Poniżej przedstawiono kilka zaleceń:

[!INCLUDE [active-directory-v2-quickstart-table](~/includes/active-directory-v2-quickstart-table.md)]
