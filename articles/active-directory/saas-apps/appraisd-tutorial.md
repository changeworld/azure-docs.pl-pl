---
title: 'Samouczek: Integracja usługi Azure Active Directory z Appraisd | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Appraisd.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: db063306-4d0d-43ca-aae0-09f0426e7429
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2018
ms.author: jeedes
ms.openlocfilehash: dae9a59b89f03a50b1adaed55cd8f97c06906526
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2018
ms.locfileid: "49118665"
---
# <a name="tutorial-azure-active-directory-integration-with-appraisd"></a>Samouczek: Integracja usługi Azure Active Directory z Appraisd

W tym samouczku dowiesz się, jak zintegrować Appraisd w usłudze Azure Active Directory (Azure AD).

Integrowanie Appraisd z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Appraisd.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do Appraisd (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Appraisd, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Appraisd logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Appraisd z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-appraisd-from-the-gallery"></a>Dodawanie Appraisd z galerii
Aby skonfigurować integrację Appraisd w usłudze Azure AD, należy dodać Appraisd z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Appraisd z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![image](./media/appraisd-tutorial/selectazuread.png)

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![image](./media/appraisd-tutorial/a_select_app.png)
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![image](./media/appraisd-tutorial/a_new_app.png)

4. W polu wyszukiwania wpisz **Appraisd**, wybierz opcję **Appraisd** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

     ![image](./media/appraisd-tutorial/tutorial_appraisd_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Appraisd w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Appraisd do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Appraisd musi można ustanowić.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Appraisd, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego Appraisd](#create-an-appraisd-test-user)**  — aby odpowiednikiem Britta Simon w Appraisd połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Appraisd.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Appraisd, wykonaj następujące czynności:**

1. W [witryny Azure portal](https://portal.azure.com/)na **Appraisd** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![image](./media/appraisd-tutorial/B1_B2_Select_SSO.png)

2. Na **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, wybierz **SAML** trybu, aby włączyć logowanie jednokrotne.

    ![image](./media/appraisd-tutorial/b1_b2_saml_sso.png)

3. Na **Ustaw się logowanie jednokrotne z SAML** kliknij **Edytuj** przycisk, aby otworzyć **podstawową konfigurację protokołu SAML** okna dialogowego.

    ![image](./media/appraisd-tutorial/b1-domains_and_urlsedit.png)

4. Na **podstawową konfigurację protokołu SAML** sekcji, wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_url.png)

    a. Kliknij przycisk **Ustaw dodatkowe adresy URL**. 

    b. W **tan przekaźnika** pole tekstowe, wpisz adres URL: `<TENANTCODE>`

    c. Jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb, w **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://app.appraisd.com/saml/<TENANTCODE>`

    > [!NOTE]
    > Uzyskujesz rzeczywisty adres URL logowania i stan przekazywania wartości na stronie konfiguracji logowania jednokrotnego Appraisd, które zostało wyjaśnione w dalszej części tego samouczka.
    
5. Aplikacja Appraisd oczekuje twierdzenia SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Możesz zarządzać wartości te atrybuty z **atrybutów użytkownika** sekcji na stronie integracji aplikacji. Kliknij przycisk **Edytuj** przycisk, aby otworzyć **atrybutów użytkownika** okna dialogowego.

    ![image](./media/appraisd-tutorial/i3-attribute.png)

6. W **oświadczenia użytkownika** sekcji na **atrybutów użytkownika** okno dialogowe, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej i wykonaj następujące czynności:
    
    a. Kliknij przycisk **Edytuj** przycisk, aby otworzyć **Zarządzanie oświadczenia użytkownika** okna dialogowego.

    ![image](./media/appraisd-tutorial/i2-attribute.png)

    ![image](./media/appraisd-tutorial/i4-attribute.png)

    b. Z **atrybut źródłowy** , wybierz wartość atrybutu na liście.

    c. Kliknij pozycję **Zapisz**. 

7. W **certyfikat podpisywania SAML** kliknij **Pobierz** można pobrać **certyfikat (Base64)** i zapisz go na komputerze.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_certficate.png) 

8. Na **Konfigurowanie Appraisd** sekcji, skopiuj odpowiedni adres URL, zgodnie z wymaganiami.

    a. Adres URL logowania

    b. Identyfikator usługi Azure Ad

    c. Adres URL wylogowywania

    ![image](./media/appraisd-tutorial/d1_samlsonfigure.png)

9. W oknie przeglądarki internetowej innej Zaloguj się do Appraisd jako Administrator zabezpieczeń.

10. U góry po prawej stronie kliknij pozycję na **ustawienia** ikonę, a następnie przejdź do **konfiguracji**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_sett.png)

