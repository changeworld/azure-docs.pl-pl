---
title: Skonfiguruj konto i zaloguj się przy użyciu konta usługi GitHub
titleSuffix: Azure AD B2C
description: Klientom rejestracji i logowania za pomocą kont usługi GitHub w aplikacjach przy użyciu usługi Azure Active Directory B2C.
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
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78188209"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>Konfigurowanie rejestracji i logowania za pomocą konta usługi GitHub za pomocą usługi Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-github-oauth-application"></a>Tworzenie aplikacji OAuth usługi GitHub

Aby użyć konta usługi GitHub jako [dostawcy tożsamości](authorization-code-flow.md) w programie Azure Active Directory B2C (Azure AD B2C), musisz utworzyć aplikację w swojej dzierżawie, która go reprezentuje. Jeśli nie masz jeszcze konta usługi GitHub, możesz zarejestrować się w [https://www.github.com/](https://www.github.com/).

1. Zaloguj się do witryny sieci Web dla [deweloperów usługi GitHub](https://github.com/settings/developers) przy użyciu swoich poświadczeń w usłudze GitHub.
1. Wybierz pozycję **aplikacje OAuth** , a następnie wybierz pozycję **Nowa aplikacja OAuth**.
1. Wprowadź **nazwę aplikacji** i **adres URL strony głównej**.
1. Wprowadź `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` w **adresie URL wywołania zwrotnego autoryzacji**. Zastąp `your-tenant-name` nazwą dzierżawy Azure AD B2C. Podczas wprowadzania nazwa dzierżawy, nawet jeśli dzierżawa jest zdefiniowana z wielkich liter w usłudze Azure AD B2C, należy używać małych liter.
1. Kliknij pozycję **zarejestruj aplikację**.
1. Skopiuj wartości **Identyfikator klienta** i **klucz tajny klienta**. Należy zarówno do dodawania dostawcy tożsamości z dzierżawą.

## <a name="configure-a-github-account-as-an-identity-provider"></a>Konfigurowanie konta usługi GitHub jako dostawcy tożsamości

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając pozycję **katalog i subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawcę.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz pozycję **dostawcy tożsamości**, a następnie wybierz pozycję **GitHub (wersja zapoznawcza)** .
1. Wprowadź **nazwę**. Na przykład serwis *GitHub*.
1. W polu **Identyfikator klienta**wprowadź identyfikator klienta utworzonej wcześniej aplikacji usługi GitHub.
1. W polu **klucz tajny klienta**Wprowadź zarejestrowany wpis tajny klienta.
1. Wybierz pozycję **Zapisz**.
