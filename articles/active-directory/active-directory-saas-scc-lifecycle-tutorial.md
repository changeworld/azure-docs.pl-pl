---
title: "Samouczek: Azure Active Directory integrację z cyklem życia SCC | Dokumentacja firmy Microsoft"
description: "Dowiedz się, jak użyć SCC cyklu życia z usługą Azure Active Directory w celu włączenia logowania jednokrotnego, automatyczne Inicjowanie obsługi i inne!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: mtillman
ms.assetid: 9748bf38-ffc3-4d51-a1ae-207ce57104fa
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/03/2018
ms.author: jeedes
ms.openlocfilehash: 943698311d10b479879fd92d2482d827650e2b0c
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/14/2018
---
# <a name="tutorial-azure-active-directory-integration-with-scc-lifecycle"></a>Samouczek: Azure Active Directory integrację z cyklem życia SCC
Celem tego samouczka jest pokazanie integracji Azure i SCC cyklu życia.  

Scenariusz opisany w tym samouczku założono, że już następujące elementy:

* Ważnej subskrypcji platformy Azure
* Cykl życia SCC rejestracji jednokrotnej (SSO) włączone subskrypcji

Ten samouczek użytkowników usługi Azure AD, zostały przypisane do cyklu życia SCC będzie można funkcji logowania jednokrotnego do aplikacji w witrynie firmy SCC cyklu życia (usługa zainicjował dostawcy logowania) lub przy użyciu [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md).

Scenariusz opisany w tym samouczku składa się z następujących bloków konstrukcyjnych:

1. Włączanie integracji aplikacji dla SCC cykl życia
2. Konfigurowanie rejestracji jednokrotnej (SSO)
3. Konfigurowanie Inicjowanie obsługi użytkowników
4. Przypisywanie użytkowników

![Scenariusz](./media/active-directory-saas-scc-lifecycle-tutorial/IC794120.png "scenariusza")

## <a name="enable-the-application-integration-for-scc-lifecycle"></a>Włącz integrację aplikacji dla SCC cykl życia
Celem tej sekcji jest przedstawiają sposób włączania integracji aplikacji dla SCC cyklu życia.

**Aby włączyć integrację aplikacji dla cyklu życia SCC, wykonaj następujące czynności:**

1. W klasycznym portalu Azure, w okienku nawigacji po lewej stronie kliknij **usługi Active Directory**.
   
    ![Usługi Active Directory](./media/active-directory-saas-scc-lifecycle-tutorial/IC700993.png "usługi Active Directory")
2. Z **katalogu** listy, wybierz katalog, dla którego chcesz włączyć integracji katalogów.
3. Aby otworzyć widok aplikacji, w widoku katalogu, kliknij przycisk **aplikacji** w menu u góry.
   
    ![Aplikacje](./media/active-directory-saas-scc-lifecycle-tutorial/IC700994.png "aplikacji")
4. Kliknij przycisk **Dodaj** w dolnej części strony.
   
    ![Dodaj aplikację](./media/active-directory-saas-scc-lifecycle-tutorial/IC749321.png "Dodaj aplikację")
5. Na **co chcesz zrobić** okna dialogowego, kliknij przycisk **dodać aplikację z galerii**.
   
    ![Dodawanie aplikacji z gallerry](./media/active-directory-saas-scc-lifecycle-tutorial/IC749322.png "dodać aplikację z gallerry")
6. W **pole wyszukiwania**, typ **cyklu życia SCC**.
   
    ![Galerii aplikacji](./media/active-directory-saas-scc-lifecycle-tutorial/IC794121.png "galerii aplikacji")
7. W okienku wyników wybierz **cyklu życia SCC**, a następnie kliknij przycisk **Complete** można dodać aplikację.
   
    ![Cykl życia SCC](./media/active-directory-saas-scc-lifecycle-tutorial/IC795082.png "SCC cykl życia")
   
## <a name="configure-single-sign-on"></a>Konfigurowanie rejestracji jednokrotnej

Celem tej sekcji jest przedstawiają sposób umożliwić użytkownikom uwierzytelnianie na cyklu życia SCC do swojego konta w usłudze Azure AD przy użyciu federacyjnego na podstawie protokołu SAML.

