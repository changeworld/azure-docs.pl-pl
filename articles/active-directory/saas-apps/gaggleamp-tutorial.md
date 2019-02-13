---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą GaggleAMP | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i GaggleAMP.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 9cc1a4b7-964b-406b-9e0c-05cb1a7c9856
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/06/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce28f3667689134a2de177ed5c0dfae810dc1889
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56173576"
---
# <a name="tutorial-azure-active-directory-integration-with-gaggleamp"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą GaggleAMP

W tym samouczku dowiesz się, jak zintegrować GaggleAMP w usłudze Azure Active Directory (Azure AD).

Integrowanie GaggleAMP z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do GaggleAMP
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do GaggleAMP (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą GaggleAMP, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- GaggleAMP logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie GaggleAMP z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-gaggleamp-from-the-gallery"></a>Dodawanie GaggleAMP z galerii
Aby skonfigurować integrację GaggleAMP w usłudze Azure AD, należy dodać GaggleAMP z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać GaggleAMP z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **GaggleAMP**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/gaggleamp-tutorial/tutorial_gaggleamp_search.png)

1. W panelu wyników wybierz **GaggleAMP**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/gaggleamp-tutorial/tutorial_gaggleamp_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą GaggleAMP w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w GaggleAMP do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w GaggleAMP musi można ustanowić.

W GaggleAMP, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą GaggleAMP, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego GaggleAMP](#creating-a-gaggleamp-test-user)**  — aby odpowiednikiem Britta Simon w GaggleAMP połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji GaggleAMP.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z GaggleAMP, wykonaj następujące czynności:**

1. W witrynie Azure portal na **GaggleAMP** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/gaggleamp-tutorial/tutorial_gaggleamp_samlbase.png)

1. Na **GaggleAMP domena i adresy URL** sekcji, wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![Konfigurowanie logowania jednokrotnego](./media/gaggleamp-tutorial/tutorial_gaggleamp_url.png)

     W **identyfikator** pole tekstowe, wpisz adres URL: `https://accounts.gaggleamp.com/auth/saml/callback`

1. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![Konfigurowanie logowania jednokrotnego](./media/gaggleamp-tutorial/tutorial_gaggleamp_url1.png)

     W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://gaggleamp.com/i/<customerid>`

    > [!NOTE]
    > Wartość adresu URL logowania nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta GaggleAMP](mailto:sales@gaggleamp.com) aby zyskać tę wartość.
 
1. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/gaggleamp-tutorial/tutorial_gaggleamp_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/gaggleamp-tutorial/tutorial_general_400.png)

1. Na **konfiguracji GaggleAMP** , kliknij przycisk **skonfigurować GaggleAMP** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego adres URL usługi** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/gaggleamp-tutorial/tutorial_gaggleamp_configure.png) 

1. W innym wystąpieniu przeglądarki, przejdź do strony logowania jednokrotnego SAML, utworzyć przez Gaggle zespołu pomocy technicznej (na przykład: *https://accounts.gaggleamp.com/saml_configurations/oXH8sQcP79dOzgFPqrMTyw/edit*).

1. Na Twojej **logowania jednokrotnego SAML** strony, wykonaj następujące czynności:  
   
    ![GaggleAMP logowanie jednokrotne](./media/gaggleamp-tutorial/tutorial_gaggleamp_06.png)

    a. Wybierz **innych** formularza **dostawcy tożsamości** menu rozwijanego.
    
    b. W **wystawca dostawcy tożsamości** pola tekstowego, Wklej wartość **adres URL wystawcy** skopiowanej w witrynie Azure portal.
    
    c. W **pojedynczy znak na adres URL dostawcy tożsamości** pola tekstowego, Wklej wartość **pojedynczy znak na adres URL usługi** skopiowanej w witrynie Azure portal.
    
    d. Otwórz swoje pobrany **Certificate(Base64)** w Notatniku plik, skopiuj jego zawartość do Schowka, a następnie wklej go do **certyfikat X.509** pola tekstowego.
    
    e. Kliknij pozycję **Zapisz**.

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/gaggleamp-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/gaggleamp-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/gaggleamp-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/gaggleamp-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-gaggleamp-test-user"></a>Tworzenie użytkownika testowego GaggleAMP

Celem tej sekcji jest, aby utworzyć użytkownika o nazwie Britta Simon w GaggleAMP. GaggleAMP obsługę just-in-time, który jest domyślnie włączona.

W tej sekcji nie musisz niczego robić. Nowy użytkownik jest tworzony podczas próby dostępu GaggleAMP, jeśli go jeszcze nie istnieje. 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do GaggleAMP.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon GaggleAMP, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **GaggleAMP**.

    ![Konfigurowanie logowania jednokrotnego](./media/gaggleamp-tutorial/tutorial_gaggleamp_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji jest test konfiguracji logowania jednokrotnego usługi Azure AD za pomocą panelu dostępu.

Po kliknięciu kafelka GaggleAMP w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji GaggleAMP.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/gaggleamp-tutorial/tutorial_general_01.png
[2]: ./media/gaggleamp-tutorial/tutorial_general_02.png
[3]: ./media/gaggleamp-tutorial/tutorial_general_03.png
[4]: ./media/gaggleamp-tutorial/tutorial_general_04.png

[100]: ./media/gaggleamp-tutorial/tutorial_general_100.png

[200]: ./media/gaggleamp-tutorial/tutorial_general_200.png
[201]: ./media/gaggleamp-tutorial/tutorial_general_201.png
[202]: ./media/gaggleamp-tutorial/tutorial_general_202.png
[203]: ./media/gaggleamp-tutorial/tutorial_general_203.png
