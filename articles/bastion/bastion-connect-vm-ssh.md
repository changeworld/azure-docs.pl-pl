---
title: Łączenie z maszyną Wirtualną systemu Linux, za pomocą usługi Azure bastionu | Dokumentacja firmy Microsoft
description: W tym artykule Dowiedz się, jak połączyć do maszyny wirtualnej systemu Linux za pomocą usługi Azure bastionu.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: 69548541d16db95f633400808f72aebaf59cff08
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477777"
---
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion-preview"></a>Nawiązywanie połączenia przy użyciu protokołu SSH z maszyną wirtualną systemu Linux przy użyciu bastionu Azure (wersja zapoznawcza)

W tym artykule przedstawiono, jak można bezpiecznie i bez przeszkód SSH do maszyn wirtualnych systemu Linux w usłudze Azure virtual network. Połączenie z maszyną wirtualną można nawiązywać bezpośrednio w witrynie Azure Portal. Gdy używasz usługi Azure Bastion, maszyny wirtualne nie wymagają klienta, agenta ani dodatkowego oprogramowania. Aby uzyskać więcej informacji na temat usługi Azure bastionu zobacz [Przegląd](bastion-overview.md).

Bastionu Azure umożliwia łączenie z maszyną wirtualną systemu Linux przy użyciu protokołu SSH. Można użyć nazwy użytkownika/hasła i klucze SSH do uwierzytelniania. Można się połączyć z maszyną wirtualną przy użyciu kluczy SSH przy użyciu:

* Wprowadź ręcznie klucz prywatny
* Plik, który zawiera informacje o kluczu prywatnym

Prywatny klucz SSH musi być w formacie, który rozpoczyna się od `"-----BEGIN RSA PRIVATE KEY-----"` i kończy `"-----END RSA PRIVATE KEY-----"`.

> [!IMPORTANT]
> Ten podgląd publiczny nie jest objęty umową dotyczącą poziomu usług i nie należy korzystać z niego w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, mogą mieć ograniczone możliwości lub mogą nie być dostępne we wszystkich lokalizacjach platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Przed rozpoczęciem

Upewnij się, że po skonfigurowaniu hostem bastionu platformy Azure, w ramach sieci wirtualnej, w której znajduje się maszyna wirtualna. Aby uzyskać więcej informacji, zobacz [tworzenie hosta platformy Azure bastionu](bastion-create-host-portal.md). Gdy usługa bastionu jest aprowizowana i wdrożone w sieci wirtualnej, można użyć go połączyć się z dowolnej maszyny Wirtualnej w tej sieci wirtualnej. W tej wersji zapoznawczej gdy używasz bastionu nawiązać połączenie, przyjęto założenie, że używasz protokołu RDP, połączyć się z maszyn wirtualnych Windows i SSH połączyć się z maszyn wirtualnych systemu Linux.

Aby można było utworzyć połączenie, wymagane są następujące role:

* Rola czytelnika na maszynie wirtualnej
* Rola czytelnika na karcie Sieciowej z prywatnym adresem IP maszyny wirtualnej
* Rola czytelnika na zasobie bastionu platformy Azure

## <a name="username"></a>Połączenie: Przy użyciu nazwy użytkownika i hasła


