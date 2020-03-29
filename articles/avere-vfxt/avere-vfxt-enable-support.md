---
title: Włącz obsługę avere vFXT — Azure
description: Jak włączyć przekazywanie pomocy technicznej z usługi Avere vFXT for Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/14/2019
ms.author: rohogue
ms.openlocfilehash: d12bbd1708ceb948aea982f9ed1ab36879e3751c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75415363"
---
# <a name="enable-support-uploads"></a>Włączanie przekazywania pomocy technicznej

Avere vFXT for Azure może automatycznie przekazywać dane pomocy technicznej dotyczące klastra. Te przesłane pliki pozwalają personelowi pomocy technicznej zapewnić najlepszą możliwą obsługę klienta.

## <a name="steps-to-enable-uploads"></a>Kroki umożliwiające przesyłanie

Wykonaj następujące kroki z Panelu sterowania Avere, aby aktywować obsługę. (Dostęp [do odczytu klastra vFXT,](avere-vfxt-cluster-gui.md) aby dowiedzieć się, jak otworzyć panel sterowania).

1. Przejdź do karty **Ustawienia** u góry.
1. Kliknij link **Pomoc techniczna** po lewej stronie i zaakceptuj politykę prywatności.

   ![Zrzut ekranu przedstawiający Panel sterowania Avere i wyskakujące okno z przyciskiem Potwierdź, aby zaakceptować zasady ochrony prywatności](media/avere-vfxt-privacy-policy.png)

1. Na stronie konfiguracji pomocy technicznej otwórz sekcję **Informacje o kliencie,** klikając trójkąt po lewej stronie.
1. Kliknij przycisk **Ponownie przywdziaj informacje o przekazywaniu.**
1. Ustaw nazwę pomocy technicznej klastra w **unikatowej nazwie klastra**. Upewnij się, że ta nazwa jednoznacznie identyfikuje klaster do obsługi personelu.
1. Zaznacz pola wyboru **Monitorowanie statystyk,** **Przesyłanie informacji ogólnych**i **Przekazywanie informacji o awariach**.
1. Kliknij **przycisk Prześlij**.

   ![Zrzut ekranu zawierający ukończoną sekcję informacji o kliencie na stronie ustawień pomocy technicznej](media/avere-vfxt-support-info.png)

1. Kliknij trójkąt po lewej stronie **bezpiecznej pomocy proaktywnej (SPS),** aby rozwinąć sekcję.
1. Zaznacz pole wyboru **Włącz łącze SPS**.
1. Kliknij **przycisk Prześlij**.

   ![Zrzut ekranu zawierający ukończoną sekcję Bezpieczne wsparcie proaktywne na stronie ustawień pomocy technicznej](media/avere-vfxt-support-sps.png)

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz dodać lokalny lub istniejący system magazynowania w chmurze do klastra, postępuj zgodnie z instrukcjami w [temacie Konfigurowanie magazynu](avere-vfxt-add-storage.md).

Jeśli chcesz rozpocząć dołączanie klientów do klastra, przeczytaj artykuł [Zainstaluj klaster Avere vFXT](avere-vfxt-mount-clients.md).
