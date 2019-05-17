---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą monday.com | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i monday.com.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9e8ad807-0664-4e31-91de-731097c768e2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc142bf02a44ea85861f4cc648fd7ee8602c7520
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65780822"
---
# <a name="tutorial-azure-active-directory-integration-with-mondaycom"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą monday.com

W tym samouczku dowiesz się, jak zintegrować monday.com w usłudze Azure Active Directory (Azure AD).

Integrowanie monday.com z usługą Azure AD zapewnia następujące korzyści:

* Możesz użyć usługi Azure AD w celu kontrolowania, kto ma dostęp do monday.com.
* Użytkownicy mogą automatycznie zalogowany do monday.com za pomocą kont usługi Azure AD (logowanie jednokrotne).
* Możesz zarządzać kontami z jednej centralnej lokalizacji — witryny Azure Portal.

Aby uzyskać więcej informacji na temat oprogramowania jako usługi (SaaS) integracji aplikacji z usługą Azure AD, zobacz [logowanie jednokrotne do aplikacji w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą monday.com, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcję usługi Azure AD, Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) przed przystąpieniem do wykonywania.
* Subskrypcja monday.com przy użyciu logowania jednokrotnego włączone.

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka konfigurowania i testowania usługi Azure AD logowanie jednokrotne w środowisku testowym, a integracja monday.com z usługą Azure AD.

MONDAY.com obsługuje następujące funkcje:

* **Zainicjowane przez Dostawcę logowania jednokrotnego**
* **Logowanie jednokrotne inicjowane przez dostawcę tożsamości**
* **Aprowizowanie użytkowników typu just in time**

## <a name="add-mondaycom-in-the-azure-portal"></a>Dodaj monday.com w witrynie Azure portal

Aby zintegrować monday.com z usługą Azure AD, należy dodać monday.com z listą zarządzanych aplikacji SaaS.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W menu po lewej stronie wybierz **usługi Azure Active Directory**.

    ![Opcja usługi Azure Active Directory](common/select-azuread.png)

