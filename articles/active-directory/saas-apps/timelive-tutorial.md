---
title: 'Samouczek: Integracja usługi Azure Active Directory z TimeLive | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i TimeLive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 34123629-4ad5-465c-a4c1-8299f857e720
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: jeedes
ms.openlocfilehash: 26e70abc336b7cb13342873784aa2cb4048a47d9
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39437325"
---
# <a name="tutorial-azure-active-directory-integration-with-timelive"></a>Samouczek: Integracja usługi Azure Active Directory z TimeLive

W tym samouczku dowiesz się, jak zintegrować TimeLive w usłudze Azure Active Directory (Azure AD).

Integrowanie TimeLive z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do TimeLive.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do TimeLive (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą TimeLive, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- TimeLive logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie TimeLive z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-timelive-from-the-gallery"></a>Dodawanie TimeLive z galerii
Aby skonfigurować integrację TimeLive w usłudze Azure AD, należy dodać TimeLive z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać TimeLive z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

1. W polu wyszukiwania wpisz **TimeLive**, wybierz opcję **TimeLive** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![TimeLive na liście wyników](./media/timelive-tutorial/tutorial_timelive_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą TimeLive w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w TimeLive do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w TimeLive musi można ustanowić.

W TimeLive, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą TimeLive, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego TimeLive](#create-a-timelive-test-user)**  — aby odpowiednikiem Britta Simon w TimeLive połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji TimeLive.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z TimeLive, wykonaj następujące czynności:**

1. W witrynie Azure portal na **TimeLive** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/timelive-tutorial/tutorial_timelive_samlbase.png)

1. Na **TimeLive domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![TimeLive domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/timelive-tutorial/tutorial_timelive_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://domainname.livetecs.com/`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://domainname.livetecs.com/`

    > [!NOTE] 
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adres URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta TimeLive](mailto:support@livetecs.com) do uzyskania tych wartości. 

1. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/timelive-tutorial/tutorial_timelive_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/timelive-tutorial/tutorial_general_400.png)
    
1. Na **konfiguracji TimeLive** , kliknij przycisk **skonfigurować TimeLive** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfiguracja TimeLive](./media/timelive-tutorial/tutorial_timelive_configure.png)

1. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy TimeLive.

1. Wybierz **preferencje** w obszarze **Opcje administratora**.

    ![Konfiguracja TimeLive](./media/timelive-tutorial/configure1.png)

1. W **preferencji aplikacji** sekcji, wykonaj następujące czynności:
    
    ![Konfiguracja TimeLive](./media/timelive-tutorial/configure2.png)

    a. Wybierz **zabezpieczeń** kartę.

    b. Sprawdź **włączyć logowanie jednokrotne (SSO)** pola wyboru.

    c. Wybierz **SAML** z rozwijanego menu z pozycją **Zaloguj się przy użyciu pojedynczego logowania jednokrotnego (SSO) przy użyciu**.

    d. W **adres URL logowania jednokrotnego SAML**, Wklej **SAML pojedynczego logowania jednokrotnego usługi adresu URL** wartość, która została skopiowana tworzą witryny Azure portal.

    e. W **adres URL wylogowania zdalnego**, Wklej **adres URL wylogowania** wartość, która została skopiowana tworzą witryny Azure portal.

    f. Otwórz pobrany **certyfikat szyfrowany algorytmem base-64** z witryny Azure portal w programie Notatnik, skopiuj zawartość, a następnie wklej go do **certyfikat X.509** pola tekstowego.

    g. Kliknij przycisk **aktualizacji**.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/timelive-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/timelive-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/timelive-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/timelive-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-timelive-test-user"></a>Tworzenie użytkownika testowego TimeLive

Celem tej sekcji jest, aby utworzyć użytkownika o nazwie Britta Simon w TimeLive. TimeLive obsługę just-in-time, który jest domyślnie włączona. Brak elementu akcji dla Ciebie w tej sekcji. Nowy użytkownik jest tworzony podczas próby dostępu TimeLive, jeśli go jeszcze nie istnieje.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do TimeLive.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon TimeLive, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **TimeLive**.

    ![Link TimeLive na liście aplikacji](./media/timelive-tutorial/tutorial_timelive_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka TimeLive w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji TimeLive.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/timelive-tutorial/tutorial_general_01.png
[2]: ./media/timelive-tutorial/tutorial_general_02.png
[3]: ./media/timelive-tutorial/tutorial_general_03.png
[4]: ./media/timelive-tutorial/tutorial_general_04.png

[100]: ./media/timelive-tutorial/tutorial_general_100.png

[200]: ./media/timelive-tutorial/tutorial_general_200.png
[201]: ./media/timelive-tutorial/tutorial_general_201.png
[202]: ./media/timelive-tutorial/tutorial_general_202.png
[203]: ./media/timelive-tutorial/tutorial_general_203.png

