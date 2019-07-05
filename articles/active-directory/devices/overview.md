---
title: Co to jest tożsamość urządzenia w usłudze Azure Active Directory?
description: Dowiedz się, jak Zarządzanie tożsamościami urządzeń może ułatwić Ci zarządzanie urządzeniami, które uzyskują dostęp do zasobów w danym środowisku.
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
ms.openlocfilehash: 5d38ca8bdf93ff201b3f5842f4cb0e8409dcd0c3
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481675"
---
# <a name="what-is-a-device-identity"></a>Co to jest tożsamość urządzenia?

Powszechne stosowanie urządzeń wszystkie kształty i rozmiarach oraz koncepcji urządzenia (PRZYNIEŚ własne) specjalistów IT muszą stawiać dwa nieco przeciwstawnym cele:

- Użytkownicy końcowi produktywności wszędzie tam, gdzie i kiedy
- Ochrona zasobów organizacji

Aby chronić te zasoby, personel IT muszą najpierw Zarządzanie tożsamościami urządzeń. Personel IT ją tworzyć przy użyciu tożsamości urządzenia za pomocą narzędzi, takich jak Microsoft Intune, aby upewnić się, że spełniono standardy zabezpieczeń i zgodności. Azure Active Directory (Azure AD) umożliwia logowanie jednokrotne do urządzeń, aplikacji i usług z dowolnego miejsca za pomocą tych urządzeń.

- Użytkownicy uzyskają dostęp do zasobów Twojej organizacji, które są im potrzebne. 
- Dział informatyczny Uzyskaj kontrolek, należy zabezpieczyć Twoją organizację.

Zarządzanie tożsamościami urządzeń jest podstawą dla [dostępu warunkowego opartego na urządzeniu](../conditional-access/require-managed-devices.md). Za pomocą zasad dostępu warunkowego opartego na urządzeniu można zagwarantować, że dostęp do zasobów w środowisku jest możliwe tylko przy użyciu zarządzanych urządzeń.

## <a name="getting-devices-in-azure-ad"></a>Pobieranie urządzenia w usłudze Azure AD

Aby uzyskać urządzenie w usłudze Azure AD, masz kilka opcji:

- **Zarejestrowana z usługi Azure AD**
   - Urządzenia, które są zarejestrowane z usługi Azure AD są zazwyczaj osobiście lub przenośnych urządzeń i zalogowano się do osobistego konta Microsoft lub innego konta lokalnego.
      - Windows 10
      - iOS
      - Android
      - MacOS
- **Dołączono do usługi Azure AD**
   - Urządzenia, które są dołączone do usługi Azure AD są własnością organizacji i zalogowano się przy użyciu konta usługi Azure AD należących do organizacji. Istnieją one tylko w chmurze.
      - Windows 10 
- **hybrydowym dołączonym do usługi Azure AD**.
   - Urządzenia, które są dołączone do usługi Azure AD hybrydowej własnością organizacji i zalogowano się przy użyciu konta usługi Azure AD należących do organizacji. Istnieją one w chmurze i 
      - Windows 7, 8.1 i 10
      - Windows Server 2008 lub nowszym

![Urządzenia wyświetlane w bloku urządzenia w usłudze Azure AD](./media/overview/azure-ad-devices-all-devices-overview.png)

## <a name="device-management"></a>Zarządzanie urządzeniami

Urządzenia w usłudze Azure AD mogą być zarządzane przy użyciu narzędzi zarządzania urządzeniami przenośnymi (MDM), takich jak Microsoft Intune, System Center Configuration Manager, zasad grupy (dołączenie do hybrydowej usługi Azure AD), narzędzia do zarządzania aplikacjami mobilnymi (MAM) lub innych narzędzi innych firm.

## <a name="resource-access"></a>Dostęp do zasobów

Rejestrowanie i przyłączanie zapewnić użytkownikom bezproblemowego logowania jednokrotnego (SSO) do chmury, zasoby i administratorów, możliwość stosowania zasad dostępu warunkowego dla tych zasobów. 

Urządzenia, które są dołączone do usługi Azure AD lub hybrydowej usługi Azure AD przyłączone do korzyści z logowania jednokrotnego do organizacji zasobów lokalnych oraz zasobów w chmurze. Więcej informacji można znaleźć w artykule [jak logowanie Jednokrotne do lokalnych zasobów działa w usłudze Azure AD urządzenia przyłączone do](azuread-join-sso.md).

## <a name="device-security"></a>Bezpieczeństwo urządzeń

- **Urządzeń zarejestrowanych w usłudze Azure AD** korzystanie z kontem zarządzanym przez użytkownika końcowego, to konto jest kontem Microsoft lub innego poświadczenia lokalnie zarządzanych zabezpieczony za pomocą co najmniej jeden z następujących czynności.
   - Hasło
   - NUMER PIN
   - Wzorce
   - Windows Hello
- **Przyłączone do usługi Azure AD lub urządzeń przyłączonych do hybrydowej usługi Azure AD** korzystanie z konta organizacyjnego w usłudze Azure AD zabezpieczony za pomocą co najmniej jeden z następujących czynności.
   - Hasło
   - Windows Hello dla firm

## <a name="provisioning"></a>Inicjowanie obsługi

Pobieranie urządzenia w usłudze Azure AD może odbywać się w sposób samoobsługi lub kontrolowanego proces inicjowania obsługi przez administratorów.

## <a name="summary"></a>Podsumowanie

Zarządzanie tożsamościami urządzeń w usłudze Azure AD możesz wykonywać następujące czynności:

- Upraszcza proces łączy i zarządzanie urządzeniami w usłudze Azure AD
- Zapewnienie użytkownikom łatwego dostępu do zasobów organizacji opartych na chmurze

## <a name="license-requirements"></a>Wymagania licencyjne

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [urządzeń zarejestrowanych w usłudze Azure AD](concept-azure-ad-register.md)
- Dowiedz się więcej o [urządzeń przyłączonych do usługi Azure AD](concept-azure-ad-join.md)
- Dowiedz się więcej o [urządzeń przyłączonych do hybrydowej usługi Azure AD](concept-azure-ad-join-hybrid.md)
- Aby uzyskać podstawowe informacje o Zarządzanie tożsamościami urządzeń w witrynie Azure portal, zobacz [Zarządzanie tożsamościami urządzeń przy użyciu witryny Azure portal](device-management-azure-portal.md).
- Aby dowiedzieć się więcej na temat dostępu warunkowego opartego na urządzeniach, zobacz [zasady dostępu warunkowego opartego na urządzeniu Azure skonfiguruj usługę Active Directory](../conditional-access/require-managed-devices.md).
