---
title: 'Samouczek: Integracji Azure Active Directory z ClickTime | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i ClickTime.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: d437b5ab-4d71-4c13-96d0-79018cebbbd4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: jeedes
ms.openlocfilehash: f24dab1856880a3d8c69701257167cae8d97e241
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34343368"
---
# <a name="tutorial-azure-active-directory-integration-with-clicktime"></a>Samouczek: Integracji Azure Active Directory z ClickTime

Z tego samouczka dowiesz się integrowanie ClickTime z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD ClickTime zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do ClickTime
- Umożliwia użytkownikom automatycznie pobrać zalogowane do ClickTime (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z ClickTime, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- ClickTime logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie ClickTime z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-clicktime-from-the-gallery"></a>Dodawanie ClickTime z galerii
Aby skonfigurować integrację usługi Azure AD ClickTime, należy dodać ClickTime z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać ClickTime z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **ClickTime**, wybierz pozycję **ClickTime** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![ClickTime na liście wyników](./media/active-directory-saas-clicktime-tutorial/tutorial_clicktime_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z ClickTime w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w ClickTime jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w ClickTime musi się.

W ClickTime, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z ClickTime, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego ClickTime](#create-a-clicktime-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta ClickTime połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji ClickTime.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z ClickTime, wykonaj następujące czynności:**

1. W portalu Azure na **ClickTime** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/active-directory-saas-clicktime-tutorial/tutorial_clicktime_samlbase.png)

3. Na **ClickTime domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Adresy URL i domeny ClickTime pojedynczy informacje logowania jednokrotnego](./media/active-directory-saas-clicktime-tutorial/tutorial_clicktime_url.png)

    a. W **identyfikator** tekstowym, wpisz adres URL jako: `https://app.clicktime.com/sp/`
    
    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL za pomocą następujących wzorców: 

    | |
    |--|
    | `https://app.clicktime.com/Login/` |
    | `https://app.clicktime.com/App/Login/Consume.aspx` |

4. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/active-directory-saas-clicktime-tutorial/tutorial_clicktime_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-clicktime-tutorial/tutorial_general_400.png)

6. Na **konfiguracji ClickTime** , kliknij przycisk **skonfigurować ClickTime** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfiguracja ClickTime](./media/active-directory-saas-clicktime-tutorial/tutorial_clicktime_configure.png) 

7. W oknie przeglądarki innej witryny sieci web Zaloguj się do witryny firmy ClickTime jako administrator.

8. Na pasku narzędzi u góry kliknij **preferencje**, a następnie kliknij przycisk **ustawienia zabezpieczeń**.

9. W **preferencje rejestracji jednokrotnej** konfiguracji sekcji, wykonaj następujące czynności:
   
    ![Ustawienia zabezpieczeń](./media/active-directory-saas-clicktime-tutorial/tic777280.png "ustawienia zabezpieczeń")
   
    a.  Wybierz **Zezwalaj** Zaloguj się przy użyciu pojedynczego logowania jednokrotnego (SSO) z **usługi Azure AD**.
   
    b. W **punktu końcowego dostawcy tożsamości** pole tekstowe, Wklej **SAML pojedynczy znak na adres URL usługi** którego została skopiowana z portalu Azure.
   
    c.  Otwórz **certyfikatu algorytmem base-64** pobrany z portalu Azure w **Notatnik**, skopiuj zawartość, a następnie wklej go do **certyfikatu X.509** pola tekstowego.
   
    d.  Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/active-directory-saas-clicktime-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.
    
    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/active-directory-saas-clicktime-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.
 
    ![Przycisk Dodaj](./media/active-directory-saas-clicktime-tutorial/create_aaduser_03.png) 

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:
 
    ![Okno dialogowe użytkownika](./media/active-directory-saas-clicktime-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-clicktime-test-user"></a>Tworzenie użytkownika testowego ClickTime

Aby włączyć użytkowników usługi Azure AD zalogować się do ClickTime, musi być przygotowana do ClickTime.  
W przypadku ClickTime Inicjowanie obsługi to zadanie ręczne.

> [!NOTE]
> Możesz użyć innych ClickTime użytkownika konta tworzenia narzędzi lub interfejsów API dostarczonych przez ClickTime do udostępnienia konta użytkownika usługi Azure AD.

**Aby udostępnić konta użytkownika, wykonaj następujące czynności:**
1. Zaloguj się do Twojego **ClickTime** dzierżawy.
2. Na pasku narzędzi u góry kliknij **firmy**, a następnie kliknij przycisk **osób**.
   
    ![Osoby](./media/active-directory-saas-clicktime-tutorial/tic777282.png "osób")
3. Kliknij przycisk **Dodaj osobę**.
   
    ![Dodaj osobę](./media/active-directory-saas-clicktime-tutorial/tic777283.png "Dodaj osobę")
4. W sekcji nowej osoby wykonaj następujące czynności:
   
    ![Osoby](./media/active-directory-saas-clicktime-tutorial/tic777284.png "osób")
   
    a.  W **Pełna nazwa** pole tekstowe, typ Pełna nazwa użytkownika, takich jak **Simona Britta**. 
  
    b.  W **adres e-mail** tekstowym, wpisz adres e-mail użytkownika, takich jak **brittasimon@contoso.com**.
       
    > [!NOTE]
    > Jeśli chcesz, można ustawić dodatkowe właściwości dla nowego obiektu osoby.
   
    c.  Kliknij pozycję **Zapisz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu ClickTime.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta ClickTime, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **ClickTime**.

    ![Łącze ClickTimne na liście aplikacji](./media/active-directory-saas-clicktime-tutorial/tutorial_clicktime_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka ClickTime w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji ClickTime.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_203.png

