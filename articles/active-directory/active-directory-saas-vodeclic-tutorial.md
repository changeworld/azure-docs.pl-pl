---
title: 'Samouczek: Integracji Azure Active Directory z Vodeclic | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Vodeclic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: d77a0f53-e3a3-445e-ab3e-119cef6e2e1d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: jeedes
ms.openlocfilehash: bc889919f2d869478843881cc8eae06fc9cb232c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34348210"
---
# <a name="tutorial-azure-active-directory-integration-with-vodeclic"></a>Samouczek: Integracji Azure Active Directory z Vodeclic

Z tego samouczka dowiesz się integrowanie Vodeclic z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Vodeclic zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Vodeclic.
- Można umożliwić użytkownikom automatycznie pobrać zalogowanego do Vodeclic (logowanie jednokrotne lub logowania jednokrotnego) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji--portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Vodeclic, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Włączyć logowanie Jednokrotne Vodeclic subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, wykonaj te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, [Pobierz bezpłatną wersję próbną miesięcznego](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Vodeclic z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="add-vodeclic-from-the-gallery"></a>Dodaj Vodeclic z galerii
Aby skonfigurować integrację usługi Azure AD Vodeclic, należy dodać Vodeclic z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Vodeclic z galerii, należy wykonać następujące czynności:**

1. W [portalu Azure](https://portal.azure.com), w okienku po lewej stronie wybierz **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, zaznacz **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Vodeclic**. Wybierz **Vodeclic** z panelu wyniki, a następnie wybierz **Dodaj** przycisk, aby dodać aplikację.

    ![Vodeclic na liście wyników](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji możesz skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Vodeclic na podstawie użytkownika testowego, nazywany "Britta Simona".

Do rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, komu użytkownika odpowiednika w Vodeclic jest użytkownikiem w usłudze Azure AD. Innymi słowy należy ustanowić powiązanie użytkownika usługi Azure AD i danemu użytkownikowi w Vodeclic.

W Vodeclic, należy podać wartość **Username** tę samą wartość jak **nazwy użytkownika** w usłudze Azure AD. Teraz ustanowieniu połączenia między dwóch użytkowników.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Vodeclic, wykonaj poniższe bloki konstrukcyjne:

1. [Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on) umożliwienie użytkownikom korzystać z tej funkcji.
2. [Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user) do testowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. [Tworzenie użytkownika testowego Vodeclic](#create-a-vodeclic-test-user) w celu zapewnienia odpowiednikiem Simona Britta Vodeclic połączonego z usługi Azure AD reprezentację użytkownika.
4. [Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user) umożliwiające Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. [Test rejestracji jednokrotnej](#test-single-sign-on) Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Vodeclic.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Vodeclic, należy wykonać następujące czynności:**

1. W portalu Azure na **Vodeclic** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. W **logowanie jednokrotne** okna dialogowego, w obszarze **Tryb Single-Sign-on**, wybierz pozycję **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_samlbase.png)

3. Jeśli chcesz skonfigurować aplikację w **IDP** inicjowane w trybie **Vodeclic domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Adresy URL i domeny Vodeclic pojedynczy informacje logowania jednokrotnego](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_url.png)

    a. W **identyfikator** wpisz adres URL z następującego wzorca: `https://<companyname>.lms.vodeclic.net/auth/saml`

    b. W **adres URL odpowiedzi** wpisz adres URL z następującego wzorca: `https://<companyname>.lms.vodeclic.net/auth/saml/callback`

4. Jeśli chcesz skonfigurować aplikację w **SP** tryb inicjowane, wybierz opcję **Pokaż zaawansowane ustawienia adresu URL** pole wyboru, a następnie wykonaj następujący krok:

    ![Adresy URL i domeny Vodeclic pojedynczy informacje logowania jednokrotnego](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_url1.png)

    W **adres URL logowania** wpisz adres URL z następującego wzorca: `https://<companyname>.lms.vodeclic.net/auth/saml`
     
    > [!NOTE] 
    > Wartości te nie są prawdziwe. Zaktualizować te wartości przy użyciu rzeczywistego identyfikatora odpowiedzi na adres URL i adresu URL logowania jednokrotnego. Skontaktuj się z [zespołem pomocy technicznej klienta Vodeclic](mailto:hotline@vodeclic.com) uzyskać te wartości.

5. W **certyfikat podpisywania SAML** zaznacz **XML metadanych**. Następnie zapisz plik metadanych na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_certificate.png) 

6. Wybierz pozycję **Zapisz**.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-vodeclic-tutorial/tutorial_general_400.png)
    
7. Skonfigurować logowanie jednokrotne w **Vodeclic** po stronie, Wyślij pobrany **XML metadanych** do [zespołem pomocy technicznej Vodeclic](mailto:hotline@vodeclic.com). To ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML one wartość.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com) podczas konfigurowania aplikacji. Po dodaniu tej aplikacji z **usługi Active Directory** > **aplikacje dla przedsiębiorstw** zaznacz **rejestracji jednokrotnej** karcie i dostępu do osadzonego Dokumentacja za pośrednictwem **konfiguracji** sekcji u dołu. Możesz przeczytać dodatkowe informacje o funkcji dokumentacji osadzony w [usługi Azure AD osadzonych dokumentacji]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, należy wykonać następujące czynności:**

1. W portalu Azure, w lewym okienku wybierz **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/active-directory-saas-vodeclic-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**. Następnie wybierz **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/active-directory-saas-vodeclic-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, wybierz opcję **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/active-directory-saas-vodeclic-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego należy wykonać następujące czynności:

    ![Okno dialogowe użytkownika](./media/active-directory-saas-vodeclic-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Wybierz pozycję **Utwórz**.
 
### <a name="create-a-vodeclic-test-user"></a>Tworzenie użytkownika testowego Vodeclic

W tej sekcji należy utworzyć użytkownika o nazwie Simona Britta w Vodeclic. Praca z [zespołem pomocy technicznej Vodeclic](mailto:hotline@vodeclic.com) Aby dodać użytkowników do platformy Vodeclic. Użytkownicy muszą utworzyć i aktywowana, aby użyć rejestracji jednokrotnej.

> [!NOTE]
> Zgodnie z wymaganiami aplikacji może być konieczne uzyskanie białej Twojego komputera. W tym stanie, należy udostępnić publicznego adresu IP z [zespołem pomocy technicznej Vodeclic](mailto:hotline@vodeclic.com).

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu Vodeclic.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta Vodeclic, należy wykonać następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu. Następnie należy przejść do **aplikacje dla przedsiębiorstw**, a następnie wybierz **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Vodeclic**.

    ![Łącze Vodeclic na liście aplikacji](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_app.png)  

3. W menu po lewej stronie wybierz **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Wybierz **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okno dialogowe.

    ![W okienku Dodaj przydziału][203]

5. W **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** w **użytkowników** listy.

6. W **użytkowników i grup** okno dialogowe, wybierz opcję **wybierz** przycisku.

7. W **Dodaj przydziału** okno dialogowe, wybierz opcję **przypisać** przycisku.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po wybraniu kafelka Vodeclic w panelu dostępu, możesz pobrać automatycznie zalogowany do aplikacji Vodeclic.

Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS w usłudze Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_203.png

