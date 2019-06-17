---
title: 'Samouczek: Integracja usługi Azure Active Directory z aplikacją Soloinsight-CloudGate SSO | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Soloinsight-CloudGate SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 9263c241-85a4-4724-afac-0351d6275958
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d2dc6dcbcdee8df93f34cf4d68b5e08453554bc8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67090010"
---
# <a name="tutorial-integrate-soloinsight-cloudgate-sso-with-azure-active-directory"></a>Samouczek: Integracja logowania jednokrotnego Soloinsight CloudGate za pomocą usługi Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować Soloinsight CloudGate logowanie Jednokrotne z usługą Azure Active Directory (Azure AD). Po zintegrowaniu Soloinsight CloudGate logowanie Jednokrotne z usługą Azure AD, możesz wykonywać następujące czynności:

* Kontrolowanie w usłudze Azure AD, kto ma dostęp do logowania jednokrotnego Soloinsight CloudGate.
* Umożliwianie użytkownikom można automatycznie zalogowany Soloinsight CloudGate logowania jednokrotnego przy użyciu konta usługi Azure AD.
* Zarządzanie Twoimi kontami w jednej centralnej lokalizacji — witryny Azure portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz pobrać miesięcznej bezpłatnej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).
* Usługa rejestracji Jednokrotnej Soloinsight CloudGate logowania jednokrotnego (SSO) włączone subskrypcji.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku, skonfiguruj i przetestuj logowania jednokrotnego usługi Azure AD w środowisku testowym. Obsługuje logowanie Jednokrotne Soloinsight CloudGate **SP** jednokrotne logowanie inicjowane przez.

## <a name="adding-soloinsight-cloudgate-sso-from-the-gallery"></a>Dodawanie aplikacji Soloinsight-CloudGate SSO z galerii

Aby skonfigurować integrację aplikacji Soloinsight-CloudGate SSO z usługą Azure AD, należy dodać aplikację Soloinsight-CloudGate SSO z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **usługi Azure Active Directory** usługi.
1. Przejdź do **aplikacje dla przedsiębiorstw** , a następnie wybierz **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz **nową aplikację**.
1. W **Dodaj z galerii** sekcji, wpisz **logowania jednokrotnego Soloinsight CloudGate** w polu wyszukiwania.
1. Wybierz **logowania jednokrotnego Soloinsight CloudGate** z wyników panelu, a następnie dodać aplikację. Odczekaj kilka sekund, podczas gdy aplikacja zostanie dodany do Twojej dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

Konfiguracja i testowanie logowania jednokrotnego usługi Azure AD przy użyciu logowania jednokrotnego Soloinsight CloudGate za pomocą użytkownika testu o nazwie **Britta Simon**. Logowania jednokrotnego do pracy należy ustanowić relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w Soloinsight CloudGate logowania jednokrotnego.

