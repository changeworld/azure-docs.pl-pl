---
title: Znakowanie wytyczne dotyczące aplikacji | Dokumentacja firmy Microsoft
description: Kompletny przewodnik dewelopera dotyczący zasobów dla usługi Azure Active Directory
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 72f4e464-1352-4a49-a18f-c37f58e7d5c4
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: celested
ms.reviewer: arielgo, skwan
ms.custom: aaddev
ms.openlocfilehash: 97ae16e21e0771c89b1c878c3cabaeab25bcee4b
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2018
ms.locfileid: "36318606"
---
# <a name="branding-guidelines-for-applications"></a>Znakowanie wytyczne dotyczące aplikacji

W tym artykule omówiono znakowania wskazówki, które powinny być używane podczas tworzenia aplikacji w usłudze Azure Active Directory (Azure AD). Te wskazówki pomogą, bezpośrednie klienci chcą używać swojego konta firmowego lub szkolnego, zarządzane w usłudze Azure AD, lub osobistego konta dla rejestracji i logowania do aplikacji.

## <a name="personal-accounts-vs-work-or-school-accounts-from-microsoft"></a>Kont osobistych a pracy lub szkoły kont firmy Microsoft

Firma Microsoft zarządza dwa rodzaje kont użytkowników:

* **Konta osobiste** (znanego wcześniej jako identyfikator Windows Live ID). Te konta reprezentowania relacji między *poszczególnych* użytkowników i firmy Microsoft i są używane do dostępu do urządzeń konsumenckich i usług firmy Microsoft. Te konta są przeznaczone do użytku osobistego.
* **Konta służbowe.** Te konta są zarządzane przez firmę Microsoft w imieniu organizacji, które używają usługi Azure Active Directory. Te konta są używane do logowania do usługi Office 365 i innych usług biznesowych firmy Microsoft.

Microsoft, które konta służbowe zwykle są przypisywane do użytkowników końcowych (pracowników, studentów, pracowników federalnych) przez organizacji (firmy, szkoły, agencji rządowych Stanów Zjednoczonych). Te konta zarządzanych bezpośrednio w chmurze (na platformie Azure AD) lub synchronizowane z usługą Azure AD z katalogiem lokalnym, takie jak Windows Server Active Directory. Firma Microsoft *niejawnego* pracy lub szkoły konta, ale konta są własnością i kontrolowane przez organizację.

## <a name="referring-to-azure-ad-accounts-in-your-application"></a>Odwołanie do konta usługi Azure AD w aplikacji

Microsoft nie zapewnia użytkownikom końcowym platformy Azure lub nazw markami usługi Active Directory i nie należy użytkownik.

* Po zalogowaniu użytkownicy Użyj możliwie logo i nazwę organizacji. To jest lepszym rozwiązaniem niż przy użyciu ogólnych warunków, takich jak "Twojej organizacji."
* Gdy użytkownicy nie są zalogowani, dotyczą kont jako "pracy lub kont służbowych" i Użyj logo firmy Microsoft do przekazania, że firma Microsoft zarządza tych kont. Nie używaj warunków, takich jak "enterprise konto", "konto firmowe" lub "konto firmowe", co utworzenie użytkownika pomyłek.

## <a name="user-account-pictogram"></a>Piktogram konta użytkownika

We wcześniejszej wersji z tymi wytycznymi zaleca się przy użyciu piktogramu "badge niebieski". Na podstawie opinii użytkowników i deweloperów, teraz zalecamy użycie logo firmy Microsoft zamiast tego. Logo firmy Microsoft pomoże użytkownikom zrozumienie, że może ponownie użyć konta, które korzystają z usługi Office 365 lub Microsoft innych usług biznesowych do logowania się do aplikacji.

## <a name="signing-up-and-signing-in-with-azure-ad"></a>Rejestracji i logowania przy użyciu usługi Azure AD

