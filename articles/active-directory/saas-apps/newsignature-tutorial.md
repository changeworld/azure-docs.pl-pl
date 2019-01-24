---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą portalu zarządzania w chmurze dla systemu Microsoft Azure | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i portalu zarządzania w chmurze dla systemu Microsoft Azure.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 4ea9f47c-25ca-42b0-a878-9e7aa6f34973
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: 896a40bf9358d446eb7d02666d8ec956f9623e91
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54822012"
---
# <a name="tutorial-azure-active-directory-integration-with-cloud-management-portal-for-microsoft-azure"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą portalu zarządzania w chmurze dla systemu Microsoft Azure

W tym samouczku dowiesz się, jak zintegrować chmury portalu zarządzania Microsoft Azure z usługą Azure Active Directory (Azure AD).

Integracja portalu zarządzania w chmurze dla systemu Microsoft Azure z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do portalu zarządzania w chmurze dla systemu Microsoft Azure
- Użytkowników, aby automatycznie uzyskać zalogowanych do portalu zarządzania w chmurze dla systemu Microsoft Azure (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą portalu zarządzania w chmurze dla systemu Microsoft Azure, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Portal zarządzania Cloud for Microsoft Azure logowania jednokrotnego włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie portalu zarządzania w chmurze dla systemu Microsoft Azure z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-cloud-management-portal-for-microsoft-azure-from-the-gallery"></a>Dodawanie portalu zarządzania w chmurze dla systemu Microsoft Azure z galerii
Aby skonfigurować integrację portalu zarządzania w chmurze dla systemu Microsoft Azure w usłudze Azure AD, należy dodać chmury portalu zarządzania Microsoft Azure z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać chmury portalu zarządzania Microsoft Azure z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **chmury portalu zarządzania Microsoft Azure**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/newsignature-tutorial/tutorial_newsignature_search.png)

1. W panelu wyników wybierz **chmury portalu zarządzania Microsoft Azure**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/newsignature-tutorial/tutorial_newsignature_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowania usługi Azure AD logowanie jednokrotne za pomocą portalu zarządzania w chmurze dla systemu Microsoft Azure, w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w portalu zarządzania w chmurze dla systemu Microsoft Azure jest dla użytkownika, w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w portalu zarządzania w chmurze dla systemu Microsoft Azure musi nawiązać.

W portalu zarządzania Cloud for Microsoft Azure, przypisz wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą portalu zarządzania w chmurze dla systemu Microsoft Azure, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie portalu zarządzania Cloud dla użytkownika testowego Microsoft Azure](#creating-a-cloud-management-portal-for-microsoft-azure-test-user)**  — aby odpowiednikiem Britta Simon w chmurze portalu zarządzania Microsoft Azure, w którym jest połączony z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i skonfigurować logowanie jednokrotne w portalu zarządzania usługi chmury dla aplikacji Microsoft Azure.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne za pomocą portalu zarządzania w chmurze dla systemu Microsoft Azure, wykonaj następujące czynności:**

1. W witrynie Azure portal na **chmury portalu zarządzania Microsoft Azure** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/newsignature-tutorial/tutorial_newsignature_samlbase.png)

1. Na **chmury portalu zarządzania Microsoft Azure domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/newsignature-tutorial/tutorial_newsignature_url.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następujących wzorców: 
    
    | |
    |--|
    | `https://portal.newsignature.com/<instancename>` |   
    | `https://portal.igcm.com/<instancename>` |
    
    b. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następujących wzorców: 
    
    | |
    |--|
    | `https://<subdomain>.igcm.com` |
    | `https://<subdomain>.newsignature.com` |

    c. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następujących wzorców: 
    
    | |
    |--|
    | `https://<subdomain>.igcm.com/<instancename>` |
    | `https://<subdomain>.newsignature.com` |
    | `https://<subdomain>.newsignature.com/<instancename>` |

    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami adresu URL logowania, identyfikatora i adresu URL odpowiedzi. Skontaktuj się z pomocą [chmurze Portal zarządzania dla zespołu pomocy technicznej systemu Microsoft Azure Client](mailto:jczernuszka@newsignature.com) do uzyskania tych wartości. 
 
1. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/newsignature-tutorial/tutorial_newsignature_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/newsignature-tutorial/tutorial_general_400.png)

1. Na **chmury portalu zarządzania Microsoft Azure Configuration** kliknij **Konfigurowanie chmury portalu zarządzania Microsoft Azure** otworzyć **Konfigurowanie logowania jednokrotnego** okno. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/newsignature-tutorial/tutorial_newsignature_configure.png) 

1. Aby skonfigurować logowanie jednokrotne na **chmury portalu zarządzania Microsoft Azure** stronie, musisz wysłać pobrany **certyfikatu**, **adres URL wylogowania**,  **SAML pojedynczego logowania jednokrotnego usługi adresu URL** i **identyfikator jednostki SAML** do [chmurze Portal zarządzania dla zespołu pomocy technicznej Microsoft Azure](mailto:jczernuszka@newsignature.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/newsignature-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/newsignature-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/newsignature-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/newsignature-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-cloud-management-portal-for-microsoft-azure-test-user"></a>Tworzenie portalu zarządzania Cloud dla użytkownika testowego Microsoft Azure

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon w portalu zarządzania w chmurze dla systemu Microsoft Azure. Skontaktuj się z [chmurze Portal zarządzania dla zespołu pomocy technicznej Microsoft Azure](mailto:jczernuszka@newsignature.com) Aby dodać użytkowników w portalu zarządzania chmury dla konta Microsoft Azure.


### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do portalu zarządzania w chmurze dla systemu Microsoft Azure.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon do portalu zarządzania w chmurze dla systemu Microsoft Azure, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **chmury portalu zarządzania Microsoft Azure**.

    ![Konfigurowanie logowania jednokrotnego](./media/newsignature-tutorial/tutorial_newsignature_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji jest do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.
Po kliknięciu chmury portalu zarządzania Microsoft Azure kafelka w panelu dostępu, możesz powinien pobrać automatycznie zalogowanych do portalu zarządzania usługi chmury dla aplikacji Microsoft Azure.

Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/newsignature-tutorial/tutorial_general_01.png
[2]: ./media/newsignature-tutorial/tutorial_general_02.png
[3]: ./media/newsignature-tutorial/tutorial_general_03.png
[4]: ./media/newsignature-tutorial/tutorial_general_04.png

[100]: ./media/newsignature-tutorial/tutorial_general_100.png

[200]: ./media/newsignature-tutorial/tutorial_general_200.png
[201]: ./media/newsignature-tutorial/tutorial_general_201.png
[202]: ./media/newsignature-tutorial/tutorial_general_202.png
[203]: ./media/newsignature-tutorial/tutorial_general_203.png

