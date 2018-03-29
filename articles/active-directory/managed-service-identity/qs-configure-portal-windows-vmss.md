---
title: Skonfiguruj MSI skali maszyny wirtualnej platformy Azure, ustawić za pomocą portalu Azure
description: Krok kroku instrukcje dotyczące konfigurowania zarządzane tożsamości usługi (MSI) na VMSS Azure, przy użyciu portalu Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: d9b493203a78aebdfadef15cf53d9cc023bb66f8
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="configure-an-azure-virtual-machine-scale-set-managed-service-identity-msi-using-the-azure-portal"></a>Konfigurowanie usługi Azure maszyny wirtualnej skali ustawić zarządzane usługi tożsamości (MSI) przy użyciu portalu Azure

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Tożsamość usługi zarządzanej zapewnia usług platformy Azure przy użyciu tożsamości automatycznie zarządzane w usłudze Azure Active Directory. Ta tożsamość służy do uwierzytelniania do dowolnej usługi obsługującej uwierzytelniania usługi Azure AD, bez konieczności poświadczeń w kodzie. 

W tym artykule dowiesz sposobu włączania i Usuń MSI dla zestawu skalowania maszyny wirtualnej platformy Azure przy użyciu portalu Azure.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="enable-msi-during-creation-of-azure-virtual-machine-scale-set"></a>Włącz MSI podczas tworzenia zestawu skali maszyny wirtualnej platformy Azure

Począwszy od chwili pisania tego dokumentu Włączanie MSI podczas tworzenia skali maszyny wirtualnej w portalu Azure nie jest obsługiwane. Zamiast tego można znaleźć maszyny wirtualnej platformy Azure skali zestawu tworzenia szybkiego startu artykule do utworzenia zestawu skalowania maszyny wirtualnej platformy Azure:

- [Tworzenie zestawu skalowania maszyny wirtualnej w portalu Azure](../../virtual-machine-scale-sets/quick-create-portal.md)  

Przejdź do następnej sekcji, aby uzyskać więcej informacji na temat włączania MSI na zestaw skali maszyny wirtualnej.

## <a name="enable-msi-on-an-existing-azure-vmms"></a>Włącz MSI na istniejących VMMS Azure

Jeśli masz zestaw skali maszyny wirtualnej, który został pierwotnie zainicjowana bez MSI:

1. Zaloguj się do [portalu Azure](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, która zawiera zestaw skali maszyny wirtualnej.

2. Przejdź do zestawu skalowania odpowiednią maszynę wirtualną.

3. Kliknij przycisk **konfiguracji** MSI, Włącz skalowania maszyny wirtualnej ustawić, wybierając pozycję **tak** tożsamością"zarządzana Usługa", następnie kliknij przycisk **zapisać**. Ta operacja może zająć 60 sekund lub więcej ukończenia:

   ![Zrzut ekranu strony konfiguracji](../media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png)  

## <a name="remove-msi-from-an-azure-virtual-machine-scale-set"></a>Usuń MSI z zestawu skalowania maszyny wirtualnej platformy Azure

Jeśli masz zestaw skali maszyny wirtualnej, który nie będzie już potrzebował MSI:

1. Zaloguj się do [portalu Azure](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, która zawiera zestaw skali maszyny wirtualnej. Upewnij się, Twoje konto należy do roli, która umożliwia również uprawnienia do zapisu w zestawie skalowania maszyn wirtualnych.

2. Przejdź do zestawu skalowania odpowiednią maszynę wirtualną.

3. Kliknij przycisk **konfiguracji** Usuń MSI od skali maszyny wirtualnej ustawić, wybierając pozycję **nr** w obszarze **tożsamość usługi zarządzanej**, następnie kliknij przycisk **Zapisz** . Ta operacja może zająć 60 sekund lub więcej ukończenia:

   ![Zrzut ekranu strony konfiguracji](../media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)  

## <a name="next-steps"></a>Kolejne kroki

- Omówienie MSI, zobacz [omówienie zarządzane tożsamość usługi](overview.md).
- Przy użyciu portalu Azure, należy podać wartość skali maszyny wirtualnej platformy Azure MSI [dostęp do zasobów platformy Azure w innym](howto-assign-access-portal.md).

W poniższej sekcji komentarzy umożliwia wyrazić swoją opinię i pomóc nam dostosować i kształtu zawartość.
