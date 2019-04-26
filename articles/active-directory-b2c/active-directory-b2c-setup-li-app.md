---
title: Konfigurowanie rejestracji i logowania za pomocą konta LinkedIn — Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Klientom rejestracji i logowania za pomocą kont usługi LinkedIn w aplikacjach przy użyciu usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: de58f960842e0a4f8e9b964774ce62b3e2772113
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60316385"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Konfigurowanie rejestracji i logowania za pomocą konta LinkedIn, za pomocą usługi Azure Active Directory B2C

## <a name="create-a-linkedin-application"></a>Utwórz aplikację usługi LinkedIn

Aby użyć konta LinkedIn jako [dostawcy tożsamości](active-directory-b2c-reference-oauth-code.md) w usłudze Azure Active Directory (Azure AD) B2C, musisz utworzyć aplikację w dzierżawie, który go reprezentuje. Jeśli nie masz jeszcze konta usługi LinkedIn, możesz pobrać na stronie [ https://www.linkedin.com/ ](https://www.linkedin.com/).

1. Zaloguj się do [witryny sieci Web deweloperów LinkedIn](https://www.developer.linkedin.com/) przy użyciu poświadczeń konta usługi LinkedIn.
2. Wybierz **Moje aplikacje**, a następnie kliknij przycisk **tworzenia aplikacji**.
3. Wprowadź **nazwy firmy**, **Nazwa aplikacji**, **opis aplikacji**, **Logo aplikacji**, **użycia aplikacji** , **Adres URL witryny internetowej**, **firmowa Poczta E-mail**, i **Telefon służbowy**.
4. Zaakceptuj **LinkedIn API warunki użytkowania** i kliknij przycisk **przesyłania**.
5. Skopiuj wartości z **identyfikator klienta** i **klucz tajny klienta**. Znajdziesz je w obszarze **klucze uwierzytelniania**. Konieczne będzie, obie z nich, aby skonfigurować usługi LinkedIn jako dostawcy tożsamości w dzierżawie. **Klucz tajny klienta** jest ważnym poświadczeniem zabezpieczeń.
6. Wprowadź `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` w **autoryzacji adresów URL przekierowania**. Zastąp `your-tenant-name` nazwą Twojej dzierżawy. Należy używać małych liter, podczas wprowadzania nazwa dzierżawy, nawet jeśli dzierżawa jest zdefiniowana z wielkich liter w usłudze Azure AD B2C. Wybierz **Dodaj**, a następnie kliknij przycisk **aktualizacji**.

## <a name="configure-a-linkedin-account-as-an-identity-provider"></a>Konfigurowanie konta usługi LinkedIn jako dostawcy tożsamości

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
2. Pamiętaj, że używasz katalogu, który zawiera dzierżawy usługi Azure AD B2C, klikając **filtr katalogów i subskrypcji** w górnym menu i wybierając katalog, który zawiera Twojej dzierżawy.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz **dostawców tożsamości**, a następnie wybierz pozycję **Dodaj**.
5. Podaj **nazwa**. Na przykład, wprowadź *LinkedIn*.
6. Wybierz **typ dostawcy tożsamości**, wybierz opcję **LinkedIn**i kliknij przycisk **OK**.
7. Wybierz **skonfiguruj tego dostawcę tożsamości** i wprowadź identyfikator klienta, który wcześniej zarejestrowane jako **identyfikator klienta** i wprowadź klucz tajny klienta, które są rejestrowane jako **klucz tajny klienta**aplikacji konto LinkedIn, która została utworzona wcześniej.
8. Kliknij przycisk **OK** a następnie kliknij przycisk **Utwórz** można zapisać konfiguracji konta usługi LinkedIn.

## <a name="migration-from-v10-to-v20"></a>Migracja z wersji 1.0 do wersji 2.0

Niedawno w serwisie LinkedIn [zaktualizować ich interfejsów API w wersji 1.0 do wersji 2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). W ramach migracji usługi Azure AD B2C jest tylko możliwość uzyskania pełną nazwę użytkownika usługi LinkedIn podczas rejestracji. Jeśli adres e-mail jest jednym z atrybutów, które są zbierane podczas tworzenia konta, użytkownik musi ręcznie wprowadzić adres e-mail i zweryfikuje go.
