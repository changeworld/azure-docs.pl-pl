---
title: 'Samouczek: Raporty wydatków Integracja usługi Azure Active Directory za pomocą N2F - | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i N2F — raporty wydatków.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: f56d53d7-5a08-490a-bfb9-78fefc2751ec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3166a4e19bc137d57b97dbb516e7037228e1c020
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56188821"
---
# <a name="tutorial-azure-active-directory-integration-with-n2f---expense-reports"></a>Samouczek: Raporty wydatków Integracja usługi Azure Active Directory za pomocą N2F-

W tym samouczku dowiesz się, jak zintegrować N2F — raporty wydatków w usłudze Azure Active Directory (Azure AD).

Zintegrowanie N2F — raporty wydatków w usłudze Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do N2F — raporty wydatków.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do N2F — raporty wydatków (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Wymagania wstępne

Do konfigurowania integracji z usługą Azure AD z N2F — raporty wydatków, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- N2F — raporty wydatków pojedynczego logowania jednokrotnego włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie N2F — raporty wydatków z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-n2f---expense-reports-from-the-gallery"></a>Dodawanie N2F — raporty wydatków z galerii

Aby skonfigurować integrację N2F - raportów wydatków w usłudze Azure AD, musisz dodać N2F - raportów z galerii z listą zarządzanych aplikacji SaaS wydatków.

**Aby dodać N2F — raporty wydatków z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

4. W polu wyszukiwania wpisz **N2F — raporty wydatków**, wybierz opcję **N2F — raporty wydatków** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![N2F — raporty wydatków na liście wyników](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i test usługi Azure AD logowanie jednokrotne za pomocą N2F — raporty wydatków użytkownika testowego w oparciu o nazwie "Britta Simon".

Logowanie jednokrotne do pracy usługi Azure AD musi wiedzieć, jakie użytkownik odpowiednika w N2F — raporty wydatków jest dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w N2F — raporty wydatków powinien być określony.

Do konfigurowania i testowania usługi Azure AD logowanie jednokrotne za pomocą N2F — raporty wydatków, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
3. **Utwórz N2F — raporty wydatków testowe użytkownika** — aby odpowiednikiem Britta Simon w N2F — raporty wydatków, które jest połączone z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w sieci N2F — raporty wydatków w aplikacji.

**Aby skonfigurować usługę Azure AD w logowanie jednokrotne za pomocą N2F — raporty wydatków, wykonaj następujące czynności:**

1. W witrynie Azure portal na **N2F — raporty wydatków** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.

    ![Okno dialogowe rejestracji jednokrotnej](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_samlbase.png)

3. Na **N2F — raporty wydatków domena i adresy URL** sekcji, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb, użytkownik nie musi wykonywać żadnych czynności, ponieważ aplikacja jest już wstępnie zintegrowane z usługą Azure.

    ![N2F — raporty wydatków, domena i adresy URL pojedynczy informacje logowania jednokrotnego](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_url1.png)

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![N2F — raporty wydatków, domena i adresy URL pojedynczy informacje logowania jednokrotnego](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_url2.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL: `https://www.n2f.com/app/`

5. Na **certyfikat podpisywania SAML** sekcji, kliknij przycisk kopiowania, aby skopiować **adres Url metadanych Federacji aplikacji** i wklej go w Notatniku.

    ![Link do pobierania certyfikatu](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_certificate.png)

6. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/n2f-expensereports-tutorial/tutorial_general_400.png)

7. Na **N2F — raporty wydatków w konfiguracji** , kliknij przycisk **skonfigurować N2F — raporty wydatków** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki SAML** z **krótki przewodnik po sekcji.**

    ![Raporty wydatków N2F — Konfiguracja](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_configure.png)

8. W oknie przeglądarki internetowej innej Zaloguj się do Twojej N2F - witryny firmy raportów wydatków jako administrator.

9. Kliknij pozycję **ustawienia** , a następnie wybierz **ustawienia zaawansowane** z listy rozwijanej.

    ![Raporty wydatków N2F — Konfiguracja](./media/n2f-expensereports-tutorial/configure1.png)

10. Wybierz **ustawienia konta** kartę.

    ![Raporty wydatków N2F — Konfiguracja](./media/n2f-expensereports-tutorial/configure2.png)

11. Wybierz **uwierzytelniania** , a następnie wybierz **+ Dodaj metodę uwierzytelniania** kartę.

    ![Raporty wydatków N2F — Konfiguracja](./media/n2f-expensereports-tutorial/configure3.png)

12. Wybierz **SAML Microsoft Office 365** jako metodę uwierzytelniania.

    ![Raporty wydatków N2F — Konfiguracja](./media/n2f-expensereports-tutorial/configure4.png)

13. Na **metodę uwierzytelniania** sekcji, wykonaj następujące czynności:

    ![Raporty wydatków N2F — Konfiguracja](./media/n2f-expensereports-tutorial/configure5.png)

    a. W **identyfikator jednostki** pola tekstowego, Wklej **identyfikator jednostki SAML** wartości, które zostały skopiowane z witryny Azure portal.

    b. W **adres URL metadanych** pola tekstowego, Wklej **adres Url metadanych Federacji aplikacji** wartości, które zostały skopiowane z witryny Azure portal.

    c. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/n2f-expensereports-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/n2f-expensereports-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/n2f-expensereports-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/n2f-expensereports-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.

### <a name="create-a-n2f---expense-reports-test-user"></a>Utwórz N2F — raporty wydatków testowe użytkownika

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do N2F — raporty wydatków one musi być obsługiwana w N2F — raporty wydatków. W przypadku N2F — raporty wydatków aprowizacji to zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do Twojej N2F - witryny firmy raportów wydatków jako administrator.

2. Kliknij pozycję **ustawienia** , a następnie wybierz **ustawienia zaawansowane** z listy rozwijanej.

   ![N2F - wydatków Dodaj użytkownika](./media/n2f-expensereports-tutorial/configure1.png)

3. Wybierz **użytkowników** kartę z panelu nawigacyjnym po lewej stronie.

    ![Raporty wydatków N2F — Konfiguracja](./media/n2f-expensereports-tutorial/user1.png)

4. Wybierz **+ nowy użytkownik** kartę.

   ![Raporty wydatków N2F — Konfiguracja](./media/n2f-expensereports-tutorial/user2.png)

5. Na **użytkownika** sekcji, wykonaj następujące czynności:

    ![Raporty wydatków N2F — Konfiguracja](./media/n2f-expensereports-tutorial/user3.png)

    a. W **adres E-mail** polu tekstowym wprowadź adres e-mail użytkownika, takich jak **brittasimon@contoso.com**.

    b. W **imię** polu tekstowym Wprowadź imię użytkownika, takich jak **Britta**.

    c. W **nazwa** polu tekstowym wprowadź nazwę użytkownika, takich jak **BrittaSimon**.

    d. Wybierz **roli, Menedżer bezpośrednich (N + 1)**, i **dzielenia** zgodnie z wymaganiami organizacji.

    e. Kliknij przycisk **sprawdzania poprawności i wysłać zaproszenie**.

    > [!NOTE]
    > Jeśli podczas dodawania użytkownika występują wszelkich problemów, skontaktuj się z pomocą [N2F — raporty wydatków zespołu pomocy technicznej](mailto:support@n2f.com)

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do N2F — raporty wydatków.

![Przypisanie roli użytkownika][200]

**Aby przypisać Britta Simon N2F — raporty wydatków, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji wybierz **N2F — raporty wydatków**.

    ![Raporty wydatków N2F - łącze na liście aplikacji](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu N2F — raporty wydatków kafelka w panelu dostępu, możesz należy pobrać automatycznie zalogowanych do Twojej N2F — raporty wydatków w aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/n2f-expensereports-tutorial/tutorial_general_01.png
[2]: ./media/n2f-expensereports-tutorial/tutorial_general_02.png
[3]: ./media/n2f-expensereports-tutorial/tutorial_general_03.png
[4]: ./media/n2f-expensereports-tutorial/tutorial_general_04.png

[100]: ./media/n2f-expensereports-tutorial/tutorial_general_100.png

[200]: ./media/n2f-expensereports-tutorial/tutorial_general_200.png
[201]: ./media/n2f-expensereports-tutorial/tutorial_general_201.png
[202]: ./media/n2f-expensereports-tutorial/tutorial_general_202.png
[203]: ./media/n2f-expensereports-tutorial/tutorial_general_203.png

