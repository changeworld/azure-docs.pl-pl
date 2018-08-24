---
title: Konfigurowanie tożsamości usługi zarządzanej na maszynie wirtualnej platformy Azure zestawu skalowania przy użyciu witryny Azure portal
description: Krok po kroku instrukcje dotyczące konfigurowania tożsamości usługi zarządzanej w usłudze VMSS platformy Azure, przy użyciu witryny Azure portal.
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
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: bfd63262a1d5568223b4e4077e2f8c987b7ec0d4
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/23/2018
ms.locfileid: "42746740"
---
# <a name="configure-a-virtual-machine-scale-set-managed-service-identity-using-the-azure-portal"></a>Konfigurowanie maszyny wirtualnej zestawu skalowania przy użyciu witryny Azure portal tożsamości usługi zarządzanej

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Tożsamość usługi zarządzanej udostępnia usługi platformy Azure przy użyciu automatycznie zarządzanych tożsamości w usłudze Azure Active Directory. Można użyć tej tożsamości do uwierzytelniania na dowolne usługi obsługujące uwierzytelnianie usługi Azure AD bez poświadczeń w kodzie. 

W tym artykule dowiesz się, jak włączanie i wyłączanie systemowych i użytkownika przypisane do tożsamości dla maszyny wirtualnej zestawu skalowania przy użyciu witryny Azure portal.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli jesteś zaznajomiony z tożsamości usługi zarządzanej, zapoznaj się z [sekcji Przegląd](overview.md).
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Do wykonywania operacji zarządzania, w tym artykule, Twoje konto musi następujące przypisania roli:
    - [Współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) włączyć i Usuń przypisanej tożsamość zarządzaną z zestawu skalowania maszyn wirtualnych w systemie.

## <a name="system-assigned-identity"></a>Tożsamości przypisanej w systemie 

W tej sekcji dowiesz się, jak włączyć i wyłączyć system przypisane tożsamość maszyny wirtualnej zestawu skalowania przy użyciu witryny Azure portal.

### <a name="enable-system-assigned-identity-during-creation-of-a-virtual-machine-scale-set"></a>Włączanie tożsamości przypisanej w systemie podczas tworzenia zestawu skalowania maszyn wirtualnych

Witryny Azure portal nie obsługuje obecnie włączenie systemu tożsamości przypisanej podczas tworzenia zestawu skalowania maszyn wirtualnych. Zamiast tego należy odwoływać się do następujących maszyn wirtualnych skalowania zestaw tworzenia z artykułem Szybki Start do utworzenia zestawu skalowania maszyn wirtualnych, a następnie przejść do następnej sekcji, aby uzyskać szczegółowe informacje na temat włączania przypisanej tożsamości w zestawie skalowania maszyn wirtualnych w systemie:

- [Tworzenie zestawu skalowania maszyn wirtualnych w witrynie Azure portal](../../virtual-machine-scale-sets/quick-create-portal.md)  

### <a name="enable-system-assigned-identity-on-an-existing-virtual-machine-scale-set"></a>Włączanie tożsamości przypisanej w systemie, na podstawie istniejącego zestawu skalowania maszyn wirtualnych

Aby włączyć tożsamości przypisanej w systemie w zestawie skalowania maszyny wirtualnej, która pierwotnie została aprowizowana bez niego:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, który zawiera zestaw skalowania maszyn wirtualnych.

2. Przejdź do zestawu skalowania odpowiednią maszynę wirtualną.

