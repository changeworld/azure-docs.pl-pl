---
title: Konfiguracja dostawcy tożsamości usługi GitHub w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Podaj rejestracji i logowania do klientów z kontami usługi GitHub w aplikacjach, które są zabezpieczone przez usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 02/06/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 16c7f34c00bbd5bd0c2be53df2b781a1852b84ff
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34712212"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-github-accounts"></a>Usługa Azure Active Directory B2C: Umożliwiają rejestracji i logowania użytkowników z kontami usługi GitHub

> [!NOTE]
> Ta funkcja jest dostępna w wersji zapoznawczej.
> 

W tym artykule przedstawiono, jak włączyć logowanie użytkowników z kontem usługi GitHub.

## <a name="create-a-github-oauth-application"></a>Tworzenie aplikacji GitHub OAuth

Aby korzystać z usługi GitHub jako dostawca tożsamości w usłudze Azure AD B2C, należy do tworzenia aplikacji GitHub OAuth i dostarczyć prawo parametrów.

1. Przejdź do [ustawień dewelopera GitHub](https://github.com/settings/developers) po zarejestrowaniu się w witrynie GitHub.
1. Kliknij przycisk **Nowa aplikacja OAuth**
1. Wprowadź **Nazwa aplikacji** i **adres URL strony głównej**.
1. Aby uzyskać **adresu URL wywołania zwrotnego autoryzacji**, wprowadź `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`. Zastąp **{dzierżawa}** o nazwie dzierżawy usługi Azure AD B2C (na przykład contosob2c.onmicrosoft.com).

    >[!NOTE]
    >Wartość "dzierżawa" musi być małych liter w **adres URL logowania**.

1. Kliknij przycisk **zarejestrować aplikację**.
1. Zapisywanie wartości **identyfikator klienta** i **klucz tajny klienta**. Będą potrzebne w następnej sekcji.

## <a name="configure-github-as-an-identity-provider-in-your-azure-ad-b2c-tenant"></a>Skonfiguruj funkcję dostawcy tożsamości w dzierżawie usługi Azure AD B2C GitHub

1. Wykonaj następujące kroki, aby [przejdź do bloku funkcji B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) w portalu Azure.
1. W bloku funkcji B2C, kliknij polecenie **dostawców tożsamości**.
1. Kliknij pozycję **+Dodaj** w górnej części bloku.
1. Podaj przyjazną nazwę w polu **nazwa** konfiguracji dostawcy tożsamości. Wprowadź na przykład "GitHub".
1. Kliknij przycisk **typ dostawcy tożsamości**, wybierz pozycję **GitHub**i kliknij przycisk **OK**.
1. Kliknij przycisk **skonfigurować ten dostawca tożsamości** , a następnie wprowadź identyfikator klienta i klucz tajny klienta aplikacji GitHub OAuth, które wcześniej zostały skopiowane.
1. Kliknij przycisk **OK** , a następnie kliknij przycisk **Utwórz** Aby zapisać konfigurację usługi GitHub.

## <a name="next-steps"></a>Kolejne kroki

Utwórz lub Edytuj [wbudowanych zasad](active-directory-b2c-reference-policies.md) i Dodaj GitHub jako dostawcy tożsamości.