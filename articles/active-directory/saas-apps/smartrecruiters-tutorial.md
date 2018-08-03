---
title: 'Samouczek: Integracja usługi Azure Active Directory z SmartRecruiters | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i SmartRecruiters.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: e96aeecd-e113-454e-89c3-58c9f44cfd4c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: jeedes
ms.openlocfilehash: b248cd7d5d45e4f91bc97a5a29476f9bfa03089d
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39426989"
---
# <a name="tutorial-azure-active-directory-integration-with-smartrecruiters"></a>Samouczek: Integracja usługi Azure Active Directory z SmartRecruiters

W tym samouczku dowiesz się, jak zintegrować SmartRecruiters w usłudze Azure Active Directory (Azure AD).

Integrowanie SmartRecruiters z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do SmartRecruiters.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do SmartRecruiters (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą SmartRecruiters, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- SmartRecruiters logowania jednokrotnego włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie SmartRecruiters z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-smartrecruiters-from-the-gallery"></a>Dodawanie SmartRecruiters z galerii
Aby skonfigurować integrację SmartRecruiters w usłudze Azure AD, należy dodać SmartRecruiters z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać SmartRecruiters z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

1. W polu wyszukiwania wpisz **SmartRecruiters**, wybierz opcję **SmartRecruiters** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![SmartRecruiters na liście wyników](./media/smartrecruiters-tutorial/tutorial_smartrecruiters_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą SmartRecruiters w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w SmartRecruiters do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w SmartRecruiters musi można ustanowić.

W SmartRecruiters, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą SmartRecruiters, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego SmartRecruiters](#create-a-smartrecruiters-test-user)**  — aby odpowiednikiem Britta Simon w SmartRecruiters połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji SmartRecruiters.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z SmartRecruiters, wykonaj następujące czynności:**

1. W witrynie Azure portal na **SmartRecruiters** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/smartrecruiters-tutorial/tutorial_smartrecruiters_samlbase.png)

1. Na **SmartRecruiters domena i adresy URL** sekcji, wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![SmartRecruiters domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/smartrecruiters-tutorial/tutorial_smartrecruiters_url.png)

    a. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://www.smartrecruiters.com/web-sso/saml/<companyname>`

    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://www.smartrecruiters.com/web-sso/saml/<companyname>/callback`

1. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![SmartRecruiters domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/smartrecruiters-tutorial/tutorial_smartrecruiters_url1.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://www.smartrecruiters.com/web-sso/saml/<companyname>/login`
     
    > [!NOTE] 
    > Te wartości są prawdziwe. Rzeczywisty identyfikator, adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta SmartRecruiters](https://www.smartrecruiters.com/about-us/contact-us/) do uzyskania tych wartości. 

1. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie Zapisz certyfikat na komputerze.

    ![Link pobierania certyfikatu](./media/smartrecruiters-tutorial/tutorial_smartrecruiters_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/smartrecruiters-tutorial/tutorial_general_400.png)
    
1. Na **konfiguracji SmartRecruiters** , kliknij przycisk **skonfigurować SmartRecruiters** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfiguracja SmartRecruiters](./media/smartrecruiters-tutorial/tutorial_smartrecruiters_configure.png) 

1. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy SmartRecruiters.

1. Przejdź do **ustawienia / Admin**.

    ![Konfiguracja SmartRecruiters](./media/smartrecruiters-tutorial/configure.png)

1. W **konfiguracji** kliknij **Usługa rejestracji Jednokrotnej w sieci Web**.

    ![Konfiguracja SmartRecruiters](./media/smartrecruiters-tutorial/configure1.png)

1. Przełącz **Włączanie logowania jednokrotnego w sieci Web**.

    ![Konfiguracja SmartRecruiters](./media/smartrecruiters-tutorial/configure2.png)

1. W **konfiguracji dostawcy tożsamości**, wykonaj następujące czynności:

    ![Konfiguracja SmartRecruiters](./media/smartrecruiters-tutorial/configure4.png)

    a. W **adres URL dostawcy tożsamości** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL** skopiowanej w witrynie Azure portal.

    b. Otwórz **certificate(Base64)** zostały pobrane z witryny Azure portal i Wklej wartość w **certyfikatu dostawcy tożsamości** pola tekstowego.

1. Kliknij przycisk **konfiguracji Usługa rejestracji Jednokrotnej w sieci Web Zapisz**.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/smartrecruiters-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/smartrecruiters-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/smartrecruiters-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/smartrecruiters-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-smartrecruiters-test-user"></a>Tworzenie użytkownika testowego SmartRecruiters

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w SmartRecruiters. Praca z [zespołu pomocy technicznej SmartRecruiters](https://www.smartrecruiters.com/about-us/contact-us/) Aby dodać użytkowników na platformie SmartRecruiters. Użytkownicy muszą być tworzone i aktywowana, aby używać logowania jednokrotnego. 

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do SmartRecruiters.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon SmartRecruiters, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **SmartRecruiters**.

    ![Link SmartRecruiters na liście aplikacji](./media/smartrecruiters-tutorial/tutorial_smartrecruiters_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka SmartRecruiters w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji SmartRecruiters.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/smartrecruiters-tutorial/tutorial_general_01.png
[2]: ./media/smartrecruiters-tutorial/tutorial_general_02.png
[3]: ./media/smartrecruiters-tutorial/tutorial_general_03.png
[4]: ./media/smartrecruiters-tutorial/tutorial_general_04.png

[100]: ./media/smartrecruiters-tutorial/tutorial_general_100.png

[200]: ./media/smartrecruiters-tutorial/tutorial_general_200.png
[201]: ./media/smartrecruiters-tutorial/tutorial_general_201.png
[202]: ./media/smartrecruiters-tutorial/tutorial_general_202.png
[203]: ./media/smartrecruiters-tutorial/tutorial_general_203.png

