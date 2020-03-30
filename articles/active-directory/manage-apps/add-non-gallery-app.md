---
title: Dodawanie aplikacji spoza galerii — platforma tożsamości firmy Microsoft | Dokumenty firmy Microsoft
description: Dodaj aplikację spoza galerii do dzierżawy usługi Azure AD.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd5a5f100dbe09c3b82f58183a118ee3bf455f70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063615"
---
# <a name="add-an-unlisted-non-gallery-application-to-your-azure-ad-organization"></a>Dodawanie niepublicznej aplikacji (niezwiązanych z galerią) do organizacji usługi Azure AD

Oprócz opcji w [galerii aplikacji usługi Azure AD,](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)można dodać **aplikację inną niż galeria.** Można dodać dowolną aplikację, która już istnieje w organizacji lub dowolnej aplikacji innej firmy od dostawcy, który nie jest jeszcze częścią galerii usługi Azure AD. W zależności od [umowy licencyjnej](https://azure.microsoft.com/pricing/details/active-directory/)dostępne są następujące funkcje:

- Integracja samoobsługowa dowolnej aplikacji obsługującej dostawców tożsamości [JĘZYKA OŚWIADCZEŃ ZABEZPIECZEŃ (SAML) 2.0](https://wikipedia.org/wiki/SAML_2.0) (inicjowane jako SP lub inicjowane przez IdP)
- Samoobsługowa integracja dowolnej aplikacji internetowej, która ma stronę logowania opartą na języku HTML przy użyciu [logowania logowania opartego na hasłach](what-is-single-sign-on.md#password-based-sso)
- Samoobsługowe połączenie aplikacji korzystających z [protokołu SciM (System for Cross-Domain Identity Management) do inicjowania obsługi administracyjnej przez użytkowników](../app-provisioning/use-scim-to-provision-users-and-groups.md)
- Możliwość dodawania łączy do dowolnej aplikacji w [programie Uruchamianie aplikacji usługi Office 365](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) lub [w panelu dostępu usługi Azure AD](what-is-single-sign-on.md#linked-sign-on)

W tym artykule opisano sposób dodawania aplikacji spoza galerii do **aplikacji dla przedsiębiorstw** w witrynie Azure portal bez pisania kodu. Jeśli zamiast tego szukasz wskazówek dla deweloperów dotyczących integrowania aplikacji niestandardowych z usługą Azure AD, zobacz [Scenariusze uwierzytelniania dla usługi Azure AD.](../develop/authentication-scenarios.md) Podczas tworzenia aplikacji, która używa nowoczesnego protokołu, takich jak [OpenId Connect/OAuth](../develop/active-directory-v2-protocols.md) do uwierzytelniania użytkowników, można zarejestrować go na platformie tożsamości firmy Microsoft przy użyciu [środowiska rejestracji aplikacji](../develop/quickstart-register-app.md) w witrynie Azure portal.

## <a name="add-a-non-gallery-application"></a>Dodawanie aplikacji spoza galerii

1. Zaloguj się do [portalu usługi Azure Active Directory](https://aad.portal.azure.com/) przy użyciu konta administratora platformy tożsamości firmy Microsoft.

2. Wybierz **aplikacje dla przedsiębiorstw** > **Nowa aplikacja**.

3. (Opcjonalnie, ale zalecane) W polu wyszukiwania **Przeglądaj galerię usług Azure AD Gallery** wprowadź nazwę wyświetlaną aplikacji. 

4. Wybierz **pozycję Utwórz własną aplikację**. Zostanie wyświetlona strona **Utwórz własną aplikację.**

   ![Dodawanie aplikacji](media/add-non-gallery-app/create-your-own-application.png)

5. Zacznij wpisywać nazwę wyświetlaną dla nowej aplikacji. Jeśli istnieją aplikacje galerii o podobnych nazwach, pojawią się na liście wyników wyszukiwania.

   > [!NOTE]
   > Zalecamy korzystanie z wersji galerii aplikacji, gdy tylko jest to możliwe. Jeśli aplikacja, którą chcesz dodać, pojawi się w wynikach wyszukiwania, wybierz aplikację i pomiń pozostałą część tej procedury.

6. W obszarze Co chcesz zrobić z **Integrate any other application you don't find in the gallery** **aplikacją?** Ta opcja jest zwykle używana dla aplikacji SAML i WS-Fed.

   > [!NOTE]
   > Pozostałe dwie opcje są używane w następujących scenariuszach:
   >* **Konfigurowanie serwera proxy aplikacji dla bezpiecznego dostępu zdalnego do aplikacji lokalnej** otwiera stronę konfiguracji serwera proxy aplikacji usługi Azure AD i łączników.
   >* **Zarejestruj aplikację, nad którą pracujesz, aby zintegrować się z usługą Azure AD** otwiera stronę **rejestracje aplikacji.** Ta opcja jest zwykle używana w aplikacjach OpenID Connect.

7. Wybierz **pozycję Utwórz**. Zostanie otwarta strona **Przegląd** aplikacji.

## <a name="configure-user-sign-in-properties"></a>Konfigurowanie właściwości logowania użytkownika

1. Wybierz **pozycję Właściwości,** aby otworzyć okienko właściwości do edycji.

    ![Okienko Edytowanie właściwości](media/add-non-gallery-app/edit-properties.png)

2. Ustaw następujące opcje, aby określić, w jaki sposób użytkownicy, którzy są przypisani lub nieprzypisane do aplikacji mogą logować się do aplikacji i czy użytkownik może zobaczyć aplikację w panelu dostępu.

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

3. Aby użyć niestandardowego logo, utwórz logo o wymiarach 215 na 215 pikseli i zapisz je w formacie PNG. Następnie przejdź do swojego logo i prześlij go.

    ![Zmienianie logo](media/add-non-gallery-app/change-logo.png)

4. Po zakończeniu wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy aplikacja została dodana do organizacji usługi Azure AD, [wybierz metodę logowania jednokrotnego,](what-is-single-sign-on.md#choosing-a-single-sign-on-method) której chcesz użyć, i zapoznaj się z odpowiednim artykułem poniżej:

- [Konfigurowanie logowania jednokrotnego opartego na języku SAML](configure-single-sign-on-non-gallery-applications.md)
- [Konfigurowanie logowania jednokrotnego hasła](configure-password-single-sign-on-non-gallery-applications.md)
- [Konfigurowanie połączonego logowania](configure-linked-sign-on.md)
