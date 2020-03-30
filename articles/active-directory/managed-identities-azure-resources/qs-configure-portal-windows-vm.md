---
title: Konfigurowanie tożsamości zarządzanych przy użyciu witryny Azure portal — Azure AD
description: Instrukcje krok po kroku dotyczące konfigurowania tożsamości zarządzanych dla zasobów platformy Azure na maszynie Wirtualnej platformy Azure przy użyciu witryny Azure portal.
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
ms.openlocfilehash: 75971dbd35a6b29306bc87988423ab662a343705
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266665"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-vm-using-the-azure-portal"></a>Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure na maszynie Wirtualnej przy użyciu witryny Azure portal

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Tożsamości zarządzane dla zasobów platformy Azure zapewniają usługom platformy Azure automatycznie zarządzaną tożsamość w usłudze Azure Active Directory. Tej tożsamości można użyć do uwierzytelniania do dowolnej usługi, która obsługuje uwierzytelnianie usługi Azure AD, bez poświadczeń w kodzie. 

W tym artykule dowiesz się, jak włączyć i wyłączyć tożsamości zarządzane systemu i przypisane przez użytkownika dla maszyny wirtualnej platformy Azure przy użyciu witryny Azure portal. 

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz tożsamości zarządzanych dla zasobów platformy Azure, zapoznaj się z [sekcją omówienie](overview.md).
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.

## <a name="system-assigned-managed-identity"></a>Tożsamość zarządzana przypisana do systemu

W tej sekcji dowiesz się, jak włączyć i wyłączyć przypisaną do systemu tożsamość zarządzaną dla maszyny Wirtualnej przy użyciu witryny Azure portal.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-vm"></a>Włączanie tożsamości zarządzanej przypisanej przez system podczas tworzenia maszyny wirtualnej

