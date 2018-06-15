---
title: 'Samouczek: Integracji Azure Active Directory z sygnałów dynamicznych | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i sygnałów dynamicznych.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 863f7340-b065-4f59-b092-daa67da6f703
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2018
ms.author: jeedes
ms.openlocfilehash: 759d5656873d7870dd0c46c30e89372878b300f8
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34344851"
---
# <a name="tutorial-azure-active-directory-integration-with-dynamic-signal"></a>Samouczek: Integracji Azure Active Directory z sygnałów dynamicznych

Z tego samouczka dowiesz się integrowanie sygnałów dynamicznych w usłudze Azure Active Directory (Azure AD).

Integrowanie sygnałów dynamicznych z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do sygnałów dynamicznych.
- Umożliwia użytkownikom automatycznie pobrać zalogowane dynamiczne sygnału (logowanie jednokrotne) z konta usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z sygnałów dynamicznych, są potrzebne następujące elementy:

- Subskrypcję usługi Azure AD
- Dynamiczne sygnału jednokrotnego włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie sygnałów dynamicznych z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-dynamic-signal-from-the-gallery"></a>Dodawanie sygnałów dynamicznych z galerii
Aby skonfigurować integrację usługi Azure AD sygnałów dynamicznych, należy dodać sygnałów dynamicznych z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać sygnałów dynamicznych z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **sygnałów dynamicznych**, wybierz pozycję **sygnałów dynamicznych** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Sygnałów dynamicznych na liście wyników](./media/active-directory-saas-dynamicsignal-tutorial/tutorial_dynamicsignal_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z sygnałów dynamicznych w oparciu o nazwie "Britta Simona" użytkownika testowego.

Do rejestracji jednokrotnej do pracy usługi Azure AD musi ustalić użytkownika odpowiednika w sygnałów dynamicznych do użytkownika w usłudze Azure AD. Innymi słowy musi można ustanowić łącze relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w dynamicznej.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z sygnałów dynamicznych, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego sygnałów dynamicznych](#create-a-dynamic-signal-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta dynamiczne sygnał, który jest połączony z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji sygnałów dynamicznych.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z sygnałów dynamicznych, wykonaj następujące czynności:**

1. W portalu Azure na **sygnałów dynamicznych** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/active-directory-saas-dynamicsignal-tutorial/tutorial_dynamicsignal_samlbase.png)

3. Na **adresy URL i dynamicznych domeny sygnału** sekcji, wykonaj następujące czynności:
 
    ![Dynamiczne adresy URL i domeny sygnału pojedynczy informacje logowania jednokrotnego](./media/active-directory-saas-dynamicsignal-tutorial/tutorial_dynamicsignal_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<subdomain>.voicestorm.com`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<subdomain>.voicestorm.com`

    c. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<subdomain>.voicestorm.com/User/SsoResponse`

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty identyfikator, adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej dynamiczne klienta sygnału](mailto:support@dynamicsignal.com) uzyskać te wartości. 

4. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/active-directory-saas-dynamicsignal-tutorial/tutorial_dynamicsignal_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-dynamicsignal-tutorial/tutorial_general_400.png)
    
6. Na **konfiguracji dynamicznej sygnału** kliknij **skonfigurować sygnałów dynamicznych** otworzyć **konfigurowania rejestracji** okna. Kopiuj **Sign-Out adres URL, identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfiguracja dynamicznej sygnału](./media/active-directory-saas-dynamicsignal-tutorial/tutorial_dynamicsignal_configure.png) 

7. Do konfigurowania rejestracji jednokrotnej na **sygnałów dynamicznych** stronie, musisz wysłać pobrany **certyfikatu (Base64), adres URL Sign-Out identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** do [dynamiczne Sygnał zespołem pomocy technicznej](mailto:support@dynamicsignal.com). To ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML one wartość.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/active-directory-saas-dynamicsignal-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/active-directory-saas-dynamicsignal-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/active-directory-saas-dynamicsignal-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/active-directory-saas-dynamicsignal-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-dynamic-signal-test-user"></a>Tworzenie użytkownika testowego sygnałów dynamicznych

Celem tej sekcji jest utworzenie użytkownika o nazwie Simona Britta w sygnałów dynamicznych. Dynamiczne sygnału obsługę w czasie, który jest domyślnie włączone. Nie ma elementu akcji można w tej sekcji. Nowy użytkownik został utworzony podczas próby dostępu dynamicznej sygnału, jeśli go jeszcze nie istnieje.

>[!Note]
>Jeśli trzeba ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej sygnałów dynamicznych](mailto:support@dynamicsignal.com).

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do dynamicznego sygnału.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta sygnałów dynamicznych, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **sygnałów dynamicznych**.

    ![Łącze sygnałów dynamicznych na liście aplikacji](./media/active-directory-saas-dynamicsignal-tutorial/tutorial_dynamicsignal_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka sygnałów dynamicznych w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane sygnałów dynamicznych aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-dynamicsignal-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-dynamicsignal-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-dynamicsignal-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-dynamicsignal-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-dynamicsignal-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-dynamicsignal-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-dynamicsignal-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-dynamicsignal-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-dynamicsignal-tutorial/tutorial_general_203.png

