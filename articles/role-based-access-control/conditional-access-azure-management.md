---
title: Zarządzanie dostępem do zarządzania platformy Azure przy użyciu dostępu warunkowego w usłudze Azure Active Directory
description: Więcej informacji na temat przy użyciu dostępu warunkowego w usłudze Azure AD w celu zarządzania dostępem do zarządzania platformy Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: skwan
ms.assetid: 0adc8b11-884e-476c-8c43-84f9bf12a34b
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/15/2019
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: 4ee58835a5055f5382cefa3a049424ffe97a01c4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67122942"
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>Zarządzanie dostępem do zarządzania platformy Azure przy użyciu dostępu warunkowego

Dostęp warunkowy w usłudze Azure Active Directory (Azure AD) steruje dostępem do aplikacji na podstawie określonych warunków, które określisz w chmurze. Aby zezwolić na dostęp, należy utworzyć zasady dostępu warunkowego, które zezwalania lub blokowania dostępu opartego na czy są spełnione wymagania zasad. 

Zwykle Użyj dostępu warunkowego do kontrolowania dostępu do aplikacji w chmurze. Możesz również skonfigurować zasady, do kontrolowania dostępu do zarządzania platformy Azure na podstawie określonych warunków (np. ryzyko logowania, lokalizacji lub urządzeń) oraz do wymuszania wymagań, takich jak uwierzytelnianie wieloskładnikowe.

Aby utworzyć zasady zarządzania systemu Azure, musisz wybrać **Microsoft Azure Management** w obszarze **aplikacje w chmurze** podczas wybierania aplikacji, dla której chcesz zastosować zasady.

![Dostęp warunkowy do zarządzania platformą Azure](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

Utworzonej zasady mają zastosowanie do wszystkich punktów końcowych zarządzania platformy Azure, łącznie z witryny Azure portal, dostawca usługi Azure Resource Manager i interfejsów API zarządzania usługami programu Azure PowerShell i programu Visual Studio subskrypcji administrator portalu klasycznym. Należy pamiętać, że zostaną zastosowane zasady programu Azure PowerShell, która wywołuje interfejs API usługi Azure Resource Manager. Nie ma zastosowania do [usługi Azure AD PowerShell](/powershell/azure/active-directory/install-adv2), która wymaga programu Microsoft Graph.

> [!CAUTION]
> Upewnij się, że rozumiesz, jak działa dostęp warunkowy, przed rozpoczęciem konfigurowania zasad do zarządzania dostępem do zarządzania platformy Azure. Upewnij się, że nie utworzono warunki, które mogą blokować dostęp do portalu.

Aby uzyskać więcej informacji na temat sposobu konfigurowania i korzystania z dostępu warunkowego, zobacz [dostępu warunkowego w usłudze Azure Active Directory](../active-directory/active-directory-conditional-access-azure-portal.md).