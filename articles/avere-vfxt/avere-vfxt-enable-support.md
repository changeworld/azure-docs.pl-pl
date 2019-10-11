---
title: Włączanie obsługi avere vFXT — Azure
description: Jak włączyć obsługę przekazywania z avere vFXT dla platformy Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: rohogue
ms.openlocfilehash: ac7db46a681fcde6bfcbb7695e2d66724f738918
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256218"
---
# <a name="enable-support-uploads"></a>Włączanie przekazywania pomocy technicznej

Avere vFXT dla platformy Azure może automatycznie przekazywać dane pomocy technicznej dotyczące klastra. Te operacje przekazywania umożliwiają personelowi pomocy technicznej dostarczenie najlepszej możliwej usługi klienta.

## <a name="steps-to-enable-uploads"></a>Kroki umożliwiające przekazywanie

Wykonaj następujące kroki w panelu sterowania avere, aby aktywować pomoc techniczną. (Przeczytaj [dostęp do klastra vFXT,](avere-vfxt-cluster-gui.md) aby dowiedzieć się, jak otworzyć Panel sterowania avere).

1. Przejdź do karty **Ustawienia** w górnej części strony.
1. Kliknij link **Pomoc techniczna** po lewej stronie i zaakceptuj zasady ochrony prywatności.

   ![Zrzut ekranu przedstawiający panel sterowania avere i okno podręczne z przyciskiem Potwierdź, aby zaakceptować zasady ochrony prywatności](media/avere-vfxt-privacy-policy.png)

1. Kliknij trójkąt z lewej strony **informacji o kliencie** , aby rozwinąć sekcję.
1. Kliknij przycisk ponownie **Sprawdź poprawność przekazywania informacji** .
1. Ustaw nazwę obsługi klastra na **unikatową nazwę klastra** — upewnij się, że jednoznacznie identyfikuje klaster, aby obsługiwał personel.
1. Zaznacz pola wyboru dotyczące **monitorowania statystyk**, **przekazywania informacji ogólnych**i **przekazywania informacji o awarii**.
1. Kliknij przycisk **Prześlij**.

   ![Zrzut ekranu przedstawiający sekcję ukończonych informacji o kliencie strony ustawień pomocy technicznej](media/avere-vfxt-support-info.png)

1. Kliknij trójkąt po lewej stronie **bezpiecznej proaktywnej pomocy technicznej (SPS)** , aby rozwinąć sekcję.
1. Zaznacz pole wyboru **Włącz połączenie programu SPS**.
1. Kliknij przycisk **Prześlij**.

   ![Zrzut ekranu przedstawiający sekcję ukończona obsługa bezpiecznych proaktywnie na stronie ustawień obsługi](media/avere-vfxt-support-sps.png)

## <a name="next-steps"></a>Następne kroki

Jeśli konieczne jest dodanie lokalnego lub istniejącego systemu magazynu w chmurze do klastra, postępuj zgodnie z instrukcjami w temacie [Konfigurowanie magazynu](avere-vfxt-add-storage.md). 

Aby rozpocząć dołączanie klientów do klastra, przeczytaj artykuł [Instalowanie klastra avere vFXT](avere-vfxt-mount-clients.md).