---
title: Jak skonfigurować MSI w maszynie Wirtualnej platformy Azure przy użyciu portalu Azure
description: Krok kroku instrukcje dotyczące konfigurowania zarządzane tożsamości usługi (MSI) na maszynie Wirtualnej platformy Azure, przy użyciu portalu Azure.
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
ms.openlocfilehash: 37710015904c8112e5d2de504ed5b42895ffb809
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2018
ms.locfileid: "28979177"
---
# <a name="configure-a-vm-managed-service-identity-msi-using-the-azure-portal"></a>Konfigurowanie maszyny Wirtualnej zarządzane usługi tożsamości (MSI) przy użyciu portalu Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Tożsamość usługi zarządzanej zapewnia usług platformy Azure przy użyciu tożsamości automatycznie zarządzane w usłudze Azure Active Directory. Ta tożsamość służy do uwierzytelniania do dowolnej usługi obsługującej uwierzytelniania usługi Azure AD, bez konieczności poświadczeń w kodzie. 

W tym artykule dowiesz się, jak włączyć i usunąć MSI dla maszyny Wirtualnej platformy Azure, przy użyciu portalu Azure.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Włącz MSI podczas tworzenia maszyny Wirtualnej platformy Azure

Począwszy od chwili pisania tego dokumentu Włączanie MSI podczas tworzenia maszyny wirtualnej w portalu Azure nie jest obsługiwane. Zamiast tego można znaleźć w następujących artykułach szybkiego startu tworzenia maszyny Wirtualnej do utworzenia maszyny Wirtualnej:

- [Utwórz maszynę wirtualną z systemem Windows przy użyciu portalu Azure](~/articles/virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Utwórz maszynę wirtualną systemu Linux przy użyciu portalu Azure](~/articles/virtual-machines/linux/quick-create-portal.md#create-virtual-machine)  

Przejdź do następnej sekcji, aby uzyskać więcej informacji na temat włączania MSI w maszynie Wirtualnej.

## <a name="enable-msi-on-an-existing-azure-vm"></a>Włącz MSI na istniejącej maszynie Wirtualnej Azure

Jeśli masz maszyny Wirtualnej, który został pierwotnie zainicjowana bez MSI:

1. Zaloguj się do [portalu Azure](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, który zawiera maszyny Wirtualnej. Upewnij się, Twoje konto należy do roli, która umożliwia również uprawnienia do zapisu na maszynie Wirtualnej, takie jak "Współautora maszyny wirtualnej".

2. Przejdź do żądanego maszyny wirtualnej.

2. Kliknij stronę "Konfiguracja", włączyć MSI w maszynie Wirtualnej, wybierając przycisk "Tak" w "Tożsamość usługi zarządzany", a następnie kliknij przycisk **zapisać**. Ta operacja może zająć 60 sekund lub więcej ukończenia:

   ![Zrzut ekranu strony konfiguracji](~/articles/active-directory/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

## <a name="remove-msi-from-an-azure-vm"></a>Usuń MSI w maszynie Wirtualnej platformy Azure

Jeśli masz maszyny wirtualnej, która nie będzie już potrzebował MSI:

1. Zaloguj się do [portalu Azure](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, który zawiera maszyny Wirtualnej. Upewnij się, Twoje konto należy do roli, która umożliwia również uprawnienia do zapisu na maszynie Wirtualnej, takie jak "Współautora maszyny wirtualnej".

2. Przejdź do żądanego maszyny wirtualnej.

3. Kliknij stronę "Konfiguracja", Usuń MSI z maszyny Wirtualnej przez wybranie opcji "Nie", w obszarze "Tożsamość usługi zarządzany", a następnie kliknij przycisk **zapisać**. Ta operacja może zająć 60 sekund lub więcej ukończenia:

   ![Zrzut ekranu strony konfiguracji](~/articles/active-directory/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)  

## <a name="related-content"></a>Zawartość pokrewna

- Omówienie MSI, zobacz [omówienie zarządzane tożsamość usługi](msi-overview.md).

## <a name="next-steps"></a>Kolejne kroki

- Za pomocą portalu Azure, nadaj MSI wirtualna Azure [dostęp do zasobów platformy Azure w innym](msi-howto-assign-access-portal.md).

W poniższej sekcji komentarzy umożliwia wyrazić swoją opinię i pomóc nam dostosować i kształtu zawartość.
