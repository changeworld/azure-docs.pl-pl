---
title: Jak przypisać tożsamości usługi Zarządzanej dostępu do zasobów platformy Azure przy użyciu witryny Azure portal
description: Szczegółowe instrukcje dotyczące przypisywania Instalatora MSI na jednym dostępu do zasobów do innego zasobu za pomocą witryny Azure portal.
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
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: c2048583cde397ac3325fd149982b3a3db475566
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44157283"
---
# <a name="assign-a-managed-service-identity-access-to-a-resource-by-using-the-azure-portal"></a>Przypisywanie dostępu tożsamości usługi zarządzanej do zasobów przy użyciu witryny Azure portal

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Po skonfigurowaniu zasobu platformy Azure przy użyciu tożsamość usługi zarządzanej (MSI), można zapewnić dostęp MSI do innego zasobu, podobnie jak podmiot zabezpieczeń. W tym artykule pokazano, jak przyznać maszynie wirtualnej platformy Azure lub dostępu tożsamości usługi Zarządzanej zestaw skalowania maszyn wirtualnych do konta usługi Azure storage przy użyciu witryny Azure portal.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Przypisywanie dostępu tożsamości usługi Zarządzanej do innego zasobu przy użyciu kontroli RBAC

Po włączeniu tożsamości usługi Zarządzanej w obrębie zasobu platformy Azure, takich jak [maszyny Wirtualnej platformy Azure](qs-configure-portal-windows-vm.md) lub [Azure VMSS](qs-configure-portal-windows-vmss.md):

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, w którym skonfigurowano pliku MSI.

2. Przejdź do żądanego zasobu, na którym chcesz zmodyfikować kontroli dostępu. W tym przykładzie udostępniamy możliwość maszynie wirtualnej platformy Azure i platformy Azure zestawu skalowania maszyn wirtualnych dostęp do konta magazynu, dzięki czemu możemy przejdź do konta magazynu.

3. W maszynie wirtualnej platformy Azure wybierz **kontrola dostępu (IAM)** strony zasobu i wybierz **+ Dodaj**. Następnie określ **roli**, **przypisywanie dostępu do maszyny wirtualnej**i określ odpowiednie **subskrypcji** i **grupy zasobów** gdzie znajduje się zasób. W obszarze kryteria wyszukiwania powinna być widoczna zasobu. Wybierz zasób, a następnie wybierz pozycję **Zapisz**. 

   ![Zrzut ekranu (IAM) kontroli dostępu](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  
   Dla zestawu skalowania maszyn wirtualnych platformy Azure, wybierz **kontrola dostępu (IAM)** strony zasobu i wybierz **+ Dodaj**. Następnie określ **roli**, **Przypisz dostęp do**. W obszarze kryteria wyszukiwania, wyszukaj zestaw skalowania maszyn wirtualnych. Wybierz zasób, a następnie wybierz pozycję **Zapisz**.
   
   ![Zrzut ekranu (IAM) kontroli dostępu](./media/msi-howto-assign-access-vmss-portal/assign-access-control-vmss-iam-blade-before.png)  

4. Nastąpi powrót do głównego **kontrola dostępu (IAM)** stronie tam, gdzie zobaczysz nowy wpis do pliku MSI do zasobu.

    Maszyna wirtualna platformy Azure:

   ![Zrzut ekranu (IAM) kontroli dostępu](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-after.png)

    Zestaw skalowania maszyn wirtualnych platformy Azure:

    ![Zrzut ekranu (IAM) kontroli dostępu](./media/msi-howto-assign-access-vmss-portal/assign-access-control-vmss-iam-blade-after.png)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Plik MSI dla zasobu nie jest wyświetlany na liście dostępnych tożsamości, sprawdź, czy poprawnie włączono pliku MSI. W naszym przypadku możemy wróć do maszyny wirtualnej platformy Azure i sprawdź następujące kwestie:

- Przyjrzyj się **konfiguracji** strony i upewnij się, że wartość **włączoną tożsamością usługi Zarządzanej** jest **tak**.
- Przyjrzyj się **rozszerzenia** strony i upewnij się, że rozszerzenie tożsamości usługi Zarządzanej zostało pomyślnie wdrożone (**rozszerzenia** strona nie jest dostępna dla zestawu skalowania maszyn wirtualnych platformy Azure).

Jeśli jest albo nieprawidłowa, konieczne może być ponownie Wdróż ponownie MSI Twojego zasobu i rozwiązywanie problemów błędu wdrożenia.

## <a name="related-content"></a>Powiązana zawartość

- Aby uzyskać omówienie MSI, zobacz [Przegląd tożsamości usługi zarządzanej](overview.md).
- Aby włączyć MSI w maszynie wirtualnej platformy Azure, zobacz [Azure VM tożsamość usługi zarządzanej (MSI) przy użyciu witryny Azure portal skonfigurować](qs-configure-portal-windows-vm.md).
- Aby włączyć tożsamości usługi Zarządzanej w zestawie skalowania maszyn wirtualnych platformy Azure, zobacz [Konfigurowanie usługi Azure Virtual Machine skalowania Ustaw tożsamość usługi zarządzanej (MSI) przy użyciu witryny Azure portal](qs-configure-portal-windows-vmss.md)


