---
title: 'Samouczek: Integracja usługi Azure Active Directory z usługą BeeLine | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i z usługi BeeLine.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 0726859d-1dac-44a0-810b-da56d89039ee
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 7b6410067426f65627c22536cd598ed318ae0c81
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55161620"
---
# <a name="tutorial-azure-active-directory-integration-with-beeline"></a>Samouczek: Integracja usługi Azure Active Directory z usługą BeeLine

W tym samouczku dowiesz się, jak zintegrować z usługi BeeLine w usłudze Azure Active Directory (Azure AD).

Integracja z usługi BeeLine z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do z usługi BeeLine
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do z usługi BeeLine (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą BeeLine, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Z usługi BeeLine logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie z usługi BeeLine z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-beeline-from-the-gallery"></a>Dodawanie z usługi BeeLine z galerii
Aby skonfigurować integrację z usługi BeeLine w usłudze Azure AD, należy dodać z usługi BeeLine z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać z usługi BeeLine z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **z usługi BeeLine**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/beeline-tutorial/tutorial_beeline_search.png)

1. W panelu wyników wybierz **z usługi BeeLine**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/beeline-tutorial/tutorial_beeline_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji konfigurowania i testowania usługi Azure AD logowanie jednokrotne z usługą BeeLine w oparciu o użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać odpowiednika użytkownika z usługi BeeLine do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkownika z usługi BeeLine musi zostać ustanowione.

Z usługi BeeLine, przypisywanie wartości **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z usługą BeeLine, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego z usługi BeeLine](#creating-a-beeline-test-user)**  — mieć odpowiednikiem Britta Simon z usługi BeeLine, połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji z usługi BeeLine.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z usługą BeeLine, wykonaj następujące czynności:**

1. W witrynie Azure portal na **z usługi BeeLine** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/beeline-tutorial/tutorial_beeline_samlbase.png)

1. Na **z usługi BeeLine domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/beeline-tutorial/tutorial_beeline_url.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://projects.beeline.net/<instancename>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca:
    | |
    |--|
    | `https://projects.beeline.net/<instancename>/SSO_External.ashx`|
    | `https://projects.beeline.net/<companyname>/SSO_External.ashx` |

    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Skontaktuj się z pomocą [zespołem pomocy technicznej z usługi BeeLine](https://www.beeline.com/contact-us/) do uzyskania tych wartości.
 
1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/beeline-tutorial/tutorial_beeline_certificate.png) 

1. Aplikacja z usługi Beeline oczekuje twierdzenia SAML w określonym formacie. Skontaktuj się z [zespołem pomocy technicznej z usługi BeeLine](https://www.beeline.com/contact-us/) najpierw po to, aby zidentyfikować identyfikator właściwy użytkownik, który będzie mapowany do aplikacji. Ponadto prosimy o poświęcenie ze wskazówkami [zespołem pomocy technicznej z usługi BeeLine](https://www.beeline.com/contact-us/) o atrybucie, w którym mają być użyte dla tego mapowania. Możesz określić wartość tego atrybutu z **atrybutów użytkownika** kartę w aplikacji. Poniższy zrzut ekranu przedstawia przykład tego działania. W tym miejscu możemy zmapowane **identyfikator użytkownika** oświadczenia **userprincipalname** atrybut, który zawiera unikatowy identyfikator użytkownika, które zostanie wysłane do aplikacji z usługi Beeline w każdym pomyślnej odpowiedzi SAML.

    ![Konfigurowanie logowania jednokrotnego](./media/beeline-tutorial/tutorial_attribute.png)    

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/beeline-tutorial/tutorial_general_400.png)

1. Na **konfiguracji z usługi BeeLine** kliknij **skonfigurować z usługi BeeLine** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiowanie **adres URL wylogowania** i **identyfikator jednostki SAML** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/beeline-tutorial/tutorial_beeline_configure.png) 

1. Aby skonfigurować logowanie jednokrotne na **z usługi BeeLine** stronie, musisz wysłać pobrany **XML metadanych** i **identyfikator jednostki SAML**, **adres URL wylogowania** do [Zespołem pomocy technicznej z usługi beeLine](https://www.beeline.com/contact-us/).

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/beeline-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/beeline-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/beeline-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/beeline-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-beeline-test-user"></a>Tworzenie użytkownika testowego z usługi BeeLine

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w z usługi Beeline. Z usługi beeline aplikacja potrzebuje wszystkich użytkowników do udostępnienia w aplikacji przed wykonaniem logowanie jednokrotne. Dlatego działają z [zespołem pomocy technicznej z usługi BeeLine](https://www.beeline.com/contact-us/) do aprowizowania tych użytkowników do aplikacji. 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon korzystać z platformy Azure logowania jednokrotnego przez udzielenie dostępu z usługi BeeLine.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon z usługi BeeLine, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **z usługi BeeLine**.

    ![Konfigurowanie logowania jednokrotnego](./media/beeline-tutorial/tutorial_beeline_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu. Po kliknięciu kafelka z usługi Beeline w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji z usługi Beeline.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/beeline-tutorial/tutorial_general_01.png
[2]: ./media/beeline-tutorial/tutorial_general_02.png
[3]: ./media/beeline-tutorial/tutorial_general_03.png
[4]: ./media/beeline-tutorial/tutorial_general_04.png

[100]: ./media/beeline-tutorial/tutorial_general_100.png

[200]: ./media/beeline-tutorial/tutorial_general_200.png
[201]: ./media/beeline-tutorial/tutorial_general_201.png
[202]: ./media/beeline-tutorial/tutorial_general_202.png
[203]: ./media/beeline-tutorial/tutorial_general_203.png

