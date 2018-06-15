---
title: 'Samouczek: Integracji Azure Active Directory z Rightscale | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Rightscale.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 3a8d376d-95fb-4dd7-832a-4fdd4dd7c87c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2017
ms.author: jeedes
ms.openlocfilehash: 37df1eca6b55a8bd126645f43d177263a1bede70
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34352603"
---
# <a name="tutorial-azure-active-directory-integration-with-rightscale"></a>Samouczek: Integracji Azure Active Directory z Rightscale

Z tego samouczka dowiesz się integrowanie Rightscale z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Rightscale zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Rightscale
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Rightscale (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Rightscale, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Rightscale logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Rightscale z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-rightscale-from-the-gallery"></a>Dodawanie Rightscale z galerii
Aby skonfigurować integrację usługi Azure AD Rightscale, należy dodać Rightscale z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Rightscale z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **Rightscale**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_search.png)

5. W panelu wyników wybierz **Rightscale**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Rightscale w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Rightscale jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Rightscale musi się.

W Rightscale, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Rightscale, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Rightscale](#creating-a-rightscale-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Rightscale połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Rightscale.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Rightscale, wykonaj następujące czynności:**

1. W portalu Azure na **Rightscale** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_samlbase.png)

3. Na **domeny Rightscale i adres URL** sekcji, jeśli chcesz skonfigurować aplikację w **IDP zainicjował tryb** nie trzeba wykonywać żadnych czynności, ponieważ aplikacja jest już wstępnie zintegrowane z usługi Azure.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_url.png)

4. Na **domeny Rightscale i adres URL** sekcji, jeśli chcesz skonfigurować aplikację w **SP zainicjował tryb**, wykonaj następujące czynności:
    
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_url1.png)

    a. Polecenie **Pokaż zaawansowane ustawienia adresu URL**.

    b. W **na adres URL logowania** tekstowym, wpisz adres URL: `https://login.rightscale.com/`

5. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_certificate.png) 

6. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-rightscale-tutorial/tutorial_general_400.png)

7. Na **konfiguracji Rightscale** , kliknij przycisk **skonfigurować Rightscale** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_configure.png) 
<CS>
8. Aby uzyskać logowania jednokrotnego skonfigurowane dla aplikacji, należy logowania jednokrotnego dla Twojej dzierżawy RightScale jako administrator.

    a. W menu u góry kliknij **ustawienia** i wybierz **rejestracji jednokrotnej**.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_001.png) 

    b. Kliknij przycisk "**nowe**" przycisk, aby dodać **Your dostawców tożsamości SAML**.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_002.png) 
 
    c. W polu tekstowym z **Nazwa wyświetlana**, wprowadź nazwę swojej firmy.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_003.png)
 
    d. Wybierz **inicjowane Zezwalaj RightScale logowania jednokrotnego, używając wskazówki odnajdywania** i wejściowego z **nazwy domeny** w poniżej pola tekstowego.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_004.png)

    e. Wklej wartość **SAML pojedynczy znak na adres URL usługi** , które zostały skopiowane z portalu Azure do **punktu końcowego logowania jednokrotnego SAML** w RightScale.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_006.png)

    f. Wklej wartość **identyfikator jednostki SAML** , które zostały skopiowane z portalu Azure do **SAML EntityID** w RightScale.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_008.png)

    g. Kliknij przycisk **przeglądarki** przycisk, aby przekazać certyfikat, który został pobrany z portalu Azure.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_009.png)

    h. Kliknij pozycję **Zapisz**.
<CE>
> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-rightscale-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-rightscale-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-rightscale-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-rightscale-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-rightscale-test-user"></a>Tworzenie użytkownika testowego Rightscale

W tej sekcji należy utworzyć użytkownika o nazwie Simona Britta w RightScale. Praca z [zespołem pomocy technicznej klienta Rightscale](mailto:support@rightscale.com) Aby dodać użytkowników do platformy RightScale.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu Rightscale.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta Rightscale, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Rightscale**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

Celem tej sekcji służy do testowania konfigurację usługi Azure AD z logowania jednokrotnego za pomocą panelu dostępu.  

Po kliknięciu kafelka RightScale w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji RightScale.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_203.png

