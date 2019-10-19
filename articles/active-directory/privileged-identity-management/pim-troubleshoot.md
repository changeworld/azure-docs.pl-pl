---
title: Rozwiązywanie problemów z Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak rozwiązywać problemy z błędami systemu przy użyciu ról w Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b7fc3508f7f672e277577f92218ff1860b676cb
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72559478"
---
# <a name="troubleshoot-a-problem-with-privileged-identity-management"></a>Rozwiązywanie problemów z usługą Privileged Identity Management

Czy masz problem z usługą Privileged Identity Management (PIM) w usłudze Azure Active Directory (Azure AD)? Poniższe informacje mogą pomóc w ponownym uruchomieniu.

## <a name="access-to-azure-resources-denied"></a>Odmowa dostępu do zasobów platformy Azure

### <a name="problem"></a>Problem

Wystąpił błąd autoryzacji podczas próby uprawnienia użytkownika do roli administratora usługi Azure AD i nie można uzyskać dostępu do zasobów platformy Azure w obszarze Privileged Identity Management. Nie możesz uzyskać dostępu do zasobów platformy Azure w obszarze Privileged Identity Management, nawet jeśli jesteś administratorem globalnym i właścicielem subskrypcji.

### <a name="cause"></a>Przyczyna

Ten problem może wystąpić, gdy rola administratora dostępu użytkownika dla jednostki usługi PIM została przypadkowo usunięta z subskrypcji. Aby usługa Privileged Identity Management mogła uzyskać dostęp do zasobów platformy Azure, jednostka usługi MS-PIM powinna mieć zawsze przypisaną [rolę administratora dostępu użytkownika](../../role-based-access-control/built-in-roles.md#user-access-administrator) w ramach subskrypcji platformy Azure.

### <a name="resolution"></a>Rozdzielczość

Przypisz rolę Administrator dostępu użytkowników do nazwy głównej usługi Privileged Identity Management (MS — PIM) na poziomie subskrypcji. To przypisanie powinno umożliwić usłudze Privileged Identity Management dostęp do zasobów platformy Azure. Rolę można przypisywać na poziomie grupy zarządzania lub na poziomie subskrypcji, w zależności od wymagań. Aby uzyskać więcej informacji o nazwach głównych usługi, zobacz [przypisywanie aplikacji do roli](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role).

## <a name="next-steps"></a>Następne kroki

- [Wymagania licencyjne dotyczące używania Privileged Identity Management](subscription-requirements.md)
- [Zabezpieczanie uprzywilejowanego dostępu dla wdrożeń hybrydowych i wdrożeń w chmurze w usłudze Azure AD](../users-groups-roles/directory-admin-roles-secure.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [Wdróż Privileged Identity Management](pim-deployment-plan.md)
