---
title: 'Samouczek: Integracja usługi Azure Active Directory z aplikacją Dropbox dla Firm | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Dropbox dla Firm.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 63502412-758b-4b46-a580-0e8e130791a1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2109c3f67a666d0c379cc188c07968c8b31d6a1
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65989676"
---
# <a name="tutorial-integrate-dropbox-for-business-with-azure-active-directory"></a>Samouczek: Integrowanie usługi Dropbox dla firm z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować usługa Dropbox dla firm z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Dropbox dla firm z usługą Azure AD, możesz wykonywać następujące czynności:

* Kontrolowanie w usłudze Azure AD, kto ma dostęp do usługi Dropbox dla firm.
* Umożliwianie użytkownikom można automatycznie zalogowany do usługi Dropbox dla firm za pomocą kont usługi Azure AD.
* Zarządzanie Twoimi kontami w jednej centralnej lokalizacji — witryny Azure portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz pobrać miesięcznej bezpłatnej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).
* Usługa Dropbox dla firm logowania jednokrotnego (SSO) włączone subskrypcji.

## <a name="scenario-description"></a>Opis scenariusza

* W tym samouczku, skonfiguruj i przetestuj logowania jednokrotnego usługi Azure AD w środowisku testowym. Aplikacja Dropbox dla Firm obsługuje logowanie jednokrotne inicjowane przez **SP**

* Aplikacja Dropbox dla Firm obsługuje aprowizowanie użytkowników typu **Just In Time**

## <a name="adding-dropbox-for-business-from-the-gallery"></a>Dodawanie aplikacji Dropbox dla Firm z galerii

Aby skonfigurować integrację aplikacji Dropbox dla Firm z usługą Azure AD, musisz dodać aplikację Dropbox dla Firm z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **usługi Azure Active Directory** usługi.
1. Przejdź do **aplikacje dla przedsiębiorstw** , a następnie wybierz **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz **nową aplikację**.
1. W **Dodaj z galerii** sekcji, wpisz **usługa Dropbox dla firm** w polu wyszukiwania.
1. Wybierz **usługa Dropbox dla firm** z wyników panelu, a następnie dodać aplikację. Odczekaj kilka sekund, podczas gdy aplikacja zostanie dodany do Twojej dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

Konfiguracja i testowanie logowania jednokrotnego usługi Azure AD z usługi Dropbox dla firm przy użyciu użytkownika testu o nazwie **Britta Simon**. Logowanie Jednokrotne do pracy musisz ustanowić relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w usłudze Dropbox dla firm.

