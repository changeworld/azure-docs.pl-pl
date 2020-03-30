---
title: Przypisywanie dostępu do tożsamości zarządzanej do zasobu przy użyciu witryny Azure portal — Azure AD
description: Instrukcje krok po kroku dotyczące przypisywania tożsamości zarządzanej w jednym dostępie do zasobu do innego zasobu przy użyciu witryny Azure portal.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: e24c97909870c4d76b07ec837e5f624a509bd1f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74547288"
---
# <a name="assign-a-managed-identity-access-to-a-resource-by-using-the-azure-portal"></a>Przypisywanie dostępu do tożsamości zarządzanej do zasobu przy użyciu witryny Azure portal

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Po skonfigurowaniu zasobu platformy Azure z tożsamością zarządzaną można przyznać tożsamości zarządzanej dostęp do innego zasobu, podobnie jak każdy podmiot zabezpieczeń. W tym artykule pokazano, jak udzielić maszyny wirtualnej platformy Azure lub zestawu tożsamości zarządzanej zestawu maszyn wirtualnych do konta magazynu platformy Azure przy użyciu witryny Azure portal.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz tożsamości zarządzanych dla zasobów platformy Azure, zapoznaj się z [sekcją omówienie](overview.md). **Pamiętaj, aby przejrzeć [różnicę między tożsamością zarządzaną przypisaną do systemu a przypisaną przez użytkownika](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Przypisywanie dostępu do tożsamości zarządzanej do innego zasobu za pomocą funkcji RBAC

Po włączeniu tożsamości zarządzanej w zasobie platformy Azure, takim jak [maszyna wirtualna platformy Azure](qs-configure-portal-windows-vm.md) lub maszyna [wirtualna platformy Azure:](qs-configure-portal-windows-vmss.md)

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, w ramach której skonfigurowano tożsamość zarządzaną.

2. Przejdź do żądanego zasobu, na którym chcesz zmodyfikować kontrolę dostępu. W tym przykładzie dajemy maszynie wirtualnej platformy Azure dostęp do konta magazynu, więc przejdź do konta magazynu.

3. Wybierz stronę **Kontroli dostępu (IAM)** zasobu i wybierz pozycję **+ Dodaj przypisanie roli**. Następnie określ **rolę**, **Przypisz dostęp do**programu i określ odpowiednią **subskrypcję**. W obszarze kryteriów wyszukiwania powinien zostać wyświetlony zasób. Zaznacz zasób i wybierz pozycję **Zapisz**. 

   ![Zrzut ekranu przedstawiający kontrolę dostępu (IAM)](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  
     
## <a name="next-steps"></a>Następne kroki

- [Omówienie tożsamości zarządzanej dla zasobów platformy Azure](overview.md)
- Aby włączyć tożsamość zarządzaną na maszynie wirtualnej platformy Azure, zobacz [Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure na maszynie Wirtualnej przy użyciu portalu Azure.](qs-configure-portal-windows-vm.md)
- Aby włączyć tożsamość zarządzaną w zestawie skalowania maszyny wirtualnej platformy Azure, zobacz [Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure w zestawie skalowania maszyny wirtualnej przy użyciu portalu Azure.](qs-configure-portal-windows-vmss.md)


