---
title: Jak skonfigurować zarządzane tożsamości dla zasobów platformy Azure w zestawie skalowania maszyn wirtualnych
description: Krok po kroku instrukcje dotyczące konfigurowania zarządzania tożsamościami dla zasobów platformy Azure na maszynie wirtualnej zestawu skalowania przy użyciu witryny Azure portal.
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
ms.openlocfilehash: 57f0ec91bd5c72b593d9b28f7d47f691181a6a0f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60290655"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-the-azure-portal"></a>Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej zestawu skalowania przy użyciu witryny Azure portal

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Zarządzanych tożsamości dla zasobów platformy Azure udostępnia usługi platformy Azure przy użyciu automatycznie zarządzanych tożsamości w usłudze Azure Active Directory. Można użyć tej tożsamości do uwierzytelniania na dowolne usługi obsługujące uwierzytelnianie usługi Azure AD bez poświadczeń w kodzie. 

W tym artykule przy użyciu programu PowerShell, nauczysz się wykonywać następujące zarządzanych tożsamości dla operacji tworzenia zasobów platformy Azure w zestawie skalowania maszyn wirtualnych:

- Jeśli jesteś zaznajomiony z zarządzanych tożsamości dla zasobów platformy Azure, zapoznaj się z [sekcji Przegląd](overview.md).
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Do wykonywania operacji zarządzania, w tym artykule, Twoje konto musi następujące przypisania kontroli dostępu opartej na rolach platformy Azure:

    > [!NOTE]
    > Nie dodatkowych Azure przypisań ról katalogu usługi AD wymagane.

    - [Współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) włączyć i Usuń przypisany systemowo tożsamość zarządzaną z zestawu skalowania maszyn wirtualnych.

## <a name="system-assigned-managed-identity"></a>Przypisana przez system tożsamości zarządzanej

W tej sekcji dowiesz się, jak włączyć i wyłączyć przypisany systemowo tożsamość zarządzaną w witrynie Azure portal.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-virtual-machine-scale-set"></a>Włącz przypisany systemowo tożsamości zarządzanej podczas tworzenia zestawu skalowania maszyn wirtualnych

Obecnie witryna Azure portal nie obsługuje Włączanie przypisany systemowo tożsamości zarządzanej podczas tworzenia zestawu skalowania maszyn wirtualnych. Zamiast tego można znaleźć w następujących tworzenia zestawu skalowania maszyn wirtualnych artykuł Szybki Start, aby najpierw utworzyć zestaw skalowania maszyn wirtualnych, a następnie przejść do następnej sekcji, aby uzyskać szczegółowe informacje na temat włączania przypisany systemowo tożsamość zarządzaną w zestawie skalowania maszyn wirtualnych:

- [Tworzenie zestawu skalowania maszyn wirtualnych w witrynie Azure portal](../../virtual-machine-scale-sets/quick-create-portal.md)  

### <a name="enable-system-assigned-managed-identity-on-an-existing-virtual-machine-scale-set"></a>Włącz przypisany systemowo tożsamość zarządzana na podstawie istniejącego zestawu skalowania maszyn wirtualnych

Aby włączyć przypisany systemowo tożsamość zarządzaną w zestawie skalowania maszyny wirtualnej, która pierwotnie została aprowizowana bez niego:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, który zawiera zestaw skalowania maszyn wirtualnych.

2. Przejdź do zestawu skalowania odpowiednią maszynę wirtualną.

3. W obszarze **przypisanej w systemie**, **stan**, wybierz opcję **na** a następnie kliknij przycisk **Zapisz**:

   ![Zrzut ekranu strony konfiguracji](./media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png) 

### <a name="remove-system-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Usuń przypisany systemowo tożsamość zarządzaną z zestawu skalowania maszyn wirtualnych

Jeśli masz zestaw skalowania maszyn wirtualnych, który nie wymaga tożsamości zarządzanej przypisana przez system:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, który zawiera zestaw skalowania maszyn wirtualnych. Upewnij się, Twoje konto należy do roli, który zapewnia również uprawnienia do zapisu w zestawie skalowania maszyn wirtualnych.

2. Przejdź do zestawu skalowania odpowiednią maszynę wirtualną.

3. W obszarze **przypisanej w systemie**, **stan**, wybierz opcję **poza** a następnie kliknij przycisk **Zapisz**:

   ![Zrzut ekranu strony konfiguracji](./media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)

## <a name="user-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez użytkownika

W tej sekcji dowiesz się, jak dodawać i usuwać przypisane przez użytkownika tożsamości zarządzanej maszyny wirtualnej zestawu skalowania przy użyciu witryny Azure portal.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Przypisz przypisanych przez użytkownika tożsamości zarządzanej podczas tworzenia zestawu skalowania maszyn wirtualnych

Obecnie witryna Azure portal nie obsługuje przypisywanie przypisanych przez użytkownika tożsamości zarządzanej podczas tworzenia zestawu skalowania maszyn wirtualnych. Zamiast tego można znaleźć w następujących tworzenia zestawu skalowania maszyn wirtualnych artykuł Szybki Start, aby najpierw utworzyć zestaw skalowania maszyn wirtualnych, a następnie przejść do następnej sekcji, aby uzyskać szczegółowe informacje na temat przypisywania tożsamości zarządzanej użytkownik przypisany do niego:

- [Tworzenie zestawu skalowania maszyn wirtualnych w witrynie Azure portal](../../virtual-machine-scale-sets/quick-create-portal.md)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Przypisz tożsamości zarządzanej użytkownik przypisany do istniejącego zestawu skalowania maszyn wirtualnych

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, który zawiera zestaw skalowania maszyn wirtualnych.
2. Przejdź do zestawu skalowania odpowiednią maszynę wirtualną, a następnie kliknij przycisk **tożsamości**, **przypisane do użytkowników** i następnie  **\+Dodaj**.

   ![Dodawanie tożsamości przypisanych przez użytkownika do zestawu skalowania maszyn wirtualnych](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vmss-screenshot1.png)

3. Kliknij przycisk tożsamości przypisanych przez użytkownika, chcesz dodać do zestawu skalowania maszyn wirtualnych, a następnie kliknij przycisk **Dodaj**.
   
   ![Dodawanie tożsamości przypisanych przez użytkownika do zestawu skalowania maszyn wirtualnych](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Usuń tożsamość zarządzaną z przypisanego przez użytkownika z zestawu skalowania maszyn wirtualnych

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, który zawiera maszynę Wirtualną.
2. Przejdź do zestawu skalowania odpowiednią maszynę wirtualną, a następnie kliknij przycisk **tożsamości**, **przypisane do użytkowników**, nazwa użytkownika przypisane zarządzanych tożsamości, które chcesz usunąć, a następnie kliknij przycisk **Usuń** (kliknij **tak** w okienku potwierdzenia).

   ![Usuwanie tożsamości przypisanych przez użytkownika z zestawu skalowania maszyn wirtualnych](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vmss-screenshot.png)


## <a name="next-steps"></a>Kolejne kroki

- Przy użyciu witryny Azure portal, zapewniają zestawu skalowania maszyn wirtualnych platformy Azure tożsamość zarządzaną [dostęp do zasobów platformy Azure w innym](howto-assign-access-portal.md).


