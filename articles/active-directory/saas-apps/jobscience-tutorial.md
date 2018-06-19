---
title: 'Samouczek: Integracji Azure Active Directory z Jobscience | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Jobscience.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 77282dcc-bbe2-4728-953d-adb4ab6a713b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 65fc899c73e127750935e3cd8c24a488870ac0ca
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35938608"
---
# <a name="tutorial-azure-active-directory-integration-with-jobscience"></a>Samouczek: Integracji Azure Active Directory z Jobscience

Z tego samouczka dowiesz się integrowanie Jobscience z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Jobscience zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Jobscience
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Jobscience (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Jobscience, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Jobscience logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna tutaj: [oferta wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Jobscience z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-jobscience-from-the-gallery"></a>Dodawanie Jobscience z galerii
Aby skonfigurować integrację usługi Azure AD Jobscience, należy dodać Jobscience z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Jobscience z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **Jobscience**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/jobscience-tutorial/tutorial_jobscience_search.png)

5. W panelu wyników wybierz **Jobscience**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/jobscience-tutorial/tutorial_jobscience_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji możesz skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Jobscience na podstawie użytkownika testowego, nazywany "Britta Simona".

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Jobscience jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Jobscience musi się.

W Jobscience, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Jobscience, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Jobscience](#creating-a-jobscience-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Jobscience połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Jobscience.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Jobscience, wykonaj następujące czynności:**

1. W portalu Azure na **Jobscience** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/jobscience-tutorial/tutorial_jobscience_samlbase.png)

3. Na **Jobscience domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/jobscience-tutorial/tutorial_jobscience_url.png)

    W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca:  `http://<company name>.my.salesforce.com`
    
    > [!NOTE] 
    > Ta wartość nie jest prawdziwe. Zaktualizuj tę wartość przy rzeczywisty adres URL logowania. Uzyskać tę wartość za pomocą [zespołem pomocy technicznej klienta Jobscience](https://www.jobscience.com/support) lub w profilu rejestracji Jednokrotnej zostanie utworzona, który znajduje się w dalszej części tego samouczka. 
 
4. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/jobscience-tutorial/tutorial_jobscience_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/jobscience-tutorial/tutorial_general_400.png)

6. Na **konfiguracji Jobscience** , kliknij przycisk **skonfigurować Jobscience** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adres URL, identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/jobscience-tutorial/tutorial_jobscience_configure.png) 

7. Zaloguj się do witryny firmy Jobscience jako administrator.

8. Przejdź do **Instalatora**.
   
   ![Instalator](./media/jobscience-tutorial/IC784358.png "Instalatora")

9. W okienku nawigacji po lewej stronie w **Administruj** , kliknij przycisk **Zarządzanie domenami** rozwiń sekcję powiązane, a następnie kliknij przycisk **Moje domeny** otworzyć **Moje domeny** strony. 
   
   ![Mojej domeny](./media/jobscience-tutorial/ic767825.png "mojej domeny")

10. Aby sprawdzić, czy domeny nie został skonfigurowany prawidłowo, upewnij się, że jest on "**krok 4 wdrożone dla użytkowników**" i zapoznaj się z "**Moje ustawienia domeny**".

    ![Wdrożonego dla użytkownika domeny](./media/jobscience-tutorial/ic784377.png "wdrożonego dla użytkownika domeny")

11. W witrynie firmy Jobscience kliknij przycisk **kontroli bezpieczeństwa**, a następnie kliknij przycisk **ustawień rejestracji jednokrotnej**.
    
    ![Opcje zabezpieczeń](./media/jobscience-tutorial/ic784364.png "środki zabezpieczające.")

12. W **ustawień rejestracji jednokrotnej** sekcji, wykonaj następujące czynności:
    
    ![Single Sign-On ustawienia](./media/jobscience-tutorial/ic781026.png "Single Sign-On ustawienia")
    
    a. Wybierz **SAML włączone**.

    b. Kliknij przycisk **Nowy**.

13. Na **SAML pojedynczego logowania jednokrotnego ustawienie Edytuj** okna dialogowego, wykonaj następujące czynności:
    
    ![SAML pojedynczy znak na ustawienie](./media/jobscience-tutorial/ic784365.png "SAML pojedynczy znak na ustawienie")
    
    a. W **nazwa** tekstowym, wpisz nazwę dla danej konfiguracji.

    b. W **wystawcy** pole tekstowe, Wklej wartość **identyfikator jednostki SAML**, które zostały skopiowane z portalu Azure.

    c. W **identyfikator jednostki** pole tekstowe, typ `https://salesforce-jobscience.com`

    d. Kliknij przycisk **Przeglądaj** można przekazać certyfikatu usługi Azure AD.

    e. Jako **typ tożsamości SAML**, wybierz pozycję **potwierdzenia zawiera identyfikator federacji z obiektu użytkownika**.

    f. Jako **lokalizacji tożsamości SAML**, wybierz pozycję **jest tożsamość w elemencie NameIdentfier instrukcji podmiotu**.

    g. W **adresu URL logowania do dostawcy tożsamości** pole tekstowe, Wklej wartość **SAML pojedynczy znak na adres URL usługi**, które zostały skopiowane z portalu Azure.

    h. W **adres URL wylogowania dostawcy tożsamości** pole tekstowe, Wklej wartość **Sign-Out adres URL**, które zostały skopiowane z portalu Azure.

    i. Kliknij pozycję **Zapisz**.

