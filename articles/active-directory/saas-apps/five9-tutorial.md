---
title: 'Samouczek: Integracja usługi Azure Active Directory Five9 oraz karty (CTI, skontaktuj się z Centrum agentów) | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Five9 oraz karty (CTI, skontaktuj się z Centrum agentów).
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 88dc82ab-be0b-4017-8335-c47d00775d7b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: jeedes
ms.openlocfilehash: 7b31cbbf639e5835a3a46063b4b15d67915fb3d4
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54816674"
---
# <a name="tutorial-azure-active-directory-integration-with-five9-plus-adapter-cti-contact-center-agents"></a>Samouczek: Integracja usługi Azure Active Directory Five9 oraz karty (CTI, skontaktuj się z Centrum agentów)

W tym samouczku dowiesz się, jak zintegrować Five9 oraz karty (CTI, skontaktuj się z Centrum agentów) z usługą Azure Active Directory (Azure AD).

Integrowanie Five9 oraz karty (CTI, skontaktuj się z Centrum agentów) z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Five9 oraz karty sieciowej (CTI, skontaktuj się z Centrum agentów)
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do Five9 oraz karty (CTI, skontaktuj się z Centrum agentów) (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD Five9 oraz karty (CTI, skontaktuj się z Centrum agentów), potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Five9 oraz karty (CTI, skontaktuj się z Centrum agentów) logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej tutaj: [Wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Five9 oraz karty (CTI, skontaktuj się z Centrum agentów) z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-five9-plus-adapter-cti-contact-center-agents-from-the-gallery"></a>Dodawanie Five9 oraz karty (CTI, skontaktuj się z Centrum agentów) z galerii
Aby skonfigurować integrację Five9 oraz karty (CTI, skontaktuj się z Centrum agentów) w usłudze Azure AD, należy dodać Five9 oraz karty (CTI, skontaktuj się z Centrum agentów) z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Five9 oraz karty (CTI, skontaktuj się z Centrum agentów) z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **Five9 oraz karty (CTI, skontaktuj się z Centrum agentów)**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/five9-tutorial/tutorial_five9_search.png)

1. W panelu wyników wybierz **Five9 oraz karty (CTI, skontaktuj się z Centrum agentów)**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/five9-tutorial/tutorial_five9_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowania usługi Azure AD logowanie jednokrotne za pomocą Five9 oraz karty (CTI, skontaktuj się z Centrum agentów) w oparciu o nazwie "Britta Simon" użytkownika testowego.

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika Five9 oraz karty (CTI, skontaktuj się z Centrum agentów) dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika Five9 oraz karty (CTI, skontaktuj się z Centrum agentów) musi zostać nawiązane.

Five9 oraz karty (CTI, skontaktuj się z Centrum agentów), należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Five9 oraz karty (CTI, skontaktuj się z Centrum agentów), należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego Five9 oraz karty (CTI, skontaktuj się z Centrum agentów)](#creating-a-five9-plus-adapter-cti-contact-center-agents-test-user)**  — aby odpowiednikiem Britta Simon Five9 oraz karty (CTI, skontaktuj się z Centrum agentów) połączoną usługę Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Five9 oraz karty (CTI, skontaktuj się z Centrum agentów).

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Five9 oraz karty (CTI, skontaktuj się z Centrum agentów), wykonaj następujące czynności:**

1. W witrynie Azure portal na **Five9 oraz karty (CTI, skontaktuj się z Centrum agentów)** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/five9-tutorial/tutorial_five9_samlbase.png)

