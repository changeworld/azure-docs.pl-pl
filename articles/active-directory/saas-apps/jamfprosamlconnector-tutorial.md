---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą Jamf Pro | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i oprogramowaniem Jamf Pro.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 35e86d08-c29e-49ca-8545-b0ff559c5faf
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 24f0d067351ab2e24b103624c8534ac8d6f2e757
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70305438"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-jamf-pro"></a>Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą Jamf Pro

W tym samouczku dowiesz się, jak zintegrować usługę Jamf Pro z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Jamf Pro z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Jamf Pro.
* Zezwól użytkownikom na automatyczne logowanie do Jamf Pro z kontami usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) usługi Jamf Pro.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Oprogramowanie Jamf Pro obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług oraz dostawcę tożsamości**

## <a name="adding-jamf-pro-from-the-gallery"></a>Dodawanie oprogramowania Jamf Pro z galerii

Aby skonfigurować integrację oprogramowania Jamf Pro z usługą Azure AD, musisz dodać oprogramowanie Jamf Pro z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Jamf Pro** w polu wyszukiwania.
1. Wybierz pozycję **Jamf Pro** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-jamf-pro"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla Jamf Pro

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Jamf Pro przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w programie Jamf Pro.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Jamf Pro, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne w usłudze Jamf Pro](#configure-jamf-pro-sso)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego Jamf Pro](#create-jamf-pro-test-user)** , aby dysponować odpowiednikiem B. Simon w Jamf Pro, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Jamf Pro** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.jamfcloud.com/saml/metadata`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.jamfcloud.com/saml/SSO`

1. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.jamfcloud.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Rzeczywista wartość identyfikatora zostanie pobrana z sekcji **Single Sign-On** (Logowanie jednokrotne) w portalu oprogramowania Jamf Pro, co objaśniono w dalszej części tego samouczka. Możesz wyodrębnić rzeczywistą wartość **poddomeny** z wartości identyfikatora i użyć tych informacji o **poddomenie** w adresie URL logowania i adresie URL odpowiedzi. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** kliknij przycisk Kopiuj, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **nowego użytkownika** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension. Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do usługi Jamf Pro.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz **Jamf Pro**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

## <a name="configure-jamf-pro-sso"></a>Konfigurowanie rejestracji jednokrotnej Jamf Pro

1. Aby zautomatyzować konfigurację w oprogramowaniu Jamf Pro, musisz zainstalować **rozszerzenie przeglądarki do bezpiecznego logowania Moje aplikacje**, klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki kliknij pozycję **Skonfiguruj aplikację Jamf Pro**. Spowoduje to bezpośrednie przejście do aplikacji Jamf Pro. W tym miejscu podaj poświadczenia administratora w celu zalogowania się do aplikacji Jamf Pro. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i zautomatyzuje kroki 3–7.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować Jamf Pro, Otwórz nowe okno przeglądarki sieci Web i zaloguj się w witrynie firmy Jamf Pro jako administrator i wykonaj następujące czynności:

4. Kliknij **ikonę Ustawienia** w prawym górnym rogu strony.

    ![Konfiguracja oprogramowania Jamf Pro](./media/jamfprosamlconnector-tutorial/configure1.png)

5. Kliknij pozycję **Single Sign On** (Logowanie jednokrotne).

    ![Konfiguracja oprogramowania Jamf Pro](./media/jamfprosamlconnector-tutorial/configure2.png)

6. Na stronie **Single Sign On**(Logowanie jednokrotne) wykonaj następujące czynności:

    ![Konfiguracja oprogramowania Jamf Pro](./media/jamfprosamlconnector-tutorial/configure3.png)

    a. Zaznacz **opcję Włącz uwierzytelnianie logowania jednokrotnego**.

    b. Wybierz pozycję **inne** jako opcję z listy rozwijanej **dostawca tożsamości** .

    c. W polu tekstowym **OTHER PROVIDER** (INNY DOSTAWCA) wprowadź wartość **Azure AD**.

    d. Skopiuj wartość **Identyfikator jednostki** i wklej ją do pola tekstowego **Identyfikator (identyfikator jednostki)** w sekcji **Podstawowa konfiguracja SAML** na Azure Portal.

    > [!NOTE]
    > W `<SUBDOMAIN>` tym miejscu należy użyć tej wartości, aby uzupełnić adres URL logowania i adres URL odpowiedzi w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

    e. Wybierz pozycję **adres URL metadanych** jako opcję z listy rozwijanej **Źródło metadanych dostawcy tożsamości** i w poniższym polu tekstowym wklej wartość **adresu URL metadanych federacji aplikacji** skopiowaną z Azure Portal.

7. Na tej samej stronie przewiń w dół do sekcji **Mapowanie użytkownika** i wykonaj następujące czynności: 

    ![Logowanie jednokrotne w oprogramowaniu Jamf Pro](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_single.png)

    a. Wybierz opcję **NameID** dla **mapowania użytkownika dostawcy tożsamości**. Domyślnie to ustawienie ma wartość **NameID**, ale można zdefiniować atrybut niestandardowy.

    b. Wybierz pozycję **poczta e-mail** dla **mapowania użytkowników JAMF Pro**. Oprogramowanie Jamf Pro mapuje atrybuty SAML wysyłane przez dostawcę tożsamości na następujące sposoby: według użytkowników i według grup. Gdy użytkownik próbuje uzyskać dostęp do oprogramowania Jamf Pro, domyślnie oprogramowanie Jamf Pro pobiera informacje o użytkowniku od dostawcy tożsamości i uzgadnia je względem kont użytkowników w oprogramowaniu Jamf Pro. Jeśli przychodzące konto użytkownika nie istnieje w oprogramowaniu Jamf Pro, wykonywane jest dopasowanie nazwy grupy.

    c. Wklej wartość `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` w polu tekstowym **nazwa atrybutu grupy dostawcy tożsamości** .

    d. Wybranie opcji **zezwól użytkownikom na pomijanie uwierzytelniania logowania** jednokrotnego użytkownicy nie zostaną przekierowani do strony podpisywania dostawcy tożsamości w celu uwierzytelnienia, ale może zamiast tego zalogować się do Jamf Pro. Gdy użytkownik spróbuje uzyskać dostęp do oprogramowania Jamf Pro za pośrednictwem dostawcy tożsamości, nastąpi uwierzytelnianie i autoryzacja za pomocą logowania jednokrotnego zainicjowanego przez dostawcę tożsamości.

    e. Kliknij polecenie **Zapisz**.

### <a name="create-jamf-pro-test-user"></a>Tworzenie użytkownika testowego oprogramowania Jamf Pro

Aby umożliwić użytkownikom usługi Azure AD logowanie się w usłudze Jamf Pro, muszą one być obsługiwane w programie Jamf Pro. W przypadku oprogramowania Jamf Pro aprowizacja jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do witryny firmy Jamf Pro jako administrator.

2. Kliknij **ikonę Ustawienia** w prawym górnym rogu strony.

    ![Dodawanie pracownika](./media/jamfprosamlconnector-tutorial/configure1.png)

3. Kliknij pozycję **Konta i grupy użytkowników oprogramowania Jamf Pro**.

    ![Dodawanie pracownika](./media/jamfprosamlconnector-tutorial/user1.png)

4. Kliknij przycisk **Nowy**.

    ![Dodawanie pracownika](./media/jamfprosamlconnector-tutorial/user2.png)

5. Wybierz pozycję **Utwórz standardowe konto**.

    ![Dodawanie pracownika](./media/jamfprosamlconnector-tutorial/user3.png)

6. W oknie dialogowym **Nowe konto** wykonaj następujące kroki:

    ![Dodawanie pracownika](./media/jamfprosamlconnector-tutorial/user4.png)

    a. W polu tekstowym **USERNAME** (NAZWA UŻYTKOWNIKA) wpisz pełną nazwę użytkownika BrittaSimon.

    b. Wybierz odpowiednie opcje zgodnie z Twoją organizacją dla pól **ACCESS LEVEL** (POZIOM DOSTĘPU), **PRIVILEGE SET** (ZESTAW UPRAWNIEŃ) i **ACCESS STATUS** (STAN DOSTĘPU).

    c. W polu tekstowym **FULL NAME** (IMIĘ I NAZWISKO) wpisz imię i nazwisko Britta Simon.

    d. W polu tekstowym **EMAIL ADDRESS** (ADRES E-MAIL) wpisz adres e-mail konta użytkownika Britta Simon.

    e. W polu tekstowym **PASSWORD** (HASŁO) wpisz hasło użytkownika.

    f. W polu tekstowym **VERIFY PASSWORD** (WERYFIKUJ HASŁO) wpisz hasło użytkownika.

    g. Kliknij polecenie **Zapisz**.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Jamf Pro w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Jamf Pro, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj usługę Jamf Pro z usługą Azure AD](https://aad.portal.azure.com/)

