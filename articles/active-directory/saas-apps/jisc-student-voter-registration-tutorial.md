---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z rejestracją wyborców jisc student | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a rejestracją wyborców uczniów jisc.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a95d016d-ec53-4b3c-9a51-e0cac0791e49
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/26/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7de54b507630174107994f1434f17120f3abffbb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74807472"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-jisc-student-voter-registration"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z rejestracją wyborców studentów Jisc

W tym samouczku dowiesz się, jak zintegrować rejestrację wyborców jisc student z usługą Azure Active Directory (Azure AD). Po zintegrowaniu jisc student rejestracji wyborców z usługi Azure AD, można:

* Kontrola w usłudze Azure AD, który ma dostęp do jisc student rejestracji wyborców.
* Włącz użytkownikom automatyczne logowanie się do rejestracji wyborców jisc student za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Jisc Student Voter Registration single sign-on (SSO) włączono subskrypcję.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Jisc Student Rejestracja wyborców obsługuje **SP** zainicjowane SSO
* Jisc Student Rejestracja wyborców obsługuje **just in time** użytkownika inicjowania obsługi administracyjnej

## <a name="adding-jisc-student-voter-registration-from-the-gallery"></a>Dodawanie Jisc Student Rejestracja wyborców z galerii

Aby skonfigurować integrację jisc student rejestracji wyborców do usługi Azure AD, należy dodać Jisc Student Rejestracja wyborców z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Jisc Student Voter Registration** w polu wyszukiwania.
1. Wybierz **Jisc Student Rejestracja wyborców** z panelu wyników, a następnie dodać aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-jisc-student-voter-registration"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla rejestracji wyborców uczniów jisc

Konfigurowanie i testowanie usługi Azure AD SSO z jisc student rejestracji wyborców przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby identyfikator YG działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w rejestracji wyborców uczniów jisc.

Aby skonfigurować i przetestować usługę Azure AD SSO z rejestracją wyborców jisc student, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj logowanie jednokrotne rejestracji wyborców jisc](#configure-jisc-student-voter-registration-sso)** - aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    * **[Utwórz użytkownika testu jisc student rejestracji wyborców](#create-jisc-student-voter-registration-test-user)** - mieć odpowiednik B.Simon w Jisc Student Rejestracja wyborców, który jest połączony z reprezentacji usługi Azure AD użytkownika.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Jisc Student Voter Registration** znajdź sekcję **Zarządzaj** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następującego pola:

    W polu **tekstowym "Podpisywanie adresu URL"** wpisz adres URL:`https://www.studentvoterregistration.ac.uk/consent`

1. Jisc Student Rejestracja aplikacji oczekuje potwierdzenia SAML w określonym formacie, który wymaga, aby dodać niestandardowe mapowania atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image](common/default-attributes.png)

1. Oprócz powyższego, Jisc Student Rejestracja aplikacji oczekuje kilka więcej atrybutów, które mają być przekazywane z powrotem w odpowiedzi SAML, które są pokazane poniżej. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami.

    | Nazwa |  Atrybut źródłowy|
    | ---------|  --------- |
    | Postalcode | user.postalcode |
    | Unikatowy identyfikator użytkownika | user.objectid |

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** znajdź kod **XML metadanych federacji** i wybierz pozycję **Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie rejestracji wyborców studentów Jisc** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do Jisc Student Rejestracja wyborców.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz **Jisc Student Voter Registration**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-jisc-student-voter-registration-sso"></a>Konfigurowanie jisc student rejestracji wyborców SSO

Aby skonfigurować logowanie jednokrotne po stronie **jisc student voter registration,** musisz wysłać pobrany **kod XML metadanych federacji** i odpowiednie skopiowane adresy URL z witryny Azure portal do [zespołu pomocy technicznej Jisc Student Voter Registration](mailto:studentvote@jisc.ac.uk). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-jisc-student-voter-registration-test-user"></a>Utwórz użytkownika testu rejestracji wyborców jisc

W tej sekcji użytkownik o nazwie B.Simon jest tworzony w Jisc Student Rejestracji wyborców. Jisc Student Rejestracja wyborców obsługuje just-in-time użytkownika inicjowania obsługi administracyjnej, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w Jisc Student Rejestracja wyborców, nowy jest tworzony po uwierzytelnieniu.

## <a name="test-sso"></a>Test SSO

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Jisc Student Voter Registration w panelu dostępu należy automatycznie zalogować się do jisc student rejestracji wyborców, dla których skonfigurowano SSO. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj rejestrację wyborców jisc student z usługą Azure AD](https://aad.portal.azure.com/)