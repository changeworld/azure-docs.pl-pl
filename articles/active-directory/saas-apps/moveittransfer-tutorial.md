---
title: 'Samouczek: Integracja usługi Azure Active Directory z transferem MOVEit — integracja z usługą Azure AD | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a transferem MOVEit — integracja usługi Azure AD.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8ff7102d-be73-4888-ae81-d8e3d01dd534
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.openlocfilehash: 43383e82d983c998d159728997da4757cc364999
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73161321"
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>Samouczek: Integracja usługi Azure Active Directory z transferem MOVEit — integracja z usługą Azure AD

W tym samouczku dowiesz się, jak zintegrować integrację MOVEit Transfer — Usługa Azure AD z usługą Azure Active Directory (Azure AD).
Integracja MOVEit Transfer — integracja usługi Azure AD z usługą Azure AD zapewnia następujące korzyści:

* Można kontrolować w usłudze Azure AD, który ma dostęp do MOVEit Transfer — integracja usługi Azure AD.
* Można włączyć użytkowników, aby automatycznie zalogować się do MOVEit Transfer — integracja usługi Azure AD (logowanie jednokrotne) z ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z integracją usługi MOVEit — usługa Azure AD, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Transfer MOVEit — subskrypcja z obsługą pojedynczego logowania integracji usługi Azure AD

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Transfer MOVEit — integracja usługi Azure AD obsługuje zainicjowane przez **usługę SP** samosołodę SSO

## <a name="adding-moveit-transfer---azure-ad-integration-from-the-gallery"></a>Dodawanie transferu MOVEit — integracja usługi Azure AD z galerii

