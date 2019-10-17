---
title: 'Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z pakietem servicesuite | Microsoft Docs'
description: Dowiedz się, jak można skonfigurować funkcję logowania jednokrotnego między usługą Azure Active Directory i aplikacją NetSuite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a9b834feacd6241e66e18a4f4e6aadc43e909c7
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72438497"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netsuite"></a>Samouczek: integracja logowania jednokrotnego (SSO) Azure Active Directory z pakietem

W tym samouczku dowiesz się, jak zintegrować pakiet z usługą Azure Active Directory (Azure AD). Podczas integrowania pakietu z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do pakietu.
* Zezwól użytkownikom na automatyczne logowanie do pakietu z użyciem kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja NetSuite obsługuje logowanie jednokrotne inicjowane przez **dostawcę tożsamości**

* Aplikacja NetSuite obsługuje aprowizację użytkowników typu **Just In Time**

* Aplikacja NetSuite obsługuje [zautomatyzowaną aprowizację użytkowników](NetSuite-provisioning-tutorial.md)

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="adding-netsuite-from-the-gallery"></a>Dodawanie aplikacji NetSuite z galerii

Aby skonfigurować integrację aplikacji NetSuite w usłudze Azure AD, należy dodać aplikację NetSuite z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **pakiet** w polu wyszukiwania.
1. Wybierz pozycję **pakiet** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netsuite"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla pakietu

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą pakietu, używając użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w pakiecie.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą pakietu, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-netsuite-sso)** w ramach pakietu, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego pakietu](#create-netsuite-test-user)** telefonicznego, aby dysponować odpowiednikiem B. Simon w pakiecie, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie **integracja aplikacji sieci** Web Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca:

    | |
    |--|
    | `https://<tenant-name>.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.na1.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.na2.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.sandbox.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.na1.sandbox.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.na2.sandbox.NetSuite.com/saml2/acs` |

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zaktualizuj ją, stosując rzeczywisty adres URL odpowiedzi. Skontaktuj się z [zespołem pomocy technicznej klienta aplikacji NetSuite](http://www.netsuite.com/portal/services/support-services/suitesupport.shtml), aby uzyskać tę wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja dla pakietu servicesuite oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe Atrybuty użytkownika.

    ![image](common/edit-attribute.png)

1. Oprócz powyższych, aplikacja pakietu servicesuite oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML. W sekcji oświadczenia użytkownika w oknie dialogowym atrybuty użytkownika wykonaj następujące kroki, aby dodać atrybut tokenu SAML, jak pokazano w poniższej tabeli:

    | Nazwa | Atrybut źródłowy | 
    | ---------------| --------------- |
    | account  | `account id` |

    1. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    1. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    1. Pozostaw pole **Przestrzeń nazw** puste.

    1. Dla opcji Źródło wybierz wartość **Atrybut**.

    1. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    1. Kliknij przycisk **OK**.

    1. Kliknij przycisk **Save** (Zapisz).

    >[!NOTE]
    >Wartość atrybutu konta nie jest prawdziwa. Zaktualizujesz tę wartość, co zostanie wyjaśnione w dalszej części tego samouczka.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie pakietu** , skopiuj odpowiednie adresy URL na podstawie wymagania.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź username@companydomain.extension. Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do pakietu.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Z listy Aplikacje wybierz pozycję **zestaw**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

## <a name="configure-netsuite-sso"></a>Konfigurowanie logowania jednokrotnego dla pakietu

1. Otwórz nową kartę w przeglądarce i zaloguj się do witryny firmowej w aplikacji NetSuite jako administrator.

2. Na pasku narzędzi w górnej części strony kliknij pozycję **Instalator**, a następnie przejdź do opcji **firma** , a następnie kliknij pozycję **Włącz funkcje**.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-setupsaml.png)

3. Na pasku narzędzi w środku strony kliknij pozycję **SuiteCloud**.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-suitecloud.png)

4. W obszarze **Zarządzanie uwierzytelnianiem** wybierz pozycję **SAML logowanie** jednokrotne, aby włączyć opcję logowania jednokrotnego protokołu SAML w zestawie.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-ticksaml.png)

5. Na pasku narzędzi w górnej części strony kliknij pozycję **Konfiguracja**.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-setup.png)

