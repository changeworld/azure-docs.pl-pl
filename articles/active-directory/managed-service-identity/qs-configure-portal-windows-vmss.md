---
title: Skonfiguruj MSI skali maszyny wirtualnej platformy Azure, ustawić za pomocą portalu Azure
description: Krok kroku instrukcje dotyczące konfigurowania zarządzane tożsamości usługi (MSI) na VMSS Azure, przy użyciu portalu Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: c915c692a12781538e10d367d40e3efe473a6853
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
ms.locfileid: "33929047"
---
# <a name="configure-a-vmss-managed-service-identity-msi-using-the-azure-portal"></a>Skonfiguruj VMSS zarządzane usługi tożsamości (MSI) przy użyciu portalu Azure

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Tożsamość usługi zarządzanej zapewnia usług platformy Azure przy użyciu tożsamości automatycznie zarządzane w usłudze Azure Active Directory. Ta tożsamość służy do uwierzytelniania do dowolnej usługi obsługującej uwierzytelniania usługi Azure AD, bez konieczności poświadczeń w kodzie. 

W tym artykule dowiesz się, jak włączyć lub wyłączyć system przypisane tożsamości dla VMSS, przy użyciu portalu Azure. Przypisywanie i usuwanie użytkownika z przypisanym tożsamości z VMSS Azure nie jest obecnie obsługiwane za pośrednictwem portalu Azure.

> [!NOTE]
> Obecnie tożsamości operacje przypisane przez użytkownika nie są obsługiwane za pośrednictwem portalu Azure. Sprawdzanie dostępności aktualizacji.

## <a name="prerequisites"></a>Wymagania wstępne


- Jeśli znasz zarządzane tożsamość usługi, zapoznaj się [sekcji Przegląd](overview.md).
- Jeśli nie masz jeszcze konta platformy Azure, [Załóż bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.

## <a name="managed-service-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Zarządzane tożsamość usługi podczas tworzenia zestawu skali maszyny wirtualnej platformy Azure

Tworzenie maszyny Wirtualnej za pośrednictwem portalu Azure nie obsługuje obecnie, operacje zarządzane tożsamości usługi. Zamiast tego można znaleźć maszyny wirtualnej platformy Azure skali zestawu tworzenia szybkiego startu artykule do utworzenia zestawu skalowania maszyny wirtualnej platformy Azure:

- [Tworzenie zestawu skalowania maszyny wirtualnej w portalu Azure](../../virtual-machine-scale-sets/quick-create-portal.md)  

Przejdź do następnej sekcji, aby uzyskać więcej informacji na temat włączania MSI na zestaw skali maszyny wirtualnej.

## <a name="enable-managed-service-identity-on-an-existing-azure-vmms"></a>Włącz zarządzane tożsamości usługi na istniejących VMMS Azure

Aby włączyć system przypisane tożsamości na maszynie Wirtualnej, który został pierwotnie zainicjowana bez go:

1. Zaloguj się do [portalu Azure](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, która zawiera zestaw skali maszyny wirtualnej.

2. Przejdź do zestawu skalowania odpowiednią maszynę wirtualną.

3. Włącz tożsamości systemu przypisane na maszynie Wirtualnej, wybierając przycisk Tak, w obszarze "Tożsamość usługi zarządzany", a następnie kliknij przycisk **zapisać**. Ta operacja może zająć 60 sekund lub więcej ukończenia:

   [![Zrzut ekranu strony konfiguracji](../media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png)](../media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png#lightbox)  

## <a name="remove-managed-service-identity-from-an-azure-virtual-machine-scale-set"></a>Usuń tożsamość usługi zarządzane z zestawu skalowania maszyny wirtualnej platformy Azure

Jeśli masz zestaw skali maszyny wirtualnej, który nie będzie już potrzebował MSI:

1. Zaloguj się do [portalu Azure](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, która zawiera zestaw skali maszyny wirtualnej. Upewnij się, Twoje konto należy do roli, która umożliwia również uprawnienia do zapisu w zestawie skalowania maszyn wirtualnych.

2. Przejdź do zestawu skalowania odpowiednią maszynę wirtualną.

3. Wyłącz system przypisane przez wybranie opcji "Nie", w obszarze "Tożsamość usługi zarządzany" tożsamości na maszynie Wirtualnej, a następnie kliknij przycisk Zapisz. Ta operacja może zająć 60 sekund lub więcej ukończenia:

   ![Zrzut ekranu strony konfiguracji](../media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)  

## <a name="related-content"></a>Powiązana zawartość

- Omówienie zarządzane tożsamość usługi, zobacz [omówienie](overview.md).

## <a name="next-steps"></a>Kolejne kroki

- Przy użyciu portalu Azure, należy podać wartość skali maszyny wirtualnej platformy Azure MSI [dostęp do zasobów platformy Azure w innym](howto-assign-access-portal.md).

W poniższej sekcji komentarzy umożliwia wyrazić swoją opinię i pomóc nam dostosować i kształtu zawartość.
