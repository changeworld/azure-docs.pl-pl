---
title: 'Samouczek: Integracja usługi Azure Active Directory z XaitPorter | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i XaitPorter.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d33c7cb7-0550-425b-882a-619a713a71b7
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2017
ms.author: jeedes
ms.openlocfilehash: e7cc2779661f4359c3c30fe76a387740f5f044f2
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39055580"
---
# <a name="tutorial-azure-active-directory-integration-with-xaitporter"></a>Samouczek: Integracja usługi Azure Active Directory z XaitPorter

W tym samouczku dowiesz się, jak zintegrować XaitPorter w usłudze Azure Active Directory (Azure AD).

Integrowanie XaitPorter z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do XaitPorter.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do XaitPorter (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą XaitPorter, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- XaitPorter logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie XaitPorter z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-xaitporter-from-the-gallery"></a>Dodawanie XaitPorter z galerii
Aby skonfigurować integrację XaitPorter w usłudze Azure AD, należy dodać XaitPorter z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać XaitPorter z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **XaitPorter**, wybierz opcję **XaitPorter** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![XaitPorter na liście wyników](./media/xaitporter-tutorial/tutorial_xaitporter_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą XaitPorter w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w XaitPorter do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w XaitPorter musi można ustanowić.

W XaitPorter, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą XaitPorter, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego XaitPorter](#create-a-xaitporter-test-user)**  — aby odpowiednikiem Britta Simon w XaitPorter połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji XaitPorter.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z XaitPorter, wykonaj następujące czynności:**

1. W witrynie Azure portal na **XaitPorter** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/xaitporter-tutorial/tutorial_xaitporter_samlbase.png)

3. Na **XaitPorter domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![XaitPorter domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/xaitporter-tutorial/tutorial_xaitporter_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<subdomain>.xaitporter.com/saml/login`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<subdomain>.xaitporter.com`

    > [!NOTE] 
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL logowania jednokrotnego i identyfikator. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta XaitPorter](https://www.xait.com/support/) do uzyskania tych wartości.
     
4. Na **certyfikat podpisywania SAML** sekcji, kliknij przycisk kopiowania, aby skopiować **adres Url metadanych Federacji aplikacji** i wklej go w Notatniku. 

    ![Link pobierania certyfikatu](./media/xaitporter-tutorial/tutorial_xaitporter_certificate.png) 

5. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/xaitporter-tutorial/tutorial_general_400.png)

6. Podaj **adresu IP** lub **adres Url metadanych Federacji aplikacji** do [SmartRecruiters zespołu pomocy technicznej](https://www.smartrecruiters.com/about-us/contact-us/), dzięki czemu XaitPorter można upewnić się, że adres IP jest dostępny z poziomu usługi Wystąpienie XaitPorter skonfigurowanie listy dozwolonych po ich stronie. 

7. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy XaitPorter.

8. Kliknij pozycję **administratora**.

    ![Konfigurowanie logowania jednokrotnego](./media/xaitporter-tutorial/user1.png)

9. Wybierz **Zarządzanie logowania jednokrotnego** z **konfiguracji systemu** listy rozwijanej.

    ![Konfigurowanie logowania jednokrotnego](./media/xaitporter-tutorial/user2.png)

10. W **Zarządzanie logowanie Jednokrotne** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/xaitporter-tutorial/user3.png)

    a. Wybierz **włączyć uwierzytelnianie rejestracji jednokrotnej**.

    b. W **ustawień dostawcy tożsamości** pola tekstowego, Wklej **adres Url metadanych Federacji aplikacji** skopiowanej w witrynie Azure portal, a następnie kliknij **pobrać**.

    c. Wybierz **Włącz Autocreation użytkowników**.

    d. Kliknij przycisk **OK**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/xaitporter-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/xaitporter-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/xaitporter-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/xaitporter-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-xaitporter-test-user"></a>Tworzenie użytkownika testowego XaitPorter

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w XaitPorter. Praca z [zespołem pomocy technicznej klienta XaitPorter](https://www.xait.com/support/) Aby dodać użytkowników na platformie XaitPorter. Użytkownicy muszą być tworzone i aktywowana, aby używać logowania jednokrotnego. 

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do XaitPorter.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon XaitPorter, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **XaitPorter**.

    ![Link XaitPorter na liście aplikacji](./media/xaitporter-tutorial/tutorial_xaitporter_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka XaitPorter w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji XaitPorter.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/xaitporter-tutorial/tutorial_general_01.png
[2]: ./media/xaitporter-tutorial/tutorial_general_02.png
[3]: ./media/xaitporter-tutorial/tutorial_general_03.png
[4]: ./media/xaitporter-tutorial/tutorial_general_04.png

[100]: ./media/xaitporter-tutorial/tutorial_general_100.png

[200]: ./media/xaitporter-tutorial/tutorial_general_200.png
[201]: ./media/xaitporter-tutorial/tutorial_general_201.png
[202]: ./media/xaitporter-tutorial/tutorial_general_202.png
[203]: ./media/xaitporter-tutorial/tutorial_general_203.png