11. Z lewej strony menu, kliknij pozycję **SAML logowania jednokrotnego**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_single.png)

12. Na **konfiguracji SAML 2.0 logowania jednokrotnego** strony, wykonaj następujące czynności:
    
    ![image](./media/appraisd-tutorial/tutorial_appraisd_saml.png)

    a. Kopiuj **domyślny stan przekazywania** wartości, a następnie wklej je **tan przekaźnika** polu tekstowym w **podstawową konfigurację protokołu SAML** w witrynie Azure portal.

    b. Kopiuj **adres URL logowania zainicjowanych przez usługę** wartości, a następnie wklej je **adres URL logowania** polu tekstowym w **podstawową konfigurację protokołu SAML** w witrynie Azure portal.

13. Przewiń w dół strony w obszarze **identyfikowanie użytkowników**, wykonaj następujące czynności:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_identifying.png)

    a. W **pojedynczy znak na adres URL dostawcy tożsamości** pola tekstowego, Wklej wartość **adres URL logowania**, skopiowanej w witrynie Azure portal, a następnie kliknij **Zapisz**.

    b. W **adres URL wystawcy dostawcy tożsamości** pola tekstowego, Wklej wartość **usługi Azure Ad identyfikator**, skopiowanej w witrynie Azure portal, a następnie kliknij **Zapisz**.

    c. W programie Notatnik, otwórz certyfikat zakodowany base-64, pobrany z witryny Azure portal, skopiuj jego zawartość i następnie wklej go do **certyfikat X.509** pole, a następnie kliknij przycisk **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

1. W witrynie Azure portal w okienku po lewej stronie wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**.

    ![image](./media/appraisd-tutorial/d_users_and_groups.png)

2. Wybierz **nowego użytkownika** w górnej części ekranu.

    ![image](./media/appraisd-tutorial/d_adduser.png)

3. We właściwościach użytkownika wykonaj następujące czynności.

    ![image](./media/appraisd-tutorial/d_userproperties.png)

    a. W **nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    c. Wybierz **właściwości**, wybierz opcję **hasło Show** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w polu hasło.

    d. Wybierz pozycję **Utwórz**.
 
### <a name="create-an-appraisd-test-user"></a>Tworzenie użytkownika testowego Appraisd

Aby włączyć usługi Azure AD użytkownicy logują się do Appraisd, ich musi być obsługiwana w Appraisd. W Appraisd Inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby udostępnić konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do Appraisd jako Administrator zabezpieczeń.

2. U góry po prawej stronie kliknij pozycję na **ustawienia** ikonę, a następnie przejdź do **Centrum administracyjne**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_admin.png)

3. Na pasku narzędzi w górnej części strony kliknij **osób**, a następnie przejdź do **dodać nowego użytkownika**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_user.png)

4. Na **dodać nowego użytkownika** strony, wykonaj następujące czynności:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_newuser.png)
    
    a. W **imię** tekstu Wprowadź imię użytkownika, takich jak **Britta**.

    b. W **nazwisko** tekstu wprowadź nazwisko użytkownika, takich jak **simon**.

    c. W **E-mail** tekstu wprowadź adres e-mail użytkownika, takich jak **Brittasimon@contoso.com**.

    d. Kliknij pozycję **Dodaj użytkownika**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Appraisd.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**.

    ![image](./media/appraisd-tutorial/d_all_applications.png)

2. Na liście aplikacji wybierz **Appraisd**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_app.png)

3. W menu po lewej stronie wybierz **użytkowników i grup**.

    ![image](./media/appraisd-tutorial/d_leftpaneusers.png)

4. Wybierz **Dodaj** przycisk, a następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okna dialogowego.

    ![image](./media/appraisd-tutorial/d_assign_user.png)

4. W **użytkowników i grup** okna dialogowego wybierz **Britta Simon** na liście użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.

5. W **Dodaj przydziału** okno dialogowe, wybierz opcję **przypisać** przycisku.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Appraisd w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Appraisd.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
