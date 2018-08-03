---
title: 'Samouczek: Integracja usługi Azure Active Directory z pakietem CX przewiduje | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i przewiduje się pakiet CX.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 5f4b7830-6186-4d17-b77b-504d4192bfde
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2018
ms.author: jeedes
ms.openlocfilehash: b288bcbe14050c0f764f348d5e20186570e32866
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39442204"
---
# <a name="tutorial-azure-active-directory-integration-with-foresee-cx-suite"></a>Samouczek: Integracja usługi Azure Active Directory z pakietem przewiduje CX

W tym samouczku dowiesz się, jak zintegrować przewiduje CX Suite z usługą Azure Active Directory (Azure AD).

Integrowanie zestawu CX przewiduje się z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do zestawu CX przewidzieć.
- Użytkowników, aby automatycznie uzyskać zalogowanych do przewiduje Suite CX (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z pakietem CX przewiduje się, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Przewiduje się Suite CX logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym.
Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie pakietu CX przewiduje się za pomocą galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-foresee-cx-suite-from-the-gallery"></a>Dodawanie pakietu CX przewiduje się za pomocą galerii
Aby skonfigurować integrację pakietu CX przewiduje się do usługi Azure AD, należy dodać pakiet CX przewiduje się z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać pakiet CX przewiduje się z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]

1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

1. W polu wyszukiwania wpisz **przewiduje Suite CX**, wybierz opcję **przewiduje Suite CX** z panelu wynik następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Pakiet CX przewiduje się na liście wyników](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą zestawu CX przewiduje oparte na użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w zestawie CX przewiduje się do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w zestawie CX przewiduje się musi nawiązać.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z pakietem CX przewiduje się, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego przewiduje Suite CX](#create-a-foresee-cx-suite-test-user)**  — aby odpowiednikiem Britta Simon pakietu przewiduje CX połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji pakietu CX przewiduje się.

**Aby skonfigurować usługi Azure AD logowanie jednokrotne z pakietem CX przewiduje się, wykonaj następujące czynności:**

1. W witrynie Azure portal na **przewiduje Suite CX** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.

    ![Okno dialogowe rejestracji jednokrotnej](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_samlbase.png)

1. Na **przewiduje CX Suite domena i adresy URL** sekcji, jeśli masz **plik metadanych usługodawcy**, wykonaj następujące czynności:

    ![Przewiduje się CX Suite domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/foreseecxsuite-tutorial/upload.png)

    a. Kliknij przycisk **przekazywania pliku metadanych**.

    ![Przewiduje się CX Suite domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/foreseecxsuite-tutorial/tutorial_foreseen_uploadconfig.png)

    b. Kliknij pozycję **logo folderu** wybierz plik metadanych, a następnie kliknij przycisk **przekazywanie**.

    c. Po pomyślnym ukończeniu przekazywania **plik metadanych usługodawcy** **identyfikator** wartość get automatycznie wypełnione w **przewiduje CX Suite domena i adresy URL** sekcji pole tekstowe, jak pokazano poniżej:

    ![Przewiduje się CX Suite domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/foreseecxsuite-tutorial/urlupload.png)

1. Jeśli nie masz **plik metadanych usługodawcy**, wykonaj następujące czynności:

    ![Przewiduje się CX Suite domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL: `https://cxsuite.foresee.com/`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://www.okta.com/saml2/service-provider/<UniqueID>`

    > [!NOTE]
    > Wartość identyfikatora nie jest prawdziwe. Zaktualizuj tę wartość przy użyciu rzeczywistego identyfikatora. Skontaktuj się z pomocą [zespołem pomocy technicznej przewiduje CX pakietu klienta](mailto:support@foresee.com) aby zyskać tę wartość.

1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_certificate.png)

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/foreseecxsuite-tutorial/tutorial_general_400.png)

1. Do konfigurowania logowania jednokrotnego na **przewiduje Suite CX** stronie, musisz wysłać pobrany **XML metadanych** do [przewiduje Suite CX zespołem pomocy technicznej](mailto:support@foresee.com). Ustawiają to ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/foreseecxsuite-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/foreseecxsuite-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/foreseecxsuite-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/foreseecxsuite-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.

### <a name="create-a-foresee-cx-suite-test-user"></a>Tworzenie użytkownika testowego przewiduje Suite CX

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w zestawie CX przewidzieć. Praca z [przewiduje Suite CX zespołem pomocy technicznej](mailto:support@foresee.com) można dodać użytkowników lub domeny, który jest wymagany do listy dozwolonych na platformie przewiduje Suite CX. Jeśli domena jest dodawany przez zespół, użytkownicy będą automatycznie aprowizowany platformę przewiduje Suite CX. Użytkownicy muszą być tworzone i aktywowana, aby używać logowania jednokrotnego.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do udzielania dostępu do zestawu CX przewiduje się za pomocą platformy Azure logowania jednokrotnego.

![Przypisanie roli użytkownika][200]

**Aby przypisać Britta Simon przewiduje Suite CX, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

1. Na liście aplikacji wybierz **przewiduje Suite CX**.

    ![Link Suite CX przewiduje się na liście aplikacji](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_app.png)

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Suite CX przewiduje się w panelu dostępu, możesz należy pobrać automatycznie zalogowanych do przewiduje CX pakietu aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/foreseecxsuite-tutorial/tutorial_general_01.png
[2]: ./media/foreseecxsuite-tutorial/tutorial_general_02.png
[3]: ./media/foreseecxsuite-tutorial/tutorial_general_03.png
[4]: ./media/foreseecxsuite-tutorial/tutorial_general_04.png

[100]: ./media/foreseecxsuite-tutorial/tutorial_general_100.png

[200]: ./media/foreseecxsuite-tutorial/tutorial_general_200.png
[201]: ./media/foreseecxsuite-tutorial/tutorial_general_201.png
[202]: ./media/foreseecxsuite-tutorial/tutorial_general_202.png
[203]: ./media/foreseecxsuite-tutorial/tutorial_general_203.png

