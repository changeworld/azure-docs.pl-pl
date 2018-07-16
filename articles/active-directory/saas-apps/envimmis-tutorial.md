---
title: 'Samouczek: Integracja usługi Azure Active Directory z Envi MMIS | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Envi MMIS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ab89f8ee-2507-4625-94bc-b24ef3d5e006
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: jeedes
ms.openlocfilehash: 70066f1c29849b77c67710eb908ef2a340cdc45f
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39047664"
---
# <a name="tutorial-azure-active-directory-integration-with-envi-mmis"></a>Samouczek: Integracja usługi Azure Active Directory z Envi MMIS

W tym samouczku dowiesz się, jak zintegrować Envi MMIS w usłudze Azure Active Directory (Azure AD).

Integrowanie Envi MMIS z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Envi MMIS.
- Użytkowników, aby automatycznie uzyskać zalogowanych do MMIS Envi (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Envi MMIS, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Envi MMIS logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Envi MMIS z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-envi-mmis-from-the-gallery"></a>Dodawanie Envi MMIS z galerii
Aby skonfigurować integrację Envi MMIS w usłudze Azure AD, należy dodać Envi MMIS z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Envi MMIS z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Envi MMIS**, wybierz opcję **Envi MMIS** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![MMIS Envi na liście wyników](./media/envimmis-tutorial/tutorial_envimmis_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji można skonfigurować, i test usługi Azure AD logowanie jednokrotne za pomocą Envi MMIS oparte na użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Envi MMIS do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w Envi MMIS musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Envi MMIS, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego Envi MMIS](#create-an-envi-mmis-test-user)**  — aby odpowiednikiem Britta Simon w MMIS Envi, połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Envi MMIS.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Envi MMIS, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Envi MMIS** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/envimmis-tutorial/tutorial_envimmis_samlbase.png)

3. Na **Envi MMIS domena i adresy URL** sekcji, wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![Envi MMIS domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/envimmis-tutorial/tutorial_envimmis_url.png)

    a. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://www.<CUSTOMER DOMAIN>.com/Account`

    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://www.<CUSTOMER DOMAIN>.com/Account/Acs`

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![Envi MMIS domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/envimmis-tutorial/tutorial_envimmis_url1.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://www.<CUSTOMER DOMAIN>.com/Account`
     
    > [!NOTE]
    > Te wartości są prawdziwe. Rzeczywisty identyfikator, adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta MMIS Envi](mailto:support@ioscorp.com) do uzyskania tych wartości.

5. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/envimmis-tutorial/tutorial_envimmis_certificate.png) 

6. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/envimmis-tutorial/tutorial_general_400.png)

7. W oknie przeglądarki internetowej innej Zaloguj się do witryny Envi MMIS, jako administrator.

8. Kliknij pozycję **Moja domena** kartę.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/envimmis-tutorial/configure1.png)

9. Kliknij pozycję **Edytuj**.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/envimmis-tutorial/configure2.png)

10. Wybierz **zdalne uwierzytelnianie** pole wyboru, a następnie wybierz **przekierowania HTTP** z **typ uwierzytelniania** listy rozwijanej.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/envimmis-tutorial/configure3.png)

11. Wybierz **zasobów** kartę, a następnie kliknij przycisk **Przekazywanie metadanych**.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/envimmis-tutorial/configure4.png)

12. W **Przekazywanie metadanych** okna podręcznego, wykonaj następujące czynności:

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/envimmis-tutorial/configure5.png)

    a. Wybierz **pliku** opcję **przekazywania z** listy rozwijanej.

    b. Przekaż plik metadanych pobranego z witryny Azure portal, wybierając **wybierz ikonę pliku**.

    c. Kliknij przycisk **OK**.

13. Po przekazaniu pliku pobranego metadanych pola zostanie wypełniony automatycznie. Kliknij przycisk **aktualizacji**

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/envimmis-tutorial/configure6.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/envimmis-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/envimmis-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/envimmis-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/envimmis-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-an-envi-mmis-test-user"></a>Tworzenie użytkownika testowego Envi MMIS

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do Envi MMIS, musi być obsługiwana w Envi MMIS.  
W przypadku Envi MMIS aprowizacji to zadanie ręczne.

**Aby udostępnić konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy Envi MMIS jako administrator.

2. Kliknij pozycję **listy użytkowników** kartę.

    ![Dodawanie pracownika](./media/envimmis-tutorial/user1.png)

3. Kliknij przycisk **Dodaj użytkownika** przycisku.

    ![Dodawanie pracownika](./media/envimmis-tutorial/user2.png)

4. W **Dodaj użytkownika** sekcji, wykonaj następujące czynności:

    ![Dodawanie pracownika](./media/envimmis-tutorial/user3.png)

    a. W **nazwa_użytkownika** pola tekstowego, typ nazwy użytkownika konta Britta Simon, takich jak **brittasimon@contoso.com**.
    
    b. W **imię** polu tekstowym wpisz nazwę pierwszego BrittaSimon, takich jak **Britta**.

    c. W **nazwisko** polu tekstowym wpisz nazwisko BrittaSimon, takich jak **Simon**.

    d. Wprowadź tytuł dla użytkownika w **tytuł** pola tekstowego.
    
    e. W **adres E-mail** polu tekstowym wpisz adres e-mail konta Britta Simon, takich jak **brittasimon@contoso.com**.

    f. W **nazwa użytkownika logowania jednokrotnego** pola tekstowego, typ nazwy użytkownika konta Britta Simon, takich jak **brittasimon@contoso.com**.

    g. Kliknij pozycję **Zapisz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do Envi MMIS.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon Envi MMIS, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **Envi MMIS**.

    ![Łącze Envi MMIS na liście aplikacji](./media/envimmis-tutorial/tutorial_envimmis_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Envi MMIS w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Envi MMIS.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/envimmis-tutorial/tutorial_general_01.png
[2]: ./media/envimmis-tutorial/tutorial_general_02.png
[3]: ./media/envimmis-tutorial/tutorial_general_03.png
[4]: ./media/envimmis-tutorial/tutorial_general_04.png

[100]: ./media/envimmis-tutorial/tutorial_general_100.png

[200]: ./media/envimmis-tutorial/tutorial_general_200.png
[201]: ./media/envimmis-tutorial/tutorial_general_201.png
[202]: ./media/envimmis-tutorial/tutorial_general_202.png
[203]: ./media/envimmis-tutorial/tutorial_general_203.png

