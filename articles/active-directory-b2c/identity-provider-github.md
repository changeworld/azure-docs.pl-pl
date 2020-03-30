---
title: Konfigurowanie rejestracji i logowania się za pomocą konta Usługi GitHub
titleSuffix: Azure AD B2C
description: Zapewnij klientom rejestrację i logowanie się za pomocą kont GitHub w aplikacjach przy użyciu usługi Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a7435abf2740800184a6de1aad07bca53cd56cf8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188209"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>Konfigurowanie rejestracji i logowanie się za pomocą konta Usługi GitHub przy użyciu usługi Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-github-oauth-application"></a>Tworzenie aplikacji GitHub OAuth

Aby użyć konta Usługi GitHub jako [dostawcy tożsamości](authorization-code-flow.md) w usłudze Azure Active Directory B2C (Azure AD B2C), należy utworzyć aplikację w dzierżawie, która go reprezentuje. Jeśli nie masz jeszcze konta GitHub, możesz zarejestrować się w [https://www.github.com/](https://www.github.com/)pliku .

1. Zaloguj się w witrynie [gitHub developer](https://github.com/settings/developers) za pomocą poświadczeń GitHub.
1. Wybierz **pozycję Aplikacje OAuth,** a następnie wybierz **pozycję Nowa aplikacja OAuth**.
1. Wprowadź **nazwę aplikacji** i **adres URL strony głównej**.
1. Wprowadź `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` adres **URL wywołania zwrotnego autoryzacji**. Zamień `your-tenant-name` na nazwę dzierżawy usługi Azure AD B2C. Użyj wszystkich małych liter podczas wprowadzania nazwy dzierżawy, nawet jeśli dzierżawca jest zdefiniowany za pomocą wielkich liter w usłudze Azure AD B2C.
1. Kliknij **pozycję Zarejestruj aplikację**.
1. Skopiuj wartości identyfikatora **klienta** i **klucza tajnego klienta**. Należy zarówno dodać dostawcę tożsamości do dzierżawy.

## <a name="configure-a-github-account-as-an-identity-provider"></a>Konfigurowanie konta Usługi GitHub jako dostawcy tożsamości

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD B2C, wybierając filtr **subskrypcja katalog +** w górnym menu i wybierając katalog zawierający dzierżawę.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz **pozycję Dostawcy tożsamości**, a następnie wybierz **gitHub (Wersja zapoznawcza)**.
1. Wprowadź **nazwę**. Na przykład *GitHub*.
1. W przypadku **identyfikatora klienta**wprowadź identyfikator klienta aplikacji GitHub, który został utworzony wcześniej.
1. W przypadku **klucza tajnego Klienta**wprowadź zarejestrowany klucz tajny klienta.
1. Wybierz **pozycję Zapisz**.
