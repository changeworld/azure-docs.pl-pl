---
title: 'Samouczek: Integracja usługi Azure Active Directory z aplikacją Fidelity NetBenefits | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Fidelity NetBenefits.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 77dc8a98-c0e7-4129-ab88-28e7643e432a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b2b35deab4c4fb20c58e135c0de01a0aa05b1c33
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "65862671"
---
# <a name="tutorial-azure-active-directory-integration-with-fidelity-netbenefits"></a>Samouczek: Integracja usługi Azure Active Directory z aplikacją Fidelity NetBenefits

Z tego samouczka dowiesz się, jak zintegrować aplikację Fidelity NetBenefits z usługą Azure Active Directory (Azure AD).
Integrowanie aplikacji Fidelity NetBenefits z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować w usłudze Azure AD, kto ma dostęp do aplikacji Fidelity NetBenefits.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do aplikacji Fidelity NetBenefits (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji usługi Azure AD z aplikacją Fidelity NetBenefits potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji Fidelity NetBenefits z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Fidelity NetBenefits obsługuje logowanie jednokrotne inicjowane przez **dostawcę tożsamości**

* Aplikacja Fidelity NetBenefits obsługuje aprowizowanie użytkowników typu **Just In Time**

## <a name="adding-fidelity-netbenefits-from-the-gallery"></a>Dodawanie aplikacji Fidelity NetBenefits z galerii

Aby skonfigurować integrację aplikacji Fidelity NetBenefits z usługą Azure AD, musisz dodać aplikację Fidelity NetBenefits z galerii do swojej listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację Fidelity NetBenefits z galerii, wykonaj następujące kroki:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Fidelity NetBenefits**, wybierz pozycję **Fidelity NetBenefits** z panelu wyników i kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Aplikacja Fidelity NetBenefits na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z aplikacją Fidelity NetBenefits, korzystając z danych użytkownika testowego **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji Fidelity NetBenefits.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z aplikacją Fidelity NetBenefits, należy wykonać czynności opisane w poniższych blokach konstrukcyjnych:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w aplikacji Fidelity NetBenefits](#configure-fidelity-netbenefits-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego aplikacji Fidelity NetBenefits](#create-fidelity-netbenefits-test-user)** — aby mieć w aplikacji Fidelity NetBenefits odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w aplikacji Fidelity NetBenefits, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **Fidelity NetBenefits** wybierz pozycję **Logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** wykonaj następujące kroki:

    ![Domena i adresy URL aplikacji Fidelity NetBenefits — informacje dotyczące logowania jednokrotnego](common/idp-intiated.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca:

    W przypadku środowiska testowego: `urn:sp:fidelity:geninbndnbparts20:uat:xq1`

    W przypadku środowiska produkcyjnego: `urn:sp:fidelity:geninbndnbparts20`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL dostarczony przez firmę Fidelity w czasie wdrażania lub skontaktuj się z przypisanym menedżerem ds. obsługi klienta Fidelity.

5. Aplikacja Fidelity NetBenefits oczekuje asercji SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu języka SAML. Na poniższym zrzucie ekranu przedstawiono listę atrybutów domyślnych, gdzie atrybut **nameidentifier** jest mapowany na atrybut **user.userprincipalname**. Aplikacja Fidelity NetBenefits oczekuje mapowania atrybutu **nameidentifier** z oświadczeniem **employeeid** lub dowolnym innym oświadczeniem odpowiednim dla danej organizacji jako atrybut **nameidentifier**, więc należy edytować mapowanie atrybutów, klikając ikonę **Edytuj**, i zmienić mapowanie atrybutów.

    ![image](common/edit-attribute.png)

    >[!Note]
    >Aplikacja Fidelity NetBenefits obsługuje federację statyczną i dynamiczną. Statyczna oznacza, że nie będzie używać aprowizacji użytkowników typu just in time opartej na SAML, natomiast dynamiczna oznacza, że obsługuje aprowizację użytkowników typu just in time. W przypadku używania aprowizacji typu JIT klienci muszą dodać więcej oświadczeń w usłudze Azure AD, takich jak data urodzenia (birthdate) użytkownika itd. Te informacje są udostępniane przez przypisanego **menedżera ds obsługi klienta Fidelity**, który musi włączyć tę federację dynamiczną dla danego wystąpienia.

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/metadataxml.png)

7. W sekcji **Konfigurowanie aplikacji Fidelity NetBenefits** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-fidelity-netbenefits-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w aplikacji Fidelity NetBenefits

Aby skonfigurować logowanie jednokrotne po stronie aplikacji **Fidelity NetBenefits**, musisz wysłać pobrany **kod XML metadanych federacji** i odpowiednie adresy URL skopiowane z witryny Azure Portal [zespołowi pomocy technicznej aplikacji Fidelity NetBenefits](mailto:SSOMaintenance@fmr.com). Ustawiają to ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola **brittasimon\@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz dla użytkownika Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji Fidelity NetBenefits.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, pozycję **Wszystkie aplikacje**, a następnie pozycję **Fidelity NetBenefits**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Fidelity NetBenefits**.

    ![Link aplikacji Fidelity NetBenefits na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-fidelity-netbenefits-test-user"></a>Tworzenie użytkownika testowego aplikacji Fidelity NetBenefits

W tej sekcji utworzysz w aplikacji Fidelity NetBenefits użytkownika o nazwie Britta Simon. Jeśli tworzysz federację statyczną, skontaktuj się z przypisanym **menedżerem usługi klienta Fidelity** w celu utworzenia użytkowników na platformie Fidelity NetBenefits. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

W przypadku federacji dynamicznej użytkownicy są tworzeni przy użyciu aprowizacji użytkowników typu Just In Time. W przypadku używania aprowizacji typu JIT klienci muszą dodać więcej oświadczeń w usłudze Azure AD, takich jak data urodzenia (birthdate) użytkownika itd. Te informacje są udostępniane przez przypisanego **menedżera ds obsługi klienta Fidelity**, który musi włączyć tę federację dynamiczną dla danego wystąpienia.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Fidelity NetBenefits w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Fidelity NetBenefits, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

