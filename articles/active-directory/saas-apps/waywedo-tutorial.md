---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą sposób prowadzenia | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i sposób prowadzenia.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 84fc4f36-ecd1-42c6-8a70-cb0f3dc15655
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: faa23f61e5a213c492a7fb51bfc5b108e5c77946
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2019
ms.locfileid: "67310405"
---
# <a name="tutorial-integrate-way-we-do-with-azure-active-directory"></a>Samouczek: Integracja z usługą Azure Active Directory prowadzenia

W tym samouczku dowiesz się, jak zintegrować sposób prowadzenia w usłudze Azure Active Directory (Azure AD). Po zintegrowaniu sposób możemy zrobić z usługą Azure AD, możesz wykonywać następujące czynności:

* Kontrolowanie w usłudze Azure AD, kto ma dostęp do prowadzenia sposób.
* Umożliwianie użytkownikom można automatycznie zalogowany sposób prowadzenia za pomocą kont usługi Azure AD.
* Zarządzanie Twoimi kontami w jednej centralnej lokalizacji — witryny Azure portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz pobrać miesięcznej bezpłatnej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).
* Sposób możemy logowanie jednokrotne (SSO) włączone subskrypcji.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku, skonfiguruj i przetestuj logowania jednokrotnego usługi Azure AD w środowisku testowym.

* Sposób prowadzenia obsługuje **SP** jednokrotne logowanie inicjowane przez
* Sposób prowadzenia obsługuje **Just In Time** aprowizacji użytkowników

## <a name="adding-way-we-do-from-the-gallery"></a>Dodawanie sposób prowadzenia z galerii

Aby skonfigurować integrację z sposób prowadzenia w usłudze Azure AD, należy dodać sposób prowadzenia z galerii z listą zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **usługi Azure Active Directory** usługi.
1. Przejdź do **aplikacje dla przedsiębiorstw** , a następnie wybierz **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz **nową aplikację**.
1. W **Dodaj z galerii** sekcji, wpisz **sposób prowadzenia** w polu wyszukiwania.
1. Wybierz **sposób prowadzenia** z wyników panelu, a następnie dodać aplikację. Odczekaj kilka sekund, podczas gdy aplikacja zostanie dodany do Twojej dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

Konfiguracja i testowanie logowania jednokrotnego usługi Azure AD za pomocą sposób możemy zrobić za pomocą użytkownika testu o nazwie **B.Simon**. Logowanie Jednokrotne do pracy musisz ustanowić relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w sposób prowadzenia.

