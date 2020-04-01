---
title: 'Samouczek: Integracja usługi Azure Active Directory z abstrakcyjnym | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a abstrakcyjną.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2eb167ab-d769-4661-a8cb-ae371cb63d2a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81886fa9165269d89bde8306c5829be41952c190
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "68302586"
---
# <a name="tutorial-integrate-abstract-with-azure-active-directory"></a>Samouczek: Integruj abstrakcję z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować abstrakcyjne z usługi Azure Active Directory (Azure AD). Po zintegrowaniu abstract z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do abstrakcyjne.
* Włącz użytkownikom automatyczne logowanie do abstrakcyjnego za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją rejestracji jednokrotnej.Abstract single sign-on (SSO) enabled subscription.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Abstract obsługuje jednostkę SSO inicjowane przez PROTOKÓŁ SP **i IDP**

## <a name="adding-abstract-from-the-gallery"></a>Dodawanie streszczenia z galerii

Aby skonfigurować integrację abstrakcyjne do usługi Azure AD, należy dodać abstrakcyjne z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Abstract** w polu wyszukiwania.
1. Wybierz **pozycję Abstrakcyjny** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Konfigurowanie i testowanie usługi Azure AD SSO z użyciem abstrakcyjnego przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w abstract.

Aby skonfigurować i przetestować przychylić się do usługi Azure AD z pomocą abstrakcyjną, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj abstrakcyjne logowanie jednokrotne](#configure-abstract-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz abstrakcyjnego użytkownika testowego](#create-abstract-test-user)** — aby mieć odpowiednik Britta Simon w abstrakcji, który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie Integracja aplikacji **abstrakcyjnej** znajdź sekcję **Zarządzaj** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** aplikacja jest wstępnie skonfigurowana w trybie inicjowanym **przez usługę IDP,** a niezbędne adresy URL są już wstępnie wypełnione platformą Azure. Użytkownik musi zapisać konfigurację, klikając przycisk **Zapisz.**

1. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    W polu **tekstowym "Podpisywanie adresu URL"** wpisz adres URL:`https://app.abstract.com/signin`

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij przycisk kopiowania, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

### <a name="configure-abstract-sso"></a>Konfigurowanie abstrakcyjnego przysłowiu — jako

Upewnij się, aby `App Federation Metadata Url` pobrać `Azure AD Identifier` i z witryny Azure portal, jak trzeba będzie te do skonfigurowania sytuować na abstrakcyjne.

Informacje te znajdują się na stronie **Konfigurowanie logowania jednokrotnego z SAML:**

* Znajduje `App Federation Metadata Url` się w sekcji **Certyfikat podpisywania SAML.**
* Znajduje `Azure AD Identifier` się w sekcji **Konfigurowanie abstrakcyjne.**


Teraz można skonfigurować sytą łatę w programie Abstract:

>[!Note]
>Aby uzyskać dostęp do ustawień logowania dodatkowego w programie Abstract, należy uwierzytelnić się przy użyciu konta administratora organizacji.

1. Otwórz [abstrakcyjną aplikację internetową](https://app.abstract.com/).
2. Przejdź do strony **Uprawnienia** na pasku po lewej stronie.
3. W sekcji **Konfigurowanie jednostki SSO** wprowadź **adres URL metadanych** i **identyfikator jednostki**.
4. Wprowadź ręczne wyjątki, które mogą mieć. Wiadomości e-mail wymienione w sekcji ręczne wyjątki omijają logowanie logowania i będą mogły logować się za pomocą poczty e-mail i hasła. 
5. Kliknij **pozycję Zapisz zmiany**.

>[!Note] 
>Musisz użyć podstawowych adresów e-mail na liście ręcznych wyjątków. Aktywacja SSO zakończy się niepowodzeniem, jeśli lista wiadomości e-mail jest pomocniczą wiadomością e-mail użytkownika. Jeśli tak się stanie, zostanie wyświetlony komunikat o błędzie z podstawową wiadomością e-mail dla konta, które nie po awarii. Dodaj tę podstawową wiadomość e-mail do listy ręcznych wyjątków po zweryfikowaniu, że znasz użytkownika.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do abstrakcyjne.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Streszczenie**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

### <a name="create-abstract-test-user"></a>Utwórz abstrakcyjnego użytkownika testowego

Aby przetestować sytą próbę sytua na abstrakcyjnym:

1. Otwórz [abstrakcyjną aplikację internetową](https://app.abstract.com/).
2. Przejdź do strony **Uprawnienia** na pasku po lewej stronie.
3. Kliknij **przycisk Testuj z moim kontem**. Jeśli test nie powiedzie się, [skontaktuj się z naszym zespołem pomocy technicznej.](https://www.abstract.com/help/contact/)

>[!Note]
>Aby uzyskać dostęp do ustawień logowania dodatkowego w programie Abstract, należy uwierzytelnić się przy użyciu konta administratora organizacji.
To konto administratora organizacji będzie musiało zostać przypisane do abstrakcyjnego w witrynie Azure portal.

### <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Abstrakcyjne w Panelu dostępu należy automatycznie zalogować się do streszczenia, dla którego skonfigurowano logującą się jednoślik. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

