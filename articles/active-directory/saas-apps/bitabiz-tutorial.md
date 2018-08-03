---
title: 'Samouczek: Integracja usługi Azure Active Directory z BitaBIZ | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i BitaBIZ.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 1a51e677-c62b-4aee-9c61-56926aaaa899
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: jeedes
ms.openlocfilehash: 2a05a4f1b9162a69e074bf6243236df48c8ce536
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39429666"
---
# <a name="tutorial-azure-active-directory-integration-with-bitabiz"></a>Samouczek: Integracja usługi Azure Active Directory z BitaBIZ

W tym samouczku dowiesz się, jak zintegrować BitaBIZ w usłudze Azure Active Directory (Azure AD).

Integrowanie BitaBIZ z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do BitaBIZ.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do BitaBIZ (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą BitaBIZ, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- BitaBIZ logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie BitaBIZ z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-bitabiz-from-the-gallery"></a>Dodawanie BitaBIZ z galerii
Aby skonfigurować integrację BitaBIZ w usłudze Azure AD, należy dodać BitaBIZ z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać BitaBIZ z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

1. W polu wyszukiwania wpisz **BitaBIZ**, wybierz opcję **BitaBIZ** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![BitaBIZ na liście wyników](./media/bitabiz-tutorial/tutorial_bitabiz_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą BitaBIZ w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w BitaBIZ do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w BitaBIZ musi można ustanowić.

W BitaBIZ, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą BitaBIZ, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego BitaBIZ](#create-a-bitabiz-test-user)**  — aby odpowiednikiem Britta Simon w BitaBIZ połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji BitaBIZ.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z BitaBIZ, wykonaj następujące czynności:**

1. W witrynie Azure portal na **BitaBIZ** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/bitabiz-tutorial/tutorial_bitabiz_samlbase.png)

1. Na **BitaBIZ domena i adresy URL** sekcji, jeśli chcesz skonfigurować aplikację w trybie zainicjował dostawcy tożsamości należy wykonać następujące czynności:

    ![BitaBIZ domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/bitabiz-tutorial/tutorial_bitabiz_url.png)

    W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://www.bitabiz.com/<instanceId>`

    > [!NOTE] 
    > Wartość w powyższy adres URL jest tylko demonstracyjne. Zaktualizuj wartość za pomocą rzeczywisty identyfikator, który zostało wyjaśnione w dalszej części tego samouczka.

1. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![BitaBIZ domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/bitabiz-tutorial/tutorial_bitabiz_url1.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL: `https://www.bitabiz.com/dashboard`

1. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/bitabiz-tutorial/tutorial_bitabiz_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/bitabiz-tutorial/tutorial_general_400.png)
    
1. Na **konfiguracji BitaBIZ** , kliknij przycisk **skonfigurować BitaBIZ** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego adres URL usługi** z **krótki przewodnik po sekcji.**

    ![Konfiguracja BitaBIZ](./media/bitabiz-tutorial/tutorial_bitabiz_configure.png) 

1. W oknie przeglądarki internetowej innej logowanie jednokrotne do swojej dzierżawy BitaBIZ jako administrator.

1. Kliknij pozycję **ustawienia administratora**.

    ![Konfiguracja BitaBIZ](./media/bitabiz-tutorial/settings1.png)

1. Kliknij pozycję **integracji Microsoft** w obszarze **ulepszyć swoje** sekcji.

    ![Konfiguracja BitaBIZ](./media/bitabiz-tutorial/settings2.png)

1. Przewiń w dół do sekcji **usługi Microsoft Azure AD (Włącz logowanie jednokrotne)** i wykonać następujące kroki:

    ![Konfiguracja BitaBIZ](./media/bitabiz-tutorial/settings3.png)

    a. Skopiuj wartości z **identyfikator jednostki ("identyfikator" w usłudze Azure AD)** pole tekstowe i wklej go w **identyfikator** tekstowe na **BitaBIZ domena i adresy URL** sekcji w witrynie Azure portal. 
    
    b. W **usługi Azure AD pojedynczego logowania jednokrotnego usługi adresu URL** pola tekstowego, Wklej **SAML pojedynczego logowania jednokrotnego usługi adresu URL**, który skopiowano z witryny Azure portal.
    
    c. W **identyfikator jednostki usługi Azure AD SAML** pola tekstowego, Wklej **SAML identyfikator jednostki**, które zostały skopiowane z witryny Azure portal.

    d. Otwórz swoje pobrany **Certificate(Base64)** w Notatniku plik, skopiuj jego zawartość do Schowka, a następnie wklej go do **usługi Azure AD certyfikat podpisywania (kodowanie Base64)** pola tekstowego.

    e. Dodawanie domeny poczty e-mail firmy oznacza to, że nazwa mycompany.com w **nazwy domeny** logowania jednokrotnego, aby przypisać użytkownikom w Twojej firmie, z tą domeną poczty e-mail w polu tekstowym (nie jest to konieczne).
    
    f. Oznacz **włączone logowanie Jednokrotne** konta BitaBIZ.
    
    g. Kliknij przycisk **Zapisz konfigurację usługi Azure AD** Zapisz i Aktywuj konfiguracji logowania jednokrotnego.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/bitabiz-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/bitabiz-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/bitabiz-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/bitabiz-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-bitabiz-test-user"></a>Tworzenie użytkownika testowego BitaBIZ

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do BitaBIZ, musi być obsługiwana w BitaBIZ.  
W przypadku BitaBIZ Inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby udostępnić konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy BitaBIZ jako administrator.

1. Kliknij pozycję **ustawienia administratora**.

    ![BitaBIZ Dodaj użytkownika](./media/bitabiz-tutorial/settings1.png)

1. Kliknij pozycję **dodawania użytkowników** w obszarze **organizacji** sekcji.

    ![BitaBIZ Dodaj użytkownika](./media/bitabiz-tutorial/user1.png)

1. Kliknij przycisk **Dodaj nowego pracownika**.

    ![BitaBIZ Dodaj użytkownika](./media/bitabiz-tutorial/user2.png)

1. Na **"Dodaj nowe employee"** okna dialogowego strony, wykonaj następujące czynności:

    ![BitaBIZ Dodaj użytkownika](./media/bitabiz-tutorial/user3.png)

    a. W **imię** polu tekstowym Nazwa typu pierwszego użytkownika, takich jak Britta.

    b. W **nazwisko** polu tekstowym wpisz nazwisko użytkownika, takich jak Simon.

    c. W **E-mail** polu tekstowym wpisz adres e-mail użytkownika, takie jak Brittasimon@contoso.com.

    d. Wybierz datę w **Data zatrudnienia**.

    e. Istnieją inne atrybuty użytkownika nieobowiązkowa, które można skonfigurować dla użytkownika. Zapoznaj się [Doc Instalatora pracowników](https://help.bitabiz.dk/manage-or-set-up-your-account/on-boarding-employees/new-employee) Aby uzyskać więcej informacji.    
    
    f. Kliknij przycisk **pracowników Zapisz**.
    
    > [!NOTE]
    > Właściciel konta usługi Azure Active Directory otrzymuje wiadomość e-mail, a także następujące łącze, aby potwierdzić swoje konto, zanim stanie się aktywny.
    
### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do BitaBIZ.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon BitaBIZ, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **BitaBIZ**.

    ![Link BitaBIZ na liście aplikacji](./media/bitabiz-tutorial/tutorial_bitabiz_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka BitaBIZ w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji BitaBIZ.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/bitabiz-tutorial/tutorial_general_01.png
[2]: ./media/bitabiz-tutorial/tutorial_general_02.png
[3]: ./media/bitabiz-tutorial/tutorial_general_03.png
[4]: ./media/bitabiz-tutorial/tutorial_general_04.png

[100]: ./media/bitabiz-tutorial/tutorial_general_100.png

[200]: ./media/bitabiz-tutorial/tutorial_general_200.png
[201]: ./media/bitabiz-tutorial/tutorial_general_201.png
[202]: ./media/bitabiz-tutorial/tutorial_general_202.png
[203]: ./media/bitabiz-tutorial/tutorial_general_203.png

