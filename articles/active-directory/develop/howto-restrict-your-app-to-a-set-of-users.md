---
title: Jak ograniczyć aplikacji zarejestrowanych w usłudze Azure Active Directory, grupy użytkowników
description: Dowiedz się, jak ograniczyć dostęp do aplikacji zarejestrowanej w usłudze Azure AD w wybranej grupie użytkowników.
services: active-directory
documentationcenter: ''
author: kalyankrishna1
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e07d9f0fa6ec6b4abc7ce96279b7b02faae298fa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65540196"
---
# <a name="how-to-restrict-your-app-to-a-set-of-users"></a>Instrukcje: Ograniczanie aplikacji do zestawu użytkowników

Aplikacje zarejestrowane w dzierżawie usługi Azure Active Directory (Azure AD) są domyślnie dostępne dla wszystkich użytkowników dzierżawy, którzy uwierzytelniają się pomyślnie.

Podobnie w przypadku programu [wielodostępnych](howto-convert-app-to-be-multi-tenant.md) aplikacji, wszyscy użytkownicy w dzierżawie usługi Azure AD, w którym ta aplikacja jest aprowizowany będzie dostęp do tej aplikacji po pomyślnym uwierzytelnieniu, w ramach ich odpowiednich dzierżawy.

Administratorzy dzierżawy i deweloperzy często mają wymagania gdzie aplikacji musi być określony zbiór użytkowników. Deweloperzy mogą wykonać takie same przy użyciu popularnych autoryzacji wzorców takich jak roli na podstawie kontroli dostępu (RBAC), ale takie podejście wymaga znacznej ilości pracy na części projektanta.

Usługa Azure AD umożliwia dzierżawy, administratorów i deweloperów ograniczyć aplikację do określonej grupy użytkowników lub grup zabezpieczeń w ramach dzierżawy.

## <a name="supported-app-configurations"></a>Konfiguracje obsługiwane aplikacji

Możliwość ograniczenia aplikacji do określonej grupy użytkowników lub grup zabezpieczeń w dzierżawie współpracuje z następujących typów aplikacji:

- Aplikacje skonfigurowane pod kątem federacyjnego logowania jednokrotnego przy użyciu uwierzytelniania opartego na SAML
- Aplikacje serwera proxy aplikacji, które używają wstępnego uwierzytelniania usługi Azure AD
- Aplikacje utworzone bezpośrednio na platformie aplikacji Azure AD, które używają protokołu OAuth 2.0/OpenID Connect uwierzytelniania po użytkownik lub administrator wyraził zgodę na tę aplikację.

     > [!NOTE]
     > Ta funkcja jest dostępna dla aplikacji sieci web sieci web i aplikacji interfejsu API i enterprise tylko. Aplikacje, które są zarejestrowane jako [natywnych](quickstart-v1-integrate-apps-with-azure-ad.md) nie może być ograniczone do zbiór użytkowników lub grup zabezpieczeń w ramach dzierżawy.

## <a name="update-the-app-to-enable-user-assignment"></a>Aktualizacja aplikacji, aby umożliwić przypisanie użytkownika

1. Przejdź do [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**
1. Na górnym pasku wybierz konto logowania. 
1. W obszarze **katalogu**, wybierz dzierżawę usługi Azure AD, w którym aplikacja zostanie zarejestrowana.
1. W obszarze nawigacji po lewej stronie, wybierz **usługi Azure Active Directory**. Jeśli usługi Azure Active Directory nie jest dostępne w okienku nawigacji, następnie wykonaj następujące kroki:

    1. Wybierz **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.
    1. Wpisz **usługi Azure Active Directory** w filtru pole wyszukiwania, a następnie wybierz **usługi Azure Active Directory** elementów z wynikiem.

1. W **usługi Azure Active Directory** okienku wybierz **aplikacje dla przedsiębiorstw** z **usługi Azure Active Directory** menu nawigacji po lewej stronie.
1. Wybierz **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

     Jeśli nie widzisz aplikacji mają być wyświetlane tutaj użyć różnych filtrów w górnej części **wszystkie aplikacje** listy, aby ograniczyć listę lub przewiń w dół na liście, aby zlokalizować aplikację.

1. Wybierz aplikację, którą chcesz przypisać użytkownika lub grupę zabezpieczeń z listy.
1. Przy stosowaniu **Przegląd** wybierz opcję **właściwości** menu nawigacji po lewej stronie aplikacji.
1. Znajdź ustawienie **wymagane przypisanie użytkownika?** i ustaw ją na **tak**. Jeśli ta opcja jest ustawiona **tak**, a następnie użytkownicy najpierw muszą być przypisani do tej aplikacji, zanim będzie mógł uzyskać do niego dostęp.
1. Wybierz **Zapisz** Aby zapisać tę zmianę konfiguracji.

## <a name="assign-users-and-groups-to-the-app"></a>Przypisywanie użytkowników i grup do aplikacji

Po skonfigurowaniu aplikacji, aby umożliwić przypisanie użytkownika można Przejdź dalej i przypisać użytkowników i grup do aplikacji.

1. Wybierz **użytkowników i grup** okienko w menu nawigacji po lewej stronie aplikacji.
1. W górnej części **użytkowników i grup** listy wybierz **Dodaj użytkownika** przycisk, aby otworzyć **Dodaj przydziału** okienka.
1. Wybierz **użytkowników** selektor z **Dodaj przydziału** okienka. 

     Lista użytkowników i grup zabezpieczeń będą wyświetlane wraz z pola tekstowego do wyszukiwania i Znajdź niektórych użytkownika lub grupy. Na tym ekranie można wybrać wielu użytkowników i grup w jednym z rzeczywistym użyciem.

1. Po zakończeniu wybierania użytkowników i grup, naciśnij klawisz **wybierz** przycisk na dole, aby przejść do następnej części.
1. Naciśnij klawisz **przypisać** przycisk w dolnej części, aby zakończyć przypisań użytkowników i grup do aplikacji. 
1. Upewnij się, że użytkownicy i grupy dodane są wyświetlane w zaktualizowanej **użytkowników i grup** listy.

