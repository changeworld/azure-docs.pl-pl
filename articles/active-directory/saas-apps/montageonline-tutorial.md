---
title: 'Samouczek: Integracja usługi Azure Active Directory z usługą montażowy Online | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i montażowy Online.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 5c5e8c6f-e4fb-43fe-8841-e371f568ebed
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: jeedes
ms.openlocfilehash: 2ddc4b6f792aa02308a95aa5464f0506cbd5c2ec
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36215270"
---
# <a name="tutorial-azure-active-directory-integration-with-montage-online"></a>Samouczek: Integracja usługi Azure Active Directory z usługą montażowy Online

Z tego samouczka dowiesz się integrowanie montażowy Online z usługą Azure Active Directory (Azure AD).

Integrowanie montażowy Online z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do montażowy Online.
- Umożliwia użytkownikom automatycznie pobrać zalogowane montażowy online (logowanie jednokrotne) z konta usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z montażowy Online, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Montażowy Online logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie montażowy Online z poziomu galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-montage-online-from-the-gallery"></a>Dodawanie montażowy Online z poziomu galerii
Aby skonfigurować integrację montażowy online z usługą Azure AD, należy dodać do listy zarządzanych aplikacji SaaS montażowy Online z poziomu galerii.

**Aby dodać montażowy Online z poziomu galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **montażowy Online**, wybierz pozycję **montażowy Online** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Montażowy Online na liście wyników](./media/montageonline-tutorial/tutorial_montageonline_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z montażowy Online na podstawie użytkownika testowego, nazywany "Britta Simona".

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w trybie Online montażowy jest dla użytkownika, w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i danemu użytkownikowi w trybie Online montażowy musi określone.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z montażowy Online, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego montażowy Online](#create-a-montage-online-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta montażowy Online, które jest połączone z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w trybie Online montażowy aplikacji.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z montażowy Online, wykonaj następujące czynności:**

1. W portalu Azure na **montażowy Online** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/montageonline-tutorial/tutorial_montageonline_samlbase.png)

3. Na **montażowy Online domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Adresy URL i domen montażowy pojedynczy informacje logowania jednokrotnego](./media/montageonline-tutorial/tutorial_montageonline_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca:

    W środowisku produkcyjnym: `https://<subdomain>.montageonline.co.nz/`

    Dla środowiska testowego: `https://build-<subdomain>.montageonline.co.nz/`

    b. W **identyfikator** tekstowym, wpisz adres URL:

    W środowisku produkcyjnym: `MOL_Azure`

    Dla środowiska testowego: `MOL_Azure_Build`

    > [!NOTE] 
    > Wartość adres URL logowania nie jest prawdziwe. Zaktualizuj tę wartość z adresem URL logowania rzeczywistych. Skontaktuj się z [zespołem pomocy technicznej Online klienta montażowy](https://www.montage.co.nz/contact-us/) można uzyskać wartość. 
 
4. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/montageonline-tutorial/tutorial_montageonline_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/montageonline-tutorial/tutorial_general_400.png)

6. Na **konfiguracji Online montażowy** , kliknij przycisk **skonfigurować montażowy Online** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adres URL, identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfiguracja Online montażowy](./media/montageonline-tutorial/tutorial_montageonline_configure.png) 

7. Aby skonfigurować logowanie jednokrotne w **montażowy Online** stronie, musisz wysłać pobrany **certyfikatu (Base64), adres URL Sign-Out identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** do [montażowy Zespół pomocy technicznej online](https://www.montage.co.nz/contact-us/). To ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML one wartość.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/montageonline-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/montageonline-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/montageonline-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/montageonline-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-montage-online-test-user"></a>Tworzenie użytkownika testowego montażowy Online

W tej sekcji utworzysz użytkownika o nazwie Simona Britta w montażowy w trybie Online. Praca z [zespół pomocy technicznej Online montażowy](https://www.montage.co.nz/contact-us/) Aby dodać użytkowników do platformy montażowy Online. Użytkownicy muszą być tworzone i aktywowana, aby użyć rejestracji jednokrotnej

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do montażowy Online.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta montażowy Online, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **montażowy Online**.

    ![Łącze montażowy Online na liście aplikacji](./media/montageonline-tutorial/tutorial_montageonline_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka montażowy Online w panelu dostępu użytkownik powinien uzyskać automatycznie zalogowane montażowy Online aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/montageonline-tutorial/tutorial_general_01.png
[2]: ./media/montageonline-tutorial/tutorial_general_02.png
[3]: ./media/montageonline-tutorial/tutorial_general_03.png
[4]: ./media/montageonline-tutorial/tutorial_general_04.png

[100]: ./media/montageonline-tutorial/tutorial_general_100.png

[200]: ./media/montageonline-tutorial/tutorial_general_200.png
[201]: ./media/montageonline-tutorial/tutorial_general_201.png
[202]: ./media/montageonline-tutorial/tutorial_general_202.png
[203]: ./media/montageonline-tutorial/tutorial_general_203.png

