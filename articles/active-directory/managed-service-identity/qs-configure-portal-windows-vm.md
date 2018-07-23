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
ms.openlocfilehash: 81aa8153198f69abd1722f97462927a0c242d4e7
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2018
ms.locfileid: "39186163"
---
# <a name="configure-a-vm-managed-service-identity-using-the-azure-portal"></a>Skonfiguruj indywidualne tożsamości usługi zarządzanej maszyny Wirtualnej przy użyciu witryny Azure portal

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Tożsamość usługi zarządzanej udostępnia usługi platformy Azure przy użyciu automatycznie zarządzanych tożsamości w usłudze Azure Active Directory. Można użyć tej tożsamości do uwierzytelniania na dowolne usługi obsługujące uwierzytelnianie usługi Azure AD bez poświadczeń w kodzie. 

W tym artykule dowiesz się, jak włączyć i wyłączyć system tożsamości przypisanej do maszyny Wirtualnej platformy Azure, przy użyciu witryny Azure portal. Przypisywanie i usuwanie tożsamości przypisanych przez z maszyn wirtualnych platformy Azure użytkownika nie jest obecnie obsługiwane za pośrednictwem witryny Azure Portal.

> [!NOTE]
> Obecnie operacje tożsamości przypisanych przez użytkownika nie są obsługiwane za pośrednictwem witryny Azure Portal. Wracaj tutaj, aby zapoznać się z aktualizacjami. 

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli jesteś zaznajomiony z tożsamości usługi zarządzanej, zapoznaj się z [sekcji Przegląd](overview.md).
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Do wykonywania operacji zarządzania, w tym artykule, Twoje konto musi następujące przypisania roli:
    - [Współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) Włączanie i usuwanie tożsamości usługi zarządzanej maszyny wirtualnej platformy Azure.

## <a name="managed-service-identity-during-creation-of-an-azure-vm"></a>Tożsamość usługi zarządzanej podczas tworzenia maszyny Wirtualnej platformy Azure

Obecnie tworzenie maszyny Wirtualnej w witrynie Azure portal nie obsługuje operacji tożsamości usługi zarządzanej. Zamiast tego zapoznaj się z jednym z następujących artykułów szybkiego startu tworzenia maszyny Wirtualnej do utworzenia maszyny Wirtualnej:

- [Utwórz maszynę wirtualną Windows za pomocą witryny Azure portal](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Utwórz maszynę wirtualną systemu Linux w witrynie Azure portal](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)  

Przejdź do następnej sekcji, aby uzyskać szczegółowe informacje na temat włączania tożsamości usługi zarządzanej maszyny wirtualnej.

## <a name="enable-managed-service-identity-on-an-existing-azure-vm"></a>Włączanie tożsamości usługi zarządzanej istniejącej maszyny wirtualnej platformy Azure

Aby włączyć system przypisane tożsamości na maszynie Wirtualnej, która pierwotnie została aprowizowana bez niego:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, który zawiera maszynę Wirtualną.

2. Przejdź do odpowiednią maszynę wirtualną, a następnie wybierz stronę "Configuration".

3. Włączanie tożsamości przypisanej w systemie na maszynie Wirtualnej, wybierając pozycję "Yes" w obszarze "Tożsamość usługi zarządzane", a następnie kliknij przycisk **Zapisz**. Ta operacja może potrwać 60 sekund lub więcej, aby wykonać:

   > [!NOTE]
   > Dodawanie tożsamości przypisanych przez użytkownika do maszyny Wirtualnej nie jest obecnie obsługiwane za pośrednictwem witryny Azure Portal.

   ![Zrzut ekranu strony konfiguracji](../managed-service-identity/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

## <a name="remove-managed-service-identity-from-an-azure-vm"></a>Usuwanie tożsamości usługi zarządzanej maszyny Wirtualnej platformy Azure

Jeśli masz maszyny wirtualnej, która nie wymaga tożsamości przypisanej w systemie:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, który zawiera maszynę Wirtualną. 

2. Przejdź do odpowiednią maszynę wirtualną, a następnie wybierz stronę "Configuration".

3. Wyłącz system przypisane tożsamości na maszynie Wirtualnej, wybierając pozycję "No", w obszarze "Tożsamość usługi zarządzane", a następnie kliknij przycisk Zapisz. Ta operacja może potrwać 60 sekund lub więcej, aby wykonać:

    > [!NOTE]
    > Dodawanie tożsamości przypisanych przez użytkownika do maszyny Wirtualnej nie jest obecnie obsługiwane za pośrednictwem witryny Azure Portal.

   ![Zrzut ekranu strony konfiguracji](../managed-service-identity/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)  

## <a name="related-content"></a>Powiązana zawartość

- Aby zapoznać się z omówieniem tożsamości usługi zarządzanej, zobacz [Przegląd](overview.md).

## <a name="next-steps"></a>Kolejne kroki

- Za pomocą witryny Azure portal nadaj tożsamości usługi zarządzanej maszynie Wirtualnej platformy Azure [dostęp do zasobów platformy Azure w innym](howto-assign-access-portal.md).

