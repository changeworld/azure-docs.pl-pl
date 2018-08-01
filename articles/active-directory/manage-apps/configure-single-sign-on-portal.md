---
title: Single Zarządzanie logowania jednokrotnego dla aplikacji przedsiębiorstwa w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Zarządzanie ustawieniami pojedynczego logowania jednokrotnego dla aplikacji korporacyjnych w organizacji z galerii aplikacji usługi Azure Active Directory
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2017
ms.author: barbkess
ms.reviewer: asmalser
ms.openlocfilehash: 81b959a08f55f13fd0bcadce32b65ba64f9bb270
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2018
ms.locfileid: "39365495"
---
# <a name="managing-single-sign-on-for-enterprise-apps"></a>Zarządzanie logowania jednokrotnego dla aplikacji przedsiębiorstwa

W tym artykule opisano sposób używania [witryny Azure portal](https://portal.azure.com) do zarządzania ustawieniami pojedynczego logowania jednokrotnego dla aplikacji korporacyjnych. Aplikacje dla przedsiębiorstw to aplikacje, które są wdrażane i używane w organizacji. W tym artykule mają zastosowanie szczególnie do aplikacji, które zostały dodane od [galerii aplikacji usługi Azure Active Directory](what-is-single-sign-on.md#get-started-with-the-azure-ad-application-gallery). 

## <a name="finding-your-apps-in-the-portal"></a>Znajdowanie aplikacji w portalu
Można wyświetlać wszystkie aplikacje przedsiębiorstwa, które są skonfigurowane dla logowania jednokrotnego i zarządzane w witrynie Azure portal. Aplikacje można znaleźć w **wszystkich usług** &gt; **aplikacje dla przedsiębiorstw** sekcji w portalu. 

![Bloku aplikacje w przedsiębiorstwie](./media/configure-single-sign-on-portal/enterprise-apps-blade.png)

Wybierz **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji, które zostały skonfigurowane. Wybieranie aplikacji Wyświetla zasoby dla tej aplikacji, w którym raporty mogą być wyświetlane dla tej aplikacji i różne ustawienia, które mogą być zarządzane.

Aby zarządzać ustawieniami rejestracji jednokrotnej, wybierz **logowanie jednokrotne**.

![Bloku zasobów aplikacji](./media/configure-single-sign-on-portal/enterprise-apps-sso-blade.png)

## <a name="single-sign-on-modes"></a>Tryby rejestracji jednokrotnej
**Logowanie jednokrotne** zaczyna się od **tryb** menu, które umożliwia pojedynczego tryb logowania jednokrotnego należy skonfigurować. Dostępne opcje to:

* **Logowanie na podstawie protokołu SAML** — ta opcja jest dostępna, jeśli aplikacja obsługuje pełne federacyjnego logowania jednokrotnego w usłudze Azure Active Directory przy użyciu protokołu SAML 2.0, WS-Federation, lub OpenID connect protokołów.
* **Logowanie na podstawie hasła** — ta opcja jest dostępna, jeśli usługa Azure AD obsługuje formularz hasła wypełnianie dla tej aplikacji.
* **Połączone logowanie jednokrotne** -znana wcześniej jako "Istniejące logowanie jednokrotne", ta opcja umożliwia administratorom umieścić łącze do tej aplikacji w swoich użytkowników panelu dostępu usługi Azure AD lub Office 365 module uruchamiania aplikacji.

Aby uzyskać więcej informacji dotyczących tych trybów, zobacz [jak logowanie jednokrotne z usługą Azure Active Directory](what-is-single-sign-on.md#how-does-single-sign-on-with-azure-active-directory-work).

## <a name="saml-based-sign-on"></a>Logowanie na podstawie protokołu SAML
**Opartej na SAML logowania jednokrotnego** opcji są podzielone na cztery sekcje:

### <a name="domains-and-urls"></a>Domen i adresów URL
Jest to, gdzie wszystkie szczegółowe informacje o aplikacji domena i adresy URL są dodawane do katalogu usługi Azure AD. Wszystkie dane wejściowe wymagane do przygotowania pojedynczego logowania aplikacji są wyświetlane bezpośrednio na ekranie, natomiast wszystkich danych wejściowych opcjonalnych można wyświetlić, wybierając **Pokaż zaawansowane ustawienia adresu URL** pola wyboru. Zawiera pełną listę obsługiwanych danych wejściowych:

* **adres URL logowania** — w przypadku, gdy użytkownik przechodzi do logowania do aplikacji. Jeśli aplikacja jest skonfigurowana do wykonywania zainicjowana przez dostawcę usługi logowania jednokrotnego, gdy użytkownik otwiera ten adres URL, dostawcy usług wykonuje przekierowanie do usługi Azure AD do uwierzytelniania i zalogować użytkownika. 
  * Jeśli to pole zostanie wypełnione, następnie usługi Azure AD używa adresu URL do uruchomienia aplikacji z usługi Office 365 i stronie panelu dostępu usługi Azure AD.
  * Jeśli to pole zostanie pominięty, następnie usługi Azure AD zamiast tego wykonuje zainicjowana przez dostawcę tożsamości logowania jednokrotnego, gdy aplikacja jest uruchamiana z usługi Office 365, panelu dostępu usługi Azure AD lub z usługi Azure AD pojedynczy adres URL logowania.
* **Identyfikator** — ten identyfikator URI musi jednoznacznie wskazywać na aplikację, dla których logowanie jednokrotne jest konfigurowane. Jest to wartość, która usługi Azure AD wysyła z powrotem do aplikacji jako parametr odbiorców tokenu SAML, a aplikacja oczekuje się, aby zweryfikować, czy. Ta wartość pojawia się również jako identyfikator jednostki w żadnych metadanych SAML udostępniany przez aplikację.
* **Adres URL odpowiedzi** — adres URL odpowiedzi to, gdzie aplikacja oczekuje otrzymać SAML token. To również jest określane jako adresu URL asercji konsumenta Service (ACS). Po te zostały wprowadzone, kliknij przycisk Dalej, aby przejść do następnego ekranu. Ten ekran zawiera informacje o czym należy skonfigurować na stronie aplikacji, aby umożliwić jej do akceptowania tokenu SAML z usługi Azure AD.
* **Stan przekazywania** — stan usługi relay jest opcjonalnym parametrem, który może pomóc informujące aplikacji, gdzie do przekierowania użytkownika po zakończeniu uwierzytelniania. Zazwyczaj wartość jest prawidłowym adresem URL w aplikacji, jednak niektóre aplikacje używają tego pola w inny sposób (patrz aplikacji pojedynczego logowania jednokrotnego szczegóły w dokumentacji). Możliwość ustawienia stanu usługi relay jest nową funkcję, która jest unikatowa dla nowej witryny Azure portal.

### <a name="user-attributes"></a>Atrybuty użytkownika
Jest to, gdzie administratorzy mogą wyświetlać i edytować atrybuty, które będą wysyłane w tokenie SAML, wysyłający w usłudze Azure AD do aplikacji każdego logowania użytkownika.

Atrybut wyłącznie edytowalne, obsługiwane jest **identyfikator użytkownika** atrybutu. Wartość tego atrybutu jest pole w usłudze Azure AD, który unikatowo identyfikuje każdy użytkownik w aplikacji. Na przykład jeśli aplikacja została wdrożona przy użyciu "Adres E-mail" jako nazwy użytkownika i unikatowy identyfikator, następnie wartość będzie miał ustawienie pola "user.mail" w usłudze Azure AD.

### <a name="saml-signing-certificate"></a>Certyfikat podpisywania SAML
W tej sekcji przedstawiono szczegółowe informacje o certyfikat, który używa usługi Azure AD, aby zarejestrować tokeny SAML, które są wysyłane do aplikacji, w każdym razem, użytkownik jest uwierzytelniany. Umożliwia ona właściwości bieżącego certyfikatu, aby sprawdził, łącznie z datą wygaśnięcia.

### <a name="application-configuration"></a>Konfiguracja aplikacji
W końcowej sekcji przedstawiono dokumentację i/lub kontrolki, wymagane do skonfigurowania aplikacji do użycia usługi Azure Active Directory jako dostawcy tożsamości.

**Konfiguruj aplikację** menu wysuwanego udostępnia nowe zwięzły, skierowany do osadzonych instrukcje dotyczące konfigurowania aplikacji. To jest kolejną nową funkcją unikatowe dla nowej witryny Azure portal.

> [!NOTE]
> Pełny przykład dokumentacja embedded Zobacz aplikacji Salesforce.com. Dokumentacja dla dodatkowych aplikacji jest dodawana stale.
> 
> 

![Dokumentacja osadzonego](./media/configure-single-sign-on-portal/enterprise-apps-blade-embedded-docs.png)

## <a name="password-based-sign-on"></a>Logowanie na podstawie hasła
Jeśli jest obsługiwany dla aplikacji, wybierając tryb logowania jednokrotnego opartego na hasłach i wybierając **Zapisz** natychmiast konfiguruje mu SSO oparte na hasłach. Aby uzyskać więcej informacji na temat wdrażania opartego na hasłach logowania jednokrotnego zobacz [jak logowanie jednokrotne z usługą Azure Active Directory](what-is-single-sign-on.md#how-does-single-sign-on-with-azure-active-directory-work).

![Logowanie na podstawie hasła](./media/configure-single-sign-on-portal/enterprise-apps-blade-password-sso.png)

## <a name="linked-sign-on"></a>Połączone logowanie
Jeśli jest obsługiwany dla aplikacji, wybierając połączone trybu logowania jednokrotnego umożliwia wprowadź adres URL, którego chcesz przekierować do, gdy użytkownik kliknie w tej aplikacji Panel dostępu usługi Azure AD lub usługi Office 365. Aby uzyskać więcej informacji na temat połączonego logowania jednokrotnego (dawniej "SSO istniejących"), zobacz [jak logowanie jednokrotne z usługą Azure Active Directory](what-is-single-sign-on.md#how-does-single-sign-on-with-azure-active-directory-work).

![Połączone logowanie](./media/configure-single-sign-on-portal/enterprise-apps-blade-linked-sso.png)

## <a name="feedback"></a>Opinia

Mamy nadzieję, że możesz takimi jak wymaganie użycia ulepszone środowisko usługi Azure AD. Pamiętaj o opinie dostępne! Opinie i pomysły dotyczące poprawy **portalu administracyjnego** części naszych [forum z opiniami](https://feedback.azure.com/forums/169401-azure-active-directory/category/162510-admin-portal).  Firma Microsoft jest podekscytowani o tworzeniu nowego ciekawostki codziennie, użyj wskazówek z kształtem i zdefiniować, co mamy utworzyć w następnej kolejności.

