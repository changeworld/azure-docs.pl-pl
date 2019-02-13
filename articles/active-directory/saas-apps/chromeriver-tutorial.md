---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Chromeriver | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Chromeriver.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 445c5600-e340-4724-a9cb-3cfaf5770b70
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6a037bf945dd6d30df0272a595babeffa2fb7d2
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56178060"
---
# <a name="tutorial-azure-active-directory-integration-with-chromeriver"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Chromeriver

W tym samouczku dowiesz się, jak zintegrować Chromeriver w usłudze Azure Active Directory (Azure AD).

Integrowanie Chromeriver z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Chromeriver
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do Chromeriver (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Chromeriver, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Chromeriver logowania jednokrotnego włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Chromeriver z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-chromeriver-from-the-gallery"></a>Dodawanie Chromeriver z galerii
Aby skonfigurować integrację Chromeriver w usłudze Azure AD, należy dodać Chromeriver z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Chromeriver z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **Chromeriver**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/chromeriver-tutorial/tutorial_chromeriver_search.png)

1. W panelu wyników wybierz **Chromeriver**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/chromeriver-tutorial/tutorial_chromeriver_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą Chromeriver w oparciu o użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Chromeriver do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Chromeriver musi można ustanowić.

W Chromeriver, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Chromeriver, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego Chromeriver](#creating-a-chromeriver-test-user)**  — aby odpowiednikiem Britta Simon w Chromeriver połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Chromeriver.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Chromeriver, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Chromeriver** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/chromeriver-tutorial/tutorial_chromeriver_samlbase.png)

1. Na **Chromeriver domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/chromeriver-tutorial/tutorial_chromeriver_url.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.chromeriver.com`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.chromeriver.com/login/sso/saml/consume?customerId=<uniqueid>`

    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Skontaktuj się z pomocą [zespołem pomocy technicznej Chromeriver](https://www.chromeriver.com/services/support) do uzyskania tych wartości.
 


1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/chromeriver-tutorial/tutorial_chromeriver_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/chromeriver-tutorial/tutorial_general_400.png)

1. Aby skonfigurować logowanie jednokrotne na **Chromeriver** stronie, musisz wysłać pobrany **XML metadanych** do [zespołem pomocy technicznej Chromeriver](https://www.chromeriver.com/services/support). Gdy logowanie jednokrotne zostanie włączone dla Twojej subskrypcji, otrzymasz o tym powiadomienie.

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/chromeriver-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/chromeriver-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/chromeriver-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/chromeriver-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-chromeriver-test-user"></a>Tworzenie użytkownika testowego Chromeriver

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do Chromeriver, musi być obsługiwana w Chromeriver.  

W przypadku Chromeriver, konta użytkowników muszą zostać utworzone przez użytkownika [zespołem pomocy technicznej Chromeriver](https://www.chromeriver.com/services/support).

>[!NOTE]
>Można użyć jakichkolwiek innych Chromeriver użytkownika konta tworzenie narzędzi lub interfejsów API dostarczonych przez Chromeriver do świadczenia usługi Azure Active Directory kont użytkowników.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Chromeriver.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Chromeriver, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Chromeriver**.

    ![Konfigurowanie logowania jednokrotnego](./media/chromeriver-tutorial/tutorial_chromeriver_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji jest do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Chromeriver w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Chromeriver. Aby uzyskać więcej szczegółów na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/chromeriver-tutorial/tutorial_general_01.png
[2]: ./media/chromeriver-tutorial/tutorial_general_02.png
[3]: ./media/chromeriver-tutorial/tutorial_general_03.png
[4]: ./media/chromeriver-tutorial/tutorial_general_04.png

[100]: ./media/chromeriver-tutorial/tutorial_general_100.png

[200]: ./media/chromeriver-tutorial/tutorial_general_200.png
[201]: ./media/chromeriver-tutorial/tutorial_general_201.png
[202]: ./media/chromeriver-tutorial/tutorial_general_202.png
[203]: ./media/chromeriver-tutorial/tutorial_general_203.png

