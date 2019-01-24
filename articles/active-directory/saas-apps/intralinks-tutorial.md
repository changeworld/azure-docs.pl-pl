---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Intralinks | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Intralinks.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 147f2bf9-166b-402e-adc4-4b19dd336883
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 09866fbaa1ddea68b84013633040d28b3e930aad
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54814349"
---
# <a name="tutorial-azure-active-directory-integration-with-intralinks"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Intralinks

W tym samouczku dowiesz się, jak zintegrować Intralinks w usłudze Azure Active Directory (Azure AD).

Integrowanie Intralinks z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Intralinks
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do Intralinks (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Intralinks, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Intralinks logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Intralinks z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-intralinks-from-the-gallery"></a>Dodawanie Intralinks z galerii
Aby skonfigurować integrację Intralinks w usłudze Azure AD, należy dodać Intralinks z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Intralinks z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **Intralinks**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/intralinks-tutorial/tutorial_intralinks_search.png)

1. W panelu wyników wybierz **Intralinks**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/intralinks-tutorial/tutorial_intralinks_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Intralinks w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Intralinks do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Intralinks musi można ustanowić.

W Intralinks, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Intralinks, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego Intralinks](#creating-an-intralinks-test-user)**  — aby odpowiednikiem Britta Simon w Intralinks połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Intralinks.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Intralinks, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Intralinks** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/intralinks-tutorial/tutorial_intralinks_samlbase.png)

1. Na **Intralinks domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/intralinks-tutorial/tutorial_intralinks_url.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca:  `https://<company name>.Intralinks.com/?PartnerIdpId=https://sts.windows.net/<AzureADTenantID>`

    > [!NOTE] 
    > Ta wartość nie jest prawdziwe. Zaktualizuj tę wartość przy użyciu rzeczywisty adres URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Intralinks](https://www.intralinks.com/contact) aby zyskać tę wartość. 
 
1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/intralinks-tutorial/tutorial_intralinks_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/intralinks-tutorial/tutorial_general_400.png)

1. Aby skonfigurować logowanie jednokrotne na **Intralinks** stronie, musisz wysłać pobrany **XML metadanych** [zespołu pomocy technicznej Intralinks](https://www.intralinks.com/contact). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/intralinks-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/intralinks-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/intralinks-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/intralinks-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-an-intralinks-test-user"></a>Tworzenie użytkownika testowego Intralinks

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w Intralinks. Skontaktuj się z [zespołu pomocy technicznej Intralinks](https://www.intralinks.com/contact) Aby dodać użytkowników na platformie Intralinks.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Intralinks.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Intralinks, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Intralinks**.

    ![Konfigurowanie logowania jednokrotnego](./media/intralinks-tutorial/tutorial_intralinks_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.

### <a name="add-intralinks-via-or-elite-application"></a>Dodaj aplikację Intralinks za pośrednictwem lub Elite

Intralinks używa tej samej platformy tożsamości rejestracji Jednokrotnej dla wszystkich innych aplikacjach Intralinks wykluczenie węzła transakcji aplikacji. Dlatego jeśli planujesz używać żadnej innej aplikacji Intralinks najpierw musisz skonfigurować logowanie Jednokrotne dla jednej aplikacji głównej Intralinks za pomocą procedury opisanej powyżej.

Po tym, można wykonać poniższe procedurą, aby dodać inną aplikację Intralinks w Twojej dzierżawie, które mogą korzystać z tej aplikacji głównej, logowania jednokrotnego. 

>[!NOTE]
>Ta funkcja jest dostępna tylko dla klientów jednostki SKU Premium usługi Azure AD i nie jest dostępna dla klientów w warstwie bezpłatna lub podstawowa jednostka SKU.

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]


1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **Intralinks**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/intralinks-tutorial/tutorial_intralinks_search.png)

1. Na **Intralinks Dodaj aplikację** wykonaj następujące czynności:

    ![Dodawanie aplikacji Intralinks za pośrednictwem lub Elite](./media/intralinks-tutorial/tutorial_intralinks_addapp.png)

    a. W **nazwa** polu tekstowym wprowadź odpowiednią nazwę aplikacji, np. **Intralinks Elite**.

    b. Kliknij przycisk **Add** (Dodaj).

1.  W witrynie Azure portal na **Intralinks** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **połączone logowanie jednokrotne**.
 
    ![Konfigurowanie logowania jednokrotnego](./media/intralinks-tutorial/tutorial_intralinks_linkedsignon.png)

1. Pobierz SP zainicjowane rejestracji Jednokrotnej adres URL [zespołu Intralinks](https://www.intralinks.com/contact) stosowania Intralinks i wprowadź go w **Konfiguruj adres URL logowania** jak pokazano poniżej. 
    
     ![Konfigurowanie logowania jednokrotnego](./media/intralinks-tutorial/tutorial_intralinks_customappurl.png)
    
     W polu tekstowym na adres URL logowania należy wpisać adres URL używany przez użytkowników do logowania jednokrotnego do aplikacji Intralinks za pomocą następującego wzorca:
   
    `https://<company name>.Intralinks.com/?PartnerIdpId=https://sts.windows.net/<AzureADTenantID>`

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/intralinks-tutorial/tutorial_general_400.png)

1. Przypisywanie aplikacji do użytkowników lub grup, jak pokazano w sekcji  **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**.

### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Intralinks w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Intralinks.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/intralinks-tutorial/tutorial_general_01.png
[2]: ./media/intralinks-tutorial/tutorial_general_02.png
[3]: ./media/intralinks-tutorial/tutorial_general_03.png
[4]: ./media/intralinks-tutorial/tutorial_general_04.png

[100]: ./media/intralinks-tutorial/tutorial_general_100.png

[200]: ./media/intralinks-tutorial/tutorial_general_200.png
[201]: ./media/intralinks-tutorial/tutorial_general_201.png
[202]: ./media/intralinks-tutorial/tutorial_general_202.png
[203]: ./media/intralinks-tutorial/tutorial_general_203.png