Aby skonfigurować i przetestować logowania jednokrotnego usługi Azure AD z usługi Dropbox dla firm, wykonaj poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-sso)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie usługi Dropbox dla firm logowania jednokrotnego](#configure-dropbox-for-business-sso)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego aplikacji Dropbox dla Firm](#create-dropbox-for-business-test-user)** — aby mieć w aplikacji Dropbox dla Firm odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-sso)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

Wykonaj następujące kroki, aby włączyć logowania jednokrotnego usługi Azure AD w witrynie Azure portal.

1. W [witryny Azure portal](https://portal.azure.com/)na **usługa Dropbox dla firm** strona integracji aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **logowanie jednokrotne**.
1. Na **wybierz jedną metodę logowania jednokrotnego** wybierz **SAML**.
1. Na **Ustaw się logowanie jednokrotne z SAML** kliknij ikonę edycji/pióra **podstawową konfigurację protokołu SAML** edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na **podstawową konfigurację protokołu SAML** strony, wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://www.dropbox.com/sso/<id>`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz wartość: `Dropbox`

    > [!NOTE]
    > Podana wyżej wartość adresu URL logowania nie jest prawdziwa. Ta wartość zostanie zaktualizowana przy użyciu rzeczywistego adresu URL logowania, co objaśniono w dalszej części tego samouczka.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Skonfiguruj aplikację Dropbox dla Firm** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-dropbox-for-business-sso"></a>Konfigurowanie usługi Dropbox dla firm logowania jednokrotnego

1. Aby zautomatyzować konfigurację w usłudze Dropbox dla firm, należy zainstalować **rozszerzenia przeglądarki do bezpiecznego Moje aplikacje logowania** , klikając **zainstalować rozszerzenie**.

    ![Moje rozszerzenie aplikacji](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki, kliknij pozycję **Instalatora Dropbox dla firm** nastąpi bezpośrednie przekierowanie do usługi Dropbox dla aplikacji biznesowych. W tym miejscu należy podać poświadczenia administratora do logowania się do usługi Dropbox dla firm. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzowania czynności 3-8.

    ![Konfiguracja instalacji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować usługi Dropbox dla firm, Otwórz nowe okno przeglądarki sieci web przejdź w usłudze Dropbox dla dzierżawy firmy i zarejestrowania się w usłudze Dropbox dla firm dzierżawy. i wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/dropboxforbusiness-tutorial/ic769509.png "Konfigurowanie logowania jednokrotnego")

4. Kliknij **ikonę użytkownika** i wybierz kartę **Settings** (Ustawienia).

    ![Konfigurowanie logowania jednokrotnego](./media/dropboxforbusiness-tutorial/configure1.png "Konfigurowanie logowania jednokrotnego")

5. W okienku nawigacji po lewej stronie kliknij pozycję **Admin console** (Konsola administracyjna).

    ![Konfigurowanie logowania jednokrotnego](./media/dropboxforbusiness-tutorial/configure2.png "Konfigurowanie logowania jednokrotnego")

6. W obszarze **Admin console** (Konsola administracyjna) kliknij pozycję **Settings** (Ustawienia) w okienku nawigacji po lewej stronie.

    ![Konfigurowanie logowania jednokrotnego](./media/dropboxforbusiness-tutorial/configure3.png "Konfigurowanie logowania jednokrotnego")

7. Wybierz opcję **Single sign-on** (Logowanie jednokrotne) w sekcji **Authentication** (Uwierzytelnianie).

    ![Konfigurowanie logowania jednokrotnego](./media/dropboxforbusiness-tutorial/configure4.png "Konfigurowanie logowania jednokrotnego")

8. W sekcji **Single sign-on** (Logowanie jednokrotne) wykonaj następujące kroki:  

    ![Konfigurowanie logowania jednokrotnego](./media/dropboxforbusiness-tutorial/configure5.png "Konfigurowanie logowania jednokrotnego")

    a. Wybierz **wymagane** jako opcję z listy rozwijanej dla **logowanie jednokrotne**.

    b. Kliknij pozycję **Add sign-in URL** (Dodaj adres URL logowania), a następnie w polu tekstowym **Identity provider sign-in URL** (Adres URL logowania dostawcy tożsamości) wklej wartość **adresu URL logowania**, która została skopiowana z witryny Azure Portal, i wybierz pozycję  **Done** (Gotowe).

    ![Konfigurowanie logowania jednokrotnego](./media/dropboxforbusiness-tutorial/configure6.png "Konfigurowanie logowania jednokrotnego")

    c. Kliknij pozycję **Upload certificate** (Przekaż certyfikat), a następnie przejdź do **pliku certyfikatu zakodowanego w formacie Base64**, który został pobrany z witryny Azure Portal.

    d. Kliknij pozycję **Copy link** (Kopiuj link) i wklej skopiowaną wartość w polu tekstowym **Adres URL logowania** w sekcji **Domena i adresy URL aplikacji Dropbox dla Firm** w witrynie Azure Portal.

    e. Kliknij pozycję **Zapisz**.

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

W tej sekcji można udostępnić Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do usługi Dropbox dla firm.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz **usługa Dropbox dla firm**.
1. Na stronie Przegląd usługi aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **użytkowników i grup**.

   ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz **Dodaj użytkownika**, a następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okna dialogowego.

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** z listy użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. Jeśli oczekujesz wszelkie wartości roli dla asercji SAML w **wybierz rolę** okno dialogowe, wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-dropbox-for-business-test-user"></a>Tworzenie użytkownika testowego aplikacji Dropbox dla Firm

W tej sekcji w aplikacji Dropbox dla Firm jest tworzony użytkownik o nazwie Britta Simon. Aplikacja Dropbox dla Firm obsługuje aprowizację użytkowników Just In Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w aplikacji Dropbox dla Firm, zostanie utworzony po uwierzytelnieniu.

>[!Note]
>Jeśli chcesz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej klienta Dropbox dla Firm](https://www.dropbox.com/business/contact)

### <a name="test-sso"></a>Test SSO

Po wybraniu Dropbox dla firm kafelka w panelu dostępu, powinny być automatycznie zarejestrowaniu w usłudze Dropbox dla firm, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)