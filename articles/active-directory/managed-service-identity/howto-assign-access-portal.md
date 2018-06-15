---
title: Jak przypisać MSI dostępu do zasobów platformy Azure przy użyciu portalu Azure
description: Instrukcje krok po kroku dotyczące przypisywania MSI na jeden dostęp do zasobów z innym zasobem przy użyciu portalu Azure.
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
ms.openlocfilehash: 06f316a7c96ff266e9f4593fa3a9ac871b2979aa
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
ms.locfileid: "33929776"
---
# <a name="assign-a-managed-service-identity-access-to-a-resource-by-using-the-azure-portal"></a>Przypisywanie dostępu zarządzane tożsamość usługi do zasobu za pomocą portalu Azure

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Po skonfigurowaniu zasobów platformy Azure z zarządzania usługi tożsamości (MSI), można zapewnić dostęp MSI do innego zasobu, podobnie jak podmiot zabezpieczeń. W tym artykule przedstawiono sposób udzielić dostępu MSI zestaw skali maszyny wirtualnej lub maszyny wirtualnej platformy Azure do konta magazynu platformy Azure przy użyciu portalu Azure.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Użycie funkcji RBAC można przypisać MSI dostęp do innego zasobu

Po włączeniu MSI na zasobów platformy Azure, takich jak [maszyny Wirtualnej Azure](qs-configure-portal-windows-vm.md) lub [Azure VMSS](qs-configure-portal-windows-vmss.md):

1. Zaloguj się do [portalu Azure](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, w którym skonfigurowano MSI.

2. Przejdź do żądanego zasobu, na którym chcesz zmodyfikować kontroli dostępu. W tym przykładzie udostępniamy możliwość sprawowania maszyny wirtualnej platformy Azure i Azure zestawu skalowania maszyn wirtualnych dostęp do konta magazynu, dlatego firma Microsoft, przejdź do konta magazynu.

3. Dla maszyny wirtualnej platformy Azure, wybierz **(IAM) kontroli dostępu** zasobów, a następnie wybierz **+ Dodaj**. Następnie określ **roli**, **przypisać dostęp do maszyny wirtualnej**i określ odpowiednie **subskrypcji** i **grupy zasobów** gdzie znajduje się zasób. W obszarze kryteria wyszukiwania powinna zostać wyświetlona zasobu. Wybierz zasób, a następnie wybierz **zapisać**. 

   ![Zrzut ekranu (IAM) kontroli dostępu](../media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  
   Dla zestawu skalowania maszyny wirtualnej platformy Azure, wybierz **(IAM) kontroli dostępu** zasobów, a następnie wybierz **+ Dodaj**. Następnie określ **roli**, **przypisany dostęp**. W obszarze kryteria wyszukiwania wyszukiwania zestawu skali maszyny wirtualnej. Wybierz zasób, a następnie wybierz **zapisać**.
   
   ![Zrzut ekranu (IAM) kontroli dostępu](../media/msi-howto-assign-access-vmss-portal/assign-access-control-vmss-iam-blade-before.png)  

4. Nastąpi powrót do głównego **(IAM) kontroli dostępu** strony, w której występuje nowy wpis msi zasobu.

    Maszyna wirtualna platformy Azure:

   ![Zrzut ekranu (IAM) kontroli dostępu](../media/msi-howto-assign-access-portal/assign-access-control-iam-blade-after.png)

    Zestaw skali maszyny wirtualnej platformy Azure:

    ![Zrzut ekranu (IAM) kontroli dostępu](../media/msi-howto-assign-access-vmss-portal/assign-access-control-vmss-iam-blade-after.png)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli plik MSI dla zasobu nie występuje na liście dostępnych tożsamości, sprawdź, czy MSI została prawidłowo włączona. W tym przypadku firma Microsoft wrócić do maszyny wirtualnej platformy Azure i sprawdź następujące kwestie:

- Przyjrzyj się **konfiguracji** strony i upewnij się, że wartość **MSI włączone** jest **tak**.
- Przyjrzyj się **rozszerzenia** strony i upewnij się, że rozszerzenie MSI pomyślnie wdrożone (**rozszerzenia** strona nie jest dostępna dla zestawu skalowania maszyny wirtualnej platformy Azure).

Jeśli jest albo nieprawidłowa, może być konieczne ponownie Wdróż ponownie MSI na zasobie oraz rozwiązywania problemów dotyczących niepowodzenia wdrożenia.

## <a name="related-content"></a>Zawartość pokrewna

- Omówienie MSI, zobacz [omówienie zarządzane tożsamość usługi](overview.md).
- Aby włączyć MSI w maszynie wirtualnej platformy Azure, zobacz [skonfigurować Azure VM zarządzane usługi tożsamości (MSI) przy użyciu portalu Azure](qs-configure-portal-windows-vm.md).
- Aby włączyć MSI na podstawie zestawu skali maszyny wirtualnej platformy Azure, zobacz [skonfigurować Azure maszyny wirtualnej skali ustawić zarządzane usługi tożsamości (MSI) przy użyciu portalu Azure](qs-configure-portal-windows-vmss.md)


