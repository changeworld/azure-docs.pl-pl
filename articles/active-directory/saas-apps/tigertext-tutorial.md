---
title: 'Samouczek: Integracja usługi Azure Active Directory programu TigerText Secure Messenger | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i TigerText Secure Messenger.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 03f1e128-5bcb-4e49-b6a3-fe22eedc6d5e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: 840b1fe55556cfd853e0928164891d6b21b17cc2
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2019
ms.locfileid: "65956869"
---
# <a name="tutorial-azure-active-directory-integration-with-tigertext-secure-messenger"></a>Samouczek: Integracja usługi Azure Active Directory programu TigerText Secure Messenger

W tym samouczku dowiesz się, jak zintegrować TigerText Messenger zabezpieczyć za pomocą usługi Azure Active Directory (Azure AD).

Integrowanie TigerText Secure Messenger z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do TigerText Secure Messenger.
* Aby umożliwić użytkownikom automatyczne logowanie do programu Messenger Secure TigerText (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji: witryna Azure portal.

Aby uzyskać szczegółowe informacje na temat oprogramowania jako usługi (SaaS) integracji aplikacji z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Do konfigurowania integracji z usługą Azure AD z TigerText Secure Messenger, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja TigerText Messenger zabezpieczyć za pomocą logowania jednokrotnego włączone.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku konfigurowania i testowania usługi Azure AD logowanie jednokrotne w środowisku testowym, a integracja TigerText Secure Messenger z usługą Azure AD.

Obsługuje TigerText Secure Messenger zainicjowanego przez dostawcę usług logowania jednokrotnego (SSO).

## <a name="add-tigertext-secure-messenger-from-the-azure-marketplace"></a>Dodaj TigerText Messenger bezpiecznego portalu Azure Marketplace

Aby skonfigurować integrację programu TigerText Secure Messenger w usłudze Azure AD, należy dodać TigerText Secure Messenger w witrynie Azure Marketplace z listą zarządzanych aplikacji SaaS:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com?azure-portal=true).
1. W lewym okienku wybierz pozycję **Azure Active Directory**.

    ![Opcja usługi Azure Active Directory](common/select-azuread.png)

1. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.

    ![Okienko Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

1. Aby dodać nową aplikację, wybierz **+ Nowa aplikacja** u góry okienka.

    ![Nowa opcja aplikacji](common/add-new-app.png)

1. W polu wyszukiwania wprowadź **TigerText Secure Messenger**. W wynikach wyszukiwania wybierz **TigerText Secure Messenger**, a następnie wybierz pozycję **Dodaj** umożliwiające dodanie aplikacji.

    ![Zabezpieczanie Messenger TigerText na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji, konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą TigerText Messenger zabezpieczenia oparte na nazwie użytkownika testowego **Britta Simon**. Dla logowania jednokrotnego do pracy należy nawiązania połączenia między użytkownika usługi Azure AD i powiązanych użytkowników TigerText Secure Messenger.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą TigerText Secure Messenger, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Konfigurowanie TigerText Messenger bezpiecznego logowania jednokrotnego](#configure-tigertext-secure-messenger-single-sign-on)**  do konfigurowania pojedynczego ustawień logowania jednokrotnego na stronie aplikacji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  do testowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  umożliwiające Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Tworzenie użytkownika testowego TigerText Secure Messenger](#create-a-tigertext-secure-messenger-test-user)**  tak, że istnieje użytkownik o nazwie Britta Simon TigerText Secure Messenger, który jest połączony z użytkownikiem usługi Azure AD o nazwie Britta Simon.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z TigerText Secure Messenger, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **TigerText Secure Messenger** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Konfigurowanie opcji rejestracji jednokrotnej](common/select-sso.png)

1. Na **wybierz jedną metodę logowania jednokrotnego** okienku wybierz **SAML/WS-Fed** trybu, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

1. Na **Ustaw się logowania jednokrotnego przy użyciu protokołu SAML** okienku wybierz **Edytuj** (ikonę ołówka) aby otworzyć **podstawową konfigurację protokołu SAML** okienka.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W **podstawową konfigurację protokołu SAML** okienko, wykonaj następujące czynności:

    ![TigerText Secure Messenger domena i adresy URL pojedynczy informacje logowania jednokrotnego](common/sp-identifier.png)

    1. W **adres URL logowania** wprowadź adres URL:

       `https://home.tigertext.com`

    1. W **identyfikator jednostki** wpisz adres URL przy użyciu następującego wzorca:

       `https://saml-lb.tigertext.me/v1/organization/<instance ID>`

    > [!NOTE]
    > **Identyfikator jednostki** wartość nie jest prawdziwe. Zaktualizuj tę wartość przy użyciu rzeczywistego identyfikatora. Aby uzyskać wartość, skontaktuj się z [zespołem pomocy technicznej TigerText Secure Messenger](mailto:prosupport@tigertext.com). Może również odnosić się do wzorców objętego **podstawową konfigurację protokołu SAML** okienko w witrynie Azure portal.

1. Na **Ustaw się logowanie jednokrotne z SAML** okienko w **certyfikat podpisywania SAML** zaznacz **Pobierz** można pobrać **XML metadanych Federacji**  z podanych opcji, a następnie zapisz go na komputerze.

    ![Opcja pobierania XML metadanych Federacji](common/metadataxml.png)

1. W **Konfigurowanie TigerText Secure Messenger** sekcji, skopiuj adres URL lub adresy URL, które są potrzebne:

   * **Adres URL logowania**
   * **Identyfikator usługi Azure AD**
   * **Adres URL wylogowania**

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-tigertext-secure-messenger-single-sign-on"></a>Konfigurowanie TigerText Messenger bezpiecznego logowania jednokrotnego

Aby skonfigurować logowanie jednokrotne stronie TigerText Secure Messenger, musisz wysłać pobrany XML metadanych Federacji i odpowiednie adresy URL skopiowany z witryny Azure portal, aby [zespołem pomocy technicznej TigerText Secure Messenger](mailto:prosupport@tigertext.com). Zespół TigerText Secure Messenger będzie upewnij się, że połączenia logowania jednokrotnego SAML jest prawidłowo po obu stronach.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego o nazwie Britta Simon w witrynie Azure Portal.

1. W witrynie Azure Portal w okienku po lewej wybierz kolejno pozycje **Azure Active Directory**   > **Użytkownicy** > **Wszyscy użytkownicy**.

    ![Użytkownicy i "All users" Opcje](common/users.png)

1. W górnej części ekranu wybierz **+ nowy użytkownik**.

    ![Nowa opcja użytkownika](common/new-user.png)

1. W **użytkownika** okienko, wykonaj następujące czynności:

    ![W okienku użytkownika](common/user-properties.png)

    1. W polu **Nazwa** wpisz **BrittaSimon**.
  
    1. W **nazwa_użytkownika** wprowadź **BrittaSimon\@\<domenatwojejfirmy >.\< Rozszerzenia >**. Na przykład **BrittaSimon\@contoso.com**.

    1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.

    1. Wybierz pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do nadawania im praw do TigerText Messenger zabezpieczyć za pomocą platformy Azure logowania jednokrotnego.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje** > **TigerText Secure Messenger**.

    ![Okienko aplikacje przedsiębiorstwa](common/enterprise-applications.png)

1. Na liście aplikacji wybierz **TigerText Secure Messenger**.

    ![Zabezpieczanie Messenger TigerText na liście aplikacji](common/all-applications.png)

1. W okienku po lewej stronie w obszarze **ZARZĄDZAJ**, wybierz opcję **użytkowników i grup**.

    ![Opcji "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz **+ Dodaj użytkownika**, a następnie wybierz pozycję **użytkowników i grup** w **Dodaj przydziału** okienka.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

1. W **użytkowników i grup** okienku wybierz **Britta Simon** w **użytkowników** , a następnie wybierz **wybierz** w dolnej części okienka.

1. Jeśli oczekujesz wartość roli dla asercji SAML, następnie w **wybierz rolę** okienku, wybierz odpowiednią rolę dla użytkownika z listy. W dolnej części okienka wybierz **wybierz**.

1. W **Dodaj przydziału** okienku wybierz **przypisać**.

### <a name="create-a-tigertext-secure-messenger-test-user"></a>Tworzenie użytkownika testowego TigerText Secure Messenger

W tej sekcji utworzysz użytkownika o nazwie Britta Simon TigerText Secure Messenger. Praca z [zespołem pomocy technicznej TigerText Secure Messenger](mailto:prosupport@tigertext.com) dodać Britta Simon jako użytkownika w programie TigerText Secure Messenger. Użytkownicy muszą być tworzone i aktywowana, aby używać logowania jednokrotnego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji możesz przetestować konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu portalu Moje aplikacje.

Po wybraniu **TigerText Secure Messenger** w portalu Moje aplikacje możesz powinny być automatycznie zalogowany do subskrypcji TigerText Secure Messenger, dla którego możesz skonfigurować logowanie jednokrotne. Aby uzyskać więcej informacji na temat portalu Moje aplikacje, zobacz [dostępu i użycia aplikacji w portalu Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
