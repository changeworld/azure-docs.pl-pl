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
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 104caa001f773c48a708879f3f9b50b74190fc74
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2019
ms.locfileid: "57449227"
---
# <a name="tutorial-azure-active-directory-integration-with-foresee-cx-suite"></a>Samouczek: Integracja usługi Azure Active Directory z pakietem przewiduje CX

W tym samouczku dowiesz się, jak zintegrować przewiduje CX Suite z usługą Azure Active Directory (Azure AD).

Integrowanie zestawu CX przewiduje się z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do zestawu CX przewidzieć.
- Użytkowników, aby automatycznie uzyskać zalogowanych do przewiduje Suite CX (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z pakietem CX przewiduje się, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Przewiduje się Suite CX logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym.
Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie pakietu CX przewiduje się za pomocą galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-foresee-cx-suite-from-the-gallery"></a>Dodawanie pakietu CX przewiduje się za pomocą galerii
Aby skonfigurować integrację pakietu CX przewiduje się do usługi Azure AD, należy dodać pakiet CX przewiduje się z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać pakiet CX przewiduje się z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]

1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

1. W polu wyszukiwania wpisz **przewiduje Suite CX**, wybierz opcję **przewiduje Suite CX** z panelu wynik następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Pakiet CX przewiduje się na liście wyników](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą zestawu CX przewiduje oparte na użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w zestawie CX przewiduje się do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w zestawie CX przewiduje się musi nawiązać.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z pakietem CX przewiduje się, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
1. **[Tworzenie użytkownika testowego przewiduje Suite CX](#create-a-foresee-cx-suite-test-user)**  — aby odpowiednikiem Britta Simon pakietu przewiduje CX połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji pakietu CX przewiduje się.

**Aby skonfigurować usługi Azure AD logowanie jednokrotne z pakietem CX przewiduje się, wykonaj następujące czynności:**

1. W witrynie Azure portal na **przewiduje Suite CX** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.

    ![Okno dialogowe rejestracji jednokrotnej](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_samlbase.png)

1. Na **przewiduje CX Suite domena i adresy URL** sekcji, jeśli masz **plik metadanych usługodawcy**, wykonaj następujące czynności:

    ![Przewiduje się CX Suite domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/foreseecxsuite-tutorial/upload.png)

    a. Kliknij pozycję **Przekaż plik metadanych**.

    ![Przewiduje się CX Suite domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/foreseecxsuite-tutorial/tutorial_foreseen_uploadconfig.png)

    b. Kliknij **logo folderu**, aby wybrać plik metadanych, a następnie kliknij pozycję **Przekaż**.

    c. Po pomyślnym ukończeniu przekazywania **plik metadanych usługodawcy** **identyfikator** wartość get automatycznie wypełnione w **przewiduje CX Suite domena i adresy URL** sekcji pole tekstowe, jak pokazano poniżej:

    ![Przewiduje się CX Suite domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/foreseecxsuite-tutorial/urlupload.png)

1. Jeśli nie masz **plik metadanych usługodawcy**, wykonaj następujące czynności:

    ![Przewiduje się CX Suite domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL: `https://cxsuite.foresee.com/`

    b. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://www.okta.com/saml2/service-provider/<UniqueID>`

    > [!NOTE]
    > Wartość identyfikatora nie jest prawdziwa. Zaktualizuj tę wartość za pomocą rzeczywistego identyfikatora. Skontaktuj się z pomocą [zespołem pomocy technicznej przewiduje CX pakietu klienta](mailto:support@foresee.com) aby zyskać tę wartość.

1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_certificate.png)

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/foreseecxsuite-tutorial/tutorial_general_400.png)

1. Do konfigurowania logowania jednokrotnego na **przewiduje Suite CX** stronie, musisz wysłać pobrany **XML metadanych** do [przewiduje Suite CX zespołem pomocy technicznej](mailto:support@foresee.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

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

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w zestawie CX przewidzieć. Praca z [przewiduje Suite CX zespołem pomocy technicznej](mailto:support@foresee.com) można dodać użytkowników lub domeny, który jest wymagany do listy dozwolonych na platformie przewiduje Suite CX. Jeśli domena jest dodawany przez zespół, użytkownicy będą automatycznie aprowizowany platformę przewiduje Suite CX. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

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

* [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](tutorial-list.md)
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

