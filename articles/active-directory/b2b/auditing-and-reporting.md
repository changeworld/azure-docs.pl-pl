---
title: Inspekcja i raportowanie użytkownik współpracy B2B — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Właściwości użytkownika gościa są konfigurowane we współpracy B2B usługi Azure Active Directory
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
ms.openlocfilehash: 506721a23a5a18eaea76efaca61df49f20dc9228
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65812465"
---
# <a name="auditing-and-reporting-a-b2b-collaboration-user"></a>Inspekcja i raportowanie użytkownika współpracy B2B
Użytkownicy-goście masz inspekcji funkcje podobne do użytkowników. 

## <a name="access-reviews"></a>Sprawdzanie dostępu
Przeglądy dostępu umożliwia okresowo sprawdzać, czy użytkownicy-goście nadal potrzebują dostępu do zasobów. **Przeglądów dostępu** funkcja jest dostępna w **usługi Azure Active Directory** w obszarze **Zarządzaj** > **relacjeworganizacji**. (Możesz również wyszukać "przeglądów dostępu" z **wszystkich usług** w witrynie Azure portal.) Aby dowiedzieć się, jak używać przeglądów dostępu, zobacz [przeglądy zarządzanie dostępem gości za pomocą usługi Azure AD access](../governance/manage-guest-access-with-access-reviews.md).

## <a name="audit-logs"></a>Dzienniki inspekcji

Inspekcja usługi Azure AD dzienniki dostarczają informacji na temat działania systemu i użytkownika, w tym działania zainicjowane przez użytkowników-gości. Uzyskiwanie dostępu do dzienników inspekcji w **usługi Azure Active Directory**w obszarze **monitorowanie**, wybierz opcję **dzienniki inspekcji**. Oto przykład zaproszenie i realizacji historii użytkownika osoby zaproszonej Sam Oogle:

![Zrzut ekranu przedstawiający i przykład inspekcji dziennika danych wyjściowych](./media/auditing-and-reporting/audit-log.png)

Możesz od razu do każdego z tych zdarzeń, aby uzyskać szczegóły. Na przykład Przyjrzyjmy się szczegóły zatwierdzenia.

![Zrzut ekranu przedstawiający i przykład danych wyjściowych szczegóły działania](./media/auditing-and-reporting/activity-details.png)

Można również wyeksportować te dzienniki z usługi Azure AD i uzyskiwanie dostosowanych raportów za pomocą narzędzia raportowania wybranych przez użytkownika.

### <a name="next-steps"></a>Kolejne kroki

- [B2B collaboration user properties (Właściwości użytkowników współpracy B2B)](user-properties.md)