3. Włączanie tożsamości przypisanej w systemie na maszynie Wirtualnej, wybierając pozycję "Yes" w obszarze "Tożsamość usługi zarządzane", a następnie kliknij przycisk **Zapisz**. Ta operacja może potrwać 60 sekund lub więcej, aby wykonać:

   [![Zrzut ekranu strony konfiguracji](../managed-service-identity/media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png)](../managed-service-identity/media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png#lightbox)  

### <a name="remove-system-assigned-identity-from-a-virtual-machine-scale-set"></a>Usuń tożsamości przypisanej w systemie z zestawu skalowania maszyn wirtualnych

Jeśli masz już zestaw skalowania maszyn wirtualnych wymaga systemu tożsamości przypisanej:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, który zawiera zestaw skalowania maszyn wirtualnych. Upewnij się, Twoje konto należy do roli, który zapewnia również uprawnienia do zapisu w zestawie skalowania maszyn wirtualnych.

2. Przejdź do zestawu skalowania odpowiednią maszynę wirtualną.

3. Wyłącz system przypisane tożsamości na maszynie Wirtualnej, wybierając pozycję "No", w obszarze "Tożsamość usługi zarządzane", a następnie kliknij przycisk Zapisz. Ta operacja może potrwać 60 sekund lub więcej, aby wykonać:

   ![Zrzut ekranu strony konfiguracji](../managed-service-identity/media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)

## <a name="user-assigned-identity"></a>Tożsamości przypisanych przez użytkownika

W tej sekcji dowiesz się, jak dodać i usunąć użytkownika z tożsamości przypisanej z maszyny wirtualnej zestawu skalowania przy użyciu witryny Azure portal.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Przypisz tożsamości przypisanych przez użytkownika podczas tworzenia zestawu skalowania maszyn wirtualnych

Obecnie Azure portal obsługuje przypisanie użytkownika tożsamości przypisanej podczas tworzenia zestawu skalowania maszyn wirtualnych. Zamiast tego należy odwoływać się do następujących maszyn wirtualnych skalowania zestaw tworzenia z artykułem Szybki Start do utworzenia zestawu skalowania maszyn wirtualnych, a następnie przejść do następnej sekcji, aby uzyskać szczegółowe informacje na temat przypisywania użytkownika tożsamości przypisanej do niego:

- [Tworzenie zestawu skalowania maszyn wirtualnych w witrynie Azure portal](../../virtual-machine-scale-sets/quick-create-portal.md)

### <a name="assign-a-user-assigned-identity-to-an-existing-virtual-machine-scale-set"></a>Przypisz tożsamości przypisanych przez użytkownika do istniejącego zestawu skalowania maszyn wirtualnych

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, który zawiera zestaw skalowania maszyn wirtualnych.
2. Przejdź do zestawu skalowania odpowiednią maszynę wirtualną, a następnie kliknij przycisk **tożsamości**, **przypisane do użytkowników** i następnie  **\+Dodaj**.

   ![Dodawanie tożsamości przypisanych przez użytkownika do zestawu skalowania maszyn wirtualnych](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vmss-screenshot1.png)

3. Kliknij przycisk tożsamości przypisanych przez użytkownika, chcesz dodać do zestawu skalowania maszyn wirtualnych, a następnie kliknij przycisk **Dodaj**.
   
   ![Dodawanie tożsamości przypisanych przez użytkownika do zestawu skalowania maszyn wirtualnych](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-identity-from-a-virtual-machine-scale-set"></a>Usuwanie tożsamości przypisanych przez użytkownika z zestawu skalowania maszyn wirtualnych

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, który zawiera maszynę Wirtualną.
2. Przejdź do zestawu skalowania odpowiednią maszynę wirtualną, a następnie kliknij przycisk **tożsamości**, **przypisane do użytkowników**, nazwy tożsamości przypisanych przez użytkownika, które chcesz usunąć, a następnie kliknij przycisk **Usuń** () Kliknij przycisk **tak** w okienku potwierdzenia).

   ![Usuwanie tożsamości przypisanych z zestawu skalowania maszyn wirtualnych przez użytkownika](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vmss-screenshot.png)


## <a name="related-content"></a>Powiązana zawartość

- Aby zapoznać się z omówieniem tożsamości usługi zarządzanej, zobacz [Przegląd](overview.md).

## <a name="next-steps"></a>Kolejne kroki

- Za pomocą witryny Azure portal, zapewniają zestawu skalowania maszyn wirtualnych platformy Azure tożsamości usługi zarządzanej [dostęp do zasobów platformy Azure w innym](howto-assign-access-portal.md).

W poniższej sekcji komentarzy umożliwia opinią i Pomóż nam analizy i połącz kształt naszej zawartości.
