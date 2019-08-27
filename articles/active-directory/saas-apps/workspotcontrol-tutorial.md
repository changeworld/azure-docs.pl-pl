---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą kontrolki Workspot | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne usługi Azure Active Directory i Workspot kontroli.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3ea8e4e9-f61f-4f45-b635-b0e306eda3d1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: jeedes
ms.openlocfilehash: 086ec95531b01477be56d4b1a19d189f167a020f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67086680"
---
# <a name="tutorial-azure-active-directory-integration-with-workspot-control"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą kontrolki Workspot

W tym samouczku dowiesz się, jak zintegrować Workspot kontroli przy użyciu usługi Azure Active Directory (Azure AD). Po zintegrowaniu Workspot kontrolki z usługą Azure AD możesz wykonywać następujące czynności:

* Używaj usługi Azure AD w celu kontrolowania, kto ma dostęp do kontroli Workspot.
* Umożliwić użytkownikom automatyczne logowanie do sterowania Workspot (logowanie jednokrotne [logowanie Jednokrotne]) za pomocą ich kont usługi Azure AD.
* Zarządzanie Twoimi kontami w jednej centralnej lokalizacji: witryna Azure portal.

Aby uzyskać więcej informacji na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [logowanie jednokrotne do aplikacji w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą kontrolki Workspot, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/).

* Formant Workspot pojedynczy znak na włączona subskrypcji.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

> [!Note]
> Sterowanie Workspot obsługuje logowanie Jednokrotne zainicjowane przez Dostawcę i inicjowane przez dostawcę tożsamości.


## <a name="adding-workspot-control-from-the-gallery"></a>Dodawanie kontrolki Workspot z galerii

Aby skonfigurować integrację Workspot kontrolki w usłudze Azure AD, należy dodać formant Workspot z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać kontrolkę Workspot z galerii, wykonaj następujące kroki:**

