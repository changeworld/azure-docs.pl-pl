---
title: 'Samouczek: Integracji Azure Active Directory przy użyciu kopii zapasowej końcowego Autotask | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i tworzenie kopii zapasowych Autotask punktu końcowego.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 9f55319e-895b-4130-8460-71713f25ed04
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: jeedes
ms.openlocfilehash: 0cbdb297f7f92c247295f11df459fe682ccebf47
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36220571"
---
# <a name="tutorial-azure-active-directory-integration-with-autotask-endpoint-backup"></a>Samouczek: Integracji Azure Active Directory przy użyciu kopii zapasowej końcowego Autotask

Z tego samouczka dowiesz się integrowanie Autotask punktu końcowego w kopii zapasowej z usługi Azure Active Directory (Azure AD).

Integrowanie Autotask punktu końcowego w kopii zapasowej z usługi Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do narzędzia Kopia zapasowa Autotask punktu końcowego.
- Umożliwia użytkownikom automatycznie pobrać zalogowane Autotask Backup punktu końcowego (logowanie jednokrotne) z konta usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu kopii zapasowej końcowego Autotask, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Kopii zapasowej końcowego Autotask logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym.
Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie punktu końcowego Autotask tworzenia kopii zapasowej z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-autotask-endpoint-backup-from-the-gallery"></a>Dodawanie punktu końcowego Autotask tworzenia kopii zapasowej z galerii
Aby skonfigurować integrację usługi Azure AD Autotask punktu końcowego w kopii zapasowej, należy dodać punktu końcowego Autotask tworzenia kopii zapasowej z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać punkt końcowy Autotask tworzenia kopii zapasowej z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]

3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Autotask punktu końcowego w kopii zapasowej**, wybierz pozycję **kopii zapasowej końcowego Autotask** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Kopia zapasowa punktu końcowego Autotask na liście wyników](./media/autotaskendpointbackup-tutorial/tutorial_autotaskendpointbackup_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD logowania jednokrotnego przy użyciu kopii zapasowej końcowego Autotask w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednik w programie Kopia zapasowa Autotask punktu końcowego jest dla użytkownika, w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązane użytkownika w programie Kopia zapasowa Autotask punktu końcowego musi określone.

Aby skonfigurować i przetestować usługi Azure AD logowania jednokrotnego przy użyciu kopii zapasowej końcowego Autotask, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego kopii zapasowej końcowego Autotask](#create-a-autotask-endpoint-backup-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Autotask kopii zapasowej punktu końcowego, która jest połączona z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne do aplikacji kopii zapasowej końcowego Autotask.

**Aby skonfigurować usługi Azure AD logowania jednokrotnego przy użyciu kopii zapasowej końcowego Autotask, wykonaj następujące czynności:**

1. W portalu Azure na **kopii zapasowej końcowego Autotask** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.

    ![Okno dialogowe rejestracji jednokrotnej](./media/autotaskendpointbackup-tutorial/tutorial_autotaskendpointbackup_samlbase.png)

3. Na **Autotask punktu końcowego kopii zapasowej domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Adresy URL i domeny kopii zapasowej końcowego Autotask pojedynczy informacje logowania jednokrotnego](./media/autotaskendpointbackup-tutorial/tutorial_autotaskendpointbackup_url.png)

    a. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<subdomain>.backup.autotask.net/singlesignon/saml/metadata`

    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<subdomain>.backup.autotask.net/singlesignon/saml/SSO`

    > [!NOTE]
    > Wartości te nie są prawdziwe. Rzeczywisty identyfikator i adres URL odpowiedzi, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej kopii zapasowej końcowego Autotask](https://backup.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) uzyskać te wartości.

4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/autotaskendpointbackup-tutorial/tutorial_autotaskendpointbackup_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/autotaskendpointbackup-tutorial/tutorial_general_400.png)

6. Skonfigurować logowanie jednokrotne w **kopii zapasowej końcowego Autotask** stronie, musisz wysłać pobrany **XML metadanych** do [zespołem pomocy technicznej usługi tworzenia kopii zapasowej Autotask punktu końcowego](https://backup.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm). To ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML one wartość.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/autotaskendpointbackup-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/autotaskendpointbackup-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/autotaskendpointbackup-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/autotaskendpointbackup-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
  
### <a name="create-a-autotask-endpoint-backup-test-user"></a>Tworzenie kopii zapasowej końcowego Autotask użytkownika testowego

W tej sekcji utworzysz użytkownika o nazwie Simona Britta Autotask punktu końcowego w kopii zapasowej. Praca z [zespołem pomocy technicznej kopii zapasowej końcowego Autotask](https://backup.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) Aby dodać użytkowników do tworzenia kopii zapasowej Autotask punktu końcowego platformy. Użytkownicy muszą utworzyć i aktywowana, aby użyć rejestracji jednokrotnej.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do wykonania kopii zapasowej Autotask punktu końcowego.

![Przypisanie roli użytkownika][200]

**Aby przypisać Simona Britta do tworzenia kopii zapasowej Autotask punktu końcowego, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji zaznacz **kopii zapasowej końcowego Autotask**.

    ![Łącze Autotask punktu końcowego w kopii zapasowej na liście aplikacji](./media/autotaskendpointbackup-tutorial/tutorial_autotaskendpointbackup_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Autotask punktu końcowego w kopii zapasowej w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji kopii zapasowej końcowego Autotask.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/autotaskendpointbackup-tutorial/tutorial_general_01.png
[2]: ./media/autotaskendpointbackup-tutorial/tutorial_general_02.png
[3]: ./media/autotaskendpointbackup-tutorial/tutorial_general_03.png
[4]: ./media/autotaskendpointbackup-tutorial/tutorial_general_04.png

[100]: ./media/autotaskendpointbackup-tutorial/tutorial_general_100.png

[200]: ./media/autotaskendpointbackup-tutorial/tutorial_general_200.png
[201]: ./media/autotaskendpointbackup-tutorial/tutorial_general_201.png
[202]: ./media/autotaskendpointbackup-tutorial/tutorial_general_202.png
[203]: ./media/autotaskendpointbackup-tutorial/tutorial_general_203.png

