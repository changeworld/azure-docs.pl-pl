---
title: Łączenie się z maszyną wirtualną z systemem Linux przy użyciu bastionu platformy Azure
description: W tym artykule dowiesz się, jak połączyć się z maszyną wirtualną systemu Linux przy użyciu usługi Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: cherylmc
ms.openlocfilehash: 381f45a51002188c72174cff83c26b829912a0b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596831"
---
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion"></a>Łączenie się przy użyciu funkcji SSH z maszyną wirtualną systemu Linux przy użyciu usługi Azure Bastion

W tym artykule pokazano, jak bezpiecznie i bezproblemowo SSH do maszyn wirtualnych z systemem Linux w sieci wirtualnej platformy Azure. Możesz połączyć się z maszyną wirtualną bezpośrednio z witryny Azure portal. Podczas korzystania z usługi Azure Bastion maszyny wirtualne nie wymagają klienta, agenta ani dodatkowego oprogramowania. Aby uzyskać więcej informacji na temat usługi Azure Bastion, zobacz [omówienie](bastion-overview.md).

Za pomocą usługi Azure Bastion można połączyć się z maszyną wirtualną systemu Linux przy użyciu funkcji SSH. Do uwierzytelniania można używać zarówno nazwy użytkownika/hasła, jak i kluczy SSH. Możesz połączyć się z maszyną wirtualną za pomocą klawiszy SSH za pomocą:

* Klucz prywatny wprowadzony ręcznie
* Plik zawierający informacje o kluczu prywatnym

Klucz prywatny SSH musi być w `"-----BEGIN RSA PRIVATE KEY-----"` formacie zaczynanym od . `"-----END RSA PRIVATE KEY-----"`

## <a name="before-you-begin"></a>Przed rozpoczęciem

Upewnij się, że skonfigurowałeś hosta bastionu platformy Azure dla sieci wirtualnej, w której znajduje się maszyna wirtualna. Aby uzyskać więcej informacji, zobacz [Tworzenie hosta bastionu platformy Azure](bastion-create-host-portal.md). Po zaaprowizowaniu i wdrożeniu usługi Bastion w sieci wirtualnej można jej używać do łączenia się z dowolną maszyną wirtualną w tej sieci wirtualnej. 

Gdy używasz Bastion do łączenia, zakłada, że używasz protokołu RDP do łączenia się z maszyną wirtualną systemu Windows i SSH, aby połączyć się z maszynami wirtualnymi z systemem Linux. Aby uzyskać informacje dotyczące łączenia się z maszyną wirtualną systemu Windows, zobacz [Łączenie się z maszyną wirtualną — Windows](bastion-connect-vm-rdp.md).

### <a name="required-roles"></a>Wymagane role

Aby nawiązać połączenie, wymagane są następujące role:

* Rola czytnika na maszynie wirtualnej
* Rola czytnika karty sieciowej z prywatnym adresem IP maszyny wirtualnej
* Rola czytnika w zasobie Bastion platformy Azure

### <a name="ports"></a>Porty

Aby połączyć się z maszyną wirtualną z systemem Linux za pośrednictwem protokołu SSH, na maszynie wirtualnej muszą być otwarte następujące porty:

* Port przychodzący: SSH (22)

## <a name="connect-using-username-and-password"></a><a name="username"></a>Połącz: używanie nazwy użytkownika i hasła

1. Otwórz witrynę [Azure Portal](https://portal.azure.com). Przejdź do maszyny wirtualnej, z którą chcesz się połączyć, a następnie kliknij przycisk **Połącz** i wybierz **bastion** z listy rozwijanej.

   ![Połącz](./media/bastion-connect-vm-ssh/connect.png)
1. Po kliknięciu przycisku Bastion pojawi się pasek boczny zawierający trzy karty – RDP, SSH i Bastion. Jeśli bastion został aprowidywny dla sieci wirtualnej, karta Bastion jest domyślnie aktywna. Jeśli nie udostępnino programu Bastion dla sieci wirtualnej, zobacz [Konfigurowanie bastionu](bastion-create-host-portal.md).

   ![Bastion](./media/bastion-connect-vm-ssh/bastion.png)
1. Wprowadź nazwę użytkownika i hasło dla SSH do maszyny wirtualnej.
1. Po wprowadzeniu klawisza kliknij przycisk **Połącz.**

## <a name="connect-manually-enter-a-private-key"></a><a name="privatekey"></a>Łączenie: ręczne wprowadzanie klucza prywatnego

1. Otwórz witrynę [Azure Portal](https://portal.azure.com). Przejdź do maszyny wirtualnej, z którą chcesz się połączyć, a następnie kliknij przycisk **Połącz** i wybierz **bastion** z listy rozwijanej.

   ![Połącz](./media/bastion-connect-vm-ssh/connect.png)
1. Po kliknięciu przycisku Bastion pojawi się pasek boczny zawierający trzy karty – RDP, SSH i Bastion. Jeśli bastion został aprowidywny dla sieci wirtualnej, karta Bastion jest domyślnie aktywna. Jeśli nie udostępnino programu Bastion dla sieci wirtualnej, zobacz [Konfigurowanie bastionu](bastion-create-host-portal.md).

   ![Bastion](./media/bastion-connect-vm-ssh/bastion.png)
1. Wprowadź nazwę użytkownika i wybierz **klucz prywatny SSH**.
1. Wprowadź klucz prywatny w obszarze tekstowym **Klucz prywatny SSH** (lub wklej go bezpośrednio).
1. Po wprowadzeniu klawisza kliknij przycisk **Połącz.**

## <a name="connect-using-a-private-key-file"></a><a name="ssh"></a>Łączenie: korzystanie z pliku klucza prywatnego

1. Otwórz witrynę [Azure Portal](https://portal.azure.com). Przejdź do maszyny wirtualnej, z którą chcesz się połączyć, a następnie kliknij przycisk **Połącz** i wybierz **bastion** z listy rozwijanej.

   ![Połącz](./media/bastion-connect-vm-ssh/connect.png)
1. Po kliknięciu przycisku Bastion pojawi się pasek boczny zawierający trzy karty – RDP, SSH i Bastion. Jeśli bastion został aprowidywny dla sieci wirtualnej, karta Bastion jest domyślnie aktywna. Jeśli nie udostępnino programu Bastion dla sieci wirtualnej, zobacz [Konfigurowanie bastionu](bastion-create-host-portal.md).

   ![Bastion](./media/bastion-connect-vm-ssh/bastion.png)
1. Wprowadź nazwę użytkownika i wybierz **klucz prywatny SSH z pliku lokalnego**.
1. Kliknij przycisk **Przeglądaj** (ikonę folderu w pliku lokalnym).
1. Wyszukaj plik, a następnie kliknij przycisk **Otwórz**.
1. Kliknij **przycisk Połącz,** aby połączyć się z maszyną wirtualną. Po kliknięciu przycisku Połącz, SSH do tej maszyny wirtualnej zostanie otwarty bezpośrednio w witrynie Azure portal. To połączenie jest za pośrednictwem HTML5 przy użyciu portu 443 w usłudze Bastion za pośrednictwem prywatnego adresu IP maszyny wirtualnej.

## <a name="next-steps"></a>Następne kroki

Przeczytaj często zadawane pytania dotyczące [bastionu](bastion-faq.md)