Aplikacja może stanowić oddzielne ścieżki rejestracji i logowania i w poniższych sekcjach visual wskazówki dotyczące oba scenariusze.

**Jeśli aplikacja obsługuje użytkownika końcowego rejestracji (na przykład bezpłatnej wersji próbnej lub freemium modelu)**: można wyświetlić **logowania** przycisku, który umożliwia użytkownikom uzyskiwanie dostępu do aplikacji za pomocą konta służbowego lub osobistego konta. Usługi Azure AD wyświetli monit o zgodę uzyskują oni dostęp do aplikacji po raz pierwszy.

**Jeśli aplikacja wymaga uprawnień, które tylko administratorzy mogą wyrazić zgodę na, czy aplikacja wymaga licencjonowania w organizacji**: oddzielne nabycia administratora z logowaniem użytkownika. **Przycisk "Pobierz tę aplikację"** przekieruje administratorom Zaloguj się, a następnie zadać je, aby udzielić zgody w imieniu użytkowników w organizacji, którego dodatkowa korzyść z pominięciem monitami zgody użytkownika końcowego do aplikacji.

## <a name="visual-guidance-for-app-acquisition"></a>Visual wskazówki dotyczące pozyskiwania aplikacji

Łącze "Pobierz aplikację" musi przekierowuje użytkownika do usługi Azure AD Udziel dostępu (autoryzować) strony, aby umożliwić administratorowi organizacji zezwolić aplikacji na dostęp do danych używanych w organizacji, która jest hostowana przez firmę Microsoft. Więcej informacji na temat żądania dostępu zostały omówione w [integracji aplikacji z usługą Azure Active Directory](active-directory-integrating-applications.md) artykułu.

