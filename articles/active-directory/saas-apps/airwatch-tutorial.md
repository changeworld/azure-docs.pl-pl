---
title: 'Tutorial: Azure Active Directory integration with AirWatch | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją AirWatch.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 96a3bb1c-96c6-40dc-8ea0-060b0c2a62e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 772b37816b83c275bae927d825434dc3ca76a35c
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231986"
---
# <a name="tutorial-integrate-airwatch-with-azure-active-directory"></a>Tutorial: Integrate AirWatch with Azure Active Directory

In this tutorial, you'll learn how to integrate AirWatch with Azure Active Directory (Azure AD). When you integrate AirWatch with Azure AD, you can:

* Control in Azure AD who has access to AirWatch.
* Enable your users to be automatically signed-in to AirWatch with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

To learn more about SaaS app integration with Azure AD, see [What is application access and single sign-on with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

To get started, you need the following items:

* Subskrypcja usługi Azure AD. If you don't have a subscription, you can get one-month free trial [here](https://azure.microsoft.com/pricing/free-trial/).
* AirWatch single sign-on (SSO) enabled subscription.

## <a name="scenario-description"></a>Opis scenariusza

In this tutorial, you configure and test Azure AD SSO in a test environment. AirWatch supports **SP** initiated SSO.

## <a name="adding-airwatch-from-the-gallery"></a>Dodawanie aplikacji AirWatch z galerii

Aby skonfigurować integrację aplikacji AirWatch z usługą Azure AD, musisz dodać aplikację AirWatch z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **AirWatch** in the search box.
1. Select **AirWatch** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Configure and test Azure AD SSO with AirWatch using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in AirWatch.

To configure and test Azure AD SSO with AirWatch, complete the following building blocks:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
2. **[Configure AirWatch SSO](#configure-airwatch-sso)** - to configure the Single Sign-On settings on application side.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Tworzenie użytkownika testowego aplikacji AirWatch](#create-airwatch-test-user)** — aby mieć w aplikacji AirWatch odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
5. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
6. **[Test SSO](#test-sso)** - to verify whether the configuration works.

### <a name="configure-azure-ad-sso"></a>Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the [Azure portal](https://portal.azure.com/), on the **AirWatch** application integration page, find the **Manage** section and select **Single sign-on**.
1. On the **Select a Single sign-on method** page, select **SAML**.
1. On the **Set up Single Sign-On with SAML** page, click the edit/pen icon for **Basic SAML Configuration** to edit the settings.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. On the **Basic SAML Configuration** page, enter the values for the following fields:

    1. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`

    1. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz wartość: `AirWatch`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Aby uzyskać tę wartość, skontaktuj się z [zespołem obsługi klienta firmy AirWatch](https://www.air-watch.com/company/contact-us/). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja AirWatch oczekuje asercji SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty użytkownika** na stronie integracji aplikacji. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Atrybuty użytkownika**.

    ![image](common/edit-attribute.png)

1. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** edytuj oświadczenia, korzystając z **ikony edycji**, lub dodaj je za pomocą opcji **Dodaj nowe oświadczenie**, aby skonfigurować atrybut tokenu języka SAML, jak pokazano na ilustracji powyżej, a następnie wykonaj następujące czynności:

    | Nazwa |  Atrybut źródłowy|
    |---------------|----------------|
    | UID | user.userprincipalname |
    | | |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    d. Pozostaw pole **Przestrzeń nazw** puste.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij przycisk **OK**.

    g. Kliknij przycisk **Save** (Zapisz).

1. On the **Set up Single Sign-On with SAML** page, in the **SAML Signing Certificate** section, find **Federation Metadata XML** and select **Download** to download the Metadata XML and save it on your computer.

   ![Link do pobierania certyfikatu](common/metadataxml.png)

1. On the **Set up AirWatch** section, copy the appropriate URL(s) based on your requirement.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-airwatch-sso"></a>Configure AirWatch SSO

1. In a different web browser window, sign in to your AirWatch company site as an administrator.

1. On the settings page. Select **Settings > Enterprise Integration > Directory Services**.

   ![Ustawienia](./media/airwatch-tutorial/ic791921.png "Ustawienia")

1. Kliknij kartę **User** (Użytkownik), w polu tekstowym **Base DN** (Bazowa nazwa domeny) wpisz nazwę domeny, a następnie kliknij pozycję **Save** (Zapisz).

   ![User](./media/airwatch-tutorial/ic791922.png "Użytkownik")

1. Kliknij kartę **Server** (Serwer).

   ![Serwer](./media/airwatch-tutorial/ic791923.png "Serwer")

1. Perform the following steps on the **LDAP** section:

    ![Upload](./media/airwatch-tutorial/ic791924.png "LDAP")   

    a. W polu **Directory Type** (Typ katalogu) wybierz pozycję **None** (Brak).

    b. Wybierz pozycję **Use SAML For Authentication** (Użyj protokołu SAML na potrzeby uwierzytelniania).

1. On the **SAML 2.0** section, to upload the downloaded certificate, click **Upload**.

    ![Upload](./media/airwatch-tutorial/ic791932.png "Przekazywanie")

1. W sekcji **Request** (Żądanie) wykonaj następujące czynności:

    ![Żądanie](./media/airwatch-tutorial/ic791925.png "Prośba")  

    a. W polu **Request Binding Type** (Typ powiązania żądania) wybierz opcję **POST**.

    b. In the Azure portal, on the **Configure single sign-on at AirWatch** dialog page, copy the **Login URL** value, and then paste it into the **Identity Provider Single Sign On URL** textbox.

    d. W polu **NameID Format** (Format identyfikatora nazwy) wybierz opcję **Email Address** (Adres e-mail).

    d. As **Authentication Request Security**, select **None**.

    e. Kliknij przycisk **Save** (Zapisz).

1. Ponownie kliknij kartę **User** (Użytkownik).

    ![User](./media/airwatch-tutorial/ic791926.png "Użytkownik")

1. W sekcji **Attribute** (Atrybut) wykonaj następujące czynności:

    ![Attribute](./media/airwatch-tutorial/ic791927.png "Atrybut")

    a. W polu tekstowym **Object Identifier** (Identyfikator obiektu) wpisz `http://schemas.microsoft.com/identity/claims/objectidentifier`.

    b. W polu tekstowym **Username** (Nazwa użytkownika) wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    d. W polu tekstowym **Display Name** (Nazwa wyświetlana) wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    d. W polu tekstowym **First Name** (Imię) wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    e. W polu tekstowym **Last Name** (Nazwisko) wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.

    f. W polu tekstowym **Email** (Adres e-mail) wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    g. Kliknij przycisk **Save** (Zapisz).

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

In this section, you'll create a test user in the Azure portal called B.Simon.

1. From the left pane in the Azure portal, select **Azure Active Directory**, select **Users**, and then select **All users**.
1. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.
1. In the **User** properties, follow these steps:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. In the **User name** field, enter the username@companydomain.extension. Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to AirWatch.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. Na liście aplikacji wybierz pozycję **AirWatch**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.

    ![The Add User link](common/add-assign-user.png)

1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you're expecting any role value in the SAML assertion, in the **Select Role** dialog, select the appropriate role for the user from the list and then click the **Select** button at the bottom of the screen.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-airwatch-test-user"></a>Tworzenie użytkownika testowego aplikacji AirWatch

To enable Azure AD users to sign in to AirWatch, they must be provisioned in to AirWatch. W przypadku aplikacji AirWatch aprowizowanie jest zadaniem ręcznym.

**Aby skonfigurować aprowizację użytkowników, wykonaj następujące czynności:**

1. Sign in to your **AirWatch** company site as administrator.

2. W okienku nawigacji po lewej stronie kliknij pozycję **Accounts** (Konta), a następnie kliknij pozycję **Users** (Użytkownicy).
  
   ![Użytkownicy](./media/airwatch-tutorial/ic791929.png "Użytkownicy")

3. In the **Users** menu, click **List View**, and then click **Add > Add User**.
  
   ![Add User](./media/airwatch-tutorial/ic791930.png "Dodaj użytkownika")

4. W oknie dialogowym **Add / Edit User** (Dodawanie/edytowanie użytkownika) wykonaj następujące czynności:

   ![Add User](./media/airwatch-tutorial/ic791931.png "Dodaj użytkownika")

   a. W polach tekstowych **Username** (Nazwa użytkownika), **Password** (Hasło), **Confirm Password** (Potwierdź hasło), **First Name** (Imię), **Last Name** (Nazwisko), **Email Address** (Adres e-mail) wpisz wartości prawidłowego konta usługi Azure Active Directory, które chcesz aprowizować.

   b. Kliknij przycisk **Save** (Zapisz).

> [!NOTE]
> You can use any other AirWatch user account creation tools or APIs provided by AirWatch to provision Azure AD user accounts.

### <a name="test-sso"></a>Test SSO

When you select the AirWatch tile in the Access Panel, you should be automatically signed in to the AirWatch for which you set up SSO. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
