---
title: 'Samouczek: Integracji Azure Active Directory z SpaceIQ | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i SpaceIQ.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 5b55ae29-491f-401f-9299-d3a6b64a1b99
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: jeedes
ms.openlocfilehash: d69a0f801f1bf3fbd44514289dff4b9a95305ba4
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36227925"
---
# <a name="tutorial-azure-active-directory-integration-with-spaceiq"></a>Samouczek: Integracji Azure Active Directory z SpaceIQ

Z tego samouczka dowiesz się integrowanie SpaceIQ z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD SpaceIQ zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do SpaceIQ.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do SpaceIQ (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z SpaceIQ, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- SpaceIQ logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie SpaceIQ z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-spaceiq-from-the-gallery"></a>Dodawanie SpaceIQ z galerii
Aby skonfigurować integrację usługi Azure AD SpaceIQ, należy dodać SpaceIQ z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać SpaceIQ z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **SpaceIQ**, wybierz pozycję **SpaceIQ** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![SpaceIQ na liście wyników](./media/spaceiq-tutorial/tutorial_spaceiq_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji możesz skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z SpaceIQ na podstawie użytkownika testowego, nazywany "Britta Simona".

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w SpaceIQ jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w SpaceIQ musi się.

W SpaceIQ, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z SpaceIQ, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego SpaceIQ](#create-a-spaceiq-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta SpaceIQ połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji SpaceIQ.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z SpaceIQ, wykonaj następujące czynności:**

1. W portalu Azure na **SpaceIQ** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/spaceiq-tutorial/tutorial_spaceiq_samlbase.png)

3. Na **SpaceIQ domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Adresy URL i domeny SpaceIQ pojedynczy informacje logowania jednokrotnego](./media/spaceiq-tutorial/tutorial_spaceiq_url.png)

    a. W **identyfikator** tekstowym, wpisz adres URL: `https://api.spaceiq.com`

    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://api.spaceiq.com/saml/<instanceid>/callback`

    > [!NOTE] 
    > Rzeczywisty adres URL odpowiedzi i identyfikator, który znajduje się w dalszej części tego samouczka, należy zaktualizować te wartości.
 
4. Na **certyfikat podpisywania SAML** kliknij **(Base64 certyfikatu)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/spaceiq-tutorial/tutorial_spaceiq_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/spaceiq-tutorial/tutorial_general_400.png)

6. Na **konfiguracji SpaceIQ** , kliknij przycisk **skonfigurować SpaceIQ** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki SAML** z **sekcji krótkimi opisami.**

    ![Konfiguracja SpaceIQ](./media/spaceiq-tutorial/tutorial_spaceiq_configure.png) 

7.  Otwórz nowe okno przeglądarki, a następnie zaloguj się do środowiska SpaceIQ jako administrator.

8. Gdy użytkownik jest zalogowany, kliknij znak układanki u góry po prawej, a następnie kliknij przycisk **"Integracji"**

    ![Ustawienia konta](./media/spaceiq-tutorial/setting1.png) 

9. W obszarze **Inicjowanie obsługi wszystkich & logowania jednokrotnego**, kliknij **Azure** Kafelek, aby dodać wystąpienia Azure jako dostawca tożsamości.

    ![Ikona SAML](./media/spaceiq-tutorial/setting2.png)

10. W **logowania jednokrotnego** okna dialogowego wykonaj następujące czynności:

    ![Ustawienia uwierzytelniania SAML](./media/spaceiq-tutorial/setting3.png)

    a. W **adres URL wystawcy SAML** Wklej **identyfikator jednostki SAML** wartości skopiowanych z okna konfiguracji aplikacji usługi Azure AD.
    
    b. Kopiuj **SAML URL punktu końcowego wywołania zwrotnego (tylko do odczytu)** i Wklej wartość w **adres URL odpowiedzi służący** pole w portalu Azure w ramach SpaceIQ **domeny i adres URL** sekcji.
    
    c. Kopiuj **identyfikatora URI odbiorców SAML (tylko do odczytu)** i Wklej wartość w **identyfikator** pole w portalu Azure w ramach SpaceIQ **domeny i adres URL** sekcji.

    d. Otwórz plik pobranego certyfikatu w programie Notatnik, skopiuj zawartość, a następnie wklej go w **certyfikatu X.509** pole.
    
    e. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/spaceiq-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/spaceiq-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/spaceiq-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/spaceiq-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
  
### <a name="create-a-spaceiq-test-user"></a>Tworzenie użytkownika testowego SpaceIQ

W tej sekcji należy utworzyć użytkownika o nazwie Simona Britta w SpaceIQ. Praca [zespołem pomocy technicznej SpaceIQ](mailto:eng@spaceiq.com) Aby dodać użytkowników do platformy SpaceIQ. Użytkownicy muszą utworzyć i aktywowana, aby użyć rejestracji jednokrotnej.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu SpaceIQ.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta SpaceIQ, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **SpaceIQ**.

    ![Łącze SpaceIQ na liście aplikacji](./media/spaceiq-tutorial/tutorial_spaceiq_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka SpaceIQ w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji SpaceIQ.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/spaceiq-tutorial/tutorial_general_01.png
[2]: ./media/spaceiq-tutorial/tutorial_general_02.png
[3]: ./media/spaceiq-tutorial/tutorial_general_03.png
[4]: ./media/spaceiq-tutorial/tutorial_general_04.png

[100]: ./media/spaceiq-tutorial/tutorial_general_100.png

[200]: ./media/spaceiq-tutorial/tutorial_general_200.png
[201]: ./media/spaceiq-tutorial/tutorial_general_201.png
[202]: ./media/spaceiq-tutorial/tutorial_general_202.png
[203]: ./media/spaceiq-tutorial/tutorial_general_203.png

