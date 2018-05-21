---
title: Pojedynczy zarządzania logowania jednokrotnego dla aplikacji przedsiębiorstwa w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Zarządzanie ustawieniami pojedynczego logowania jednokrotnego dla aplikacji przedsiębiorstwa w obrębie organizacji z galerii aplikacji usługi Azure Active Directory
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2017
ms.author: barbkess
ms.reviewer: asmalser
ms.openlocfilehash: 548bd4ca9e8ec66e4c50ea078dc0bccc1788095a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="managing-single-sign-on-for-enterprise-apps"></a>Zarządzanie logowanie jednokrotne dla aplikacji przedsiębiorstwa

W tym artykule opisano sposób użycia [portalu Azure](https://portal.azure.com) do zarządzania ustawieniami pojedynczego logowania jednokrotnego dla aplikacji przedsiębiorstwa. Enterprise aplikacje to aplikacje, które są wdrożone i używane w organizacji. Ten artykuł dotyczy szczególnie aplikacje, które zostały dodane z [galerii aplikacji usługi Azure Active Directory](manage-apps/what-is-single-sign-on.md#get-started-with-the-azure-ad-application-gallery). 

## <a name="finding-your-apps-in-the-portal"></a>Znajdowanie aplikacji w portalu
Wszystkie aplikacje firmowe, które są skonfigurowane dla logowania jednokrotnego, można wyświetlać i zarządzać w portalu Azure. Aplikacje można znaleźć w **wszystkie usługi** &gt; **aplikacje dla przedsiębiorstw** części portalu. 

![Blok aplikacje dla przedsiębiorstw](./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade.png)

Wybierz **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji, które zostały skonfigurowane. Wybieranie aplikacji Wyświetla zasoby dla danej aplikacji, w którym raporty można wyświetlać dla danej aplikacji i różne ustawienia mogą być zarządzane.

Aby zarządzać ustawienia rejestracji jednokrotnej, wybierz **logowanie jednokrotne**.

![Bloku zasobów aplikacji](./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-sso-blade.png)

## <a name="single-sign-on-modes"></a>Tryby rejestracji jednokrotnej
**Logowanie jednokrotne** rozpoczyna się od **tryb** menu, dzięki czemu jeden tryb logowania jednokrotnego do skonfigurowania. Dostępne opcje to:

* **Na podstawie SAML logowania jednokrotnego** — ta opcja jest dostępna, jeśli aplikacja obsługuje pełne federacyjnego logowania jednokrotnego w usłudze Azure Active Directory przy użyciu protokołu SAML 2.0, WS-Federation, lub OpenID connect protokołów.
* **Na podstawie hasła logowania jednokrotnego** — ta opcja jest dostępna, jeśli usługi Azure AD obsługuje formularz hasła wypełnianie dla tej aplikacji.
* **Połączone logowania jednokrotnego** — wcześniej znana jako "Istniejące rejestracji jednokrotnej", ta opcja umożliwia administratorom umieść link do tej aplikacji w ich użytkownik uruchamiający aplikację Panel dostępu usługi Azure AD lub usługi Office 365.

Aby uzyskać więcej informacji na temat trybów, zobacz [jak logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md#how-does-single-sign-on-with-azure-active-directory-work).

## <a name="saml-based-sign-on"></a>Na podstawie SAML logowania jednokrotnego
**Na języku SAML logowania jednokrotnego** opcji jest podzielony na cztery sekcje:

### <a name="domains-and-urls"></a>Domenach i adresach URL
Jest to, gdzie wszystkie szczegółowe informacje o domenie i adresy URL aplikacji są dodawane do katalogu usługi Azure AD. Są wyświetlane wszystkie dane wejściowe wymagane do przygotowania aplikacji do pracy rejestracji jednokrotnej bezpośrednio na ekranie, dlatego wszystkie dane wejściowe Opcjonalnie można wyświetlić, wybierając **Pokaż zaawansowane ustawienia adresu URL** wyboru. Zawiera pełną listę obsługiwanych dane wejściowe:

* **adres URL logowania na** — w przypadku, gdy użytkownik przechodzi do logowania do aplikacji. Jeśli aplikacja jest skonfigurowana do wykonywania inicjowane przez dostawcę usługi logowania jednokrotnego, jeśli zostanie otwarty ten adres URL, dostawcy usług wykonuje przekierowanie do usługi Azure AD do uwierzytelniania i zalogować użytkownika. 
  * Jeśli to pole zostanie wypełnione, następnie usługi Azure AD korzysta z adresu URL do uruchomienia aplikacji z usługi Office 365 i panelu dostępu usługi Azure AD.
  * Jeśli to pole jest pominięty, następnie usługi Azure AD w zamian wykonuje inicjowane przez dostawcę tożsamości logowania jednokrotnego, gdy aplikacja jest uruchamiana z usługi Office 365, Panel dostępu usługi Azure AD lub z usługi Azure AD pojedynczego logowania jednokrotnego adresu URL.
* **Identyfikator** — ten identyfikator URI musi jednoznacznie wskazywać aplikacji, dla którego rejestracji jednokrotnej jest konfigurowany. Jest to wartość, która wysyła usługi Azure AD z powrotem do aplikacji jako parametr odbiorców tokenu SAML i aplikacji powinien go zweryfikować. Ta wartość jest także wyświetlany jako identyfikator obiektu w dowolnym metadanych SAML udostępniany przez aplikację.
* **Adres URL odpowiedzi** — adres URL odpowiedzi jest, gdy aplikacja oczekuje odebrać tokenu SAML. Jest to także określane jako adres URL usługi konsumenta potwierdzenia (ACS). Po te zostały wprowadzone, kliknij przycisk Dalej, aby przejść do następnego ekranu. Ten ekran informuje o co musi zostać skonfigurowane na stronie aplikacji do akceptowania tokenu SAML z usługi Azure AD.
* **Stan przekazywania** — stan przekazywania jest opcjonalnym parametrem, który może pomóc informujące aplikacji, gdzie przekieruje użytkownika, po zakończeniu uwierzytelniania. Zwykle jest to wartość prawidłowy adres URL w aplikacji, jednak niektóre aplikacje używają tego pola inaczej (zobacz aplikacji pojedynczego logowania jednokrotnego dokumentację, aby uzyskać szczegółowe informacje). Aby ustawić stan przekazywania jest nowa funkcja, która jest unikatowa dla nowego portalu Azure.

### <a name="user-attributes"></a>Atrybuty użytkownika
Jest to, gdzie Administratorzy można wyświetlać i edytować atrybuty, które zostały wysłane w tokenie SAML, który usługi Azure AD wystawia aplikacji każdego logowania użytkownika.

Atrybut tylko edytowalny, obsługiwane jest **identyfikator użytkownika** atrybutu. Wartość tego atrybutu jest polem w usłudze Azure AD, który unikatowo identyfikuje każdego użytkownika w aplikacji. Na przykład jeśli aplikacja została wdrożona przy użyciu "Adres E-mail" jako nazwy użytkownika i unikatowym identyfikatorem, następnie będzie można ustawić wartości do pola "user.mail" w usłudze Azure AD.

### <a name="saml-signing-certificate"></a>Certyfikat podpisywania SAML
W tej sekcji przedstawiono szczegóły certyfikatu, który używa usługi Azure AD do podpisywania tokeny SAML, które są wysyłane do aplikacji, w każdym razem, użytkownik jest uwierzytelniany. Umożliwia on właściwości bieżącego certyfikatu, aby sprawdził, łącznie z datą wygaśnięcia.

### <a name="application-configuration"></a>Konfiguracja aplikacji
Ostatnia zawiera dokumentacja i/lub kontrolki, należy skonfigurować aplikację do użycia usługi Azure Active Directory jako dostawcy tożsamości.

**Konfiguruj aplikację** menu wysuwanego udostępnia nowe zwięzła, osadzone instrukcje dotyczące konfigurowania aplikacji. Jest to kolejną nową funkcją unikatowe dla nowego portalu Azure.

> [!NOTE]
> Pełny przykład osadzonych dokumentacji Zobacz aplikacji witryny Salesforce.com. Dodatkowe aplikacje w dokumentacji jest stale dodawane.
> 
> 

![Osadzony dokumentów](./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-embedded-docs.png)

## <a name="password-based-sign-on"></a>Na podstawie hasła logowania jednokrotnego
Jeśli jest obsługiwany dla aplikacji, wybierając tryb logowania jednokrotnego opartego na hasłach i wybierając **zapisać** natychmiast konfiguruje wykonania opartego na hasłach logowania jednokrotnego. Aby uzyskać więcej informacji na temat wdrażania opartego na hasłach logowania jednokrotnego, zobacz [jak logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md#how-does-single-sign-on-with-azure-active-directory-work).

![Na podstawie hasła logowania jednokrotnego](./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-password-sso.png)

## <a name="linked-sign-on"></a>Połączonego logowania jednokrotnego
Jeśli obsługiwane w przypadku aplikacji, wybierając tryb połączonego logowania jednokrotnego pozwala na wprowadź adres URL, który ma być Panel dostępu usługi Azure AD lub usługi Office 365 przekierowania po kliknięciu tej aplikacji. Aby uzyskać więcej informacji na temat połączonego logowania jednokrotnego (wcześniej znane jako "istniejące SSO"), zobacz [jak logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md#how-does-single-sign-on-with-azure-active-directory-work).

![Połączonego logowania jednokrotnego](./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-linked-sso.png)

## <a name="feedback"></a>Opinia

Mamy nadzieję przy użyciu udoskonalone środowisko usługi Azure AD. Pamiętaj o opinie przesyłanych! Opinie i pomysły dotyczące poprawy **portalu administracyjnego** sekcji naszych [forum opinii](https://feedback.azure.com/forums/169401-azure-active-directory/category/162510-admin-portal).  Firma Microsoft jest podekscytowani, informacje o kompilowaniu chłodnych nowości codziennie i użyj wskazówek z kształtem i zdefiniować, co mamy utworzyć w następnej kolejności.

