---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Peakon | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Peakon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a944c397-ed3f-4d45-b9b2-6d4bcb6b0a09
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e32f0ac6fcc6839bad973c3d014436d08ae43896
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56211313"
---
# <a name="tutorial-azure-active-directory-integration-with-peakon"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Peakon

W tym samouczku dowiesz się, jak zintegrować Peakon w usłudze Azure Active Directory (Azure AD).

Integrowanie Peakon z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Peakon.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do Peakon (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Peakon, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Peakon logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska usługi Azure AD w wersji próbnej, możesz skorzystać z [miesięcznej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Peakon z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-peakon-from-the-gallery"></a>Dodawanie Peakon z galerii

Aby skonfigurować integrację Peakon w usłudze Azure AD, należy dodać Peakon z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Peakon z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

4. W polu wyszukiwania wpisz **Peakon**, wybierz opcję **Peakon** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Peakon na liście wyników](./media/peakon-tutorial/tutorial_peakon_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Peakon w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Peakon do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Peakon musi można ustanowić.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Peakon, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego Peakon](#creating-a-peakon-test-user)**  — aby odpowiednikiem Britta Simon w Peakon połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Peakon.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Peakon, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Peakon** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

2. Na **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, kliknij przycisk **wybierz** dla **SAML** trybu, aby włączyć logowanie jednokrotne.

    ![Konfigurowanie logowania jednokrotnego](common/tutorial_general_301.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Konfigurowanie logowania jednokrotnego](common/editconfigure.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **dostawcę tożsamości**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki:

    ![Peakon domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/peakon-tutorial/tutorial_peakon_url.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://app.peakon.com/saml/<companyid>/metadata`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://app.peakon.com/saml/<companyid>/assert`

5. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    ![Peakon domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/peakon-tutorial/tutorial_peakon_url1.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL: `https://app.peakon.com/login`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adres URL odpowiedzi, czyli latert zostało wyjaśnione w tym samouczku.

6. W **certyfikat podpisywania SAML** kliknij **Pobierz** można pobrać **certyfikatu (Raw)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link do pobierania certyfikatu](./media/peakon-tutorial/tutorial_peakon_certificate.png) 

7. Na **Konfigurowanie Peakon** sekcji, skopiuj odpowiedni adres URL, zgodnie z wymaganiami.

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

    ![Konfiguracja Peakon](common/configuresection.png)

8. W oknie przeglądarki internetowej innej Zaloguj się w Peakon jako Administrator.

9. Na pasku menu w lewej części strony kliknij **konfiguracji**, a następnie przejdź do **integracje**.

    ![Konfiguracji](./media/peakon-tutorial/tutorial_peakon_config.png)

10. Na **integracje** kliknij na **logowania jednokrotnego**.

    ![Pojedynczej precyzji](./media/peakon-tutorial/tutorial_peakon_single.png)

11. W obszarze **logowania jednokrotnego** sekcji, kliknij pozycję **Włącz**.

    ![Włącz](./media/peakon-tutorial/tutorial_peakon_enable.png)

12. Na **logowanie jednokrotne dla pracowników za pośrednictwem protokołu SAML** sekcji, wykonaj następujące czynności:

    ![Saml](./media/peakon-tutorial/tutorial_peakon_saml.png)

    a. W **adres URL logowania do logowania jednokrotnego** pola tekstowego, Wklej wartość **adres URL logowania**, który skopiowano z witryny Azure portal.

    b. W **adres URL wylogowania logowania jednokrotnego** pola tekstowego, Wklej wartość **adres URL wylogowania**, który skopiowano z witryny Azure portal.

    c. Kliknij przycisk **Choose file** można przekazać certyfikatu, który został pobrany z witryny Azure portal, w polu certyfikatu.

    d. Kliknij przycisk **ikonę** do skopiowania **identyfikator jednostki** i Wklej **identyfikator** polu tekstowym w **podstawową konfigurację protokołu SAML** sekcji w witrynie Azure portal.

    e. Kliknij przycisk **ikonę** do skopiowania **adres URL odpowiedzi (ACS)** i Wklej **adres URL odpowiedzi** polu tekstowym w **podstawową konfigurację protokołu SAML**   sekcja w witrynie Azure portal.

    f. Kliknij pozycję **Zapisz**

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Utwórz użytkownika usługi Azure AD][100]

2. Wybierz **nowego użytkownika** w górnej części ekranu.

    ![Tworzenie użytkownika testowego usługi Azure AD](common/create_aaduser_01.png) 

3. We właściwościach użytkownika wykonaj następujące czynności.

    ![Tworzenie użytkownika testowego usługi Azure AD](common/create_aaduser_02.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    c. Wybierz **właściwości**, wybierz opcję **hasło Show** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w polu hasło.

    d. Wybierz pozycję **Utwórz**.

### <a name="creating-a-peakon-test-user"></a>Tworzenie użytkownika testowego Peakon

Dla użytkowników usługi Azure AD zarejestrować się w Peakon włączeniu musi być obsługiwana w Peakon.  
W przypadku Peakon Inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do witryny firmy Peakon jako administrator.

2. Na pasku menu w lewej części strony kliknij **konfiguracji**, a następnie przejdź do **pracowników**.

    ![Pracownik](./media/peakon-tutorial/tutorial_peakon_employee.png)

3. W prawej górnej części strony, kliknij polecenie **pracowników Dodaj**.

      ![Dodaj pracownika](./media/peakon-tutorial/tutorial_peakon_addemployee.png)

3. Na **nowego pracownika** okna dialogowego strony, wykonaj następujące czynności:

     ![Nowych pracowników](./media/peakon-tutorial/tutorial_peakon_create.png)

    a. W **nazwa** polu tekstowym wpisz imię jako **Britta** i nazwiska jako **simon**.

    b. W **E-mail** pole tekstowe, wpisz adres e-mail, takich jak **Brittasimon@contoso.com**.

    c. Kliknij przycisk **pracowników Utwórz**.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Peakon.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji wybierz **Peakon**.

    ![Konfigurowanie logowania jednokrotnego](./media/peakon-tutorial/tutorial_peakon_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. W **Dodaj przydziału** okno dialogowe, wybierz opcję **przypisać** przycisku.

### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Peakon w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Peakon.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