Aby skonfigurować i przetestować logowania jednokrotnego usługi Azure AD przy użyciu logowania jednokrotnego Soloinsight CloudGate, wykonaj poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-sso)**  aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego Soloinsight CloudGate](#configure-soloinsight-cloudgate-sso)**  do konfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  do testowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  umożliwiające Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego logowania jednokrotnego Soloinsight CloudGate](#create-soloinsight-cloudgate-sso-test-user)**  mieć odpowiednikiem Britta Simon Soloinsight CloudGate Usługa rejestracji Jednokrotnej, połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-sso)**  Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

Wykonaj następujące kroki, aby włączyć logowania jednokrotnego usługi Azure AD w witrynie Azure portal.

1. W [witryny Azure portal](https://portal.azure.com/)na **logowania jednokrotnego Soloinsight CloudGate** strona integracji aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **logowanie jednokrotne**.
1. Na **wybierz jedną metodę logowania jednokrotnego** wybierz **SAML**.
1. Na **Ustaw się logowanie jednokrotne z SAML** kliknij ikonę edycji/pióra **podstawową konfigurację protokołu SAML** edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na **podstawową konfigurację protokołu SAML** strony, wprowadź wartości dla następujących pól:

    1. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<SUBDOMAIN>.sigateway.com/login`

    1. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<SUBDOMAIN>.sigateway.com/process/sso`

   > [!NOTE]
   > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania, co zostało wyjaśnione poniżej w sekcji **Konfigurowanie logowania jednokrotnego w aplikacji Soloinsight-CloudGate SSO** samouczka.

1. Na **Ustaw się logowanie jednokrotne z SAML** strony w **certyfikat podpisywania SAML** sekcji, Znajdź **certyfikat (Base64)** i wybierz **Pobierz** do pobrania certyfikatu i zapisz go na komputerze.

   ![Link pobierania certyfikatu](common/certificatebase64.png)

1. Na **skonfigurować logowanie Jednokrotne Soloinsight CloudGate** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-soloinsight-cloudgate-sso"></a>Konfigurowanie logowania jednokrotnego Soloinsight CloudGate

1. Aby zautomatyzować konfigurację w ramach Soloinsight CloudGate logowania jednokrotnego, musisz zainstalować **rozszerzenia przeglądarki do bezpiecznego Moje aplikacje logowania** , klikając **zainstalować rozszerzenie**.

    ![Moje rozszerzenie aplikacji](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki, kliknij pozycję **Soloinsight CloudGate Instalatora Usługa rejestracji Jednokrotnej** nastąpi bezpośrednie przekierowanie do aplikacji Soloinsight CloudGate logowania jednokrotnego. W tym miejscu podaj poświadczenia administratora do logowania się do logowania jednokrotnego Soloinsight CloudGate. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzowania czynności 3-8.

    ![Konfiguracja instalacji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować logowanie Jednokrotne Soloinsight CloudGate, Otwórz nowe okno przeglądarki sieci web i zaloguj się do witryny firmy logowania jednokrotnego Soloinsight CloudGate jako administrator i wykonaj następujące czynności:

4. Aby uzyskać wartości, które są do wklejenia w witrynie Azure portal podczas konfigurowania podstawowych SAML, zaloguj się do portalu sieci Web CloudGate przy użyciu poświadczeń, a następnie dostęp do ustawień logowania jednokrotnego, które można znaleźć w następującej ścieżce **strona główna > Administracja > Ustawienia systemowe > Ogólne**.

    ![Ustawienia aplikacji CloudGate SSO](./media/soloinsight-cloudgate-sso-tutorial/sso-main-settings.png)

5. **Adres URL użytkownika języka SAML**

    * Kopiowanie łącza, które są dostępne przed **adres URL klienta Saml** i **adresu URL przekierowania** pola, a następnie wklej je w witrynie Azure portal **podstawową konfigurację protokołu SAML** sekcji  **Identyfikator jednostki** i **adres URL odpowiedzi** odpowiednio w polach.

        ![SAMLIdentifier](./media/soloinsight-cloudgate-sso-tutorial/saml-identifier.png)

6. **Certyfikat podpisywania SAML**

    * Przejdź do źródłowego pliku certyfikatu (kodowanie Base64), który został pobrany z witryny Azure portal certyfikat podpisywania SAML listy i kliknij prawym przyciskiem myszy na nim. Wybierz opcję **Edit with Notepad++** (Edytuj za pomocą Notatnika++) z listy. 

        ![SAMLcertificate](./media/soloinsight-cloudgate-sso-tutorial/certificate-file.png)

    * Skopiuj zawartość pliku Notatnika++ certyfikatu (Base64).

        ![Kopiowanie certyfikatu](./media/soloinsight-cloudgate-sso-tutorial/certificate-copy.png)

    * Wklej zawartość w obszarze ustawień logowania jednokrotnego portalu internetowego CloudGate w polu **Certificate** (Certyfikat), a następnie kliknij przycisk zapisywania.

        ![Portal certyfikatu](./media/soloinsight-cloudgate-sso-tutorial/certificate-portal.png)

7. **Default Group** (Grupa domyślna)

    * Wybierz pozycję **Business Admin** (Administrator firmy) z listy rozwijanej **Default Group** (Grupa domyślna) w portalu internetowym CloudGate

        ![Grupa domyślna](./media/soloinsight-cloudgate-sso-tutorial/default-group.png)

8. **Adres URL identyfikatora usługi AD i adres URL logowania**

    * Skopiowany **adres URL logowania** w witrynie Azure portal **skonfigurować logowanie Jednokrotne Soloinsight CloudGate** konfiguracje są wprowadzane w sekcji Ustawienia CloudGate Usługa rejestracji Jednokrotnej w sieci Web w portalu.

    * Wklej **adres URL logowania** łącze z witryny Azure portal w portalu sieci Web CloudGate **adres URL logowania usług AD** pola.

    * Wklej **usługi Azure AD identyfikator** łącze z witryny Azure portal w portalu sieci Web CloudGate **AD identyfikator** pola

        ![Logowanie w usłudze AD](./media/soloinsight-cloudgate-sso-tutorial/ad-login.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzymy użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

1. W okienku po lewej stronie w witrynie Azure portal wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**.
1. Wybierz **nowego użytkownika** w górnej części ekranu.
1. W **użytkownika** właściwości, wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `Britta Simon`.  
   1. W **nazwa_użytkownika** wprowadź username@companydomain.extension. Na przykład `BrittaSimon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można udostępnić Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do logowania jednokrotnego Soloinsight CloudGate.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Soloinsight-CloudGate SSO**.
1. Na stronie Przegląd usługi aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **użytkowników i grup**.

   ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz **Dodaj użytkownika**, a następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okna dialogowego.

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** z listy użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. Jeśli oczekujesz wszelkie wartości roli dla asercji SAML w **wybierz rolę** okno dialogowe, wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-soloinsight-cloudgate-sso-test-user"></a>Tworzenie użytkownika testowego aplikacji Soloinsight-CloudGate SSO

Aby utworzyć użytkownika testowego, wybierz pozycję **Employees** z menu głównego w internetowym portalu aplikacji CloudGate i wypełnij formularz Add new employee (Dodawanie nowego pracownika). Poziom urzędu do przypisania do użytkownika testowego to **Business Admin** (Administrator firmy). Kliknij raz pozycję **Create** (Utwórz) po wypełnieniu wszystkich wymaganych pól.

![Testowanie pracownika](./media/soloinsight-cloudgate-sso-tutorial/employee-test.png)

### <a name="test-sso"></a>Test SSO

Po wybraniu kafelka Usługa rejestracji Jednokrotnej Soloinsight CloudGate w panelu dostępu, powinny być automatycznie zarejestrowaniu w usłudze Soloinsight CloudGate Usługa rejestracji Jednokrotnej, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)