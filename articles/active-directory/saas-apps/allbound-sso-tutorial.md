---
title: 'Samouczek: Integracja usługi Azure Active Directory przy użyciu logowania jednokrotnego Allbound | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Allbound logowania jednokrotnego.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 15011ddf-941f-4da2-b993-40ad94a08e42
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2018
ms.author: jeedes
ms.openlocfilehash: e0658841e121dc8ea5bd4c704d99fd6bac98a32f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55171392"
---
# <a name="tutorial-azure-active-directory-integration-with-allbound-sso"></a>Samouczek: Integracja usługi Azure Active Directory przy użyciu logowania jednokrotnego Allbound

W tym samouczku dowiesz się, jak zintegrować Allbound logowanie Jednokrotne z usługą Azure Active Directory (Azure AD).

Integrowanie Allbound logowanie Jednokrotne z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do logowania jednokrotnego Allbound.
- Użytkowników, aby automatycznie uzyskać zalogowanych do Allbound SSO (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu logowania jednokrotnego Allbound, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Usługa rejestracji Jednokrotnej Allbound logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie rejestracji Jednokrotnej Allbound z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-allbound-sso-from-the-gallery"></a>Dodawanie rejestracji Jednokrotnej Allbound z galerii

Aby skonfigurować integrację Allbound logowania jednokrotnego w usłudze Azure AD, należy dodać Allbound logowania jednokrotnego z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Allbound logowania jednokrotnego z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

4. W polu wyszukiwania wpisz **Allbound logowania jednokrotnego**, wybierz opcję **Allbound logowania jednokrotnego** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Usługa rejestracji Jednokrotnej Allbound, na liście wyników](./media/allbound-sso-tutorial/tutorial_allbound-sso_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować, i test usługi Azure AD logowania jednokrotnego przy użyciu logowania jednokrotnego Allbound oparte na użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Allbound logowania jednokrotnego do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Allbound logowania jednokrotnego musi można ustanowić.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego przy użyciu logowania jednokrotnego Allbound, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego logowania jednokrotnego Allbound](#creating-an-allbound-sso-test-user)**  — aby odpowiednikiem Britta Simon w Allbound Usługa rejestracji Jednokrotnej, połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji rejestracji Jednokrotnej Allbound.

**Aby skonfigurować usługę Azure AD logowania jednokrotnego przy użyciu logowania jednokrotnego Allbound, wykonaj następujące czynności:**

1. W witrynie Azure portal na **logowania jednokrotnego Allbound** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

2. Na **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, kliknij przycisk **wybierz** dla **SAML** trybu, aby włączyć logowanie jednokrotne.

    ![Konfigurowanie logowania jednokrotnego](common/tutorial_general_301.png)

3. Na **Ustaw się logowanie jednokrotne z SAML** kliknij **Edytuj** ikonę, aby otworzyć **podstawową konfigurację protokołu SAML** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](common/editconfigure.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **dostawcę tożsamości**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki:

    ![Domena logowania jednokrotnego Allbound i adresy URL pojedynczego logowania jednokrotnego informacji](./media/allbound-sso-tutorial/tutorial_allbound-sso_url.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://<SUBDOMAIN>.allbound.com/`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<SUBDOMAIN>.allbound.com/acs`

5. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    ![Domena logowania jednokrotnego Allbound i adresy URL pojedynczego logowania jednokrotnego informacji](./media/allbound-sso-tutorial/tutorial_allbound-sso_url1.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<SUBDOMAIN>.allbound.com/`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta logowania jednokrotnego Allbound](mailto:engineering@allbound.com) do uzyskania tych wartości.

6. Na **certyfikat podpisywania SAML** strony w **certyfikat podpisywania SAML** kliknij **Pobierz** można pobrać **XML metadanych Federacji** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link do pobierania certyfikatu](./media/allbound-sso-tutorial/tutorial_allbound-sso_certificate.png) 

7. Na **skonfigurować logowanie Jednokrotne Allbound** sekcji, skopiuj odpowiedni adres URL, zgodnie z wymaganiami.

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

    ![Konfiguracja logowania jednokrotnego Allbound](common/configuresection.png)

8. Aby skonfigurować logowanie jednokrotne na **Allbound logowania jednokrotnego** stronie, musisz wysłać pobrany **XML metadanych Federacji** do [zespołem pomocy technicznej Allbound logowania jednokrotnego](mailto:engineering@allbound.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Utwórz użytkownika usługi Azure AD][100]

2. Wybierz **nowego użytkownika** w górnej części ekranu.

    ![Tworzenie użytkownika testowego usługi Azure AD](common/create_aaduser_01.png) 

3. We właściwościach użytkownika wykonaj następujące czynności.

    ![Tworzenie użytkownika testowego usługi Azure AD](common/create_aaduser_02.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    c. Wybierz **właściwości**, wybierz opcję **hasło Show** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w polu hasło.

    d. Wybierz pozycję **Utwórz**.

### <a name="creating-an-allbound-sso-test-user"></a>Tworzenie użytkownika testowego Allbound logowania jednokrotnego

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon w Allbound logowania jednokrotnego. Usługa rejestracji Jednokrotnej Allbound obsługę just-in-time, który jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Nowy użytkownik jest tworzony podczas próby dostępu Allbound Usługa rejestracji Jednokrotnej, jeśli go jeszcze nie istnieje.
>[!Note]
>Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej Allbound logowania jednokrotnego](mailto:engineering@allbound.com).

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do Allbound logowania jednokrotnego.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji wybierz **logowania jednokrotnego Allbound**.

    ![Konfigurowanie logowania jednokrotnego](./media/allbound-sso-tutorial/tutorial_allbound-sso_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. W **użytkowników i grup** okna dialogowego wybierz **Britta Simon** na liście użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.

6. W **Dodaj przydziału** okna dialogowego wybierz **przypisać** przycisku.

### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Allbound logowania jednokrotnego w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do Allbound logowania jednokrotnego aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
