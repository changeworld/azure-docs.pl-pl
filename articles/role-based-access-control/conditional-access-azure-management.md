---
title: Zarządzanie dostępem do zarządzania platformy Azure przy użyciu dostępu warunkowego w usłudze Azure Active Directory
description: Więcej informacji na temat korzystania z dostępu warunkowego w usłudze Azure AD do zarządzania dostępem do zarządzania systemem Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: skwan
ms.assetid: 0adc8b11-884e-476c-8c43-84f9bf12a34b
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/22/2017
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: dcf701c15ee20b7d9aeea65f3d06b794006a5202
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34203934"
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>Zarządzanie dostępem do zarządzania platformy Azure przy użyciu dostępu warunkowego

Dostęp warunkowy w usłudze Azure Active Directory (Azure AD) kontroluje dostęp do aplikacji na podstawie określonych warunków, które określisz w chmurze. Aby zezwolić na dostęp, należy utworzyć zasady dostępu warunkowego, które umożliwiają lub blokowanie dostępu oparte na Określa, czy zostały spełnione wymagania zasad. 

Zwykle Użyj dostępu warunkowego, aby kontrolować dostęp do aplikacji w chmurze. Zasady można skonfigurować również, aby kontrolować dostęp do zarządzania platformy Azure na podstawie określonych warunków (takich jak logowanie ryzyka, lokalizacji lub urządzenia) oraz do wymuszania wymagania, takich jak uwierzytelnianie wieloskładnikowe.

Aby utworzyć zasady zarządzania platformy Azure, należy wybrać **Microsoft Azure Management** w obszarze **aplikacji w chmurze** podczas wybierania aplikacji, do którego należy zastosować te zasady.

![Dostęp warunkowy do zarządzania platformy Azure](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

Utworzonej zasady ma zastosowanie do wszystkich punktów końcowych zarządzania platformy Azure, łącznie z klasycznego portalu Azure, portalu Azure, dostawca usługi Azure Resource Manager klasycznego interfejsy API usługi zarządzania i programu Azure PowerShell.

> [!CAUTION]
> Upewnij się, że rozumiesz, jak dostęp warunkowy działa przed rozpoczęciem konfigurowania zasad do zarządzania dostępem do zarządzania systemem Azure. Upewnij się, że nie twórz warunki, które można zablokować dostęp do portalu.

Aby uzyskać więcej informacji na temat sposobu ustawiania i korzystaj z dostępu warunkowego, zobacz [dostępu warunkowego w usłudze Azure Active Directory](../active-directory/active-directory-conditional-access-azure-portal.md).