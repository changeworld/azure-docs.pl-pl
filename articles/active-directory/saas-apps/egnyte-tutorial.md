---
title: 'Samouczek: Integracja usługi Azure Active Directory z egnyte | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a aplikacją Egnyte.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8c2101d4-1779-4b36-8464-5c1ff780da18
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f8eb0f0d566d656436da11141ea7f6c521b7b82
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76983728"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-egnyte"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (Logowanie jednokrotne) z egnyte

W tym samouczku dowiesz się, jak zintegrować egnyte z usługą Azure Active Directory (Azure AD). Po zintegrowaniu egnyte z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do Egnyte.
* Włącz użytkownikom automatyczne logowanie do egnyte za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją logowania jednokrotnego (SSO) firmy Egnyte.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Egnyte obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług**
* Po skonfigurowaniu egnyte można wymusić kontrolę sesji, które chronią eksfiltracji i infiltracji poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymusić kontrolę nad sesją za pomocą programu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-egnyte-from-the-gallery"></a>Dodawanie aplikacji Egnyte z galerii

Aby skonfigurować integrację aplikacji Egnyte z usługą Azure AD, musisz dodać aplikację Egnyte z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **egnyte** w polu wyszukiwania.
1. Wybierz **egnyte** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z aplikacją Egnyte, korzystając z danych użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD a powiązanym użytkownikiem aplikacji Egnyte.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z aplikacją Egnyte, należy wykonać czynności opisane w poniższych blokach konstrukcyjnych:

Aby skonfigurować i przetestować usługę Azure AD SSO za pomocą programu Egnyte, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj logowanie jednokrotne Egnyte](#configure-egnyte-sso)** - aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz użytkownika testowego Egnyte](#create-egnyte-test-user)** — aby mieć odpowiednik B.Simon w Egnyte, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD z aplikacją Egnyte, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **Egnyte** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje o domenie i adresach URL logowania jednokrotnego aplikacji Egnyte](common/sp-signonurl.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<companyname>.egnyte.com`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta aplikacji Egnyte](https://www.egnyte.com/corp/contact_egnyte.html), aby uzyskać tę wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

5. W sekcji **Konfigurowanie aplikacji Egnyte** skopiuj odpowiednie adresy URL zgodnie ze swoimi wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz **pozycję Nowy użytkownik** u góry ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **\@brittasimon yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz dla użytkownika Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji Egnyte.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, pozycję **Wszystkie aplikacje**, a następnie pozycję **Egnyte**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Egnyte**.

    ![Link aplikacji Egnyte na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

## <a name="configure-egnyte-sso"></a>Konfigurowanie sytuacji sytuacyjnej egnyte

1. W innym oknie przeglądarki internetowej zaloguj się do witryny firmy Egnyte jako administrator.

2. Kliknij przycisk **Ustawienia**.
   
    ![Ustawienia](./media/egnyte-tutorial/ic787819.png "Ustawienia")

3. W menu kliknij pozycję **Settings (Ustawienia)**.

    ![Ustawienia](./media/egnyte-tutorial/ic787820.png "Ustawienia")

4. Kliknij kartę **Configuration (Konfiguracja)**, a następnie kliknij pozycję **Security (Zabezpieczenia)**.

    ![Zabezpieczenia](./media/egnyte-tutorial/ic787821.png "Zabezpieczenia")

5. W sekcji **Single Sign-On Authentication (Uwierzytelnianie logowania jednokrotnego)** wykonaj następujące kroki:

    ![Uwierzytelnianie logowania jednokrotnego](./media/egnyte-tutorial/ic787822.png "Uwierzytelnianie logowania jednokrotnego")   
    
    a. Dla pozycji **Single sign-on authentication (Uwierzytelnianie logowania jednokrotnego)** wybierz pozycję **SAML 2.0**.
   
    b. Dla pozycji **Identity provider (Dostawca tożsamości)** wybierz pozycję **AzureAD**.
   
    d. Wklej **adres URL logowania** skopiowany z witryny Azure Portal do pola tekstowego **Identity provider login URL (Adres URL logowania dostawcy tożsamości)**.
   
    d. Wklej **identyfikator usługi Azure AD** skopiowany z witryny Azure Portal do pola tekstowego **Identity provider entity ID (Identyfikator jednostki dostawcy tożsamości)**.
      
    e. Otwórz w Notatniku swój certyfikat zakodowany w formacie base-64 pobrany z witryny Azure Portal, skopiuj jego zawartość do schowka, a następnie wklej ją w polu tekstowym **Identity provider certificate (Certyfikat dostawcy tożsamości)**.
   
    f. Dla pozycji **Default user mapping (Domyślne mapowanie użytkownika)** wybierz pozycję **Email address (Adres e-mail)**.
   
    g. Dla pozycji **Use domain-specific issuer value (Użyj wartości wystawcy specyficznej dla domeny)** wybierz pozycję **disabled (wyłączone)**.
   
    h. Kliknij przycisk **Zapisz**.

### <a name="create-egnyte-test-user"></a>Tworzenie użytkownika testowego aplikacji Egnyte

Aby umożliwić użytkownikom usługi Azure AD zalogowanie się do egnyte, muszą one być aprowizowana w Egnyte. W przypadku aplikacji Egnyte aprowizowanie jest zadaniem ręcznym.

**Aby aprowizować konta użytkowników, wykonaj następujące kroki:**

1. Zaloguj się do witryny firmy **Egnyte** jako administrator.

2. Przejdź do pozycji **Settings (Ustawienia) \> Users & Groups (Użytkownicy i grupy)**.

3. Kliknij pozycję **Add New User (Dodaj nowego użytkownika)**, a następnie wybierz typ użytkownika, którego chcesz dodać.
   
    ![Użytkownicy](./media/egnyte-tutorial/ic787824.png "Użytkownicy")

4. W sekcji **New Power User (Nowy użytkownik zaawansowany)** wykonaj następujące kroki:
    
    ![Nowy użytkownik standardowy](./media/egnyte-tutorial/ic787825.png "Nowy użytkownik standardowy")   

    a. W polu tekstowym **e-mail** wpisz wiadomość e-mail użytkownika, takiego jak **\@Brittasimon contoso.com**.

    b. W polu tekstowym **Username (Nazwa użytkownika)** wprowadź nazwę użytkownika, na przykład **Brittasimon**.

    d. Wybierz pozycję **Single Sign-On (Logowanie jednokrotne)** dla pozycji **Authentication Type (Typ uwierzytelniania)**.
   
    d. Kliknij przycisk **Zapisz**.
    
    >[!NOTE]
    >Właściciel konta usługi Azure Active Directory otrzyma wiadomość e-mail z powiadomieniem.
    >

>[!NOTE]
>Do aprowizowania kont użytkowników usługi Azure AD można użyć innych narzędzi do tworzenia kont użytkowników egnyte lub interfejsów API dostarczonych przez firmę Egnyte.
>

### <a name="test-sso"></a>Test SSO

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Egnyte na panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Egnyte, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Co to jest kontrola sesji w usłudze Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
