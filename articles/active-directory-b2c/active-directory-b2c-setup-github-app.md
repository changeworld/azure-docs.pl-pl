---
title: Konfigurowanie rejestrowania i zaloguj się przy użyciu konta usługi GitHub — Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Klientom rejestracji i logowania za pomocą kont usługi GitHub w aplikacjach przy użyciu usługi Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 88d92e081a7b852035dd2b7d3bc9e4e29fefaddd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66508575"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>Konfigurowanie rejestracji i logowania za pomocą konta usługi GitHub za pomocą usługi Azure Active Directory B2C

> [!NOTE]
> Ta funkcja jest dostępna w wersji zapoznawczej.
> 

Aby użyć konta usługi GitHub jako [dostawcy tożsamości](active-directory-b2c-reference-oauth-code.md) w usłudze Azure Active Directory (Azure AD) B2C, musisz utworzyć aplikację w dzierżawie, który go reprezentuje. Jeśli nie masz jeszcze konta usługi GitHub, możesz pobrać na stronie [ https://www.github.com/ ](https://www.github.com/).

## <a name="create-a-github-oauth-application"></a>Tworzenie aplikacji OAuth usługi GitHub

1. Zaloguj się do [GitHub dla deweloperów](https://github.com/settings/developers) witryny sieci Web przy użyciu swoich poświadczeń usługi GitHub.
2. Wybierz **aplikacji OAuth** , a następnie wybierz **Nowa aplikacja OAuth**.
3. Wprowadź **Nazwa aplikacji** i **adres URL strony głównej**.
4. Wprowadź `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` w **adresów URL wywołania zwrotnego autoryzacji**. Zastąp `your-tenant-name` nazwą dzierżawy usługi Azure AD B2C. Podczas wprowadzania nazwa dzierżawy, nawet jeśli dzierżawa jest zdefiniowana z wielkich liter w usłudze Azure AD B2C, należy używać małych liter.
5. Kliknij przycisk **zarejestrować aplikację**.
6. Skopiuj wartości z **identyfikator klienta** i **klucz tajny klienta**. Należy zarówno do dodawania dostawcy tożsamości z dzierżawą.

## <a name="configure-a-github-account-as-an-identity-provider"></a>Konfigurowanie konta usługi GitHub jako dostawcy tożsamości

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
2. Pamiętaj, że używasz katalogu, który zawiera dzierżawy usługi Azure AD B2C, klikając **filtr katalogów i subskrypcji** w górnym menu i wybierając katalog, który zawiera Twojej dzierżawy.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz **dostawców tożsamości**, a następnie wybierz pozycję **Dodaj**.
5. Podaj **nazwa**. Na przykład, wprowadź *GitHub*.
6. Wybierz **typ dostawcy tożsamości**, wybierz opcję **GitHub (wersja zapoznawcza)** i kliknij przycisk **OK**.
7. Wybierz **skonfiguruj tego dostawcę tożsamości** i wprowadź identyfikator klienta, który wcześniej zarejestrowane jako **identyfikator klienta** i wprowadź klucz tajny klienta, które są rejestrowane jako **klucz tajny klienta**aplikacji konto usługi GitHub, która została utworzona wcześniej.
8. Kliknij przycisk **OK** a następnie kliknij przycisk **Utwórz** można zapisać konfiguracji konta usługi GitHub.
