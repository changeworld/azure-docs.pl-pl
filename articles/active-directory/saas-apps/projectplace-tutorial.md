---
title: 'Samouczek: Integracja usługi Azure Active Directory z miejscem w programie Projectplace | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a miejscem projectu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 298059ca-b652-4577-916a-c31393d53d7a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/29/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 179721075484c35c5ebbb3d936b83bc407b75a8d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67093524"
---
# <a name="tutorial-integrate-projectplace-with-azure-active-directory"></a>Samouczek: Integrowanie miejsca pracy projektu z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować miejsce pracy projektu Projectplace z usługą Azure Active Directory (Azure AD). Po zintegrowaniu projectplace z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do projectplace.
* Włącz użytkownikom automatyczne logowanie do miejsca pracy projectplace za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.
* Użytkownicy mogą być aprowizacji w Projectplace automatycznie.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją logowania jednokrotnego (SSO) w projectplace.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym. Projectplace obsługuje **sp i IDP** zainicjowane samoustawione samoso i obsługuje **just in time** użytkownika inicjowania obsługi administracyjnej.

## <a name="adding-projectplace-from-the-gallery"></a>Dodawanie miejsca projektu z galerii

Aby skonfigurować integrację miejsca projectu z usługą Azure AD, należy dodać miejsce pracy projektu z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Miejsce projektu** w polu wyszukiwania.
1. Wybierz **pozycję Projectplace** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Konfigurowanie i testowanie samoustawu samouszeńców usługi Azure AD przy użyciu funkcji Projectplace przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w miejscu pracy programu Projectplace.

Aby skonfigurować i przetestować samoustawę samouszeńców usługi Azure AD za pomocą projectplace, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj sytuasz usługi Azure AD,](#configure-azure-ad-sso)** aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj miejsce pracy projektu,](#configure-projectplace)** aby skonfigurować ustawienia logowania sytego po stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD,](#create-an-azure-ad-test-user)** aby przetestować logowanie jednokrotne usługi Azure AD za pomocą B. Simon.
4. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby włączyć B. Simon do korzystania z usługi Azure AD logowania jednokrotnego.
5. **[Utwórz użytkownika testowego Projectplace](#create-projectplace-test-user)** mieć odpowiednik B. Simon w miejscu projektu, który jest połączony z reprezentacji usługi Azure AD użytkownika.
6. **[Przetestuj sytą próbę sycącą,](#test-sso)** aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie Integracja aplikacji **Projectplace** znajdź sekcję **Zarządzaj** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML,** jeśli chcesz skonfigurować aplikację w trybie inicjowanym **przez usługę IDP,** aplikacja jest wstępnie skonfigurowana, a niezbędne adresy URL są już wstępnie wypełnione platformą Azure. Użytkownik musi zapisać konfigurację, klikając przycisk **Zapisz.**

1. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    W polu **tekstowym "Podpisywanie adresu URL"** wpisz adres URL:`https://service.projectplace.com`

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** kliknij **ikonę** kopiowania, aby skopiować **adres URL metadanych federacji aplikacji**, zgodnie z wymaganiami i zapisać go w Notatniku.

   ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

1. W sekcji **Konfigurowanie miejsca projektu** skopiuj odpowiednie adresy URL na podstawie wymagań.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-projectplace"></a>Konfigurowanie miejsca projektu

Aby skonfigurować logowanie jednokrotne po stronie **Projectplace,** musisz wysłać skopiowany **adres URL metadanych federacji aplikacji** z witryny Azure portal do zespołu pomocy technicznej [Projectplace](https://success.planview.com/Projectplace/Support). Ten zespół zapewnia, że połączenie SSO SAML jest prawidłowo ustawione po obu stronach.

>[!NOTE]
>Konfiguracja logowania jednokrotnego musi być wykonywana przez [zespół pomocy technicznej Projectplace](https://success.planview.com/Projectplace/Support). Otrzymasz powiadomienie zaraz po zakończeniu konfiguracji. 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w witrynie Azure portal o nazwie B. Simon.

1. Z lewego okienka w witrynie Azure portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **pozycję Nowy użytkownik** u góry ekranu.
1. We właściwościach **Użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B. Simon`.  
   1. W polu **Nazwa użytkownika** username@companydomain.extensionwprowadź pole . Na przykład `BrittaSimon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz B. Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do Projectplace.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Projectplace**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz **pozycję B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

### <a name="create-projectplace-test-user"></a>Utwórz użytkownika testowego miejsca projektu

>[!NOTE]
>Ten krok można pominąć, jeśli w miejscu projectplace włączono inicjowanie obsługi administracyjnej. Możesz poprosić [zespół pomocy technicznej Projectplace,](https://success.planview.com/Projectplace/Support) aby włączyć provisoning, po zakończeniu użytkownicy zostaną utworzone w Projectplace podczas pierwszego logowania.

Aby umożliwić użytkownikom usługi Azure AD zalogowanie się do miejsca pracy projectplace, należy dodać je do projectplace. Należy dodać je ręcznie.

**Aby utworzyć konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy **Projectplace** jako administrator.

2. Przejdź do **osoby**, a następnie wybierz **członków:**
   
    ![Przejdź do osoby, a następnie wybierz członków](./media/projectplace-tutorial/ic790228.png "People")

3. Wybierz **dodaj element członkowski:**
   
    ![Wybierz pozycję Dodaj element członkowski](./media/projectplace-tutorial/ic790232.png "Dodawanie członków")

4. W sekcji **Dodaj członka,** należy wykonać następujące kroki.
   
    ![Dodawanie sekcji członkowskiej](./media/projectplace-tutorial/ic790233.png "Nowi członkowie")
   
    1. W polu **Nowi członkowie** wprowadź adres e-mail prawidłowego konta usługi Azure AD, które chcesz dodać.
   
    1. Wybierz pozycję **Wyślij**.

   Wiadomość e-mail zawierająca łącze potwierdzające konto przed jego uaktywne jest wysyłana do posiadacza konta usługi Azure AD.

>[!NOTE]
>Można również użyć dowolnego innego narzędzia do tworzenia konta użytkownika lub interfejsu API dostarczonego przez projectplace, aby dodać konta użytkowników usługi Azure AD.


### <a name="test-sso"></a>Test SSO

Po wybraniu kafelka Projectplace w Panelu dostępu należy automatycznie zalogować się do miejsca projektu, dla którego skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)