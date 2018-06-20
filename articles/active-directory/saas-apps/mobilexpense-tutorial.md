---
title: 'Samouczek: Integracji Azure Active Directory z Mobile Xpense | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Mobile Xpense.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: e649fc4e-3e15-4948-b977-00bfe9f7db13
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: jeedes
ms.openlocfilehash: 57c811f0f29c885ef4bbe78c46ddc6b10b0f71f3
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36212533"
---
# <a name="tutorial-azure-active-directory-integration-with-mobile-xpense"></a>Samouczek: Integracji Azure Active Directory z Mobile Xpense

Z tego samouczka dowiesz się integrowanie Mobile Xpense w usłudze Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Mobile Xpense zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Mobile Xpense.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Xpense Mobile (logowanie jednokrotne) z konta usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Mobile Xpense, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Mobile Xpense jednokrotnego włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Mobile Xpense z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-mobile-xpense-from-the-gallery"></a>Dodawanie Mobile Xpense z galerii
Aby skonfigurować integrację Mobile Xpense w usłudze Azure Active Directory, należy dodać Mobile Xpense z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Mobile Xpense z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Mobile Xpense**, wybierz pozycję **Mobile Xpense** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Xpense Mobile na liście wyników](./media/mobilexpense-tutorial/tutorial_mobilexpense_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Xpense Mobile w oparciu o nazwie "Britta Simona" użytkownika testowego.

Do rejestracji jednokrotnej do pracy usługi Azure AD musi ustalić użytkownika odpowiednika w Mobile Xpense do użytkownika w usłudze Azure AD. Innymi słowy musi można ustanowić łącze relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Mobile Xpense.

W Mobile Xpense, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Mobile Xpense, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Mobile Xpense](#create-a-mobile-xpense-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Xpense Mobile, połączonej z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji mobilnej Xpense.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Mobile Xpense, wykonaj następujące czynności:**

1. W portalu Azure na **Mobile Xpense** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/mobilexpense-tutorial/tutorial_mobilexpense_samlbase.png)

3. Na **Mobile Xpense domeny i adres URL** sekcji, wykonaj następujące kroki, aby skonfigurować aplikację w trybie IDP inicjowane:

    ![Adresy URL i domeny Xpense Mobile pojedynczy informacje logowania jednokrotnego](./media/mobilexpense-tutorial/tutorial_mobilexpense_url11.png)

    a. W **identyfikator** tekstowym, wpisz adres URL: `https://mobilexpense.com/ServiceProvider`

    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<sub-domain>.mobilexpense.com/NET/SSO/SAML20/SAML/AssertionConsumerService.aspx`

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** inicjowane tryb:

    ![Adresy URL i domeny Xpense Mobile pojedynczy informacje logowania jednokrotnego](./media/mobilexpense-tutorial/tutorial_mobilexpense_url22.png)

    W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<sub-domain>.mobilexpense.com/<customername>`
     
    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej klienta Xpense Mobile](http://www.mobilexpense.net/contact) uzyskać te wartości. 

5. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/mobilexpense-tutorial/tutorial_mobilexpense_certificate.png) 

6. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/mobilexpense-tutorial/tutorial_general_400.png)

7. Skonfigurować logowanie jednokrotne w **Mobile Xpense** stronie, musisz wysłać pobrany **XML metadanych** do [zespołem pomocy technicznej Mobile Xpense](http://www.mobilexpense.net/contact). To ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML one wartość.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/mobilexpense-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/mobilexpense-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/mobilexpense-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/mobilexpense-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-mobile-xpense-test-user"></a>Tworzenie użytkownika testowego Mobile Xpense

W tej sekcji należy utworzyć użytkownika o nazwie Simona Britta w MobileXpense. Korzystanie z [zespołem pomocy technicznej MobileXpense](http://www.mobilexpense.net/contact) Aby dodać użytkowników do platformy MobileXpense. Użytkownicy muszą utworzyć i aktywowana, aby użyć rejestracji jednokrotnej. 

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu Mobile Xpense.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta Mobile Xpense, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Mobile Xpense**.

    ![Łącze Mobile Xpense na liście aplikacji](./media/mobilexpense-tutorial/tutorial_mobilexpense_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Mobile Xpense w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji mobilnej Xpense.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/mobilexpense-tutorial/tutorial_general_01.png
[2]: ./media/mobilexpense-tutorial/tutorial_general_02.png
[3]: ./media/mobilexpense-tutorial/tutorial_general_03.png
[4]: ./media/mobilexpense-tutorial/tutorial_general_04.png

[100]: ./media/mobilexpense-tutorial/tutorial_general_100.png

[200]: ./media/mobilexpense-tutorial/tutorial_general_200.png
[201]: ./media/mobilexpense-tutorial/tutorial_general_201.png
[202]: ./media/mobilexpense-tutorial/tutorial_general_202.png
[203]: ./media/mobilexpense-tutorial/tutorial_general_203.png

