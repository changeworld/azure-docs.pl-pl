---
title: Konfigurowanie tożsamości usługi zarządzanej na Maszynie wirtualnej platformy Azure przy użyciu witryny Azure portal
description: Krok po kroku instrukcje dotyczące konfigurowania tożsamości usługi zarządzanej na Maszynie wirtualnej platformy Azure przy użyciu witryny Azure portal.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2017
ms.author: daveba
ms.openlocfilehash: 552fce2ffd8b6bd786010da82e702ee98c3f8647
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/24/2018
ms.locfileid: "42888560"
---
# <a name="configure-a-vm-managed-service-identity-using-the-azure-portal"></a>Skonfiguruj indywidualne tożsamości usługi zarządzanej maszyny Wirtualnej przy użyciu witryny Azure portal

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Tożsamość usługi zarządzanej udostępnia usługi platformy Azure przy użyciu automatycznie zarządzanych tożsamości w usłudze Azure Active Directory. Można użyć tej tożsamości do uwierzytelniania na dowolne usługi obsługujące uwierzytelnianie usługi Azure AD bez poświadczeń w kodzie. 

W tym artykule dowiesz się, jak włączanie i wyłączanie systemowych i użytkownika tożsamości przypisanej do maszyny wirtualnej (maszyny Wirtualnej platformy Azure), przy użyciu witryny Azure portal. 

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli jesteś zaznajomiony z tożsamości usługi zarządzanej, zapoznaj się z [sekcji Przegląd](overview.md).
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Do wykonywania operacji zarządzania, w tym artykule, Twoje konto musi następujące przypisania roli:
    - [Współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) włączyć i usuwania tożsamości przypisanej w systemie z Maszyną wirtualną platformy Azure.

## <a name="system-assigned-identity"></a>Tożsamości przypisanej w systemie

W tej sekcji dowiesz się, jak włączyć i wyłączyć system tożsamości przypisanej do maszyny Wirtualnej przy użyciu witryny Azure portal.

### <a name="enable-system-assigned-identity-during-creation-of-a-vm"></a>Włącz system tożsamości przypisanej podczas tworzenia maszyny Wirtualnej

Witryny Azure portal nie obsługuje obecnie włączenie tożsamości przypisanej w systemie podczas tworzenia maszyny Wirtualnej. Zamiast tego zobacz następujące artykuły Szybki Start tworzenia maszyny Wirtualnej, aby najpierw utworzyć Maszynę wirtualną, a następnie przejść do następnej sekcji, aby uzyskać szczegółowe informacje na temat włączania tożsamości przypisanej w systemie na maszynie Wirtualnej:

- [Utwórz maszynę wirtualną Windows za pomocą witryny Azure portal](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Utwórz maszynę wirtualną systemu Linux w witrynie Azure portal](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)  

### <a name="enable-system-assigned-identity-on-an-existing-vm"></a>Włącz system przypisane tożsamości istniejącej maszyny wirtualnej

Aby włączyć system przypisane tożsamości na maszynie Wirtualnej, która pierwotnie została aprowizowana bez niego:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, który zawiera maszynę Wirtualną.

2. Przejdź do odpowiednią maszynę wirtualną i wybierz **tożsamości**.

3. W obszarze **przypisanej w systemie**, **stan**, wybierz opcję **na** a następnie kliknij przycisk **Zapisz**:

   ![Zrzut ekranu strony konfiguracji](../managed-service-identity/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

### <a name="remove-system-assigned-identity-from-a-vm"></a>Usuń system tożsamości przypisanej z maszyny Wirtualnej

Jeśli masz maszyny wirtualnej, która nie wymaga tożsamości przypisanej w systemie:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, który zawiera maszynę Wirtualną. 

2. Przejdź do odpowiednią maszynę wirtualną i wybierz **tożsamości**.

3. W obszarze **przypisanej w systemie**, **stan**, wybierz opcję **poza** a następnie kliknij przycisk **Zapisz**:

   ![Zrzut ekranu strony konfiguracji](../managed-service-identity/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)

## <a name="user-assigned-identity"></a>Tożsamości przypisanych przez użytkownika

 W tej sekcji dowiesz się, jak dodać i usunąć użytkownika z tożsamości przypisanej z maszyny Wirtualnej przy użyciu witryny Azure portal.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-vm"></a>Przypisywanie użytkownika tożsamości przypisanej podczas tworzenia maszyny Wirtualnej

Obecnie witryna Azure portal nie obsługuje przypisanie użytkownika tożsamości przypisanej podczas tworzenia maszyny Wirtualnej. Zamiast tego można skorzystać z jednego z następujących artykułów szybkiego startu tworzenia maszyny Wirtualnej do utworzenia maszyny Wirtualnej, a następnie przejść do następnej sekcji, aby uzyskać szczegółowe informacje na temat przypisywania użytkownika tożsamości przypisanej do maszyny Wirtualnej:

- [Utwórz maszynę wirtualną Windows za pomocą witryny Azure portal](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Utwórz maszynę wirtualną systemu Linux w witrynie Azure portal](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)

### <a name="assign-a-user-assigned-identity-to-an-existing-vm"></a>Przypisywanie użytkownika tożsamości przypisanej do istniejącej maszyny Wirtualnej

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, który zawiera maszynę Wirtualną.
2. Przejdź do maszyny Wirtualnej i kliknij żądany **tożsamości**, **przypisane do użytkowników** i następnie  **\+Dodaj**.

   ![Dodawanie tożsamości przypisanych przez użytkownika do maszyny Wirtualnej](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot1.png)

3. Kliknij przycisk tożsamości przypisanych przez użytkownika, chcesz dodać do maszyny Wirtualnej, a następnie kliknij przycisk **Dodaj**.

    ![Dodawanie tożsamości przypisanych przez użytkownika do maszyny Wirtualnej](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-identity-from-a-vm"></a>Usuń użytkownika z tożsamości przypisanej z maszyny Wirtualnej

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, który zawiera maszynę Wirtualną.
2. Przejdź do maszyny Wirtualnej i kliknij żądany **tożsamości**, **przypisane do użytkowników**, nazwy tożsamości przypisanych przez użytkownika, które chcesz usunąć, a następnie kliknij przycisk **Usuń** (kliknij **Tak** w okienku potwierdzenia).

   ![Usuwanie tożsamości przypisanych z maszyny Wirtualnej przez użytkownika](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vm-screenshot.png)

## <a name="related-content"></a>Powiązana zawartość

- Aby zapoznać się z omówieniem tożsamości usługi zarządzanej, zobacz [Przegląd](overview.md).

## <a name="next-steps"></a>Kolejne kroki

- Za pomocą witryny Azure portal nadaj tożsamości usługi zarządzanej maszynie Wirtualnej platformy Azure [dostęp do zasobów platformy Azure w innym](howto-assign-access-portal.md).

