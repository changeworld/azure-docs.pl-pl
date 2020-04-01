---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z usługą SAP Cloud dla klienta | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i platformą SAP Cloud for Customer.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 90154dab-eba2-4563-bcf0-f2acc797ea97
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 837787d375a7570b7daf0a149960ca0020bcdced
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72264010"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-cloud-for-customer"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z usługą SAP Cloud for Customer

W tym samouczku dowiesz się, jak zintegrować sap cloud dla klienta z usługi Azure Active Directory (Azure AD). Po zintegrowaniu sap cloud dla klienta z usługą Azure AD, można:

* Kontrola w usłudze Azure AD, która ma dostęp do sap cloud dla klienta.
* Włącz użytkownikom automatyczne logowanie do usługi SAP Cloud dla klientów za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją rejestracji jednokrotnej SAP Cloud dla klienta (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Platforma SAP Cloud for Customer obsługuje logowanie jednokrotne inicjowane przez **dostawcę tożsamości**

## <a name="adding-sap-cloud-for-customer-from-the-gallery"></a>Dodawanie platformy SAP Cloud for Customer z galerii

Aby skonfigurować integrację platformy SAP Cloud for Customer z usługą Azure AD, należy dodać tę platformę z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **SAP Cloud dla klienta** w polu wyszukiwania.
1. Wybierz **SAP Cloud dla klienta** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-cloud-for-customer"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla usługi SAP Cloud dla klienta

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą sap cloud dla klienta przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik sytuować uszjący działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w sap cloud dla klienta.

Aby skonfigurować i przetestować przychylić się do usługi Azure AD sytuować za pomocą sap cloud dla klienta, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj logowanie jednokrotne SAP Cloud for Customer](#configure-sap-cloud-for-customer-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz użytkownika testowego SAP Cloud for Customer](#create-sap-cloud-for-customer-test-user)** — aby mieć odpowiednik B.Simon w SAP Cloud dla klienta, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)— na stronie integracji aplikacji **SAP Cloud dla klientów** znajdź sekcję **Zarządzaj** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<server name>.crm.ondemand.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<server name>.crm.ondemand.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. W celu uzyskania tych wartości skontaktuj się z [zespołem pomocy technicznej klienta platformy SAP Cloud for Customer](https://www.sap.com/about/agreements.sap-cloud-services-customers.html). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Sap Cloud for Customer aplikacja oczekuje potwierdzeń SAML w określonym formacie, który wymaga, aby dodać niestandardowe mapowania atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe Atrybuty użytkownika.

    ![image](common/edit-attribute.png)

1. W sekcji **Atrybuty użytkownika** okna dialogowego **Atrybuty i oświadczenia użytkownika** wykonaj następujące kroki:

    a. Kliknij **ikonę Edytuj**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![image](./media/sap-customer-cloud-tutorial/tutorial_usermail.png)

    ![image](./media/sap-customer-cloud-tutorial/tutorial_usermailedit.png)

    b. Wybierz pozycję **Przekształcanie** dla pola **Źródło**.

    d. Z listy **Przekształcenie** wybierz pozycję **ExtractMailPrefix()**.

    d. Z listy **Parametr 1** wybierz atrybut użytkownika, którego chcesz użyć na potrzeby implementacji.
    Na przykład jeśli chcesz użyć identyfikatora EmployeeID jako unikatowego identyfikatora użytkownika, a wartość atrybutu jest przechowywana w elemencie ExtensionAttribute2, wybierz pozycję user.extensionattribute2.

    e. Kliknij przycisk **Zapisz**.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** znajdź kod **XML metadanych federacji** i wybierz pozycję **Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie sap cloud dla klientów** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w witrynie Azure portal o nazwie B.Simon.

1. Z lewego okienka w witrynie Azure portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **pozycję Nowy użytkownik** u góry ekranu.
1. We właściwościach **Użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** username@companydomain.extensionwprowadź pole . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do SAP Cloud dla klienta.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz **sap cloud dla klienta**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-sap-cloud-for-customer-sso"></a>Konfigurowanie usługi SAP Cloud dla klienta — sytego systemu

1. Otwórz nowe okno przeglądarki internetowej i zaloguj się do witryny firmy SAP Cloud dla klientów jako administrator.

2. Po lewej stronie menu kliknij pozycję **Dostawcy** > **tożsamości dostawcy tożsamości** > korporacyjnej**Dodaj** i w wyskakującym okienku dodaj nazwę dostawcy tożsamości, takiego jak **Azure AD,** kliknij przycisk **Zapisz,** a następnie kliknij **konfigurację SAML 2.0**.

    ![Konfiguracja SAP](./media/sap-customer-cloud-tutorial/configure01.png)

3. W sekcji **Konfiguracja SAML 2.0** wykonaj następujące czynności:

    ![Konfiguracja SAP](./media/sap-customer-cloud-tutorial/configure02.png)

    a. Kliknij **przycisk Przeglądaj,** aby przekazać plik XML metadanych federacji, który został pobrany z witryny Azure Portal.

    b. Po pomyślnym przesłaniu pliku XML poniższe wartości zostaną automatycznie automatycznie wypełnione, a następnie kliknij przycisk **Zapisz**.

### <a name="create-sap-cloud-for-customer-test-user"></a>Tworzenie użytkownika testowego klienta platformy SAP Cloud for Customer

Aby umożliwić użytkownikom usługi Azure AD logowanie się do usługi SAP Cloud dla klienta, muszą one być aprowidzone w sap cloud dla klienta. W sap cloud dla klienta inicjowania obsługi administracyjnej jest zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do SAP Cloud dla klienta jako administrator zabezpieczeń.

2. Po lewej stronie menu kliknij na **Użytkownicy & Authorizations** > **User Management** > **Add User**.

    ![Konfiguracja SAP](./media/sap-customer-cloud-tutorial/configure03.png)

3. W sekcji **Dodawanie nowego użytkownika** wykonaj następujące czynności:

    ![Konfiguracja SAP](./media/sap-customer-cloud-tutorial/configure04.png)

    a. W polu **tekstowym Imię** wprowadź nazwę użytkownika, takiego jak **B**.

    b. W polu **tekstowym Nazwisko** wprowadź nazwę użytkownika, takiego jak **Simon**.

    d. W polu **tekstowym Poczta e-mail** `B.Simon@contoso.com`wprowadź adres e-mail użytkownika w stylu .

    d. W polu **tekstowym Nazwa logowania** wprowadź nazwę użytkownika, taką jak **B.Simon**.

    e. Wybierz **typ użytkownika** zgodnie z wymaganiami.

    f. Wybierz opcję **Aktywacja konta** zgodnie z wymaganiami.

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka SAP Cloud for Customer na panelu dostępu powinno nastąpić automatyczne zalogowanie do platformy SAP Cloud for Customer, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj SAP Cloud dla klientów z usługą Azure AD](https://aad.portal.azure.com/)

