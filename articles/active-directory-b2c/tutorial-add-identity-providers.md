---
title: Samouczek — Dodawanie dostawcy tożsamości do aplikacji — Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodać dostawców tożsamości do aplikacji w usłudze Azure Active Directory B2C w witrynie Azure portal.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 02/01/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 76e12dc6bf9bcb50dc58e7730f3a08dd6a9d4440
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66512194"
---
# <a name="tutorial-add-identity-providers-to-your-applications-in-azure-active-directory-b2c"></a>Samouczek: Dodawanie dostawcy tożsamości do aplikacji w usłudze Azure Active Directory B2C

W swoich aplikacjach możesz umożliwić użytkownikom logowanie się za pomocą różnych dostawców tożsamości. *Dostawca tożsamości* tworzy, i przechowuje informacje dotyczące tożsamości oraz zarządza nimi, zapewniając jednocześnie aplikacjom usługi uwierzytelniania. Możesz dodać dostawców tożsamości, które są obsługiwane przez usługi Azure Active Directory (Azure AD) B2C, aby Twoje [przepływy użytkownika](active-directory-b2c-reference-policies.md) przy użyciu witryny Azure portal.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Tworzenie aplikacji dostawcy tożsamości
> * Dodaj dostawców tożsamości dla Twojej dzierżawy
> * Dodawanie dostawcy tożsamości do przepływu użytkownika

Zazwyczaj używa się tylko jednego dostawcę tożsamości w swoich aplikacjach, ale istnieje możliwość, aby dodać więcej. W tym samouczku przedstawiono sposób dodawania do aplikacji dostawcy tożsamości usługi Azure AD i dostawcy tożsamości usługi Facebook. Dodanie obu tych dostawców tożsamości do aplikacji jest opcjonalne. Możesz również dodać innych dostawców tożsamości, takich jak [Amazon](active-directory-b2c-setup-amzn-app.md), [Github](active-directory-b2c-setup-github-app.md), [Google](active-directory-b2c-setup-goog-app.md), [LinkedIn](active-directory-b2c-setup-li-app.md), [firmyMicrosoft](active-directory-b2c-setup-msa-app.md), lub [Twitter](active-directory-b2c-setup-twitter-app.md). 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

[Utwórz przepływ użytkownika](tutorial-create-user-flows.md) aby użytkownicy mogli zarejestrować się i zaloguj się do aplikacji. 

## <a name="create-applications"></a>Tworzenie aplikacji

Aplikacje dostawcy tożsamości, podaj identyfikator i klucz w celu umożliwienia komunikacji z dzierżawy usługi Azure AD B2C. W tej części samouczka utworzysz aplikację usługi Azure AD i aplikacji usługi Facebook, z których możesz korzystać z identyfikatory i klucze, aby dodać dostawców tożsamości do dzierżawy. W przypadku dodawania tylko jednego z dostawców tożsamości, wystarczy utworzyć aplikację dla tego dostawcy.

### <a name="create-an-azure-active-directory-application"></a>Tworzenie aplikacji usługi Azure Active Directory

Aby włączyć logowania dla użytkowników z usługi Azure AD, musisz zarejestrować aplikację w dzierżawie usługi Azure AD. Dzierżawy usługi Azure AD nie jest taka sama jak dzierżawy usługi Azure AD B2C.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Pamiętaj, że używasz katalogu, który zawiera dzierżawy usługi Azure AD, klikając **filtr katalogów i subskrypcji** w górnym menu i wybierając katalog, który zawiera dzierżawy usługi Azure AD.
3. Wybierz **wszystkich usług** w lewym górnym rogu witryny Azure portal, a następnie wyszukaj i wybierz **rejestracje aplikacji**.
4. Wybierz pozycję **Rejestrowanie nowej aplikacji**.
5. Wprowadź nazwę aplikacji. Na przykład `Azure AD B2C App`.
6. Aby uzyskać **typ aplikacji**, wybierz opcję `Web app / API`.
7. Aby uzyskać **adres URL logowania**, wprowadź następujący adres URL w małych liter, gdzie `your-B2C-tenant-name` jest zastępowana nazwą dzierżawy usługi Azure AD B2C.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```
    
    Na przykład `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
    
    Wszystkie adresy URL powinny teraz być za pomocą [z usługi b2clogin.com](b2clogin.md).

