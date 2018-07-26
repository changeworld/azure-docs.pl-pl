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
ms.openlocfilehash: 93c532cf2864db28b580303ecefec8b6dbed65f6
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2018
ms.locfileid: "39257763"
---
# <a name="configure-a-virtual-machine-scale-set-managed-service-identity-using-the-azure-portal"></a>Konfigurowanie maszyny wirtualnej zestawu skalowania przy użyciu witryny Azure portal tożsamości usługi zarządzanej

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Tożsamość usługi zarządzanej udostępnia usługi platformy Azure przy użyciu automatycznie zarządzanych tożsamości w usłudze Azure Active Directory. Można użyć tej tożsamości do uwierzytelniania na dowolne usługi obsługujące uwierzytelnianie usługi Azure AD bez poświadczeń w kodzie. 

W tym artykule nauczysz pozwala włączać i wyłączać system przypisane do tożsamości dla zestawu skalowania maszyn wirtualnych przy użyciu witryny Azure portal. Przypisywanie i usuwanie tożsamości przypisanych przez użytkownika z zestawu skalowania maszyn wirtualnych platformy Azure nie jest obecnie obsługiwane w witrynie Azure portal.

> [!NOTE]
> Obecnie operacje tożsamości przypisanych przez użytkownika nie są obsługiwane za pośrednictwem witryny Azure Portal. Wracaj tutaj, aby zapoznać się z aktualizacjami.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli jesteś zaznajomiony z tożsamości usługi zarządzanej, zapoznaj się z [sekcji Przegląd](overview.md).
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Do wykonywania operacji zarządzania, w tym artykule, Twoje konto musi następujące przypisania roli:
    - [Współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) włączyć i Usuń przypisanej tożsamość zarządzaną z zestawu skalowania maszyn wirtualnych w systemie.

## <a name="managed-service-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Tożsamość usługi zarządzanej podczas tworzenia zestawu skalowania maszyn wirtualnych platformy Azure

Obecnie tworzenie maszyny Wirtualnej w witrynie Azure portal nie obsługuje operacji tożsamości usługi zarządzanej. Zamiast tego można znaleźć następujących maszyn wirtualnych platformy Azure skalowania zestaw tworzenia z artykułem Szybki Start do utworzenia zestawu skalowania maszyn wirtualnych platformy Azure:

- [Tworzenie zestawu skalowania maszyn wirtualnych w witrynie Azure portal](../../virtual-machine-scale-sets/quick-create-portal.md)  

Przejdź do następnej sekcji, aby uzyskać szczegółowe informacje na temat włączania tożsamości usługi zarządzanej w zestawie skalowania maszyn wirtualnych.

## <a name="enable-managed-service-identity-on-an-existing-azure-vmms"></a>Włączanie tożsamości usługi zarządzanej w istniejących VMMS platformy Azure

Aby włączyć system przypisane tożsamości na maszynie Wirtualnej, która pierwotnie została aprowizowana bez niego:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, który zawiera zestaw skalowania maszyn wirtualnych.

2. Przejdź do zestawu skalowania odpowiednią maszynę wirtualną.

3. Włączanie tożsamości przypisanej w systemie na maszynie Wirtualnej, wybierając pozycję "Yes" w obszarze "Tożsamość usługi zarządzane", a następnie kliknij przycisk **Zapisz**. Ta operacja może potrwać 60 sekund lub więcej, aby wykonać:

   [![Zrzut ekranu strony konfiguracji](../managed-service-identity/media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png)](../managed-service-identity/media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png#lightbox)  

## <a name="remove-managed-service-identity-from-an-azure-virtual-machine-scale-set"></a>Usuwanie tożsamości usługi zarządzanej zestaw skalowania maszyn wirtualnych platformy Azure

Jeśli masz zestaw skalowania maszyn wirtualnych, który nie wymaga tożsamości usługi zarządzanej:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, który zawiera zestaw skalowania maszyn wirtualnych. Upewnij się, Twoje konto należy do roli, który zapewnia również uprawnienia do zapisu w zestawie skalowania maszyn wirtualnych.

2. Przejdź do zestawu skalowania odpowiednią maszynę wirtualną.

3. Wyłącz system przypisane tożsamości na maszynie Wirtualnej, wybierając pozycję "No", w obszarze "Tożsamość usługi zarządzane", a następnie kliknij przycisk Zapisz. Ta operacja może potrwać 60 sekund lub więcej, aby wykonać:

   ![Zrzut ekranu strony konfiguracji](../managed-service-identity/media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)  

## <a name="related-content"></a>Powiązana zawartość

- Aby zapoznać się z omówieniem tożsamości usługi zarządzanej, zobacz [Przegląd](overview.md).

## <a name="next-steps"></a>Kolejne kroki

- Za pomocą witryny Azure portal, zapewniają zestawu skalowania maszyn wirtualnych platformy Azure tożsamości usługi zarządzanej [dostęp do zasobów platformy Azure w innym](howto-assign-access-portal.md).

W poniższej sekcji komentarzy umożliwia opinią i Pomóż nam analizy i połącz kształt naszej zawartości.