6. Na liście **SETUP TASKS** (ZADANIA KONFIGURACJI) kliknij opcję **Integration** (Integracja).

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-integration.png)

7. W sekcji **MANAGE AUTHENTICATION** (ZARZĄDZANIE UWIERZYTELNIANIEM) kliknij opcję **SAML Single Sign-on** (Logowanie jednokrotne SAML).

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-saml.png)

8. Na stronie **SAML Setup** (Konfigurowanie protokołu SAML) w sekcji **NetSuite Configuration** (Konfiguracja NetSuite) wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. Wybierz opcję **PRIMARY AUTHENTICATION METHOD** (PODSTAWOWA METODA UWIERZYTELNIANIA).

    b. W polu oznaczonym jako **SAMLV2 IDENTITY PROVIDER METADATA** (METADANE DOSTAWCY TOŻSAMOŚCI SAMLV2) wybierz opcję **UPLOAD IDP METADATA FILE** (PRZEKAŻ PLIK METADANYCH DOSTAWCY TOŻSAMOŚCI). Następnie kliknij przycisk **Browse** (Przeglądaj), aby przekazać plik metadanych, który został pobrany z witryny Azure Portal.

    d. Kliknij przycisk **Prześlij**.

9. W aplikacji NetSuite kliknij pozycję **Setup** (Konfiguracja), następnie przejdź do pozycji **Company** (Firma) i kliknij przycisk **Company Information** (Informacje o firmie) w górnym menu nawigacji.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-com.png)

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-account-id.png)

    b. Ze strony **Company Information** (Informacje o firmie) skopiuj wartość **ACCOUNT ID** (IDENTYFIKATOR KONTA), znajdującą się w prawej kolumnie.

    d. Wklej wartość **Identyfikator konta** skopiowaną z konta aplikacji NetSuite w polu **Wartość atrybutu** w usłudze Azure AD. 

10. Zanim użytkownicy będą mogli wykonywać logowanie jednokrotne do aplikacji NetSuite, należy im wcześniej przypisać odpowiednie uprawnienia w tej aplikacji. Postępuj zgodnie z poniższymi instrukcjami, aby przypisać te uprawnienia.

    a. W górnym menu nawigacyjnym kliknij opcję **Setup** (Konfiguracja).

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-setup.png)

    b. W menu nawigacji po lewej stronie wybierz opcję **Users/Roles** (Użytkownicy/Role), a następnie kliknij przycisk **Manage Roles** (Zarządzaj rolami).

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-manage-roles.png)

    d. Kliknij opcję **New Role** (Nowa rola).

    d. Wpisz nazwę nowej roli w polu **Name**.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-new-role.png)

    e. Kliknij przycisk **Save** (Zapisz).

    f. W menu u góry kliknij pozycję **Permissions** (Uprawnienia). Następnie kliknij pozycję **Setup** (Konfiguracja).

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-sso.png)

    g. Wybierz opcję **SAML Single Sign-on** (Logowanie jednokrotne SAML), a następnie kliknij przycisk **Add** (Dodaj).

    h. Kliknij przycisk **Save** (Zapisz).

    i. W górnym menu nawigacyjnym kliknij pozycję **Setup** (Konfiguracja), następnie kliknij pozycję **Setup Manager** (Menedżer instalacji).

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-setup.png)

    j. W menu nawigacji po lewej stronie wybierz opcję **Users/Roles** (Użytkownicy/Role), a następnie kliknij pozycję **Manage Users** (Zarządzaj użytkownikami).

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-manage-users.png)

    k. Wybierz użytkownika testowego. Następnie kliknij przycisk **Edit** (Edytuj) i przejdź do karty **Access** (Dostęp).

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-edit-user.png)

    l. W oknie dialogowym Roles (Role) przypisz odpowiednią rolę, która została przez Ciebie utworzone.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-add-role.png)

    m. Kliknij przycisk **Save** (Zapisz).

### <a name="create-netsuite-test-user"></a>Tworzenie użytkownika testowego aplikacji NetSuite

W tej sekcji w aplikacji NetSuite tworzony jest użytkownik o nazwie Britta Simon. Aplikacja NetSuite obsługuje aprowizację użytkowników just in time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w aplikacji NetSuite, zostanie utworzony po uwierzytelnieniu.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka NetSuite w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji NetSuite, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj pakiet z usługą Azure AD](https://aad.portal.azure.com/)

