---
title: Jak przypisać tożsamości usługi Zarządzanej dostępu do zasobów platformy Azure przy użyciu witryny Azure portal
description: Szczegółowe instrukcje dotyczące przypisywania Instalatora MSI na jednym dostępu do zasobów do innego zasobu za pomocą witryny Azure portal.
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
ms.openlocfilehash: 83a56793d08632918a75f6580360a9dd148d7316
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38611072"
---
# <a name="assign-a-managed-service-identity-access-to-a-resource-by-using-the-azure-portal"></a>Przypisywanie dostępu tożsamości usługi zarządzanej do zasobów przy użyciu witryny Azure portal

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Po skonfigurowaniu zasobu platformy Azure przy użyciu tożsamość usługi zarządzanej (MSI), można zapewnić dostęp MSI do innego zasobu, podobnie jak podmiot zabezpieczeń. W tym artykule pokazano, jak udzielić dostępu tożsamości usługi Zarządzanej maszyny wirtualnej platformy Azure w konto magazynu platformy Azure przy użyciu witryny Azure portal.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Przypisywanie dostępu tożsamości usługi Zarządzanej do innego zasobu przy użyciu kontroli RBAC

Po włączeniu tożsamości usługi Zarządzanej w obrębie zasobu platformy Azure, [takich jak Maszynie wirtualnej platformy Azure](msi-qs-configure-portal-windows-vm.md):

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, w którym skonfigurowano pliku MSI.

2. Przejdź do żądanego zasobu, na którym chcesz zmodyfikować kontroli dostępu. W tym przykładzie udostępniamy możliwość dostępu do maszyny Wirtualnej platformy Azure na koncie magazynu, dzięki czemu możemy przejdź do konta magazynu.

3. Wybierz **kontrola dostępu (IAM)** strony zasobu i wybierz **+ Dodaj**. Następnie określ **roli**, **przypisywanie dostępu do maszyny wirtualnej**i określ odpowiednie **subskrypcji** i **grupy zasobów** gdzie znajduje się zasób. W obszarze kryteria wyszukiwania powinna być widoczna zasobu. Wybierz zasób, a następnie wybierz pozycję **Zapisz**. 

   ![Zrzut ekranu (IAM) kontroli dostępu](~/articles/active-directory/media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  

4. Nastąpi powrót do głównego **kontrola dostępu (IAM)** stronie tam, gdzie zobaczysz nowy wpis do pliku MSI do zasobu. W tym przykładzie ma "SimpleWinVM" maszyny Wirtualnej w grupie zasobów o pokaz **Współautor** dostępu do konta magazynu.

   ![Zrzut ekranu (IAM) kontroli dostępu](~/articles/active-directory/media/msi-howto-assign-access-portal/assign-access-control-iam-blade-after.png)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Plik MSI dla zasobu nie jest wyświetlany na liście dostępnych tożsamości, sprawdź, czy poprawnie włączono pliku MSI. W naszym przypadku możemy przejść z powrotem do maszyny Wirtualnej platformy Azure i sprawdź następujące kwestie:

- Przyjrzyj się **konfiguracji** strony i upewnij się, że wartość **włączoną tożsamością usługi Zarządzanej** jest **tak**.
- Przyjrzyj się **rozszerzenia** strony i upewnij się, że rozszerzenie tożsamości usługi Zarządzanej zostało pomyślnie wdrożone.

Jeśli jest albo nieprawidłowa, konieczne może być ponownie Wdróż ponownie MSI Twojego zasobu i rozwiązywanie problemów błędu wdrożenia.

## <a name="related-content"></a>Powiązana zawartość

- Aby uzyskać omówienie MSI, zobacz [Przegląd tożsamości usługi zarządzanej](msi-overview.md).
- Aby włączyć tożsamości usługi Zarządzanej maszyny wirtualnej platformy Azure, zobacz [Azure VM tożsamość usługi zarządzanej (MSI) przy użyciu witryny Azure portal skonfigurować](msi-qs-configure-portal-windows-vm.md).


