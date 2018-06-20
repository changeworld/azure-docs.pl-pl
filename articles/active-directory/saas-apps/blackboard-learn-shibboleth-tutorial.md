---
title: 'Samouczek: Integracji Azure Active Directory z tablica Dowiedz się - Shibboleth | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między nauki tablica - Shibboleth i Azure Active Directory.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: e435cbb4-c0f0-400e-943c-5c923fa8ddf2
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2017
ms.author: jeedes
ms.openlocfilehash: 5c7f8fb213359da948b4b6d92333bf68935815db
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36227469"
---
# <a name="tutorial-azure-active-directory-integration-with-blackboard-learn---shibboleth"></a>Samouczek: Integracji Azure Active Directory z tablica Dowiedz się - Shibboleth

Z tego samouczka dowiesz integrowanie Dowiedz się tablica - Shibboleth w usłudze Azure Active Directory (Azure AD).

Integrowanie informacje tablica - Shibboleth z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do informacji tablica - Shibboleth
- Umożliwia użytkownikom automatycznie pobrać zalogowane tablica informacji - Shibboleth (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z tablica Dowiedz się - Shibboleth, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Tablica Dowiedz się - Shibboleth jednokrotnego włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie informacje tablica - Shibboleth z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-blackboard-learn---shibboleth-from-the-gallery"></a>Dodawanie informacje tablica - Shibboleth z galerii
Aby skonfigurować integrację informacji tablica - Shibboleth do usługi Azure AD, należy dodać informacje tablica — Shibboleth z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać informacje tablica - Shibboleth z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **tablica Dowiedz się - Shibboleth**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/blackboard-learn-shibboleth-tutorial/tutorial_blackboardlearn-shibboleth_search.png)

5. W panelu wyników wybierz **tablica Dowiedz się - Shibboleth**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/blackboard-learn-shibboleth-tutorial/tutorial_blackboardlearn-shibboleth_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji możesz skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z tablica Dowiedz się więcej — Shibboleth oparte na koncie użytkownika testu o nazwie "Britta Simona".

Logowanie jednokrotne do pracy usługi Azure AD musi wiedzieć użytkownika odpowiednika w Dowiedz się, tablica — Shibboleth jest użytkownikiem w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i danemu użytkownikowi w tablica Dowiedz się więcej — Shibboleth musi się.

Dowiedz się więcej tablica - Shibboleth, przypisywanie wartości **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Do konfigurowania i testowania usługi Azure AD rejestracji jednokrotnej z Dowiedz się tablica - Shibboleth, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie tablica informacji - użytkownika testowego Shibboleth](#creating-a-blackboard-learn---shibboleth-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta tablica Dowiedz się - Shibboleth połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w sieci tablica informacje - Shibboleth aplikacji.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z tablica Dowiedz się - Shibboleth, wykonaj następujące czynności:**

1. W portalu Azure na **tablica Dowiedz się - Shibboleth** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/blackboard-learn-shibboleth-tutorial/tutorial_blackboardlearn-shibboleth_samlbase.png)

3. Na **tablica Poznaj - Shibboleth domeny i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/blackboard-learn-shibboleth-tutorial/tutorial_blackboardlearn-shibboleth_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<yourblackoardlearnserver>.blackboardlearn.com/Shibboleth.sso/Login`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<yourblackoardlearnserver>.blackboardlearn.com/shibboleth-sp`

    c. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<yourblackoardlearnserver>.blackboardlearn.com/Shibboleth.sso/SAML2/POST`
 
    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty identyfikator, adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości. Skontaktuj się z [tablica Dowiedz się - zespołem pomocy technicznej klienta Shibboleth](https://www.blackboard.com/forms/contact-us_form.aspx) uzyskać te wartości. 

4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/blackboard-learn-shibboleth-tutorial/tutorial_blackboardlearn-shibboleth_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/blackboard-learn-shibboleth-tutorial/tutorial_general_400.png)
    
6. Na **tablica Dowiedz się - Shibboleth konfiguracji** , kliknij przycisk **skonfigurować tablica informacji - Shibboleth** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adres URL, identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/blackboard-learn-shibboleth-tutorial/tutorial_blackboardlearn-shibboleth_configure.png) 

7. Skonfigurować logowanie jednokrotne w **tablica Dowiedz się - Shibboleth** stronie, musisz wysłać pobrany **XML metadanych** i **Sign-Out adres URL, identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi**  do [tablica Dowiedz się - zespołem pomocy technicznej Shibboleth](https://www.blackboard.com/forms/contact-us_form.aspx).

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/blackboard-learn-shibboleth-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/blackboard-learn-shibboleth-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/blackboard-learn-shibboleth-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/blackboard-learn-shibboleth-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-blackboard-learn---shibboleth-test-user"></a>Tworzenie tablica informacji - Shibboleth użytkownika testowego

W tej sekcji można utworzyć użytkownika o nazwie Simona Britta w tablica Dowiedz się - Shibboleth. Praca z Twojej [tablica Dowiedz się - Shibboleth zespołem pomocy technicznej](https://www.blackboard.com/forms/contact-us_form.aspx) Aby dodać użytkowników w tablica informacji - Shibboleth platformy.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do informacji tablica - Shibboleth.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta tablica informacji - Shibboleth, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **tablica Dowiedz się - Shibboleth**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/blackboard-learn-shibboleth-tutorial/tutorial_blackboardlearn-shibboleth_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu tablica informacji - Shibboleth kafelka w panelu dostępu należy należy pobrać automatycznie zalogowane Twojej tablica informacji - Shibboleth aplikacji.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/blackboard-learn-shibboleth-tutorial/tutorial_general_01.png
[2]: ./media/blackboard-learn-shibboleth-tutorial/tutorial_general_02.png
[3]: ./media/blackboard-learn-shibboleth-tutorial/tutorial_general_03.png
[4]: ./media/blackboard-learn-shibboleth-tutorial/tutorial_general_04.png

[100]: ./media/blackboard-learn-shibboleth-tutorial/tutorial_general_100.png

[200]: ./media/blackboard-learn-shibboleth-tutorial/tutorial_general_200.png
[201]: ./media/blackboard-learn-shibboleth-tutorial/tutorial_general_201.png
[202]: ./media/blackboard-learn-shibboleth-tutorial/tutorial_general_202.png
[203]: ./media/blackboard-learn-shibboleth-tutorial/tutorial_general_203.png

