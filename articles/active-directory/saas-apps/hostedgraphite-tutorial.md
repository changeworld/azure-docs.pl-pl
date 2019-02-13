---
title: 'Samouczek: Integracja usługi Azure Active Directory z hostowanej grafitu | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i grafitu hostowanej.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: a1ac4d7f-d079-4f3c-b6da-0f520d427ceb
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce0ae55fdc43704670f1d74e52b55db91a20cceb
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56179250"
---
# <a name="tutorial-azure-active-directory-integration-with-hosted-graphite"></a>Samouczek: Integracja usługi Azure Active Directory z hostowanej grafitu

W tym samouczku dowiesz się, jak zintegrować grafitu hostowanej usłudze Azure Active Directory (Azure AD).

Integrowanie grafitu hostowany przy użyciu usługi Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do hostowanych grafitu
- Użytkowników, aby automatycznie uzyskać zalogowanych do hostowanych grafitu (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Do konfigurowania integracji z usługą Azure AD z hostowanej grafitu, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Hostowana grafitu logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie grafitu hostowanych za pomocą galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-hosted-graphite-from-the-gallery"></a>Dodawanie grafitu hostowanych za pomocą galerii
Aby skonfigurować integrację grafitu hostowanej w usłudze Azure AD, należy dodać hostowanych grafitu z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać grafitu hostowanych w galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **hostowanych grafitu**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_search.png)

1. W panelu wyników wybierz **hostowanych grafitu**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą hostowanych grafitu oparte na użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w hostowanej grafitu do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w hostowanej grafitu musi nawiązać.

W hostowanej grafitu, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą hostowanych grafitu, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego hostowanych grafitu](#creating-a-hosted-graphite-test-user)**  — aby odpowiednikiem Britta Simon w hostowanej grafitu połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji hostowanych grafitu.

**Aby skonfigurować usługi Azure AD logowanie jednokrotne z hostowanej grafitu, wykonaj następujące czynności:**

1. W witrynie Azure portal na **hostowanych grafitu** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_samlbase.png)

1. Na **hostowanych grafitu domena i adresy URL** sekcji, jeśli chcesz skonfigurować aplikację w **tryb inicjowane przez dostawcę tożsamości**, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_url.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://www.hostedgraphite.com/metadata/<user id>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://www.hostedgraphite.com/complete/saml/<user id>`

1. Na **hostowanych grafitu domena i adresy URL** sekcji, jeśli chcesz skonfigurować aplikację w **SP zainicjowano tryb**, wykonaj następujące czynności:
   
    ![Konfigurowanie logowania jednokrotnego](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_10.png)
  
    a. Kliknij pozycję **Pokaż zaawansowane ustawienia adresu URL** opcji

    b. W **na adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://www.hostedgraphite.com/login/saml/<user id>/`   

    > [!NOTE] 
    > Należy pamiętać, że nie są to rzeczywiste wartości. Musisz zaktualizować te wartości z rzeczywistego identyfikatora, adres URL odpowiedzi i na adres URL logowania. Aby uzyskać te wartości, możesz przejść do dostępu -> Ustawienia języka SAML na stronie aplikacji lub skontaktuj się z [hostowanych grafitu zespołem pomocy technicznej](mailto:help@hostedgraphite.com).
    >
 
1. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/hostedgraphite-tutorial/tutorial_general_400.png)

1. Na **obsługiwanych konfiguracji grafitu** , kliknij przycisk **skonfigurować grafitu hostowanych** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego adres URL usługi** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_configure.png) 

1. Zaloguj się do dzierżawy usługi hostowanej grafitu jako administrator.

1. Przejdź do **strony konfiguracji SAML** na pasku bocznym (**dostępu -> Ustawienia języka SAML**).
   
    ![Konfigurowanie pojedynczego logowania jednokrotnego w aplikacji po stronie](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_000.png)

1. Upewnij się, te adresy URL dopasować go do konfiguracji na **hostowanych grafitu domena i adresy URL** części witryny Azure portal.
   
    ![Konfigurowanie pojedynczego logowania jednokrotnego w aplikacji po stronie](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_001.png)

1. W **jednostki lub identyfikator wystawcy** i **adres URL logowania SSO** pola tekstowe, Wklej wartość **identyfikator jednostki SAML** i **SAML pojedynczego logowania jednokrotnego adres URL usługi** który Skopiowano z witryny Azure portal. 
   
    ![Konfigurowanie pojedynczego logowania jednokrotnego w aplikacji po stronie](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_002.png)
   

1. Wybierz pozycję "**tylko do odczytu**" jako **domyślna rola**.
    
    ![Konfigurowanie pojedynczego logowania jednokrotnego w aplikacji po stronie](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_004.png)

1. Otwórz w Notatniku swój certyfikat zakodowany w formacie base-64 pobrany z witryny Azure Portal, skopiuj zawartość do schowka, a następnie wklej ją w polu tekstowym **Certyfikat X.509**.
    
    ![Konfigurowanie pojedynczego logowania jednokrotnego w aplikacji po stronie](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_005.png)

1. Kliknij przycisk **Save** (Zapisz).

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/hostedgraphite-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/hostedgraphite-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/hostedgraphite-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/hostedgraphite-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-hosted-graphite-test-user"></a>Tworzenie użytkownika testowego grafitu hostowanych

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon w hostowanej grafitu. Hostowanej grafitu obsługę just-in-time, który jest domyślnie włączona.

W tej sekcji nie musisz niczego robić. Nowy użytkownik zostanie utworzony podczas próby dostępu grafitu hostowanych, jeśli go jeszcze nie istnieje.

>[!NOTE]
>Jeśli potrzebujesz ręcznie utworzyć użytkownika, musisz skontaktować się z zespołem pomocy technicznej grafitu hostowane za pośrednictwem <mailto:help@hostedgraphite.com>. 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do hostowanych grafitu.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon grafitu hostowanych, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **hostowanych grafitu**.

    ![Konfigurowanie logowania jednokrotnego](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji jest test konfiguracji logowania jednokrotnego usługi Azure AD za pomocą panelu dostępu.

Po kliknięciu kafelka grafitu hostowanych w panelu dostępu, możesz należy pobrać automatycznie zalogowanych do aplikacji hostowanej grafitu.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/hostedgraphite-tutorial/tutorial_general_01.png
[2]: ./media/hostedgraphite-tutorial/tutorial_general_02.png
[3]: ./media/hostedgraphite-tutorial/tutorial_general_03.png
[4]: ./media/hostedgraphite-tutorial/tutorial_general_04.png

[100]: ./media/hostedgraphite-tutorial/tutorial_general_100.png

[200]: ./media/hostedgraphite-tutorial/tutorial_general_200.png
[201]: ./media/hostedgraphite-tutorial/tutorial_general_201.png
[202]: ./media/hostedgraphite-tutorial/tutorial_general_202.png
[203]: ./media/hostedgraphite-tutorial/tutorial_general_203.png

