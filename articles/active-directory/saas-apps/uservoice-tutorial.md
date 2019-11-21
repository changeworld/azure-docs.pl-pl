---
title: 'Tutorial: Azure Active Directory integration with UserVoice | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and UserVoice.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 684a405b-8932-46f6-b43a-4d97a42b6b87
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: 7a3302f1ca615fe5005be9ed1f09995ebf432eb7
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232001"
---
# <a name="tutorial-azure-active-directory-integration-with-uservoice"></a>Tutorial: Azure Active Directory integration with UserVoice

In this tutorial, you learn how to integrate UserVoice with Azure Active Directory (Azure AD).
Integrating UserVoice with Azure AD provides you with the following benefits:

* You can control in Azure AD who has access to UserVoice.
* You can enable your users to be automatically signed-in to UserVoice (Single Sign-On) with their Azure AD accounts.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

To configure Azure AD integration with UserVoice, you need the following items:

* Subskrypcja usługi Azure AD. If you don't have an Azure AD environment, you can get a [free account](https://azure.microsoft.com/free/)
* UserVoice single sign-on enabled subscription

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* UserVoice supports **SP** initiated SSO

## <a name="adding-uservoice-from-the-gallery"></a>Adding UserVoice from the gallery

To configure the integration of UserVoice into Azure AD, you need to add UserVoice from the gallery to your list of managed SaaS apps.

**To add UserVoice from the gallery, perform the following steps:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. In the search box, type **UserVoice**, select **UserVoice** from result panel then click **Add** button to add the application.

     ![UserVoice in the results list](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

In this section, you configure and test Azure AD single sign-on with UserVoice based on a test user called **Britta Simon**.
For single sign-on to work, a link relationship between an Azure AD user and the related user in UserVoice needs to be established.

To configure and test Azure AD single sign-on with UserVoice, you need to complete the following building blocks:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Configure UserVoice Single Sign-On](#configure-uservoice-single-sign-on)** - to configure the Single Sign-On settings on application side.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Create UserVoice test user](#create-uservoice-test-user)** - to have a counterpart of Britta Simon in UserVoice that is linked to the Azure AD representation of user.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

To configure Azure AD single sign-on with UserVoice, perform the following steps:

1. In the [Azure portal](https://portal.azure.com/), on the **UserVoice** application integration page, select **Single sign-on**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![UserVoice Domain and URLs single sign-on information](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<tenantname>.UserVoice.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<tenantname>.UserVoice.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Contact [UserVoice Client support team](https://www.uservoice.com/) to get these values. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. W sekcji **Certyfikat podpisywania SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Certyfikat podpisywania SAML**.

    ![Edytowanie certyfikatu podpisywania SAML](common/edit-certificate.png)

6. W sekcji **Certyfikat podpisywania SAML** skopiuj wartość **Odcisk palca** i zapisz go na komputerze.

    ![Kopiowanie wartości Odcisk palca](common/copy-thumbprint.png)

7. On the **Set up UserVoice** section, copy the appropriate URL(s) as per your requirement.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-uservoice-single-sign-on"></a>Configure UserVoice Single Sign-On

1. In a different web browser window, sign in to your UserVoice company site as an administrator.

2. In the toolbar on the top, click **Settings**, and then select **Web portal** from the menu.
   
    ![Settings Section On App Side](./media/uservoice-tutorial/ic777519.png "Ustawienia")

3. On the **Web portal** tab, in the **User authentication** section, click **Edit** to open the **Edit User Authentication** dialog page.
   
    ![Web portal Tab](./media/uservoice-tutorial/ic777520.png "Web portal")

4. On the **Edit User Authentication** dialog page, perform the following steps:
   
    ![Edit user authentication](./media/uservoice-tutorial/ic777521.png "Edit user authentication")
   
    a. Click **Single Sign-On (SSO)** .
 
    b. Paste the **Login URL** value, which you have copied from the Azure portal into the **SSO Remote Sign-In** textbox.

    d. Paste the **Logout URL** value, which you have copied from the Azure portal into the **SSO Remote Sign-Out textbox**.
 
    d. Paste the **Thumbprint** value , which you have copied from Azure portal  into the **Current certificate SHA1 fingerprint** textbox.
    
    e. Click **Save authentication settings**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. In the **User name** field type brittasimon@yourcompanydomain.extension. Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

In this section, you enable Britta Simon to use Azure single sign-on by granting access to UserVoice.

1. In the Azure portal, select **Enterprise Applications**, select **All applications**, then select **UserVoice**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. In the applications list, select **UserVoice**.

    ![The UserVoice link in the Applications list](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-uservoice-test-user"></a>Create UserVoice test user

To enable Azure AD users to sign in to UserVoice, they must be provisioned into UserVoice. In the case of UserVoice, provisioning is a manual task.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Aby aprowizować konto użytkownika, wykonaj następujące czynności:

1. Sign in to your **UserVoice** tenant.

2. Przejdź do obszaru **Settings** (Ustawienia).
   
    ![Ustawienia](./media/uservoice-tutorial/ic777811.png "Ustawienia")

3. Click **General**.

4. Click **Agents and permissions**.
   
    ![Agents and permissions](./media/uservoice-tutorial/ic777812.png "Agents and permissions")

5. Click **Add admins**.
   
    ![Add admins](./media/uservoice-tutorial/ic777813.png "Add admins")

6. On the **Invite admins** dialog, perform the following steps:
   
    ![Invite admins](./media/uservoice-tutorial/ic777814.png "Invite admins")
   
    a. In the Emails textbox, type the email address of the account you want to provision, and then click **Add**.
   
    b. Kliknij pozycję **Invite** (Zaproś).

> [!NOTE]
> You can use any other UserVoice user account creation tools or APIs provided by UserVoice to provision Azure AD user accounts.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

When you click the UserVoice tile in the Access Panel, you should be automatically signed in to the UserVoice for which you set up SSO. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [What is Conditional Access in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

