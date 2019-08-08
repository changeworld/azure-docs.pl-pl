---
title: Jak ograniczyć swoją zarejestrowaną przez Azure Active Directory aplikację do zbioru użytkowników
description: Dowiedz się, jak ograniczyć dostęp do aplikacji zarejestrowanych w usłudze Azure AD do wybranego zestawu użytkowników.
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
ms.openlocfilehash: a51c49633e68fdc5f9afd4bf0205adaa625940ff
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812961"
---
# <a name="how-to-restrict-your-app-to-a-set-of-users"></a>Instrukcje: Ograniczanie aplikacji do zestawu użytkowników

Aplikacje zarejestrowane w dzierżawie usługi Azure Active Directory (Azure AD) są domyślnie dostępne dla wszystkich użytkowników dzierżawy, którzy pomyślnie uwierzytelniają się.

Podobnie, w przypadku aplikacji wielodostępnej, wszyscy użytkownicy w dzierżawie usługi Azure AD, w której ta aplikacja jest obsługiwana, będą mogli uzyskiwać dostęp do tej aplikacji po pomyślnym uwierzytelnieniu w odpowiedniej dzierżawie. [](howto-convert-app-to-be-multi-tenant.md)

Administratorzy dzierżawy i deweloperzy często mają wymagania, w przypadku których aplikacja musi być ograniczona do określonego zbioru użytkowników. Deweloperzy mogą osiągnąć ten sam sposób, korzystając ze popularnych wzorców autoryzacji, takich jak Access Control oparte na rolach (RBAC), ale takie podejście wymaga dużej ilości pracy na części dewelopera.

Usługa Azure AD umożliwia administratorom dzierżawy i deweloperom ograniczenie aplikacji do określonego zestawu użytkowników lub grup zabezpieczeń w dzierżawie.

## <a name="supported-app-configurations"></a>Obsługiwane konfiguracje aplikacji

Opcja ograniczenia aplikacji do określonego zestawu użytkowników lub grup zabezpieczeń w dzierżawie współpracuje z następującymi typami aplikacji:

- Aplikacje skonfigurowane do federacyjnego logowania jednokrotnego przy użyciu uwierzytelniania opartego na protokole SAML
- Aplikacje serwera proxy aplikacji korzystające z uwierzytelniania wstępnego usługi Azure AD
- Aplikacje utworzone bezpośrednio na platformie aplikacji usługi Azure AD, która korzysta z uwierzytelniania OAuth 2.0/OpenID Connect, gdy użytkownik lub administrator wyraził zgodę na tę aplikację.

     > [!NOTE]
     > Ta funkcja jest dostępna tylko dla aplikacji sieci Web/internetowego interfejsu API i aplikacje dla przedsiębiorstw. Aplikacje zarejestrowane jako natywne [](quickstart-v1-integrate-apps-with-azure-ad.md) nie mogą być ograniczone do zestawu użytkowników lub grup zabezpieczeń w dzierżawie.

## <a name="update-the-app-to-enable-user-assignment"></a>Aktualizowanie aplikacji w celu włączenia przypisania użytkownika

Istnieją dwa sposoby tworzenia aplikacji z włączonym przypisaniem użytkownika. Jeden z nich wymaga roli **administratora globalnego** , a druga nie.

### <a name="enterprise-applications-requires-the-global-adminstrator-role"></a>Aplikacje dla przedsiębiorstw (wymaga roli administratora globalnego)

