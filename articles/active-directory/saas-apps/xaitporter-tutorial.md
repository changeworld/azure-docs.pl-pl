---
title: 'Samouczek: Integracji Azure Active Directory z XaitPorter | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i XaitPorter.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d33c7cb7-0550-425b-882a-619a713a71b7
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2017
ms.author: jeedes
ms.openlocfilehash: ef43f7cc91467d4f9b7ab2f4cbc8c35e9b7f4586
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36211797"
---
# <a name="tutorial-azure-active-directory-integration-with-xaitporter"></a>Samouczek: Integracji Azure Active Directory z XaitPorter

Z tego samouczka dowiesz się integrowanie XaitPorter z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD XaitPorter zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do XaitPorter.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do XaitPorter (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z XaitPorter, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- XaitPorter logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie XaitPorter z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-xaitporter-from-the-gallery"></a>Dodawanie XaitPorter z galerii
Aby skonfigurować integrację usługi Azure AD XaitPorter, należy dodać XaitPorter z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać XaitPorter z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **XaitPorter**, wybierz pozycję **XaitPorter** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![XaitPorter na liście wyników](./media/xaitporter-tutorial/tutorial_xaitporter_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z XaitPorter w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w XaitPorter jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w XaitPorter musi się.

W XaitPorter, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z XaitPorter, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego XaitPorter](#create-a-xaitporter-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta XaitPorter połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji XaitPorter.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z XaitPorter, wykonaj następujące czynności:**

1. W portalu Azure na **XaitPorter** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/xaitporter-tutorial/tutorial_xaitporter_samlbase.png)

3. Na **XaitPorter domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Adresy URL i domeny XaitPorter pojedynczy informacje logowania jednokrotnego](./media/xaitporter-tutorial/tutorial_xaitporter_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<subdomain>.xaitporter.com/saml/login`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<subdomain>.xaitporter.com`

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL logowania i identyfikator, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej klienta XaitPorter](https://www.xait.com/support/) uzyskać te wartości.
     
4. Na **certyfikat podpisywania SAML** sekcji, kliknij przycisk Kopiuj, aby skopiować **adres Url metadanych Federacji aplikacji** i wklej go do Notatnika. 

    ![Łącze pobierania certyfikatu](./media/xaitporter-tutorial/tutorial_xaitporter_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/xaitporter-tutorial/tutorial_general_400.png)

6. Podaj **adres IP** lub **adres Url metadanych Federacji aplikacji** do [SmartRecruiters obsługuje zespołu](https://www.smartrecruiters.com/about-us/contact-us/), dzięki czemu XaitPorter można zapewnić, że adres IP jest dostępny z sieci Wystąpienie XaitPorter konfigurowania listy dozwolonych adresów IP w bok. 

7. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy XaitPorter.

8. Polecenie **Admin**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/xaitporter-tutorial/user1.png)

9. Wybierz **Zarządzanie rejestracji jednokrotnej** z **konfiguracji systemu** listy rozwijanej.

    ![Konfigurowanie rejestracji jednokrotnej](./media/xaitporter-tutorial/user2.png)

10. W **zarządzanie jednym logowania** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/xaitporter-tutorial/user3.png)

    a. Wybierz **włączyć uwierzytelnianie rejestracji jednokrotnej**.

    b. W **ustawień dostawcy tożsamości** pole tekstowe, Wklej **adres Url metadanych Federacji aplikacji** , które zostały skopiowane z portalu Azure i kliknij przycisk **pobrać**.

    c. Wybierz **włączyć Autocreation użytkowników**.

    d. Kliknij przycisk **OK**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/xaitporter-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/xaitporter-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/xaitporter-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/xaitporter-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-xaitporter-test-user"></a>Tworzenie użytkownika testowego XaitPorter

W tej sekcji należy utworzyć użytkownika o nazwie Simona Britta w XaitPorter. Praca z [zespołem pomocy technicznej klienta XaitPorter](https://www.xait.com/support/) Aby dodać użytkowników do platformy XaitPorter. Użytkownicy muszą utworzyć i aktywowana, aby użyć rejestracji jednokrotnej. 

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu XaitPorter.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta XaitPorter, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **XaitPorter**.

    ![Łącze XaitPorter na liście aplikacji](./media/xaitporter-tutorial/tutorial_xaitporter_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka XaitPorter w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji XaitPorter.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/xaitporter-tutorial/tutorial_general_01.png
[2]: ./media/xaitporter-tutorial/tutorial_general_02.png
[3]: ./media/xaitporter-tutorial/tutorial_general_03.png
[4]: ./media/xaitporter-tutorial/tutorial_general_04.png

[100]: ./media/xaitporter-tutorial/tutorial_general_100.png

[200]: ./media/xaitporter-tutorial/tutorial_general_200.png
[201]: ./media/xaitporter-tutorial/tutorial_general_201.png
[202]: ./media/xaitporter-tutorial/tutorial_general_202.png
[203]: ./media/xaitporter-tutorial/tutorial_general_203.png

