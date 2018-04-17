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
ms.openlocfilehash: 26ba68be6d4932da77befaf7c968525393c0a033
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="enable-a-cloud-service-provider-to-manage-your-azure-stack-subscription"></a>Włącz dostawcy usług w chmurze do zarządzania subskrypcją Azure stosu

*Dotyczy: Azure stosu zintegrowane systemy*

Jeśli używasz stosu Azure z dostawcy usług chmury (CSP), dostęp do zasobów w Twojej subskrypcji platformy Azure i w stosie Azure mogą być zarządzane przez dostawcę. Lub może zarządzania własną subskrypcją. W tym artykule analizuje jak można włączyć usługodawcy korzystać z subskrypcji w Twoim imieniu lub upewnij się, że dostawca usług można zarządzać usługą.

> [!Note]  
>  Jeśli poniższe kroki są pomijane, a dostawca usług Kryptograficznych nie jest już Zarządzanie kontem, dostawca usług Kryptograficznych nie będzie mógł zarządzać subskrypcją Azure stosu w Twoim imieniu.

## <a name="manage-your-subscription-with-a-cloud-service-provider"></a>Zarządzanie subskrypcją z dostawcy usług w chmurze

1. Dodawanie do dostawcy usług Kryptograficznych jako gość z rolą użytkownika do katalogu dzierżawcy.  Aby uzyskać instrukcje dotyczące dodawania użytkownika, zobacz [Dodawanie nowych użytkowników do usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. Dostawca usług Kryptograficznych, spowoduje to utworzenie lokalnego subskrypcji Azure stosu dla Ciebie.
3. Wszystko jest gotowe rozpocząć korzystanie z usługi Azure stosu.
3. Dostawca usług Kryptograficznych należy następnie utwórz zasób w ramach subskrypcji, aby sprawdzić, czy można również zarządzać zasobami. Na przykład można [Utwórz maszynę wirtualną z systemem Windows przy użyciu portalu Azure stosu](azure-stack-quick-windows-portal.md).

## <a name="enable-the-cloud-service-provider-to-manage-your-subscription-using-rbac-rights"></a>Włącz dostawcy usług w chmurze umożliwiający Zarządzanie subskrypcją za pomocą praw RBAC

Dodawanie dostawcy usług Kryptograficznych jako właściciela do subskrypcji. 

1. Dodaj do dostawcy usług Kryptograficznych jako Gość. przy użyciu roli właściciela do katalogu dzierżawcy.  Aby uzyskać instrukcje dotyczące dodawania użytkownika, zobacz [Dodawanie nowych użytkowników do usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. Dodaj rolę właściciela na użytkownika gościa dostawcy usług Kryptograficznych. Aby uzyskać instrukcje dotyczące dodawania użytkownika dostawcy usług Kryptograficznych do subskrypcji, zobacz [Use Role-Based kontroli dostępu, aby zarządzać dostępem do zasobów subskrypcji platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)
3. Dostawca usług Kryptograficznych, spowoduje to utworzenie lokalnego subskrypcji Azure stosu dla Ciebie.
4. Wszystko jest gotowe rozpocząć korzystanie z usługi Azure stosu.
5. Dostawca usług Kryptograficznych należy następnie utwórz zasób w ramach subskrypcji, aby sprawdzić, czy mogą zarządzać zasobami. 

## <a name="next-steps"></a>Kolejne kroki

  - Aby dowiedzieć się więcej o tym, jak można pobrać informacji o użyciu zasobów ze stosu Azure, zobacz [użycie i rozliczenia Azure stosu](../azure-stack-billing-and-chargeback.md).
