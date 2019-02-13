---
title: Inspekcja i raportowanie użytkownik współpracy B2B usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Właściwości użytkownika gościa są konfigurowane we współpracy B2B usługi Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fc36128f8f998d78dd2cf9ef112fe5961bbef5b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56204614"
---
# <a name="auditing-and-reporting-a-b2b-collaboration-user"></a>Inspekcja i raportowanie użytkownika współpracy B2B
Użytkownicy-goście masz inspekcji funkcje podobne do użytkowników. 

## <a name="access-reviews"></a>Przeglądy dostępu
Przeglądy dostępu umożliwia okresowo sprawdzać, czy użytkownicy-goście nadal potrzebują dostępu do zasobów. **Przeglądów dostępu** funkcja jest dostępna w **usługi Azure Active Directory** w obszarze **Zarządzaj** > **relacjeworganizacji**. (Możesz również wyszukać "przeglądów dostępu" z **wszystkich usług** w witrynie Azure portal.) Aby dowiedzieć się, jak używać przeglądów dostępu, zobacz [przeglądy zarządzanie dostępem gości za pomocą usługi Azure AD access](../governance/manage-guest-access-with-access-reviews.md).

## <a name="audit-logs"></a>Dzienniki inspekcji

Inspekcja usługi Azure AD dzienniki dostarczają informacji na temat działania systemu i użytkownika, w tym działania zainicjowane przez użytkowników-gości. Uzyskiwanie dostępu do dzienników inspekcji w **usługi Azure Active Directory**w obszarze **monitorowanie**, wybierz opcję **dzienniki inspekcji**. Oto przykład zaproszenie i realizacji historii użytkownika osoby zaproszonej Sam Oogle:

![dziennik inspekcji](./media/auditing-and-reporting/audit-log.png)

Możesz od razu do każdego z tych zdarzeń, aby uzyskać szczegóły. Na przykład Przyjrzyjmy się szczegóły zatwierdzenia.

![Szczegóły działania](./media/auditing-and-reporting/activity-details.png)

Można również wyeksportować te dzienniki z usługi Azure AD i uzyskiwanie dostosowanych raportów za pomocą narzędzia raportowania wybranych przez użytkownika.

### <a name="next-steps"></a>Kolejne kroki

- [B2B collaboration user properties (Właściwości użytkowników współpracy B2B)](user-properties.md)