Aby włączyć tożsamość zarządzaną przypisaną do systemu na maszynie wirtualnej podczas jej tworzenia, konto wymaga przypisania roli [współautora maszyny wirtualnej.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Nie są wymagane żadne dodatkowe przypisania ról katalogu usługi Azure AD.

- W obszarze **karta Zarządzanie** w sekcji **Tożsamość** przełącz **tożsamość usługi zarządzanej** **na Włączone**.  

![Włączanie tożsamości przypisanej przez system podczas tworzenia maszyny Wirtualnej](./media/msi-qs-configure-portal-windows-vm/enable-system-assigned-identity-vm-creation.png)

Aby utworzyć maszynę wirtualną, należy zapoznać się z następującymi przewodnikami Szybki start: 

- [Tworzenie maszyny wirtualnej z systemem Windows za pomocą witryny Azure Portal](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine) 
- [Tworzenie maszyny wirtualnej z systemem Linux za pomocą witryny Azure Portal](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)


### <a name="enable-system-assigned-managed-identity-on-an-existing-vm"></a>Włączanie tożsamości zarządzanej przypisanej przez system na istniejącej maszynie wirtualnej

Aby włączyć tożsamość zarządzaną przypisaną do systemu na maszynie wirtualnej, która została pierwotnie zainicjowana bez niej, twoje konto wymaga przypisania roli [współautora maszyny wirtualnej.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Nie są wymagane żadne dodatkowe przypisania ról katalogu usługi Azure AD.

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, która zawiera maszynę wirtualną.

2. Przejdź do żądanej maszyny wirtualnej i wybierz **opcję Tożsamość**.

3. W **obszarze Przypisany system**, **Stan**, wybierz pozycję **Włączone,** a następnie kliknij przycisk **Zapisz**:

   ![Zrzut ekranu strony konfiguracji](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

### <a name="remove-system-assigned-managed-identity-from-a-vm"></a>Usuwanie tożsamości zarządzanej przypisanej przez system z maszyny Wirtualnej

Aby usunąć tożsamość zarządzaną przypisaną do systemu z maszyny wirtualnej, twoje konto wymaga przypisania roli [Współautor maszyny wirtualnej.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Nie są wymagane żadne dodatkowe przypisania ról katalogu usługi Azure AD.

Jeśli masz maszynę wirtualną, która nie potrzebuje już tożsamości zarządzanej przypisanej do systemu:

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, która zawiera maszynę wirtualną. 

2. Przejdź do żądanej maszyny wirtualnej i wybierz **opcję Tożsamość**.

3. W **obszarze Przypisany system**, **Stan**, wybierz pozycję **Wyłączone,** a następnie kliknij przycisk **Zapisz**:

   ![Zrzut ekranu strony konfiguracji](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)

## <a name="user-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez użytkownika

 W tej sekcji dowiesz się, jak dodać i usunąć tożsamość zarządzaną przypisaną przez użytkownika z maszyny Wirtualnej przy użyciu portalu Azure.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-vm"></a>Przypisywanie tożsamości przypisanej przez użytkownika podczas tworzenia maszyny Wirtualnej

Aby przypisać tożsamość przypisaną przez użytkownika do maszyny Wirtualnej, twoje konto wymaga przypisania roli [Współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) i [Operator tożsamości zarządzanej.](/azure/role-based-access-control/built-in-roles#managed-identity-operator) Nie są wymagane żadne dodatkowe przypisania ról katalogu usługi Azure AD.

Obecnie witryna Azure portal nie obsługuje przypisywania tożsamości zarządzanej przypisanej przez użytkownika podczas tworzenia maszyny Wirtualnej. Zamiast tego zapoznaj się z jednym z następujących artykułów szybkiego startu tworzenia maszyn wirtualnych, aby najpierw utworzyć maszynę wirtualną, a następnie przejdź do następnej sekcji, aby uzyskać szczegółowe informacje na temat przypisywania tożsamości zarządzanej przypisanej przez użytkownika do maszyny Wirtualnej:

- [Tworzenie maszyny wirtualnej z systemem Windows za pomocą witryny Azure Portal](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Tworzenie maszyny wirtualnej z systemem Linux za pomocą witryny Azure Portal](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-vm"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika do istniejącej maszyny Wirtualnej

Aby przypisać tożsamość przypisaną przez użytkownika do maszyny Wirtualnej, twoje konto wymaga przypisania roli [Współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) i [Operator tożsamości zarządzanej.](/azure/role-based-access-control/built-in-roles#managed-identity-operator) Nie są wymagane żadne dodatkowe przypisania ról katalogu usługi Azure AD.

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, która zawiera maszynę wirtualną.
2. Przejdź do żądanej maszyny Wirtualnej i kliknij ** \+** pozycję **Tożsamość**, **przypisany użytkownik,** a następnie dodaj .

   ![Dodawanie tożsamości zarządzanej przypisanej przez użytkownika do maszyny Wirtualnej](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot1.png)

3. Kliknij tożsamość przypisaną przez użytkownika, którą chcesz dodać do maszyny Wirtualnej, a następnie kliknij pozycję **Dodaj**.

    ![Dodawanie tożsamości zarządzanej przypisanej przez użytkownika do maszyny Wirtualnej](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-vm"></a>Usuwanie tożsamości zarządzanej przypisanej przez użytkownika z maszyny Wirtualnej

Aby usunąć tożsamość przypisaną przez użytkownika z maszyny Wirtualnej, twoje konto wymaga przypisania roli [Współautor maszyny wirtualnej.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) Nie są wymagane żadne dodatkowe przypisania ról katalogu usługi Azure AD.

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, która zawiera maszynę wirtualną.
2. Przejdź do żądanej maszyny Wirtualnej i kliknij pozycję **Tożsamość**, **przypisany użytkownik**, nazwę tożsamości zarządzanej przypisanej przez użytkownika, którą chcesz usunąć, a następnie kliknij przycisk **Usuń** (kliknij przycisk **Tak** w okienku potwierdzenia).

   ![Usuwanie tożsamości zarządzanej przypisanej przez użytkownika z maszyny Wirtualnej](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vm-screenshot.png)

## <a name="next-steps"></a>Następne kroki

- Korzystając z witryny Azure portal, zapewnij maszynom wirtualnym zarządzany dostęp do tożsamości [innej usługi Azure.](howto-assign-access-portal.md)

