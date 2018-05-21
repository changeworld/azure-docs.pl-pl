---
title: 'Samouczek: Integracji Azure Active Directory z Veracode | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Veracode.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 4fe78050-cb6d-4db9-96ec-58cc0779167f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 774cd70f7cc97be8b050cf6ac31063c150b2f657
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-veracode"></a>Samouczek: Integracji Azure Active Directory z Veracode

Z tego samouczka dowiesz się integrowanie Veracode z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Veracode zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Veracode.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Veracode (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Veracode, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Veracode logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodaj Veracode z galerii
2. Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

## <a name="add-veracode-from-the-gallery"></a>Dodaj Veracode z galerii
Aby skonfigurować integrację usługi Azure AD Veracode, należy dodać Veracode z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Veracode z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Veracode**, wybierz pozycję **Veracode** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Veracode na liście wyników](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Veracode w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Veracode jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Veracode musi się.

W Veracode, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Veracode, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Veracode](#create-a-veracode-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Veracode połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Veracode.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Veracode, wykonaj następujące czynności:**

1. W portalu Azure na **Veracode** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_samlbase.png)

3. Na **Veracode domeny i adres URL** sekcji, użytkownik nie trzeba wykonywać żadnych czynności, jak aplikacja już jest wstępna Integracja z usługą Azure. 

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_url.png)

4. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_certificate.png) 

5. Celem tej sekcji jest przedstawiają sposób umożliwić użytkownikom uwierzytelnianie na Veracode do swojego konta w usłudze Azure AD przy użyciu federacyjnego na podstawie protokołu SAML.

    Aplikacja Veracode oczekuje potwierdzenia języka SAML w określonym formacie, musisz dodać mapowania atrybutu niestandardowego do Twojej **atrybuty tokenu saml** konfiguracji. Poniższy zrzut ekranu przedstawia przykład tego.
    
    ![Atrybuty](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_attr.png "atrybutów")

6. Aby dodać mapowania wymaganego atrybutu, wykonaj następujące czynności:

    | Nazwa atrybutu | Wartość atrybutu |
    |--- |--- |
    | Imię |User.givenName |
    | nazwisko |User.surname |
    | wyślij wiadomość e-mail |User.mail |
    
    a. Dla każdego wiersza danych w tabeli powyżej, kliknij przycisk **Dodaj atrybut użytkownika**.
    
    ![Atrybuty](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_addattr.png "atrybutów")
    
    ![Atrybuty](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_addattr1.png "atrybutów")
    
    b. W **nazwa atrybutu** tekstowym, wpisz nazwę atrybut wyświetlany dla danego wiersza.
    
    c. W **wartość atrybutu** pole tekstowe, wybierz wartość atrybutu wyświetlany dla danego wiersza.
    
    d. Kliknij przycisk **OK**.

7. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-veracode-tutorial/tutorial_general_400.png)

8. Na **konfiguracji Veracode** , kliknij przycisk **skonfigurować Veracode** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki SAML** z **sekcji krótkimi opisami.**

    ![Konfiguracja Veracode](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_configure.png) 

9. W oknie przeglądarki innej witryny sieci web Zaloguj się do witryny firmy Veracode jako administrator.

10. W menu u góry kliknij **ustawienia**, a następnie kliknij przycisk **Admin**.
   
    ![Administracja](./media/active-directory-saas-veracode-tutorial/ic802911.png "administracji")

11. Kliknij przycisk **SAML** kartę.

12. W **ustawienia SAML organizacji** sekcji, wykonaj następujące czynności:
   
    ![Administracja](./media/active-directory-saas-veracode-tutorial/ic802912.png "administracji")
   
    a.  W **wystawcy** pole tekstowe, Wklej wartość **identyfikator jednostki SAML** którego została skopiowana z portalu Azure.
    
    b. Aby przekazać certyfikat pobrany z portalu Azure, kliknij przycisk **wybierz plik**.
   
    c. Wybierz **umożliwić samodzielną rejestrację**.

13. W **ustawień rejestracji samoobsługowego** sekcji, wykonaj następujące czynności, a następnie kliknij przycisk **zapisać**:
   
    ![Administracja](./media/active-directory-saas-veracode-tutorial/ic802913.png "administracji")
   
    a. Jako **nowe Aktywacja użytkownika**, wybierz pozycję **wymagane uaktywnienie nr**.
   
    b. Jako **aktualizacje danych użytkownika**, wybierz pozycję **dane użytkownika Veracode preferencji**.
   
    c. Aby uzyskać **szczegółów atrybutów SAML**, wykonaj następujące czynności:
      * **Role użytkowników**
      * **Administrator zasad**
      * **osoba dokonująca przeglądu**
      * **Realizacji zabezpieczeń**
      * **Główna programu SMS**
      * **Obiekt przesyłający**
      * **Twórcy**
      * **Wszystkie typy skanowania**
      * **Członkostwo zespołu**
      * **Domyślny zespół**

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/active-directory-saas-veracode-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/active-directory-saas-veracode-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/active-directory-saas-veracode-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/active-directory-saas-veracode-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-veracode-test-user"></a>Tworzenie użytkownika testowego Veracode
Aby włączyć użytkowników usługi Azure AD zalogować się do Veracode, musi być przygotowana do Veracode. W przypadku Veracode Inicjowanie obsługi to zadanie automatyczne. Nie ma elementu akcji dla Ciebie. Użytkownicy są tworzone automatycznie w razie potrzeby podczas pierwszej pojedynczego logowania jednokrotnego próby.

> [!NOTE]
> Możesz użyć innych Veracode użytkownika konta tworzenia narzędzi lub interfejsów API dostarczonych przez Veracode do udostępnienia konta użytkownika usługi Azure AD.
> 

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu Veracode.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta Veracode, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Veracode**.

    ![Łącze Veracode na liście aplikacji](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Veracode w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji Veracode.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_203.png

