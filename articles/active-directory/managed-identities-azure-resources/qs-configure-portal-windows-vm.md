---
title: Jak skonfigurować zarządzane tożsamości dla zasobów platformy Azure na Maszynie wirtualnej platformy Azure przy użyciu witryny Azure portal
description: Krok po kroku instrukcje dotyczące konfigurowania zarządzania tożsamościami dla zasobów platformy Azure na Maszynie wirtualnej platformy Azure przy użyciu witryny Azure portal.
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
ms.date: 11/10/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab0a5b021048f0f684473d3f54bbeadf870cd007
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58445304"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-vm-using-the-azure-portal"></a>Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie Wirtualnej przy użyciu witryny Azure portal

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Zarządzanych tożsamości dla zasobów platformy Azure udostępnia usługi platformy Azure przy użyciu automatycznie zarządzanych tożsamości w usłudze Azure Active Directory. Można użyć tej tożsamości do uwierzytelniania na dowolne usługi obsługujące uwierzytelnianie usługi Azure AD bez poświadczeń w kodzie. 

W tym artykule dowiesz się, jak włączyć i wyłączyć systemu i tożsamości przypisanych przez użytkownika zarządzanego dla maszyny wirtualnej (maszyny Wirtualnej platformy Azure), przy użyciu witryny Azure portal. 

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli jesteś zaznajomiony z zarządzanych tożsamości dla zasobów platformy Azure, zapoznaj się z [sekcji Przegląd](overview.md).
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.

## <a name="system-assigned-managed-identity"></a>Przypisana przez system tożsamości zarządzanej

W tej sekcji dowiesz się, jak włączanie i wyłączanie przypisany systemowo tożsamości zarządzanej dla maszyny Wirtualnej przy użyciu witryny Azure portal.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-vm"></a>Włącz przypisany systemowo tożsamości zarządzanej podczas tworzenia maszyny Wirtualnej

Aby włączyć przypisany systemowo tożsamości zarządzanej maszyny wirtualnej podczas jej tworzenia, Twoje konto musi [Współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) przypisania roli.  Nie dodatkowych Azure przypisań ról katalogu usługi AD są wymagane.

- W obszarze **zarządzania** karcie **tożsamości** sekcji, Przełącz **tożsamości usługi zarządzanej** do **na**.  

![Włączanie tożsamości przypisanych przez system podczas tworzenia maszyny Wirtualnej](./media/msi-qs-configure-portal-windows-vm/enable-system-assigned-identity-vm-creation.png)

Zapoznaj się z następujących przewodników Szybki Start tworzenie maszyny Wirtualnej: 

- [Utwórz maszynę wirtualną Windows za pomocą witryny Azure portal](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine) 
- [Utwórz maszynę wirtualną systemu Linux w witrynie Azure portal](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)


### <a name="enable-system-assigned-managed-identity-on-an-existing-vm"></a>Włącz przypisany systemowo tożsamość zarządzaną istniejącej maszyny wirtualnej

Aby włączyć tożsamość zarządzana na maszynie Wirtualnej, która pierwotnie została aprowizowana bez niego przypisana przez system, konto usługi musi [Współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) przypisania roli.  Nie dodatkowych Azure przypisań ról katalogu usługi AD są wymagane.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, który zawiera maszynę Wirtualną.

2. Przejdź do odpowiednią maszynę wirtualną i wybierz **tożsamości**.