14. W okienku nawigacji po lewej stronie w **Administruj** , kliknij przycisk **Zarządzanie domenami** rozwiń sekcję powiązane, a następnie kliknij przycisk **Moje domeny** otworzyć **Moje domeny** strony. 
    
    ![Mojej domeny](./media/jobscience-tutorial/ic767825.png "mojej domeny")

15. Na **Moje domeny** strony w **znakowanie strony logowania** kliknij **Edytuj**.
    
    ![Znakowanie strony logowania](./media/jobscience-tutorial/ic767826.png "znakowanie strony logowania")

16. Na **znakowanie strony logowania** strony w **usługi uwierzytelniania** sekcji Nazwa Twojej **ustawienia logowania jednokrotnego SAML** jest wyświetlany. Wybierz go, a następnie kliknij przycisk **zapisać**.
    
    ![Znakowanie strony logowania](./media/jobscience-tutorial/ic784366.png "znakowanie strony logowania")

17. Aby uzyskać PS zainicjować funkcji logowania jednokrotnego kliknij adres URL logowania na **ustawień rejestracji jednokrotnej** w **kontroli bezpieczeństwa** części menu.

    ![Opcje zabezpieczeń](./media/jobscience-tutorial/ic784368.png "środki zabezpieczające.")
    
    Kliknij profil rejestracji Jednokrotnej, utworzony w poprzednim kroku. Ta strona zawiera rejestracji jednokrotnej na adres URL dla Twojej firmy (na przykład [ https://companyname.my.salesforce.com?so=companyid ](https://companyname.my.salesforce.com?so=companyid).    

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/jobscience-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/jobscience-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/jobscience-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/jobscience-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-jobscience-test-user"></a>Tworzenie użytkownika testowego Jobscience

Aby umożliwić użytkownikom zalogować się do Jobscience usługi Azure AD, musi być przygotowana do Jobscience. W przypadku Jobscience Inicjowanie obsługi to zadanie ręczne.

>[!NOTE]
>Możesz użyć innych Jobscience użytkownika konta tworzenia narzędzi lub interfejsów API dostarczonych przez Jobscience do świadczenia usługi Azure Active Directory kont użytkowników.
>  
        
**Aby skonfigurować, inicjowanie obsługi użytkowników, wykonaj następujące czynności:**

1. Zaloguj się do Twojego **Jobscience** witryny firmy jako administrator.

2. Przejdź do instalacji.
   
   ![Instalator](./media/jobscience-tutorial/ic784358.png "Instalatora")
3. Przejdź do **Zarządzanie użytkownikami \> użytkowników**.
   
   ![Użytkownicy](./media/jobscience-tutorial/ic784369.png "użytkowników")
4. Kliknij przycisk **nowego użytkownika**.
   
   ![Wszyscy użytkownicy](./media/jobscience-tutorial/ic784370.png "wszyscy użytkownicy")
5. Na **Edytowanie użytkownika** okna dialogowego, wykonaj następujące czynności:
   
   ![Edycja użytkownika](./media/jobscience-tutorial/ic784371.png "Edycja użytkownika")
   
   a. W **imię** tekstowym, wpisz imię użytkownika, takich jak Britta.
   
   b. W **nazwisko** tekstowym, wpisz nazwisko użytkownika, takich jak Simona.
   
   c. W **Alias** tekstowym, wpisz nazwę użytkownika, takich jak brittas aliasu.

   d. W **E-mail** tekstowym, wpisz adres e-mail użytkownika, takich jak Brittasimon@contoso.com.

   e. W **nazwy użytkownika** tekstowym, wpisz nazwę użytkownika użytkownika, takich jak Brittasimon@contoso.com.

   f. W **pseudonim** tekstowym, wpisz nazwę użytkownika, takich jak Simona nick.

   g. Kliknij pozycję **Zapisz**.

    
> [!NOTE]
> Właściciel konta usługi Azure Active Directory otrzymuje wiadomość e-mail i następuje łącze, aby potwierdzić swoje konto, zanim staje się aktywny.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu Jobscience.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta Jobscience, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Jobscience**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/jobscience-tutorial/tutorial_jobscience_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Jobscience w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji Jobscience.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/jobscience-tutorial/tutorial_general_01.png
[2]: ./media/jobscience-tutorial/tutorial_general_02.png
[3]: ./media/jobscience-tutorial/tutorial_general_03.png
[4]: ./media/jobscience-tutorial/tutorial_general_04.png

[100]: ./media/jobscience-tutorial/tutorial_general_100.png

[200]: ./media/jobscience-tutorial/tutorial_general_200.png
[201]: ./media/jobscience-tutorial/tutorial_general_201.png
[202]: ./media/jobscience-tutorial/tutorial_general_202.png
[203]: ./media/jobscience-tutorial/tutorial_general_203.png

