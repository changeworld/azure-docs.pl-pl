---
title: 'Samouczek: Integracja usługi Azure Active Directory z ochrony przed rozproszonymi LMS | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i ochrony przed rozproszonymi LMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: ba9f1b3d-a4a0-4ff7-b0e7-428e0ed92142
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: jeedes
ms.openlocfilehash: 066ae92056e4b80b6627b371d6ebeb3235b2781d
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39043782"
---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>Samouczek: Integracja usługi Azure Active Directory z ochrony przed rozproszonymi LMS

W tym samouczku dowiesz się, jak zintegrować ochrony przed rozproszonymi LMS za pomocą usługi Azure Active Directory (Azure AD).

Integracja ochrony przed rozproszonymi LMS z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do ochrony przed rozproszonymi LMS.
- Aby umożliwić użytkownikom automatyczne logowanie do ochrony przed rozproszonymi LMS (przy użyciu logowania jednokrotnego) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji, witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat oprogramowania jako usługi (SaaS) integracji aplikacji z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z ochrony przed rozproszonymi LMS, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Ochrony przed rozproszonymi LMS logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Nie zalecamy korzystania do środowiska produkcyjnego na potrzeby tego samouczka.

Aby przetestować czynności w ramach tego samouczka, wykonaj te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

* Dodawanie ochrony przed rozproszonymi LMS z galerii
* Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="add-absorb-lms-from-the-gallery"></a>Dodawanie ochrony przed rozproszonymi LMS z galerii
Aby skonfigurować integrację ochrony przed rozproszonymi LMS w usłudze Azure AD, należy dodać wchłonąć LMS z galerii z listą zarządzanych aplikacji SaaS.

Aby dodać wchłonąć LMS z galerii, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com), w okienku po lewej stronie wybierz **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**.

    ![W okienku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać aplikację, wybierz pozycję **nową aplikację** przycisku.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **ochrony przed rozproszonymi LMS**, wybierz opcję **ochrony przed rozproszonymi LMS** w wyniku panelu, a następnie wybierz **Dodaj** przycisku.

    ![Wchłonąć LMS na liście wyników](./media/absorblms-tutorial/tutorial_absorblms_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą ochrony przed rozproszonymi LMS, w oparciu o użytkownika testu o nazwie Britta Simon.

Dla logowania jednokrotnego do pracy usługi Azure AD musi wiedzieć, co użytkownik odpowiednika LMS ochrony przed rozproszonymi znajduje się w usłudze Azure AD. Innymi słowy należy ustanowić relację łącza między użytkownikiem w usłudze Azure AD i odpowiedniego użytkownika w ochrony przed rozproszonymi LMS.

Ustanowienia tej relacji łączy, przypisując *nazwy użytkownika* wartość w usłudze Azure AD jako *Username* wartość ochrony przed rozproszonymi LMS.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z ochrony przed rozproszonymi LMS, wykonaj bloki konstrukcyjne w pięć następnych sekcjach.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji ochrony przed rozproszonymi LMS.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z ochrony przed rozproszonymi LMS, wykonaj następujące czynności:

1. W witrynie Azure portal na **ochrony przed rozproszonymi LMS** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. W **logowanie jednokrotne** dialogowym **tryb** wybierz opcję **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/absorblms-tutorial/tutorial_absorblms_samlbase.png)

3. W **ochrony przed rozproszonymi LMS domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Ochrony przed rozproszonymi LMS domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/absorblms-tutorial/tutorial_absorblms_url.png)

    a. W **identyfikator** wpisz adres URL, który używa następującej składni: `https://<subdomain>.myabsorb.com/Account/SAML`.

    b. W **adres URL odpowiedzi** wpisz adres URL, który używa następującej składni: `https://<subdomain>.myabsorb.com/Account/SAML`.
     
    > [!NOTE] 
    > Te adresy URL nie są rzeczywiste wartości. Zaktualizuj je za pomocą rzeczywisty identyfikator i adresy URL odpowiedzi. Aby uzyskać te wartości, skontaktuj się z pomocą [zespołem pomocy technicznej klienta ochrony przed rozproszonymi LMS](https://www.absorblms.com/support). 

4. W **certyfikat podpisywania SAML** sekcji w **Pobierz** kolumny wybierz **XML metadanych**, a następnie zapisz plik metadanych do komputera.

    ![Link do pobierania podpisywania certyfikatu](./media/absorblms-tutorial/tutorial_absorblms_certificate.png) 

5. Wybierz pozycję **Zapisz**.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/absorblms-tutorial/tutorial_general_400.png)
    
