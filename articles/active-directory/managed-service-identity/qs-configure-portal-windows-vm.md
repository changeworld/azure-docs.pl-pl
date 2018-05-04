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
ms.date: 09/19/2017
ms.author: daveba
ms.openlocfilehash: 91b4d50bb72e438c677b8b83e292998cce26415c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="configure-a-vm-managed-service-identity-msi-using-the-azure-portal"></a>Konfigurowanie maszyny Wirtualnej zarządzane usługi tożsamości (MSI) przy użyciu portalu Azure

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Tożsamość usługi zarządzanej zapewnia usług platformy Azure przy użyciu tożsamości automatycznie zarządzane w usłudze Azure Active Directory. Ta tożsamość służy do uwierzytelniania do dowolnej usługi obsługującej uwierzytelniania usługi Azure AD, bez konieczności poświadczeń w kodzie. 

W tym artykule dowiesz się, jak włączyć lub wyłączyć system przypisane tożsamości dla maszyny Wirtualnej platformy Azure, przy użyciu portalu Azure. Przypisywanie i usuwanie użytkownika z przypisanym tożsamości z maszyn wirtualnych platformy Azure nie jest obecnie obsługiwane za pośrednictwem portalu Azure.

> [!NOTE]
> Obecnie tożsamości operacje przypisane przez użytkownika nie są obsługiwane za pośrednictwem portalu Azure. Sprawdzanie dostępności aktualizacji. 

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli znasz zarządzane tożsamość usługi, zapoznaj się [sekcji Przegląd](overview.md).
- Jeśli nie masz jeszcze konta platformy Azure, [Załóż bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.

## <a name="managed-service-identity-during-creation-of-an-azure-vm"></a>Zarządzane tożsamość usługi podczas tworzenia maszyny Wirtualnej platformy Azure

Tworzenie maszyny Wirtualnej za pośrednictwem portalu Azure nie obsługuje obecnie, operacje zarządzane tożsamości usługi. Zamiast tego można znaleźć w następujących artykułach szybkiego startu tworzenia maszyny Wirtualnej do utworzenia maszyny Wirtualnej:

- [Utwórz maszynę wirtualną z systemem Windows przy użyciu portalu Azure](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Utwórz maszynę wirtualną systemu Linux przy użyciu portalu Azure](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)  

Przejdź do następnej sekcji, aby uzyskać więcej informacji na temat włączania zarządzane tożsamości usługi na maszynie Wirtualnej.

## <a name="enable-managed-service-identity-on-an-existing-azure-vm"></a>Włącz zarządzane tożsamości usługi na istniejącej maszynie Wirtualnej Azure

Aby włączyć system przypisane tożsamości na maszynie Wirtualnej, który został pierwotnie zainicjowana bez go:

1. Zaloguj się do [portalu Azure](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, który zawiera maszyny Wirtualnej. Upewnij się, Twoje konto należy do roli, która umożliwia również uprawnienia do zapisu na maszynie Wirtualnej, takie jak "Współautora maszyny wirtualnej".

2. Przejdź do odpowiednią maszynę wirtualną, a następnie wybierz stronę "Configuration".

3. Włącz tożsamości systemu przypisane na maszynie Wirtualnej, wybierając przycisk Tak, w obszarze "Tożsamość usługi zarządzany", a następnie kliknij przycisk **zapisać**. Ta operacja może zająć 60 sekund lub więcej ukończenia:

    > [!NOTE]
    > Dodawanie tożsamości użytkownika przypisanego do maszyny Wirtualnej nie jest obecnie obsługiwane za pośrednictwem portalu Azure.

   ![Zrzut ekranu strony konfiguracji](../media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

## <a name="remove-managed-service-identity-from-an-azure-vm"></a>Usuń tożsamość usługi zarządzanej z maszyny Wirtualnej platformy Azure

Jeśli masz maszyny wirtualnej, która nie będzie już potrzebował tożsamości systemu przypisane:

1. Zaloguj się do [portalu Azure](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, który zawiera maszyny Wirtualnej. Upewnij się, Twoje konto należy do roli, która umożliwia również uprawnienia do zapisu na maszynie Wirtualnej, takie jak "Współautora maszyny wirtualnej".

2. Przejdź do odpowiednią maszynę wirtualną, a następnie wybierz stronę "Configuration".

3. Wyłącz system przypisane przez wybranie opcji "Nie", w obszarze "Tożsamość usługi zarządzany" tożsamości na maszynie Wirtualnej, a następnie kliknij przycisk Zapisz. Ta operacja może zająć 60 sekund lub więcej ukończenia:

    > [!NOTE]
    > Dodawanie tożsamości użytkownika przypisanego do maszyny Wirtualnej nie jest obecnie obsługiwane za pośrednictwem portalu Azure.

   ![Zrzut ekranu strony konfiguracji](../media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)  

## <a name="related-content"></a>Zawartość pokrewna

- Omówienie zarządzane tożsamość usługi, zobacz [omówienie](overview.md).

## <a name="next-steps"></a>Kolejne kroki

- Za pomocą portalu Azure, nadaj MSI wirtualna Azure [dostęp do zasobów platformy Azure w innym](howto-assign-access-portal.md).

