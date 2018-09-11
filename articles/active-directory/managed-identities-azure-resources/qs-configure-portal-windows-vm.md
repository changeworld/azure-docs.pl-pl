---
title: Jak skonfigurować zarządzane tożsamości dla zasobów platformy Azure na Maszynie wirtualnej platformy Azure przy użyciu witryny Azure portal
description: Krok po kroku instrukcje dotyczące konfigurowania zarządzania tożsamościami dla zasobów platformy Azure na Maszynie wirtualnej platformy Azure przy użyciu witryny Azure portal.
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
ms.openlocfilehash: f729a7972a36d53cb8f4f3769aedde4ca2005d80
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2018
ms.locfileid: "44346135"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-vm-using-the-azure-portal"></a>Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie Wirtualnej przy użyciu witryny Azure portal

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Zarządzanych tożsamości dla zasobów platformy Azure udostępnia usługi platformy Azure przy użyciu automatycznie zarządzanych tożsamości w usłudze Azure Active Directory. Można użyć tej tożsamości do uwierzytelniania na dowolne usługi obsługujące uwierzytelnianie usługi Azure AD bez poświadczeń w kodzie. 

W tym artykule dowiesz się, jak włączyć i wyłączyć systemu i tożsamości przypisanych przez użytkownika zarządzanego dla maszyny wirtualnej (maszyny Wirtualnej platformy Azure), przy użyciu witryny Azure portal. 

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli jesteś zaznajomiony z zarządzanych tożsamości dla zasobów platformy Azure, zapoznaj się z [sekcji Przegląd](overview.md).
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Do wykonywania operacji zarządzania, w tym artykule, Twoje konto musi następujące przypisania kontroli dostępu opartej na rolach platformy Azure:
    > [!NOTE]
    > Nie dodatkowych Azure przypisań ról katalogu usługi AD wymagane.
    - [Współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) włączyć i Usuń przypisany systemowo tożsamości zarządzanej maszyny wirtualnej platformy Azure.

## <a name="system-assigned-managed-identity"></a>Przypisana przez system tożsamości zarządzanej

W tej sekcji dowiesz się, jak włączanie i wyłączanie przypisany systemowo tożsamości zarządzanej dla maszyny Wirtualnej przy użyciu witryny Azure portal.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-vm"></a>Włącz przypisany systemowo tożsamości zarządzanej podczas tworzenia maszyny Wirtualnej

Obecnie witryna Azure portal nie obsługuje Włączanie tożsamości przypisanych przez system podczas tworzenia maszyny Wirtualnej. Zamiast tego zobacz następujące artykuły Szybki Start tworzenia maszyny Wirtualnej, aby najpierw utworzyć Maszynę wirtualną, a następnie przejść do następnej sekcji, aby uzyskać szczegółowe informacje na temat włączania tożsamości przypisanych przez system na maszynie Wirtualnej:

- [Utwórz maszynę wirtualną Windows za pomocą witryny Azure portal](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Utwórz maszynę wirtualną systemu Linux w witrynie Azure portal](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)  

### <a name="enable-system-assigned-managed-identity-on-an-existing-vm"></a>Włącz przypisany systemowo tożsamość zarządzaną istniejącej maszyny wirtualnej

Aby włączyć przypisany systemowo tożsamości zarządzanej maszyny wirtualnej, która pierwotnie została aprowizowana bez niego:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, który zawiera maszynę Wirtualną.

2. Przejdź do odpowiednią maszynę wirtualną i wybierz **tożsamości**.

3. W obszarze **przypisanej w systemie**, **stan**, wybierz opcję **na** a następnie kliknij przycisk **Zapisz**:

   ![Zrzut ekranu strony konfiguracji](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

### <a name="remove-system-assigned-managed-identity-from-a-vm"></a>Usuwanie przypisany systemowo tożsamości zarządzanej maszyny Wirtualnej

Jeśli masz maszyny wirtualnej, która nie potrzebuje już przypisany systemowo tożsamość zarządzana:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, który zawiera maszynę Wirtualną. 

2. Przejdź do odpowiednią maszynę wirtualną i wybierz **tożsamości**.

3. W obszarze **przypisanej w systemie**, **stan**, wybierz opcję **poza** a następnie kliknij przycisk **Zapisz**:

   ![Zrzut ekranu strony konfiguracji](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)

## <a name="user-assigned-managed-identity"></a>Przypisane przez użytkownika z tożsamości zarządzanej

 W tej sekcji dowiesz się, jak dodawać i usuwać przypisanych przez użytkownika tożsamości zarządzanej maszyny wirtualnej przy użyciu witryny Azure portal.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-vm"></a>Przypisz tożsamości przypisanych przez użytkownika podczas tworzenia maszyny Wirtualnej

Obecnie witryny Azure portal nie obsługuje przypisywanie przypisanych przez użytkownika tożsamości zarządzanej podczas tworzenia maszyny Wirtualnej. Zamiast tego zobacz następujące artykuły Szybki Start tworzenia maszyny Wirtualnej, aby najpierw utworzyć Maszynę wirtualną, a następnie przejść do następnej sekcji, aby uzyskać szczegółowe informacje na temat przypisywania tożsamości zarządzanej użytkownik przypisany do maszyny Wirtualnej:

- [Utwórz maszynę wirtualną Windows za pomocą witryny Azure portal](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Utwórz maszynę wirtualną systemu Linux w witrynie Azure portal](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-vm"></a>Przypisz tożsamości zarządzanej użytkownik przypisany do istniejącej maszyny Wirtualnej

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, który zawiera maszynę Wirtualną.
2. Przejdź do maszyny Wirtualnej i kliknij żądany **tożsamości**, **przypisane do użytkowników** i następnie  **\+Dodaj**.

   ![Dodawanie tożsamości zarządzanej użytkownik przypisany do maszyny Wirtualnej](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot1.png)

3. Kliknij przycisk tożsamości przypisanych przez użytkownika, chcesz dodać do maszyny Wirtualnej, a następnie kliknij przycisk **Dodaj**.

    ![Dodawanie tożsamości zarządzanej użytkownik przypisany do maszyny Wirtualnej](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-vm"></a>Usuwanie przypisanych przez użytkownika tożsamości zarządzanej maszyny Wirtualnej

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, który zawiera maszynę Wirtualną.
2. Przejdź do maszyny Wirtualnej i kliknij żądany **tożsamości**, **przypisane do użytkowników**, nazwę użytkownika przypisane zarządzanych tożsamości, które chcesz usunąć, a następnie kliknij przycisk **Usuń** (kliknij  **Tak** w okienku potwierdzenia).

   ![Usuwanie przypisanych przez użytkownika z tożsamości zarządzanej maszyny Wirtualnej](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vm-screenshot.png)

## <a name="next-steps"></a>Kolejne kroki

- W witrynie Azure portal, zapewniają tożsamość zarządzaną w maszynie Wirtualnej platformy Azure [dostęp do zasobów platformy Azure w innym](howto-assign-access-portal.md).

