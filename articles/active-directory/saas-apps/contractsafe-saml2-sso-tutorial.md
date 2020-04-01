---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z logałem jednokrotnym contractSafe Saml2 | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a logajną SSO Saml2 firmy ContractSafe.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9d8c9eba-6a90-4c8f-b387-a6ead4af00af
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 12/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ab2dc71f39164988e0d229fc994548a00447986
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77185630"
---
# <a name="tutorial-integrate-azure-active-directory-single-sign-on-sso-with-contractsafe-saml2-sso"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z logiem samouszczeniem jednokrotnym usługi ContractSafe Saml2

W tym samouczku dowiesz się, jak zintegrować contractSafe Saml2 SSO z usługą Azure Active Directory (Azure AD). Po zintegrowaniu contractSafe Saml2 SSO z usługą Azure AD można:

* Kontroluj, kto ma dostęp do umowySafe Saml2 SSO w usłudze Azure AD.
* Włącz użytkownikom automatyczne logowanie się do contractSafe Saml2 Logso za pomocą swoich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji: w witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji z usługą Azure AD z aplikacjami typu oprogramowanie jako usługa SaaS, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, potrzebne będą następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja SSO Saml2 z włączonym identyfikatorem SSO.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym. ContractSafe Saml2 SSO obsługuje **IDP**-initiated SSO.

## <a name="add-contractsafe-saml2-sso-from-the-gallery"></a>Dodaj contractSafe Saml2 SSO z galerii

Aby skonfigurować integrację contractSafe Saml2 SSO do usługi Azure AD, należy dodać contractSafe Saml2 SSO z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **ContractSafe Saml2 SSO** w polu wyszukiwania.
1. Wybierz **contractSafe Saml2 SSO** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-contractsafe-saml2-sso"></a>Konfigurowanie i testowanie usługi Azure AD SSO dla usługi ContractSafe Saml2 SSO

Konfigurowanie i testowanie usługi Azure AD SSO przy użyciu funkcji SSO ContractSafe Saml2 przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik syt/r działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w aplikacji SSO Saml2 ContractSafe.

Aby skonfigurować i przetestować przychylić do usługi Azure AD sytuować za pomocą usługi ContractSafe Saml2 SSO, wykonaj następujące bloki konstrukcyjne:

1. [Skonfiguruj sytuasz usługi Azure AD,](#configure-azure-ad-sso) aby umożliwić użytkownikom korzystanie z tej funkcji.
   * [Utwórz użytkownika testowego usługi Azure AD,](#create-an-azure-ad-test-user) aby przetestować sywoszyka usługi Azure AD przy użyciu konta **B.Simon.**
   * [Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user) aby włączyć **B.Simon** do korzystania z usługi Azure AD SSO.

1. [Skonfiguruj identyfikatora SYT ContractSafe Saml2,](#configure-contractsafe-saml2-sso) aby skonfigurować ustawienia logowania sytego po stronie aplikacji.
   * [Utwórz contractSafe Saml2 SSO użytkownika testowego,](#create-a-contractsafe-saml2-sso-test-user) aby mieć odpowiednik **B.Simon** w ContractSafe Saml2 SSO, który jest połączony z reprezentacji usługi Azure AD użytkownika.
2. [Przetestuj sytą próbę sycącą,](#test-sso) aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal:

1. W [witrynie Azure portal](https://portal.azure.com/)— na stronie integracji aplikacji **logowania jednokrotnego ContractSafe Saml2** znajdź sekcję **Zarządzaj** i wybierz **opcję logowania jednokrotnego**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** wybierz ikonę edycji (pióra) dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** wprowadź następujące wartości w odpowiednich polach:

    a. W polu tekstowym **Identyfikator** wprowadź adres URL, używając następującego formatu:`https://app.contractsafe.com/saml2_auth/<UNIQUEID>/acs/`

    b. W polu tekstowym **Odpowiedz na adres URL** wprowadź adres URL, używając następującego formatu:`https://app.contractsafe.com/saml2_auth/<UNIQUEID>/acs/`

    > [!NOTE]
    > To nie są rzeczywiste wartości. Należy je zastąpić rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Skontaktuj się z [zespołem pomocy technicznej klienta SSO ContractSafe Saml2,](mailto:support@contractsafe.com) aby uzyskać te wartości. Można również odwołać się do formatów wyświetlanych w sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal.

1. ContractSafe Saml2 SSO oczekuje potwierdzeń SAML w określonym formacie, który wymaga dodawania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![Typowe atrybuty domyślne](common/default-attributes.png)

1. Oprócz atrybutów domyślnych, ContractSafe Saml2 aplikacji SSO oczekuje kilka więcej atrybutów, które mają być przekazywane z powrotem w odpowiedzi SAML. Te atrybuty są wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami. Na poniższej liście przedstawiono dodatkowe atrybuty.

    | Nazwa | Atrybut źródłowy|
    | ---------------| --------------- |
    | nazwa e-mail | user.userprincipalname |
    | email | user.onpremisesuserprincipalname |

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** znajdź identyfikator **XML metadanych federacji**. Wybierz **pozycję Pobierz,** aby pobrać certyfikat, a następnie zapisz go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie umowy SSO Saml2** skopiuj odpowiednie adresy URL na podstawie wymagań użytkownika.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

## <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w witrynie Azure portal o nazwie **B.Simon**.

1. Z lewego okienka w witrynie Azure Portal wybierz pozycję **Azure Active Directory**. Wybierz **pozycję Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **pozycję Nowy użytkownik** u góry ekranu.
1. We właściwościach **Użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź adres `username@companydomain.extension` e-mail w formacie. Może to być na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Wybierz **pozycję Utwórz**.

## <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz **B.Simon** do korzystania z usługi Azure SSO, udzielając dostępu do ContractSafe Saml2 SSO.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **ContractSafe Saml2 SSO**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzanie,** a następnie wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

   ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy **Użytkownicy.** Następnie wybierz przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy. Następnie wybierz przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** wybierz przycisk **Przypisz.**

## <a name="configure-contractsafe-saml2-sso"></a>Konfigurowanie umowySafe Saml2 SSO

Aby skonfigurować opcję SSO w witrynie **ContractSafe Saml2 SSO,** należy wysłać pobrany **kod XML metadanych federacji** i odpowiednie skopiowane adresy URL z portalu Azure do [zespołu pomocy technicznej contractSafe Saml2 SSO](mailto:support@contractsafe.com). Zespół jest odpowiedzialny za prawidłowe ustawienie połączenia SSO SAML po obu stronach.

## <a name="create-a-contractsafe-saml2-sso-test-user"></a>Tworzenie użytkownika testowego SSO SSO ContractSafe Saml2

Utwórz użytkownika o nazwie B.Simon w ContractSafe Saml2 SSO. Współpracuj z [zespołem pomocy technicznej ContractSafe Saml2 SSO,](mailto:support@contractsafe.com) aby dodać użytkowników na platformie ContractSafe Saml2 SSO. Użytkownicy muszą zostać utworzone i aktywowane przed użyciem sytego użytkownika.

## <a name="test-sso"></a>Test SSO

Przetestuj konfigurację usługi Azure AD SSO przy użyciu panelu dostępu. Po wybraniu kafelka SSO SSO ContractSafe Saml2 w Panelu dostępu należy automatycznie zalogować się do umowySafe Saml2 SSO, dla którego skonfigurowano logującą loga. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj umowysafe Saml2 SSO z usługą Azure AD](https://aad.portal.azure.com/)
