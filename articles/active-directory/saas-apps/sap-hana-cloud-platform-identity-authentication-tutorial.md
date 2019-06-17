---
title: 'Samouczek: integracja usługi Azure Active Directory z usługą SAP Cloud Platform Identity Authentication | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługami Azure Active Directory i SAP Cloud Platform Identity Authentication.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: eff07f157f6011b9b561334c00b3a4d94989ff5a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67091853"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform-identity-authentication"></a>Samouczek: integracja usługi Azure Active Directory z usługą SAP Cloud Platform Identity Authentication

Z tego samouczka dowiesz się, jak zintegrować usługę SAP Cloud Platform Identity Authentication z usługą Azure Active Directory (Azure AD).
Zintegrowanie usługi SAP Cloud Platform Identity Authentication z usługą Azure AD daje następujące korzyści:

* Możliwość kontrolowania dostępu do usługi SAP Cloud Platform Identity Authentication za pomocą usługi Azure AD.
* Umożliwianie użytkownikom automatycznego logowania się w usłudze SAP Cloud Platform Identity Authentication (logowania jednokrotnego) przy użyciu ich kont usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą SAP Cloud Platform Identity Authentication, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja usługi SAP Cloud Platform Identity Authentication z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa SAP Cloud Platform Identity Authentication obsługuje logowanie jednokrotne zainicjowane przez **dostawcę usługi** oraz **dostawcę tożsamości**

Zanim zagłębisz się w szczegóły techniczne, konieczne jest zrozumienie koncepcji, które zostaną omówione. Usługi SAP Cloud Platform Identity Authentication oraz Active Directory Federation Services umożliwiają wdrożenie logowania jednokrotnego we wszystkich aplikacjach i usługach chronionych przez usługę Azure AD (jako dostawcę tożsamości) z aplikacjami i usługami SAP chronionymi przez usługę SAP Cloud Platform Identity Authentication.

Obecnie usługa SAP Cloud Platform Identity Authentication pełni funkcję dodatkowego dostawcy tożsamości w aplikacjach SAP. Usługa Azure Active Directory z kolei pełni funkcję głównego dostawcy tożsamości w tej konfiguracji. 

Na poniższym diagramie przedstawiono tę relację:

![Tworzenie użytkownika testowego usługi Azure AD](./media/sap-hana-cloud-platform-identity-authentication-tutorial/architecture-01.png)

W tej konfiguracji dzierżawa usługi SAP Cloud Platform Identity Authentication jest konfigurowana jako zaufana aplikacja w usłudze Azure Active Directory.

Wszystkie usługi i aplikacje SAP, które chcesz zabezpieczyć w ten sposób, są następnie konfigurowane w konsoli zarządzania usługą SAP Cloud Platform Identity Authentication.

Autoryzacja dostępu do aplikacji i usług SAP musi mieć miejsce w usłudze SAP Cloud Platform Identity Authentication, a nie w usłudze Azure Active Directory.

Dzięki skonfigurowaniu usługi SAP Cloud Platform Identity Authentication jako aplikacji w witrynie Azure Active Directory Marketplace nie musisz konfigurować pojedynczych oświadczeń lub asercji SAML.

> [!NOTE]
> Obecnie tylko logowanie jednokrotne przez Internet zostało przetestowane przez obie strony. Przepływy, które są niezbędne do nawiązania komunikacji pomiędzy aplikacją a interfejsem API lub pomiędzy interfejsami API, powinny działać, ale nie zostały jeszcze przetestowane. Zostaną przetestowane później.

## <a name="adding-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Dodawanie usługi SAP Cloud Platform Identity Authentication z galerii

