---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą LearnUpon | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i LearnUpon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: b11c6315-c79d-4f34-9610-bd17070ab7c7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70d4e507087e645c9bfd41e7ef6b90098079ab1d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60259036"
---
# <a name="tutorial-azure-active-directory-integration-with-learnupon"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą LearnUpon

W tym samouczku dowiesz się, jak zintegrować LearnUpon w usłudze Azure Active Directory (Azure AD).

Integrowanie LearnUpon z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do LearnUpon
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do LearnUpon (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą LearnUpon, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- LearnUpon logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie LearnUpon z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-learnupon-from-the-gallery"></a>Dodawanie LearnUpon z galerii
Aby skonfigurować integrację LearnUpon w usłudze Azure AD, należy dodać LearnUpon z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać LearnUpon z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **LearnUpon**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/learnupon-tutorial/tutorial_learnupon_search.png)

1. W panelu wyników wybierz **LearnUpon**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/learnupon-tutorial/tutorial_learnupon_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą LearnUpon w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w LearnUpon do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w LearnUpon musi można ustanowić.

W LearnUpon, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą LearnUpon, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego LearnUpon](#creating-a-learnupon-test-user)**  — aby odpowiednikiem Britta Simon w LearnUpon połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji LearnUpon.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z LearnUpon, wykonaj następujące czynności:**

1. W witrynie Azure portal na **LearnUpon** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/learnupon-tutorial/tutorial_learnupon_samlbase.png)

1. Na **LearnUpon domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/learnupon-tutorial/tutorial_learnupon_url.png)

    W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<companyname>.learnupon.com/saml/consumer`

    > [!NOTE] 
    > Należy pamiętać, że nie jest rzeczywistą wartość. Musisz zaktualizować tę wartość przy użyciu rzeczywistego adresu URL odpowiedzi. Aby zyskać tę wartość, skontaktuj się z pomocą [zespołem pomocy technicznej LearnUpon](https://www.learnupon.com/features/support/).



1. Na **certyfikat podpisywania SAML** sekcji, odszukaj **odcisk palca** — zostanie ona dodana do LearnUpon ustawień protokołu SAML.

    ![Konfigurowanie logowania jednokrotnego](./media/learnupon-tutorial/tutorial_learnupon_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/learnupon-tutorial/tutorial_general_400.png)

1. Na **konfiguracji LearnUpon** , kliknij przycisk **skonfigurować LearnUpon** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/learnupon-tutorial/tutorial_learnupon_configure.png) 

1. Otwórz innym wystąpieniu przeglądarki i zaloguj się do LearnUpon przy użyciu konta administratora. 

1. Kliknij przycisk **ustawienia** kartę.
   
    ![Konfigurowanie logowania jednokrotnego](./media/learnupon-tutorial/tutorial_learnupon_06.png)

1. Kliknij przycisk **Rejestracja jednokrotna — SAML**, a następnie kliknij przycisk **ustawienia ogólne** do konfigurowania ustawień protokołu SAML.
   
    ![Konfigurowanie logowania jednokrotnego](./media/learnupon-tutorial/tutorial_learnupon_07.png) 

1. W **ustawienia ogólne** sekcji, wykonaj następujące czynności:
   
    ![Konfigurowanie logowania jednokrotnego](./media/learnupon-tutorial/tutorial_learnupon_08.png)  
  
    a. Wybierz **włączone**.

    b. Wybierz **wersji** jako **2.0**.

    c. Wybierz **pominąć warunki** jako **nie**.

    d. W **wpis tokenu SAML nazwa param** polu tekstowym wpisz nazwę parametru post żądania na adres URL klienta SAML wskazany powyżej, zawierający potwierdzenie SAML, która ma zostać zweryfikowane i uwierzytelniony — na przykład **SAMLResponse** .

    e. W **Format identyfikatora nazwy** pole tekstowe, wpisz wartość, która wskazuje, gdzie w swojej potwierdzenie SAML identyfikator użytkowników (adres E-mail) znajduje się — na przykład **urn: oasis: nazwy: tc: SAML:1.1:nameid — format: emailAddress**.
  
    f. W **identyfikowania lokalizacji dostawcy** polu tekstowym wpisz wartość, która wskazuje, gdzie użytkownicy są wysyłane do, jeśli kliknij ikona przekazanych z ekranu logowania do portalu Azure.
  
    g. W **adres URL wylogowania** pola tekstowego, Wklej **adres URL wylogowania** skopiowanej w witrynie Azure portal.
    
    h. Kliknij przycisk **Zarządzanie drukuje finger**, a następnie przekaż odcisk palca certyfikatu pobrany.

1. Kliknij przycisk **ustawienia użytkownika**, a następnie wykonaj następujące czynności:
   
     ![Konfigurowanie logowania jednokrotnego](./media/learnupon-tutorial/tutorial_learnupon_11.png)  
 
    a. W **Format identyfikatora nazwy pierwszej** pole tekstowe, wpisz wartość, która informuje NAS, gdzie znajdują się w Twojej potwierdzenie SAML firstname użytkowników znajduje się — na przykład: **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
  
    b. W **ostatniego Format identyfikatora nazwy** pole tekstowe, wpisz wartość, która informuje NAS, gdzie znajdują się w Twojej potwierdzenie SAML lastname użytkowników znajduje się — na przykład: **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/learnupon-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/learnupon-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/learnupon-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/learnupon-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-learnupon-test-user"></a>Tworzenie użytkownika testowego LearnUpon

Celem tej sekcji jest, aby utworzyć użytkownika o nazwie Britta Simon w LearnUpon. LearnUpon obsługę just-in-time, który jest domyślnie włączona.

W tej sekcji nie musisz niczego robić. Nowy użytkownik zostanie utworzony podczas próby dostępu LearnUpon, jeśli go jeszcze nie istnieje. Konfigurowanie usługi Azure AD logowania jednokrotnego.

>[!NOTE]
>Jeśli musisz utworzyć ręcznie przez użytkownika, musisz skontaktować się z [zespołem pomocy technicznej LearnUpon](https://www.learnupon.com/features/support/). 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do LearnUpon.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon LearnUpon, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **LearnUpon**.

    ![Konfigurowanie logowania jednokrotnego](./media/learnupon-tutorial/tutorial_learnupon_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka LearnUpon w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji LearnUpon.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/learnupon-tutorial/tutorial_general_01.png
[2]: ./media/learnupon-tutorial/tutorial_general_02.png
[3]: ./media/learnupon-tutorial/tutorial_general_03.png
[4]: ./media/learnupon-tutorial/tutorial_general_04.png

[100]: ./media/learnupon-tutorial/tutorial_general_100.png

[200]: ./media/learnupon-tutorial/tutorial_general_200.png
[201]: ./media/learnupon-tutorial/tutorial_general_201.png
[202]: ./media/learnupon-tutorial/tutorial_general_202.png
[203]: ./media/learnupon-tutorial/tutorial_general_203.png

