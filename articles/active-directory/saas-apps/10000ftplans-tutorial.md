---
title: 'Samouczek: Integracja usługi Azure Active Directory z planami 10 000 stóp | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a planami 10 000 stóp.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b60c955e-8fa3-4872-a897-c4e81fd7beac
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 441c9345f92974c387429baac768dbccb03fc995
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67107603"
---
# <a name="tutorial-azure-active-directory-integration-with-10000ft-plans"></a>Samouczek: Integracja usługi Azure Active Directory z planami 10 000 stóp

W tym samouczku dowiesz się, jak zintegrować plany 10 000 stóp z usługą Azure Active Directory (Azure AD).
Integracja planów 10 000 stóp z usługą Azure AD zapewnia następujące korzyści:

* Można kontrolować w usłudze Azure AD, który ma dostęp do planów 10,000ft.
* Można włączyć użytkowników, aby automatycznie zalogować się do planów 10,000ft (logowanie jednokrotne) z ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z planami 10 000 stóp, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* 10,000ft Plany pojedynczego logowania włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* 10,000ft Plany wsparcia **SP** zainicjowane SSO
* Plany 10,000ft obsługują **inicjowanie** obsługi administracyjnej użytkowników just in time

## <a name="adding-10000ft-plans-from-the-gallery"></a>Dodawanie planów 10,000ft z galerii

Aby skonfigurować integrację planów 10 000 stóp z usługą Azure AD, musisz dodać plany 10 000 stóp z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać plany 10 000 stóp z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** u góry okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **plany 10 000 stóp**, wybierz **10 000-stopowe plany** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

    ![Plany 10,000ft na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego z 10,000ft Plany na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w planach 10 000ft.

Aby skonfigurować i przetestować usługę Azure AD logowania jednokrotnego z 10,000ft plany, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj plany 10000ft Logowanie jednokrotne](#configure-10000ft-plans-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz 10000ft Plany użytkownika testowego](#create-10000ft-plans-test-user)** — mieć odpowiednik Britta Simon w 10,000ft Plany, który jest połączony z reprezentacji usługi Azure AD użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD z planami 10 000 stóp, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Plany 10 000** wybierz pozycję **Logowanie jednokrotne.**

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![10,000ft Plany Domeny i adresy URL single sign-on informacji](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz następujący adres URL: `https://app.10000ft.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL: `https://app.10000ft.com/saml/metadata`

    > [!NOTE]
    > Wartość **identyfikatora** jest inna, jeśli masz domenę niestandardową. Skontaktuj się z [zespołem pomocy technicznej klienta planów 10,000ft,](https://www.10000ft.com/plans/support) aby uzyskać tę wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (nieprzetworzony)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificateraw.png)

6. W sekcji **Konfigurowanie planów 10 000 stóp** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-10000ft-plans-single-sign-on"></a>Konfigurowanie planów 10000ft Logowanie jednokrotne

Aby skonfigurować logowanie jednokrotne po stronie **planów 10 000 stóp,** musisz wysłać pobrany **certyfikat (raw)** i odpowiednie skopiowane adresy URL z witryny Azure portal do [zespołu pomocy technicznej planów 10 000ft.](https://www.10000ft.com/plans/support) Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz **pozycję Nowy użytkownik** u góry ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź pole **BrittaSimon**.
  
    b. W polu **Nazwa** użytkownika `brittasimon@yourcompanydomain.extension`wpisz . Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Britta Simon do korzystania z usługi Azure logowania jednokrotnego, udzielając dostępu do planów 10,000ft.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz **pozycję Wszystkie aplikacje**, a następnie wybierz **plany 10 000 stóp**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **10,000ft Plany**.

    ![Łącze Plany 10 000 stóp na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **Britta Simon** na liście Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

### <a name="create-10000ft-plans-test-user"></a>Tworzenie 10000ft Plany użytkownika testowego

W tej sekcji użytkownik o nazwie Britta Simon jest tworzony w 10,000ft Plany. Plany 10,000ft obsługuje just-in-time in-time user provisioning, który jest domyślnie włączony. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w planach 10,000ft, nowy jest tworzony po uwierzytelnieniu.

> [!NOTE]
> Jeśli chcesz ręcznie utworzyć użytkownika, musisz skontaktować się z [zespołem pomocy technicznej klienta planów 10,000ft.](https://www.10000ft.com/plans/support)

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Plany 10,000ft w Panelu dostępu, należy automatycznie zalogować się do 10,000ft Plany, dla których skonfigurowano logując się logując. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
