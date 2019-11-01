---
title: Co to jest tożsamość urządzenia w Azure Active Directory?
description: Dowiedz się, jak zarządzanie tożsamościami urządzeń może ułatwić zarządzanie urządzeniami, które uzyskują dostęp do zasobów w danym środowisku.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: overview
ms.date: 06/27/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07c1d6e3c622a76a1e625962ad5f08d414ad8cef
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175044"
---
# <a name="what-is-a-device-identity"></a>Co to jest tożsamość urządzenia?

Dzięki rozłącznym urządzeniom wszystkich kształtów i rozmiarów oraz koncepcji "Dobierz własne urządzenie" (BYOD), specjaliści IT są podłączeni do dwóch zamierzonych celów:

- Zezwalaj użytkownikom końcowym na produktywność wszędzie tam, gdzie i za każdym razem
- Ochrona zasobów organizacji

Aby chronić te zasoby, pracownicy działu IT muszą najpierw zarządzać tożsamościami urządzeń. Pracownicy IT mogą kompilować tożsamość urządzenia przy użyciu narzędzi, takich jak Microsoft Intune, aby zapewnić zgodność ze standardami bezpieczeństwa i zgodności. Usługa Azure Active Directory (Azure AD) umożliwia logowanie jednokrotne do urządzeń, aplikacji i usług z dowolnego miejsca za pośrednictwem tych urządzeń.

- Użytkownicy uzyskują dostęp do potrzebnych zasobów organizacji. 
- Pracownicy działu IT uzyskują kontrolki potrzebne do zabezpieczenia organizacji.

Zarządzanie tożsamościami urządzeń jest podstawą dla [dostępu warunkowego opartego na urządzeniach](../conditional-access/require-managed-devices.md). Za pomocą zasad dostępu warunkowego opartego na urządzeniach można zapewnić, że dostęp do zasobów w środowisku jest możliwy tylko przy użyciu zarządzanych urządzeń.

## <a name="getting-devices-in-azure-ad"></a>Pobieranie urządzeń w usłudze Azure AD

Aby uzyskać urządzenie w usłudze Azure AD, masz wiele opcji:

- **Zarejestrowane usługi Azure AD**
   - Urządzenia, które są zarejestrowane w usłudze Azure AD, są zazwyczaj urządzeniami prywatnymi lub przenośnymi i są zalogowane przy użyciu osobistego konto Microsoft lub innego konta lokalnego.
      - Windows 10
      - iOS
      - Android
      - MacOS
- **Przyłączone do usługi Azure AD**
   - Urządzenia, które są przyłączone do usługi Azure AD, należą do organizacji i są zalogowani do programu przy użyciu konta usługi Azure AD należącego do tej organizacji. Istnieją one tylko w chmurze.
      - Windows 10 
- **hybrydowym dołączonym do usługi Azure AD**.
   - Urządzenia, które są dołączone do hybrydowej usługi Azure AD, są własnością organizacji i są zalogowane do programu przy użyciu konta usługi Azure AD należącego do tej organizacji. Istnieją one w chmurze i lokalnie.
      - Windows 7, 8,1 lub 10
      - System Windows Server 2008 lub nowszy

![Urządzenia wyświetlane w bloku urządzenia usługi Azure AD](./media/overview/azure-ad-devices-all-devices-overview.png)

## <a name="device-management"></a>Zarządzanie urządzeniami

Urządzeniami w usłudze Azure AD można zarządzać przy użyciu narzędzi do zarządzania urządzeniami przenośnymi (MDM), takich jak Microsoft Intune, System Center Configuration Manager, zasady grupy (sprzężenie hybrydowe usługi Azure AD), narzędzi do zarządzania aplikacjami mobilnymi (MAM) lub innych narzędzi innych firm.

## <a name="resource-access"></a>Dostęp do zasobów

Zarejestrowanie i dołączanie umożliwia użytkownikom bezproblemowe logowanie się do zasobów w chmurze i administratorów możliwość stosowania zasad dostępu warunkowego do tych zasobów. 

Urządzenia, które są przyłączone do usługi Azure AD lub korzystają z hybrydowej usługi Azure AD z logowaniem jednokrotnym do zasobów lokalnych organizacji, a także zasobów w chmurze. Więcej informacji można znaleźć w artykule, w [jaki sposób logowanie JEDNOkrotne do zasobów lokalnych działa na urządzeniach przyłączonych do usługi Azure AD](azuread-join-sso.md).

## <a name="device-security"></a>Bezpieczeństwo urządzeń

- **Zarejestrowane urządzenia usługi Azure AD** wykorzystują konto zarządzane przez użytkownika końcowego, to konto jest konto Microsoft lub innym lokalnym zarządzanym poświadczeniami zabezpieczonymi przy użyciu co najmniej jednej z poniższych opcji.
   - Hasło
   - WEJŚCIE
   - Wzorce
   - Windows Hello
- **Przyłączone do usługi Azure AD lub hybrydowe urządzenia z usługą Azure AD** wykorzystują konto organizacyjne w usłudze Azure AD zabezpieczone przy użyciu co najmniej jednego z następujących elementów.
   - Hasło
   - Windows Hello dla firm

## <a name="provisioning"></a>Inicjowanie obsługi administracyjnej

Uzyskiwanie urządzeń w usłudze Azure AD może odbywać się w sposób samoobsługowy lub kontrolowany proces aprowizacji przez administratorów.

## <a name="summary"></a>Podsumowanie

Za pomocą zarządzania tożsamościami urządzeń w usłudze Azure AD można:

- Uproszczenie procesu przenoszenia urządzeń i zarządzania nimi w usłudze Azure AD
- Zapewnienie użytkownikom łatwego dostępu do zasobów organizacji opartych na chmurze

## <a name="license-requirements"></a>Wymagania licencyjne

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [zarejestrowanych urządzeniach usługi Azure AD](concept-azure-ad-register.md)
- Dowiedz się więcej o [urządzeniach przyłączonych do usługi Azure AD](concept-azure-ad-join.md)
- Dowiedz się więcej o [urządzeniach przyłączonych do hybrydowej usługi Azure AD](concept-azure-ad-join-hybrid.md)
- Aby dowiedzieć się, jak zarządzać tożsamościami urządzeń w Azure Portal, zobacz [Zarządzanie tożsamościami urządzeń przy użyciu Azure Portal](device-management-azure-portal.md).
- Aby dowiedzieć się więcej o dostępie warunkowym opartym na urządzeniach, zobacz [Konfigurowanie zasad dostępu warunkowego opartego na urządzeniach Azure Active Directory](../conditional-access/require-managed-devices.md).
