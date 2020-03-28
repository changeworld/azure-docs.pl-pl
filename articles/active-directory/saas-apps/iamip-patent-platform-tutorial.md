---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z platformą patentową IamIP | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a platformą patentową IamIP.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8d5b4fc1-e8fd-4418-a369-189272fef80d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7d487aaf7ba4aaf666962cf91ca86d46115055b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78190742"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-iamip-patent-platform"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z platformą patentową IamIP

W tym samouczku dowiesz się, jak zintegrować platformę patentową IamIP z usługą Azure Active Directory (Azure AD). Po zintegrowaniu platformy patentowej IamIP z usługą Azure AD można:

* Użyj usługi Azure AD, aby kontrolować, kto może uzyskać dostęp do platformy patentowej IamIP.
* Włącz użytkownikom automatyczne logowanie do platformy patentowej IamIP za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji: w witrynie Azure Portal.

Aby dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja platformy patentowej IamIP z włączoną funkcją logowania jednokrotnego.An IamIP Patent Platform subscription with single sign-on (SSO) enabled.

## <a name="tutorial-description"></a>Opis samouczka

W tym samouczku skonfigurujesz i przetestujesz samouszeńcę usługi Azure AD w środowisku testowym.

Platforma patentowa IamIP obsługuje jednostkę SSO inicjowaną przez SP i inicjowaną przez PROTOKÓŁ IDP.

Po skonfigurowaniu platformy patentowej IamIP można wymusić kontrolę sesji, która chroni eksfiltrację i infiltrację poufnych danych organizacji w czasie rzeczywistym. Formanty sesji rozciągają się od dostępu warunkowego. [Dowiedz się, jak wymusić kontrolę nad sesją za pomocą programu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


## <a name="add-iamip-patent-platform-from-the-gallery"></a>Dodaj platformę patentową IamIP z galerii

Aby skonfigurować integrację platformy patentowej IamIP z usługą Azure AD, należy dodać platformę patentową IamIP z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) za pomocą konta służbowego lub szkolnego lub osobistego konta Microsoft.
1. W lewym okienku wybierz pozycję **Azure Active Directory**.
1. Przejdź do **aplikacji enterprise,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **IamIP Patent Platform** w polu wyszukiwania.
1. Wybierz **platformę patentową IamIP** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-iamip-patent-platform"></a>Konfigurowanie i testowanie usługi Azure AD SSO dla platformy patentowej IamIP

Skonfigurujesz i przetestujesz usługę Azure AD SSO za pomocą platformy patentowej IamIP przy użyciu użytkownika testowego o nazwie B.Simon. Aby użytkownik łącza sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a odpowiednim użytkownikiem w platformie patentowej IamIP.

Aby skonfigurować i przetestować usługę Azure AD SSO za pomocą platformy patentowej IamIP, wykonaj następujące kroki wysokiego poziomu:

