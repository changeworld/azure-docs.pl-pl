---
title: Włącz dostawcy usług w chmurze do zarządzania subskrypcją Azure stosu | Dokumentacja firmy Microsoft
description: Włączenie subskrypcji Azure stosu dostępu do dostawcy usług.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: f0cff8f575b87872c0032854f1916b140d7fd62b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="enable-a-cloud-service-provider-to-manage-your-azure-stack-subscription"></a>Włącz dostawcy usług w chmurze do zarządzania subskrypcją Azure stosu

*Dotyczy: Azure stosu zintegrowane systemy*

Jeśli używasz stosu Azure z dostawcy usług chmury (CSP), możesz wybrać do zarządzania subskrypcją dostęp do zasobów na platformie Azure i w stosie Azure. Można także pozwolić dostawcy na Zarządzanie subskrypcją za Ciebie. W tym artykule opisano sposób do:

 * Nadaj subskrypcji dostęp dostawcy usługi.
 * Upewnij się, że dostawca usług można zarządzać usługą.

> [!Note]
>  Jeśli dostawca usług Kryptograficznych nie jest zarządzanie kontem i pominęli następujące kroki, dostawca usług Kryptograficznych nie może zarządzać subskrypcją Azure stosu dla Ciebie.

## <a name="manage-your-subscription-with-a-cloud-service-provider"></a>Zarządzanie subskrypcją z dostawcy usług w chmurze

Dodawanie dostawcy usług Kryptograficznych jako **użytkownika** do subskrypcji.

1. Dodawanie do dostawcy usług Kryptograficznych jako gość z rolą użytkownika do katalogu dzierżawcy.  Aby uzyskać instrukcje dotyczące dodawania użytkownika, zobacz [Dodawanie nowych użytkowników do usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. Dostawca usług Kryptograficznych tworzy lokalne subskrypcji Azure stosu dla Ciebie.
3. Wszystko jest gotowe rozpocząć korzystanie z usługi Azure stosu.
4. Dostawca usług Kryptograficznych, należy utworzyć zasobu w ramach subskrypcji, aby sprawdzić, czy można również zarządzać zasobami. Na przykład można [Utwórz maszynę wirtualną z systemem Windows przy użyciu portalu Azure stosu](azure-stack-quick-windows-portal.md).

## <a name="enable-the-cloud-service-provider-to-manage-your-subscription-using-rbac-rights"></a>Włącz dostawcy usług w chmurze umożliwiający Zarządzanie subskrypcją za pomocą praw RBAC

Dodawanie dostawcy usług Kryptograficznych jako **właściciela** do subskrypcji.

1. Dodawanie do dostawcy usług Kryptograficznych jako gość do katalogu dzierżawcy.  Aby uzyskać instrukcje dotyczące dodawania użytkownika, zobacz [Dodawanie nowych użytkowników do usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. Dodaj rolę właściciela na użytkownika gościa dostawcy usług Kryptograficznych. Aby uzyskać instrukcje dotyczące dodawania użytkownika dostawcy usług Kryptograficznych do subskrypcji, zobacz [Use Role-Based kontroli dostępu, aby zarządzać dostępem do zasobów subskrypcji platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)
3. Dostawca usług Kryptograficznych tworzy lokalne subskrypcji Azure stosu dla Ciebie.
4. Wszystko jest gotowe rozpocząć korzystanie z usługi Azure stosu.
5. Dostawca usług Kryptograficznych, należy utworzyć zasobu w ramach subskrypcji, aby sprawdzić, czy mogą zarządzać zasobami.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o tym, jak można pobrać informacji o użyciu zasobów ze stosu Azure, zobacz [użycie i rozliczenia Azure stosu](../azure-stack-billing-and-chargeback.md).
