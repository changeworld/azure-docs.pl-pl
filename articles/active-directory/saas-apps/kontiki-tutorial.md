---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Kontiki | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Kontiki.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67098488"
---
# <a name="tutorial-azure-active-directory-integration-with-kontiki"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Kontiki

W tym samouczku dowiesz się, jak zintegrować Kontiki w usłudze Azure Active Directory (Azure AD).

Integrowanie Kontiki z usługą Azure AD zapewnia następujące korzyści:

* Możesz użyć usługi Azure AD w celu kontrolowania, kto ma dostęp do Kontiki.
* Użytkownicy mogą być automatycznie zalogowany do Kontiki za pomocą kont usługi Azure AD (logowanie jednokrotne).
* Możesz zarządzać kontami z jednej centralnej lokalizacji — witryny Azure Portal.

Aby uzyskać więcej informacji na temat oprogramowania jako usługi (SaaS) integracji aplikacji z usługą Azure AD, zobacz [logowanie jednokrotne do aplikacji w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Kontiki, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcję usługi Azure AD, Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) przed przystąpieniem do wykonywania.
* Subskrypcja Kontiki przy użyciu logowania jednokrotnego włączone.

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka konfigurowania i testowania usługi Azure AD logowanie jednokrotne w środowisku testowym, a integracja Kontiki z usługą Azure AD.

Kontiki obsługuje następujące funkcje:

* **Zainicjowane przez Dostawcę logowania jednokrotnego**
* **Aprowizowanie użytkowników typu just in time**

## <a name="add-kontiki-in-the-azure-portal"></a>Dodaj Kontiki w witrynie Azure portal

Aby zintegrować Kontiki z usługą Azure AD, należy dodać Kontiki z listą zarządzanych aplikacji SaaS.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W menu po lewej stronie wybierz **usługi Azure Active Directory**.

    ![Opcja usługi Azure Active Directory](common/select-azuread.png)

