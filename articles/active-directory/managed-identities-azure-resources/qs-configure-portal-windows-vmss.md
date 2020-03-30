---
title: Konfigurowanie tożsamości zarządzanych w usłudze Azure VMSS — usługa Azure AD
description: Instrukcje krok po kroku dotyczące konfigurowania tożsamości zarządzanych dla zasobów platformy Azure w zestawie skalowania maszyny wirtualnej przy użyciu witryny Azure portal.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32d8944b55794412eb322697469933030e2fbb56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74184034"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-the-azure-portal"></a>Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure w zestawie skalowania maszyny wirtualnej przy użyciu witryny Azure portal

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Tożsamości zarządzane dla zasobów platformy Azure zapewniają usługom platformy Azure automatycznie zarządzaną tożsamość w usłudze Azure Active Directory. Tej tożsamości można użyć do uwierzytelniania do dowolnej usługi, która obsługuje uwierzytelnianie usługi Azure AD, bez poświadczeń w kodzie. 

W tym artykule za pomocą programu PowerShell dowiesz się, jak wykonać następujące tożsamości zarządzane dla operacji zasobów platformy Azure na zestawie skalowania maszyny wirtualnej:

- Jeśli nie znasz tożsamości zarządzanych dla zasobów platformy Azure, zapoznaj się z [sekcją omówienie](overview.md).
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Aby wykonać operacje zarządzania w tym artykule, twoje konto wymaga następujących przypisań kontroli dostępu opartych na rolach platformy Azure:

    > [!NOTE]
    > Nie są wymagane żadne dodatkowe przypisania ról katalogu usługi Azure AD.

    - [Współautor maszyny wirtualnej,](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) aby włączyć i usunąć tożsamości zarządzanej przypisane do systemu z zestawu skalowania maszyny wirtualnej.

## <a name="system-assigned-managed-identity"></a>Tożsamość zarządzana przypisana do systemu

W tej sekcji dowiesz się, jak włączyć i wyłączyć tożsamość zarządzaną przypisaną do systemu przy użyciu witryny Azure portal.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-virtual-machine-scale-set"></a>Włączanie tożsamości zarządzanej przypisanej do systemu podczas tworzenia zestawu skalowania maszyny wirtualnej

Obecnie witryna Azure portal nie obsługuje włączania tożsamości zarządzanej przypisanej do systemu podczas tworzenia zestawu skalowania maszyny wirtualnej. Zamiast tego zapoznaj się z następującym artykułem tworzenia zestawu skalowania maszyny wirtualnej Szybki start, aby najpierw utworzyć zestaw skalowania maszyny wirtualnej, a następnie przejdź do następnej sekcji, aby uzyskać szczegółowe informacje na temat włączania tożsamości zarządzanej przypisanej do systemu w zestawie skalowania maszyny wirtualnej:

- [Tworzenie zestawu skalowania maszyny wirtualnej w witrynie Azure portal](../../virtual-machine-scale-sets/quick-create-portal.md)  

### <a name="enable-system-assigned-managed-identity-on-an-existing-virtual-machine-scale-set"></a>Włączanie tożsamości zarządzanej przypisanej do systemu w istniejącym zestawie skalowania maszyny wirtualnej

Aby włączyć przypisaną do systemu tożsamość zarządzaną w zestawie skalowania maszyny wirtualnej, który został pierwotnie zainicjowany bez niego:

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, która zawiera zestaw skalowania maszyny wirtualnej.

2. Przejdź do żądanego zestawu skalowania maszyny wirtualnej.

3. W **obszarze Przypisany system**, **Stan**, wybierz pozycję **Włączone,** a następnie kliknij przycisk **Zapisz**:

   ![Zrzut ekranu strony konfiguracji](./media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png) 

### <a name="remove-system-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Usuwanie tożsamości zarządzanej przypisanej przez system z zestawu skalowania maszyny wirtualnej

Jeśli masz zestaw skalowania maszyny wirtualnej, który nie wymaga już tożsamości zarządzanej przypisanej do systemu:

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, która zawiera zestaw skalowania maszyny wirtualnej. Upewnij się również, że twoje konto należy do roli, która daje uprawnienia do zapisu na zestawie skalowania maszyny wirtualnej.

2. Przejdź do żądanego zestawu skalowania maszyny wirtualnej.

3. W **obszarze Przypisany system**, **Stan**, wybierz pozycję **Wyłączone,** a następnie kliknij przycisk **Zapisz**:

   ![Zrzut ekranu strony konfiguracji](./media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)

## <a name="user-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez użytkownika

W tej sekcji dowiesz się, jak dodać i usunąć tożsamość zarządzaną przypisaną przez użytkownika z zestawu skalowania maszyny wirtualnej przy użyciu portalu Azure.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika podczas tworzenia zestawu skalowania maszyny wirtualnej

Obecnie portal Azure portal nie obsługuje przypisywania tożsamości zarządzanej przypisanej przez użytkownika podczas tworzenia zestawu skalowania maszyny wirtualnej. Zamiast tego zapoznaj się z następującym artykułem tworzenia zestawu skalowania maszyny wirtualnej Szybki start, aby najpierw utworzyć zestaw skalowania maszyny wirtualnej, a następnie przejdź do następnej sekcji, aby uzyskać szczegółowe informacje na temat przypisywania do niej tożsamości zarządzanej przypisanej przez użytkownika:

- [Tworzenie zestawu skalowania maszyny wirtualnej w witrynie Azure portal](../../virtual-machine-scale-sets/quick-create-portal.md)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika do istniejącego zestawu skalowania maszyny wirtualnej

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, która zawiera zestaw skalowania maszyny wirtualnej.
2. Przejdź do żądanego zestawu skalowania maszyny wirtualnej ** \+** i kliknij pozycję **Tożsamość**, **przypisany użytkownik,** a następnie dodaj .

   ![Dodawanie tożsamości przypisanej przez użytkownika do usługi VMSS](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vmss-screenshot1.png)

3. Kliknij tożsamość przypisaną przez użytkownika, którą chcesz dodać do zestawu skalowania maszyny wirtualnej, a następnie kliknij przycisk **Dodaj**.
   
   ![Dodawanie tożsamości przypisanej przez użytkownika do usługi VMSS](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Usuwanie tożsamości zarządzanej przypisanej przez użytkownika z zestawu skalowania maszyny wirtualnej

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, która zawiera maszynę wirtualną.
2. Przejdź do żądanego zestawu skalowania maszyny wirtualnej i kliknij pozycję **Tożsamość**, **przypisany użytkownik**, nazwę przypisanej przez użytkownika tożsamości zarządzanej, którą chcesz usunąć, a następnie kliknij przycisk **Usuń** (kliknij przycisk **Tak** w okienku potwierdzenia).

   ![Usuwanie tożsamości przypisanej przez użytkownika z usługi VMSS](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vmss-screenshot.png)


## <a name="next-steps"></a>Następne kroki

- Korzystając z witryny Azure portal, nadaj zestawowi skalowania maszyny wirtualnej platformy Azure dostęp do tożsamości zarządzanej [do innego zasobu platformy Azure.](howto-assign-access-portal.md)


