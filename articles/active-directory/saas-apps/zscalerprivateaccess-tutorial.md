---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą rozwiązania Zscaler prywatny dostęp (ZPA) | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i rozwiązania Zscaler prywatny dostęp (ZPA).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 83711115-1c4f-4dd7-907b-3da24b37c89e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0a1538f640bb4722eca1d4f3a80125837593bab
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67085714"
---
# <a name="tutorial-integrate-zscaler-private-access-zpa-with-azure-active-directory"></a>Samouczek: Integracja rozwiązania Zscaler dostęp prywatny (ZPA) z usługi Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować rozwiązania Zscaler prywatny dostęp (ZPA) z usługą Azure Active Directory (Azure AD). Po zintegrowaniu rozwiązania Zscaler prywatny dostęp (ZPA) z usługą Azure AD, możesz wykonywać następujące czynności:

* Kontrolowanie w usłudze Azure AD, kto ma dostęp do rozwiązania Zscaler prywatny dostęp (ZPA).
* Umożliwianie użytkownikom można automatycznie zalogowany do rozwiązania Zscaler prywatny dostęp (ZPA) przy użyciu konta usługi Azure AD.
* Zarządzanie Twoimi kontami w jednej centralnej lokalizacji — witryny Azure portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/).
* Rozwiązania Zscaler prywatny dostęp (ZPA) logowanie jednokrotne (SSO) włączone subskrypcji.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku, skonfiguruj i przetestuj logowania jednokrotnego usługi Azure AD w środowisku testowym. Obsługuje rozwiązania Zscaler prywatny dostęp (ZPA) **SP** jednokrotne logowanie inicjowane przez.

## <a name="adding-zscaler-private-access-zpa-from-the-gallery"></a>Dodawanie rozwiązania Zscaler prywatny dostęp (ZPA) z galerii

Aby skonfigurować integrację z rozwiązania Zscaler prywatny dostęp (ZPA) w usłudze Azure AD, należy dodać rozwiązania Zscaler prywatny dostęp (ZPA) z galerii z listą zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **usługi Azure Active Directory** usługi.
1. Przejdź do **aplikacje dla przedsiębiorstw** , a następnie wybierz **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz **nową aplikację**.
1. W **Dodaj z galerii** sekcji, wpisz **rozwiązania Zscaler prywatny dostęp (ZPA)** w polu wyszukiwania.
1. Wybierz **rozwiązania Zscaler prywatny dostęp (ZPA)** z wyników panelu, a następnie dodać aplikację. Odczekaj kilka sekund, podczas gdy aplikacja zostanie dodany do Twojej dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

Konfiguracja i testowanie logowania jednokrotnego usługi Azure AD za pomocą rozwiązania Zscaler prywatny dostęp (ZPA) przy użyciu użytkownika testu o nazwie **Britta Simon**. Logowanie Jednokrotne do pracy musisz ustanowić relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w rozwiązania Zscaler prywatny dostęp (ZPA).