3. W obszarze **przypisanej w systemie**, **stan**, wybierz opcję **na** a następnie kliknij przycisk **Zapisz**:

   ![Zrzut ekranu strony konfiguracji](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

### <a name="remove-system-assigned-managed-identity-from-a-vm"></a>Usuwanie przypisany systemowo tożsamości zarządzanej maszyny Wirtualnej

Aby usunąć przypisany systemowo tożsamości zarządzanej maszyny Wirtualnej, Twoje konto musi [Współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) przypisania roli.  Nie dodatkowych Azure przypisań ról katalogu usługi AD są wymagane.

Jeśli masz maszyny wirtualnej, która nie potrzebuje już przypisany systemowo tożsamość zarządzana:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, który zawiera maszynę Wirtualną. 

2. Przejdź do odpowiednią maszynę wirtualną i wybierz **tożsamości**.

3. W obszarze **przypisanej w systemie**, **stan**, wybierz opcję **poza** a następnie kliknij przycisk **Zapisz**:

   ![Zrzut ekranu strony konfiguracji](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)

## <a name="user-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez użytkownika

 W tej sekcji dowiesz się, jak dodawać i usuwać przypisanych przez użytkownika tożsamości zarządzanej maszyny wirtualnej przy użyciu witryny Azure portal.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-vm"></a>Przypisz tożsamości przypisanych przez użytkownika podczas tworzenia maszyny Wirtualnej

Aby przypisać tożsamości przypisanych przez użytkownika do maszyny Wirtualnej, Twoje konto musi [Współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) i [Operator tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-operator) przypisań ról. Nie dodatkowych Azure przypisań ról katalogu usługi AD są wymagane.

Obecnie witryny Azure portal nie obsługuje przypisywanie przypisanych przez użytkownika tożsamości zarządzanej podczas tworzenia maszyny Wirtualnej. Zamiast tego zobacz następujące artykuły Szybki Start tworzenia maszyny Wirtualnej, aby najpierw utworzyć Maszynę wirtualną, a następnie przejść do następnej sekcji, aby uzyskać szczegółowe informacje na temat przypisywania tożsamości zarządzanej użytkownik przypisany do maszyny Wirtualnej:

- [Utwórz maszynę wirtualną Windows za pomocą witryny Azure portal](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Utwórz maszynę wirtualną systemu Linux w witrynie Azure portal](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-vm"></a>Przypisz tożsamości zarządzanej użytkownik przypisany do istniejącej maszyny Wirtualnej

Aby przypisać tożsamości przypisanych przez użytkownika do maszyny Wirtualnej, Twoje konto musi [Współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) i [Operator tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-operator) przypisań ról. Nie dodatkowych Azure przypisań ról katalogu usługi AD są wymagane.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, który zawiera maszynę Wirtualną.
2. Przejdź do maszyny Wirtualnej i kliknij żądany **tożsamości**, **przypisane do użytkowników** i następnie  **\+Dodaj**.

   ![Dodawanie tożsamości zarządzanej użytkownik przypisany do maszyny Wirtualnej](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot1.png)

3. Kliknij przycisk tożsamości przypisanych przez użytkownika, chcesz dodać do maszyny Wirtualnej, a następnie kliknij przycisk **Dodaj**.

    ![Dodawanie tożsamości zarządzanej użytkownik przypisany do maszyny Wirtualnej](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-vm"></a>Usuwanie przypisanych przez użytkownika tożsamości zarządzanej maszyny Wirtualnej

Aby usunąć tożsamości przypisanych przez użytkownika z maszyny Wirtualnej, Twoje konto musi [Współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) przypisania roli. Nie dodatkowych Azure przypisań ról katalogu usługi AD są wymagane.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, który zawiera maszynę Wirtualną.
2. Przejdź do maszyny Wirtualnej i kliknij żądany **tożsamości**, **przypisane do użytkowników**, nazwę użytkownika przypisane zarządzanych tożsamości, które chcesz usunąć, a następnie kliknij przycisk **Usuń** (kliknij  **Tak** w okienku potwierdzenia).

   ![Usuwanie przypisanych przez użytkownika z tożsamości zarządzanej maszyny Wirtualnej](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vm-screenshot.png)

## <a name="next-steps"></a>Kolejne kroki

- W witrynie Azure portal, zapewniają tożsamość zarządzaną w maszynie Wirtualnej platformy Azure [dostęp do zasobów platformy Azure w innym](howto-assign-access-portal.md).