Aby skonfigurować i logowania jednokrotnego usługi Azure AD za pomocą sposób prowadzenia testów, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-sso)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Skonfiguruj sposób możemy wykonać logowanie Jednokrotne](#configure-way-we-do-sso)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie sposób testowania użytkownika](#create-way-we-do-test-user)**  — aby odpowiednikiem Britta Simon w sposób czynność tę możemy wykonać jest połączony z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-sso)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

Wykonaj następujące kroki, aby włączyć logowania jednokrotnego usługi Azure AD w witrynie Azure portal.

1. W [witryny Azure portal](https://portal.azure.com/)na **sposób prowadzenia** strona integracji aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **logowanie jednokrotne**.
1. Na **wybierz jedną metodę logowania jednokrotnego** wybierz **SAML**.
1. Na **Ustaw się logowanie jednokrotne z SAML** kliknij ikonę edycji/pióra **podstawową konfigurację protokołu SAML** edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na **podstawową konfigurację protokołu SAML** strony, wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<SUBDOMAIN>.waywedo.com/Authentication/ExternalSignIn`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<SUBDOMAIN>.waywedo.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej w sposób możemy zrobić klienta](mailto:support@waywedo.com) do uzyskania tych wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na **Ustaw się logowanie jednokrotne z SAML** strony w **certyfikat podpisywania SAML** sekcji, Znajdź **certyfikatu (Raw)** i wybierz **Pobierz**do pobrania certyfikatu i zapisz go na komputerze.

   ![Link pobierania certyfikatu](common/certificateraw.png)

1. Na **skonfigurować sposób prowadzenia** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-way-we-do-sso"></a>Skonfiguruj sposób prowadzenia logowania jednokrotnego

1. Aby zautomatyzować konfigurację w sposób możemy zrobić, musisz zainstalować **rozszerzenia przeglądarki do bezpiecznego Moje aplikacje logowania** , klikając **zainstalować rozszerzenie**.

    ![Moje rozszerzenie aplikacji](common/install-myappssecure-extension.png)

1. Po dodaniu rozszerzenia do przeglądarki, kliknij pozycję **Instalatora sposób prowadzenia** bezpośredni sposób prowadzenia aplikacji. W tym miejscu podaj poświadczenia administratora do logowania się do prowadzenia sposób. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i zautomatyzować kroki od 3 do 6.

    ![Konfiguracja instalacji](common/setup-sso.png)

1. Jeśli chcesz ręcznie skonfigurować sposób możemy zrobić, Otwórz nowe okno przeglądarki sieci web i zaloguj się do witryny firmy sposób prowadzenia jako administrator i wykonaj następujące czynności:

1. Kliknij przycisk **ikona osoby** w prawym górnym rogu dowolnej stronie w sposób prowadzenia kliknięcie **konta** w menu rozwijanym.

    ![Sposób prowadzenia konta](./media/waywedo-tutorial/tutorial_waywedo_account.png)

1. Kliknij przycisk **ikonę menu** można otworzyć menu nawigacji wypychania i kliknij przycisk **Single Sign On**.

    ![Sposób prowadzenia pojedynczego](./media/waywedo-tutorial/tutorial_waywedo_single.png)

1. Na **ustawienia rejestracji jednokrotnej** strony, wykonaj następujące czynności:

    ![Sposób prowadzenia Zapisz](./media/waywedo-tutorial/tutorial_waywedo_save.png)

    a. Kliknij przycisk **włączyć logowanie jednokrotne** Przełącz, aby **tak** włączyć logowanie jednokrotne.

    b. W **nazwa rejestracji jednokrotnej** polu tekstowym wprowadź nazwę.

    c. W **identyfikator jednostki** pola tekstowego, Wklej wartość **usługi Azure AD identyfikator**, który skopiowano z witryny Azure portal.

    d. W polu tekstowym **Adres URL logowania jednokrotnego protokołu SAML** wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.

    e. Przekaż certyfikat, klikając **wybierz** znajdujący się obok **certyfikatu**.

    f. **Ustawienia opcjonalne** -
    
    * Włącz haseł — Jeśli ta opcja jest wyłączona, regularne hasła funkcji dla sposób prowadzenia, dzięki czemu użytkownicy mogą tylko używać logowania jednokrotnego.

    * Włączanie automatycznego aprowizowania — Jeśli ta opcja jest włączona, adres e-mail używany do logowania jednokrotnego będą automatycznie porównywane do listy użytkowników w sposób prowadzenia. Jeśli adres e-mail nie jest zgodny z aktywnego użytkownika w sposób prowadzenia, automatycznie dodaje nowe konto użytkownika osoby, logowania, żądanie wszystkie brakujące informacje.

      > [!NOTE]
      > Użytkownicy dodani za pomocą logowania jednokrotnego są dodawane jako użytkownik ogólny i nie przypisano rolę w systemie. Administrator będzie mógł go w programie i modyfikowanie ich rola zabezpieczeń jako edytor lub administrator i można także przypisać organizacyjny jeden lub kilka ról.

    g. Kliknij przycisk **Zapisz** można utrwalić ustawień.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzymy użytkownika testowego w witrynie Azure portal, o nazwie B.Simon.

1. W okienku po lewej stronie w witrynie Azure portal wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**.
1. Wybierz **nowego użytkownika** w górnej części ekranu.
1. W **użytkownika** właściwości, wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W **nazwa_użytkownika** wprowadź username@companydomain.extension. Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można udostępnić B.Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do prowadzenia sposób.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz **sposób prowadzenia**.
1. Na stronie Przegląd usługi aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **użytkowników i grup**.

   ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz **Dodaj użytkownika**, a następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okna dialogowego.

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W **użytkowników i grup** okno dialogowe, wybierz opcję **B.Simon** z listy użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. Jeśli oczekujesz wszelkie wartości roli dla asercji SAML w **wybierz rolę** okno dialogowe, wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-way-we-do-test-user"></a>Tworzenie użytkownika testowego sposób prowadzenia

W tej sekcji użytkownika o nazwie Britta Simon jest tworzony w sposób prowadzenia. Sposób prowadzenia obsługuje aprowizacji użytkowników w czasie, który jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w sposób prowadzenia, nowy katalog jest tworzony po uwierzytelnieniu.

> [!Note]
> Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej w sposób możemy zrobić klienta](mailto:support@waywedo.com).

### <a name="test-sso"></a>Test SSO

Po wybraniu kafelka sposób prowadzenia w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze sposób prowadzenia dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)