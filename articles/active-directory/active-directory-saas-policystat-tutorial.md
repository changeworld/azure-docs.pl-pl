---
title: 'Samouczek: Integracji Azure Active Directory z PolicyStat | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i PolicyStat.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: af5eb0f1-1c8e-4809-b0c4-8ccfb915ca42
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 0cd4cc4a622a8e23b98e06bb33d99755f247612c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-policystat"></a>Samouczek: Integracji Azure Active Directory z PolicyStat

Z tego samouczka dowiesz się integrowanie PolicyStat z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD PolicyStat zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do PolicyStat
- Umożliwia użytkownikom automatycznie pobrać zalogowane do PolicyStat (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z PolicyStat, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- PolicyStat logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie PolicyStat z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-policystat-from-the-gallery"></a>Dodawanie PolicyStat z galerii
Aby skonfigurować integrację usługi Azure AD PolicyStat, należy dodać PolicyStat z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać PolicyStat z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **PolicyStat**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_search.png)

5. W panelu wyników wybierz **PolicyStat**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z PolicyStat w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w PolicyStat jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w PolicyStat musi się.

W PolicyStat, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z PolicyStat, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego PolicyStat](#creating-a-policystat-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta PolicyStat połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji PolicyStat.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z PolicyStat, wykonaj następujące czynności:**

1. W portalu Azure na **PolicyStat** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_samlbase.png)

3. Na **PolicyStat domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<companyname>.policystat.com`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<companyname>.policystat.com/saml2/metadata/`

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL logowania i identyfikator, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej klienta PolicyStat](http://www.policystat.com/support/) uzyskać te wartości. 
 
4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_certificate.png) 

5. Celem tej sekcji jest przedstawiają sposób umożliwić użytkownikom uwierzytelnianie na PolicyStat do swojego konta w usłudze Azure AD przy użyciu federacyjnego na podstawie protokołu SAML.

    Aplikacja PolicyStat oczekuje potwierdzenia języka SAML w określonym formacie, musisz dodać mapowania atrybutu niestandardowego do Twojej **atrybuty tokenu SAML** konfiguracji.  

     Poniższy zrzut ekranu przedstawia przykład.

     ![Atrybuty](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_attribute.png "atrybutów")

6. Aby dodać mapowania wymaganego atrybutu, wykonaj następujące czynności:

    | Nazwa atrybutu    |   Wartość atrybutu |
    |------------------- | -------------------- |
    | Identyfikator UID | ExtractMailPrefix([mail]) |
    
    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_04.png)

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_addatribute.png)
    
    b. W **nazwa atrybutu** pole tekstowe, typ **uid**.

    c. W **wartość atrybutu** pole tekstowe, wybierz opcję **ExtractMailPrefix()**.    
   
    d. Z **poczty** listy, wybierz **User.mail**.
    
    e. Kliknij przycisk **Ok**

7. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-policystat-tutorial/tutorial_general_400.png)

8. W oknie przeglądarki innej witryny sieci web Zaloguj się do witryny firmy PolicyStat jako administrator.

9. Kliknij przycisk **Admin** , a następnie kliknij pozycję **konfiguracji rejestracji jednokrotnej** w lewym okienku nawigacji.
   
    ![Menu administratora](./media/active-directory-saas-policystat-tutorial/ic808633.png "Menu administratora")

10. W **Instalator** zaznacz **włączyć pojedynczego logowania jednokrotnego integracji**.
   
    ![Pojedynczy konfiguracji logowania jednokrotnego](./media/active-directory-saas-policystat-tutorial/ic808634.png "pojedynczy konfiguracji logowania jednokrotnego")

11. Kliknij przycisk **Konfigurowanie atrybutów**, a następnie w **Konfigurowanie atrybutów** sekcji, wykonaj następujące czynności:
   
    ![Pojedynczy konfiguracji logowania jednokrotnego](./media/active-directory-saas-policystat-tutorial/ic808635.png "pojedynczy konfiguracji logowania jednokrotnego")
   
    a. W **atrybutu Username** pole tekstowe, typ **uid**.

    b. W **atrybutu imię** pole tekstowe, typ **imię** użytkownika **Britta**.

    c. W **ostatniego atrybutu Name** pole tekstowe, typ **nazwisko** użytkownika **Simona**.

    d. W **atrybut poczty E-mail** pole tekstowe, typ **emailaddress** użytkownika **BrittaSimon@contoso.com**.

    e. Kliknij przycisk **zapisać zmiany**.

12. Kliknij przycisk **Your metadanych IDP**, a następnie w **Your metadanych IDP** sekcji, wykonaj następujące czynności:
   
    ![Pojedynczy konfiguracji logowania jednokrotnego](./media/active-directory-saas-policystat-tutorial/ic808636.png "pojedynczy konfiguracji logowania jednokrotnego")
   
    a. Otwórz plik metadanych pobranych, skopiuj zawartość, a następnie wklej go do **Your metadanych dostawcy tożsamości** pola tekstowego.

    b. Kliknij przycisk **zapisać zmiany**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-policystat-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-policystat-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-policystat-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-policystat-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-policystat-test-user"></a>Tworzenie użytkownika testowego PolicyStat

Aby włączyć użytkowników usługi Azure AD zalogować się do PolicyStat, musi być przygotowana do PolicyStat.  

PolicyStat obsługuje tylko w czasie Inicjowanie obsługi użytkowników. Oznacza to, że nie należy ręcznie dodać użytkowników do PolicyStat. Użytkownicy będą zostaną dodane automatycznie na ich pierwsze logowanie za pomocą logowania jednokrotnego.

>[!NOTE]
>Możesz użyć innych PolicyStat użytkownika konta tworzenia narzędzi lub interfejsów API dostarczonych przez PolicyStat do udostępnienia konta użytkownika usługi Azure AD.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu PolicyStat.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta PolicyStat, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **PolicyStat**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka PolicyStat w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji PolicyStat.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_203.png

