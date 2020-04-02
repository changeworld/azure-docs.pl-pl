---
title: Wtyczka SSO firmy Microsoft Enterprise dla urządzeń Apple
titleSuffix: Microsoft identity platform | Azure
description: Dowiedz się więcej o usłudze SSO usługi Azure Active Directory firmy Microsoft dla urządzeń z systemem iOS i macOS.
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 7233a0bba5cf30eab018bfa744a7322303300604
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550320"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Wtyczka SSO firmy Microsoft Enterprise dla urządzeń Apple (wersja zapoznawcza)

> [!NOTE]
> Ta funkcja jest dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

*Wtyczka logowania jednokrotnego firmy Microsoft Enterprise dla urządzeń Apple* zapewnia logowanie jednokrotne (Logowanie jednokrotne) dla kont usługi Active Directory we wszystkich aplikacjach obsługujących funkcję [jednokrotnego logowania](https://developer.apple.com/documentation/authenticationservices) firmy Apple Enterprise. Firma Microsoft ściśle współpracowała z firmą Apple w celu opracowania tej wtyczki w celu zwiększenia użyteczności aplikacji, zapewniając jednocześnie najlepszą ochronę, jaką mogą zapewnić Apple i Microsoft.

W tej wersji publicznej wersji zapoznawczej wtyczka SSO przedsiębiorstwa jest dostępna tylko dla urządzeń z systemem iOS i jest rozpowszechniana w niektórych aplikacjach firmy Microsoft.

Pierwsze zastosowanie wtyczki SSO enterprise odbywa się za pomocą naszej nowej funkcji [trybu współdzielonego urządzenia.](msal-ios-shared-devices.md)

## <a name="features"></a>Funkcje

Wtyczka SSO firmy Microsoft Enterprise dla urządzeń Apple oferuje następujące korzyści:

- Zapewnia logowanie jednokrotne dla kont usługi Active Directory we wszystkich aplikacjach obsługujących funkcję rejestracji jednokrotnej firmy Apple Enterprise.
- Dostarczane automatycznie w programie Microsoft Authenticator i mogą być włączane przez dowolne rozwiązanie do zarządzania urządzeniami przenośnymi (MDM).

## <a name="requirements"></a>Wymagania

Aby użyć wtyczki SSO firmy Microsoft Enterprise dla urządzeń Apple:

- System iOS 13.0 lub nowszy musi być zainstalowany na urządzeniu.
- Aplikacja firmy Microsoft, która udostępnia wtyczkę SSO firmy Microsoft Enterprise dla urządzeń Apple, musi być zainstalowana na urządzeniu. W przypadku publicznej wersji zapoznawczej aplikacje te obejmują [aplikację Microsoft Authenticator](../user-help/user-help-auth-app-overview.md).
- Urządzenie musi być zarejestrowane w usłudze MDM (na przykład w usłudze Microsoft Intune).
- Konfiguracja musi zostać wypchnięta do urządzenia, aby włączyć wtyczkę SSO firmy Microsoft Enterprise dla urządzeń Apple na urządzeniu. To ograniczenie zabezpieczeń jest wymagane przez apple.

## <a name="enable-the-sso-extension-with-mobile-device-management-mdm"></a>Włącz rozszerzenie rejestracji rejestracji w tym celu za pomocą zarządzania urządzeniami przenośnymi (MDM)

Aby włączyć wtyczkę SSO firmy Microsoft Enterprise dla urządzeń Apple, urządzenia muszą być wysyłane sygnał za pośrednictwem usługi MDM. Ponieważ firma Microsoft zawiera wtyczkę SSO przedsiębiorstwa w [aplikacji Microsoft Authenticator,](..//user-help/user-help-auth-app-overview.md)użyj urządzenia MDM, aby skonfigurować aplikację, aby włączyć wtyczkę microsoft enterprise sso.

Użyj następujących parametrów, aby skonfigurować wtyczkę SSO firmy Microsoft Enterprise dla urządzeń Apple:

- **Typ**: Przekierowanie
- **Identyfikator rozszerzenia:**`com.microsoft.azureauthenticator.ssoextension`
- **Identyfikator zespołu**:`SGGM6D27TK`
- **Adresy URL**:
  - `https://login.microsoftonline.com`
  - `https://login.windows.net`
  - `https://login.microsoft.com`
  - `https://sts.windows.net`
  - `https://login.partner.microsoftonline.cn`
  - `https://login.chinacloudapi.cn`
  - `https://login.microsoftonline.de`
  - `https://login.microsoftonline.us`
  - `https://login.usgovcloudapi.net`
  - `https://login-us.microsoftonline.com`

Usługi Microsoft Intune można używać jako usługi MDM w celu ułatwienia konfiguracji wtyczki SSO firmy Microsoft Enterprise. Aby uzyskać więcej informacji, zobacz [dokumentację konfiguracji usługi Intune](https://docs.microsoft.com/intune/configuration/ios-device-features-settings).

## <a name="using-the-sso-extension-in-your-application"></a>Korzystanie z rozszerzenia SSO w aplikacji

[Biblioteka uwierzytelniania firmy Microsoft (MSAL) dla urządzeń Apple](https://github.com/AzureAD/microsoft-authentication-library-for-objc) w wersji 1.1.0 lub nowszej obsługuje wtyczkę SSO firmy Microsoft Enterprise dla urządzeń Apple.

Jeśli chcesz obsługiwać tryb urządzenia udostępnionego dostarczony przez wtyczkę SSO firmy Microsoft Enterprise dla urządzeń Apple, upewnij się, że aplikacje używają określonej minimalnej wymaganej wersji usługi MSAL.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat trybu udostępnionego urządzenia w systemach iOS, zobacz [Tryb urządzenia udostępnionego dla urządzeń z systemem iOS](msal-ios-shared-devices.md).