6. W **konfiguracji ochrony przed rozproszonymi LMS** zaznacz **konfigurowania ochrony przed rozproszonymi LMS** otworzyć **Konfigurowanie logowania jednokrotnego** okna, a następnie skopiuj **adres URL wylogowania** w **krótki przewodnik po sekcji.**

    ![W okienku ochrony przed rozproszonymi LMS konfiguracji](./media/absorblms-tutorial/tutorial_absorblms_configure.png) 

7. W nowym oknie przeglądarki sieci web należy zalogować się jako administrator do ochrony przed rozproszonymi LMS witryny firmy.

8. Wybierz **konta** przycisk w prawym górnym rogu. 

    ![Przycisk konta](./media/absorblms-tutorial/1.png)

9. W okienku konta wybierz **ustawienia portalu**.

    ![Link ustawienia portalu](./media/absorblms-tutorial/2.png)
    
10. Wybierz kartę **Użytkownicy**.

    ![Na karcie Użytkownicy](./media/absorblms-tutorial/3.png)

11. Na stronie konfiguracji rejestracji jednokrotnej wykonaj następujące czynności:

    ![Na stronie konfiguracji rejestracji jednokrotnej](./media/absorblms-tutorial/4.png)

    a. W **tryb** wybierz opcję **zainicjował dostawcy tożsamości**.

    b. Otwórz w Notatniku certyfikat, który został pobrany z witryny Azure portal. Usuń **---BEGIN CERTIFICATE---** i **---END CERTIFICATE---** tagów. Następnie w **klucz** pole, Wklej pozostałej zawartości.
    
    c. W **Właściwość Id** wybierz atrybut, który został skonfigurowany jako identyfikator użytkownika w usłudze Azure AD. Na przykład jeśli *userPrincipalName* jest zaznaczona w usłudze Azure AD, wybierz **Username**.

    d. W **adres URL logowania** pole, Wklej **adres URL dostępu użytkownika** z aplikacji **właściwości** strony w witrynie Azure Portal.

    e. W **adres URL wylogowania**, Wklej **adres URL wylogowania** wartości, który został skopiowany z **Konfigurowanie logowania jednokrotnego** okna witryny Azure portal.

12. Przełącz **Zezwalaj tylko na logowanie SSO** do **na**.

    ![Przełącz tylko Zezwalaj na logowanie SSO](./media/absorblms-tutorial/5.png)

13. Wybierz **Zapisz.**

