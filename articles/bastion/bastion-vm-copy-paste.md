---
title: 'Kopiowanie i wklejanie do i z maszyny wirtualnej: Bastion platformy Azure'
description: W tym artykule dowiesz się, jak kopiować i wklejać do i z maszyny Wirtualnej platformy Azure przy użyciu bastionu.
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 0af0fdfa1b0bcbb06d9424390e7ca8c8f406bcb3
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619301"
---
# <a name="copy-and-paste-to-a-virtual-machine-azure-bastion"></a>Kopiowanie i wklejenie do maszyny wirtualnej: Bastion platformy Azure

Ten artykuł ułatwia kopiowanie i wklejanie tekstu do i z maszyn wirtualnych podczas korzystania z usługi Azure Bastion. Przed rozpoczęciem pracy z maszyną wirtualną upewnij się, że postępujesz do kroków, aby [utworzyć host bastionu](bastion-create-host-portal.md). Następnie połącz się z maszyną wirtualną, z którą chcesz pracować, korzystając z [protokołu RDP](bastion-connect-vm-rdp.md) lub [SSH](bastion-connect-vm-ssh.md).

W przypadku przeglądarek obsługujących zaawansowany dostęp do interfejsu API Schowka można kopiować i wklejać tekst między urządzeniem lokalnym a sesją zdalną w taki sam sposób, w jaki można kopiować i wklejać między aplikacjami na urządzeniu lokalnym. W przypadku innych przeglądarek można użyć palety narzędzi dostępu do schowka Bastion.

   ![Zezwalaj na schowek](./media/bastion-vm-manage/allow.png)

Obsługiwane jest tylko kopiowanie/wklejanie tekstu. W przypadku bezpośredniego kopiowania i wklejania przeglądarka może monitować o dostęp do schowka podczas inicjowania sesji Bastion. **Zezwól** stronie internetowej na dostęp do schowka.

## <a name="copy-to-a-remote-session"></a><a name="to"></a>Kopiowanie do sesji zdalnej

Po nawiązaniu połączenia z maszyną wirtualną za pomocą [portalu Azure ](https://portal.azure.com)wykonaj następujące kroki:

1. Skopiuj tekst/zawartość z urządzenia lokalnego do lokalnego schowka.
1. Podczas sesji zdalnej uruchom paletę narzędzi dostępu do schowka Bastion, wybierając dwie strzałki. Strzałki znajdują się po lewej stronie sesji.

   ![paleta narzędzi](./media/bastion-vm-manage/left.png)

   ![schowek](./media/bastion-vm-manage/clipboard.png)
1. Zazwyczaj skopiowany tekst jest automatycznie wyświetlany na palecie wklejanie kopii bastionu. Jeśli tekstu nie ma, wklej tekst w obszarze tekstowym na palecie.
1. Gdy tekst znajduje się w obszarze tekstowym, można go wkleić do sesji zdalnej.

   ![Wklej](./media/bastion-vm-manage/local.png)

## <a name="copy-from-a-remote-session"></a><a name="from"></a>Kopiowanie z sesji zdalnej

Po nawiązaniu połączenia z maszyną wirtualną za pomocą [portalu Azure ](https://portal.azure.com)wykonaj następujące kroki:

1. Skopiuj tekst/zawartość z sesji zdalnej do zdalnego schowka (za pomocą klawisza Ctrl-C).

   ![paleta narzędzi](./media/bastion-vm-manage/remote.png)
1. Podczas sesji zdalnej uruchom paletę narzędzi dostępu do schowka Bastion, wybierając dwie strzałki. Strzałki znajdują się po lewej stronie sesji.

   ![schowek](./media/bastion-vm-manage/clipboard2.png)
1. Zazwyczaj skopiowany tekst jest automatycznie wyświetlany na palecie wklejanie kopii bastionu. Jeśli tekstu nie ma, wklej tekst w obszarze tekstowym na palecie.
1. Gdy tekst znajduje się w obszarze tekstowym, można go wkleić do urządzenia lokalnego.

   ![Wklej](./media/bastion-vm-manage/local2.png)
 
## <a name="next-steps"></a>Następne kroki

Przeczytaj często zadawane pytania dotyczące [bastionu](bastion-faq.md).