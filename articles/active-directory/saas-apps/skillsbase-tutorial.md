---
title: 'Samouczek: Integracja usługi Azure Active Directory z umiejętności Base | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Base umiejętności.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 237d90c4-8243-4f80-a305-b5ad9204159e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8bc0353453cf5fe689eec398f6a7d73fb356b178
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56190844"
---
# <a name="tutorial-azure-active-directory-integration-with-skills-base"></a>Samouczek: Integracja usługi Azure Active Directory z Base umiejętności

W tym samouczku dowiesz się, jak zintegrować Base umiejętności za pomocą usługi Azure Active Directory (Azure AD).

Integrowanie umiejętności podstawowej z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do podstawowej umiejętności.
- Użytkowników, aby automatycznie uzyskać zalogowanych do podstawowej umiejętności (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Do konfigurowania integracji z usługą Azure AD z Base umiejętności, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Podstawa umiejętności logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie podstawowej umiejętności za pomocą galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-skills-base-from-the-gallery"></a>Dodawanie podstawowej umiejętności za pomocą galerii
Aby skonfigurować integrację podstawy umiejętności w usłudze Azure AD, należy dodać Base umiejętności z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać podstawowy umiejętności z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

4. W polu wyszukiwania wpisz **Base umiejętności**, wybierz opcję **Base umiejętności** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Podstawa umiejętności na liście wyników](./media/skillsbase-tutorial/tutorial_skillsbase_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Base umiejętności oparte na użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w podstawowym umiejętności do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w podstawowym umiejętności musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego przy użyciu podstawowej umiejętności, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
3. **[Tworzenie użytkownika testowego Base umiejętności](#create-a-skills-base-test-user)**  — aby odpowiednikiem Britta Simon w podstawowym umiejętności, połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Base umiejętności.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Base umiejętności, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Base umiejętności** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/skillsbase-tutorial/tutorial_skillsbase_samlbase.png)

3. Na **umiejętności Base domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Domena podstawowa umiejętności i adresy URL pojedynczego logowania jednokrotnego informacji](./media/skillsbase-tutorial/tutorial_skillsbase_url.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://app.skills-base.com/o/<customer-unique-key>`

    > [!NOTE] 
    > Adres URL logowania można uzyskać z poziomu aplikacji Base umiejętności. . Zaloguj się jako Administrator i przejść do administratora -> Ustawienia -> wystąpienie szczegółów -> skrótu. Skopiuj adres URL logowania jednokrotnego, a następnie wklej je powyżej pola tekstowego.

4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/skillsbase-tutorial/tutorial_skillsbase_certificate.png) 

5. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/skillsbase-tutorial/tutorial_general_400.png)

6. W oknie przeglądarki internetowej innej, zaloguj się do podstawowej umiejętności jako Administrator zabezpieczeń.

7. Z menu po lewej stronie w obszarze **administratora** kliknij **uwierzytelniania**.

    ![Administrator](./media/skillsbase-tutorial/tutorial_skillsbase_auth.png)

8. Na **uwierzytelniania** strony, wybierz opcję jednokrotne logowanie jako **SAML 2**.

    ![Pojedynczej precyzji](./media/skillsbase-tutorial/tutorial_skillsbase_single.png)

9. Na **uwierzytelniania** strony, wykonaj następujące czynności:

    ![Pojedynczej precyzji](./media/skillsbase-tutorial/tutorial_skillsbase_save.png)

    a. Kliknij pozycję **metadanych aktualizacji tożsamości** znajdujący się obok **stan** opcji, a następnie wklej zawartość XML metadanych, który został pobrany z witryny Azure portal w określonym polu tekstowym.

    > [!Note]
    > Możesz również walidować metadanych tożsamości za pośrednictwem **modułu sprawdzania poprawności metadanych** narzędzie jako wyróżniane na powyższym zrzucie ekranu.

    b. Kliknij pozycję **Zapisz**.
    
### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/skillsbase-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/skillsbase-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/skillsbase-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/skillsbase-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-skills-base-test-user"></a>Tworzenie użytkownika testowego Base umiejętności

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon w podstawowym umiejętności. Podstawa umiejętności obsługę just-in-time, który jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Nowy użytkownik jest tworzony podczas próby dostępu Base umiejętności, jeśli go jeszcze nie istnieje.

>[!Note]
>Jeśli potrzebujesz ręcznie utworzyć użytkownika, postępuj zgodnie z instrukcjami [tutaj](http://wiki.skills-base.net/index.php?title=Adding_people_and_enabling_them_to_log_in).

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do podstawowej umiejętności.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon Base umiejętności, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **Base umiejętności**.

    ![Link Base umiejętności, na liście aplikacji](./media/skillsbase-tutorial/tutorial_skillsbase_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Base umiejętności w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Base umiejętności.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/skillsbase-tutorial/tutorial_general_01.png
[2]: ./media/skillsbase-tutorial/tutorial_general_02.png
[3]: ./media/skillsbase-tutorial/tutorial_general_03.png
[4]: ./media/skillsbase-tutorial/tutorial_general_04.png

[100]: ./media/skillsbase-tutorial/tutorial_general_100.png

[200]: ./media/skillsbase-tutorial/tutorial_general_200.png
[201]: ./media/skillsbase-tutorial/tutorial_general_201.png
[202]: ./media/skillsbase-tutorial/tutorial_general_202.png
[203]: ./media/skillsbase-tutorial/tutorial_general_203.png

