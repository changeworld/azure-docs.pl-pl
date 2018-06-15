---
title: 'Samouczek: Integracji Azure Active Directory z Envi MMIS | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Envi MMIS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ab89f8ee-2507-4625-94bc-b24ef3d5e006
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: jeedes
ms.openlocfilehash: 0d338f7f56e668c25378403e88a6532b8b8573c5
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34344273"
---
# <a name="tutorial-azure-active-directory-integration-with-envi-mmis"></a>Samouczek: Integracji Azure Active Directory z Envi MMIS

Z tego samouczka dowiesz się integrowanie Envi MMIS z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Envi MMIS zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Envi MMIS.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do MMIS Envi (logowanie jednokrotne) z konta usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Envi MMIS, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Envi MMIS logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Envi MMIS z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-envi-mmis-from-the-gallery"></a>Dodawanie Envi MMIS z galerii
Aby skonfigurować integrację usługi Azure AD Envi MMIS, należy dodać Envi MMIS z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Envi MMIS z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Envi MMIS**, wybierz pozycję **Envi MMIS** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![MMIS Envi na liście wyników](./media/active-directory-saas-envimmis-tutorial/tutorial_envimmis_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji możesz skonfigurować i test usługi Azure AD rejestracji jednokrotnej z Envi MMIS w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Envi MMIS jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Envi MMIS musi się.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Envi MMIS, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Envi MMIS](#create-an-envi-mmis-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta MMIS Envi, połączonej z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Envi MMIS.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Envi MMIS, wykonaj następujące czynności:**

1. W portalu Azure na **Envi MMIS** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/active-directory-saas-envimmis-tutorial/tutorial_envimmis_samlbase.png)

3. Na **Envi MMIS domeny i adres URL** sekcji, wykonaj następujące kroki, aby skonfigurować aplikację w **IDP** inicjowane tryb:

    ![Adresy URL i domeny MMIS Envi pojedynczy informacje logowania jednokrotnego](./media/active-directory-saas-envimmis-tutorial/tutorial_envimmis_url.png)

    a. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://www.<CUSTOMER DOMAIN>.com/Account`

    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://www.<CUSTOMER DOMAIN>.com/Account/Acs`

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** inicjowane tryb:

    ![Adresy URL i domeny MMIS Envi pojedynczy informacje logowania jednokrotnego](./media/active-directory-saas-envimmis-tutorial/tutorial_envimmis_url1.png)

    W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://www.<CUSTOMER DOMAIN>.com/Account`
     
    > [!NOTE]
    > Wartości te nie są prawdziwe. Rzeczywisty identyfikator, adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej klienta MMIS Envi](mailto:support@ioscorp.com) uzyskać te wartości.

5. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/active-directory-saas-envimmis-tutorial/tutorial_envimmis_certificate.png) 

6. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-envimmis-tutorial/tutorial_general_400.png)

7. W oknie przeglądarki innej witryny sieci web Zaloguj się do witryny Envi MMIS jako administrator.

8. Polecenie **Moje domeny** kartę.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-envimmis-tutorial/configure1.png)

9. Kliknij pozycję **Edytuj**.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-envimmis-tutorial/configure2.png)

10. Wybierz **zdalnego uwierzytelnianie** pole wyboru, a następnie wybierz **przekierowywanie HTTP** z **typ uwierzytelniania** listy rozwijanej.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-envimmis-tutorial/configure3.png)

11. Wybierz **zasobów** a następnie kliknij pozycję **przekazać metadanych**.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-envimmis-tutorial/configure4.png)

12. W **przekazać metadanych** menu podręczne, wykonaj następujące czynności:

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-envimmis-tutorial/configure5.png)

    a. Wybierz **pliku** opcję **przekazać z** listy rozwijanej.

    b. Przekaż plik metadanych pobranych z portalu Azure, wybierając **wybierz ikonę pliku**.

    c. Kliknij przycisk **OK**.

13. Po przekazaniu pliku metadanych pobranych pola zostanie wypełniony automatycznie. Kliknij przycisk **aktualizacji**

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-envimmis-tutorial/configure6.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/active-directory-saas-envimmis-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/active-directory-saas-envimmis-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/active-directory-saas-envimmis-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/active-directory-saas-envimmis-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-an-envi-mmis-test-user"></a>Tworzenie użytkownika testowego Envi MMIS

Aby umożliwić użytkownikom usługi Azure AD zalogować się do Envi MMIS, muszą mieć przydzielone do Envi MMIS.  
W przypadku Envi MMIS Inicjowanie obsługi to zadanie ręczne.

**Aby udostępnić konta użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy Envi MMIS jako administrator.

2. Polecenie **listy użytkowników** kartę.

    ![Dodawanie pracownika](./media/active-directory-saas-envimmis-tutorial/user1.png)

3. Kliknij przycisk **Dodaj użytkownika** przycisku.

    ![Dodawanie pracownika](./media/active-directory-saas-envimmis-tutorial/user2.png)

4. W **Dodaj użytkownika** sekcji, wykonaj następujące czynności:

    ![Dodawanie pracownika](./media/active-directory-saas-envimmis-tutorial/user3.png)

    a. W **nazwy użytkownika** pole tekstowe, typu nazwa użytkownika konta Simona Britta, takich jak **brittasimon@contoso.com**.
    
    b. W **imię** tekstowym, wpisz nazwę pierwszego BrittaSimon, takich jak **Britta**.

    c. W **nazwisko** tekstowym, wpisz nazwisko BrittaSimon, takich jak **Simona**.

    d. Wprowadź nazwę użytkownika w **tytuł** pola tekstowego.
    
    e. W **adres E-mail** pole tekstowe, typ adresu e-mail konta Simona Britta, takich jak **brittasimon@contoso.com**.

    f. W **nazwę użytkownika logowania jednokrotnego** pole tekstowe, typu nazwa użytkownika konta Simona Britta, takich jak **brittasimon@contoso.com**.

    g. Kliknij pozycję **Zapisz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu Envi MMIS.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta Envi MMIS, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Envi MMIS**.

    ![Łącze Envi MMIS na liście aplikacji](./media/active-directory-saas-envimmis-tutorial/tutorial_envimmis_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Envi MMIS w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji Envi MMIS.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-envimmis-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-envimmis-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-envimmis-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-envimmis-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-envimmis-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-envimmis-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-envimmis-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-envimmis-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-envimmis-tutorial/tutorial_general_203.png

