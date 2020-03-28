---
title: 'Samouczek: Integracja usługi Azure Active Directory z usługą Adaptive Insights | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a usługą Adaptive Insights.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/19/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf05fc30a2b111a12c0b8e131db5117ec784075b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74232124"
---
# <a name="tutorial-integrate-adaptive-insights-with-azure-active-directory"></a>Samouczek: Integrowanie adaptacyjnej analizy za pomocą usługi Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować usługę Adaptive Insights z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Adaptive Insights z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do usługi Adaptive Insights.
* Włącz użytkownikom automatyczne logowanie do usługi Adaptive Insights za pomocą kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z obsługą logowania jednokrotnego adaptive Insights(Logowanie jednokrotne).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Adaptive Insights obsługuje jednostkę SSO inicjowane przez **protokół IDP**

## <a name="adding-adaptive-insights-from-the-gallery"></a>Dodawanie adaptacyjnych statystyk z galerii

Aby skonfigurować integrację usługi Adaptive Insights z usługą Azure AD, należy dodać adaptacyjne statystyki z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **adaptive insights** w polu wyszukiwania.
1. Wybierz **pozycję Adaptive Insights** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą usługi Adaptive Insights przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuował działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w usłudze Adaptive Insights.

Aby skonfigurować i przetestować usługę Azure AD SSO za pomocą usługi Adaptive Insights, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie jednokrotne usługi Adaptive Insights](#configure-adaptive-insights-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego usługi Adaptive Insights](#create-adaptive-insights-test-user)** — aby mieć odpowiednik B.Simon w adaptive insights, który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Adaptive Insights** znajdź sekcję **Zarządzaj** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. W polu tekstowym **Identyfikator** wpisz adres URL przy użyciu następującego wzorca:`https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    > [!NOTE]
    > Możesz uzyskać wartości identyfikatora (identyfikatora jednostki) i odpowiedzi na adresy URL z sekcji **Ustawienia logowania SSO SAML** w adaptive insights.

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** znajdź **certyfikat (Base64)** i wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie statystyk adaptacyjnych** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-adaptive-insights-sso"></a>Konfigurowanie usługi doikałowej usługi Adaptive Insights

1. W innym oknie przeglądarki internetowej zaloguj się do witryny firmy Adaptive Insights jako administrator.

2. Przejdź do sekcji **Administration** (Administracja).

    ![Administracja](./media/adaptivesuite-tutorial/ic805644.png "Administrator")

3. W sekcji **Użytkownicy i role** kliknij pozycję **Ustawienia logowania jednokładowego SAML**.

    ![Zarządzanie ustawieniami logowania sytego SAML](./media/adaptivesuite-tutorial/ic805645.png "Zarządzanie ustawieniami logowania sytego SAML")

4. Na stronie **Ustawienia logowania sytograficznego SAML** wykonaj następujące czynności:

    ![Ustawienia logowania sytego SAML](./media/adaptivesuite-tutorial/ic805646.png "Ustawienia logowania sytego SAML")

    a. W polach tekstowych **nazwa dostawcy tożsamości** wpisz nazwę konfiguracji.

    b. Wklej wartość **identyfikatora usługi Azure AD** skopiowaną z witryny Azure Portal do pola tekstowego **identyfikatora jednostki dostawcy tożsamości.**

    d. Wklej wartość **adresu URL logowania** skopiowaną z witryny Azure portal do pola tekstowego adresu URL logowania dostawcy **tożsamości.**

    d. Wklej wartość **adresu URL wylogowania** skopiowaną z witryny Azure Portal do pola tekstowego **niestandardowego adresu URL wylogowania.**

    e. Aby przesłać pobrany certyfikat, kliknij pozycję **Wybierz plik**.

    f. Wybierz następujące elementy, na przykład:

     * **SAML user id**, wybierz **nazwę użytkownika Adaptive Insights .**

     * **Lokalizacja identyfikatora użytkownika SAML**, wybierz **identyfikator użytkownika w obszarze Nazwa .**

     * **Saml NameID ,** wybierz **adres e-mail**.

     * **Włącz SAML**, wybierz **opcję Zezwalaj na logowanie SSO SAML i bezpośrednie logowanie adaptacyjnej analizy.**

    g. **Skopiuj adres URL aplikacji SSO usługi Adaptive Insights** i wklej je do pól tekstowych identyfikator **(identyfikator jednostki)** i adres url **odpowiedzi** w sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal.

    h. Kliknij przycisk **Zapisz**.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do adaptive insights.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Adaptive Insights**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

### <a name="create-adaptive-insights-test-user"></a>Utwórz użytkownika testowego adaptive insights

Aby umożliwić użytkownikom usługi Azure AD zalogowanie się do usługi Adaptive Insights, muszą one zostać zainicjowane w usłudze Adaptive Insights. W przypadku adaptive insights inicjowania obsługi administracyjnej jest zadanie ręczne.

**Aby skonfigurować aprowizację użytkowników, wykonaj następujące kroki:**

1. Zaloguj się do witryny firmy **Adaptive Insights** jako administrator.

2. Przejdź do sekcji **Administration** (Administracja).

   ![Administracja](./media/adaptivesuite-tutorial/IC805644.png "Administrator")

3. W sekcji **Użytkownicy i role** kliknij pozycję **Użytkownicy**.

   ![Dodaj użytkownika](./media/adaptivesuite-tutorial/IC805648.png "Dodaj użytkownika")

4. W sekcji **Nowy użytkownik** wykonaj następujące czynności:

   ![Submit](./media/adaptivesuite-tutorial/IC805649.png "Submit")

   a. Wpisz **nazwę,** **nazwę użytkownika,** **adres e-mail,** **hasło** prawidłowego użytkownika usługi Azure Active Directory, który chcesz udostępnić w powiązanych polach tekstowych.

   b. Wybierz **rolę**.

   d. Kliknij **przycisk Prześlij**.

> [!NOTE]
> Do aprowizowania kont użytkowników usługi Azure AD można użyć innych narzędzi do tworzenia kont użytkowników usługi Adaptive Insights lub interfejsów API udostępnianych przez usługę Adaptive Insights.

### <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Usługi Adaptive Insights w Panelu dostępu należy automatycznie zalogować się do usługi Adaptive Insights, dla której skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