1. W okienku po lewej stronie [witryny Azure portal](https://portal.azure.com), wybierz opcję **usługi Azure Active Directory**.

    ![Przycisk usługi Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacje dla przedsiębiorstw** i wybierz **wszystkie aplikacje**.

    ![Okienko Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Wybierz **nową aplikację** w górnej części okna.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **kontroli Workspot**, wybierz opcję **kontroli Workspot** z panel wyników, a następnie wybierz **Dodaj**.

     !["Dodaj z galerii" okna](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowania usługi Azure AD logowanie jednokrotne za pomocą kontrolki Workspot dla użytkownika testowego, Britta Simon.
Dla logowania jednokrotnego do pracy należy nawiązania połączenia między użytkownika usługi Azure AD i powiązanych użytkownika w kontrolce Workspot.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą kontrolki Workspot, należy wykonać następujące zadania:

1. [Skonfiguruj logowanie jednokrotne w usłudze Azure AD](#configure-azure-ad-single-sign-on), aby umożliwić użytkownikom korzystanie z tej funkcji.
2. [Konfigurowanie kontroli Workspot logowania jednokrotnego](#configure-workspot-control-single-sign-on) do konfigurowania pojedynczego ustawień logowania jednokrotnego na stronie aplikacji.
3. [Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user) do testowania usługi Azure AD logowanie jednokrotne dla Britta Simon.
4. [Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user) — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. [Tworzenie użytkownika testowego kontroli Workspot](#create-a-workspot-control-test-user) nawiązać odpowiednikiem Britta Simon w formancie Workspot, połączonego z usługi Azure AD reprezentacja użytkownika.
6. [Przetestuj logowanie jednokrotne](#test-single-sign-on), aby sprawdzić działanie konfiguracji.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne za pomocą kontrolki Workspot, wykonaj następujące kroki:

1. Na **kontroli Workspot** strony integracji aplikacji w [witryny Azure portal](https://portal.azure.com/), wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W **wybierz jedną metodę logowania jednokrotnego** oknie Wybierz **SAML** trybu, aby włączyć logowanie jednokrotne.

    ![Wybierz okno pojedynczego wybierz metodę logowania](common/select-saml-option.png)

3. Na **Ustaw się logowanie jednokrotne z SAML** wybierz opcję **Edytuj** (ołówka) Aby uzyskać dostęp do **podstawową konfigurację protokołu SAML**.

    ![Edytuj ikonę wyróżnione "Podstawowa konfiguracja SAML"](common/edit-urls.png)

4. W **podstawową konfigurację protokołu SAML** sekcji, aby skonfigurować aplikację w trybie inicjowane przez dostawcę tożsamości, wykonaj następujące kroki:

    ![Kontrolka Workspot domena i adresy URL pojedynczy informacje logowania jednokrotnego](common/idp-intiated.png)

    1. W **identyfikator** tekstu wprowadź adres URL w następującym wzorcem:<br/>
    ***https://<<i></i>NAZWA_WYSTĄPIENIA >-saml.workspot.com/saml/metadata***

    1. W **adres URL odpowiedzi** tekstu wprowadź adres URL w następującym wzorcem:<br/>
    ***https://<<i></i>NAZWA_WYSTĄPIENIA >-saml.workspot.com/saml/assertion***

5. Aby skonfigurować aplikację w trybie zainicjowanego przez dostawcę usług, zaznacz **Ustaw dodatkowe adresy URL**.

    ![Kontrolka Workspot domena i adresy URL pojedynczy informacje logowania jednokrotnego](common/metadata-upload-additional-signon.png)

    W **adres URL logowania** tekstu wprowadź adres URL w następującym wzorcem:<br/>
    ***https://<<i></i>NAZWA_WYSTĄPIENIA >-saml.workspot.com/***

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp te wartości z identyfikatorem rzeczywisty adres URL odpowiedzi i adres URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta sterowania Workspot](mailto:support@workspot.com) do uzyskania tych wartości. Lub też mogą odwoływać się do wzorców w **podstawową konfigurację protokołu SAML** części witryny Azure portal.

6. Na **Ustaw się logowanie jednokrotne z SAML** strony w **certyfikat podpisywania SAML** zaznacz **Pobierz** można pobrać **certyfikat (Base64)** z listy dostępnych opcji, zgodnie z wymaganiami. Zapisz go na komputerze.

    ![Link pobierania certyfikat (Base64)](common/certificatebase64.png)

7. W **skonfigurować kontrolę Workspot** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami:

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    - **Adres URL logowania**

    - **Identyfikator usługi Azure AD**

    - **Adres URL wylogowania**

### <a name="configure-workspot-control-single-sign-on"></a>Konfigurowanie kontroli Workspot logowania jednokrotnego

1. W oknie przeglądarki internetowej innej Zaloguj się do sterowania Workspot jako Administrator zabezpieczeń.

2. Na pasku narzędzi w górnej części strony wybierz **instalacji** i następnie **SAML**.

    ![Opcje konfiguracji](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_setup.png)

3. W **konfigurację języka znaczników asercji zabezpieczeń** okna, wykonaj następujące kroki:
 
    ![Okno konfigurację języka znaczników asercji zabezpieczeń](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_saml.png)

    1. W **identyfikator jednostki** pole, Wklej **usługi Azure Ad identyfikator** skopiowaną z witryny Azure portal.

    1. W **adres URL usługi logować** pole, Wklej **adres URL logowania** skopiowaną z witryny Azure portal.

    1. W **adres URL usługi wylogowania** pole, Wklej **adres URL wylogowania** skopiowaną z witryny Azure portal.

    1. Wybierz **plik aktualizacji** do przekazania do certyfikatów X.509 base-64 zakodowany certyfikat, który został pobrany z witryny Azure portal.

    1. Wybierz pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w witrynie Azure portal.

1. W okienku po lewej stronie w witrynie Azure Portal wybierz **usługi Azure Active Directory**, **użytkowników**, a następnie **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](common/users.png)

2. Wybierz **nowego użytkownika** w górnej części okna.

    !["Nowy użytkownik" przycisk](common/new-user.png)

3. W oknie właściwości dla użytkownika, wykonaj następujące kroki:

    ![W oknie właściwości użytkownika](common/user-properties.png)

    1. W **nazwa** wprowadź **BrittaSimon**.
  
    1. W **nazwa_użytkownika** wprowadź **brittasimon@* yourcompanydomain.extension* **. Na przykład, wprowadź  **BrittaSimon@contoso.<i> </i>com**.

    1. Wybierz **hasło Show** pole wyboru. Zapisz wartość, która jest wyświetlana w **hasło** pole.

    1. Wybierz pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz udzielać dostępu Britta Simon Workspot kontrolki umożliwiające jej do użycia platformy Azure logowania jednokrotnego.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, **wszystkie aplikacje**, a następnie **kontroli Workspot**.

    ![Okienko Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji zaznacz **kontroli Workspot**.

    ![Link Workspot formant na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

4. Wybierz **Dodaj użytkownika** przycisku. Następnie wybierz pozycję **użytkowników i grup** w **Dodaj przypisanie** okna.

    ![W oknie "Dodawanie przypisania"](common/add-assign-user.png)

5. W **użytkowników i grup** wybierz **Britta Simon** z **użytkowników** listy. Następnie kliknij pozycję **Wybierz**.

6. Jeśli oczekujesz od dowolnej wartości roli dla asercji SAML, wybierz odpowiednią rolę dla użytkownika z listy w **wybierz rolę** okna. Następnie kliknij przycisk **wybierz** u dołu.

7. W **Dodaj przydziału** wybierz **przypisać**.

### <a name="create-a-workspot-control-test-user"></a>Tworzenie użytkownika testowego Workspot kontroli

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do sterowania Workspot, musi być obsługiwana w systemie kontroli Workspot. Inicjowanie obsługi to zadanie ręczne.

**Aby udostępnić konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do sterowania Workspot jako Administrator zabezpieczeń.

2. Na pasku narzędzi w górnej części strony wybierz **użytkowników** i następnie **Dodaj użytkownika**.

    ![Opcji "Użytkownicy"](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_adduser.png)

3. W **dodać nowego użytkownika** okna, wykonaj następujące kroki:

    ![Okno "Dodaj nowego użytkownika"](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addnewuser.png)

    1. W **imię** Wprowadź imię użytkownika, takie jak **Britta**.

    1. W **nazwisko** tekstu wprowadź nazwisko użytkownika, takie jak **Simon**.

    1. W **E-mail** wprowadź adres e-mail użytkownika, takie jak  **Brittasimon@contoso.<i> </i>com**.

    1. Wybieranie odpowiedniej roli użytkownika z **roli** listy rozwijanej.

    1. Wybierz do odpowiedniej grupy użytkowników z **grupy** listy rozwijanej.

    1. Wybierz pozycję **Add User** (Dodaj użytkownika).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji testujemy naszej usługi Azure AD jednej konfiguracji logowania jednokrotnego za pośrednictwem *panelu dostępu*.

Po kliknięciu **kontroli Workspot** kafelka w panelu dostępu, powinny być automatycznie zarejestrowaniu w usłudze kontroli Workspot, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Samouczków dotyczących integrowania aplikacji SaaS przy użyciu usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

- [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
