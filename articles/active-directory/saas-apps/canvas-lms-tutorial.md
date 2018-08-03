---
title: 'Samouczek: Integracja usługi Azure Active Directory z kanwy Lms | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i LMS kanwy.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: bfed291c-a33e-410d-b919-5b965a631d45
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2017
ms.author: jeedes
ms.openlocfilehash: af2c997f0842da751eb93f0788a7402fc7d144ae
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39433293"
---
# <a name="tutorial-azure-active-directory-integration-with-canvas-lms"></a>Samouczek: Integracja usługi Azure Active Directory z LMS kanwy

W tym samouczku dowiesz się, jak zintegrować kanwy przy użyciu usługi Azure Active Directory (Azure AD).

Integrowanie kanwy przy użyciu usługi Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do obszaru roboczego
- Można włączyć użytkowników, aby automatycznie uzyskać zalogowanych do obszaru roboczego (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu kanwy, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Kanwa logowania jednokrotnego włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie obszaru roboczego z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-canvas-from-the-gallery"></a>Dodawanie obszaru roboczego z galerii
Aby skonfigurować integrację kanwy w usłudze Azure AD, należy dodać obszaru roboczego z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać obszaru roboczego z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **kanwy**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/canvas-lms-tutorial/tutorial_canvaslms_search.png)

1. W panelu wyników wybierz **kanwy**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/canvas-lms-tutorial/tutorial_canvaslms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą kanwy, w oparciu o użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika na kanwie jest dla użytkownika, w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników na kanwie musi nawiązać.

Na kanwie, przypisz wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego przy użyciu kanwy, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego kanwy](#creating-a-canvas-test-user)**  — aby odpowiednikiem Britta Simon w kanwy, która jest połączona z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji kanwy.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z kanwy, wykonaj następujące czynności:**

1. W witrynie Azure portal na **kanwy** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/canvas-lms-tutorial/tutorial_canvaslms_samlbase.png)

1. Na **kanwy domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/canvas-lms-tutorial/tutorial_canvaslms_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<tenant-name>.instructure.com`

    b. W **identyfikator** polu tekstowym wpisz wartość, przy użyciu następującego wzorca: `https://<tenant-name>.instructure.com/saml2`

    > [!NOTE] 
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL logowania jednokrotnego i identyfikator. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta kanwy](https://community.canvaslms.com/community/help) do uzyskania tych wartości. 
 
1. Na **certyfikat podpisywania SAML** sekcji, skopiuj **odcisk PALCA** wartość certyfikatu.

    ![Konfigurowanie logowania jednokrotnego](./media/canvas-lms-tutorial/tutorial_canvaslms_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/canvas-lms-tutorial/tutorial_general_400.png)

1. Na **konfiguracji kanwy** , kliknij przycisk **skonfigurować kanwy** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Zmień hasło, adres URL, adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/canvas-lms-tutorial/tutorial_canvaslms_configure.png) 
 
1. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy kanwy.

1. Przejdź do **kursów \> zarządzane konta \> Microsoft**.
   
    ![Kanwa](./media/canvas-lms-tutorial/IC775990.png "kanwy")

1. W okienku nawigacji po lewej stronie wybierz **uwierzytelniania**, a następnie kliknij przycisk **Dodaj konfigurację SAML**.
   
    ![Uwierzytelnianie](./media/canvas-lms-tutorial/IC775991.png "uwierzytelniania")

1. Na stronie bieżącej integracji wykonaj następujące czynności:
   
    ![Bieżąca integracja](./media/canvas-lms-tutorial/IC775992.png "bieżącego integracji")

    a. W **identyfikator jednostki tożsamości** pola tekstowego, Wklej wartość **identyfikator jednostki SAML** skopiowanej w witrynie Azure portal.

    b. W **na adres URL dziennika** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL** skopiowanej w witrynie Azure portal.

    c. W **adresu URL wylogowania** pola tekstowego, Wklej wartość **adres URL wylogowania** skopiowanej w witrynie Azure portal.

    d. W **Link do zmiany hasła** pola tekstowego, Wklej wartość **Zmień hasło, adres URL** skopiowanej w witrynie Azure portal. 

    e. W **odcisk palca certyfikatu** pola tekstowego, Wklej **odcisk palca** wartości certyfikatów, które zostały skopiowane z witryny Azure portal.      
        
    f. Z **atrybut logowania** listy wybierz **NameID**.

    g. Z **Format identyfikatora** listy wybierz **emailAddress**.

    h. Kliknij przycisk **zapisywanie ustawień uwierzytelniania**.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/canvas-lms-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/canvas-lms-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/canvas-lms-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/canvas-lms-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-canvas-test-user"></a>Tworzenie użytkownika testowego kanwy

Aby umożliwić użytkownikom usługi Azure AD zalogować się do obszaru roboczego, musi być obsługiwana na kanwie.

W przypadku kanwy Inicjowanie obsługi użytkowników jest zadanie ręczne.

**Aby udostępnić konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do Twojej **kanwy** dzierżawy.

1. Przejdź do **kursów \> zarządzane konta \> Microsoft**.
   
   ![Kanwa](./media/canvas-lms-tutorial/IC775990.png "kanwy")

1. Kliknij przycisk **użytkowników**.
   
   ![Użytkownicy](./media/canvas-lms-tutorial/IC775995.png "użytkowników")

1. Kliknij przycisk **Dodaj nowego użytkownika**.
   
   ![Użytkownicy](./media/canvas-lms-tutorial/IC775996.png "użytkowników")

1. Na stronie Dodawanie nowego użytkownika okna dialogowego wykonaj następujące czynności:
   
   ![Dodaj użytkownika](./media/canvas-lms-tutorial/IC775997.png "Dodaj użytkownika")
   
   a. W **imię i nazwisko** polu tekstowym wprowadź nazwę użytkownika, takich jak **BrittaSimon**.

   b. W **E-mail** polu tekstowym wprowadź adres e-mail użytkownika, takich jak **brittasimon@contoso.com**.

   c. W **logowania** polu tekstowym wprowadź adres e-mail usługi Azure AD przez użytkownika, takich jak **brittasimon@contoso.com**.

   d. Wybierz **poczty E-mail użytkownika o tym tworzenie konta**.

   e. Kliknij przycisk **Dodaj użytkownika**.

>[!NOTE]
>Można użyć jakichkolwiek innych kanwy użytkownika konta tworzenie narzędzi lub interfejsów API dostarczonych przez kanwy do aprowizacji kont użytkowników usługi AAD.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do obszaru roboczego.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon do kanwy, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **kanwy**.

    ![Konfigurowanie logowania jednokrotnego](./media/canvas-lms-tutorial/tutorial_canvaslms_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka kanwy na panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji kanwy.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/canvas-lms-tutorial/tutorial_general_01.png
[2]: ./media/canvas-lms-tutorial/tutorial_general_02.png
[3]: ./media/canvas-lms-tutorial/tutorial_general_03.png
[4]: ./media/canvas-lms-tutorial/tutorial_general_04.png

[100]: ./media/canvas-lms-tutorial/tutorial_general_100.png

[200]: ./media/canvas-lms-tutorial/tutorial_general_200.png
[201]: ./media/canvas-lms-tutorial/tutorial_general_201.png
[202]: ./media/canvas-lms-tutorial/tutorial_general_202.png
[203]: ./media/canvas-lms-tutorial/tutorial_general_203.png

