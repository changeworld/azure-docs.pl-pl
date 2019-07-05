---
title: Łączenie z maszyną Wirtualną z Windows za pomocą usługi Azure bastionu | Dokumentacja firmy Microsoft
description: W tym artykule Dowiedz się, jak połączyć do platformy Azure maszyny wirtualnej z systemem Windows przy użyciu usługi Azure bastionu.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: 376b7042a513dd50647dc8f88bf1de70f65bb21c
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478401"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion-preview"></a>Łączenie z maszyną wirtualną Windows przy użyciu bastionu Azure (wersja zapoznawcza)

W tym artykule dowiesz się, jak na sposób bezpieczny i bezproblemowy protokołu RDP do maszyn wirtualnych Windows na platformie Azure wirtualnych sieci za pomocą bastionu platformy Azure. Połączenie z maszyną wirtualną można nawiązywać bezpośrednio w witrynie Azure Portal. Gdy używasz usługi Azure Bastion, maszyny wirtualne nie wymagają klienta, agenta ani dodatkowego oprogramowania. Aby uzyskać więcej informacji na temat usługi Azure bastionu zobacz [Przegląd](bastion-overview.md).

> [!IMPORTANT]
> Ten podgląd publiczny nie jest objęty umową dotyczącą poziomu usług i nie należy korzystać z niego w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, mogą mieć ograniczone możliwości lub mogą nie być dostępne we wszystkich lokalizacjach platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Przed rozpoczęciem

Upewnij się, że po skonfigurowaniu hostem bastionu platformy Azure, w ramach sieci wirtualnej, w której znajduje się maszyna wirtualna. Aby uzyskać więcej informacji, zobacz [tworzenie hosta platformy Azure bastionu](bastion-create-host-portal.md). Gdy usługa bastionu jest aprowizowana i wdrożone w sieci wirtualnej, można użyć go połączyć się z dowolnej maszyny Wirtualnej w tej sieci wirtualnej. W tej wersji zapoznawczej bastionu przyjęto założenie, że używasz protokołu RDP, połączyć się z maszyn wirtualnych Windows i SSH połączyć się z maszyn wirtualnych systemu Linux. Aby uzyskać informacje dotyczące połączenia z maszyną wirtualną systemu Linux, zobacz [nawiązywanie połączenia z maszyną Wirtualną — Linux](bastion-connect-vm-ssh.md).

Aby można było utworzyć połączenie, wymagane są następujące role:

* Rola czytelnika na maszynie wirtualnej
* Rola czytelnika na karcie Sieciowej z prywatnym adresem IP maszyny wirtualnej
* Rola czytelnika na zasobie bastionu platformy Azure

## <a name="rdp"></a>Nawiązywanie połączenia przy użyciu protokołu RDP

1. Użyj [ten link](https://aka.ms/BastionHost) o otwarcie strony portalu w wersji zapoznawczej dla usługi Azure bastionu. Przejdź do maszyny wirtualnej, którą chcesz nawiązać połączenie, a następnie kliknij przycisk **Connect**. Maszyna wirtualna powinna być maszynę wirtualną Windows, korzystając z połączeniem RDP.

    ![Połącz z maszyną Wirtualną](./media/bastion-connect-vm-rdp/connect.png)

1. Po kliknięciu przycisku Połącz pojawia się pasek boczny zawiera trzy karty — RDP, SSH i bastionu. Jeśli bastionu została aprowizowana dla sieci wirtualnej, na karcie bastionu jest domyślnie aktywny. Jeśli nie aprowizować bastionu w ramach sieci wirtualnej, możesz kliknąć link, aby skonfigurować bastionu. Aby uzyskać instrukcje dotyczące konfiguracji, zobacz [skonfigurować bastionu](bastion-create-host-portal.md). Jeśli nie widzisz **bastionu** na liście, nie została jeszcze otwarta portalu w wersji zapoznawczej. Otwórz portal za pomocą tego [Podgląd łącza](https://aka.ms/BastionHost).

    ![Połącz z maszyną Wirtualną](./media/bastion-connect-vm-rdp/bastion.png)

1. Na karcie bastionu, nazwę użytkownika i hasło dla maszyny wirtualnej, następnie kliknij przycisk **Connect**. Połączenia RDP z tą maszyną wirtualną za pośrednictwem bastionu będą otwierane bezpośrednio w witrynie Azure portal (za pośrednictwem HTML5) przy użyciu portu 443 i usługi bastionu.

    ![Połącz z maszyną Wirtualną](./media/bastion-connect-vm-rdp/443rdp.png)
 
## <a name="next-steps"></a>Kolejne kroki

Odczyt [bastionu — często zadawane pytania](bastion-faq.md)
