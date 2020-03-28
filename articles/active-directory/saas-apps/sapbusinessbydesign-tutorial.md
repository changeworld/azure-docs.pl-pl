---
title: 'Samouczek: Integracja usługi Azure Active Directory z sap business bydesign | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a sap business bydesign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 82938920-33ba-47cb-b141-511b46d19e66
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b830f0760b768826b2d937b4a8b2ffbd8e9e2a2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67091676"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>Samouczek: integracja usługi Azure Active Directory z oprogramowaniem SAP Business ByDesign

W tym samouczku dowiesz się, jak zintegrować SAP Business ByDesign z usługą Azure Active Directory (Azure AD).
Integracja SAP Business ByDesign z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD można kontrolować, kto ma dostęp do SAP Business ByDesign.
* Można włączyć użytkowników do automatycznego logowania do SAP Business ByDesign (logowanie jednokrotne) z ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z sap business bydesign, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Subskrypcja z obsługą logowania jednokrotnego SAP Business ByDesign

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* SAP Business ByDesign obsługuje sso inicjowane przez **sp**

## <a name="adding-sap-business-bydesign-from-the-gallery"></a>Dodawanie SAP Business ByDesign z galerii

Aby skonfigurować integrację SAP Business ByDesign z usługą Azure AD, należy dodać SAP Business ByDesign z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać SAP Business ByDesign z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **SAP Business ByDesign**, wybierz **SAP Business ByDesign** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

    ![SAP Business ByDesign na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego z SAP Business ByDesign na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w programie SAP Business ByDesign.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą sap business bydesign, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie jednokrotne SAP Business ByDesign](#configure-sap-business-bydesign-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego SAP Business ByDesign](#create-sap-business-bydesign-test-user)** — aby mieć odpowiednik Britta Simon w SAP Business ByDesign, który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednooznaczne usługi Azure AD za pomocą sap business bydesign, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **SAP Business ByDesign** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Sap Business ByDesign Informacje o domenie i adresach URL logowania jednokrotnego](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<servername>.sapbydesign.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<servername>.sapbydesign.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej sap business bydesign klienta,](https://www.sap.com/products/cloud-analytics.support.html) aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Sap Business ByDesign aplikacja oczekuje potwierdzeń SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty użytkownika** na stronie integracji aplikacji. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Atrybuty użytkownika**.

    ![image](common/edit-attribute.png)

6. Kliknij ikonę **Edytuj,** aby **edytować wartość identyfikatora nazwa**.

    ![image](media/sapbusinessbydesign-tutorial/mail-prefix1.png)

7. W sekcji **Zarządzanie roszczeniami użytkowników** wykonaj ![następujące kroki: obraz](media/sapbusinessbydesign-tutorial/mail-prefix2.png)

    a. Wybierz **opcję Transformacja** jako **źródło**.

    b. Z listy rozwijanej **Transformacja** wybierz pozycję **ExtractMailPrefix()**.

    d. Na liście rozwijanej **Parametr 1** wybierz atrybut użytkownika, którego chcesz użyć do implementacji. Na przykład jeśli chcesz użyć identyfikatora EmployeeID jako unikatowego identyfikatora użytkownika, a wartość atrybutu jest przechowywana w elemencie ExtensionAttribute2, wybierz pozycję user.extensionattribute2.

    d. Kliknij przycisk **Zapisz**.

8. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

9. W sekcji **Konfigurowanie sap business bydesign** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-sap-business-bydesign-single-sign-on"></a>Konfigurowanie rejestracji pojedynczej SAP Business ByDesign

1. Zaloguj się do portalu SAP Business ByDesign z uprawnieniami administratora.

2. Przejdź do **wspólnego zadania zarządzania aplikacjami i użytkownikami** i kliknij kartę **Dostawca tożsamości.**

3. Kliknij **pozycję Nowy dostawca tożsamości** i wybierz plik XML metadanych pobrany z witryny Azure portal. Przez zaimportowanie tych metadanych system automatycznie przekaże wymagany certyfikat podpisywania i certyfikat szyfrowania.

    ![Konfigurowanie logowania jednokrotnego](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)

4. Aby uwzględnić **adres URL usługi konsumenta oświadczeń** w żądaniu SAML, wybierz pozycję **Uwzględnij adres URL usługi konsumenta oświadczeń**.

5. Kliknij przycisk **Activate Single Sign-On** (Aktywuj logowanie jednokrotne).

6. Zapisz zmiany.

7. Kliknij kartę **My System** (Mój system).

    ![Konfigurowanie logowania jednokrotnego](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)

8. W polu tekstowym **adresu URL logowania usługi AD ad** wklej wartość adresu URL **logowania,** która została skopiowana z witryny Azure portal.

    ![Konfigurowanie logowania jednokrotnego](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)

9. Określ, czy pracownik może ręcznie wybrać między logowaniem za pomocą identyfikatora użytkownika i hasła lub logowania jednośląkiego, wybierając **ręczny wybór dostawcy tożsamości**.

10. W sekcji **Adres URL logowania do rejestracjij przyuśmij** adres URL określ adres URL, który powinien być używany przez pracownika do logowania się do systemu.
    Na liście rozwijanej Adres URL wysłany do pracownika możesz wybrać jedną z następujących opcji:

    **Non-SSO URL** (Adres URL w przypadku braku obsługi logowania jednokrotnego)

    System wysyła do pracownika tylko normalny adres URL systemu. Pracownik nie może logowanie przy użyciu logowania jednośliowego i zamiast tego należy użyć hasła lub certyfikatu.

    **SSO URL** (Adres URL logowania jednokrotnego)

    System wysyła do pracownika tylko adres URL logowania jednokrotnego. Pracownik może podpisać za pomocą logowania sytego. Żądanie uwierzytelnienia zostanie przekierowane za pośrednictwem dostawcy tożsamości.

    **Automatic Selection** (Wybór automatyczny)

    Jeśli logowanie jednokrotne nie jest aktywne, system wysyła do pracownika normalny adres URL systemu. Jeśli logowanie jednokrotne jest aktywne, system sprawdza, czy pracownik ma hasło. Jeśli hasło jest dostępne, do pracownika wysyłany jest zarówno adres URL logowania jednokrotnego, jak i adres URL w przypadku braku obsługi logowania jednokrotnego. Jeśli jednak pracownik nie ma hasła, do pracownika wysyłany jest tylko adres URL logowania jednokrotnego.

11. Zapisz zmiany.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz **pozycję Nowy użytkownik** u góry ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa** `brittasimon@yourcompanydomain.extension`użytkownika wpisz . Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Britta Simon do korzystania z azure logowania jednokrotnego, udzielając dostępu do SAP Business ByDesign.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz **pozycję Wszystkie aplikacje,** a następnie wybierz **pozycję SAP Business ByDesign**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **pozycję SAP Business ByDesign**.

    ![Łącze SAP Business ByDesign na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-sap-business-bydesign-test-user"></a>Tworzenie użytkownika testowego SAP Business ByDesign

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w SAP Business ByDesign. Aby dodać użytkowników na platformie SAP Business ByDesign, należy współpracować z [zespołem pomocy technicznej SAP Business ByDesign](https://www.sap.com/products/cloud-analytics.support.html) Client. 

> [!NOTE]
> Upewnij się, że wartość NameID powinna być zgodna z polem nazwy użytkownika na platformie SAP Business ByDesign.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka SAP Business ByDesign w Panelu dostępu należy automatycznie zalogować się do sap business bydesign, dla którego skonfigurowano logującą się jednoślik. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
