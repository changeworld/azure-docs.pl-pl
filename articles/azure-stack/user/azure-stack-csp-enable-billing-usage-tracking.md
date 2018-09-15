---
title: Włącz dostawcy usług w chmurze do zarządzania subskrypcją usługi Azure Stack | Dokumentacja firmy Microsoft
description: Włącz dostawcę usług można uzyskać dostępu do subskrypcji w usłudze Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: alfredop
ms.openlocfilehash: fca08ef1d803c3bd47b0ae925c4dd12255175f2c
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2018
ms.locfileid: "45630719"
---
# <a name="enable-a-cloud-service-provider-to-manage-your-azure-stack-subscription"></a>Włącz dostawcy usług w chmurze do zarządzania subskrypcją usługi Azure Stack

*Dotyczy: zintegrowane systemy usługi Azure Stack*

Jeśli używasz usługi Azure Stack przy użyciu dostawcy usług chmury (CSP), możesz wybrać do zarządzania subskrypcją dostępu do zasobów na platformie Azure i w usłudze Azure Stack. Można także pozwolić dostawcy na Zarządzanie subskrypcją za Ciebie. W tym artykule przedstawiono, jak do:

 * Zapewnij dostęp dostawcy usługi w Twojej subskrypcji.
 * Upewnij się, że dostawcy usług mogą zarządzać swoją usługą.

> [!Note]
>  Jeśli dostawca usług Kryptograficznych nie jest zarządzanie kontem i pominęli następujące kroki, dostawcy usług Kryptograficznych nie może zarządzać subskrypcją usługi Azure Stack dla Ciebie.

## <a name="manage-your-subscription-with-a-cloud-service-provider"></a>Zarządzanie subskrypcją za pomocą dostawcy usług w chmurze

Dodawanie dostawcy rozwiązań w Chmurze jako **użytkownika** do Twojej subskrypcji.

1. Dodaj do dostawcy usług Kryptograficznych jako użytkownika-gościa z rolą użytkownika do katalogu dzierżawy.  Aby uzyskać instrukcje dotyczące dodawania użytkownika, zobacz [Dodawanie nowych użytkowników do usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. Dostawca CSP tworzy lokalne subskrypcji usługi Azure Stack dla Ciebie.
3. Jesteś gotowy rozpocząć korzystanie z usługi Azure Stack.
4. Dostawca usług Kryptograficznych, należy utworzyć zasób w ramach subskrypcji, aby sprawdzić, czy można również zarządzać zasobami. Na przykład mogą oni [utworzyć maszynę wirtualną Windows za pomocą portalu usługi Azure Stack](azure-stack-quick-windows-portal.md).

## <a name="enable-the-cloud-service-provider-to-manage-your-subscription-using-rbac-rights"></a>Włącz dostawcy usług w chmurze umożliwiający Zarządzanie subskrypcją za pomocą uprawnień RBAC

Dodawanie dostawcy rozwiązań w Chmurze jako **właściciela** do Twojej subskrypcji.

1. Dodaj do dostawcy usług Kryptograficznych jako użytkownika-gościa do katalogu dzierżawy.  Aby uzyskać instrukcje dotyczące dodawania użytkownika, zobacz [Dodawanie nowych użytkowników do usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. Dodaj rolę właściciela użytkownik-Gość dostawcy usług Kryptograficznych. Aby uzyskać instrukcje dotyczące dodawania użytkownika dostawcy usług Kryptograficznych do Twojej subskrypcji, zobacz [Use Role-Based kontroli dostępu w celu zarządzania dostępem do zasobów subskrypcji platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)
3. Dostawca CSP tworzy lokalne subskrypcji usługi Azure Stack dla Ciebie.
4. Jesteś gotowy rozpocząć korzystanie z usługi Azure Stack.
5. Dostawca usług Kryptograficznych, należy utworzyć zasób w ramach subskrypcji, aby zweryfikować, że mogą zarządzać zasobami.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o tym, jak pobrać informacje o użyciu zasobów z usługi Azure Stack, zobacz [użycie i rozliczenia w usłudze Azure Stack](../azure-stack-billing-and-chargeback.md).
