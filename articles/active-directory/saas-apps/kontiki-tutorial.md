---
title: 'Samouczek: Integracja usługi Azure Active Directory z kontiki | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a kontiki.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8d5e5413-da4c-40d8-b1d0-f03ecfef030b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bfb154bce34b2ceda99b82c7ca3534b8a8ee0a1f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67098488"
---
# <a name="tutorial-azure-active-directory-integration-with-kontiki"></a>Samouczek: Integracja usługi Azure Active Directory z kontiki

W tym samouczku dowiesz się, jak zintegrować kontiki z usługą Azure Active Directory (Azure AD).

Integracja kontiki z usługą Azure AD zapewnia następujące korzyści:

* Za pomocą usługi Azure AD można kontrolować, kto ma dostęp do kontiki.
* Użytkownicy mogą być automatycznie zalogowany do Kontiki za pomocą swoich kont usługi Azure AD (logowanie jednokrotne).
* Możesz zarządzać kontami z jednej centralnej lokalizacji — witryny Azure Portal.

Aby uzyskać więcej informacji na temat integracji aplikacji z usługą Azure AD na temat oprogramowania jako usługi, zobacz [Logowanie jednokrotne w aplikacjach usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z kontiki, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji usługi Azure AD, utwórz [bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.
* Subskrypcja Kontiki z włączoną rejestracją jednoosobową.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować azure ad logowania jednokrotnego w środowisku testowym i zintegrować Kontiki z usługą Azure AD.

Kontiki obsługuje następujące funkcje:

* **Logowanie jednokrotne inicjowane przez sp.**
* **Aprowizowanie użytkowników typu just in time**

## <a name="add-kontiki-in-the-azure-portal"></a>Dodawanie kontiki w witrynie Azure portal

Aby zintegrować Kontiki z usługą Azure AD, należy dodać kontiki do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).

1. W menu po lewej stronie wybierz pozycję **Azure Active Directory**.

    ![Opcja usługi Azure Active Directory](common/select-azuread.png)

1. Wybierz **aplikacje** > enterprise**Wszystkie aplikacje**.

    ![Okienko Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

1. Aby dodać aplikację, wybierz pozycję **Nowa aplikacja**.

    ![Opcja Nowa aplikacja](common/add-new-app.png)

1. W polu wyszukiwania wpisz **Kontiki**. W wynikach wyszukiwania wybierz pozycję **Kontiki**, a następnie wybierz pozycję **Dodaj**.

    ![Kontiki na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego z Kontiki na podstawie użytkownika testowego o nazwie **Britta Simon**. Aby logowanie jednokrotne działało, należy ustanowić połączone relacje między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w kontiki.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą kontiki, należy wykonać następujące bloki konstrukcyjne:

| Zadanie | Opis |
| --- | --- |
| **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** | Umożliwia użytkownikom korzystanie z tej funkcji. |
| **[Konfigurowanie logowania jednokrotnego kontiki](#configure-kontiki-single-sign-on)** | Konfiguruje ustawienia logowania jednokrotnego w aplikacji. |
| **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** | Testy logowania jednokrotnego usługi Azure AD dla użytkownika o nazwie Britta Simon. |
| **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** | Umożliwia Firmie Britta Simon używanie logowania jednokrotnego usługi Azure AD. |
| **[Tworzenie użytkownika testowego Kontiki](#create-a-kontiki-test-user)** | Tworzy odpowiednik Britta Simon w Kontiki, który jest połączony z reprezentacji usługi Azure AD użytkownika. |
| **[Testowanie logowania jednokrotnego](#test-single-sign-on)** | Sprawdza, czy konfiguracja działa. |

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować azure ad logowania jednokrotnego z Kontiki w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)w okienku integracji aplikacji **Kontiki** wybierz pozycję **Logowanie jednokrotne**.

    ![Konfigurowanie opcji logowania jednokrotnego](common/select-sso.png)

1. W okienku **Wybierz metodę logowania jednokrotnego** wybierz tryb **SAML** lub **SAML/WS-Fed,** aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

1. W okienku **Konfigurowanie logowania jednokrotnego za pomocą saml** wybierz pozycję **Edytuj** (ikona ołówka), aby otworzyć podstawowe okienko **konfiguracji SAML.**

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W podstawowym okienku **konfiguracji SAML** w polu tekstowym **Podpisz adres URL** wprowadź adres URL zawierający następujący wzorzec:`https://<companyname>.mc.eval.kontiki.com`

    ![Informacje o logach jednokrotnych domeny kontiki i adresów URL](common/sp-signonurl.png)

    > [!NOTE]
    > Skontaktuj się z [zespołem pomocy technicznej klienta Kontiki,](https://customersupport.kontiki.com/enterprise/contactsupport.html) aby uzyskać poprawną wartość do użycia. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. W okienku **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** wybierz pozycję **Pobierz** obok pozycji **XML metadanych federacji**. Wybierz opcję pobierania na podstawie twoich wymagań. Zapisz certyfikat na komputerze.

    ![Opcja pobierania certyfikatu XML metadanych federacji](common/metadataxml.png)

1. W sekcji **Konfigurowanie kontiki** skopiuj następujące adresy URL na podstawie twoich wymagań:

    * Adres URL logowania
    * Identyfikator usługi Azure AD
    * Adres URL wylogowywania

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-kontiki-single-sign-on"></a>Konfigurowanie logowania jednokrotnego kontiki

Aby skonfigurować logowanie jednokrotne po stronie Kontiki, wyślij pobrany plik XML metadanych federacji i odpowiednie adresy URL skopiowane z witryny Azure portal do [zespołu pomocy technicznej Kontiki](https://customersupport.kontiki.com/enterprise/contactsupport.html). Zespół pomocy technicznej Kontiki używa wysyłanych informacji, aby upewnić się, że połączenie logowania jednokrotnego SAML jest poprawnie ustawione po obu stronach.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji utworzysz użytkownika testowego o nazwie Britta Simon w witrynie Azure Portal.

1. W witrynie Azure portal wybierz pozycję**Użytkownicy** >  **usługi Azure Active Directory** > **Wszyscy użytkownicy**.

    ![Opcje Użytkownicy i Wszyscy użytkownicy](common/users.png)

1. Wybierz przycisk **Nowy użytkownik**.

    ![Opcja Nowy użytkownik](common/new-user.png)

1. W okienku **Użytkownik** wykonaj następujące czynności:

    1. W polu **Nazwa** wpisz **BrittaSimon**.
  
    1. W polu **Nazwa użytkownika** wprowadź **>\@\<brittasimon twoja firma-domena.\<>rozszerzenia **. Na przykład **contoso.com brittasimon\@**.

    1. Zaznacz pole wyboru **Pokaż hasło.** Zapisz wartość wyświetlaną w polu **Hasło.**

    1. Wybierz **pozycję Utwórz**.

    ![Okienko Użytkownik](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji udzielisz Firmie Britta Simon dostępu do kontiki, aby mogła używać logowania jednokrotnego platformy Azure.

1. W portalu Azure wybierz **pozycję Aplikacje** > dla**przedsiębiorstw Wszystkie aplikacje** > **Kontiki**.

    ![Okienko Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

1. Na liście aplikacji wybierz **kontiki**.

    ![Kontiki na liście aplikacji](common/all-applications.png)

1. W menu wybierz pozycję **Użytkownicy i grupy**.

    ![Opcja Użytkownicy i grupy](common/users-groups-blade.png)

1. Wybierz przycisk **Dodaj użytkownika**. Następnie w okienku **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

1. W okienku **Użytkownicy i grupy** wybierz pozycję **Britta Simon** na liście użytkowników. Wybierz pozycję **Wybierz**.

1. Jeśli oczekujesz wartości roli w asercji SAML, w okienku **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy. Wybierz pozycję **Wybierz**.

1. W okienku **Dodawanie przydziału** wybierz pozycję **Przypisz**.

### <a name="create-a-kontiki-test-user"></a>Tworzenie użytkownika testowego Kontiki

Nie ma żadnego elementu akcji do konfigurowania inicjowania obsługi administracyjnej użytkownika w kontiki. Gdy przypisany użytkownik próbuje zalogować się do kontiki za pomocą portalu Moje aplikacje, kontiki sprawdza, czy użytkownik istnieje. Jeśli nie zostanie znalezione żadne konto użytkownika, Kontiki automatycznie utworzy konto użytkownika.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfigurację logowania jednokrotnego usługi Azure AD przy użyciu portalu Moje aplikacje.

Po skonfigurowaniu logowania jednokrotnego po wybraniu **opcji Kontiki** w portalu Moje aplikacje użytkownik jest automatycznie zalogowany do kontiki. Aby uzyskać więcej informacji o portalu Moje aplikacje, zobacz [Uzyskiwanie dostępu do aplikacji i korzystanie z nich w portalu Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej, zapoznaj się z tymi artykułami:

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