Aby skonfigurować integrację usługi SAP Cloud Platform Identity Authentication z usługą Azure AD, należy dodać aplikację SAP Cloud Platform Identity Authentication z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację SAP Cloud Platform Identity Authentication z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz frazę **SAP Cloud Platform Identity Authentication**, wybierz pozycję **SAP Cloud Platform Identity Authentication** z panelu wyników, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Aplikacja SAP Cloud Platform Identity Authentication na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z aplikacją [Application name] w oparciu o użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji [Application name].

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z aplikacją [Application name], należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego usługi SAP Cloud Platform Identity Authentication](#configure-sap-cloud-platform-identity-authentication-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego usługi SAP Cloud Platform Identity Authentication](#create-sap-cloud-platform-identity-authentication-test-user)** — aby udostępnić usłudze SAP Cloud Platform Identity Authentication odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD z aplikacją [Application name], wykonaj następujące czynności:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **SAP Cloud Platform Identity Authentication** wybierz pozycję **Logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz przeprowadzić konfigurację w trybie inicjowanym przez **dostawcę tożsamości**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki:

    ![Domena i adresy URL usługi SAP Cloud Platform Identity Authentication — informacje dotyczące logowania jednokrotnego](common/idp-intiated.png)

    a. W polu **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `<IAS-tenant-id>.accounts.ondemand.com`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Skontaktuj się z [zespołem obsługi klienta usługi SAP Cloud Platform Identity Authentication](https://cloudplatform.sap.com/capabilities/security/trustcenter.html), aby uzyskać te wartości. Jeśli nie rozumiesz wartości identyfikatora, zapoznaj się z tematem [Tenant SAML 2.0 configuration (Konfiguracja formatu SAML 2.0 dzierżawy)](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html) w dokumentacji usługi SAP Cloud Platform Identity Authentication.

5. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    ![Domena i adresy URL usługi SAP Cloud Platform Identity Authentication — informacje dotyczące logowania jednokrotnego](common/metadata-upload-additional-signon.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `{YOUR BUSINESS APPLICATION URL}`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Użyj konkretnego adresu URL logowania aplikacji biznesowej. Skontaktuj się z [zespołem obsługi klienta usługi SAP Cloud Platform Identity Authentication](https://cloudplatform.sap.com/capabilities/security/trustcenter.html), jeśli masz jakiekolwiek wątpliwości.

6. Aplikacja SAP Cloud Platform Identity Authentication oczekuje asercji SAML w konkretnym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty użytkownika** na stronie integracji aplikacji. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Atrybuty użytkownika**.

    ![image](common/edit-attribute.png)

7. Jeśli aplikacja SAP oczekuje atrybutu, na przykład **firstName**, dodaj atrybut **firstName** w sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika**, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej, i wykonaj następujące czynności:

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu firstName.

    c. Pozostaw pole **Przestrzeń nazw** puste.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wybierz wartość atrybutu user.givenname.

    f. Kliknij przycisk **OK**.

    g. Kliknij pozycję **Zapisz**.

8. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/metadataxml.png)

9. W sekcji **Konfigurowanie usługi SAP Cloud Platform Identity Authentication** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-sap-cloud-platform-identity-authentication-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi SAP Cloud Platform Identity Authentication

1. Aby umożliwić aplikacji skonfigurowanie logowania jednokrotnego, przejdź do konsoli administracyjnej usługi SAP Cloud Platform Identity Authentication. Adres URL odpowiada następującemu wzorcowi: `https://<tenant-id>.accounts.ondemand.com/admin`. Następnie zapoznaj się z tematem [Integration with Microsoft Azure AD (Integracja z usługą Microsoft Azure AD)](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html) w dokumentacji usługi SAP Cloud Platform Identity Authentication.

2. W witrynie Azure Portal wybierz przycisk **Zapisz**.

3. Wykonuj dalsze czynności tylko, jeśli chcesz dodać i włączyć logowanie jednokrotne dla kolejnej aplikacji SAP. Powtórz kroki opisane w sekcji **Dodawanie usługi SAP Cloud Platform Identity Authentication z galerii**.

4. W witrynie Azure Portal na stronie integracji aplikacji **SAP Cloud Platform Identity Authentication**, wybierz pozycję **Połączone logowanie**.

    ![Konfigurowanie funkcji Połączone logowanie](./media/sap-hana-cloud-platform-identity-authentication-tutorial/linked_sign_on.png)

5. Zapisz konfigurację.

> [!NOTE]
> Nowa aplikacja wykorzystuje konfigurację pojedynczego logowania poprzedniej aplikacji SAP. Upewnij się, że korzystasz z tych samych firmowych dostawców tożsamości w konsoli administracyjnej usługi SAP Cloud Platform Identity Authentication.

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

W tej sekcji udostępnisz użytkownikowi Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do usługi SAP Cloud Platform Identity Authentication.

1. W witrynie Azure Portal wybierz pozycje **Aplikacje dla przedsiębiorstw** i **Wszystkie aplikacje**, a następnie **SAP Cloud Platform Identity Authentication**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **SAP Cloud Platform Identity Authentication**.

    ![Link do usługi SAP Cloud Platform Identity Authentication na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-sap-cloud-platform-identity-authentication-test-user"></a>Tworzenie użytkownika testowego usługi SAP Cloud Platform Identity Authentication

Nie musisz tworzyć użytkownika w usłudze SAP Cloud Platform Identity Authentication. Użytkownicy, którzy są w magazynie użytkowników usługi Azure AD, mogą korzystać z funkcji logowania jednokrotnego.

Usługa SAP Cloud Platform Identity Authentication obsługuje opcję federacji tożsamości. Ta opcja umożliwia aplikacji sprawdzenie, czy użytkownicy, którzy są uwierzytelniani przez firmowego dostawcę tożsamości, istnieją w magazynie użytkowników usługi SAP Cloud Platform Identity Authentication.

Opcja federacji tożsamości jest domyślnie wyłączona. Jeśli opcja federacji tożsamości jest włączona, tylko użytkownicy, którzy są zaimportowani w usłudze SAP Cloud Platform Identity Authentication, mogą uzyskać dostęp do aplikacji.

Abu uzyskać więcej informacji na temat włączania lub wyłączania funkcji federacji tożsamości w usłudze SAP Cloud Platform Identity Authentication, zobacz „Enable Identity Federation with SAP Cloud Platform Identity Authentication” („Włączanie funkcji federacji tożsamości w usłudze SAP Cloud Platform Identity Authentication”) w temacie [Configure Identity Federation with the User Store of SAP Cloud Platform Identity Authentication (Konfigurowanie funkcji federacji tożsamości z magazynem użytkowników w usłudze SAP Cloud Platform Identity Authentication)](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka SAP Cloud Platform Identity Authentication w panelu dostępu powinno nastąpić automatyczne zalogowanie do usługi SAP Cloud Platform Identity Authentication, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
