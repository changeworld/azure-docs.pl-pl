---
title: 'Samouczek: Integracja usługi Azure Active Directory z Expensify | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Expensify.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 1e761484-7a2f-4321-91f4-6d5d0b69344e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2018
ms.author: jeedes
ms.openlocfilehash: c9ee0af3cbf2c1aa7b24d2f4cf5fba9d664bc087
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2018
ms.locfileid: "48248044"
---
# <a name="tutorial-azure-active-directory-integration-with-expensify"></a>Samouczek: Integracja usługi Azure Active Directory z Expensify

W tym samouczku dowiesz się, jak zintegrować Expensify w usłudze Azure Active Directory (Azure AD).

Integrowanie Expensify z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Expensify.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do Expensify (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Expensify, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Expensify logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Expensify z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-expensify-from-the-gallery"></a>Dodawanie Expensify z galerii

Aby skonfigurować integrację Expensify w usłudze Azure AD, należy dodać Expensify z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Expensify z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![image](./media/expensify-tutorial/selectazuread.png)

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![image](./media/expensify-tutorial/a_select_app.png)
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![image](./media/expensify-tutorial/a_new_app.png)

4. W polu wyszukiwania wpisz **Expensify**, wybierz opcję **Expensify** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

     ![image](./media/expensify-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Expensify w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Expensify do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Expensify musi można ustanowić.

W Expensify, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Expensify, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego Expensify](#create-an-expensify-test-user)**  — aby odpowiednikiem Britta Simon w Expensify połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Expensify.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Expensify, wykonaj następujące czynności:**

1. W [witryny Azure portal](https://portal.azure.com/)na **Expensify** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![image](./media/expensify-tutorial/b1_b2_select_sso.png)

2. Kliknij przycisk **pojedynczej zmiany trybu logowania jednokrotnego** na górze ekranu, aby wybrać **SAML** trybu.

      ![image](./media/expensify-tutorial/b1_b2_saml_ssso.png)

3. Na **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, kliknij przycisk **wybierz** dla **SAML** trybu, aby włączyć logowanie jednokrotne.

    ![image](./media/expensify-tutorial/b1_b2_saml_sso.png)

4. Na **Ustaw się logowanie jednokrotne z SAML** kliknij **Edytuj** przycisk, aby otworzyć **podstawową konfigurację protokołu SAML** okna dialogowego.

    ![image](./media/expensify-tutorial/b1-domains_and_urlsedit.png)

5. Na **podstawową konfigurację protokołu SAML** sekcji, wykonaj następujące czynności:

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL jako: `https://www.expensify.com/authentication/saml/login`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://www.<companyname>.expensify.com`

    ![image](./media/expensify-tutorial/b1-domains_and_urls.png)

    > [!NOTE] 
    > Zastąp <companyname> sekcji adres URL identyfikatora z domeny Twojej firmy. Zobacz przykład `https://contoso.expensify.com` powyżej. W Expensify, jest to nazwa domeny jak pokazano w **Ustawienia > domeny kontroli**.

    ![Informacje o domenie expensify](./media/expensify-tutorial/tutorial_expensify_domain.png)

6. Na **Ustaw się logowania jednokrotnego przy użyciu protokołu SAML** strony w **certyfikat podpisywania SAML** , kliknij przycisk **Pobierz** można pobrać odpowiedni certyfikat zgodnie z wymagania i zapisz go na komputerze.

    ![image](./media/expensify-tutorial/certificatebase64.png)

7. Aby włączyć logowanie Jednokrotne w Expensify, należy najpierw włączyć **kontroli domeny** w aplikacji. Kontrola domeny można włączyć w aplikacji za pomocą kroków wymienionych [tutaj](http://help.expensify.com/domain-control). Aby uzyskać dodatkową pomoc, pracować [zespołem pomocy technicznej klienta Expensify](mailto:help@expensify.com). Jeśli masz włączoną kontrolą domeny, wykonaj następujące kroki:
   
    ![Konfigurowanie logowania jednokrotnego](./media/expensify-tutorial/tutorial_expensify_51.png)
    
    a. Zaloguj się do aplikacji Expensify.
    
    b. W okienku po lewej stronie kliknij **ustawienia** i przejdź do **SAML**.
    
    c. Przełącz **logowania języka SAML** opcję **włączone**.
    
    d. Otwórz pobrany metadanych federacji z usługi Azure AD w programie Notatnik, skopiuj zawartość, a następnie wklej go do **metadanych dostawcy tożsamości** pola tekstowego.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

1. W witrynie Azure portal w okienku po lewej stronie wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**.

    ![image](./media/expensify-tutorial/d_users_and_groups.png)

2. Wybierz **nowego użytkownika** w górnej części ekranu.

    ![image](./media/expensify-tutorial/d_adduser.png)

3. We właściwościach użytkownika wykonaj następujące czynności.

    ![image](./media/expensify-tutorial/d_userproperties.png)

    a. W **nazwa** pola wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    c. Wybierz **właściwości**, wybierz opcję **hasło Show** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w polu hasło.

    d. Wybierz pozycję **Utwórz**.
 
### <a name="create-an-expensify-test-user"></a>Tworzenie użytkownika testowego Expensify

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w Expensify. Praca z [zespołem pomocy technicznej klienta Expensify](mailto:help@expensify.com) Aby dodać użytkowników na platformie Expensify.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Expensify.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**.

    ![image](./media/expensify-tutorial/d_all_applications.png)

2. Na liście aplikacji wybierz **Expensify**.

    ![image](./media/expensify-tutorial/d_all_proapplications.png)

3. W menu po lewej stronie wybierz **użytkowników i grup**.

    ![image](./media/expensify-tutorial/d_leftpaneusers.png)

4. Wybierz **Dodaj** przycisk, a następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okna dialogowego.

    ![image](./media/expensify-tutorial/d_assign_user.png)

4. W **użytkowników i grup** okna dialogowego wybierz **Britta Simon** na liście użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.

5. W **Dodaj przydziału** okna dialogowego wybierz **przypisać** przycisku.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Expensify w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Expensify.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)