1. **[Skonfiguruj sytuasz usługi Azure AD,](#configure-azure-ad-sso)** aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD,](#create-an-azure-ad-test-user)** aby przetestować logowanie jednookrotne usługi Azure AD.
    * **[Udziel dostępu do użytkownika testowego,](#grant-access-to-the-test-user)** aby umożliwić użytkownikowi korzystanie z logowania jednokrotnego usługi Azure AD.

1. **[Skonfiguruj sygosny SSO platformy patentowej IamIP](#configure-iamip-patent-platform-sso)** po stronie aplikacji.
    * **[Utwórz użytkownika testowego platformy patentowej IamIP](#create-iamip-patent-platform-test-user)** jako odpowiednik reprezentacji użytkownika usługi Azure AD.

1. **[Przetestuj sytą próbę sycącą,](#test-sso)** aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal:

1. W [witrynie Azure portal](https://portal.azure.com/), na stronie integracji aplikacji **platformy patentowej IamIP** w sekcji **Zarządzanie** wybierz opcję **logowania jednokrotnego**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** wybierz przycisk ołówkiem dla **podstawowej konfiguracji SAML,** aby edytować ustawienia:

   ![Przycisk Ołówek do podstawowej konfiguracji SAML](common/edit-urls.png)

1. Jeśli **Basic SAML Configuration** masz plik metadanych dostawcy usług i chcesz skonfigurować jednostkę SSO w trybie inicjowanym przez IDP, wykonaj następujące czynności:

    a. Wybierz **opcję Przekaż plik metadanych:**

    ![Przekazywanie pliku metadanych](common/upload-metadata.png)

    b. Wybierz przycisk folderu, wybierz plik metadanych, a następnie wybierz pozycję **Przekaż:**

    ![Przyciski folderu i przekazywania](common/browse-upload-metadata.png)

    d. Po przekazaniu pliku metadanych wartości **identyfikatora** i **adresu URL odpowiedzi** są automatycznie wypełniane w sekcji **Podstawowa konfiguracja SAML:**

    ![Wartości adresu URL identyfikatora i odpowiedzi](common/idp-intiated.png)

    > [!Note]
    > Jeśli wartości **adresu URL** **identyfikatora** i odpowiedzi nie są wypełniane automatycznie, podaj je ręcznie zgodnie z wymaganiami.

1. Wybierz **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez dodatek SP:

    W polu Wpisz **https:\//patents.iamip.com/login-user**. **Sign-on URL**

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Signing Certificate** **(łącze Pobieranie** **certyfikatu (raw),** aby pobrać certyfikat i zapisać go na komputerze:

    ![Link do pobierania certyfikatu](common/certificateraw.png)

1. W sekcji **Konfigurowanie platformy patentowej IamIP** skopiuj odpowiedni adres URL lub adresy URL na podstawie wymagań:

    ![Kopiowanie adresów URL konfiguracji](common/idp-intiated.png))

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego o nazwie B.Simon w witrynie Azure portal.

1. W lewym okienku witryny Azure Portal wybierz pozycję **Azure Active Directory**. Wybierz **pozycję Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **pozycję Nowy użytkownik** u góry ekranu.
1. We właściwościach **Użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź **b.simon**.  
   1. W polu **Nazwa** użytkownika \<wprowadź> nazwa\<użytkownika>@ companydomain. \<> rozszerzenia. Na przykład `B.Simon@contoso.com`.
   1. Wybierz **pozycję Pokaż hasło**, a następnie zapisz wartość wyświetlaną w polu **Hasło.**
   1. Wybierz **pozycję Utwórz**.

### <a name="grant-access-to-the-test-user"></a>Udzielanie dostępu testemu użytkownikowi

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając temu użytkownikowi dostęp do platformy patentowej IamIP.

1. W portalu Azure wybierz pozycję **Aplikacje przedsiębiorstwa**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz **platformę patentową IamIP**.
1. Na stronie przegląd aplikacji w sekcji **Zarządzanie** wybierz pozycję **Użytkownicy i grupy:**

   ![Wybieranie pozycji Użytkownicy i grupy](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika,** a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału:**

    ![Wybieranie pozycji Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** na liście **Użytkownicy,** a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** wybierz przycisk **Przypisz.**

## <a name="configure-iamip-patent-platform-sso"></a>Konfigurowanie identyfikatora SSO platformy patentowej IamIP

Aby skonfigurować logowanie jednokrotne po stronie platformy patentowej IamIP, należy wysłać pobrany certyfikat nieprzetworzony i odpowiednie adresy URL skopiowane z witryny Azure portal do [zespołu pomocy technicznej platformy patentowej IamIP.](mailto:info@iamip.com) Konfigurują połączenie SSO SAML, aby było poprawne po obu stronach.

### <a name="create-iamip-patent-platform-test-user"></a>Utwórz użytkownika testowego platformy patentowej IamIP

Współpracuj z [zespołem wsparcia platformy patentowej IamIP,](mailto:info@iamip.com) aby dodać użytkownika o nazwie B.Simon w platformie patentowej IamIP. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Test SSO

W tej sekcji przetestujesz konfigurację usługi Azure AD SSO przy użyciu panelu dostępu.

Po wybraniu kafelka platformy patentowej IamIP w Panelu dostępu należy automatycznie zalogować się do instancji platformy patentowej IamIP, dla której skonfigurowano logującą się logującą log. Aby uzyskać więcej informacji o Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Samouczki dotyczące integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj platformę patentową IamIP z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w usłudze Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
