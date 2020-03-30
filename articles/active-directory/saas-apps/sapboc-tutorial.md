---
title: 'Samouczek: Integracja usługi Azure Active Directory z usługą SAP Analytics Cloud | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a chmurą SAP Analytics Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6c5e44f0-4e52-463f-b879-834d80a55cdf
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1fd01c86086c7491d22f655fcba5da237286412f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "68347800"
---
# <a name="tutorial-integrate-sap-analytics-cloud-with-azure-active-directory"></a>Samouczek: Integracja chmury SAP Analytics cloud z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować usługę SAP Analytics Cloud z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi SAP Analytics Cloud z usługą Azure AD można:

* Kontrola w usłudze Azure AD, która ma dostęp do usługi SAP Analytics Cloud.
* Włącz użytkownikom automatyczne logowanie do usługi SAP Analytics Cloud za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z obsługą logowania jednokrotnego SAP Analytics Cloud.SAP Analytics Cloud single sign-on (SSO) enabled subscription.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* SAP Analytics Cloud obsługuje sso inicjowane przez **usługę SP**

## <a name="adding-sap-analytics-cloud-from-the-gallery"></a>Dodawanie chmury SAP Analytics Cloud z galerii

Aby skonfigurować integrację usługi SAP Analytics Cloud z usługą Azure AD, należy dodać usługę SAP Analytics Cloud z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **SAP Analytics Cloud** w polu wyszukiwania.
1. Wybierz **SAP Analytics Cloud** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą usługi SAP Analytics Cloud przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik sytuować uszjący działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w usłudze SAP Analytics Cloud.

