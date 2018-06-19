---
title: 'Samouczek: Integracji Azure Active Directory z bezpiecznego DOSTARCZANIA | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i bezpiecznego DOSTARCZANIA.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: fccd5668-fe6f-4e6d-a9ce-ba4f321c33d1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: jeedes
ms.openlocfilehash: c8f058005fe908ec6c8785318730ddecbbb19a88
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35953273"
---
# <a name="tutorial-azure-active-directory-integration-with-secure-deliver"></a>Samouczek: Integracji Azure Active Directory z bezpiecznego DOSTARCZANIA

Z tego samouczka dowiesz się integrowanie SECURE DOSTARCZANIA w usłudze Azure Active Directory (Azure AD).

Integrowanie SECURE DOSTARCZANIA z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do bezpiecznego DOSTARCZANIA
- Umożliwia użytkownikom automatycznie pobrać zalogowane SECURE dostarczać (logowanie jednokrotne) z konta usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z bezpiecznego DOSTARCZANIA, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- SECURE DOSTARCZANIA logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie SECURE DOSTARCZANIA z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-secure-deliver-from-the-gallery"></a>Dodawanie SECURE DOSTARCZANIA z galerii
Aby skonfigurować integrację z bezpiecznego DOSTARCZANIA w usłudze Azure Active Directory, należy dodać SECURE DOSTARCZANIA z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać SECURE DOSTARCZANIA z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **SECURE DOSTARCZANIA**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/securedeliver-tutorial/tutorial_securedeliver_search.png)

5. W panelu wyników wybierz **SECURE DOSTARCZANIA**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/securedeliver-tutorial/tutorial_securedeliver_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z bezpiecznego DOSTARCZANIA, w oparciu o nazwie "Britta Simona" użytkownika testowego.

Do rejestracji jednokrotnej do pracy usługi Azure AD musi ustalić użytkownika odpowiednika w bezpieczny DOSTARCZANIA do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i danemu użytkownikowi w bezpieczny DOSTARCZANIA musi określone.

W bezpieczny DOSTARCZANIA, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Do konfigurowania i testowania usługi Azure AD rejestracji jednokrotnej z bezpiecznego DOSTARCZANIA, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego SECURE DOSTARCZANIA](#creating-a-secure-deliver-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta SECURE DOSTARCZANIA połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji bezpiecznego DOSTARCZANIA.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z bezpiecznego DOSTARCZANIA, wykonaj następujące czynności:**

1. W portalu Azure na **SECURE DOSTARCZANIA** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/securedeliver-tutorial/tutorial_securedeliver_samlbase.png)

3. Na **SECURE DOSTARCZANIA domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/securedeliver-tutorial/tutorial_securedeliver_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<companyname>.i-securedeliver.jp/sd/<tenantname>/jsf/login/sso`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<companyname>.i-securedeliver.jp/sd/<tenantname>/postResponse`

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL logowania i identyfikator, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej SECURE klienta dostarczenia](mailto:iw-sd-support@fujifilm.com) uzyskać te wartości. 
 
4. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/securedeliver-tutorial/tutorial_securedeliver_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/securedeliver-tutorial/tutorial_general_400.png)

6. Na **SECURE konfiguracji DOSTARCZANIA** , kliknij przycisk **skonfigurować bezpiecznego DOSTARCZANIA** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adres URL, identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/securedeliver-tutorial/tutorial_securedeliver_configure.png) 

7. Skonfigurować logowanie jednokrotne w **SECURE DOSTARCZANIA** stronie, musisz wysłać pobrany **certyfikatu (Base64)**, **Sign-Out adres URL, identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** do [SECURE DOSTARCZANIA zespołem pomocy technicznej](mailto:iw-sd-support@fujifilm.com). To ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML one wartość.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/securedeliver-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/securedeliver-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/securedeliver-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/securedeliver-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-secure-deliver-test-user"></a>Tworzenie użytkownika testowego SECURE DOSTARCZANIA

Celem tej sekcji jest utworzenie użytkownika o nazwie Simona Britta w bezpieczny DOSTARCZANIA. Praca z [SECURE DOSTARCZANIA zespołem pomocy technicznej](mailto:iw-sd-support@fujifilm.com) Aby dodać użytkowników w ramach konta SECURE DOSTARCZANIA.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do bezpiecznego DOSTARCZANIA.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta do bezpiecznego DOSTARCZANIA, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **SECURE DOSTARCZANIA**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/securedeliver-tutorial/tutorial_securedeliver_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

Celem tej sekcji służy do testowania konfigurację usługi Azure AD z logowania jednokrotnego za pomocą panelu dostępu.  

Po kliknięciu kafelka SECURE DOSTARCZANIA w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do bezpiecznego DOSTARCZANIA aplikacji.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/securedeliver-tutorial/tutorial_general_01.png
[2]: ./media/securedeliver-tutorial/tutorial_general_02.png
[3]: ./media/securedeliver-tutorial/tutorial_general_03.png
[4]: ./media/securedeliver-tutorial/tutorial_general_04.png

[100]: ./media/securedeliver-tutorial/tutorial_general_100.png

[200]: ./media/securedeliver-tutorial/tutorial_general_200.png
[201]: ./media/securedeliver-tutorial/tutorial_general_201.png
[202]: ./media/securedeliver-tutorial/tutorial_general_202.png
[203]: ./media/securedeliver-tutorial/tutorial_general_203.png