Po Administratorzy wyrażenia zgody na aplikacji, można wybrać ją dodać do środowiska uruchamiania aplikacji usługi Office 365 swoich użytkowników (dostępny z waffle i [ https://portal.office.com/myapps ](https://portal.office.com/myapps)). Anonsowanie tej funkcji, można użyć warunków, takich jak "Dodawanie tej aplikacji do organizacji" Pokaż przycisk poniższy przykład, takich jak:

![Typy aplikacji i scenariusze](./media/active-directory-branding-guidelines/add-to-my-org.png)

Jednak zaleca się, czy zapisać tekst objaśnienia zamiast polegania na przyciskach. Na przykład:

> *Jeśli już używasz usługi Office 365 lub innych usług biznesowych firmy Microsoft, mogą udzielać dostępu < your_app_name > do danych organizacji. Umożliwi to użytkownikom dostęp < your_app_name > przy użyciu istniejącego konta służbowego.*

Aby pobrać oficjalny logo firmy Microsoft do użycia w aplikacji, kliknij prawym przyciskiem myszy ten, który chcesz wykorzystać, a następnie zapisz go na komputerze.

| Zasób                                | PNG format | SVG format |
| ------------------------------------ | ---------- | ---------- |
| Logo firmy Microsoft  | ![Logo firmy Microsoft PNG](./media/active-directory-branding-guidelines/MS-SymbolLockup_MSSymbol_19.png) | ![Logo firmy Microsoft SVG](./media/active-directory-branding-guidelines/MS-SymbolLockup_MSSymbol_19.svg) |

## <a name="visual-guidance-for-sign-in"></a>Visual dotyczące logowania

Aplikacji powinien być wyświetlany przycisk logowania, który przekierowuje użytkowników do logowania punktu końcowego, który odpowiada protokołu, który umożliwia integrację z usługą Azure AD. Poniższa sekcja zawiera szczegółowe informacje na jak powinna wyglądać tego przycisku.

### <a name="pictogram-and-sign-in-with-microsoft"></a>Piktogram i "Zaloguj się przy użyciu Microsoft"

To skojarzenie logo firmy Microsoft i warunki "Logowania w with Microsoft" jednoznacznie reprezentujących usługi Azure AD wśród innych dostawców tożsamości aplikacji może obsługiwać. Jeśli nie ma wystarczającej ilości miejsca dla "Logowania w with Microsoft", to przycisk ok, aby zmniejszyć jego "Logowania." Schemat kolorów jasny lub ciemny służy przycisków.

Poniższy diagram przedstawia zalecanymi przez firmę Microsoft redlines podczas korzystania z zasobów z aplikacji. Redlines dotyczą "Logowania w with Microsoft" lub krótsza wersja "Zaloguj".

![Zaloguj się przy użyciu Microsoft redlines](./media/active-directory-branding-guidelines/Sign-in-with-Microsoft-redlines.png)

Aby pobrać oficjalny obrazów do wykorzystania w aplikacji, kliknij prawym przyciskiem myszy ten, który chcesz wykorzystać, a następnie zapisz go na komputerze.

| Zasób                                | PNG format | SVG format |
| ------------------------------------ | ---------- | ---------- |
| Zaloguj się przy użyciu programu Microsoft (ciemny motyw)  | ![Zaloguj się przycisk ciemny motyw PNG](./media/active-directory-branding-guidelines/MS-SymbolLockup_SignIn_dark.png) | ![Zaloguj się przy użyciu Microsoft przycisk ciemny motyw SVG](./media/active-directory-branding-guidelines/MS-SymbolLockup_SignIn_dark.svg) |
| Zaloguj się przy użyciu programu Microsoft (motywu jasny) | ![Zaloguj się przycisk motywu jasny PNG](./media/active-directory-branding-guidelines/MS-SymbolLockup_SignIn_light.png) | ![Zaloguj się przy użyciu motywu jasny przycisk Microsoft SVG](./media/active-directory-branding-guidelines/MS-SymbolLockup_SignIn_light.svg) |
| Zaloguj się (ciemny motyw)                 | ![Zaloguj się w skrócie motywu ciemny przycisk PNG](./media/active-directory-branding-guidelines/MS-SymbolLockup_SignIn_dark_short.png) | ![Zaloguj się w skrócie przycisk ciemny motyw SVG](./media/active-directory-branding-guidelines/MS-SymbolLockup_SignIn_dark_short.svg) |
| Zaloguj się (motywu jasny)                | ![Zaloguj się w skrócie motywu jasny przycisk PNG](./media/active-directory-branding-guidelines/MS-SymbolLockup_SignIn_light_short.png) | ![Zaloguj się w skrócie motywu jasny przycisk SVG](./media/active-directory-branding-guidelines/MS-SymbolLockup_SignIn_light_short.svg) |


## <a name="branding-dos-and-donts"></a>Znakowanie porady

**CZY** Użyj "konto służbowe" w połączeniu z przycisku "Logowania w with Microsoft" zapewnienie dodatkowych wyjaśnienie, aby ułatwić użytkownikom końcowym rozpoznaje, czy mogą go używać. **Nie** używać inne postanowienia, takie jak "enterprise konto", "konto firmowe" lub "konta firmowego."

**Nie** Użyj "Office 365 ID" lub "Azure ID." Usługi Office 365 jest również nazwa konsumenta produkt firmy Microsoft, które nie są używane do uwierzytelniania usługi Azure AD.

**Nie** zmienić logo firmy Microsoft.

**Nie** udostępnić użytkownikom końcowym marek Azure lub usługi Active Directory. Ok jest jednak używać tych warunków z deweloperów, specjalistów IT i administratorów.

## <a name="navigation-dos-and-donts"></a>Porady nawigacji

**CZY** umożliwiają użytkownikom Wyloguj się i Przełącz na inne konto użytkownika. Chociaż większość osób jednego konta osobistego od firmy Microsoft/Facebook/Google/Twitter, użytkownicy często są skojarzone z więcej niż jednej z organizacji. Obsługa wielu zalogowanych użytkowników będzie dostępna wkrótce.