8. Kliknij pozycję **Utwórz**. Kopiuj **identyfikator aplikacji** na później.
9. Wybierz aplikację, a następnie wybierz **ustawienia**.
10. Wybierz **klucze**wprowadź opis klucza, wybierz czas trwania, a następnie kliknij przycisk **Zapisz**. Skopiuj wartość klucza, dzięki czemu można go użyć w dalszej części tego samouczka.

### <a name="create-a-facebook-application"></a>Tworzenie aplikacji usługi Facebook

Aby użyć konta w serwisie Facebook jako dostawcy tożsamości w usłudze Azure AD B2C, musisz utworzyć aplikację w usłudze Facebook. Jeśli nie masz jeszcze konta w serwisie Facebook, możesz pobrać na stronie [ https://www.facebook.com/ ](https://www.facebook.com/).

1. Zaloguj się do [serwisu Facebook dla deweloperów](https://developers.facebook.com/) przy użyciu poświadczeń konta serwisu Facebook.
2. Jeśli jeszcze tego nie zrobiono, należy zarejestrować się jako deweloper usługi Facebook. Aby się zarejestrować, wybrać **zarejestrować** w prawym górnym rogu strony, Zaakceptuj zasady w serwisie Facebook, a kroki rejestracji.
3. Wybierz **Moje aplikacje** a następnie kliknij przycisk **Dodaj nową aplikację**. 
4. Wprowadź **nazwę wyświetlaną** i prawidłową **E-mail kontaktowy**.
5. Kliknij przycisk **utworzyć identyfikator aplikacji**. Może być konieczne zaakceptować zasady platformy usługi Facebook i ukończyć sprawdzania zabezpieczeń w trybie online.
6. Wybierz **ustawienia** > **podstawowe**.
7. Wybierz **kategorii**, na przykład `Business and Pages`. Ta wartość jest wymagana przez usługi Facebook, ale nie są używane dla usługi Azure AD B2C.
8. W dolnej części strony wybierz **Dodaj platformy**, a następnie wybierz pozycję **witryny sieci Web**.
9. W **adres URL witryny**, wprowadź `https://your-tenant-name.b2clogin.com/` zastępowanie `your-tenant-name` nazwą Twojej dzierżawy. Wprowadź adres URL dla **adres URL zasad zachowania poufności**, na przykład `http://www.contoso.com`. Adres URL zasad jest stroną, których obsługa zapewniające informacje o ochronie prywatności dla aplikacji.
10. Wybierz pozycję **Zapisz zmiany**.
11. W górnej części strony, skopiuj wartość **Identyfikatora aplikacji**. 
12. Kliknij przycisk **Pokaż** i skopiuj wartość **klucz tajny aplikacji**. Obie z nich służy do konfigurowania usługi Facebook jako dostawcy tożsamości w dzierżawie. **Klucz tajny aplikacji** jest ważnym poświadczeniem zabezpieczeń.
13. Wybierz **produktów**, a następnie wybierz pozycję **Konfigurowanie** w obszarze **logowania do usługi Facebook**.
14. Wybierz **ustawienia** w obszarze **logowania do usługi Facebook**.
15. W **identyfikatory URI przekierowania OAuth prawidłowe**, wprowadź `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Zastąp `your-tenant-name` nazwą Twojej dzierżawy. Kliknij przycisk **Zapisz zmiany** w dolnej części strony.
16. Aby udostępnić aplikację usługi Facebook do usługi Azure AD B2C, kliknij przycisk **stan** selektora w prawym górnym rogu strony i ustaw ją na **na**. Kliknij pozycję **Potwierdź**. W tym momencie stan powinien zmienić z **rozwoju** do **Live**.

## <a name="add-the-identity-providers"></a>Dodawanie dostawcy tożsamości

Po utworzeniu aplikacji dostawcy tożsamości, który chcesz dodać, możesz dodać dostawcy tożsamości do dzierżawy.

### <a name="add-the-azure-active-directory-identity-provider"></a>Dostawca tożsamości usługi Azure Active Directory

1. Pamiętaj, że używasz katalogu, który zawiera dzierżawy usługi Azure AD B2C, klikając **filtr katalogów i subskrypcji** w górnym menu i wybierając katalog, który zawiera dzierżawy usługi Azure AD B2C.
2. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
3. Wybierz **dostawców tożsamości**, a następnie wybierz pozycję **Dodaj**.
4. Wprowadź **nazwa**. Na przykład, wprowadź *firmy Contoso w usłudze Azure AD*.
5. Wybierz **typ dostawcy tożsamości**, wybierz opcję **Open ID Connect (wersja zapoznawcza)** , a następnie kliknij przycisk **OK**.
6. Kliknij przycisk **skonfiguruj tego dostawcę tożsamości**
7. Aby uzyskać **adres url metadanych**, wprowadź adres URL następujących, zastępując `your-AD-tenant-domain` z nazwą domeny dzierżawy usługi Azure AD.

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    Na przykład `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`.

8. Dla **identyfikator klienta**, wprowadź identyfikator aplikacji, które wcześniej zapisaną i **klucz tajny klienta**, wprowadź wartości klucza, który wcześniej zapisane.
9. Opcjonalnie wprowadź wartość dla **Domain_hint**. Na przykład `ContosoAD`. 
10. Kliknij przycisk **OK**.
11. Wybierz **Mapowanie oświadczeń tego dostawcy tożsamości** i ustaw następujące oświadczeń:
    
    - Aby uzyskać **identyfikator użytkownika**, wprowadź `oid`.
    - Aby uzyskać **nazwę wyświetlaną**, wprowadź `name`.
    - Aby uzyskać **imię**, wprowadź `given_name`.
    - Aby uzyskać **nazwisko**, wprowadź `family_name`.
    - Aby uzyskać **E-mail**, wprowadź `unique_name`.

12. Kliknij przycisk **OK**, a następnie kliknij przycisk **Utwórz** Aby zapisać konfigurację.

### <a name="add-the-facebook-identity-provider"></a>Dostawca tożsamości aplikacji usługi Facebook

1. Wybierz **dostawców tożsamości**, a następnie wybierz pozycję **Dodaj**.
2. Wprowadź **nazwa**. Na przykład, wprowadź *Facebook*.
3. Wybierz **typ dostawcy tożsamości**, wybierz opcję **Facebook**i kliknij przycisk **OK**.
4. Wybierz **skonfiguruj tego dostawcę tożsamości** i wprowadź zapisaną wcześniej jako identyfikator aplikacji **identyfikator klienta**. Wprowadź klucz tajny aplikacji, które są rejestrowane jako **klucz tajny klienta**.
5. Kliknij przycisk **OK** a następnie kliknij przycisk **Utwórz** Aby zapisać konfigurację usługi Facebook.

## <a name="update-the-user-flow"></a>Aktualizacja przepływu użytkownika

W tym samouczku ukończono jako część wymagań wstępnych utworzono przepływ użytkownika dla rejestracji i logowania o nazwie *B2C_1_signupsignin1*. W tej sekcji dodasz dostawców tożsamości do *B2C_1_signupsignin1* przepływu użytkownika.

1. Wybierz **przepływy użytkownika (zasady)** , a następnie wybierz pozycję *B2C_1_signupsignin1* przepływu użytkownika.
2. Wybierz **dostawców tożsamości**, wybierz opcję **Facebook** i **firmy Contoso w usłudze Azure AD** dostawców tożsamości, które zostały dodane.
3. Wybierz pozycję **Zapisz**.

### <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

1. Na stronie Przegląd przepływ użytkownika, który został utworzony, wybierz **uruchomić przepływ użytkownika**.
2. Aby uzyskać **aplikacji**, wybierz aplikację sieci web o nazwie *webapp1* który został wcześniej zarejestrowany. **Adres URL odpowiedzi** powinien być wyświetlony `https://jwt.ms`.
3. Kliknij przycisk **uruchomić przepływ użytkownika**, a następnie zaloguj się przy użyciu dostawcy tożsamości, które zostały wcześniej dodane.
4. Powtórz kroki od 1 do 3 dla innych dostawców tożsamości, które zostały dodane.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie aplikacji dostawcy tożsamości
> * Dodaj dostawców tożsamości dla Twojej dzierżawy
> * Dodawanie dostawcy tożsamości do przepływu użytkownika

> [!div class="nextstepaction"]
> [Dostosowywanie interfejsu użytkownika aplikacji w usłudze Azure Active Directory B2C](tutorial-customize-ui.md)