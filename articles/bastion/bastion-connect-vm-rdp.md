---
title: Nawiązywanie połączenia z maszyną wirtualną z systemem Windows przy użyciu usługi Azure bastionu | Microsoft Docs
description: W tym artykule dowiesz się, jak nawiązać połączenie z maszyną wirtualną platformy Azure z systemem Windows przy użyciu usługi Azure bastionu.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: cherylmc
ms.openlocfilehash: dc741007c7de8d8e24f9c0f9e4e0c03306d036a4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498350"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Nawiązywanie połączenia z maszyną wirtualną z systemem Windows przy użyciu usługi Azure bastionu

W tym artykule pokazano, jak bezpiecznie i bezproblemowo korzystać z protokołu RDP na maszynach wirtualnych z systemem Windows w sieci wirtualnej platformy Azure przy użyciu usługi Azure bastionu. Połączenie z maszyną wirtualną można nawiązywać bezpośrednio w witrynie Azure Portal. Gdy używasz usługi Azure Bastion, maszyny wirtualne nie wymagają klienta, agenta ani dodatkowego oprogramowania. Aby uzyskać więcej informacji na temat usługi Azure bastionu, zobacz [Omówienie](bastion-overview.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Upewnij się, że skonfigurowano hosta usługi Azure bastionu dla sieci wirtualnej, w której znajduje się maszyna wirtualna. Aby uzyskać więcej informacji, zobacz [Tworzenie hosta usługi Azure bastionu](bastion-create-host-portal.md). Gdy usługa bastionu zostanie zainicjowana i wdrożona w sieci wirtualnej, możesz użyć jej do łączenia się z dowolną MASZYNą wirtualną w tej sieci wirtualnej. Bastionu zakłada, że używasz protokołu RDP do nawiązywania połączenia z maszyną wirtualną z systemem Windows i SSH do łączenia się z maszynami wirtualnymi z systemem Linux. Aby uzyskać informacje o połączeniu z maszyną wirtualną z systemem Linux, zobacz [nawiązywanie połączenia z maszyną wirtualną — Linux](bastion-connect-vm-ssh.md).

Aby można było nawiązać połączenie, wymagane są następujące role:

* Rola czytelnika na maszynie wirtualnej
* Rola czytelnika na karcie sieciowej z prywatnym adresem IP maszyny wirtualnej
* Rola czytelnika w zasobie Azure bastionu

## <a name="rdp"></a>Nawiązywanie połączenia przy użyciu protokołu RDP

1. Otwórz [portal Azure](https://portal.azure.com). Przejdź do maszyny wirtualnej, z którą chcesz nawiązać połączenie, a następnie kliknij przycisk **Połącz**. Maszyna wirtualna powinna być maszyną wirtualną z systemem Windows podczas korzystania z połączenia RDP.

   ![Połączenie maszyny wirtualnej](./media/bastion-connect-vm-rdp/connect.png)
1. Po kliknięciu przycisku Połącz zostanie wyświetlony pasek boczny z trzema kartami — RDP, SSH i bastionu. Jeśli Zainicjowano obsługę bastionu dla sieci wirtualnej, karta bastionu jest domyślnie aktywna. Jeśli nie zainicjowano obsługi administracyjnej usługi bastionu dla sieci wirtualnej, możesz kliknąć link, aby skonfigurować bastionu. Instrukcje dotyczące konfiguracji znajdują się w temacie [Configure bastionu](bastion-create-host-portal.md).

   ![Połączenie maszyny wirtualnej](./media/bastion-connect-vm-rdp/bastion.png)
1. Na karcie bastionu nazwę użytkownika i hasło dla maszyny wirtualnej, a następnie kliknij przycisk **Połącz**. Połączenie RDP z tą maszyną wirtualną za pośrednictwem bastionu zostanie otwarte bezpośrednio w Azure Portal (za pośrednictwem HTML5) przy użyciu portu 443 i usługi bastionu.

   ![Połączenie maszyny wirtualnej](./media/bastion-connect-vm-rdp/443rdp.png)
 
## <a name="next-steps"></a>Następne kroki

Przeczytaj [często zadawane pytania dotyczące bastionu](bastion-faq.md)