---
title: Konfigurowanie rejestracji i logowania za pomocą konta Amazon — Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Klientom rejestracji i logowania za pomocą konta Amazon w aplikacjach przy użyciu usługi Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a796826892942879e42b2d8fd22b8cc0208a2174
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66508634"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Konfigurowanie rejestracji i logowania za pomocą konta Amazon przy użyciu usługi Azure Active Directory B2C

## <a name="create-an-amazon-application"></a>Tworzenie aplikacji Amazon

Aby użyć konta Amazon jako [dostawcy tożsamości](active-directory-b2c-reference-oauth-code.md) w usłudze Azure Active Directory (Azure AD) B2C, musisz utworzyć aplikację w dzierżawie, który go reprezentuje. Jeśli nie masz jeszcze konta Amazon możesz pobrać na stronie [ https://www.amazon.com/ ](https://www.amazon.com/).

1. Zaloguj się do [Centrum deweloperów Amazon](https://login.amazon.com/) przy użyciu poświadczeń konta Amazon.
2. Jeśli jeszcze tego nie zrobiono, kliknij przycisk **Zarejestruj**, postępuj zgodnie z instrukcjami rejestracji dla deweloperów i zaakceptować zasady.
3. Wybierz **zarejestrować nową aplikację**.
4. Wprowadź **nazwa**, **opis**, i **adres URL zasad zachowania powiadomienie**, a następnie kliknij przycisk **Zapisz**. Powiadomienie dotyczące prywatności jest strona, którą zarządzasz, która zawiera informacje o ochronie prywatności dla użytkowników.
5. W **ustawień sieci Web** sekcji, skopiuj wartości z **identyfikator klienta**. Wybierz **wyświetlić wpis tajny** można pobrać klucza tajnego klienta, a następnie skopiuj go. Należy dysponować je, aby skonfigurować konto usługi Amazon jako dostawcy tożsamości w dzierżawie. **Klucz tajny klienta** jest ważnym poświadczeniem zabezpieczeń.
6. W **ustawień sieci Web** zaznacz **Edytuj**, a następnie wprowadź `https://your-tenant-name.b2clogin.com` w **dozwolone źródła JavaScript** i `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` w **dozwolone Zwraca adresy URL**. Zastąp `your-tenant-name` nazwą Twojej dzierżawy. Należy używać małych liter, podczas wprowadzania nazwa dzierżawy, nawet jeśli dzierżawa jest zdefiniowana z wielkich liter w usłudze Azure AD B2C.
7. Kliknij pozycję **Zapisz**.

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>Konfigurowanie konta Amazon jako dostawcy tożsamości

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
2. Pamiętaj, że używasz katalogu, który zawiera dzierżawy usługi Azure AD B2C, klikając **filtr katalogów i subskrypcji** w górnym menu i wybierając katalog, który zawiera Twojej dzierżawy.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz **dostawców tożsamości**, a następnie wybierz pozycję **Dodaj**.
5. Wprowadź **nazwa**. Na przykład, wprowadź *Amazon*.
6. Wybierz **typ dostawcy tożsamości**, wybierz opcję **Amazon**i kliknij przycisk **OK**.
7. Wybierz **skonfiguruj tego dostawcę tożsamości** i wprowadź identyfikator klienta, który wcześniej zarejestrowane jako **identyfikator klienta** i wprowadź klucz tajny klienta, które są rejestrowane jako **klucz tajny klienta**aplikacji Amazon, która została utworzona wcześniej.
8. Kliknij przycisk **OK** a następnie kliknij przycisk **Utwórz** Aby zapisać konfigurację Amazon.