Aby skonfigurować i przetestować logowania jednokrotnego usługi Azure AD za pomocą rozwiązania Zscaler prywatny dostęp (ZPA), wykonaj poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-sso)**  aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie rozwiązania Zscaler prywatny dostęp (ZPA)](#configure-zscaler-private-access-zpa)**  do konfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  do testowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  umożliwiające Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego rozwiązania Zscaler prywatny dostęp (ZPA)](#create-zscaler-private-access-zpa-test-user)**  mieć odpowiednikiem Britta Simon w rozwiązania Zscaler prywatny dostęp (ZPA) połączoną usługę Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-sso)**  Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

Wykonaj następujące kroki, aby włączyć logowania jednokrotnego usługi Azure AD w witrynie Azure portal.

1. W [witryny Azure portal](https://portal.azure.com/)na **rozwiązania Zscaler prywatny dostęp (ZPA)** strona integracji aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **logowanie jednokrotne**.
1. Na **wybierz jedną metodę logowania jednokrotnego** wybierz **SAML**.
1. Na **Ustaw się logowanie jednokrotne z SAML** kliknij ikonę edycji/pióra **podstawową konfigurację protokołu SAML** edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na **podstawową konfigurację protokołu SAML** strony, wprowadź wartości dla następujących pól:

    1. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://samlsp.private.zscaler.com/auth/login?domain=<your-domain-name>`

    1. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL: `https://samlsp.private.zscaler.com/auth/metadata`

    > [!NOTE]
    > Wartość **adresu URL logowania** nie jest rzeczywista. Zaktualizuj wartość za pomocą rzeczywistego adresu URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta rozwiązania Zscaler prywatny dostęp (ZPA)](https://help.zscaler.com/zpa-submit-ticket) można uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na **Ustaw się logowanie jednokrotne z SAML** strony w **certyfikat podpisywania SAML** sekcji, Znajdź **XML metadanych Federacji** i wybierz **Pobierz** do pobrania certyfikatu i zapisz go na komputerze.

   ![Link pobierania certyfikatu](common/metadataxml.png)

1. Na **Ustaw się rozwiązania Zscaler prywatny dostęp (ZPA)** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-zscaler-private-access-zpa"></a>Konfigurowanie rozwiązania Zscaler dostęp prywatny (ZPA)

1. Aby zautomatyzować konfigurację w ramach rozwiązania Zscaler prywatny dostęp (ZPA), musisz zainstalować **rozszerzenia przeglądarki do bezpiecznego Moje aplikacje logowania** , klikając **zainstalować rozszerzenie**.

    ![Moje rozszerzenie aplikacji](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki, kliknij pozycję **dostępu prywatnego rozwiązania Zscaler Instalatora (ZPA)** nastąpi bezpośrednie przekierowanie do aplikacji rozwiązania Zscaler prywatny dostęp (ZPA). W tym miejscu należy podać poświadczenia administratora do logowania do rozwiązania Zscaler prywatny dostęp (ZPA). Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i zautomatyzować kroki od 3 do 6.

    ![Konfiguracja instalacji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować rozwiązania Zscaler prywatny dostęp (ZPA), Otwórz nowe okno przeglądarki sieci web i logowania do witryny firmy rozwiązania Zscaler prywatny dostęp (ZPA) z uprawnieniami administratora i wykonaj następujące czynności:

4. W lewej części menu, kliknij **administracji** i przejdź do **uwierzytelniania** kliknij sekcję **konfigurację dostawcy tożsamości**.

    ![Administrator dostępu prywatnego rozwiązania Zscaler administracji](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-administration.png)

5. W prawym górnym rogu, kliknij przycisk **Dodawanie konfiguracji dostawcy tożsamości**. 

    ![Dostawca tożsamości Administrator dostępu prywatnego rozwiązania Zscaler](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-idp.png)

6. Na **Dodawanie konfiguracji dostawcy tożsamości** strony należy wykonać następujące czynności:
 
    ![Wybierz administratora dostępu prywatnego rozwiązania Zscaler](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-select.png)

    a. Kliknij przycisk **wybierz plik** do przekazania pobrany plik metadanych z usługi Azure AD w **przekazywania plików metadanych dostawcy tożsamości** pola.

    b. Odczytuje **metadanych tożsamości** z usługi Azure AD i wypełni wszystkie informacje dotyczące pól, jak pokazano poniżej.

    ![Konfiguracja rozwiązania Zscaler prywatny dostęp administratora](./media/zscalerprivateaccess-tutorial/config.png)

    c. Wybierz domenę z **domen** pola.
    
    d. Kliknij pozycję **Zapisz**.

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

W tej sekcji można udostępnić Britta Simon korzystać z platformy Azure logowania jednokrotnego przez udzielenie dostępu do rozwiązania Zscaler prywatny dostęp (ZPA).

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz **rozwiązania Zscaler prywatny dostęp (ZPA)** .
1. Na stronie Przegląd usługi aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **użytkowników i grup**.

   ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz **Dodaj użytkownika**, a następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okna dialogowego.

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** z listy użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. Jeśli oczekujesz wszelkie wartości roli dla asercji SAML w **wybierz rolę** okno dialogowe, wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-zscaler-private-access-zpa-test-user"></a>Tworzenie użytkownika testowego rozwiązania Zscaler prywatny dostęp (ZPA)

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w rozwiązania Zscaler prywatny dostęp (ZPA). Skontaktuj się z [zespołem pomocy technicznej rozwiązania Zscaler prywatny dostęp (ZPA)](https://help.zscaler.com/zpa-submit-ticket) Aby dodać użytkowników do platformy rozwiązania Zscaler prywatny dostęp (ZPA).

### <a name="test-sso"></a>Test SSO

Po wybraniu kafelka rozwiązania Zscaler prywatny dostęp (ZPA) w panelu dostępu, możesz powinna istnieć możliwość automatycznej rejestracji do rozwiązania Zscaler prywatny dostęp (ZPA) dla której skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)