**Aby skonfigurować rejestrację jednokrotną, wykonaj następujące czynności:**

1. W klasycznym portalu Azure na **cyklu życia SCC** strona integracji aplikacji, kliknij przycisk **skonfigurować logowanie jednokrotne** otworzyć ** skonfigurować rejestrację jednokrotną ** okna dialogowego.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-scc-lifecycle-tutorial/IC794122.png "skonfigurować logowanie jednokrotne")
2. Na **jak chcesz użytkownikom zalogować się na cyklu życia SCC** wybierz pozycję **usługi Microsoft Azure AD rejestracji jednokrotnej**, a następnie kliknij przycisk **dalej**.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-scc-lifecycle-tutorial/IC794123.png "skonfigurować logowanie jednokrotne")
3. Na **Konfigurowanie adresu URL aplikacji** strony w **na adres URL logowania** tekstowym, wpisz adres URL używany przez użytkowników do logowania się SCC cyklem życia aplikacji przy użyciu następującego wzorca " *https://bs1.scc.com/lc7/welcome/customer/PICTtest.aspx* ", a następnie kliknij przycisk **dalej**.
   
    ![Skonfiguruj adres URL aplikacji](./media/active-directory-saas-scc-lifecycle-tutorial/IC794124.png "skonfigurować adres URL aplikacji")
4. Na **skonfigurować logowanie jednokrotne w cyklu życia SCC** kliknij przycisk **pobierania metadanych**, a następnie zapisz plik metadanych lokalnie na komputerze.
   
   ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-scc-lifecycle-tutorial/IC795083.png "skonfigurować logowanie jednokrotne")
5. Przesyła ten plik metadanych, aby SCC cyklu życia z pomocą techniczną.
   
   >[!NOTE]
   >Logowania jednokrotnego musi zostać włączona przez zespół pomocy technicznej SCC cyklu życia.
   > 
   > 

6. W klasycznym portalu Azure, wybierz Potwierdzenie konfiguracji rejestracji jednokrotnej, a następnie kliknij przycisk **Complete** zamknąć **skonfigurować rejestrację jednokrotną** okna dialogowego.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-scc-lifecycle-tutorial/IC794125.png "skonfigurować logowanie jednokrotne")
   
## <a name="configure-user-provisioning"></a>Skonfiguruj Inicjowanie obsługi użytkowników

Aby włączyć logowanie w cyklu życia SCC użytkowników usługi Azure AD, muszą mieć przydzielone do SCC cyklu życia. Nie ma elementu akcji można skonfigurować do cyklu życia SCC Inicjowanie obsługi użytkowników.

Gdy przypisany użytkownik próbuje zalogować się na cyklu życia SCC, konto cyklu życia SCC jest tworzony automatycznie w razie potrzeby.

>[!NOTE]
>Możesz użyć innych cyklu życia SCC użytkownika konta tworzenia narzędzi lub interfejsów API dostarczonych przez SCC cyklu życia do kont użytkowników usługi AAD.
> 
> 

## <a name="assign-users"></a>Przypisywanie użytkowników
Aby przetestować konfigurację, musisz przyznać użytkowników usługi Azure AD, czy chcesz zezwolić, używając przypisywania do nich dostęp aplikacji do niego.

**Do przypisywania użytkowników do SCC cyklu życia, wykonaj następujące czynności:**

1. W klasycznym portalu Azure Utwórz konto testu.
2. Na ** cyklu życia SCC ** strona integracji aplikacji, kliknij przycisk **przypisywać użytkowników**.
   
    ![Przypisywanie użytkowników](./media/active-directory-saas-scc-lifecycle-tutorial/IC794126.png "przypisywanie użytkowników")
3. Wybierz użytkownika testowego, kliknij przycisk **przypisać**, a następnie kliknij przycisk **tak** o potwierdzenie przypisania.
   
    ![Tak](./media/active-directory-saas-scc-lifecycle-tutorial/IC767830.png "tak")

Jeśli chcesz przetestować ustawienia logowania jednokrotnego, otwórz Panel dostępu. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md).

