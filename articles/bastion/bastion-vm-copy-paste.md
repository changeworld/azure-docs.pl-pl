---
title: 'Skopiuj i Wklej do i z maszyny wirtualnej: Usługa Azure bastionu | Dokumentacja firmy Microsoft'
description: W tym artykule wyjaśniono sposób kopiowania i wklejania do i z maszyny Wirtualnej platformy Azure przy użyciu bastionu.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: 9d69d1a9fae258c9546a8c794fc0b0c68b952049
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191813"
---
# <a name="copy-and-paste-to-a-virtual-machine-azure-bastion-preview"></a>Skopiuj i Wklej do maszyny wirtualnej: Bastionu platformy Azure (wersja zapoznawcza)

Ten artykuł ułatwia kopiowanie i wklejanie tekstu do i z maszyn wirtualnych, korzystając z platformy Azure bastionu. Przed rozpoczęciem pracy z maszyną Wirtualną, upewnij się, że zostały wykonane kroki, aby [tworzenie hostem bastionu](bastion-create-host-portal.md). Następnie należy połączyć z maszyną wirtualną, którą chcesz pracować przy użyciu [RDP](bastion-connect-vm-rdp.md) lub [SSH](bastion-connect-vm-ssh.md).

> [!IMPORTANT]
> Ten podgląd publiczny nie jest objęty umową dotyczącą poziomu usług i nie należy korzystać z niego w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, mogą mieć ograniczone możliwości lub mogą nie być dostępne we wszystkich lokalizacjach platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

Dla przeglądarek, które obsługują dostęp zaawansowany interfejs API Schowka można skopiować i wkleić tekst między lokalnym urządzeniem i sesji zdalnej w taki sam sposób kopiowania i wklejania między aplikacjami na urządzeniu lokalnym. W przypadku innych przeglądarek możesz użyć paletę Narzędzie dostępu do Schowka bastionu.

  ![Zezwalaj na Schowka](./media/bastion-vm-manage/allow.png)

Obsługiwane jest tylko tekst kopiowania/wklejania. Dla bezpośredniego kopiowania i wklejania przeglądarka może wyświetlenie monitu o dostęp do Schowka po zainicjowaniu sesji bastionu. **Zezwalaj na** strony sieci web, aby uzyskać dostęp do Schowka.

## <a name="to"></a>Skopiuj do sesji zdalnej

Po nawiązaniu połączenia z maszynę wirtualną przy użyciu [witryny Azure portal](https://aka.ms/BastionHost) bastionu wersji zapoznawczej:

1. Skopiuj zawartość/tekstu z urządzenia lokalnego do lokalnego Schowka.
1. Podczas sesji zdalnej uruchomienie paletę Narzędzie dostępu do Schowka bastionu, wybierając dwóch strzałek. Strzałki znajdują się na środku po lewej stronie sesji.

    ![Narzędzie palety](./media/bastion-vm-manage/left.png)

    ![Schowka](./media/bastion-vm-manage/clipboard.png)

1. Zazwyczaj skopiowany tekst jest automatycznie wyświetlana na palecie Wklej kopię bastionu. Jeśli tekst nie jest określony, wkleić go w obszarze tekstowym na palecie kolorów.
1. Po tekst znajduje się w obszarze tekstowym, wklej go do sesji zdalnej.

    ![Wklej](./media/bastion-vm-manage/local.png)

## <a name="from"></a>Skopiuj z sesji zdalnej

Po nawiązaniu połączenia z maszynę wirtualną przy użyciu [witryny Azure portal](https://aka.ms/BastionHost) bastionu wersji zapoznawczej:

1. Skopiuj zawartość/tekstu w sesji zdalnej do zdalnej Schowka (za pomocą klawiszy Ctrl-C).

    ![Narzędzie palety](./media/bastion-vm-manage/remote.png)

1. Podczas sesji zdalnej uruchomienie paletę Narzędzie dostępu do Schowka bastionu, wybierając dwóch strzałek. Strzałki znajdują się na środku po lewej stronie sesji.

    ![Schowka](./media/bastion-vm-manage/clipboard2.png)

1. Zazwyczaj skopiowany tekst jest automatycznie wyświetlana na palecie Wklej kopię bastionu. Jeśli tekst nie jest określony, wkleić go w obszarze tekstowym na palecie kolorów.
1. Po tekst znajduje się w obszarze tekstowym, wklej go na urządzeniu lokalnym.

    ![Wklej](./media/bastion-vm-manage/local2.png)
 
## <a name="next-steps"></a>Kolejne kroki

Odczyt [bastionu — często zadawane pytania](bastion-faq.md).