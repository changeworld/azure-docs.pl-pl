---
title: Jak skonfigurować MSI na Maszynie wirtualnej platformy Azure przy użyciu witryny Azure portal
description: Krok po kroku instrukcje dotyczące konfigurowania tożsamość usługi zarządzanej (MSI) na Maszynie wirtualnej platformy Azure, przy użyciu witryny Azure portal.
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
ms.date: 12/15/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 54bb2466a99569c124128ad3e08f276a0bc0262d
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39035269"
---
# <a name="configure-a-vm-managed-service-identity-msi-using-the-azure-portal"></a>Konfigurowanie maszyny Wirtualnej tożsamość usługi zarządzanej (MSI) przy użyciu witryny Azure portal

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Tożsamość usługi zarządzanej udostępnia usługi platformy Azure przy użyciu automatycznie zarządzanych tożsamości w usłudze Azure Active Directory. Można użyć tej tożsamości do uwierzytelniania na dowolne usługi obsługujące uwierzytelnianie usługi Azure AD bez poświadczeń w kodzie. 

W tym artykule dowiesz się, jak włączyć i usunąć tożsamości usługi Zarządzanej dla maszyny Wirtualnej platformy Azure, przy użyciu witryny Azure portal.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Włączanie tożsamości usługi Zarządzanej podczas tworzenia maszyny Wirtualnej platformy Azure

Począwszy od chwili pisania tego dokumentu włączania MSI podczas tworzenia maszyny Wirtualnej w witrynie Azure portal nie jest obsługiwane. Zamiast tego zapoznaj się z jednym z następujących artykułów szybkiego startu tworzenia maszyny Wirtualnej do utworzenia maszyny Wirtualnej:

- [Utwórz maszynę wirtualną Windows za pomocą witryny Azure portal](~/articles/virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Utwórz maszynę wirtualną systemu Linux w witrynie Azure portal](~/articles/virtual-machines/linux/quick-create-portal.md#create-virtual-machine)  

Przejdź do następnej sekcji, aby uzyskać szczegółowe informacje na temat włączania MSI na maszynie Wirtualnej.

## <a name="enable-msi-on-an-existing-azure-vm"></a>Włączanie tożsamości usługi Zarządzanej istniejącej maszyny wirtualnej platformy Azure

Jeśli masz maszynę Wirtualną, która pierwotnie została zainicjowana bez Instalatora MSI:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, który zawiera maszynę Wirtualną. Upewnij się, Twoje konto należy do roli, który zapewnia również uprawnienia do zapisu na maszynie Wirtualnej, takie jak "Współautor maszyny wirtualnej".

2. Przejdź do odpowiednią maszynę wirtualną.

2. Kliknij stronę "Konfiguracja", Włącz MSI na maszynie Wirtualnej, wybierając pozycję "Yes" w obszarze "Tożsamość usługi zarządzane", a następnie kliknij **Zapisz**. Ta operacja może potrwać 60 sekund lub więcej, aby wykonać:

   ![Zrzut ekranu strony konfiguracji](../managed-service-identity/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

## <a name="remove-msi-from-an-azure-vm"></a>Usuń plik MSI z maszyny Wirtualnej platformy Azure

Jeśli masz maszyny wirtualnej, która nie wymaga Instalatora MSI:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, który zawiera maszynę Wirtualną. Upewnij się, Twoje konto należy do roli, który zapewnia również uprawnienia do zapisu na maszynie Wirtualnej, takie jak "Współautor maszyny wirtualnej".

2. Przejdź do odpowiednią maszynę wirtualną.

3. Kliknij stronę "Konfiguracja", usuń plik MSI z maszyny Wirtualnej, wybierając pozycję "No", w obszarze "Tożsamość usługi zarządzane", a następnie kliknij **Zapisz**. Ta operacja może potrwać 60 sekund lub więcej, aby wykonać:

   ![Zrzut ekranu strony konfiguracji](../managed-service-identity/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)  

## <a name="related-content"></a>Powiązana zawartość

- Aby uzyskać omówienie MSI, zobacz [Przegląd tożsamości usługi zarządzanej](msi-overview.md).

## <a name="next-steps"></a>Kolejne kroki

- Za pomocą witryny Azure portal nadaj MSI w maszynie Wirtualnej platformy Azure [dostęp do zasobów platformy Azure w innym](msi-howto-assign-access-portal.md).

W poniższej sekcji komentarzy umożliwia opinią i Pomóż nam analizy i połącz kształt naszej zawartości.
