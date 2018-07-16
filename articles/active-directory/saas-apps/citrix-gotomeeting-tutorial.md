---
title: 'Samouczek: Integracja usługi Azure Active Directory z GoToMeeting | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory oraz usługi GoToMeeting.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: bcaf19f2-5809-4e1c-acbc-21a8d3498ccf
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/02/2018
ms.author: jeedes
ms.openlocfilehash: b62b3b7f9f3bfd55237ed4d894954a0bde48e7fc
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39043724"
---
# <a name="tutorial-azure-active-directory-integration-with-gotomeeting"></a>Samouczek: Integracja usługi Azure Active Directory z GoToMeeting

W tym samouczku dowiesz się, jak zintegrować GoToMeeting z usługą Azure Active Directory (Azure AD).

Integrowanie GoToMeeting z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do GoToMeeting.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do GoToMeeting (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą GoToMeeting, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- GoToMeeting logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie GoToMeeting z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-gotomeeting-from-the-gallery"></a>Dodawanie GoToMeeting z galerii
Aby skonfigurować integrację GoToMeeting w usłudze Azure AD, należy dodać GoToMeeting z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać GoToMeeting z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **GoToMeeting**, wybierz opcję **GoToMeeting** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![GoToMeeting na liście wyników](./media/citrix-gotomeeting-tutorial/tutorial_gotomeeting_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą GoToMeeting w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w GoToMeeting dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w GoToMeeting musi można ustanowić.

Usługi GoToMeeting przypisywanie wartości **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD jednokrotne logowanie w usłudze GoToMeeting, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego GoToMeeting](#create-a-gotomeeting-test-user)**  — aby odpowiednikiem Britta Simon w GoToMeeting połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji GoToMeeting.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z GoToMeeting, wykonaj następujące czynności:**

1. W witrynie Azure portal na **GoToMeeting** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/citrix-gotomeeting-tutorial/tutorial_gotomeeting_samlbase.png)

3. Na **GoToMeeting domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![GoToMeeting domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/citrix-gotomeeting-tutorial/tutorial_gotomeeting_url.png)

    W **identyfikator** pole tekstowe, wpisz adres URL: `https://authentication.logmeininc.com/saml/sp`

4. Kliknij przycisk **Pokaż zaawansowane adres URL konfiguracji** i skonfigurować poniższe adresy URL

    **Adres URL logowania** (Zachowaj to puste)
    
    **Adres URL odpowiedzi**: `https://authentication.logmeininc.com/saml/acs`
    
    **RelayState**:
    
    - W przypadku aplikacji GoToMeeting Użyj `https://global.gotomeeting.com`
    
    - Aby uzyskać obsługę usług GoToTraining należy użyć `https://global.gototraining.com`
    
    - W przypadku GoToWebinar Użyj `https://global.gotowebinar.com` 
    
    - W przypadku GoToAssist Użyj `https://app.gotoassist.com`
    
5. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/citrix-gotomeeting-tutorial/tutorial_general_400.png)

6. W oknie innej przeglądarki, zaloguj się do Twojej [GoToMeeting organizacja Center](https://organization.logmeininc.com/). Użytkownik jest monitowany o potwierdzenie, że dostawca tożsamości został zaktualizowany

7. Zaznacz pole wyboru "Moje dostawca tożsamości został zaktualizowany o nową domenę". Kliknij przycisk **gotowe** po zakończeniu.


> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/citrix-gotomeeting-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/citrix-gotomeeting-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/citrix-gotomeeting-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/citrix-gotomeeting-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-gotomeeting-test-user"></a>Tworzenie użytkownika testowego GoToMeeting

W tej sekcji użytkownika o nazwie Britta Simon jest tworzony w GoToMeeting. GoToMeeting obsługę just-in-time, który jest domyślnie włączona.

Brak elementu akcji dla Ciebie w tej sekcji. Jeśli użytkownik jeszcze nie istnieje w GoToMeeting, nowy katalog jest tworzony podczas próby uzyskania dostępu GoToMeeting.

> [!NOTE]
> Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej GoToMeeting](https://support.logmeininc.com/gotomeeting).

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do GoToMeeting.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon GoToMeeting, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **GoToMeeting**.

    ![Link GoToMeeting na liście aplikacji](./media/citrix-gotomeeting-tutorial/tutorial_gotomeeting_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka GoToMeeting w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji GoToMeeting.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie Aprowizowania użytkowników](https://docs.microsoft.com/azure/active-directory/active-directory-saas-citrixgotomeeting-provisioning-tutorial)


<!--Image references-->

[1]: ./media/gotomeeting-tutorial/tutorial_general_01.png
[2]: ./media/gotomeeting-tutorial/tutorial_general_02.png
[3]: ./media/gotomeeting-tutorial/tutorial_general_03.png
[4]: ./media/gotomeeting-tutorial/tutorial_general_04.png

[100]: ./media/gotomeeting-tutorial/tutorial_general_100.png

[200]: ./media/gotomeeting-tutorial/tutorial_general_200.png
[201]: ./media/gotomeeting-tutorial/tutorial_general_201.png
[202]: ./media/gotomeeting-tutorial/tutorial_general_202.png
[203]: ./media/gotomeeting-tutorial/tutorial_general_203.png

