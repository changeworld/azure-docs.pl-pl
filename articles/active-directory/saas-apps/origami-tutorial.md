---
title: 'Samouczek: Integracji Azure Active Directory z Origami | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Origami.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: a28bb0ba-b564-46ba-accc-e587699295d4
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: 019d05cfb0339341e541e6fb3327568e8639bcda
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36230138"
---
# <a name="tutorial-azure-active-directory-integration-with-origami"></a>Samouczek: Integracji Azure Active Directory z Origami

Z tego samouczka dowiesz się integrowanie Origami w usłudze Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Origami zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Origami
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Origami (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Origami, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Origami logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Origami z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-origami-from-the-gallery"></a>Dodawanie Origami z galerii
Aby skonfigurować integrację usługi Azure AD Origami, należy dodać Origami z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Origami z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **Origami**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/origami-tutorial/tutorial_origami_search.png)

5. W panelu wyników wybierz **Origami**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/origami-tutorial/tutorial_origami_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Origami w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Origami jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Origami musi się.

W Origami, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Origami, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Origami](#creating-an-origami-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Origami połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Origami.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Origami, wykonaj następujące czynności:**

1. W portalu Azure na **Origami** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/origami-tutorial/tutorial_origami_samlbase.png)

3. Na **Origami domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/origami-tutorial/tutorial_origami_url.png)

    W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://live.origamirisk.com/origami/account/login?account=<companyname>`

    > [!NOTE] 
    > Wartość nie jest prawdziwe. Zaktualizuj tę wartość z adresem URL logowania rzeczywistych. Skontaktuj się z [zespołem pomocy technicznej klienta Origami](https://wordpress.org/support/theme/origami) można uzyskać wartość. 
 
4. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/origami-tutorial/tutorial_origami_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/origami-tutorial/tutorial_general_400.png)

6. Na **konfiguracji Origami** , kliknij przycisk **skonfigurować Origami** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adresu URL i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/origami-tutorial/tutorial_origami_configure.png) 

7. Zaloguj się do konta Origami z prawami administratora.

8. W menu u góry kliknij **Admin**.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/origami-tutorial/tutorial_origami_51.png)

9. Na stronie okna dialogowego konfiguracji na znak pojedynczego wykonaj następujące czynności:
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/origami-tutorial/tutorial_origami_531.png)

    a. Wybierz **włączenia funkcji logowania jednokrotnego w**.

    b. W **dostawcy tożsamości logowania URL strony** pole tekstowe, Wklej wartość **SAML pojedynczy znak na adres URL usługi**, które zostały skopiowane z portalu Azure.

    c. W **adres URL strony Sign-out dostawcy tożsamości** pole tekstowe, Wklej wartość **Sign-Out URL**, które zostały skopiowane z portalu Azure.

    d. Kliknij przycisk **Przeglądaj** można przekazać certyfikatu został pobrany z portalu Azure.

    e. Kliknij przycisk **zapisać zmiany**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/origami-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/origami-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/origami-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/origami-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-an-origami-test-user"></a>Tworzenie użytkownika testowego Origami

W tej sekcji należy utworzyć użytkownika o nazwie Simona Britta w Origami. 

1. Zaloguj się do konta Origami z prawami administratora.

2. W menu u góry kliknij **Admin**.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/origami-tutorial/tutorial_origami_51.png)

3. Na **użytkowników i zabezpieczeń** okna dialogowego, kliknij przycisk **użytkowników**.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/origami-tutorial/tutorial_origami_54.png)

4. Kliknij przycisk **Dodaj nowego użytkownika**.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/origami-tutorial/tutorial_origami_55.png)

5. W oknie dialogowym Dodawanie nowego użytkownika wykonaj następujące czynności:
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/origami-tutorial/tutorial_origami_56.png)

    a. W **nazwy użytkownika** pole tekstowe, wprowadź adres e-mail użytkownika, takich jak **brittasimon@contoso.com**.

    b. W **hasło** tekstowym, wpisz hasło.

    c. W **Potwierdź hasło** tekstowym, wpisz hasło ponownie.

    d. W **imię** pole tekstowe, wprowadź imię użytkownika, takich jak **Britta**.

    e. W **nazwisko** pole tekstowe, wprowadź nazwisko użytkownika, takich jak **Simona**.

    f. Kliknij pozycję **Zapisz**.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/origami-tutorial/tutorial_origami_57.png)

6. Przypisz **ról użytkownika** i **dostępu klienta** dla użytkownika. 
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/origami-tutorial/tutorial_origami_58.png)

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu Origami.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta Origami, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Origami**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/origami-tutorial/tutorial_origami_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Origami w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji Origami.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/origami-tutorial/tutorial_general_01.png
[2]: ./media/origami-tutorial/tutorial_general_02.png
[3]: ./media/origami-tutorial/tutorial_general_03.png
[4]: ./media/origami-tutorial/tutorial_general_04.png

[100]: ./media/origami-tutorial/tutorial_general_100.png

[200]: ./media/origami-tutorial/tutorial_general_200.png
[201]: ./media/origami-tutorial/tutorial_general_201.png
[202]: ./media/origami-tutorial/tutorial_general_202.png
[203]: ./media/origami-tutorial/tutorial_general_203.png

