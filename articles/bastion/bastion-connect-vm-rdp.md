---
title: Łączenie się z maszyną wirtualną systemu Windows przy użyciu bastionu platformy Azure
description: W tym artykule dowiesz się, jak połączyć się z maszyną wirtualną platformy Azure z systemem Windows przy użyciu usługi Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: cherylmc
ms.openlocfilehash: 6ec60dc313c8a4374637adf38ea0e5a7d4ed964b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597352"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Łączenie się z maszyną wirtualną systemu Windows przy użyciu bastionu platformy Azure

Korzystając z usługi Azure Bastion, można bezpiecznie i bezproblemowo połączyć się z maszynami wirtualnymi za pośrednictwem SSL bezpośrednio w witrynie Azure portal. Podczas korzystania z usługi Azure Bastion maszyny wirtualne nie wymagają klienta, agenta ani dodatkowego oprogramowania. W tym artykule pokazano, jak połączyć się z maszynami wirtualnymi systemu Windows. Aby uzyskać informacje na temat łączenia się z maszyną wirtualną z systemem Linux, zobacz [Łączenie się z maszyną wirtualną przy użyciu usługi Azure Bastion — Linux](bastion-connect-vm-ssh.md).

Usługa Azure Bastion zapewnia bezpieczną łączność ze wszystkimi maszynami wirtualnymi w sieci wirtualnej, w której jest aprowizowana. Korzystanie z usługi Azure Bastion chroni maszyny wirtualne przed udostępnianiem portów RDP/SSH na zewnątrz, zapewniając jednocześnie bezpieczny dostęp przy użyciu protokołu RDP/SSH. Aby uzyskać więcej informacji, zapoznaj się z [omówieniem](bastion-overview.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Upewnij się, że skonfigurowałeś hosta bastionu platformy Azure dla sieci wirtualnej, w której znajduje się maszyna wirtualna. Po zaaprowizowaniu i wdrożeniu usługi Bastion w sieci wirtualnej można jej używać do łączenia się z dowolną maszyną wirtualną w sieci wirtualnej. Aby skonfigurować hosta Bastionu platformy Azure, zobacz [Tworzenie hosta bastionu platformy Azure](bastion-create-host-portal.md).

### <a name="required-roles"></a>Wymagane role

Aby nawiązać połączenie, wymagane są następujące role:

* Rola czytnika na maszynie wirtualnej
* Rola czytnika karty sieciowej z prywatnym adresem IP maszyny wirtualnej
* Rola czytnika w zasobie Bastion platformy Azure

### <a name="ports"></a>Porty

Aby połączyć się z maszyną wirtualną systemu Windows, na maszynie wirtualnej systemu Windows muszą być otwarte następujące porty:

* Porty przychodzące: RDP (3389)

## <a name="connect"></a><a name="rdp"></a>Połącz

1. Otwórz witrynę [Azure Portal](https://portal.azure.com). Przejdź do maszyny wirtualnej, z którą chcesz się połączyć, a następnie kliknij przycisk **Połącz** i wybierz **bastion** z listy rozwijanej.

   ![Połączenie maszyny wirtualnej](./media/bastion-connect-vm-rdp/connect.png)
1. Po kliknięciu przycisku Bastion pojawi się pasek boczny zawierający trzy karty – RDP, SSH i Bastion. Jeśli bastion został aprowidywny dla sieci wirtualnej, karta Bastion jest domyślnie aktywna. Jeśli nie aprowizować Bastion dla sieci wirtualnej, można kliknąć łącze, aby skonfigurować Bastion. Aby uzyskać instrukcje konfiguracji, zobacz [Konfigurowanie bastionu](bastion-create-host-portal.md).

   ![karta bastion](./media/bastion-connect-vm-rdp/bastion.png)
1. Na karcie Bastion wprowadź nazwę użytkownika i hasło maszyny wirtualnej, a następnie kliknij przycisk **Połącz**. Połączenie RDP z tą maszyną wirtualną za pośrednictwem bastionu zostanie otwarte bezpośrednio w witrynie Azure portal (ponad HTML5) przy użyciu portu 443 i usługi Bastion.

   ![Połączenie z PROW](./media/bastion-connect-vm-rdp/443rdp.png)
 
## <a name="next-steps"></a>Następne kroki

Przeczytaj często zadawane pytania dotyczące [bastionu](bastion-faq.md)
