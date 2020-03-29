---
title: Rozwiązywanie problemów z zarządzaniem tożsamościami uprzywilejowanymi — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z błędami systemu za pomocą ról w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/18/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 474f2634e6f7ddc1840548c39ae86cb54c3bf08e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299690"
---
# <a name="troubleshoot-a-problem-with-privileged-identity-management"></a>Rozwiązywanie problemów z zarządzaniem tożsamościami uprzywilejowanymi

Czy masz problem z zarządzaniem tożsamościami uprzywilejowanymi (PIM) w usłudze Azure Active Directory (Azure AD)? Następujące informacje mogą pomóc w ponownym sprawdnienie pracy.

## <a name="access-to-azure-resources-denied"></a>Odmowa dostępu do zasobów platformy Azure

### <a name="problem"></a>Problem

Jako aktywny właściciel lub administrator dostępu do użytkownika zasobu platformy Azure możesz zobaczyć swój zasób w dziale zarządzania tożsamościami uprzywilejowanymi, ale nie możesz wykonywać żadnych akcji, takich jak tworzenie kwalifikującego się przydziału lub wyświetlanie listy przypisań ról z zasobu strony przeglądu. Każda z tych akcji powoduje błąd autoryzacji.

### <a name="cause"></a>Przyczyna

Ten problem może się zdarzyć, gdy rola administratora dostępu użytkownika dla jednostki usługi PIM została przypadkowo usunięta z subskrypcji. Aby usługa Zarządzanie tożsamościami uprzywilejowanymi mogła uzyskać dostęp do zasobów platformy Azure, podmiotowi usługi MS-PIM zawsze należy przypisać [rolę Administratora dostępu użytkownika](../../role-based-access-control/built-in-roles.md#user-access-administrator) w ramach subskrypcji platformy Azure.

### <a name="resolution"></a>Rozwiązanie

Przypisz rolę Administrator dostępu użytkownika do nazwy głównej usługi zarządzania tożsamością uprzywilejowaną (MS–PIM) na poziomie subskrypcji. To przypisanie powinno umożliwić usłudze zarządzania tożsamościami uprzywilejowanymi dostęp do zasobów platformy Azure. Rolę można przypisać na poziomie grupy zarządzania lub na poziomie subskrypcji, w zależności od wymagań. Aby uzyskać więcej informacji zleceniodawców usługi, zobacz [Przypisywanie aplikacji do roli](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application).

## <a name="next-steps"></a>Następne kroki

- [Wymagania licencyjne dotyczące korzystania z zarządzania tożsamościami uprzywilejowanymi](subscription-requirements.md)
- [Zabezpieczanie uprzywilejowanego dostępu dla wdrożeń hybrydowych i wdrożeń w chmurze w usłudze Azure AD](../users-groups-roles/directory-admin-roles-secure.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [Wdrażanie usługi Privileged Identity Management](pim-deployment-plan.md)
