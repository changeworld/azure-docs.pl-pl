---
title: Łączenie się z zestawem skalowania maszyny wirtualnej systemu Windows przy użyciu usługi Azure Bastion | Dokumenty firmy Microsoft
description: W tym artykule dowiesz się, jak połączyć się z zestawem skalowania maszyny wirtualnej platformy Azure przy użyciu usługi Azure Bastion.
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 290a20fcd827841c24983f3bdd54b6db8e154462
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619340"
---
# <a name="connect-to-a-virtual-machine-scale-set-using-azure-bastion"></a>Łączenie się z zestawem skalowania maszyny wirtualnej przy użyciu bastionu platformy Azure

W tym artykule pokazano, jak bezpiecznie i bezproblemowo RDP do środowiska windows virtual machine skalowania zestawu wystąpienia w sieci wirtualnej platformy Azure przy użyciu usługi Azure Bastion. Można połączyć się z wystąpieniem zestawu skalowania maszyny wirtualnej bezpośrednio z witryny Azure portal. Podczas korzystania z usługi Azure Bastion maszyny wirtualne nie wymagają klienta, agenta ani dodatkowego oprogramowania. Aby uzyskać więcej informacji na temat usługi Azure Bastion, zobacz [omówienie](bastion-overview.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Upewnij się, że skonfigurowałeś hosta bastionu platformy Azure dla sieci wirtualnej, w której znajduje się zestaw skalowania maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Tworzenie hosta bastionu platformy Azure](bastion-create-host-portal.md). Po zaaprowizowaniu i wdrożeniu usługi Bastion w sieci wirtualnej można jej używać do łączenia się z wystąpieniem zestawu skalowania maszyny wirtualnej w tej sieci wirtualnej. Bastion zakłada, że używasz protokołu RDP do łączenia się z zestawem skalowania maszyny wirtualnej systemu Windows, a SSH do łączenia się z zestawem skalowania maszyny wirtualnej systemu Linux. Aby uzyskać informacje na temat połączenia z maszyną wirtualną z systemem Linux, zobacz [Łączenie się z maszyną wirtualną — Linux](bastion-connect-vm-ssh.md).

## <a name="connect-using-rdp"></a><a name="rdp"></a>Łączenie za pomocą protokołu RDP

1. Otwórz witrynę [Azure Portal](https://portal.azure.com). Przejdź do zestawu skalowania maszyny wirtualnej, z którego chcesz się połączyć.

   ![nawigacja](./media/bastion-connect-vm-scale-set/1.png)
2. Przejdź do wystąpienia zestawu skalowania maszyny wirtualnej, z którą chcesz się połączyć, a następnie wybierz pozycję **Połącz**. Podczas korzystania z połączenia RDP zestaw skalowania maszyny wirtualnej powinien być zestawem skalowania maszyny wirtualnej systemu Windows.

   ![zestaw skalowania maszyny wirtualnej](./media/bastion-connect-vm-scale-set/2.png)
3. Po wybraniu opcji **Połącz**pojawi się pasek boczny zawierający trzy karty – RDP, SSH i Bastion. Wybierz kartę **Bastion** z paska bocznego. Jeśli nie aprowizować Bastion dla sieci wirtualnej, można wybrać łącze, aby skonfigurować Bastion. Aby uzyskać instrukcje konfiguracji, zobacz [Konfigurowanie bastionu](bastion-create-host-portal.md).

   ![Karta Bastion](./media/bastion-connect-vm-scale-set/3.png)
4. Na karcie Bastion wprowadź nazwę użytkownika i hasło do zestawu skalowania maszyny wirtualnej, a następnie wybierz pozycję **Połącz**.

   ![nawiązywania połączenia](./media/bastion-connect-vm-scale-set/4.png)
5. Połączenie RDP z tą maszyną wirtualną za pośrednictwem bastionu zostanie otwarte bezpośrednio w witrynie Azure portal (ponad HTML5) przy użyciu portu 443 i usługi Bastion.

## <a name="next-steps"></a>Następne kroki

Przeczytaj często zadawane pytania dotyczące [bastionu](bastion-faq.md).