1. Wybierz pozycję **Aplikacje dla przedsiębiorstw** > **Wszystkie aplikacje**.

    ![Okienko Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

1. Aby dodać aplikację, wybierz pozycję **nową aplikację**.

    ![Nowa opcja aplikacji](common/add-new-app.png)

1. W polu wyszukiwania wprowadź **Kontiki**. W wynikach wyszukiwania wybierz **Kontiki**, a następnie wybierz pozycję **Dodaj**.

    ![Kontiki na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji, konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą Kontiki w oparciu o nazwie użytkownika testowego **Britta Simon**. Dla logowania jednokrotnego do pracy należy ustanowić połączone relację między użytkownikiem usługi Azure AD i powiązanych użytkowników w Kontiki.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Kontiki, należy wykonać poniższe bloki konstrukcyjne:

| Zadanie | Opis |
| --- | --- |
| **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)** | Umożliwia użytkownikom korzystać z tej funkcji. |
| **[Konfigurowanie Kontiki logowania jednokrotnego](#configure-kontiki-single-sign-on)** | Konfiguruje pojedynczy ustawień logowania jednokrotnego w aplikacji. |
| **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** | Testy usługi Azure AD logowanie jednokrotne dla użytkownika o nazwie Britta Simon. |
| **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** | Umożliwia Britta Simon korzystać z usługi Azure AD logowania jednokrotnego. |
| **[Tworzenie użytkownika testowego Kontiki](#create-a-kontiki-test-user)** | Tworzy odpowiednikiem Britta Simon Kontiki połączonego z usługi Azure AD reprezentacja użytkownika. |
| **[Testowanie logowania jednokrotnego](#test-single-sign-on)** | Sprawdza, czy konfiguracja działa. |

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji skonfigurujesz usługi Azure AD logowanie jednokrotne za pomocą Kontiki w witrynie Azure portal.

1. W [witryny Azure portal](https://portal.azure.com/)w **Kontiki** okienko integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Konfigurowanie opcji rejestracji jednokrotnej](common/select-sso.png)

1. W **wybierz jedną metodę logowania jednokrotnego** okienku wybierz **SAML** lub **SAML/WS-Fed** trybu, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

1. W **Ustaw się logowania jednokrotnego przy użyciu protokołu SAML** okienku wybierz **Edytuj** (ikonę ołówka) aby otworzyć **podstawową konfigurację protokołu SAML** okienka.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W **podstawową konfigurację protokołu SAML** okienku w obszarze **adres URL logowania** tekstu wprowadź adres URL z następującym wzorcem: `https://<companyname>.mc.eval.kontiki.com`

    ![Kontiki domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/sp-signonurl.png)

    > [!NOTE]
    > Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Kontiki](https://customersupport.kontiki.com/enterprise/contactsupport.html) można pobrać poprawną wartość do użycia. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. W **Ustaw się logowanie jednokrotne z SAML** okienko w **certyfikat podpisywania SAML** zaznacz **Pobierz** obok **XML metadanych Federacji**. Wybierz opcję pobierania, w zależności od wymagań. Zapisz certyfikat na komputerze.

    ![Opcja pobierania certyfikatu XML metadanych Federacji](common/metadataxml.png)

1. W **Konfigurowanie Kontiki** sekcji, skopiuj następujące adresy URL, zgodnie z wymaganiami:

    * Adres URL logowania
    * Identyfikator usługi Azure AD
    * Adres URL wylogowywania

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-kontiki-single-sign-on"></a>Konfigurowanie Kontiki logowania jednokrotnego

Aby skonfigurować logowanie jednokrotne na stronie Kontiki, Wyślij pobrany plik XML metadanych Federacji i odpowiednie adresy URL, które zostały skopiowane z portalu Azure w celu [zespołem pomocy technicznej Kontiki](https://customersupport.kontiki.com/enterprise/contactsupport.html). Zespół pomocy technicznej Kontiki używa tych informacji, wysyłanych przez nich, aby upewnić się, że SAML logowania jednokrotnego połączenie jest prawidłowo po obu stronach.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji utworzysz użytkownika testowego o nazwie Britta Simon w witrynie Azure Portal.

1. W witrynie Azure Portal wybierz kolejno pozycje **Azure Active Directory** > **Użytkownicy** > **Wszyscy użytkownicy**.

    ![Użytkownicy i wszystkie opcje użytkowników](common/users.png)

1. Wybierz przycisk **Nowy użytkownik**.

    ![Nowa opcja użytkownika](common/new-user.png)

1. W okienku **Użytkownik** wykonaj następujące czynności:

    1. W polu **Nazwa** wpisz **BrittaSimon**.
  
    1. W **nazwa_użytkownika** wprowadź **brittasimon\@\<Twojej domeny firmy >.\< Rozszerzenia >** . Na przykład **brittasimon\@contoso.com**.

    1. Wybierz **hasło Show** pole wyboru. Zanotuj wartość, która jest wyświetlana w **hasło** pole.

    1. Wybierz pozycję **Utwórz**.

    ![W okienku użytkownika](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz udzielić dostępu Britta Simon Kontiki, dzięki czemu korzystaniem Azure logowania jednokrotnego.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje** > **Kontiki**.

    ![Okienko Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

1. Na liście aplikacji wybierz **Kontiki**.

    ![Kontiki na liście aplikacji](common/all-applications.png)

1. W menu wybierz pozycję **Użytkownicy i grupy**.

    ![Opcja użytkowników i grup](common/users-groups-blade.png)

1. Wybierz przycisk **Dodaj użytkownika**. Następnie w okienku **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

1. W **użytkowników i grup** okienku wybierz **Britta Simon** na liście Użytkownicy. Wybierz pozycję **Wybierz**.

1. Jeśli są oczekiwane wartości roli dla asercji SAML w **wybierz rolę** okienku zaznacz odpowiednie rolę dla użytkownika z listy. Wybierz pozycję **Wybierz**.

1. W **Dodaj przydziału** okienku wybierz **przypisać**.

### <a name="create-a-kontiki-test-user"></a>Tworzenie użytkownika testowego Kontiki

Brak elementu działania umożliwiające skonfigurowanie aprowizacji w Kontiki użytkowników. Gdy przypisany użytkownik próbuje zalogować się do Kontiki za pomocą portalu Moje aplikacje, Kontiki sprawdza, czy użytkownik istnieje. Nie znaleziono żadnego konta użytkownika, Kontiki automatycznie utworzy konto użytkownika.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji możesz przetestować konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu portalu Moje aplikacje.

Po skonfigurowaniu rejestracji jednokrotnej, po wybraniu **Kontiki** w portalu Moje aplikacje są automatycznie zarejestrowaniu w usłudze Kontiki. Aby uzyskać więcej informacji na temat portalu Moje aplikacje, zobacz [dostępu i użycia aplikacji w portalu Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej, przejrzyj następujące artykuły:

- [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
