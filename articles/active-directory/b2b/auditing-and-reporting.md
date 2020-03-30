---
title: Inspekcja i raportowanie użytkownika współpracy B2B — Usługa Azure AD
description: Właściwości użytkownika gościa można konfigurować we współpracy usługi Azure Active Directory B2B
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74d2473d37dcf0d435ab61763e72a64111845956
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74273297"
---
# <a name="auditing-and-reporting-a-b2b-collaboration-user"></a>Inspekcja i raportowanie użytkownika współpracy B2B
W przypadku użytkowników-gości masz funkcje inspekcji podobne do użytkowników elementów członkowskich. 

## <a name="access-reviews"></a>Przeglądy dostępu
Za pomocą recenzji dostępu można okresowo sprawdzać, czy użytkownicy-goście nadal potrzebują dostępu do zasobów. Funkcja **przeglądów programu Access** jest dostępna w **usłudze Azure Active Directory** w obszarze **Zarządzanie** > **relacjami organizacyjnymi**. (Można również wyszukać "przeglądy dostępu" ze **wszystkich usług** w witrynie Azure portal.) Aby dowiedzieć się, jak korzystać z recenzji dostępu, zobacz [Zarządzanie dostępem gościa za pomocą przeglądów dostępu usługi Azure AD](../governance/manage-guest-access-with-access-reviews.md).

## <a name="audit-logs"></a>Dzienniki inspekcji

Dzienniki inspekcji usługi Azure AD zawierają rekordy działań systemowych i użytkowników, w tym działania inicjowane przez użytkowników-gościa. Aby uzyskać dostęp do dzienników inspekcji, w **usłudze Azure Active Directory**w obszarze **Monitorowanie**wybierz pozycję **Dzienniki inspekcji**. Oto przykład zaproszenia i historii odkupienia zaproszony Sam Oogle:

![Zrzut ekranu przedstawiający i przykład danych wyjściowych dziennika inspekcji](./media/auditing-and-reporting/audit-log.png)

Możesz zagłębić się w każde z tych wydarzeń, aby uzyskać szczegółowe informacje. Na przykład przyjrzyjmy się szczegółom akceptacji.

![Zrzut ekranu przedstawiający i przykład danych wyjściowych szczegółów aktywności](./media/auditing-and-reporting/activity-details.png)

Można również wyeksportować te dzienniki z usługi Azure AD i użyć wybranego narzędzia do raportowania, aby uzyskać dostosowane raporty.

### <a name="next-steps"></a>Następne kroki

- [B2B collaboration user properties (Właściwości użytkowników współpracy B2B)](user-properties.md)

