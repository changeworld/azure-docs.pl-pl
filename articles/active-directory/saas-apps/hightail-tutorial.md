---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Hightail | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Hightail.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: e15206ac-74b0-46e4-9329-892c7d242ec0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/15/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd49d97431efc3e1902e700a54627d5ee095cc74
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56180032"
---
# <a name="tutorial-azure-active-directory-integration-with-hightail"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Hightail

W tym samouczku dowiesz się, jak zintegrować Hightail w usłudze Azure Active Directory (Azure AD).

Integrowanie Hightail z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Hightail
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do Hightail (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Hightail, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Hightail logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Hightail z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-hightail-from-the-gallery"></a>Dodawanie Hightail z galerii
Aby skonfigurować integrację Hightail w usłudze Azure AD, należy dodać Hightail z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Hightail z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **Hightail**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/hightail-tutorial/tutorial_hightail_search.png)

1. W panelu wyników wybierz **Hightail**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/hightail-tutorial/tutorial_hightail_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Hightail w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Hightail do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Hightail musi można ustanowić.

W Hightail, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Hightail, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego Hightail](#creating-a-hightail-test-user)**  — aby odpowiednikiem Britta Simon w Hightail połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Hightail.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Hightail, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Hightail** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.

    ![Konfigurowanie logowania jednokrotnego](./media/hightail-tutorial/tutorial_hightail_samlbase.png)

1. Na **Hightail domena i adresy URL** sekcji, wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![Konfigurowanie logowania jednokrotnego](./media/hightail-tutorial/tutorial_hightail_url.png)

    W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL w postaci: `https://www.hightail.com/samlLogin?phi_action=app/samlLogin&subAction=handleSamlResponse`

    > [!NOTE]
    > Wartość adresu URL odpowiedzi nie jest rzeczywistą wartość. Wartość adresu URL odpowiedzi zostaną zaktualizowane o rzeczywistego adresu URL odpowiedzi, które wyjaśniono w dalszej części tego samouczka.

1. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![Konfigurowanie logowania jednokrotnego](./media/hightail-tutorial/tutorial_hightail_url1.png)

    W **na adres URL logowania** pole tekstowe, wpisz adres URL w postaci: `https://www.hightail.com/loginSSO`

1. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/hightail-tutorial/tutorial_hightail_certificate.png) 

1. Hightail aplikacja oczekuje twierdzenia SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Możesz zarządzać wartości te atrybuty z **"Atrybutu"** kartę w aplikacji. Poniższy zrzut ekranu przedstawia przykład tego działania. 

    ![Konfigurowanie logowania jednokrotnego](./media/hightail-tutorial/tutorial_hightail_attribute.png) 

1. W **atrybutów użytkownika** sekcji na **logowanie jednokrotne** okno dialogowe, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji i wykonaj następujące czynności:
    
    | Nazwa atrybutu | Wartość atrybutu |
    | ------------------- | -------------------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | Email | user.mail |    
    | UserIdentity | user.mail |
    
    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](./media/hightail-tutorial/tutorial_officespace_04.png)

    ![Konfigurowanie logowania jednokrotnego](./media/hightail-tutorial/tutorial_officespace_05.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    c. Z **wartość** wpisz wartość atrybutu wyświetlanego dla tego wiersza.

    d. Pozostaw pole **Przestrzeń nazw** puste.

    e. Kliknij przycisk **OK**.

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/hightail-tutorial/tutorial_general_400.png)

1. Na **Hightail konfiguracji** kliknij **skonfigurować Hightail** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/hightail-tutorial/tutorial_hightail_configure.png)

    >[!NOTE]
    >Przed rozpoczęciem konfigurowania rejestracji jednokrotnej w aplikacji Hightail, skontaktuj się z białą listę z domeną poczty e-mail przy użyciu Hightail zespołu, aby wszyscy użytkownicy, którzy korzystają z tej domeny można używać funkcji logowania jednokrotnego.

1. W innym oknie przeglądarki Otwórz **Hightail** portalu administracyjnym.

1. Kliknij pozycję **ikony użytkownika** w prawym górnym rogu strony. 

    ![Konfigurowanie logowania jednokrotnego](./media/hightail-tutorial/configure1.png)

1. Kliknij przycisk **widoku konsoli administracyjnej** kartę.

    ![Konfigurowanie logowania jednokrotnego](./media/hightail-tutorial/configure2.png)

1. W menu u góry kliknij **SAML** kartę i wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/hightail-tutorial/configure3.png)

    a. W **adres URL logowania** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL** skopiowane z witryny Azure portal.

    b. Otwórz swój certyfikat zakodowany base-64 w Notatniku pobranego z witryny Azure portal, skopiuj jego zawartość do Schowka, a następnie wklej go do **certyfikat SAML** pola tekstowego.

    c. Kliknij przycisk **KOPIOWANIA** Aby skopiować adres URL klienta SAML wystąpienia i wklej ją w **adres URL odpowiedzi** polu tekstowym w **Hightail domena i adresy URL** sekcji w witrynie Azure portal.

    d. Kliknij przycisk **zapisywania konfiguracji**.

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/hightail-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/hightail-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/hightail-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/hightail-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-hightail-test-user"></a>Tworzenie użytkownika testowego Hightail

Celem tej sekcji jest, aby utworzyć użytkownika o nazwie Britta Simon w Hightail. 

W tej sekcji nie musisz niczego robić. Hightail obsługuje aprowizację użytkowników just-in-time na podstawie niestandardowych oświadczeń. Po skonfigurowaniu oświadczenia niestandardowe, jak pokazano w sekcji **[usługi Azure AD Konfigurowanie logowania jednokrotnego](#configuring-azure-ad-single-sign-on)** powyżej, użytkownik jest automatycznie tworzony w aplikacji nie istnieje. 

>[!NOTE]
>Jeśli potrzebujesz ręcznie utworzyć użytkownika, musisz skontaktować się z [zespołem pomocy technicznej Hightail](mailto:support@hightail.com). 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Hightail.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Hightail, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Hightail**.

    ![Konfigurowanie logowania jednokrotnego](./media/hightail-tutorial/tutorial_hightail_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.

### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji jest do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Hightail w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Hightail.


## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/hightail-tutorial/tutorial_general_01.png
[2]: ./media/hightail-tutorial/tutorial_general_02.png
[3]: ./media/hightail-tutorial/tutorial_general_03.png
[4]: ./media/hightail-tutorial/tutorial_general_04.png

[100]: ./media/hightail-tutorial/tutorial_general_100.png

[200]: ./media/hightail-tutorial/tutorial_general_200.png
[201]: ./media/hightail-tutorial/tutorial_general_201.png
[202]: ./media/hightail-tutorial/tutorial_general_202.png
[203]: ./media/hightail-tutorial/tutorial_general_203.png

