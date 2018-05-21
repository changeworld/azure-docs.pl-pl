---
title: 'Samouczek: Integracji Azure Active Directory z Bonusly | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Bonusly.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 29fea32a-fa20-47b2-9e24-26feb47b0ae6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: 8133ec95bf0cbf6a9ce4ca1ad224d03ac593c940
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>Samouczek: Integracji Azure Active Directory z Bonusly

Z tego samouczka dowiesz się integrowanie Bonusly z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Bonusly zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Bonusly
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Bonusly (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Bonusly, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Bonusly logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Bonusly z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-bonusly-from-the-gallery"></a>Dodawanie Bonusly z galerii
Aby skonfigurować integrację usługi Azure AD Bonusly, należy dodać Bonusly z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Bonusly z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Bonusly**, wybierz pozycję **Bonusly** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Bonusly na liście wyników](./media/active-directory-saas-bonus-tutorial/tutorial_bonusly_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej
W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Bonusly w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Bonusly jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Bonusly musi się.

W Bonusly, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Bonusly, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Bonusly](#create-a-bonusly-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Bonusly połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w Bonusly aplikacji.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Bonusly, wykonaj następujące czynności:**

1. W portalu Azure na **Bonusly** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/active-directory-saas-bonus-tutorial/tutorial_bonusly_samlbase.png)

3. Na **Bonusly domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Adresy URL i domeny bonusly pojedynczy informacje logowania jednokrotnego](./media/active-directory-saas-bonus-tutorial/tutorial_bonusly_url.png)

    W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://Bonus.ly/saml/<tenant-name>`

    > [!NOTE] 
    > Wartość nie jest prawdziwe. Zaktualizuj tę wartość do rzeczywistego adresu URL odpowiedzi. Skontaktuj się z [zespołem pomocy technicznej Bonusly](https://Bonusly/contact) można uzyskać wartość.
 
4. Na **certyfikat podpisywania SAML** sekcji, skopiuj **odcisk PALCA** wartości z certyfikatu.

    ![Łącze pobierania certyfikatu](./media/active-directory-saas-bonus-tutorial/tutorial_bonusly_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-bonus-tutorial/tutorial_general_400.png)

6. Na **Bonusly konfiguracji** kliknij **skonfigurować Bonusly** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Bonusly konfiguracji](./media/active-directory-saas-bonus-tutorial/tutorial_bonusly_configure.png) 

7. W oknie innej przeglądarki, zaloguj się do Twojego **Bonusly** dzierżawy.

8. Na pasku narzędzi u góry kliknij **ustawienia**, a następnie wybierz **integracji i aplikacje**.
   
    ![Sekcja społecznościowych bonusly](./media/active-directory-saas-bonus-tutorial/ic773686.png "Bonusly")
9. W obszarze **rejestracji jednokrotnej**, wybierz pozycję **SAML**.

10. Na **SAML** okna dialogowego strony, należy wykonać następujące czynności:
   
    ![Strony okna dialogowego Saml bonusly](./media/active-directory-saas-bonus-tutorial/ic773687.png "Bonusly")
   
    a. W **logowania jednokrotnego IdP docelowy adres URL** pole tekstowe, Wklej wartość **SAML pojedynczy znak na adres URL usługi**, które zostały skopiowane z portalu Azure.
   
    b. W **wystawcy IdP** pole tekstowe, Wklej wartość **identyfikator jednostki SAML**, które zostały skopiowane z portalu Azure. 

    c. W **adres URL logowania IdP** pole tekstowe, Wklej wartość **SAML pojedynczy znak na adres URL usługi**, które zostały skopiowane z portalu Azure.

    d. Wklej **odcisk palca** wartość skopiowany z portalu Azure do **odcisk palca certyfikatu** pola tekstowego.
   
11. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Przycisk usługi Azure Active Directory](./media/active-directory-saas-bonus-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/active-directory-saas-bonus-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Przycisk Dodaj](./media/active-directory-saas-bonus-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Okno dialogowe użytkownika](./media/active-directory-saas-bonus-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-bonusly-test-user"></a>Tworzenie użytkownika testowego Bonusly

Aby umożliwić użytkownikom zalogować się do Bonusly usługi Azure AD, musi być przygotowana do Bonusly. W przypadku Bonusly Inicjowanie obsługi to zadanie ręczne.

>[!NOTE]
>Możesz użyć innych Bonusly użytkownika konta tworzenia narzędzi lub interfejsów API dostarczonych przez Bonusly do kont użytkowników usługi AAD.
>  

**Aby skonfigurować, inicjowanie obsługi użytkowników, wykonaj następujące czynności:**

1. W oknie przeglądarki sieci web należy zalogować się do dzierżawy Bonusly.

2. Kliknij przycisk **ustawienia**.
 
    ![Ustawienia](./media/active-directory-saas-bonus-tutorial/ic781041.png "ustawienia")

3. Kliknij przycisk **użytkowników i dodatki** kartę.
   
    ![Użytkownicy i dodatki](./media/active-directory-saas-bonus-tutorial/ic781042.png "użytkowników i dodatki")

4. Kliknij przycisk **Zarządzanie użytkownikami**.
   
    ![Zarządzaj użytkownikami](./media/active-directory-saas-bonus-tutorial/ic781043.png "Zarządzanie użytkownikami")

5. Kliknij przycisk **dodać użytkownika**.
   
    ![Dodaj użytkownika](./media/active-directory-saas-bonus-tutorial/ic781044.png "Dodaj użytkownika")

6. Na **Dodaj użytkownika** okna dialogowego, wykonaj następujące czynności:
   
    ![Dodaj użytkownika](./media/active-directory-saas-bonus-tutorial/ic781045.png "Dodaj użytkownika")  

    a. W **imię** pole tekstowe, wprowadź imię użytkownika, takich jak **Britta**.

    b. W **nazwisko** pole tekstowe, wprowadź nazwisko użytkownika, takich jak **Simona**.
 
    c. W **E-mail** pole tekstowe, wprowadź adres e-mail użytkownika, takich jak **brittasimon@contoso.com**.

    d. Kliknij pozycję **Zapisz**.
   
     >[!NOTE]
     >Właściciel konta usługi Azure AD odbiera wiadomość e-mail zawierającą łącze do potwierdzenia konta, zanim staje się aktywny.
     >  

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu Bonusly.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta Bonusly, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Bonusly**.

    ![Bonusly łącza na liście aplikacji](./media/active-directory-saas-bonus-tutorial/tutorial_bonusly_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Bonusly w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane Bonusly aplikacji.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_203.png