1.  Użyj [ten link](https://aka.ms/BastionHost) o otwarcie strony portalu w wersji zapoznawczej dla usługi Azure bastionu. Przejdź do maszyny wirtualnej, którą chcesz nawiązać połączenie, a następnie kliknij przycisk **Connect**. Maszyna wirtualna powinna być maszynę wirtualną z systemem Linux przy użyciu połączenia SSH.
1. Po kliknięciu przycisku Połącz pojawia się pasek boczny zawiera trzy karty — RDP, SSH i bastionu. Jeśli bastionu została aprowizowana dla sieci wirtualnej, na karcie bastionu jest domyślnie aktywny. Jeśli bastionu nie został aprowizowany dla sieci wirtualnej, zobacz [skonfigurować bastionu](bastion-create-host-portal.md). Jeśli nie widzisz **bastionu** na liście, nie została jeszcze otwarta portalu w wersji zapoznawczej. Otwórz za pomocą portalu [ten link](https://aka.ms/BastionHost).

      ![Połącz z maszyną Wirtualną](./media/bastion-connect-vm-ssh/bastion.png)

1. Wprowadź nazwę użytkownika i hasło protokołu SSH z maszyną wirtualną.
1. Kliknij przycisk **Connect** przycisku po wprowadzeniu klucza.

## <a name="privatekey"></a>Połączenie: Ręcznie wprowadź klucz prywatny

1.  Użyj [ten link](https://aka.ms/BastionHost) o otwarcie strony portalu w wersji zapoznawczej dla usługi Azure bastionu. Przejdź do maszyny wirtualnej, którą chcesz nawiązać połączenie, a następnie kliknij przycisk **Connect**. Maszyna wirtualna powinna być maszynę wirtualną z systemem Linux przy użyciu połączenia SSH.
1. Po kliknięciu przycisku Połącz pojawia się pasek boczny zawiera trzy karty — RDP, SSH i bastionu. Jeśli bastionu została aprowizowana dla sieci wirtualnej, na karcie bastionu jest domyślnie aktywny. Jeśli bastionu nie został aprowizowany dla sieci wirtualnej, zobacz [skonfigurować bastionu](bastion-create-host-portal.md). Jeśli nie widzisz **bastionu** na liście, nie została jeszcze otwarta portalu w wersji zapoznawczej. Otwórz za pomocą portalu [ten link](https://aka.ms/BastionHost).

      ![Połącz z maszyną Wirtualną](./media/bastion-connect-vm-ssh/bastion.png)

1. Wprowadź nazwę użytkownika, a następnie wybierz pozycję **prywatny klucz SSH**.
1. Wprowadź klucz prywatny do obszaru tekstu **prywatny klucz SSH** (lub wkleić ją bezpośrednio).
1. Kliknij przycisk **Connect** przycisku po wprowadzeniu klucza.

## <a name="ssh"></a>Połączenie: Przy użyciu pliku klucza prywatnego

1.  Użyj [ten link](https://aka.ms/BastionHost) o otwarcie strony portalu w wersji zapoznawczej dla usługi Azure bastionu. Przejdź do maszyny wirtualnej, którą chcesz nawiązać połączenie, a następnie kliknij przycisk **Connect**. Maszyna wirtualna powinna być maszynę wirtualną z systemem Linux przy użyciu połączenia SSH.

    ![Połącz z maszyną Wirtualną](./media/bastion-connect-vm-ssh/connect.png)

1. Po kliknięciu przycisku Połącz pojawia się pasek boczny zawiera trzy karty — RDP, SSH i bastionu. Jeśli bastionu została aprowizowana dla sieci wirtualnej, na karcie bastionu jest domyślnie aktywny. Jeśli bastionu nie został aprowizowany dla sieci wirtualnej, zobacz [skonfigurować bastionu](bastion-create-host-portal.md). Jeśli nie widzisz **bastionu** na liście, nie została jeszcze otwarta portalu w wersji zapoznawczej. Otwórz za pomocą portalu [ten link](https://aka.ms/BastionHost).

    ![Połącz z maszyną Wirtualną](./media/bastion-connect-vm-ssh/bastion.png)

1. Wprowadź nazwę użytkownika, a następnie wybierz pozycję **prywatny klucz SSH z pliku lokalnego**.
1. Kliknij przycisk **Przeglądaj** przycisku (ikona folderu w lokalnym pliku).
1. Przeglądaj w poszukiwaniu pliku, a następnie kliknij przycisk **Otwórz**.
1. Kliknij przycisk **Connect** nawiązać połączenia z maszyną Wirtualną. Po kliknięciu przycisku Połącz, SSH do tej maszyny wirtualnej bezpośrednio otworzy w witrynie Azure portal. To połączenie wykorzystuje protokół HTML5 przy użyciu portu 443 w usłudze bastionu przez prywatny adres IP swojej maszyny wirtualnej.

## <a name="next-steps"></a>Kolejne kroki

Odczyt [bastionu — często zadawane pytania](bastion-faq.md)
