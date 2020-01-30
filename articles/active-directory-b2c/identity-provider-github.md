---
title: Skonfiguruj konto i zaloguj się przy użyciu konta usługi GitHub
titleSuffix: Azure AD B2C
description: Klientom rejestracji i logowania za pomocą kont usługi GitHub w aplikacjach przy użyciu usługi Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 6b8abeda3588eae03d2c281f9e691d66ea0b39a3
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847552"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>Konfigurowanie rejestracji i logowania za pomocą konta usługi GitHub za pomocą usługi Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-github-oauth-application"></a>Tworzenie aplikacji OAuth usługi GitHub

Aby użyć konta usługi GitHub jako [dostawcy tożsamości](authorization-code-flow.md) w programie Azure Active Directory B2C (Azure AD B2C), musisz utworzyć aplikację w swojej dzierżawie, która go reprezentuje. Jeśli nie masz jeszcze konta usługi GitHub, możesz zarejestrować się w [https://www.github.com/](https://www.github.com/).

1. Zaloguj się do [GitHub dla deweloperów](https://github.com/settings/developers) witryny sieci Web przy użyciu swoich poświadczeń usługi GitHub.
1. Wybierz **aplikacji OAuth** , a następnie wybierz **Nowa aplikacja OAuth**.
1. Wprowadź **Nazwa aplikacji** i **adres URL strony głównej**.
1. Wprowadź `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` w **adresów URL wywołania zwrotnego autoryzacji**. Zastąp `your-tenant-name` nazwą dzierżawy usługi Azure AD B2C. Podczas wprowadzania nazwa dzierżawy, nawet jeśli dzierżawa jest zdefiniowana z wielkich liter w usłudze Azure AD B2C, należy używać małych liter.
1. Kliknij przycisk **zarejestrować aplikację**.
1. Skopiuj wartości z **identyfikator klienta** i **klucz tajny klienta**. Należy zarówno do dodawania dostawcy tożsamości z dzierżawą.

## <a name="configure-a-github-account-as-an-identity-provider"></a>Konfigurowanie konta usługi GitHub jako dostawcy tożsamości

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając pozycję **katalog i subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawcę.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz pozycję **dostawcy tożsamości**, a następnie wybierz pozycję **GitHub (wersja zapoznawcza)** .
1. Wprowadź **nazwę**. Na przykład serwis *GitHub*.
1. W polu **Identyfikator klienta**wprowadź identyfikator klienta utworzonej wcześniej aplikacji usługi GitHub.
1. W polu **klucz tajny klienta**Wprowadź zarejestrowany wpis tajny klienta.
1. Wybierz pozycję **Zapisz**.
