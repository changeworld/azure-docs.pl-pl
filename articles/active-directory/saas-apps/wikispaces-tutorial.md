---
title: 'Tutorial: Azure Active Directory integration with Wikispaces | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and Wikispaces.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 665b95aa-f7f5-4406-9e2a-6fc299a1599c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 7a683923ba48b1f354f90ac94d746cb92f359e7a
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233321"
---
# <a name="tutorial-azure-active-directory-integration-with-wikispaces"></a>Tutorial: Azure Active Directory integration with Wikispaces

In this tutorial, you learn how to integrate Wikispaces with Azure Active Directory (Azure AD).
Integrating Wikispaces with Azure AD provides you with the following benefits:

* You can control in Azure AD who has access to Wikispaces.
* You can enable your users to be automatically signed-in to Wikispaces (Single Sign-On) with their Azure AD accounts.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

To configure Azure AD integration with Wikispaces, you need the following items:

* Subskrypcja usługi Azure AD. If you don't have an Azure AD environment, you can get a [free account](https://azure.microsoft.com/free/)
* Wikispaces single sign-on enabled subscription

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Wikispaces supports **SP** initiated SSO

## <a name="adding-wikispaces-from-the-gallery"></a>Adding Wikispaces from the gallery

To configure the integration of Wikispaces into Azure AD, you need to add Wikispaces from the gallery to your list of managed SaaS apps.

**To add Wikispaces from the gallery, perform the following steps:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. In the search box, type **Wikispaces**, select **Wikispaces** from result panel then click **Add** button to add the application.

     ![Wikispaces in the results list](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

In this section, you configure and test Azure AD single sign-on with Wikispaces based on a test user called **Britta Simon**.
For single sign-on to work, a link relationship between an Azure AD user and the related user in Wikispaces needs to be established.

To configure and test Azure AD single sign-on with Wikispaces, you need to complete the following building blocks:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Configure Wikispaces Single Sign-On](#configure-wikispaces-single-sign-on)** - to configure the Single Sign-On settings on application side.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Create Wikispaces test user](#create-wikispaces-test-user)** - to have a counterpart of Britta Simon in Wikispaces that is linked to the Azure AD representation of user.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

To configure Azure AD single sign-on with Wikispaces, perform the following steps:

1. In the [Azure portal](https://portal.azure.com/), on the **Wikispaces** application integration page, select **Single sign-on**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Wikispaces Domain and URLs single sign-on information](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<companyname>.wikispaces.net`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://session.wikispaces.net/<instancename>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Contact [Wikispaces Client support team](https://www.wikispaces.com/site/help) to get these values. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. On the **Set up Wikispaces** section, copy the appropriate URL(s) as per your requirement.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-wikispaces-single-sign-on"></a>Configure Wikispaces Single Sign-On

To configure single sign-on on **Wikispaces** side, you need to send the downloaded **Federation Metadata XML** and appropriate copied URLs from Azure portal to [Wikispaces support team](https://www.wikispaces.com/site/help). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

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

In this section, you enable Britta Simon to use Azure single sign-on by granting access to Wikispaces.

1. In the Azure portal, select **Enterprise Applications**, select **All applications**, then select **Wikispaces**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. In the applications list, select **Wikispaces**.

    ![The Wikispaces link in the Applications list](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-wikispaces-test-user"></a>Create Wikispaces test user

In order to enable Azure AD users to sign in to Wikispaces, they must be provisioned into Wikispaces. In the case of Wikispaces, provisioning is a manual task.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Aby aprowizować konta użytkowników, wykonaj następujące czynności:

1. Sign in to your **Wikispaces** company site as an administrator.

2. Go to **Members**.
   
    ![Members](./media/wikispaces-tutorial/ic787193.png "Members")

3. Click the **Invite People**.
   
    ![Invite People](./media/wikispaces-tutorial/ic787194.png "Invite People")

4. In the **Invite People** section, perform the following steps:
   
    ![Invite People](./media/wikispaces-tutorial/ic787208.png "Invite People")
   
    a. Type the **Usernames or Email Address** of a valid Azure AD account you want to provision into the related textboxes.
   
    b. Kliknij pozycję **Wyślij**.  
      
    > [!NOTE]
    > The Azure Active Directory account holder receives an email including a link to confirm the account before it becomes active.
    
> [!NOTE]
> You can use any other Wikispaces user account creation tools or APIs provided by Wikispaces to provision Azure AD user accounts.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

When you click the Wikispaces tile in the Access Panel, you should be automatically signed in to the Wikispaces for which you set up SSO. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [What is Conditional Access in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