Aby skonfigurować integrację MOVEit Transfer — integracja usługi Azure AD z usługą Azure AD, należy dodać MOVEit Transfer — integracja usługi Azure AD z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać moveit transfer — integracja usługi Azure AD z galerii, wykonaj następujące kroki:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **MOVEit Transfer — integracja usługi Azure AD**, wybierz opcję **MOVEit Transfer — integracja usługi Azure AD** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

     ![MOVEit Transfer — integracja usługi Azure AD na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego za pomocą MOVEit Transfer — integracja usługi Azure AD na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w MOVEit Transfer — należy ustanowić integrację usługi Azure AD.

Aby skonfigurować i przetestować usługę Azure AD jednokrotnego logowania za pomocą usługi MOVEit Transfer — integracja usługi Azure AD, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie transferu MOVEit — integracja usługi Azure AD logowanie jednokrotne](#configure-moveit-transfer---azure-ad-integration-single-sign-on)** — do konfigurowania ustawień logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz transfer MOVEit — użytkownika testowego integracji usługi Azure AD](#create-moveit-transfer---azure-ad-integration-test-user)** — aby mieć odpowiednik Britta Simon w MOVEit Transfer — integracja usługi Azure AD, która jest połączona z reprezentacją użytkownika usługi Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednookrotne usługi Azure AD za pomocą integracji MOVEit Transfer — Usługa Azure AD, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie Integracja aplikacji **integracji MOVEit Transfer — Azure AD** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML**, jeśli masz **plik metadanych dostawcy usługi**, wykonaj następujące kroki:

    a. Kliknij pozycję **Przekaż plik metadanych**.

    ![Przekazywanie pliku metadanych](common/upload-metadata.png)

    b. Kliknij **logo folderu**, aby wybrać plik metadanych, a następnie kliknij pozycję **Przekaż**.

    ![wybierz plik metadanych](common/browse-upload-metadata.png)

    d. Po pomyślnym przekazaniu pliku metadanych wartość **identyfikatora** i **adresu URL odpowiedzi** zostanie automatycznie wypełniona w sekcji **Podstawowa konfiguracja SAML:**

    ![Transfer MOVEit — informacje o domenie i logowaniach do domen i adresów URL integracji usługi Azure AD](common/sp-identifier-reply.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://contoso.com`

    > [!NOTE]
    > Wartość **adresu URL logowania** nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z [MOVEit Transfer — zespół pomocy technicznej klienta integracji usługi Azure AD,](https://community.ipswitch.com/s/support) aby uzyskać wartość. **Plik metadanych dostawcy usług** można pobrać z adresu URL **metadanych dostawcy usług,** który jest wyjaśniony w dalszej części **sekcji Konfigurowanie transferu MOVEit — integracja usługi Azure AD Logowanie jednokrotne** w samouczku. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W sekcji **Konfigurowanie transferu MOVEit — integracja usługi Azure AD** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-moveit-transfer---azure-ad-integration-single-sign-on"></a>Konfigurowanie transferu MOVEit — integracja usługi Azure AD z logacją jednokrotnym

1. Zaloguj się do dzierżawy MOVEit Transfer jako administrator.

2. W okienku nawigacji po lewej stronie kliknij pozycję **Settings** (Ustawienia).

    ![Sekcja Ustawienia po stronie aplikacji](./media/moveittransfer-tutorial/tutorial_moveittransfer_000.png)

3. Kliknij **łącze Jednokrotne,** które znajduje się w obszarze **Zasady zabezpieczeń -> użytkownik Auth**.

    ![Zasady zabezpieczeń po stronie aplikacji](./media/moveittransfer-tutorial/tutorial_moveittransfer_001.png)

4. Kliknij łącze Adres URL metadanych, aby pobrać dokument metadanych.

    ![Adres URL metadanych dostawcy usług](./media/moveittransfer-tutorial/tutorial_moveittransfer_002.png)
    
   * Sprawdź, czy **identyfikator encji** dopasowuje **identyfikator** w sekcji **Podstawowa konfiguracja SAML** .
   * Sprawdź, czy adres URL lokalizacji usługi **aserfikacyjnej** odpowiada **adresowi URL odpowiedzi** w sekcji **Podstawowa konfiguracja SAML.**
    
     ![Konfigurowanie logowania jednokrotnego po stronie aplikacji](./media/moveittransfer-tutorial/tutorial_moveittransfer_007.png)

5. Kliknij przycisk **Dodaj dostawcę tożsamości,** aby dodać nowego dostawcę tożsamości federacyjnej.

    ![Dodawanie dostawcy tożsamości](./media/moveittransfer-tutorial/tutorial_moveittransfer_003.png)

6. Kliknij **przycisk Przeglądaj...,** aby wybrać plik metadanych pobrany z witryny Azure portal, a następnie kliknij pozycję **Dodaj dostawcę tożsamości,** aby przekazać pobrany plik.

    ![Dostawca tożsamości SAML](./media/moveittransfer-tutorial/tutorial_moveittransfer_004.png)

7. Wybierz **"Tak"** jako **włączone** w **edytowaniu ustawień dostawcy tożsamości federacyjnej ...** i kliknij przycisk **Zapisz**.

    ![Ustawienia dostawcy tożsamości federacyjnej](./media/moveittransfer-tutorial/tutorial_moveittransfer_005.png)

8. Na stronie **Edytowanie ustawień użytkownika dostawcy tożsamości federacyjnej** wykonaj następujące czynności:
    
    ![Edytowanie ustawień dostawcy tożsamości federacyjnej](./media/moveittransfer-tutorial/tutorial_moveittransfer_006.png)
    
    a. Wybierz **saml nameid** jako **nazwę logowania**.
    
    b. Wybierz **inne** jako imię i **nazwisko,** a w `http://schemas.microsoft.com/identity/claims/displayname`polu tekstowym Nazwa **atrybutu** umieść wartość: .
    
    d. Wybierz **pozycję Inne** jako wiadomość **e-mail** i w polu tekstowym Nazwa **atrybutu** umieść wartość: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
    
    d. Wybierz **tak** jako **automatyczne tworzenie konta w logach**.
    
    e. Kliknij przycisk **Save** (Zapisz).

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz **pozycję Nowy użytkownik** u góry ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **\@brittasimon yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Britta Simon do korzystania z usługi Azure logowania jednokrotnego, przyznając dostęp do MOVEit Transfer — integracja usługi Azure AD.

1. W portalu Azure wybierz pozycję **Aplikacje przedsiębiorstwa**, wybierz **pozycję Wszystkie aplikacje**, a następnie wybierz pozycję **MOVEit Transfer — Integracja z usługą Azure AD**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **MOVEit Transfer — Integracja usługi Azure AD**.

    ![Łącze integracji MOVEit Transfer — Usługa Azure AD na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-moveit-transfer---azure-ad-integration-test-user"></a>Tworzenie transferu MOVEit — użytkownik testowy integracji usługi Azure AD

Celem tej sekcji jest utworzenie użytkownika o nazwie Britta Simon w MOVEit Transfer - integracja usługi Azure AD. MOVEit Transfer — integracja usługi Azure AD obsługuje inicjowanie obsługi administracyjnej just-in-time, które zostały włączone. W tej sekcji nie musisz niczego robić. Nowy użytkownik jest tworzony podczas próby dostępu MOVEit Transfer — integracja usługi Azure AD, jeśli jeszcze nie istnieje.

>[!NOTE]
>Jeśli chcesz ręcznie utworzyć użytkownika, należy skontaktować się z [zespołem pomocy technicznej klienta integracji usługi Azure AD MOVEit](https://community.ipswitch.com/s/support).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka integracji MOVEit Transfer — Azure AD w Panelu dostępu należy automatycznie zalogować się do integracji MOVEit Transfer — Azure AD, dla której skonfigurowano logującą się logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

