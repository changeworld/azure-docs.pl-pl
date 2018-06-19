---
title: 'Samouczek: Integracji Azure Active Directory z logowania jednokrotnego SAML insygnia | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i logowania jednokrotnego SAML insygnia.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 828c981c-c3dd-4eb2-8699-0f732baa43f6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/28/2017
ms.author: jeedes
ms.openlocfilehash: 5e87c331161e299eeba88202e7c7e9882192d9de
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35942639"
---
# <a name="tutorial-azure-active-directory-integration-with-insignia-saml-sso"></a>Samouczek: Integracji Azure Active Directory z logowania jednokrotnego SAML insygnia

Z tego samouczka dowiesz się integrowanie logowania jednokrotnego SAML insygnia w usłudze Azure Active Directory (Azure AD).

Integracja z usługą Azure AD logowania jednokrotnego SAML insygnia zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do logowania jednokrotnego SAML insygnia.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do logowania jednokrotnego SAML insygnia (logowanie jednokrotne) z konta usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z logowania jednokrotnego SAML insygnia, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Logowania jednokrotnego SAML insygnia logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie logowania jednokrotnego SAML insygnia z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-insignia-saml-sso-from-the-gallery"></a>Dodawanie logowania jednokrotnego SAML insygnia z galerii
Aby skonfigurować integrację logowania jednokrotnego SAML insygnia do usługi Azure AD, należy dodać logowania jednokrotnego SAML insygnia z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać logowania jednokrotnego SAML insygnia z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **logowania jednokrotnego SAML insygnia**, wybierz pozycję **logowania jednokrotnego SAML insygnia** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Insygnia logowania jednokrotnego SAML na liście wyników](./media/insigniasamlsso-tutorial/tutorial_insigniasamlsso_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z logowania jednokrotnego SAML insygnia w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w insygnia logowania jednokrotnego SAML jest dla użytkownika, w usłudze Azure AD. Innymi słowy łącze relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w logowania jednokrotnego SAML insygnia musi się.

W insygnia logowania jednokrotnego SAML, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z logowania jednokrotnego SAML insygnia, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego logowania jednokrotnego SAML insygnia](#create-an-insignia-saml-sso-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta logowania jednokrotnego SAML insygnia połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji insygnia logowania jednokrotnego SAML.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z logowania jednokrotnego SAML insygnia, wykonaj następujące czynności:**

1. W portalu Azure na **logowania jednokrotnego SAML insygnia** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/insigniasamlsso-tutorial/tutorial_insigniasamlsso_samlbase.png)

3. Na **adresy URL i domena logowania jednokrotnego SAML insygnia** sekcji, wykonaj następujące czynności:

    ![Adresy URL i domena logowania jednokrotnego SAML insygnia pojedynczy informacje logowania jednokrotnego](./media/insigniasamlsso-tutorial/tutorial_insigniasamlsso_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca:
    | |
    |--|
    | `https://<customername>.insigniails.com/ils` |
    | `https://<customername>.insigniails.com/` |
    | `https://<customername>.insigniailsusa.com/ ` |

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<customername>.insigniailsusa.com/<uniqueid>`

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL logowania i identyfikator, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej klienta logowania jednokrotnego SAML insygnia](http://www.insigniasoftware.com/insignia/Techsupport.aspx) uzyskać te wartości. 
 

4. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/insigniasamlsso-tutorial/tutorial_insigniasamlsso_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/insigniasamlsso-tutorial/tutorial_general_400.png)

6. Na **konfiguracji logowania jednokrotnego SAML insygnia** kliknij **skonfigurować insygnia logowania jednokrotnego SAML** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adresu URL i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfiguracja logowania jednokrotnego SAML insygnia](./media/insigniasamlsso-tutorial/tutorial_insigniasamlsso_configure.png) 

7. Skonfigurować logowanie jednokrotne w **logowania jednokrotnego SAML insygnia** stronie, musisz wysłać pobrany **certyfikatu (Base64)**, **Sign-Out adresu URL i SAML pojedynczy znak na adres URL usługi** do [Zespołem pomocy technicznej usługi logowania jednokrotnego SAML insygnia](http://www.insigniasoftware.com/insignia/Techsupport.aspx). To ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML one wartość.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/insigniasamlsso-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/insigniasamlsso-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/insigniasamlsso-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/insigniasamlsso-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-an-insignia-saml-sso-test-user"></a>Tworzenie użytkownika testowego insygnia logowania jednokrotnego SAML

W tej sekcji należy utworzyć użytkownika o nazwie Simona Britta insygnia biblioteki systemu. Praca z [zespołem pomocy technicznej systemu biblioteki insygnia](http://www.insigniasoftware.com/insignia/Techsupport.aspx) Aby dodać użytkowników do platformy insygnia biblioteki systemu.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji musisz włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do logowania jednokrotnego SAML insygnia.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta do logowania jednokrotnego SAML insygnia, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **logowania jednokrotnego SAML insygnia**.

    ![Łącze insygnia logowania jednokrotnego SAML na liście aplikacji](./media/insigniasamlsso-tutorial/tutorial_insigniasamlsso_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka logowania jednokrotnego SAML insygnia w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do logowania jednokrotnego SAML insygnia aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/insigniasamlsso-tutorial/tutorial_general_01.png
[2]: ./media/insigniasamlsso-tutorial/tutorial_general_02.png
[3]: ./media/insigniasamlsso-tutorial/tutorial_general_03.png
[4]: ./media/insigniasamlsso-tutorial/tutorial_general_04.png

[100]: ./media/insigniasamlsso-tutorial/tutorial_general_100.png

[200]: ./media/insigniasamlsso-tutorial/tutorial_general_200.png
[201]: ./media/insigniasamlsso-tutorial/tutorial_general_201.png
[202]: ./media/insigniasamlsso-tutorial/tutorial_general_202.png
[203]: ./media/insigniasamlsso-tutorial/tutorial_general_203.png

