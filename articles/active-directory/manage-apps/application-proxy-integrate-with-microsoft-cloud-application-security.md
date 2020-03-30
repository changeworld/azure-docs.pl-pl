---
title: Integracja aplikacji lokalnych z usługą Cloud App Security — Azure AD
description: Skonfiguruj aplikację lokalną w usłudze Azure Active Directory do pracy z usługą Microsoft Cloud App Security (MCAS). Kontrola aplikacji dostępu warunkowego usługi MCAS służy do monitorowania i kontrolowania sesji w czasie rzeczywistym na podstawie zasad dostępu warunkowego. Te zasady można zastosować do aplikacji lokalnych korzystających z serwera proxy aplikacji w usłudze Azure Active Directory (Azure AD).
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb97f9dd87277215a5d4708d3a6f49564c490204
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275494"
---
# <a name="configure-real-time-application-access-monitoring-with-microsoft-cloud-app-security-and-azure-active-directory"></a>Konfigurowanie monitorowania dostępu do aplikacji w czasie rzeczywistym za pomocą usługi Microsoft Cloud App Security i usługi Azure Active Directory
Skonfiguruj aplikację lokalną w usłudze Azure Active Directory (Azure AD) do używania usługi Microsoft Cloud App Security (MCAS) do monitorowania w czasie rzeczywistym. McAS używa kontroli aplikacji dostępu warunkowego do monitorowania i kontrolowania sesji w czasie rzeczywistym na podstawie zasad dostępu warunkowego. Te zasady można zastosować do aplikacji lokalnych korzystających z serwera proxy aplikacji w usłudze Azure Active Directory (Azure AD).

Oto kilka przykładów typów zasad, które można utworzyć za pomocą usługi MCAS:

- Blokowanie lub ochrona pobierania poufnych dokumentów na urządzeniach niezarządzanych.
- Monitoruj, gdy użytkownicy wysokiego ryzyka logują się do aplikacji, a następnie rejestruj swoje akcje z poziomu sesji. Za pomocą tych informacji można analizować zachowanie użytkownika, aby określić sposób stosowania zasad sesji.
- Użyj certyfikatów klienta lub zgodności urządzeń, aby zablokować dostęp do określonych aplikacji z urządzeń niezarządzanych.
- Ogranicz sesje użytkowników z sieci innych niż firmowe. Możesz udzielić ograniczonego dostępu użytkownikom uzyskującym dostęp do aplikacji spoza sieci firmowej. Na przykład ten ograniczony dostęp może uniemożliwić użytkownikowi pobieranie poufnych dokumentów.

Aby uzyskać więcej informacji, zobacz [Ochrona aplikacji za pomocą kontroli aplikacji dostępu warunkowego zabezpieczeń w chmurze firmy Microsoft](/cloud-app-security/proxy-intro-aad).

## <a name="requirements"></a>Wymagania

Licencji:

- licencja EMS E5, lub 
- Autonomiczne usługi Azure Active Directory Premium P1 i MCAS Standalone.

Aplikacja lokalna:

- Aplikacja lokalna musi używać delegowania ograniczonego protokołu Kerberos (KCD)

Konfigurowanie serwera proxy aplikacji:

- Skonfiguruj usługę Azure AD do używania serwera proxy aplikacji, w tym przygotowywania środowiska i instalowania łącznika serwera proxy aplikacji. Aby zapoznać się z samouczkiem, zobacz [Dodawanie aplikacji lokalnych do zdalnego dostępu za pośrednictwem serwera proxy aplikacji w usłudze Azure AD](application-proxy-add-on-premises-application.md). 

## <a name="add-on-premises-application-to-azure-ad"></a>Dodawanie aplikacji lokalnej do usługi Azure AD

Dodaj aplikację lokalną do usługi Azure AD. Aby uzyskać szybki start, zobacz [Dodawanie aplikacji lokalnej do usługi Azure AD.](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) Podczas dodawania aplikacji należy ustawić następujące dwa ustawienia w bloku **Dodaj aplikację lokalną:**

- **Uwierzytelnianie wstępne:** Wprowadź **usługę Azure Active Directory**.
- **Tłumaczenie adresów URL w treści aplikacji**: Wybierz **tak**.

Te dwa ustawienia są wymagane dla aplikacji do pracy z mcas.

## <a name="test-the-on-premises-application"></a>Testowanie aplikacji lokalnej

Po dodaniu aplikacji do usługi Azure AD, należy wykonać kroki w [testowaniu aplikacji,](application-proxy-add-on-premises-application.md#test-the-application) aby dodać użytkownika do testowania i przetestować logowania. 

## <a name="deploy-conditional-access-app-control"></a>Wdrażanie kontroli aplikacji dostępu warunkowego

Aby skonfigurować aplikację za pomocą kontroli aplikacji dostępu warunkowego, postępuj zgodnie z instrukcjami w [obszarze Wdrażanie kontroli aplikacji dostępu warunkowego dla aplikacji usługi Azure AD.](/cloud-app-security/proxy-deployment-aad)


## <a name="test-conditional-access-app-control"></a>Testowanie kontroli aplikacji dostępu warunkowego

Aby przetestować wdrażanie aplikacji usługi Azure AD za pomocą kontroli aplikacji dostępu warunkowego, postępuj zgodnie z instrukcjami w [obszarze Testowanie wdrożenia aplikacji usługi Azure AD.](/cloud-app-security/proxy-deployment-aad)





