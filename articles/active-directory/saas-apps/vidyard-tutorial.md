---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Vidyard | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Vidyard.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: bed7df23-6e13-4e7c-b4cc-53ed4804664d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d25520f88869025f7e262d81295ee64f8e12d21
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56163598"
---
# <a name="tutorial-azure-active-directory-integration-with-vidyard"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Vidyard

W tym samouczku dowiesz się, jak zintegrować Vidyard w usłudze Azure Active Directory (Azure AD).

Integrowanie Vidyard z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Vidyard.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do Vidyard (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Vidyard, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Vidyard logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Vidyard z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-vidyard-from-the-gallery"></a>Dodawanie Vidyard z galerii
Aby skonfigurować integrację Vidyard w usłudze Azure AD, należy dodać Vidyard z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Vidyard z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

1. W polu wyszukiwania wpisz **Vidyard**, wybierz opcję **Vidyard** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Vidyard na liście wyników](./media/vidyard-tutorial/tutorial_vidyard_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Vidyard w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Vidyard do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Vidyard musi można ustanowić.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Vidyard, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
1. **[Tworzenie użytkownika testowego Vidyard](#create-a-vidyard-test-user)**  — aby odpowiednikiem Britta Simon w Vidyard połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Vidyard.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Vidyard, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Vidyard** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/vidyard-tutorial/tutorial_vidyard_samlbase.png)

1. Na **Vidyard domena i adresy URL** sekcji, wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![Vidyard domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/vidyard-tutorial/tutorial_vidyard_url2.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://secure.vidyard.com/sso/saml/<unique id>/metadata`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://secure.vidyard.com/sso/saml/<unique id>/consume`

1. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![Vidyard domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/vidyard-tutorial/tutorial_vidyard_url1.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://secure.vidyard.com/sso/saml/<unique id>/login`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Te wartości zostaną zaktualizowane o rzeczywisty identyfikator, adres URL odpowiedzi i adres URL logowania, które zostało wyjaśnione w dalszej części tego samouczka

1. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link do pobierania certyfikatu](./media/vidyard-tutorial/tutorial_vidyard_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/vidyard-tutorial/tutorial_general_400.png)

1. Na **konfiguracji Vidyard** , kliknij przycisk **skonfigurować Vidyard** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfiguracja Vidyard](./media/vidyard-tutorial/tutorial_vidyard_configure.png)

1. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy Vidyard oprogramowania.

1. Na pulpicie nawigacyjnym Vidyard wybierz **grupy** > **zabezpieczeń**

    ![Konfiguracja Vidyard](./media/vidyard-tutorial/configure1.png)

1. Kliknij przycisk **nowy profil** kartę.

    ![Konfiguracja Vidyard](./media/vidyard-tutorial/configure2.png)

1. W **plik konfiguracji SAML** sekcji, wykonaj następujące czynności:

    ![Konfiguracja Vidyard](./media/vidyard-tutorial/configure3.png)

    a. Wprowadź nazwę profilu ogólnego w **nazwa profilu** pola tekstowego.

    b. Kopiuj **strony logowania użytkownika logowania jednokrotnego** wartość i wklej go w **adres URL logowania** polu tekstowym w **sekcji Vidyard domena i adresy URL** w witrynie Azure portal.

    c. Kopiuj **adres URL usługi ACS** wartość i wklej go w **adres URL odpowiedzi** polu tekstowym w **sekcji Vidyard domena i adresy URL** w witrynie Azure portal.

    d. Kopiuj **adres URL wystawcy/metadanych** wartość i wklej go w **identyfikator** polu tekstowym w **Vidyard domena i adresy URL sekcji** w witrynie Azure portal.

    e. Otwórz plik certyfikatu pobrany z witryny Azure portal w programie Notatnik i wklej go do **certyfikat X.509** pola tekstowego.

    f. W **adresu URL punktu końcowego protokołu SAML** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL** skopiowane z witryny Azure portal.

    g. Kliknij pozycję **Potwierdź**.

1. Na karcie Logowanie jednokrotne wybierz **przypisać** obok istniejącego profilu

    ![Konfiguracja Vidyard](./media/vidyard-tutorial/configure4.png)

    > [!NOTE]
    > Po utworzeniu profilu programu rejestracji Jednokrotnej, należy ją przypisać do żadnych grup, dla której użytkownicy wymagają dostępu za pośrednictwem platformy Azure. Jeśli użytkownik nie istnieje w grupie, do którego zostały przypisane, Vidyard automatycznie utworzyć konto użytkownika i przypisywanie ich roli w czasie rzeczywistym.

1. Wybierz grupę organizacji, który jest widoczny w **grup dostępnych do przypisania**.

    ![Konfiguracja Vidyard](./media/vidyard-tutorial/configure5.png)

1. Widać przypisanych grup w ramach **aktualnie przypisane grupy**. Wybierz rolę dla grupy, zgodnie z Twojej organizacji i kliknij przycisk **Potwierdź**.

    ![Konfiguracja Vidyard](./media/vidyard-tutorial/configure6.png)

    > [!NOTE]
    > Aby uzyskać więcej informacji, zobacz [tego dokumentu](https://knowledge.vidyard.com/saml-single-sign-on-authentication/saml-based-single-sign-on-sso-in-vidyard).

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/vidyard-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/vidyard-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/vidyard-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/vidyard-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-vidyard-test-user"></a>Tworzenie użytkownika testowego Vidyard

Celem tej sekcji jest, aby utworzyć użytkownika o nazwie Britta Simon w Vidyard. Vidyard obsługę just-in-time, który jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Nowy użytkownik jest tworzony podczas próby dostępu Vidyard, jeśli go jeszcze nie istnieje.
>[!Note]
>Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej Vidyard](mailto:support@vidyard.com).

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Vidyard.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon Vidyard, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Vidyard**.

    ![Link Vidyard na liście aplikacji](./media/vidyard-tutorial/tutorial_vidyard_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Vidyard w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Vidyard.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/vidyard-tutorial/tutorial_general_01.png
[2]: ./media/vidyard-tutorial/tutorial_general_02.png
[3]: ./media/vidyard-tutorial/tutorial_general_03.png
[4]: ./media/vidyard-tutorial/tutorial_general_04.png

[100]: ./media/vidyard-tutorial/tutorial_general_100.png

[200]: ./media/vidyard-tutorial/tutorial_general_200.png
[201]: ./media/vidyard-tutorial/tutorial_general_201.png
[202]: ./media/vidyard-tutorial/tutorial_general_202.png
[203]: ./media/vidyard-tutorial/tutorial_general_203.png