> [!TIP]
> Może odczytywać zwięzłe wersji tych instrukcji w [witryny Azure portal](https://portal.azure.com) podczas konfigurowania aplikacji. Po dodaniu aplikacji z **usługi Active Directory** > **aplikacje dla przedsiębiorstw** zaznacz **logowania jednokrotnego** karty i dostępu do osadzonego Dokumentacja usługi za pośrednictwem **konfiguracji** sekcji u dołu. Aby uzyskać więcej informacji, zobacz [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji opisano tworzenie użytkownika testowego Britta Simon w witrynie Azure portal.

![Tworzenie użytkownika testowego usługi Azure AD][100]

Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:

1. W witrynie Azure portal w okienku po lewej stronie wybierz **usługi Azure Active Directory**.

    ![Przycisk usługi Azure Active Directory](./media/absorblms-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, wybierz **użytkowników i grup** > **wszyscy użytkownicy**.
    
    !["Użytkownicy i grupy" i "All users" linki](./media/absorblms-tutorial/create_aaduser_02.png) 

3. W górnej części okna dialogowego wybierz **Dodaj**.
 
    ![Przycisk Dodaj](./media/absorblms-tutorial/create_aaduser_03.png) 

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:
 
    ![Okno dialogowe użytkownika](./media/absorblms-tutorial/create_aaduser_04.png) 

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** pole tekstowe, wpisz adres e-mail Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość w **hasło** pole.

    d. Wybierz pozycję **Utwórz**.

### <a name="create-an-absorb-lms-test-user"></a>Tworzenie użytkownika testowego ochrony przed rozproszonymi LMS

Dla użytkowników usługi Azure AD zalogować się do ochrony przed rozproszonymi LMS ich można skonfigurować w ochrony przed rozproszonymi LMS.  

W przypadku ochrony przed rozproszonymi systemu LMS konfiguracja jest zadanie ręczne.

Aby skonfigurować konto użytkownika, wykonaj następujące czynności:

1. Zaloguj się do witryny firmy ochrony przed rozproszonymi LMS jako administrator.

2. W okienku po lewej stronie wybierz **użytkowników**.

    ![Łącze ochrony przed rozproszonymi użytkowników LMS](./media/absorblms-tutorial/absorblms_users.png)

3. W **użytkowników** okienku wybierz **użytkowników**.

    ![Łącze użytkowników](./media/absorblms-tutorial/absorblms_userssub.png)

4. W **Dodaj nowe** listy rozwijanej wybierz **użytkownika**.

    ![Dodaj nowe listy rozwijanej](./media/absorblms-tutorial/absorblms_createuser.png)

5. Na **Dodaj użytkownika** wykonaj następujące czynności:

    ![Strona Dodawanie użytkownika](./media/absorblms-tutorial/user.png)

    a. W **imię** wpisz imię, takich jak **Britta**.

    b. W **nazwisko** wpisz nazwisko, takich jak **Simon**.
    
    c. W **Username** wpisz pełną nazwę, taką jak **Britta Simon**.

    d. W **hasło** wpisz hasło Britta Simon.

    e. W **Potwierdź hasło** pole, wprowadź ponownie hasło.
    
    f. Ustaw **jest aktywny** Przełącz, aby **Active**.  

6. Wybierz **Zapisz.**
 
### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć użytkownika Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do ochrony przed rozproszonymi LMS.

![Przypisanie roli użytkownika][200]

Aby przypisać użytkownika Britta Simon do ochrony przed rozproszonymi LMS, wykonaj następujące czynności:

1. W witrynie Azure portal Otwórz widok aplikacji, przejdź do widoku katalogu, a następnie wybierz **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**.

    ![Link "Wszystkie aplikacje"][201] 

2. W **aplikacje** listy wybierz **ochrony przed rozproszonymi LMS**.

    ![Link ochrony przed rozproszonymi LMS na liście aplikacji](./media/absorblms-tutorial/tutorial_absorblms_app.png) 

3. W okienku po lewej stronie wybierz **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202] 

4. Wybierz **Dodaj** a następnie w **Dodaj przydziału** okienku wybierz **użytkowników i grup**.

    ![Okienko Dodawanie przypisania][203]

5. W **użytkowników i grup** dialogowym **użytkowników** listy wybierz **Britta Simon**.

6. W **użytkowników i grup** okno dialogowe, wybierz opcję **wybierz** przycisku.

7. W **Dodaj przydziału** okno dialogowe, wybierz opcję **przypisać** przycisku.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

W panelu dostępu wybierając **ochrony przed rozproszonymi LMS** kafelka automatycznie zaloguje Cię do ochrony przed rozproszonymi LMS aplikacji. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/absorblms-tutorial/tutorial_general_01.png
[2]: ./media/absorblms-tutorial/tutorial_general_02.png
[3]: ./media/absorblms-tutorial/tutorial_general_03.png
[4]: ./media/absorblms-tutorial/tutorial_general_04.png

[100]: ./media/absorblms-tutorial/tutorial_general_100.png

[200]: ./media/absorblms-tutorial/tutorial_general_200.png
[201]: ./media/absorblms-tutorial/tutorial_general_201.png
[202]: ./media/absorblms-tutorial/tutorial_general_202.png
[203]: ./media/absorblms-tutorial/tutorial_general_203.png
