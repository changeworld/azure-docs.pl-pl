---
title: Konfigurowanie rejestracji i logowania za pomocą konta Facebook — Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Klientom rejestracji i logowania za pomocą kont usługi Facebook w aplikacjach przy użyciu usługi Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 6b90ba89f17b42f4d92c666c3f539fcad3e3227c
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2019
ms.locfileid: "66733525"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>Konfigurowanie rejestracji i logowania za pomocą konta Facebook, za pomocą usługi Azure Active Directory B2C

## <a name="create-a-facebook-application"></a>Tworzenie aplikacji usługi Facebook

Aby użyć konta usługi Facebook jako [dostawcy tożsamości](active-directory-b2c-reference-oauth-code.md) w usłudze Azure Active Directory (Azure AD) B2C, musisz utworzyć aplikację w dzierżawie, który go reprezentuje. Jeśli nie masz jeszcze konta w serwisie Facebook, możesz pobrać na stronie [ https://www.facebook.com/ ](https://www.facebook.com/).

1. Zaloguj się do [serwisu Facebook dla deweloperów](https://developers.facebook.com/) przy użyciu poświadczeń konta serwisu Facebook.
2. Jeśli jeszcze tego nie zrobiono, należy zarejestrować się jako deweloper usługi Facebook. Aby to zrobić, wybierz **wprowadzenie** w prawym górnym rogu strony, Zaakceptuj zasady w serwisie Facebook, a kroki rejestracji.
3. Wybierz **Moje aplikacje** i następnie **Dodaj nową aplikację**.
4. Wprowadź **nazwę wyświetlaną** i prawidłową **E-mail kontaktowy**.
5. Kliknij przycisk **utworzyć identyfikator aplikacji**. Może to wymagać zaakceptować zasady platformy usługi Facebook i ukończyć sprawdzania zabezpieczeń w trybie online.
6. Wybierz **ustawienia** > **podstawowe**.
7. Wybierz **kategorii**, na przykład `Business and Pages`. Ta wartość jest wymagana przez usługi Facebook, ale nie są używane dla usługi Azure AD B2C.
8. W dolnej części strony wybierz **Dodaj platformy**, a następnie wybierz pozycję **witryny sieci Web**.
9. W **adres URL witryny**, wprowadź `https://your-tenant-name.b2clogin.com/` zastępowanie `your-tenant-name` nazwą Twojej dzierżawy. Wprowadź adres URL dla **adres URL zasad zachowania poufności**, na przykład `http://www.contoso.com`. Adres URL zasad jest stroną, których obsługa zapewniające informacje o ochronie prywatności dla aplikacji.
10. Wybierz pozycję **Zapisz zmiany**.
11. W górnej części strony, skopiuj wartość **Identyfikatora aplikacji**.
12. Kliknij przycisk **Pokaż** i skopiuj wartość **klucz tajny aplikacji**. Obie z nich służy do konfigurowania usługi Facebook jako dostawcy tożsamości w dzierżawie. **Klucz tajny aplikacji** jest ważnym poświadczeniem zabezpieczeń.
13. Wybierz znak plus obok pozycji **produktów**, a następnie wybierz pozycję **Konfigurowanie** w obszarze **logowania do usługi Facebook**.
14. W obszarze **logowania do usługi Facebook**, wybierz opcję **ustawienia**.
15. W **identyfikatory URI przekierowania OAuth prawidłowe**, wprowadź `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Zastąp `your-tenant-name` nazwą Twojej dzierżawy. Kliknij przycisk **Zapisz zmiany** w dolnej części strony.
16. Aby udostępnić aplikację usługi Facebook do usługi Azure AD B2C, kliknij selektor stanu w prawym górnym rogu strony i przekształcać je **na** upublicznić aplikacji, a następnie kliknij przycisk **Potwierdź**.  W tym momencie stan powinien zmienić z **rozwoju** do **Live**.

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Konfigurowanie konta usługi Facebook jako dostawcy tożsamości

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
2. Pamiętaj, że używasz katalogu, który zawiera dzierżawy usługi Azure AD B2C, klikając **filtr katalogów i subskrypcji** w górnym menu i wybierając katalog, który zawiera Twojej dzierżawy.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz **dostawców tożsamości**, a następnie wybierz pozycję **Dodaj**.
5. Wprowadź **nazwa**. Na przykład, wprowadź *Facebook*.
6. Wybierz **typ dostawcy tożsamości**, wybierz opcję **Facebook**i kliknij przycisk **OK**.
7. Wybierz **skonfiguruj tego dostawcę tożsamości** i wprowadź zapisaną wcześniej jako identyfikator aplikacji **identyfikator klienta** i wprowadź klucz tajny aplikacji, które są rejestrowane jako **klucz tajny klienta** programu Aplikacja usługi Facebook, która została utworzona wcześniej.
8. Kliknij przycisk **OK** a następnie kliknij przycisk **Utwórz** Aby zapisać konfigurację usługi Facebook.