1. Wybierz pozycję **Aplikacje dla przedsiębiorstw** > **Wszystkie aplikacje**.

    ![Okienko Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

1. Aby dodać aplikację, wybierz pozycję **nową aplikację**.

    ![Nowa opcja aplikacji](common/add-new-app.png)

1. W polu wyszukiwania wprowadź **monday.com**. W wynikach wyszukiwania wybierz **monday.com**, a następnie wybierz pozycję **Dodaj**.

    ![MONDAY.com na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji, konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą monday.com w oparciu o nazwie użytkownika testowego **Britta Simon**. Dla logowania jednokrotnego do pracy należy ustanowić połączone relację między użytkownikiem usługi Azure AD i powiązanych użytkowników w monday.com.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą monday.com, należy wykonać poniższe bloki konstrukcyjne:

| Zadanie | Opis |
| --- | --- |
| **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)** | Umożliwia użytkownikom korzystać z tej funkcji. |
| **[Konfigurowanie monday.com logowania jednokrotnego](#configure-mondaycom-single-sign-on)** | Konfiguruje pojedynczy ustawień logowania jednokrotnego w aplikacji. |
| **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** | Testy usługi Azure AD logowanie jednokrotne dla użytkownika o nazwie Britta Simon. |
| **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** | Umożliwia Britta Simon korzystać z usługi Azure AD logowania jednokrotnego. |
| **[Tworzenie użytkownika testowego monday.com](#create-a-mondaycom-test-user)** | Tworzy odpowiednikiem Britta Simon monday.com połączonego z usługi Azure AD reprezentacja użytkownika. |
| **[Testowanie logowania jednokrotnego](#test-single-sign-on)** | Sprawdza, czy konfiguracja działa. |

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji skonfigurujesz usługi Azure AD logowanie jednokrotne za pomocą monday.com w witrynie Azure portal.

1. W [witryny Azure portal](https://portal.azure.com/)w **monday.com** okienko integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Konfigurowanie opcji rejestracji jednokrotnej](common/select-sso.png)

1. W **wybierz jedną metodę logowania jednokrotnego** okienku wybierz **SAML** lub **SAML/WS-Fed** trybu, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

1. W **Ustaw się logowania jednokrotnego przy użyciu protokołu SAML** okienku wybierz **Edytuj** (ikonę ołówka) aby otworzyć **podstawową konfigurację protokołu SAML** okienka.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W **podstawową konfigurację protokołu SAML** okienko, jeśli masz plik metadanych dostawcy usługi i chcesz skonfigurować *inicjowane przez dostawcę tożsamości tryb*, wykonaj następujące czynności:

    1. Wybierz **przekazywania pliku metadanych**.

       ![Opcja przekazywania pliku metadanych](common/upload-metadata.png)

    1. Aby wybrać plik metadanych, wybierz ikonę folderu, a następnie wybierz **przekazywanie**.

       ![Wybierz plik metadanych, a następnie wybierz przycisk Przekaż](common/browse-upload-metadata.png)

    1. Po pomyślnym przekazaniu pliku metadanych **identyfikator** i **adres URL odpowiedzi** wartości są wypełniane automatycznie w **podstawową konfigurację protokołu SAML** okienka:

       ![Wartości dostawcy tożsamości, w okienku podstawową konfigurację protokołu SAML](common/idp-intiated.png)

       > [!Note]
       > Jeśli **identyfikator** i **adres URL odpowiedzi** wartości nie są automatycznie wypełniane, wprowadź wartości ręcznie.

1. Aby skonfigurować aplikację w *zainicjowanego przez dostawcę usług trybu*:

    1. Wybierz **Ustaw dodatkowe adresy URL**.
    
    1. W **adres URL logowania** wprowadź adres URL z następującym wzorcem: https:\//\<Twojej domeny >. monday.com. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta monday.com](mailto:support@monday.com) można pobrać adresu URL logowania jednokrotnego.

        ![Opcja Set dodatkowe adresy URL](common/metadata-upload-additional-signon.png)

1. Aplikacja monday.com oczekuje twierdzenia SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Zarządzanie wartości tych atrybutów w **Ustaw się logowania jednokrotnego przy użyciu protokołu SAML** wybierz opcję **Edytuj** otworzyć **atrybutów użytkownika** okienka.

    ![Okienko atrybuty użytkownika](common/edit-attribute.png)

1. W obszarze **oświadczenia użytkownika**, wybierz opcję **Edytuj** edytowanie oświadczenia. Do dodawania roszczenia, wybierz **Dodaj nowe oświadczenie**, a następnie skonfiguruj atrybut tokenu SAML pokazany na wcześniejszej ilustracji. Następnie wykonaj następujące kroki: 

    1. Wybierz **Dodaj nowe oświadczenie**.

        ![Dodaj nowe oświadczenie opcji w okienku oświadczenia użytkownika](common/new-save-attribute.png)

    1. W **Zarządzanie oświadczenia użytkownika** okienko, ustaw następujące wartości:
        
       1. W **nazwa** wprowadź nazwę atrybutu, wyświetlany dla wiersza oświadczenia użytkownika.

       1. Pozostaw **Namespace** puste.

       1. Aby uzyskać **źródła**, wybierz opcję **atrybutu**.

       1. W **atrybut źródłowy** , wybierz wartość atrybutu wyświetlany dla wiersza oświadczenia użytkownika na liście.

       1. Wybierz **OK**, a następnie wybierz pozycję **Zapisz**.

       ![Zarządzaj oświadczenia użytkownika](common/new-attribute-details.png)

1. W **Ustaw się logowania jednokrotnego przy użyciu protokołu SAML** okienku w obszarze **certyfikat podpisywania SAML**, wybierz opcję **Pobierz** obok **certyfikat (Base64)**. Wybierz opcję pobierania, w zależności od wymagań. Zapisz certyfikat na komputerze.

    ![Opcja pobierania certyfikat (Base64)](common/certificatebase64.png)

1. W **Konfigurowanie monday.com** sekcji, skopiuj następujące adresy URL, zgodnie z wymaganiami:

    * Adres URL logowania
    * Identyfikator usługi Azure AD
    * Adres URL wylogowywania

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-mondaycom-single-sign-on"></a>Konfigurowanie monday.com logowania jednokrotnego

Aby skonfigurować logowanie jednokrotne na stronie monday.com, Wyślij pobrany plik certyfikatu (kodowanie Base64) i odpowiednie adresy URL, które zostały skopiowane z portalu Azure w celu [zespołem pomocy technicznej monday.com](mailto:support@monday.com). Zespół pomocy technicznej monday.com używa tych informacji, wysyłanych przez nich, aby upewnić się, że SAML logowania jednokrotnego połączenie jest prawidłowo po obu stronach.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego o nazwie Britta Simon w witrynie Azure Portal.

1. W witrynie Azure Portal wybierz kolejno pozycje **Azure Active Directory** > **Użytkownicy** > **Wszyscy użytkownicy**.

    ![Użytkownicy i wszystkie opcje użytkowników](common/users.png)

1. Wybierz przycisk **Nowy użytkownik**.

    ![Nowa opcja użytkownika](common/new-user.png)

1. W okienku **Użytkownik** wykonaj następujące czynności:

    1. W polu **Nazwa** wpisz **BrittaSimon**.
  
    1. W **nazwa_użytkownika** wprowadź **brittasimon\@\<Twojej domeny firmy >.\< Rozszerzenia >**. Na przykład **brittasimon\@contoso.com**.

    1. Wybierz **hasło Show** pole wyboru. Zanotuj wartość, która jest wyświetlana w **hasło** pole.

    1. Wybierz pozycję **Utwórz**.

    ![W okienku użytkownika](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz udostępnić Britta Simon monday.com tak korzystaniem Azure logowania jednokrotnego.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje** > **monday.com**.

    ![Okienko Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

1. Na liście aplikacji wybierz **monday.com**.

    ![MONDAY.com na liście aplikacji](common/all-applications.png)

1. W menu wybierz pozycję **Użytkownicy i grupy**.

    ![Opcja użytkowników i grup](common/users-groups-blade.png)

1. Wybierz przycisk **Dodaj użytkownika**. Następnie w okienku **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

1. W **użytkowników i grup** okienku wybierz **Britta Simon** na liście Użytkownicy. Wybierz pozycję **Wybierz**.

1. Jeśli są oczekiwane wartości roli dla asercji SAML w **wybierz rolę** okienku zaznacz odpowiednie rolę dla użytkownika z listy. Wybierz pozycję **Wybierz**.

1. W **Dodaj przydziału** okienku wybierz **przypisać**.

### <a name="create-a-mondaycom-test-user"></a>Tworzenie użytkownika testowego monday.com

W tej sekcji użytkownika o nazwie Britta Simon jest tworzony w aplikacji monday.com. MONDAY.com obsługuje aprowizacji użytkowników w czasie, który jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w monday.com, nowy katalog jest tworzony po uwierzytelnieniu.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji możesz przetestować konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu portalu Moje aplikacje.

Po skonfigurowaniu rejestracji jednokrotnej, po wybraniu **monday.com** w portalu Moje aplikacje są automatycznie zarejestrowaniu w usłudze monday.com. Aby uzyskać więcej informacji na temat portalu Moje aplikacje, zobacz [dostępu i użycia aplikacji w portalu Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej, przejrzyj następujące artykuły:

- [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
