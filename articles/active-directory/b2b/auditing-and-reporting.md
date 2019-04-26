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
manager: daveba
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5763a7e5f122702ddaf86246fbfbd18326878146
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60357192"
---
# <a name="auditing-and-reporting-a-b2b-collaboration-user"></a>Inspekcja i raportowanie użytkownika współpracy B2B
Użytkownicy-goście masz inspekcji funkcje podobne do użytkowników. 

## <a name="access-reviews"></a>Przeglądy dostępu
Przeglądy dostępu umożliwia okresowo sprawdzać, czy użytkownicy-goście nadal potrzebują dostępu do zasobów. **Przeglądów dostępu** funkcja jest dostępna w **usługi Azure Active Directory** w obszarze **Zarządzaj** > **relacjeworganizacji**. (Możesz również wyszukać "przeglądów dostępu" z **wszystkich usług** w witrynie Azure portal.) Aby dowiedzieć się, jak używać przeglądów dostępu, zobacz [przeglądy zarządzanie dostępem gości za pomocą usługi Azure AD access](../governance/manage-guest-access-with-access-reviews.md).

## <a name="audit-logs"></a>Dzienniki inspekcji

Inspekcja usługi Azure AD dzienniki dostarczają informacji na temat działania systemu i użytkownika, w tym działania zainicjowane przez użytkowników-gości. Uzyskiwanie dostępu do dzienników inspekcji w **usługi Azure Active Directory**w obszarze **monitorowanie**, wybierz opcję **dzienniki inspekcji**. Oto przykład zaproszenie i realizacji historii użytkownika osoby zaproszonej Sam Oogle:

![Zrzut ekranu przedstawiający i przykład inspekcji dziennika danych wyjściowych](./media/auditing-and-reporting/audit-log.png)

Możesz od razu do każdego z tych zdarzeń, aby uzyskać szczegóły. Na przykład Przyjrzyjmy się szczegóły zatwierdzenia.

![Zrzut ekranu przedstawiający i przykład danych wyjściowych szczegóły działania](./media/auditing-and-reporting/activity-details.png)

Można również wyeksportować te dzienniki z usługi Azure AD i uzyskiwanie dostosowanych raportów za pomocą narzędzia raportowania wybranych przez użytkownika.

### <a name="next-steps"></a>Kolejne kroki

- [B2B collaboration user properties (Właściwości użytkowników współpracy B2B)](user-properties.md)

