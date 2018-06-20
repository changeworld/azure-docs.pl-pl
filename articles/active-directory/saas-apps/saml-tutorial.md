---
title: 'Samouczek: Integracji Azure Active Directory z SAML 1.1 tokenu aplikacji LOB z obsługą | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i włączone SAML 1.1 tokenu aplikacji LOB.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ced1d88d-0e48-40d5-9aea-ef991cd9d270
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: jeedes
ms.openlocfilehash: 73135e3e4a533aa27c228b082f308cb50925c4e5
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36211700"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-11-token-enabled-lob-app"></a>Samouczek: Integracji Azure Active Directory z SAML 1.1 tokenu aplikacji LOB z obsługą

W tym samouczku opisano sposób integracji aplikacji LOB z usługą Azure Active Directory (Azure AD) z obsługą SAML 1.1 tokenu.

Integrację SAML 1.1 tokenu włączona aplikacja LOB z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do tokenu 1.1 SAML włączone aplikacji LOB.
- Można umożliwić użytkownikom automatycznie pobrać zalogowane do SAML 1.1 tokenu aplikacji z obsługą LOB (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować usługi Azure AD integracji z SAML 1.1 tokenu włączone aplikacji LOB, trzeba następujące elementy:

- Subskrypcję usługi Azure AD
- Token SAML 1.1 włączone aplikacji LOB pojedynczego logowania jednokrotnego włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie tokenu 1.1 SAML włączone aplikacji LOB z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-saml-11-token-enabled-lob-app-from-the-gallery"></a>Dodawanie tokenu 1.1 SAML włączone aplikacji LOB z galerii
Aby skonfigurować integrację SAML 1.1 tokenu aplikacji LOB z obsługą do usługi Azure AD, należy dodać z włączoną obsługą SAML 1.1 tokenu aplikacji LOB z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać z włączoną obsługą SAML 1.1 tokenu aplikacji LOB z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **aplikacji LOB z obsługą tokenu 1.1 SAML**, wybierz pozycję **aplikacji LOB z obsługą tokenu 1.1 SAML** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![SAML 1.1 tokenu aplikacji z obsługą BIZNESOWE na liście wyników](./media/saml-tutorial/tutorial_saml_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z włączona aplikacja LOB w oparciu o nazwie "Britta Simona" użytkownika testowego SAML 1.1 tokenu.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, jakie użytkownika odpowiednika w włączona aplikacja LOB jest użytkownikiem w usłudze Azure AD SAML 1.1 tokenu. Innymi słowy łącze relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w SAML 1.1 tokenu włączone aplikacji LOB powinien być określony.

Aby skonfigurować i przetestować Azure AD rejestracji jednokrotnej z SAML 1.1 tokenu aplikacji LOB z obsługą, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Utwórz SAML 1.1 Token włączone użytkownika testowego aplikacji LOB](#create-a-saml-11-token-enabled-lob-app-test-user)**  — aby odpowiednikiem Simona Britta w SAML 1.1 tokenu włączone aplikacji LOB, połączonej z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w sieci Token 1.1 SAML aplikacji LOB aplikacji z obsługą.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z SAML 1.1 tokenu aplikacji z obsługą LOB, wykonaj następujące czynności:**

1. W portalu Azure na **aplikacji LOB z obsługą tokenu 1.1 SAML** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/saml-tutorial/tutorial_saml_samlbase.png)

3. Na **SAML 1.1 tokenu włączone domeny aplikacji LOB i adres URL** sekcji, wykonaj następujące czynności:

    ![SAML 1.1 Token włączone domeny aplikacji LOB i adresów URL jednym logowania jednokrotnego informacji](./media/saml-tutorial/tutorial_saml_url.png)

    a. W **Zaloguj się na adres URL** tekstowym, wpisz adres URL, używając następującego wzorca: `https://your-app-url`

    b. W **identyfikator jednostki** tekstowym, wpisz adres URL, używając następującego wzorca: `https://your-app-url`
     
    > [!NOTE] 
    > Wartości te nie są prawdziwe. Zamień te wartości z określonego adresu URL aplikacji.  

5. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/saml-tutorial/tutorial_saml_certificate.png) 

6. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/saml-tutorial/tutorial_general_400.png)
    
7. Na **SAML 1.1 tokenu włączona konfiguracja aplikacji LOB** , kliknij przycisk **aplikacji LOB z obsługą skonfigurować tokenu SAML 1.1** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adres URL, identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![SAML 1.1 Token włączona konfiguracja aplikacji LOB](./media/saml-tutorial/tutorial_saml_configure.png) 

8. Skonfigurować logowanie jednokrotne w **aplikacji LOB z obsługą tokenu 1.1 SAML** stronie, musisz wysłać pobrany **certyfikatu (Base64), adres URL Sign-Out identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** do zespołem pomocy technicznej dla aplikacji. To ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML one wartość.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/saml-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/saml-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/saml-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/saml-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-saml-11-token-enabled-lob-app-test-user"></a>Utwórz SAML 1.1 Token włączone aplikacji LOB użytkownika testowego

W tej sekcji można utworzyć użytkownika o nazwie Simona Britta w aplikacji LOB z obsługą tokenu 1.1 SAML. Praca z aplikacją obsługuje zespołu, aby utworzyć użytkownika po stronie aplikacji. Użytkownicy muszą utworzyć i aktywowana, aby użyć rejestracji jednokrotnej.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji, Włącz Simona Britta do użycia usługi Azure logowania jednokrotnego za udzielanie dostępu do tokenu 1.1 SAML włączone aplikacji LOB.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta SAML 1.1 tokenu włączone aplikacji LOB, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **aplikacji LOB z obsługą tokenu 1.1 SAML**.

    ![SAML 1.1 Token włączone link do aplikacji LOB na liście aplikacji](./media/saml-tutorial/tutorial_saml_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu tokenu SAML 1.1 włączone kafelka aplikacji LOB na panelu dostępu, należy pobrać automatycznie podpisany w przypadku aplikacji LOB aplikacji z obsługą tokenu z SAML 1.1.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/saml-tutorial/tutorial_general_01.png
[2]: ./media/saml-tutorial/tutorial_general_02.png
[3]: ./media/saml-tutorial/tutorial_general_03.png
[4]: ./media/saml-tutorial/tutorial_general_04.png

[100]: ./media/saml-tutorial/tutorial_general_100.png

[200]: ./media/saml-tutorial/tutorial_general_200.png
[201]: ./media/saml-tutorial/tutorial_general_201.png
[202]: ./media/saml-tutorial/tutorial_general_202.png
[203]: ./media/saml-tutorial/tutorial_general_203.png

