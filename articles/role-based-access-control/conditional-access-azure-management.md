---
title: Zarządzanie dostępem do zarządzania platformą Azure za pomocą dostępu warunkowego w usłudze Azure AD
description: Dowiedz się więcej o używaniu dostępu warunkowego w usłudze Azure AD do zarządzania dostępem do zarządzania platformą Azure.
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
ms.date: 07/15/2019
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: f3341f1c30a1581b8507652c322c00581e3972aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77137410"
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>Zarządzanie dostępem do zarządzania platformą Azure za pomocą dostępu warunkowego

> [!CAUTION]
> Upewnij się, że przed skonfigurowaniem zasad zarządzania dostępem do zarządzania platformą Azure można zrozumieć, jak działa dostęp warunkowy. Upewnij się, że nie tworzysz warunków, które mogłyby zablokować twój własny dostęp do portalu.

Dostęp warunkowy w usłudze Azure Active Directory (Azure AD) kontroluje dostęp do aplikacji w chmurze na podstawie określonych warunków, które określisz. Aby zezwolić na dostęp, należy utworzyć zasady dostępu warunkowego, które zezwalają lub blokują dostęp na podstawie tego, czy wymagania w zasadach są spełnione. 

Zazwyczaj można użyć dostępu warunkowego do kontrolowania dostępu do aplikacji w chmurze. Można również skonfigurować zasady, aby kontrolować dostęp do zarządzania platformy Azure na podstawie pewnych warunków (takich jak ryzyko logowania, lokalizacja lub urządzenie) i wymuszać wymagania, takie jak uwierzytelnianie wieloskładnikowe.

Aby utworzyć zasady zarządzania platformy Azure, wybierz **microsoft azure management** w obszarze **Aplikacje w chmurze** przy wyborze aplikacji, do której mają być stosowane zasady.

![Dostęp warunkowy do zarządzania platformy Azure](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

Utworzone zasady dotyczą wszystkich punktów końcowych zarządzania platformą Azure, w tym następujących:

- Portal Azure
- Dostawca usługi Azure Resource Manager
- Klasyczne interfejsy API zarządzania usługami
- Azure PowerShell
- Portal administratora subskrypcji programu Visual Studio
- Azure DevOps
- Portal usługi Azure Data Factory

Należy zauważyć, że zasady dotyczą usługi Azure PowerShell, która wywołuje interfejs API usługi Azure Resource Manager. Nie ma zastosowania do [programu Azure AD PowerShell](/powershell/azure/active-directory/install-adv2), który wywołuje microsoft graph.


Aby uzyskać więcej informacji na temat konfigurowania i używania dostępu warunkowego, zobacz [Dostęp warunkowy w usłudze Azure Active Directory](../active-directory/active-directory-conditional-access-azure-portal.md).
