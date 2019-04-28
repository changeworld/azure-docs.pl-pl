---
title: 'Samouczek: Integracja usługi Azure Active Directory z oprogramowaniem SAP Business ByDesign | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i oprogramowaniem SAP Business ByDesign.
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
ms.openlocfilehash: 1bb7caedd440f6591d0e538f69eac4abf4e6d5be
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62104803"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>Samouczek: Integracja usługi Azure Active Directory z oprogramowaniem SAP Business ByDesign

W tym samouczku dowiesz się, jak zintegrować z oprogramowaniem SAP Business ByDesign za pomocą usługi Azure Active Directory (Azure AD).
Integracja z oprogramowaniem SAP Business ByDesign z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do oprogramowaniem SAP Business ByDesign.
* Użytkownikom można automatycznie zalogował się oprogramowaniem SAP Business ByDesign (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z oprogramowaniem SAP Business ByDesign, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/)
* SAP oprogramowaniem Business ByDesign logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługuje z oprogramowaniem SAP Business ByDesign **SP** jednokrotne logowanie inicjowane przez

## <a name="adding-sap-business-bydesign-from-the-gallery"></a>Dodawanie z oprogramowaniem SAP Business ByDesign z galerii

Aby skonfigurować integrację z oprogramowaniem SAP Business ByDesign w usłudze Azure AD, należy dodać oprogramowaniem SAP Business ByDesign z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać oprogramowaniem SAP Business ByDesign z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **oprogramowaniem SAP Business ByDesign**, wybierz opcję **oprogramowaniem SAP Business ByDesign** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Oprogramowaniem SAP Business ByDesign na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz skonfigurować i przetestować usługi Azure AD logowanie jednokrotne z oprogramowaniem SAP Business ByDesign w oparciu o nazwie użytkownika testowego **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w oprogramowaniem SAP Business ByDesign musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z oprogramowaniem SAP Business ByDesign, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie SAP Business oprogramowaniem ByDesign logowania jednokrotnego](#configure-sap-business-bydesign-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego z oprogramowaniem SAP Business ByDesign](#create-sap-business-bydesign-test-user)**  — aby odpowiednikiem Britta Simon w oprogramowaniem SAP Business ByDesign jest połączony z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługi Azure AD logowanie jednokrotne z oprogramowaniem SAP Business ByDesign, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **oprogramowaniem SAP Business ByDesign** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![SAP Business oprogramowaniem ByDesign domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<servername>.sapbydesign.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<servername>.sapbydesign.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej SAP Business oprogramowaniem ByDesign klienta](https://www.sap.com/products/cloud-analytics.support.html) do uzyskania tych wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Aplikacja oprogramowaniem Business ByDesign SAP oczekuje twierdzenia SAML, w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty użytkownika** na stronie integracji aplikacji. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Atrybuty użytkownika**.

    ![image](common/edit-attribute.png)

6. Kliknij pozycję **Edytuj** ikona edycji **nazwę wartości identyfikatora**.

    ![image](media/sapbusinessbydesign-tutorial/mail-prefix1.png)

7. Na **Zarządzanie oświadczenia użytkownika** sekcji, wykonaj następujące kroki: ![obrazu](media/sapbusinessbydesign-tutorial/mail-prefix2.png)

    a. Wybierz **przekształcania** jako **źródła**.

    b. W **przekształcania** listy rozwijanej wybierz **ExtractMailPrefix()**.

    c. W **parametr 1** listy rozwijanej wybierz atrybut użytkownika, którego chcesz użyć dla wdrożenia. Na przykład jeśli chcesz użyć identyfikatora EmployeeID jako unikatowego identyfikatora użytkownika, a wartość atrybutu jest przechowywana w elemencie ExtensionAttribute2, wybierz pozycję user.extensionattribute2.

    d. Kliknij pozycję **Zapisz**.

8. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

9. Na **Konfigurowanie z oprogramowaniem SAP Business ByDesign** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-sap-business-bydesign-single-sign-on"></a>Konfigurowanie SAP Business oprogramowaniem ByDesign logowanie jednokrotne

1. Zaloguj się do portalu oprogramowaniem SAP Business ByDesign z uprawnieniami administratora.

2. Przejdź do **aplikacji i typowe zadanie zarządzania użytkownikami** i kliknij przycisk **dostawcy tożsamości** kartę.

3. Kliknij przycisk **nowego dostawcę tożsamości** i wybierz plik XML metadanych, który został pobrany z witryny Azure portal. Przez zaimportowanie tych metadanych system automatycznie przekaże wymagany certyfikat podpisywania i certyfikat szyfrowania.

    ![Konfigurowanie logowania jednokrotnego](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)

4. Obejmujący **adres URL usługi konsumenta potwierdzenie** w żądaniu języka SAML wybierz **zawierają potwierdzenie konsumenta adres URL usługi**.

5. Kliknij przycisk **Activate Single Sign-On** (Aktywuj logowanie jednokrotne).

6. Zapisz zmiany.

7. Kliknij kartę **My System** (Mój system).

    ![Konfigurowanie logowania jednokrotnego](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)

8. W **adres URL logowania usługi AD Azure** pola tekstowego, Wklej **adres URL logowania** wartości, które zostały skopiowane z witryny Azure portal.

    ![Konfigurowanie logowania jednokrotnego](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)

9. Określ, czy pracownik można ręcznie wybrać między logowanie się przy użyciu Identyfikatora użytkownika i hasła lub logowania jednokrotnego, wybierając **wyboru dostawcy tożsamości ręczne**.

10. W **adres URL logowania jednokrotnego** sekcji, określ adres URL, które mają być używane przez pracowników, aby logować się do systemu.
    W URL wysyłane do listy rozwijanej pracownika można wybrać jedną z następujących opcji:

    **Non-SSO URL** (Adres URL w przypadku braku obsługi logowania jednokrotnego)

    System wysyła do pracownika tylko normalny adres URL systemu. Pracownik nie może logować przy użyciu logowania jednokrotnego i musi użyć hasła lub zamiast tego certyfikatu.

    **SSO URL** (Adres URL logowania jednokrotnego)

    System wysyła do pracownika tylko adres URL logowania jednokrotnego. Pracownik może się logować przy użyciu logowania jednokrotnego. Żądanie uwierzytelnienia zostanie przekierowane za pośrednictwem dostawcy tożsamości.

    **Automatic Selection** (Wybór automatyczny)

    Jeśli logowanie jednokrotne nie jest aktywne, system wysyła do pracownika normalny adres URL systemu. Jeśli logowanie jednokrotne jest aktywne, system sprawdza, czy pracownik ma hasło. Jeśli hasło jest dostępne, do pracownika wysyłany jest zarówno adres URL logowania jednokrotnego, jak i adres URL w przypadku braku obsługi logowania jednokrotnego. Jeśli jednak pracownik nie ma hasła, do pracownika wysyłany jest tylko adres URL logowania jednokrotnego.

11. Zapisz zmiany.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola `brittasimon@yourcompanydomain.extension`. Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon korzystać z platformy Azure logowania jednokrotnego przez udzielenie dostępu z oprogramowaniem SAP Business ByDesign.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **oprogramowaniem SAP Business ByDesign**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **oprogramowaniem SAP Business ByDesign**.

    ![Link oprogramowaniem SAP Business ByDesign na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-sap-business-bydesign-test-user"></a>Tworzenie użytkownika testowego z oprogramowaniem SAP Business ByDesign

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w oprogramowaniem SAP Business ByDesign. Skontaktuj się z [zespołem pomocy technicznej SAP Business oprogramowaniem ByDesign klienta](https://www.sap.com/products/cloud-analytics.support.html) Aby dodać użytkowników z oprogramowaniem SAP Business ByDesign platformy. 

> [!NOTE]
> Upewnij się, że wartość identyfikatora NameID powinny być zgodne z pola Nazwa użytkownika na platformie oprogramowaniem SAP Business ByDesign.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka oprogramowaniem SAP Business ByDesign w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze oprogramowaniem SAP Business ByDesign dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
