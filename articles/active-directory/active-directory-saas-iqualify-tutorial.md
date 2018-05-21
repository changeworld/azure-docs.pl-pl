---
title: 'Samouczek: Integracji Azure Active Directory z iQualify LMS | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i iQualify LMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 8a3caaff-dd8d-4afd-badf-a0fd60db3d2c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: jeedes
ms.openlocfilehash: 341f2175d283c08496534f3ac0d946dd8671f63c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-iqualify-lms"></a>Samouczek: Integracji Azure Active Directory z iQualify LMS

Z tego samouczka dowiesz się integrowanie iQualify LMS w usłudze Azure Active Directory (Azure AD).

Integracja z usługą Azure AD iQualify LMS zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do iQualify LMS.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do iQualify LMS (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z iQualify LMS, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- IQualify LMS logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie iQualify LMS z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-iqualify-lms-from-the-gallery"></a>Dodawanie iQualify LMS z galerii
Aby skonfigurować integrację usługi Azure AD iQualify LMS, należy dodać iQualify LMS z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać iQualify LMS z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **iQualify LMS**, wybierz pozycję **iQualify LMS** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![iQualify LMS na liście wyników](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji możesz skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z iQualify LMS oparta na koncie użytkownika testu o nazwie "Britta Simona".

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, jaki użytkownik odpowiednikiem w iQualify LMS jest użytkownikiem w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w iQualify LMS musi się.

W iQualify LMS, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z iQualify LMS, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego LMS iQualify](#create-an-iqualify-lms-test-user)**  — mają odpowiednika Simona Britta w iQualify LMS połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji LMS iQualify.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z iQualify LMS, wykonaj następujące czynności:**

1. W portalu Azure na **iQualify LMS** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_samlbase.png)

3. Na **iQualify LMS domeny i adres URL** sekcji, wykonaj następujące kroki, aby skonfigurować aplikację w trybie IDP inicjowane:

    ![informacje o iQualify LMS domeny i adres URL logowania jednokrotnego](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_url.png)

    a. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: 
    | |
    |--|--|
    | Środowisku produkcyjnym: `https://<yourorg>.iqualify.com/`|
    | Środowisko testowe: `https://<yourorg>.iqualify.io`|
    
    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: 
    | |
    |--|--|
    | Środowisku produkcyjnym: `https://<yourorg>.iqualify.com/auth/saml2/callback` |
    | Środowisko testowe: `https://<yourorg>.iqualify.io/auth/saml2/callback` |

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** inicjowane tryb:

    ![informacje o iQualify LMS domeny i adres URL logowania jednokrotnego](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_url1.png)

    W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca:
    | |
    |--|--|
    | Środowisku produkcyjnym: `https://<yourorg>.iqualify.com/login` |
    | Środowisko testowe: `https://<yourorg>.iqualify.io/login` |
     
    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty identyfikator, adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej klienta LMS iQualify](https://www.iqualify.com) uzyskać te wartości. 

5. Aplikacja LMS iQualify oczekuje potwierdzenia Security (Assertion Markup Language SAML) do wyświetlenia w określonym formacie. Skonfiguruj oświadczenia i zarządzanie nimi wartości atrybutów w **atrybuty użytkownika** części strony iQualify integracji aplikacji, jak pokazano na poniższym zrzucie ekranu:
    
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-iqualify-tutorial/atb.png)

6. W **atrybuty użytkownika** sekcji na **logowanie jednokrotne** okna dialogowego, wykonaj następujące kroki dla każdego wiersza w tabeli poniżej:
    
    | Nazwa atrybutu | Wartość atrybutu |
    | --- | --- |    
    | wyślij wiadomość e-mail | user.userprincipalname |
    | first_name | user.givenname |
    | last_name | user.surname |
    | person_id | "atrybutu" | 

    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-iqualify-tutorial/atb2.png)

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-iqualify-tutorial/atb3.png)
    
    b. W **nazwa** tekstowym, wpisz nazwę atrybut wyświetlany dla danego wiersza.
    
    c. Z **wartość** listy, wpisz wartość atrybutu wyświetlany dla danego wiersza.
    
    d. Kliknij przycisk **Ok**

    e. Powtórz kroki od "a" do "d" dla następnego wierszy tabeli. 

    > [!Note]
    > Powtórzenie kroków "a" do "d" dla **person_id** atrybutu **opcjonalne**

7. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base 64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_certificate.png) 

8. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-iqualify-tutorial/tutorial_general_400.png)
    
9. Na **iQualify konfiguracji LMS** , kliknij przycisk **skonfigurować iQualify LMS** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adresu URL i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![iQualify LMS konfiguracji](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_configure.png) 

10.  Otwórz nowe okno przeglądarki, a następnie zaloguj się do środowiska iQualify jako administrator.

11. Gdy użytkownik jest zalogowany, kliknij swój awatar u góry po prawej, a następnie kliknij przycisk **"Ustawienia konta."**

    ![Ustawienia konta](./media/active-directory-saas-iqualify-tutorial/setting1.png) 
12. W obszarze Ustawienia konta, kliknij na Wstążce menu po lewej stronie, a następnie kliknij **"Integracji."**
    
    ![INTEGRACJI](./media/active-directory-saas-iqualify-tutorial/setting2.png)

13. W obszarze integracji, kliknij na **SAML** ikony.

    ![Ikona SAML](./media/active-directory-saas-iqualify-tutorial/setting3.png)

14. W **ustawienia uwierzytelniania SAML** okna dialogowego wykonaj następujące czynności:

    ![Ustawienia uwierzytelniania SAML](./media/active-directory-saas-iqualify-tutorial/setting4.png)

    a. W **adres URL SAML POJEDYNCZEGO logowania usługi** Wklej **adres URL SAML pojedynczego Sign‑On usługi** wartości skopiowanych z okna konfiguracji aplikacji usługi Azure AD.
    
    b. W **adres URL WYLOGOWANIA SAML** Wklej **Sign‑Out URL** wartości skopiowanych z okna konfiguracji aplikacji usługi Azure AD.
    
    c. Otwórz plik pobranego certyfikatu w programie Notatnik, skopiuj zawartość, a następnie wklej go w **certyfikatu publicznego** pole.
    
    d. W **Etykieta przycisku logowania** wprowadź nazwę dla przycisku, który będzie wyświetlany na stronie logowania.
    
    e. Kliknij przycisk **SAVE** (Zapisz).

    f. Kliknij przycisk **aktualizacji**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/active-directory-saas-iqualify-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/active-directory-saas-iqualify-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/active-directory-saas-iqualify-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/active-directory-saas-iqualify-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-an-iqualify-lms-test-user"></a>Tworzenie użytkownika testowego LMS iQualify

W tej sekcji użytkownika o nazwie Simona Britta jest tworzony w iQualify. iQualify LMS obsługę just‑in‑time użytkownika, który jest domyślnie włączona.

Nie ma elementu akcji można w tej sekcji. Jeśli użytkownik nie istnieje w iQualify, nowy jest tworzony podczas próby uzyskania dostępu iQualify LMS.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji musisz włączyć Simona Britta do użycia usługi Azure logowania jednokrotnego za udzielanie dostępu do iQualify LMS.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta iQualify LMS, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **iQualify LMS**.

    ![Łącze LMS iQualify na liście aplikacji](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu iQualify, który LMS kafelka w panelu dostępu, należy pobrać strony logowania aplikacji LMS iQualify. 

   ![strony logowania](./media/active-directory-saas-iqualify-tutorial/login.png) 

Kliknij przycisk **Zaloguj się przy użyciu usługi Azure AD** button należy pobrać automatycznie zalogowane iQualify LMS aplikacji.

Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_203.png

