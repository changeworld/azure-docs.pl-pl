---
title: 'Samouczek: Integracji Azure Active Directory z Teamphoria | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Teamphoria.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: d569c705-6f0f-4ec1-b485-ba82526b5d32
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2018
ms.author: jeedes
ms.openlocfilehash: 89dea3114c502cbc726e48066138169dc4cc7e04
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34591892"
---
# <a name="tutorial-azure-active-directory-integration-with-teamphoria"></a>Samouczek: Integracji Azure Active Directory z Teamphoria

Z tego samouczka dowiesz się integrowanie Teamphoria z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Teamphoria zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Teamphoria
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Teamphoria (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Teamphoria, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Teamphoria jednokrotnego włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym.
Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Teamphoria z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-teamphoria-from-the-gallery"></a>Dodawanie Teamphoria z galerii
Aby skonfigurować integrację usługi Azure AD Teamphoria, należy dodać Teamphoria z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Teamphoria z galerii, wykonaj następujące czynności:**

1. W  **[Azure Portal](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Kliknij przycisk **Dodaj** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **Teamphoria**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_search.png)

5. W panelu wyników wybierz **Teamphoria**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Teamphoria w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Teamphoria jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Teamphoria musi się.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Teamphoria, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Teamphoria](#creating-a-teamphoria-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Teamphoria połączonego z jej reprezentacji usługi Azure AD.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Teamphoria.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Teamphoria, wykonaj następujące czynności:**

1. W portalu Azure na **Teamphoria** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_samlbase.png)

3. Na **Teamphoria domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_url.png)

    W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<sub-domain>.teamphoria.com/login`   

    > [!NOTE] 
    > Wartość adres URL logowania nie jest prawdziwe. Należy zaktualizować tę wartość z adresem URL logowania rzeczywistych. Skontaktuj się z [zespołem pomocy technicznej klienta Teamphoria](https://www.teamphoria.com/) Aby uzyskać adres URL logowania jednokrotnego.

4. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base64)** , a następnie Zapisz certyfikat na komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_certificate.png)

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-teamphoria-tutorial/tutorial_general_400.png)

6. Na **konfiguracji Teamphoria** , kliknij przycisk **skonfigurować Teamphoria** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_configure.png)

7. Aby skonfigurować logowanie jednokrotne w **Teamphoria** strony logowania do aplikacji Teamphoria jako administrator.

8. Przejdź do **ustawienia administratora** kliknij opcję na lewym pasku narzędzi oraz na karcie Konfigurowanie **POJEDYNCZEGO logowania** otworzyć okno konfiguracji logowania jednokrotnego.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-teamphoria-tutorial/admin_sso_configure.png)

9. Polecenie **Dodaj nowego DOSTAWCĘ tożsamości** opcję w prawym górnym rogu, aby otworzyć formularz dodawania ustawienia logowania jednokrotnego.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-teamphoria-tutorial/add_new_identity_provider.png)

10. Wprowadź dane w polach zgodnie z opisem poniżej-

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-teamphoria-tutorial/Teamphoria_sso_save.png)

    a. **Nazwa WYŚWIETLANA**: Wprowadź nazwę wyświetlaną wtyczki na stronie Administracja.

    b. **Nazwa przycisku**: Nazwa karty, która będzie wyświetlana na stronie logowania dla logowania za pośrednictwem rejestracji Jednokrotnej.

    c. **CERTYFIKAT**: Otwórz certyfikat wcześniej pobrany z portalu Azure w programie Notatnik, skopiuj zawartość tego samego i wklej go w tym miejscu w polu.

    d. **PUNKT wejścia**: Wklej **SAML pojedynczy znak na adres URL usługi** skopiowane wcześniej z portalu Azure.

    e. Przełącz opcję **ON** i wybierz polecenie **ZAPISAĆ**.

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-teamphoria-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-teamphoria-tutorial/create_aaduser_02.png) 

3. W górnej części okna dialogowego kliknij **Dodaj** otworzyć **użytkownika** okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-teamphoria-tutorial/create_aaduser_03.png)

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-teamphoria-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.

### <a name="creating-a-teamphoria-test-user"></a>Tworzenie użytkownika testowego Teamphoria

Aby włączyć użytkowników usługi Azure AD zalogować się do Teamphoria, musi być przygotowana do Teamphoria. W przypadku Teamphoria Inicjowanie obsługi to zadanie ręczne.

**Aby udostępnić konta użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy Teamphoria jako administrator.

2. Polecenie **ADMIN** ustawień na lewym pasku narzędzi i w obszarze **ZARZĄDZAJ** karcie kliknij na **użytkowników** aby otworzyć stronę administratora dla użytkowników.

    ![Dodawanie pracownika](./media/active-directory-saas-teamphoria-tutorial/admin_manage_users.png)

3. Polecenie **ZAPROSIĆ RĘCZNEGO** opcji.

    ![Zaproś inne osoby](./media/active-directory-saas-teamphoria-tutorial/admin_manage_add_users.png)

4. Na tej stronie należy wykonać następujące działania.
    
    ![Zaproś inne osoby](./media/active-directory-saas-teamphoria-tutorial/manual_user_invite.png)

    a. W **adres E-mail** pole tekstowe, **adres e-mail** z BrittaSimon.

    b. W **imię** pole tekstowe, typ **Britta**.

    c. W **nazwisko** pole tekstowe, typ **Simona**.

    d. Kliknij przycisk **użytkownika zaproszenia 1**. Użytkownik musi zaakceptować zaproszenie tworzone w systemie.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu Teamphoria.

![Przypisz użytkownika][200]

**Aby przypisać Simona Britta Teamphoria, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji zaznacz **Teamphoria**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.

### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Jeśli chcesz przetestować jednego ustawienia logowania jednokrotnego, otwórz Panel dostępu. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://msdn.microsoft.com/library/dn308586).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_203.png