1. Przejdź do [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny**.
1. Na górnym pasku wybierz konto zalogowane. 
1. W obszarze **katalog**wybierz dzierżawę usługi Azure AD, w której aplikacja zostanie zarejestrowana.
1. W obszarze nawigacji po lewej stronie wybierz pozycję **Azure Active Directory**. Jeśli Azure Active Directory nie jest dostępny w okienku nawigacji, wykonaj następujące kroki:

    1. Wybierz pozycję **wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.
    1. Wpisz **Azure Active Directory** w polu wyszukiwania filtru, a następnie wybierz element **Azure Active Directory** z wyniku.

1. W okienku **Azure Active Directory** wybierz pozycję **aplikacje dla przedsiębiorstw** z menu nawigacji po lewej stronie **Azure Active Directory** .
1. Wybierz pozycję **wszystkie aplikacje** , aby wyświetlić listę wszystkich aplikacji.

     Jeśli nie widzisz aplikacji, która ma być wyświetlana w tym miejscu, Użyj różnych filtrów w górnej części listy **wszystkie aplikacje** , aby ograniczyć listę, lub przewiń w dół listy, aby zlokalizować aplikację.

1. Wybierz aplikację, do której chcesz przypisać użytkownika lub grupę zabezpieczeń.
1. Na stronie **Przegląd** aplikacji wybierz pozycję **Właściwości** z menu nawigacji po lewej stronie aplikacji.
1. Znajdź **wymagane przypisanie użytkownika?** i ustaw wartość **tak**. Jeśli ta opcja jest ustawiona na **tak**, użytkownicy muszą najpierw zostać przypisani do tej aplikacji, zanim będą mieli do niej dostęp.
1. Wybierz pozycję **Zapisz** , aby zapisać tę zmianę konfiguracji.

### <a name="app-registration"></a>Rejestracja aplikacji

1. Przejdź do [**Azure Portal**](https://portal.azure.com/).
1. Na górnym pasku wybierz konto zalogowane. 
1. W obszarze **katalog**wybierz dzierżawę usługi Azure AD, w której aplikacja zostanie zarejestrowana.
1. W obszarze nawigacji po lewej stronie wybierz pozycję **Azure Active Directory**.
1. W okienku **Azure Active Directory** wybierz pozycję **rejestracje aplikacji** w menu nawigacji po lewej stronie **Azure Active Directory** .
1. Utwórz lub wybierz aplikację, którą chcesz zarządzać. Musisz być **właścicielem** tej rejestracji aplikacji.
1. Na stronie **Przegląd** aplikacji postępuj zgodnie z **aplikacją zarządzaną w katalogu lokalnym** w obszarze podstawy w górnej części strony. Spowoduje to przejście do _aplikacji zarządzanej dla przedsiębiorstw_ na potrzeby rejestracji aplikacji.
1. W bloku nawigacji po lewej stronie wybierz pozycję **Właściwości**.
1. Znajdź **wymagane przypisanie użytkownika?** i ustaw wartość **tak**. Jeśli ta opcja jest ustawiona na **tak**, użytkownicy muszą najpierw zostać przypisani do tej aplikacji, zanim będą mieli do niej dostęp.
1. Wybierz pozycję **Zapisz** , aby zapisać tę zmianę konfiguracji.

## <a name="assign-users-and-groups-to-the-app"></a>Przypisywanie użytkowników i grup do aplikacji

Po skonfigurowaniu aplikacji do włączania przypisywania użytkowników można przypisywać użytkowników i grupy do aplikacji.

1. Wybierz okienko **Użytkownicy i grupy** w menu nawigacji po lewej stronie aplikacji.
1. W górnej części listy **Użytkownicy i grupy** wybierz przycisk **Dodaj użytkownika** , aby otworzyć okienko **Dodaj przypisanie** .
1. Wybierz selektor **użytkowników** w okienku **Dodaj przypisanie** . 

     Zostanie wyświetlona lista użytkowników i grup zabezpieczeń wraz z polem tekstowym umożliwiającym wyszukiwanie i lokalizowanie określonego użytkownika lub grupy. Ten ekran umożliwia wybranie wielu użytkowników i grup w jednym przejściu.

1. Po wybraniu opcji użytkownicy i grupy naciśnij przycisk **Wybierz** na dole, aby przejść do następnej części.
1. Naciśnij przycisk **Assign (Przypisz** ) u dołu, aby zakończyć przydziały użytkowników i grup do aplikacji. 
1. Upewnij się, że dodani Użytkownicy i grupy znajdują się na liście zaktualizowanych **użytkowników i grup** .

