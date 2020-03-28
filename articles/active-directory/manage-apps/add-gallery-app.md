---
title: Dodawanie aplikacji galerii — Usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Dowiedz się, jak dodać aplikację z galerii usługi Azure AD do aplikacji korporacyjnych platformy Azure.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: tutorial
ms.workload: identity
ms.date: 10/29/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: af0a826c499790c86a20ea7061075a6c3e66b3a4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73062557"
---
# <a name="add-a-gallery-app-to-your-azure-ad-organization"></a>Dodawanie aplikacji galerii do organizacji usługi Azure AD

Usługa Azure Active Directory (Azure AD) ma galerię zawierającą tysiące wstępnie zintegrowanych aplikacji, które są włączone za pomocą logowania jednokrotnego w przedsiębiorstwie. W tym artykule opisano ogólne kroki dodawania aplikacji z galerii do organizacji usługi Azure AD.

> [!IMPORTANT]
> Najpierw sprawdź, czy aplikacja znajduje się [na liście samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/). Prawdopodobnie znajdziesz wskazówki krok po kroku dotyczące dodawania i konfigurowania aplikacji galerii, którą chcesz dodać.

## <a name="add-a-gallery-application"></a>Dodawanie aplikacji galerii

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator globalny dzierżawy usługi Azure AD, administrator aplikacji w chmurze lub administrator aplikacji.

1. W [witrynie Azure portal](https://portal.azure.com)po lewej stronie panelu nawigacji wybierz pozycję **Azure Active Directory**.

1. W okienku **Usługi Active Directory wybierz** pozycję **Aplikacje przedsiębiorstwa**.

    ![Otwarte aplikacje dla przedsiębiorstw](media/add-gallery-app/open-enterprise-apps.png)


3. Aby dodać aplikację galerii do dzierżawy, wybierz pozycję **Nowa aplikacja**.

    ![Wybierz nową aplikację, aby dodać aplikację galerii do dzierżawy](media/add-gallery-app/new-application.png)

 4. Przełącz się do nowej galerii podglądu środowiska: W baner u góry **strony Dodaj aplikację**, wybierz link, który mówi Kliknij **tutaj, aby wypróbować nową i ulepszoną galerię aplikacji.**

5. Okienko **Przeglądaj galerię usługi Azure AD Galerii** otwiera i wyświetla kafelki dla platform w chmurze, aplikacji lokalnych i polecanych aplikacji. Należy zauważyć, że aplikacje wymienione w **polecane aplikacje** sekcji mają ikony wskazujące, czy obsługują one federacyjne logowania jednokrotnego (Logowanie jednokrotne) i inicjowania obsługi administracyjnej.

    ![Wyszukiwanie aplikacji według nazwy lub kategorii](media/add-gallery-app/browse-gallery.png)

6. Przejrzyj galerię aplikacji, którą chcesz dodać, lub wyszukaj aplikację, wprowadzając jej nazwę w polu wyszukiwania. Następnie wybierz aplikację z wyników. (Opcjonalnie) W formularzu można edytować nazwę aplikacji, aby dopasować ją do potrzeb organizacji.

    ![Pokazuje, jak dodać aplikację z galerii](media/add-gallery-app/create-application.png)

7. Wybierz **pozycję Utwórz**. Zostanie wyświetlona strona wprowadzenia z opcjami konfigurowania aplikacji w organizacji.

## <a name="configure-user-sign-in-properties"></a>Konfigurowanie właściwości logowania użytkownika

1. Wybierz **pozycję Właściwości,** aby otworzyć okienko właściwości do edycji.

    ![Okienko Edytowanie właściwości](media/add-gallery-app/edit-properties.png)

1. Ustaw następujące opcje, aby określić, w jaki sposób użytkownicy, którzy są przypisani lub nieprzypisane do aplikacji mogą logować się do aplikacji i czy użytkownik może zobaczyć aplikację w panelu dostępu.

    - Opcja **Włączono dla użytkowników w celu logowania się** określa, czy użytkownicy przypisani do aplikacji mogą się logować.
    - **Wymagane przypisanie użytkownika** określa, czy użytkownicy, którzy nie są przypisani do aplikacji, mogą się logować.
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
       | Masz włączono logowanie się użytkowników? | Wymagane przypisanie użytkownika? | Widoczne dla użytkowników? | Czy nieprzypisani użytkownicy mogą się logować? | Czy nieprzypisani użytkownicy widzą aplikację?* |
       | tak | tak | tak | nie  | nie   |
       | tak | tak | nie  | nie  | nie   |
       | tak | nie  | tak | tak | nie   |
       | tak | nie  | nie  | tak | nie   |
       | nie  | tak | tak | nie  | nie   |
       | nie  | tak | nie  | nie  | nie   |
       | nie  | nie  | tak | nie  | nie   |
       | nie  | nie  | nie  | nie  | nie   |

     *Czy użytkownik widzi aplikację w panelu dostępu i narzędziu do uruchamiania aplikacji usługi Office 365?

1. Aby użyć niestandardowego logo, utwórz logo o wymiarach 215 na 215 pikseli i zapisz je w formacie PNG. Następnie przejdź do swojego logo i prześlij go.

    ![Zmienianie logo](media/add-gallery-app/change-logo.png)

1. Po zakończeniu wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy aplikacja została dodana do organizacji usługi Azure AD, [wybierz metodę logowania jednokrotnego,](what-is-single-sign-on.md#choosing-a-single-sign-on-method) której chcesz użyć, i zapoznaj się z odpowiednim artykułem poniżej:

- [Konfigurowanie logowania jednokrotnego opartego na języku SAML](configure-single-sign-on-non-gallery-applications.md)
- [Konfigurowanie logowania jednokrotnego hasła](configure-password-single-sign-on-non-gallery-applications.md)
- [Konfigurowanie połączonego logowania](configure-linked-sign-on.md)

