---
title: Dodawanie aplikacji Galerii — Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak dodać aplikację z galerii usługi Azure AD do aplikacji platformy Azure dla przedsiębiorstw.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: tutorial
ms.workload: identity
ms.date: 06/18/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1bdf275bbafa9c46cfc4577ac2843da0be74c7ef
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68477280"
---
# <a name="add-a-gallery-app-to-your-azure-ad-organization"></a>Dodawanie aplikacji galerii do organizacji usługi Azure AD

Azure Active Directory (Azure AD) zawiera galerię zawierającą tysiące wstępnie zintegrowanych aplikacji, które są włączone przy użyciu logowania jednokrotnego w przedsiębiorstwie. W tym artykule opisano ogólne kroki dodawania aplikacji z galerii do organizacji usługi Azure AD.

> [!IMPORTANT]
> Najpierw sprawdź, czy aplikacja znajduje się na [liście samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/). Prawdopodobnie znajdziesz wskazówki krok po kroku dotyczące dodawania i konfigurowania aplikacji galerii, którą chcesz dodać.

## <a name="add-a-gallery-application"></a>Dodawanie aplikacji galerii

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator globalny dzierżawy usługi Azure AD, administrator aplikacji w chmurze lub administrator aplikacji.

1. W [Azure Portal](https://portal.azure.com)w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

1. W okienku **Azure Active Directory** wybierz pozycję **aplikacje dla przedsiębiorstw**.

    ![Otwarte aplikacje dla przedsiębiorstw](media/add-application-portal/open-enterprise-apps.png)

1. Wybierz pozycję **Nowa aplikacja**.

    ![Nowa aplikacja](media/add-application-portal/new-application.png)

1. W obszarze **Dodaj z galerii**w polu wyszukiwania wprowadź nazwę aplikacji, którą chcesz dodać. 

    ![Wyszukiwanie według nazwy lub kategorii](media/add-application-portal/categories.png)

1. Wybierz aplikację z wyników.

1. Obowiązkowe W formularzu specyficznym dla aplikacji można edytować nazwę aplikacji, aby odpowiadała potrzebom organizacji.

1. Wybierz pozycję **Dodaj**. Zostanie otwarta strona **Przegląd** aplikacji.

## <a name="configure-user-sign-in-properties"></a>Konfigurowanie właściwości logowania użytkownika

1. Wybierz pozycję **Właściwości** , aby otworzyć okienko właściwości do edycji.

    ![Edytuj okienko właściwości](media/add-application-portal/edit-properties.png)

1. Ustaw następujące opcje, aby określić, jak użytkownicy przypisani lub nieprzypisane do aplikacji mogą zalogować się do aplikacji, a użytkownik może zobaczyć aplikację w panelu dostępu.

    - Opcja **Włączono dla użytkowników w celu logowania się** określa, czy użytkownicy przypisani do aplikacji mogą się logować.
    - **Wymagane przypisanie użytkownika** określa, czy użytkownicy, którzy nie są przypisani do aplikacji, mogą się zalogować.
    - Opcja **Widoczne dla użytkownika** określa, czy użytkownicy przypisani do aplikacji mogą ją widzieć w panelu dostępu i narzędziu do uruchamiania aplikacji usługi O365.

      Zachowanie w przypadku użytkowników **przypisanych**:

       | Ustawienia właściwości aplikacji | | | Środowisko użytkowników przypisanych | |
       |---|---|---|---|---|
       | Włączono dla użytkowników w celu logowania się? | Wymagane przypisanie użytkownika? | Widoczne dla użytkowników? | Czy przypisani użytkownicy mogą się logować? | Czy przypisani użytkownicy widzą aplikację?* |
       | tak | tak | tak | tak | tak  |
       | tak | tak | nie  | tak | nie   |
       | tak | nie  | tak | tak | tak  |
       | tak | nie  | nie  | tak | nie   |
       | nie  | tak | tak | nie  | nie   |
       | nie  | tak | nie  | nie  | nie   |
       | nie  | nie  | tak | nie  | nie   |
       | nie  | nie  | nie  | nie  | nie   |

      Zachowanie w przypadku użytkowników **nieprzypisanych**:

       | Ustawienia właściwości aplikacji | | | Środowisko użytkowników nieprzypisanych | |
       |---|---|---|---|---|
       | Włączono, aby użytkownicy mogli się zalogować? | Trzeba przypisać użytkownika? | Widoczne dla użytkowników? | Czy nieprzypisani użytkownicy mogą się logować? | Czy nieprzypisani użytkownicy widzą aplikację?* |
       | tak | tak | tak | nie  | nie   |
       | tak | tak | nie  | nie  | nie   |
       | tak | nie  | tak | tak | nie   |
       | tak | nie  | nie  | tak | nie   |
       | nie  | tak | tak | nie  | nie   |
       | nie  | tak | nie  | nie  | nie   |
       | nie  | nie  | tak | nie  | nie   |
       | nie  | nie  | nie  | nie  | nie   |

     *Czy użytkownik widzi aplikację w panelu dostępu i narzędziu do uruchamiania aplikacji usługi Office 365?

1. Aby użyć niestandardowego logo, Utwórz logo o 215 przez 215 pikseli i Zapisz je w formacie PNG. Następnie przejdź do swojego logo i przekaż go.

    ![Zmienianie logo](media/add-application-portal/change-logo.png)

1. Gdy skończysz, wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

Po dodaniu aplikacji do organizacji usługi Azure AD [Wybierz metodę logowania](what-is-single-sign-on.md#choosing-a-single-sign-on-method) jednokrotnego, której chcesz użyć, i zapoznaj się z odpowiednim artykułem poniżej:

- [Konfigurowanie logowania jednokrotnego opartego na protokole SAML](configure-single-sign-on-non-gallery-applications.md)
- [Konfigurowanie logowania jednokrotnego przy użyciu hasła](configure-password-single-sign-on-non-gallery-applications.md)
- [Konfiguruj logowanie połączone](configure-linked-sign-on.md)

