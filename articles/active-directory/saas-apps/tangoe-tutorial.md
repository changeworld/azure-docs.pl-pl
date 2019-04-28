---
title: 'Samouczek: Integracja usługi Azure Active Directory z Mobile — wersja Premium polecenia Tangoe | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Tangoe polecenia Premium Mobile.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 2b0b544c-9c2c-49cd-862b-ec2ee9330126
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c9f410fa890df7aac3c3bf4d89468b92e69ba38
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60869273"
---
# <a name="tutorial-azure-active-directory-integration-with-tangoe-command-premium-mobile"></a>Samouczek: Integracja usługi Azure Active Directory z Tangoe polecenia Premium Mobile

W tym samouczku dowiesz się, jak zintegrować Tangoe polecenia Premium Mobile z usługą Azure Active Directory (Azure AD).

Integrowanie Tangoe polecenia Premium Mobile z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Tangoe polecenia Premium Mobile
- Użytkowników, aby automatycznie uzyskać zalogowanych do Tangoe polecenia Premium Mobile (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Mobile — wersja Premium polecenia Tangoe, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Mobile — wersja Premium polecenia Tangoe logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodaj Mobile — wersja Premium polecenia Tangoe z galerii
1. Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

## <a name="add-tangoe-command-premium-mobile-from-the-gallery"></a>Dodaj Mobile — wersja Premium polecenia Tangoe z galerii
Aby skonfigurować integrację Tangoe polecenia Premium Mobile w usłudze Azure AD, należy dodać Mobile — wersja Premium polecenia Tangoe z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Mobile — wersja Premium polecenia Tangoe z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **Mobile — wersja Premium polecenia Tangoe**, wybierz opcję **Mobile — wersja Premium polecenia Tangoe** z panelu wynik następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Dodaj Mobile — wersja Premium polecenia Tangoe z galerii](./media/tangoe-tutorial/tutorial_tangoe_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD
W tej sekcji możesz skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Tangoe Mobile — wersja Premium dla polecenia w oparciu o nazwie "Britta Simon" użytkownika testowego.

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Mobile — wersja Premium polecenia Tangoe do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Mobile — wersja Premium polecenia Tangoe musi można ustanowić.

W aplikacji mobilnych — wersja Premium polecenia Tangoe, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z Tangoe polecenia Premium Mobile, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
1. **[Tworzenie użytkownika testowego Mobile — wersja Premium polecenia Tangoe](#create-a-tangoe-command-premium-mobile-test-user)**  — aby odpowiednikiem Britta Simon w Tangoe polecenia Premium Mobile, połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji mobilnych — wersja Premium polecenia Tangoe.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Mobile — wersja Premium polecenia Tangoe, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Mobile — wersja Premium polecenia Tangoe** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Logowanie na podstawie protokołu SAML](./media/tangoe-tutorial/tutorial_tangoe_samlbase.png)

1. Na **Tangoe polecenia Premium Mobile domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Polecenie Tangoe Premium przenośnych domena i adresy URL](./media/tangoe-tutorial/tutorial_tangoe_url.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://sso.tangoe.com/sp/startSSO.ping?PartnerIdpId=<tenant issuer>&TARGET=<target page url>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://sso.tangoe.com/sp/ACS.saml2`

    > [!NOTE] 
    > Te wartości nie są prawdziwe. Rzeczywisty adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Mobile — wersja Premium polecenia Tangoe](https://www.tangoe.com/contact-us/) do uzyskania tych wartości. 

1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Sekcji certyfikat podpisywania SAML](./media/tangoe-tutorial/tutorial_tangoe_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Przycisk Save (Zapisz)](./media/tangoe-tutorial/tutorial_general_400.png)
    
1. Na **konfigurację Mobile — wersja Premium polecenia Tangoe** , kliknij przycisk **skonfigurować Mobile — wersja Premium polecenia Tangoe** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Sekcja konfiguracji Mobile — wersja Premium polecenia Tangoe](./media/tangoe-tutorial/tutorial_tangoe_configure.png) 

1. Aby uzyskać logowanie Jednokrotne skonfigurowane pod kątem swojej aplikacji, skontaktuj się z Twojego [zespołem pomocy technicznej klienta Mobile — wersja Premium polecenia Tangoe](https://www.tangoe.com/contact-us/) i podaj następujące:

   - Plik metadanych pobrany
   - **Identyfikator jednostki SAML**
   - **Adres URL usługi rejestracji logowania jednokrotnego SAML**
   - **Adres URL wylogowania**

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/tangoe-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Użytkownicy i grupy -> Wszyscy użytkownicy](./media/tangoe-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Dodawanie użytkownika](./media/tangoe-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Strony okna dialogowego użytkownika](./media/tangoe-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-tangoe-command-premium-mobile-test-user"></a>Tworzenie użytkownika testowego Tangoe polecenia Premium Mobile

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w Tangoe polecenia Premium Mobile. 

Aplikacja Mobile — wersja Premium polecenia Tangoe musi wszystkich użytkowników do udostępnienia w aplikacji przed wykonaniem logowanie jednokrotne. Dlatego prosimy o pracy z [zespołem pomocy technicznej klienta Mobile — wersja Premium polecenia Tangoe](https://www.tangoe.com/contact-us/) do aprowizowania tych użytkowników do aplikacji. 

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do Tangoe polecenia Premium Mobile.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Mobile — wersja Premium polecenia Tangoe, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Mobile — wersja Premium polecenia Tangoe**.

    ![Telefon komórkowy Premium polecenia Tangoe listy aplikacji](./media/tangoe-tutorial/tutorial_tangoe_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji możesz przetestować konfigurację logowania jednokrotnego usługi Azure AD za pomocą panelu dostępu.

Po kliknięciu kafelka Mobile — wersja Premium polecenia Tangoe w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji mobilnych — wersja Premium polecenia Tangoe. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/tangoe-tutorial/tutorial_general_01.png
[2]: ./media/tangoe-tutorial/tutorial_general_02.png
[3]: ./media/tangoe-tutorial/tutorial_general_03.png
[4]: ./media/tangoe-tutorial/tutorial_general_04.png

[100]: ./media/tangoe-tutorial/tutorial_general_100.png

[200]: ./media/tangoe-tutorial/tutorial_general_200.png
[201]: ./media/tangoe-tutorial/tutorial_general_201.png
[202]: ./media/tangoe-tutorial/tutorial_general_202.png
[203]: ./media/tangoe-tutorial/tutorial_general_203.png