1. Na **Five9 oraz karty (CTI, skontaktuj się z Centrum agentów), domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/five9-tutorial/tutorial_five9_url.png)
    
    a. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następujących wzorców:

    |    Środowisko      |       Adres URL      |
    | :-- | :-- |
    | "Five9 oraz karty dla oprogramowania Microsoft Dynamics CRM" | `https://app.five9.com/appsvcs/saml/metadata/alias/msdc` |
    | "Five9 oraz Adapter dla systemu Zendesk" | `https://app.five9.com/appsvcs/saml/metadata/alias/zd` |
    | "Five9 oraz Adapter dla agenta Toolkit pulpitu" | `https://app.five9.com/appsvcs/saml/metadata/alias/adt` |

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca:

    |      Środowisko     |      Adres URL      |
    | :--                  | :--           |
    | "Five9 oraz karty dla oprogramowania Microsoft Dynamics CRM" | `https://app.five9.com/appsvcs/saml/SSO/alias/msdc` |
    | "Five9 oraz Adapter dla systemu Zendesk" | `https://app.five9.com/appsvcs/saml/SSO/alias/zd` |
    | "Five9 oraz Adapter dla agenta Toolkit pulpitu" | `https://app.five9.com/appsvcs/saml/SSO/alias/adt` |

1. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/five9-tutorial/tutorial_five9_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/five9-tutorial/tutorial_general_400.png)

1. Na **konfiguracji Five9 oraz karty (CTI, skontaktuj się z Centrum agentów)** , kliknij przycisk **skonfigurować Five9 oraz karty (CTI, skontaktuj się z Centrum agentów)** otworzyć **skonfigurować logowanie jednokrotne**okna. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/five9-tutorial/tutorial_five9_configure.png) 

1. Aby skonfigurować logowanie jednokrotne na **Five9 oraz karty (CTI, skontaktuj się z Centrum agentów)** stronie, musisz wysłać pobrany **Certificate(Base64), adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL**do [zespołu pomocy technicznej Five9 oraz kart (CTI, skontaktuj się z Centrum agentów)](https://www.five9.com/about/contact). Również ponadto dalsze konfigurowania logowania jednokrotnego, wykonaj poniższe kroki, zgodnie z karty:

    a. "Five9 oraz Adapter dla zestawu narzędzi pulpitu agenta" Podręczniku administratora: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf)
    
    b. "Five9 oraz karty dla oprogramowania Microsoft Dynamics CRM" Podręczniku administratora: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf)
    
    c. "Five9 oraz Adapter dla systemu Zendesk" Podręczniku administratora: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf)


> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/five9-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/five9-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/five9-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/five9-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-five9-plus-adapter-cti-contact-center-agents-test-user"></a>Tworzenie użytkownika testowego Five9 oraz karty (CTI, skontaktuj się z Centrum agentów)

W tej sekcji utworzysz użytkownika o nazwie Britta Simon Five9 oraz karty (CTI, skontaktuj się z Centrum agentów). Praca z [zespołu pomocy technicznej Five9 oraz kart (CTI, skontaktuj się z Centrum agentów)](https://www.five9.com/about/contact) Aby dodać użytkowników na platformie Five9 oraz karty (CTI, skontaktuj się z Centrum agentów). Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do Five9 oraz karty sieciowej (CTI, skontaktuj się z Centrum agentów).

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Five9 oraz karty (CTI, skontaktuj się z Centrum agentów), wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Five9 oraz karty (CTI, skontaktuj się z Centrum agentów)**.

    ![Konfigurowanie logowania jednokrotnego](./media/five9-tutorial/tutorial_five9_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Five9 oraz karty (CTI, skontaktuj się z Centrum agentów), w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Five9 oraz karty (CTI, skontaktuj się z Centrum agentów).
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/five9-tutorial/tutorial_general_01.png
[2]: ./media/five9-tutorial/tutorial_general_02.png
[3]: ./media/five9-tutorial/tutorial_general_03.png
[4]: ./media/five9-tutorial/tutorial_general_04.png

[100]: ./media/five9-tutorial/tutorial_general_100.png

[200]: ./media/five9-tutorial/tutorial_general_200.png
[201]: ./media/five9-tutorial/tutorial_general_201.png
[202]: ./media/five9-tutorial/tutorial_general_202.png
[203]: ./media/five9-tutorial/tutorial_general_203.png