Aby skonfigurować i przetestować usługę Azure AD SSO za pomocą usługi SAP Analytics Cloud, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie jednokrotne w chmurze SAP Analytics](#configure-sap-analytics-cloud-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego SAP Analytics Cloud](#create-sap-analytics-cloud-test-user)** — aby mieć odpowiednik B.Simon w SAP Analytics Cloud, który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)— na stronie integracji aplikacji **SAP Analytics Cloud** znajdź sekcję **Zarządzaj** i wybierz **opcję Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: 

    | |
    |-|-|
    | `https://<sub-domain>.sapanalytics.cloud/` |
    | `https://<sub-domain>.sapbusinessobjects.cloud/` |

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: 
    
    | |
    |-|-|
    | `<sub-domain>.sapbusinessobjects.cloud` |
    | `<sub-domain>.sapanalytics.cloud` |

    > [!NOTE] 
    > Wartości tych adresów URL są tylko na potrzeby demonstracyjne. Zaktualizuj wartości rzeczywistym adresem URL logowania i adresem URL identyfikatora. Aby uzyskać adres URL logowania, skontaktuj się z [zespołem pomocy technicznej klienta sap Analytics Cloud](https://help.sap.com/viewer/product/SAP_BusinessObjects_Cloud/release/). Adres URL identyfikatora można uzyskać, pobierając metadane sap analytics cloud z konsoli administracyjnej. Jest to wyjaśnione w dalszej części tego samouczka.

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** znajdź kod **XML metadanych federacji** i wybierz pozycję **Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W sekcji **Konfigurowanie chmury SAP Analytics** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-sap-analytics-cloud-sso"></a>Konfigurowanie usługi SAP Analytics Cloud SSO

1. W innym oknie przeglądarki internetowej zaloguj się do witryny firmy SAP Analytics Cloud jako administrator.

2. Wybierz **polecenie Menu** > **Administracja****systemem** > .
    
    ![Wybierz pozycję Menu (Menu), wybierz pozycję System (System), a następnie wybierz pozycję Administration (Administracja)](./media/sapboc-tutorial/config1.png)

3. Na karcie **Security** (Zabezpieczenia) wybierz ikonę **edycji** (ikona ołówka).
    
    ![Na karcie Security (Zabezpieczenia) wybierz ikonę edycji (ikona ołówka)](./media/sapboc-tutorial/config2.png)  

4. W obszarze **Authentication Method** (Metoda uwierzytelniania) wybierz pozycję **SAML Single Sign-On (SSO)** (Logowanie jednokrotne protokołu SAML).

    ![Wybierz pozycję SAML Single Sign-On (SSO) (Logowanie jednokrotne protokołu SAML) jako metodę uwierzytelniania](./media/sapboc-tutorial/config3.png)  

5. Aby pobrać metadane dostawcy usług (Krok 1.), wybierz pozycję **Download** (Pobierz). W pliku metadanych znajdź i skopiuj wartość elementu **entityID**. W witrynie Azure Portal w oknie dialogowym **Podstawowa konfiguracja protokołu SAML** wklej wartość w polu **Identyfikator**.

    ![Skopiuj i wklej wartość elementu entityID](./media/sapboc-tutorial/config4.png)  

6. Aby przekazać metadane dostawcy usług (Krok 2.) do pliku, który został pobrany z witryny Azure Portal, w obszarze **Upload Identity Provider metadata** (Przekazywanie metadanych dostawcy tożsamości) wybierz pozycję **Upload** (Przekaż).  

    ![W obszarze Upload Identity Provider metadata (Przekazywanie metadanych dostawcy tożsamości) wybierz pozycję Upload (Przekaż)](./media/sapboc-tutorial/config5.png)

7. Z listy **User Attribute** (Atrybut użytkownika) wybierz atrybut użytkownika (Krok 3.), którego chcesz użyć na potrzeby implementacji. Ten atrybut użytkownika jest mapowany na dostawcę tożsamości. Aby wprowadzić atrybut niestandardowy na stronie użytkownika, użyj opcji **Custom SAML Mapping** (Niestandardowe mapowanie protokołu SAML). Możesz również wybrać wartość **Email** (Adres e-mail) lub **USER ID** (Identyfikator użytkownika) jako atrybut użytkownika. W tym przykładzie wybraliśmy wartość **Email** (Adres e-mail), ponieważ oświadczenie identyfikatora użytkownika zamapowano z atrybutem **userprincipalname** w sekcji **Atrybuty i oświadczenia użytkownika** w witrynie Azure Portal. Zapewnia to unikatowy adres e-mail użytkownika, który jest wysyłany do aplikacji SAP Analytics Cloud w każdej pomyślnej odpowiedzi SAML.

    ![Wybierz atrybut użytkownika](./media/sapboc-tutorial/config6.png)

8. Aby zweryfikować konto za pomocą dostawcy tożsamości (Krok 4.) w polu **Login Credential (Email)** (Poświadczenia danych logowania — adres e-mail) wprowadź adres e-mail użytkownika. Następnie wybierz pozycję **Verify Account** (Sprawdź konto). System dodaje poświadczenia logowania do konta użytkownika.

    ![Wprowadź adres e-mail, a następnie wybierz pozycję Verify Account (Sprawdź konto)](./media/sapboc-tutorial/config7.png)

9. Wybierz ikonę **zapisywania**.

    ![Ikona zapisywania](./media/sapboc-tutorial/save.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w witrynie Azure portal o nazwie B.Simon.

1. Z lewego okienka w witrynie Azure portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **pozycję Nowy użytkownik** u góry ekranu.
1. We właściwościach **Użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** username@companydomain.extensionwprowadź pole . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do usługi SAP Analytics Cloud.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz sap **Analytics Cloud**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

### <a name="create-sap-analytics-cloud-test-user"></a>Utwórz użytkownika testowego SAP Analytics Cloud

Użytkownicy usługi Azure AD muszą być aprowizowani w usłudze SAP Analytics Cloud, zanim będą mogli zalogować się do usługi SAP Analytics Cloud. W sap analytics cloud inicjowania obsługi administracyjnej jest zadanie ręczne.

Aby aprowizować konto użytkownika:

1. Zaloguj się do witryny firmy SAP Analytics Cloud jako administrator.

2. Wybierz **polecenie** > **Użytkownicy zabezpieczeń** > **Users**menu .

    ![Dodawanie pracownika](./media/sapboc-tutorial/user1.png)

3. Na stronie **Users** (Użytkownicy) wybierz pozycję **+**, aby dodać szczegółowe informacje dotyczące nowego użytkownika. 

    ![Strona dodawania użytkowników](./media/sapboc-tutorial/user4.png)

    Następnie wykonaj następujące kroki:

    a. W polu **Identyfikator użytkownika** wprowadź identyfikator użytkownika, taki jak **B**.

    b. W polu **IMIĘ** wprowadź imię użytkownika, na przykład **B**.

    d. W polu **LAST NAME** (Nazwisko) wprowadź nazwisko użytkownika, np. **Simon**.

    d. W polu **NAZWA WYŚWIETLACZA** wprowadź pełną nazwę użytkownika, taką jak **B.Simon**.

    e. W polu **E-MAIL** (Adres e-mail) wprowadź adres e-mail użytkownika, np. `b.simon@contoso.com`.

    f. Na stronie **Select Roles** (Wybieranie ról) wybierz odpowiednią rolę użytkownika, a następnie wybierz pozycję **OK**.

      ![Wybór roli](./media/sapboc-tutorial/user3.png)

    g. Wybierz ikonę **zapisywania**.

### <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka SAP Analytics Cloud w Panelu dostępu należy automatycznie zalogować się do chmury SAP Analytics Cloud, dla której skonfigurowano logującą się do systemu. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

