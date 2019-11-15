---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z Adobe Creative Cloud | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i rozwiązaniem Adobe Creative Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c199073f-02ce-45c2-b515-8285d4bbbca2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 815cffab118f6900c1c9d42a7e44821f8af62532
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74081985"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adobe-creative-cloud"></a>Samouczek: integracja logowania jednokrotnego (SSO) Azure Active Directory z usługą Adobe Creative Cloud

W tym samouczku dowiesz się, jak zintegrować Adobe Creative Cloud z usługą Azure Active Directory (Azure AD). Podczas integrowania Adobe Creative Cloud z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Adobe Creative Cloud.
* Zezwól użytkownikom na automatyczne logowanie do Adobe Creative Cloud przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja obsługująca Logowanie jednokrotne (SSO) Adobe Creative Cloud.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Rozwiązanie Adobe Creative Cloud obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług**





## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>Dodawanie rozwiązania Adobe Creative Cloud z galerii

Aby skonfigurować integrację rozwiązania Adobe Creative Cloud z usługą Azure AD, należy dodać rozwiązanie Adobe Creative Cloud z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Adobe Creative Cloud** w polu wyszukiwania.
1. Wybierz **Adobe Creative Cloud** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-adobe-creative-cloud"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla Adobe Creative Cloud

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Adobe Creative Cloud przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Adobe Creative Cloud.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Adobe Creative Cloud, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Adobe Creative Cloud Logowanie jednokrotne](#configure-adobe-creative-cloud-sso)** — aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz Adobe Creative Cloud użytkownika testowego](#create-adobe-creative-cloud-test-user)** , aby dysponować odpowiednikiem B. Simon w Adobe Creative Cloud, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. Na [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Adobe Creative Cloud** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL: `https://adobe.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, korzystając z następującego wzorca: `https://www.okta.com/saml2/service-provider/<token>`

    > [!NOTE]
    > Wartość identyfikatora nie jest prawdziwe. Zaktualizuj tę wartość za pomocą rzeczywistego identyfikatora. W celu uzyskania tej wartości skontaktuj się z [zespołem pomocy technicznej klienta rozwiązania Adobe Creative Cloud](https://www.adobe.com/au/creativecloud/business/teams/plans.html). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja Adobe Creative Cloud oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image](common/edit-attribute.png)

1. Oprócz powyższych, aplikacja Adobe Creative Cloud oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami.

    | Nazwa | Atrybut źródłowy|
    |----- | --------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | Adres e-mail | user.mail |

    > [!NOTE]
    > Aby wartość oświadczenia e-mail była wypełniana w odpowiedzi SAML, użytkownicy muszą mieć ważną licencję usługi Office 365 ExO.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **konfigurowanie Adobe Creative Cloud** skopiuj odpowiednie adresy URL na podstawie wymagania.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **nowego użytkownika** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź username@companydomain.extension. Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do Adobe Creative Cloud.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Adobe Creative Cloud**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

## <a name="configure-adobe-creative-cloud-sso"></a>Skonfiguruj Adobe Creative Cloud Logowanie jednokrotne

1. W innym oknie przeglądarki zaloguj się jako administrator do konsoli administracyjnej [Adobe Admin Console](https://adminconsole.adobe.com).

2. Wybierz pozycję **Ustawienia** na górnym pasku nawigacyjnym, a następnie wybierz pozycję **Tożsamość**. Zostanie otwarta lista domen. Kliknij link **Konfiguruj** dla Twojej domeny. Następnie wykonaj następujące czynności w sekcji **Single Sign On Configuration Required** (Wymagana konfiguracja logowania jednokrotnego). Aby uzyskać więcej informacji, zobacz [Konfigurowanie domeny](https://helpx.adobe.com/enterprise/using/set-up-domain.html)

    ![Ustawienia](https://helpx.adobe.com/content/dam/help/en/enterprise/using/configure-microsoft-azure-with-adobe-sso/_jcr_content/main-pars/procedure_719391630/proc_par/step_3/step_par/image/edit-sso-configuration.png "Ustawienia")

    a. Kliknij przycisk **Browse** (Przeglądaj), aby przekazać certyfikat pobrany z usługi Azure AD w polu **IDP Certificate** (Certyfikat IDP).

    b. W polu tekstowym **wystawcy dostawcy tożsamości** wklej wartość **identyfikatora usługi Azure AD** skopiowaną z Azure Portal.

    d. W polu tekstowym **adres URL logowania dostawcy tożsamości** wklej wartość **adresu URL logowania** skopiowanego z Azure Portal.

    d. Wybierz wartość **HTTP — Redirect** w polu **IDP Binding** (Powiązanie IDP).

    e. Wybierz wartość **Email Address** (Adres e-mail) w polu **User Login Setting** (Ustawienie logowania użytkownika).

    f. Kliknij przycisk **Zapisz** przycisku.

3. Na pulpicie nawigacyjnym będzie teraz widoczny plik XML **„Pobierz metadane”** . Zawiera on adresy URL EntityDescriptor i AssertionConsumerService firmy Adobe. Otwórz ten plik i skonfiguruj te adresy w aplikacji usługi Azure AD.

    ![Konfigurowanie logowania jednokrotnego po stronie aplikacji](./media/adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Użyj wartości EntityDescriptor dostarczonej przez firmę Adobe w polu **Identyfikator** okna dialogowego **Konfigurowanie ustawień aplikacji**.

    b. Użyj wartości AssertionConsumerService dostarczonej przez firmę Adobe w polu **Adres URL odpowiedzi** okna dialogowego **Konfigurowanie ustawień aplikacji**.

### <a name="create-adobe-creative-cloud-test-user"></a>Tworzenie użytkownika testowego rozwiązania Adobe Creative Cloud

Aby umożliwić użytkownikom usługi Azure AD logowanie się do Adobe Creative Cloud, muszą one być obsługiwane w Adobe Creative Cloud. W przypadku rozwiązania Adobe Creative Cloud aprowizowanie jest zadaniem ręcznym.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Aby aprowizować konta użytkowników, wykonaj następujące czynności:

1. Zaloguj się w witrynie konsoli administracyjnej [Adobe Admin Console](https://adminconsole.adobe.com) jako administrator.

2. W konsoli firmy Adobe dodaj użytkownika z identyfikatorem Federated ID i przypisz go do profilu produktu. Aby uzyskać szczegółowe informacje na temat dodawania użytkowników, zobacz [Add users in Adobe Admin Console](https://helpx.adobe.com/enterprise/using/users.html#Addusers) (Dodawanie użytkowników w konsoli administracyjnej firmy Adobe). 

3. W tym momencie wpisz adres e-mail/nazwę UPN w formularzu Adobe Sign-in, naciśnij klawisz Tab, a następnie zastąp z powrotem do usługi Azure AD:
   * Web Access: Logowanie\.www > adobe.com
   * W narzędziu aplikacji klasycznej > logowanie
   * W aplikacji > pomoc > logowanie

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Adobe Creative Cloud w panelu dostępu powinno nastąpić automatyczne zalogowanie do rozwiązania Adobe Creative Cloud, dla którego skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj Adobe Creative Cloud z usługą Azure AD](https://aad.portal.azure.com/)

- [Konfigurowanie domeny (adobe.com)](https://helpx.adobe.com/enterprise/using/set-up-domain.html)
  
- [Konfiguracja oprogramowania Microsoft Azure do użytku z funkcją Adobe SSO (adobe.com)](https://helpx.adobe.com/enterprise/kb/configure-microsoft-azure-with-adobe-sso.html)

