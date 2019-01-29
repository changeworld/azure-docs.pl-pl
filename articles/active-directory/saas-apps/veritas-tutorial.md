---
title: 'Samouczek: Integracja usługi Azure Active Directory przy użyciu logowania jednokrotnego Vault.cloud Enterprise Veritas | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Veritas Enterprise Vault.cloud Usługa rejestracji Jednokrotnej.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2017
ms.author: jeedes
ms.openlocfilehash: 0b2d4acb3a717f51d0ff15868dea917806b5b604
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55181966"
---
# <a name="tutorial-azure-active-directory-integration-with-veritas-enterprise-vaultcloud-sso"></a>Samouczek: Integracja usługi Azure Active Directory przy użyciu logowania jednokrotnego Vault.cloud Enterprise Veritas

W tym samouczku dowiesz się, jak zintegrować Veritas Enterprise Vault.cloud Usługa rejestracji Jednokrotnej z usługą Azure Active Directory (Azure AD).

Integracja logowania jednokrotnego Vault.cloud Enterprise Veritas z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do logowania jednokrotnego Vault.cloud Enterprise Veritas
- Użytkowników, aby automatycznie uzyskać zalogowanych do Veritas Enterprise Vault.cloud SSO (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu logowania jednokrotnego Vault.cloud Enterprise Veritas, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Logowanie Jednokrotne Vault.cloud Enterprise Veritas logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie rejestracji Jednokrotnej Vault.cloud Enterprise Veritas z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-veritas-enterprise-vaultcloud-sso-from-the-gallery"></a>Dodawanie rejestracji Jednokrotnej Vault.cloud Enterprise Veritas z galerii
Aby skonfigurować integrację Veritas Enterprise Vault.cloud Usługa rejestracji Jednokrotnej w usłudze Azure AD, należy dodać Veritas Enterprise Vault.cloud Usługa rejestracji Jednokrotnej z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Veritas Enterprise Vault.cloud Usługa rejestracji Jednokrotnej z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **logowania jednokrotnego Vault.cloud Enterprise Veritas**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/veritas-tutorial/tutorial_veritas_search.png)

1. W panelu wyników wybierz **logowania jednokrotnego Vault.cloud Enterprise Veritas**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/veritas-tutorial/tutorial_veritas_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Veritas Enterprise Vault.cloud Usługa rejestracji Jednokrotnej w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Veritas Enterprise Vault.cloud Usługa rejestracji Jednokrotnej do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w rejestracji Jednokrotnej Vault.cloud Enterprise Veritas musi można ustanowić.

W Veritas Enterprise Vault.cloud logowania jednokrotnego, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego przy użyciu logowania jednokrotnego Vault.cloud Enterprise Veritas, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego logowania jednokrotnego Vault.cloud Enterprise Veritas](#creating-a-veritas-enterprise-vaultcloud-sso-test-user)**  — aby odpowiednikiem Britta Simon w Veritas Enterprise Vault.cloud Usługa rejestracji Jednokrotnej połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji rejestracji Jednokrotnej Vault.cloud Enterprise Veritas.

**Aby skonfigurować usługę Azure AD logowania jednokrotnego przy użyciu logowania jednokrotnego Vault.cloud Enterprise Veritas, wykonaj następujące czynności:**

1. W witrynie Azure portal na **logowania jednokrotnego Vault.cloud Enterprise Veritas** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/veritas-tutorial/tutorial_veritas_samlbase.png)

1. Na **Veritas Enterprise Vault.cloud logowania jednokrotnego domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/veritas-tutorial/tutorial_veritas_url.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://personal.ap.archive.veritas.com/CID=<CUSTOMERID>`

    b. W **identyfikator** pola tekstowego, użyj adresu URL, zgodnie z centrum danych

    | Centrum danych| Adres URL |
    |----------|----|
    | Ameryka Północna| `https://auth.lax.archivecloud.net` |
    | Europa | `https://auth.ams.archivecloud.net` |
    | Azja i Pacyfik| `https://auth.syd.archivecloud.net`|

    c. W **adres URL odpowiedzi** pola tekstowego, użyj adresu URL, zgodnie z centrum danych

    | Centrum danych| Adres URL |
    |----------|----|
    | Ameryka Północna| `https://auth.lax.archivecloud.net` |
    | Europa | `https://auth.ams.archivecloud.net` |
    | Azja i Pacyfik| `https://auth.syd.archivecloud.net`|
    
    > [!NOTE] 
    > Ta wartość nie jest prawdziwe. Zaktualizuj tę wartość przy użyciu rzeczywisty adres URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta logowania jednokrotnego Vault.cloud Enterprise Veritas](https://www.veritas.com/support/.html) aby zyskać tę wartość. 

1. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/veritas-tutorial/tutorial_veritas_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/veritas-tutorial/tutorial_general_400.png)

1. Na **konfiguracji logowania jednokrotnego Vault.cloud przedsiębiorstwa Veritas** , kliknij przycisk **skonfigurować SSO Vault.cloud Enterprise Veritas** można otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/veritas-tutorial/tutorial_veritas_configure.png) 

1. Aby skonfigurować logowanie jednokrotne na **logowania jednokrotnego Vault.cloud Enterprise Veritas** stronie, musisz wysłać pobrany **Certificate(Base64)** i **SAML pojedynczego logowania jednokrotnego adres URL usługi** Aby [zespołem pomocy technicznej logowania jednokrotnego Vault.cloud Enterprise Veritas](https://www.veritas.com/support/.html).

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/veritas-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/veritas-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/veritas-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/veritas-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-veritas-enterprise-vaultcloud-sso-test-user"></a>Tworzenie użytkownika testowego Veritas Enterprise Vault.cloud Usługa rejestracji Jednokrotnej

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w rejestracji Jednokrotnej Vault.cloud przedsiębiorstwa. Praca z [zespołem pomocy technicznej Veritas Enterprise Vault.cloud Usługa rejestracji Jednokrotnej](https://www.veritas.com/support/.html) Aby dodać użytkowników na platformie logowania jednokrotnego Vault.cloud przedsiębiorstwa. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do logowania jednokrotnego Vault.cloud Enterprise Veritas.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Veritas Enterprise Vault.cloud Usługa rejestracji Jednokrotnej, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **logowania jednokrotnego Vault.cloud Enterprise Veritas**.

    ![Konfigurowanie logowania jednokrotnego](./media/veritas-tutorial/tutorial_veritas_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Veritas Enterprise Vault.cloud Usługa rejestracji Jednokrotnej w panelu dostępu, możesz należy pobrać automatycznie zalogowanych do aplikacji Veritas Enterprise Vault.cloud Usługa rejestracji Jednokrotnej.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/veritas-tutorial/tutorial_general_01.png
[2]: ./media/veritas-tutorial/tutorial_general_02.png
[3]: ./media/veritas-tutorial/tutorial_general_03.png
[4]: ./media/veritas-tutorial/tutorial_general_04.png

[100]: ./media/veritas-tutorial/tutorial_general_100.png

[200]: ./media/veritas-tutorial/tutorial_general_200.png
[201]: ./media/veritas-tutorial/tutorial_general_201.png
[202]: ./media/veritas-tutorial/tutorial_general_202.png
[203]: ./media/veritas-tutorial/tutorial_general_203.png

