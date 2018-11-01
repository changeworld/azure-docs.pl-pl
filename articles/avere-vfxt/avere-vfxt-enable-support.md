---
title: Włącz obsługę Avere vFXT - Azure
description: Jak włączyć obsługę operacji przekazywania z Avere vFXT dla platformy Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: bc21e06b704bfe3d25132092efbbf23f342acb14
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/31/2018
ms.locfileid: "50634047"
---
# <a name="enable-support-uploads"></a>Włącz przekazywanie pomocy technicznej

VFXT Avere dla platformy Azure automatycznie przekazać dane o pomocy technicznej dotyczące klastra. Przekazywanie, te umożliwiają personelu obsługi zapewnianie najlepszych usług możliwych klienta.

## <a name="steps-to-enable-uploads"></a>Czynności, aby umożliwić przekazywanie

Wykonaj poniższe kroki z poziomu Panelu sterowania Avere, aby aktywować pomocy technicznej. (Odczytu [dostęp do klastra vFXT](avere-vfxt-cluster-gui.md) informacje na temat otworzyć Panel sterowania Avere.)

1. Przejdź do **ustawienia** kartę u góry.
1. Kliknij przycisk **pomocy technicznej** link po lewej stronie i zaakceptuj zasady zachowania poufności informacji.

   ![Zrzut ekranu przedstawiający monit potwierdzenia akceptacji zasady zachowania poufności informacji](media/avere-vfxt-privacy-policy.png)
1. Kliknij przycisk trójkąta z lewej strony **informacje o kliencie** aby rozwinąć sekcję.
1. Ustaw nazwa_klastra pomocy technicznej w **unikatową nazwę klastra** — upewnij się, unikatowo identyfikuje klaster do personelu obsługi.
1. Zaznacz pola wyboru dla **monitorowania statystyk**, **przekazać ogólne informacje o**, i **przekazywanie informacji o awariach**.
1. Kliknij przycisk **weryfikacji przekazywania informacji** przycisku.
1. Kliknij przycisk **przesłać**.
1. Kliknij przycisk trójkąta z lewej strony **Secure aktywne pomocy technicznej (SPS)** aby rozwinąć sekcję.
1. Pole wyboru dla **Włącz łącze dodatki Service Pack**.
1. Kliknij przycisk **przesłać**.

   ![Zrzut ekranu, zawierający wszystkie kroki włączania obsługi](media/avere-vfxt-support-info-steps.png)


## <a name="next-steps"></a>Kolejne kroki

Jeśli trzeba dodać do systemu magazynu lokalnego do klastra, postępuj zgodnie z instrukcjami [skonfigurować magazyn](avere-vfxt-add-storage.md). 

Jeśli wszystko jest gotowe rozpocząć dołączanie klientów do klastra, przeczytaj [instalacji klastra vFXT Avere](avere-vfxt-mount-clients.md).