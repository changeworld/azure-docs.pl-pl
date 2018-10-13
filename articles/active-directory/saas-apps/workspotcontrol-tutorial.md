---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą kontrolki Workspot | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Workspot Control.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3ea8e4e9-f61f-4f45-b635-b0e306eda3d1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2018
ms.author: jeedes
ms.openlocfilehash: 520e625ba9689ebf35e985fe95609c62102e2493
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2018
ms.locfileid: "49312985"
---
# <a name="tutorial-azure-active-directory-integration-with-workspot-control"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą kontrolki Workspot

W tym samouczku dowiesz się, jak zintegrować Workspot kontroli przy użyciu usługi Azure Active Directory (Azure AD).

Integrowanie kontroli Workspot z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do sterowania Workspot.
- Użytkowników, aby automatycznie uzyskać zalogowanych do sterowania Workspot (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą kontrolki Workspot, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Kontrolka Workspot logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie kontrolki Workspot z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-workspot-control-from-the-gallery"></a>Dodawanie kontrolki Workspot z galerii
Aby skonfigurować integrację Workspot kontrolki w usłudze Azure AD, należy dodać formant Workspot z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać kontrolkę Workspot z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![image](./media/workspotcontrol-tutorial/selectazuread.png)

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![image](./media/workspotcontrol-tutorial/a_select_app.png)
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![image](./media/workspotcontrol-tutorial/a_new_app.png)

4. W polu wyszukiwania wpisz **Workspot kontroli**, wybierz opcję **Workspot kontroli** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

     ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji można skonfigurować, i test usługi Azure AD logowanie jednokrotne za pomocą kontrolki Workspot oparte na użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w formancie Workspot do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w kontrolce Workspot musi nawiązać.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą kontrolki Workspot, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego kontroli Workspot](#create-a-workspot-control-test-user)**  — aby odpowiednikiem Britta Simon w formant Workspot, który jest połączony z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Workspot kontroli.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne za pomocą kontrolki Workspot, wykonaj następujące czynności:**

1. W [witryny Azure portal](https://portal.azure.com/)na **kontroli Workspot** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![image](./media/workspotcontrol-tutorial/B1_B2_Select_SSO.png)

2. Na **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, wybierz **SAML** trybu, aby włączyć logowanie jednokrotne.

    ![image](./media/workspotcontrol-tutorial/b1_b2_saml_sso.png)

3. Na **Ustaw się logowanie jednokrotne z SAML** kliknij **Edytuj** przycisk, aby otworzyć **podstawową konfigurację protokołu SAML** okna dialogowego.

    ![image](./media/workspotcontrol-tutorial/b1-domains_and_urlsedit.png)

4. Na **podstawową konfigurację protokołu SAML** sekcji, wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w **tożsamości** tryb intiated:

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_url.png)

    a. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<INSTANCENAME>-saml.workspot.com/saml/metadata`

    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<INSTANCENAME>-saml.workspot.com/saml/assertion`

    c. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

     ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_url1.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca:  `https://<INSTANCENAME>-saml.workspot.com/`

    > [!NOTE]
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego adresu identyfikator, adres URL odpowiedzi i logowania jednokrotnego. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta sterowania Workspot](mailto:support@workspot.com) do uzyskania tych wartości. 

5. Na **Ustaw się logowania jednokrotnego przy użyciu protokołu SAML** strony w **certyfikat podpisywania SAML** kliknij **Pobierz** można pobrać **certyfikat (Base64)** i zapisz go na komputerze.

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_certficate.png) 

6. Na **skonfigurować kontrolę Workspot** sekcji, skopiuj odpowiedni adres URL, zgodnie z wymaganiami.

    Należy zwrócić uwagę na to, czy adres URL może wskazywać następujące czynności:

    a. Adres URL logowania

    b. Identyfikator usługi Azure Ad

    c. Adres URL wylogowywania

    ![image](./media/workspotcontrol-tutorial/d1_samlsonfigure.png) 

7. W oknie przeglądarki internetowej innej, zaloguj się do sterowania Workspot jako Administrator zabezpieczeń.

8. Na pasku narzędzi w górnej części strony kliknij **instalacji**, a następnie przejdź do **SAML**.

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_setup.png)

9. Na **konfigurację języka znaczników asercji zabezpieczeń** strony, wykonaj następujące czynności:
 
    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_saml.png)

    a. W **identyfikator jednostki** pola tekstowego, Wklej wartość **usługi Azure Ad identyfikator** skopiowanej w witrynie Azure portal.   

    b.In **adres URL usługi logować** pola tekstowego, Wklej wartość **adres URL logowania** skopiowanej w witrynie Azure portal.

    c. W **adres URL usługi wylogowania** pola tekstowego, Wklej wartość **adres URL wylogowania** skopiowanej w witrynie Azure portal. 

    d. Kliknij pozycję **plik aktualizacji** certyfikat, pobrany z witryny Azure portal do certyfikat X.509 szyfrowany algorytmem przycisk, aby przekazać base-64.

    e. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

1. W witrynie Azure portal w okienku po lewej stronie wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**.

    ![image](./media/workspotcontrol-tutorial/d_users_and_groups.png)

2. Wybierz **nowego użytkownika** w górnej części ekranu.

    ![image](./media/workspotcontrol-tutorial/d_adduser.png)

3. We właściwościach użytkownika wykonaj następujące czynności.

    ![image](./media/workspotcontrol-tutorial/d_userproperties.png)

    a. W **nazwa** pola wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    c. Wybierz **właściwości**, wybierz opcję **hasło Show** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w polu hasło.

    d. Wybierz pozycję **Utwórz**.
 
### <a name="create-a-workspot-control-test-user"></a>Tworzenie użytkownika testowego Workspot kontroli

Aby umożliwić użytkownikom usługi Azure AD zalogować się do kontroli Workspot, musi być obsługiwana w systemie kontroli Workspot. W kontrolce Workspot aprowizacji to zadanie ręczne.

**Aby udostępnić konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do sterowania Workspot jako Administrator zabezpieczeń.

2. Na pasku narzędzi w górnej części strony kliknij **użytkowników**, a następnie przejdź do **Dodaj użytkownika**.

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_adduser.png)

3. Na **dodać nowego użytkownika** strony, wykonaj następujące czynności:

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addnewuser.png)

    a. W **imię** tekstu Wprowadź imię użytkownika, takich jak **Britta**.

    b. W **nazwisko** tekstu wprowadź nazwisko użytkownika, takich jak **simon**.

    c. W **E-mail** tekstu wprowadź adres e-mail użytkownika, takich jak **Brittasimon@contoso.com**.

    d. Wybieranie odpowiedniej roli użytkownika z **roli** listy rozwijanej.

    e. Wybierz do odpowiedniej grupy użytkowników z **grupy** listy rozwijanej.

    f. Kliknij przycisk **Dodaj użytkownika**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do sterowania Workspot.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**.

    ![image](./media/workspotcontrol-tutorial/d_all_applications.png)

2. Na liście aplikacji wybierz **kontroli Workspot**.

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_app.png)

3. W menu po lewej stronie wybierz **użytkowników i grup**.

    ![image](./media/workspotcontrol-tutorial/d_leftpaneusers.png)

4. Wybierz **Dodaj** przycisk, a następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okna dialogowego.

    ![image](./media/workspotcontrol-tutorial/d_assign_user.png)

4. W **użytkowników i grup** okna dialogowego wybierz **Britta Simon** na liście użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.

5. W **Dodaj przydziału** okna dialogowego wybierz **przypisać** przycisku.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Workspot kontrolki w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do Workspot kontroli aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
