---
title: 'Samouczek: Integracja usługi Azure Active Directory z GlassFrog | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i GlassFrog.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7cf5dae6-32d6-418e-8ef2-b2041e686086
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2018
ms.author: jeedes
ms.openlocfilehash: 1fdd1e8acf446ba3e409e2ff0bf9ca18f581c299
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2018
ms.locfileid: "49431373"
---
# <a name="tutorial-azure-active-directory-integration-with-glassfrog"></a>Samouczek: Integracja usługi Azure Active Directory z GlassFrog

W tym samouczku dowiesz się, jak zintegrować GlassFrog w usłudze Azure Active Directory (Azure AD).

Integrowanie GlassFrog z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do GlassFrog.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do GlassFrog (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą GlassFrog, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- GlassFrog logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie GlassFrog z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-glassfrog-from-the-gallery"></a>Dodawanie GlassFrog z galerii
Aby skonfigurować integrację GlassFrog w usłudze Azure AD, należy dodać GlassFrog z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać GlassFrog z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![image](./media/glassfrog-tutorial/selectazuread.png)

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![image](./media/glassfrog-tutorial/a_select_app.png)
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![image](./media/glassfrog-tutorial/a_new_app.png)

4. W polu wyszukiwania wpisz **GlassFrog**, wybierz opcję **GlassFrog** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

     ![image](./media/glassfrog-tutorial/tutorial_glassfrog_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą GlassFrog w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w GlassFrog do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w GlassFrog musi można ustanowić.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą GlassFrog, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego GlassFrog](#create-a-glassfrog-test-user)**  — aby odpowiednikiem Britta Simon w GlassFrog połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji GlassFrog.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z GlassFrog, wykonaj następujące czynności:**

1. W [witryny Azure portal](https://portal.azure.com/)na **GlassFrog** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![image](./media/glassfrog-tutorial/b1_b2_select_sso.png)

2. Na **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, kliknij przycisk **wybierz** dla **SAML** trybu, aby włączyć logowanie jednokrotne.

    ![image](./media/glassfrog-tutorial/b1_b2_saml_sso.png)

3. Na **Ustaw się logowanie jednokrotne z SAML** kliknij **Edytuj** przycisk, aby otworzyć **podstawową konfigurację protokołu SAML** okna dialogowego.

    ![image](./media/glassfrog-tutorial/b1-domains_and_urlsedit.png)

4. Na **podstawową konfigurację protokołu SAML** sekcji, wykonaj następujące czynności:

    W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca:  `https://app.glassfrog.com/people/sso?org_id=<ORGANIZATIONID>`

    ![image](./media/glassfrog-tutorial/tutorial_glassfrog_url.png)

    > [!NOTE] 
    > Wartość adres URL logowania nie jest prawdziwe. Zaktualizuj wartość za pomocą adresu URL logowania rzeczywistych. Skontaktuj się z pomocą [zespołem pomocy technicznej GlassFrog](https://support.glassfrog.com/support/solutions/9000107654) można uzyskać wartość.
 
5. Na **Ustaw się logowania jednokrotnego przy użyciu protokołu SAML** strony w **certyfikat podpisywania SAML** kliknij **Pobierz** można pobrać **XML metadanych Federacji**  i zapisz go na komputerze.

    ![image](./media/glassfrog-tutorial/tutorial_glassfrog_certificate.png) 

6. Aby skonfigurować logowanie jednokrotne na **GlassFrog** stronie, musisz wysłać pobrany **XML metadanych Federacji** do [zespołem pomocy technicznej GlassFrog](mailto:support@alchemy.fr). Ustawiają to ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

1. W witrynie Azure portal w okienku po lewej stronie wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**.

    ![image](./media/glassfrog-tutorial/d_users_and_groups.png)

2. Wybierz **nowego użytkownika** w górnej części ekranu.

    ![image](./media/glassfrog-tutorial/d_adduser.png)

3. We właściwościach użytkownika wykonaj następujące czynności.

    ![image](./media/glassfrog-tutorial/d_userproperties.png)

    a. W **nazwa** pola wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    c. Wybierz **właściwości**, wybierz opcję **hasło Show** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w polu hasło.

    d. Wybierz pozycję **Utwórz**.
 
### <a name="create-a-glassfrog-test-user"></a>Tworzenie użytkownika testowego GlassFrog

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w GlassFrog. Praca z [zespołem pomocy technicznej GlassFrog](https://support.glassfrog.com/support/solutions/9000107654) Aby dodać użytkowników na platformie GlassFrog. Użytkownicy muszą być tworzone i aktywowana, aby używać logowania jednokrotnego.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do GlassFrog.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**.

    ![image](./media/glassfrog-tutorial/d_all_applications.png)

2. Na liście aplikacji wybierz **GlassFrog**.

    ![image](./media/glassfrog-tutorial/tutorial_glassfrog_app.png)

3. W menu po lewej stronie wybierz **użytkowników i grup**.

    ![image](./media/glassfrog-tutorial/d_leftpaneusers.png)

4. Wybierz **Dodaj** przycisk, a następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okna dialogowego.

    ![image](./media/glassfrog-tutorial/d_assign_user.png)

4. W **użytkowników i grup** okna dialogowego wybierz **Britta Simon** na liście użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.

5. W **Dodaj przydziału** okna dialogowego wybierz **przypisać** przycisku.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka GlassFrog w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji GlassFrog.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


