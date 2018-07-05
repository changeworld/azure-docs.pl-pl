---
title: Konfiguracja dostawcy tożsamości usługi GitHub w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Klientom rejestracji i logowania za pomocą kont usługi GitHub w swoich aplikacjach, które są zabezpieczone przez usługę Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/06/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 3754a169b301bac97f3e12d10b754222e3cf325d
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37443345"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-github-accounts"></a>Usługa Azure Active Directory B2C: Zapewnienie rejestracji i logowania użytkowników z kontami usługi GitHub

> [!NOTE]
> Ta funkcja jest dostępna w wersji zapoznawczej.
> 

W tym artykule pokazano, jak włączyć logowanie dla użytkowników za pomocą konta usługi GitHub.

## <a name="create-a-github-oauth-application"></a>Tworzenie aplikacji OAuth usługi GitHub

Do korzystania z serwisu GitHub, jako dostawcy tożsamości w usłudze Azure AD B2C, musisz utworzyć aplikację OAuth usługi GitHub i dostarczyć odpowiednie parametry.

1. Przejdź do [ustawienia dewelopera GitHub](https://github.com/settings/developers) po zarejestrowaniu się w witrynie GitHub.
1. Kliknij przycisk **Nowa aplikacja OAuth**
1. Wprowadź **Nazwa aplikacji** i **adres URL strony głównej**.
1. Aby uzyskać **adresów URL wywołania zwrotnego autoryzacji**, wprowadź `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`. Zastąp **{dzierżawa}** nazwą dzierżawy usługi Azure AD B2C (na przykład contosob2c.onmicrosoft.com).

    >[!NOTE]
    >Wartość "tenant" musi być tylko małe litery w **adres URL logowania**.

1. Kliknij przycisk **zarejestrować aplikację**.
1. Zapisz wartości **identyfikator klienta** i **klucz tajny klienta**. Będą potrzebne w następnej sekcji.

## <a name="configure-github-as-an-identity-provider-in-your-azure-ad-b2c-tenant"></a>Konfigurowanie usługi GitHub jako dostawcy tożsamości w dzierżawie usługi Azure AD B2C

1. Wykonaj następujące kroki, aby [przejdź do bloku funkcji B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) w witrynie Azure portal.
1. W bloku funkcji B2C, kliknij polecenie **dostawców tożsamości**.
1. Kliknij pozycję **+Dodaj** w górnej części bloku.
1. Podaj przyjazną **nazwa** konfiguracji dostawcy tożsamości. Na przykład wprowadź "GitHub".
1. Kliknij przycisk **typ dostawcy tożsamości**, wybierz opcję **GitHub**i kliknij przycisk **OK**.
1. Kliknij przycisk **skonfiguruj tego dostawcę tożsamości** i wprowadź identyfikator klienta oraz klucz tajny klienta aplikacji OAuth usługi GitHub, które wcześniej zostały skopiowane.
1. Kliknij przycisk **OK** a następnie kliknij przycisk **Utwórz** Aby zapisać konfigurację usługi GitHub.

## <a name="next-steps"></a>Kolejne kroki

Tworzenie lub edytowanie [wbudowanych zasad](active-directory-b2c-reference-policies.md) i Dodaj GitHub jako dostawcy tożsamości.