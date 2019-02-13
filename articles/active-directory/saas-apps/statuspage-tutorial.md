---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Strona stanu | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Strona stanu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: f6ee8bb3-df43-4c0d-bf84-89f18deac4b9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e940d7593b6760a81a781fd72c0bb45347b00c2a
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56168156"
---
# <a name="tutorial-azure-active-directory-integration-with-statuspage"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Strona stanu

W tym samouczku dowiesz się, jak zintegrować Strona stanu z usługą Azure Active Directory (Azure AD).

Integrowanie Strona stanu z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Strona stanu
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do Strona stanu (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Strona stanu, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Strona stanu rejestracji jednokrotnej włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Strona dodawania stanu z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-statuspage-from-the-gallery"></a>Strona dodawania stanu z galerii
Aby skonfigurować integrację Strona stanu w usłudze Azure AD, należy dodać Strona stanu z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Strona stanu z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **Strona stanu**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/statuspage-tutorial/tutorial_statuspage_search.png)

1. W panelu wyników wybierz **Strona stanu**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/statuspage-tutorial/tutorial_statuspage_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Strona stanu, w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Strona stanu użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w Strona stanu musi zostać ustanowione.

Strona stanu, przypisywanie wartości **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Strona stanu, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego Strona stanu](#creating-a-statuspage-test-user)**  — aby odpowiednikiem Britta Simon w Strona stanu, połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji strona stanu.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Strona stanu, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Strona stanu** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/statuspage-tutorial/tutorial_statuspage_samlbase.png)

1. Na **Strona stanu domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/statuspage-tutorial/tutorial_statuspage_url.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca:
    | |
    |--|
    | `https://<subdomain>.statuspagestaging.com/` |
    | `https://<subdomain>.statuspage.io/` |

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: 
    | |
    |--|
    | `https://<subdomain>.statuspagestaging.com/sso/saml/consume` |
    | `https://<subdomain>.statuspage.io/sso/saml/consume` |

    > [!NOTE]
    > Skontaktuj się z zespołem pomocy technicznej Strona stanu na [ SupportTeam@statuspage.io ](mailto:SupportTeam@statuspage.io)żądanie metadanych niezbędne do skonfigurowania logowania jednokrotnego. 
    >
    >a. Skopiuj wartość wystawcy z metadanych, a następnie wklej go do **identyfikator** pola tekstowego.
    >
    >b. Z metadanych, skopiuj adres URL odpowiedzi, a następnie wklej go do **adres URL odpowiedzi** pola tekstowego.

1. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/statuspage-tutorial/tutorial_statuspage_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/statuspage-tutorial/tutorial_general_400.png)

1. Na **Strona stanu konfiguracji** kliknij **strona Konfigurowanie stanu** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/statuspage-tutorial/tutorial_statuspage_configure.png) 

1. W innym oknie przeglądarki Zaloguj się do witryny firmy Strona stanu jako administrator.

1. Na głównym pasku narzędzi kliknij polecenie **Zarządzaj kontem**.
   
    ![Konfigurowanie logowania jednokrotnego](./media/statuspage-tutorial/tutorial_statuspage_06.png) 

1. Kliknij przycisk **logowania jednokrotnego** kartę. 
   
    ![Konfigurowanie logowania jednokrotnego](./media/statuspage-tutorial/tutorial_statuspage_07.png) 

1. Na stronie ustawień logowania jednokrotnego wykonaj następujące czynności:
   
    ![Konfigurowanie logowania jednokrotnego](./media/statuspage-tutorial/tutorial_statuspage_08.png) 

    ![Konfigurowanie logowania jednokrotnego](./media/statuspage-tutorial/tutorial_statuspage_09.png) 
 
    a. W **logowania jednokrotnego, docelowy adres URL** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL**, który skopiowano z witryny Azure portal.

    b. Otwórz pobranego certyfikatu w programie Notatnik, skopiuj zawartość, a następnie wklej go do **certyfikatu** pola tekstowego. 

    c. Kliknij przycisk **Zapisywanie konfiguracji**.

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/statuspage-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/statuspage-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/statuspage-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/statuspage-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-statuspage-test-user"></a>Tworzenie użytkownika testowego Strona stanu

Celem tej sekcji jest, aby utworzyć użytkownika o nazwie Britta Simon w Strona stanu.

Strona stanu obsługę just-in-time. Została już włączona w [usługi Azure AD Konfigurowanie logowania jednokrotnego](#configuring-azure-ad-single-sign-on).

**Aby utworzyć użytkownika o nazwie Britta Simon w Strona stanu, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy Strona stanu jako administrator.

1. W menu u góry kliknij **Zarządzaj kontem**.

    ![Konfigurowanie logowania jednokrotnego](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. Kliknij przycisk **członków zespołu** kartę. 
   
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/statuspage-tutorial/tutorial_statuspage_10.png) 

1. Kliknij przycisk **członek zespołu Dodaj**. 
   
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/statuspage-tutorial/tutorial_statuspage_11.png) 

1. Typ **adres E-mail**, **imię**, i **nazwisko** z prawidłowym użytkownikiem, który chcesz aprowizować do powiązanych pól tekstowych. 
   
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/statuspage-tutorial/tutorial_statuspage_12.png) 

1. Jako **roli**, wybierz **Administrator klienta**.

1. Kliknij przycisk **Utwórz konto**.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do Strona stanu.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Strona stanu, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Strona stanu**.

    ![Konfigurowanie logowania jednokrotnego](./media/statuspage-tutorial/tutorial_statuspage_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji jest do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Strona stanu, w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do Strona stanu aplikacji.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/statuspage-tutorial/tutorial_general_01.png
[2]: ./media/statuspage-tutorial/tutorial_general_02.png
[3]: ./media/statuspage-tutorial/tutorial_general_03.png
[4]: ./media/statuspage-tutorial/tutorial_general_04.png

[100]: ./media/statuspage-tutorial/tutorial_general_100.png

[200]: ./media/statuspage-tutorial/tutorial_general_200.png
[201]: ./media/statuspage-tutorial/tutorial_general_201.png
[202]: ./media/statuspage-tutorial/tutorial_general_202.png
[203]: ./media/statuspage-tutorial/tutorial_general_203.png

