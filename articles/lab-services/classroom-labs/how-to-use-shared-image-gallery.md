---
title: Korzystanie z galerii obrazów udostępnionych w usługach Azure Lab Services | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować konto laboratorium do używania galerii obrazów udostępnionych, aby użytkownik mógł udostępnić obraz innym, a inny użytkownik może użyć obrazu do utworzenia szablonu maszyny Wirtualnej w laboratorium.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2020
ms.author: spelluru
ms.openlocfilehash: c611ecdb5a2534f7368e533e3e19e6e3f96de57f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190453"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Używanie galerii obrazów udostępnionych w usługach Azure Lab Services
W tym artykule pokazano, jak nauczyciele/administrator laboratorium mogą zapisywać obraz maszyny wirtualnej szablonu, aby był ponownie odtwarzany przez innych. Te obrazy są zapisywane w [galerii obrazów udostępnionych](../../virtual-machines/windows/shared-image-galleries.md)platformy Azure . W pierwszym kroku administrator laboratorium dołącza istniejącą galerię obrazów udostępnionych do konta laboratorium. Po dołączeniu udostępnionej galerii obrazów laboratoria utworzone na koncie laboratorium mogą zapisywać obrazy w galerii obrazów udostępnionych. Inni nauczyciele mogą wybrać ten obraz z udostępnionej galerii obrazów, aby utworzyć szablon dla swoich zajęć. 

> [!NOTE]
> Obecnie usługa Azure Lab Services obsługuje tworzenie szablonów maszyn wirtualnych na podstawie tylko **uogólnionych** obrazów maszyn wirtualnych (nie wyspecjalizowanych obrazów) w galerii obrazów udostępnionych. 

## <a name="prerequisites"></a>Wymagania wstępne
- Utwórz galerię obrazów udostępnionych przy użyciu [programu Azure PowerShell](../../virtual-machines/windows/shared-images.md) lub [interfejsu wiersza polecenia platformy Azure.](../../virtual-machines/linux/shared-images.md)
- Do konta laboratorium dołączono galerię obrazów udostępnionych. Aby uzyskać instrukcje krok po kroku, zobacz [Jak dołączyć lub odłączyć galerię obrazów udostępnionych](how-to-attach-detach-shared-image-gallery.md).


## <a name="save-an-image-to-the-shared-image-gallery"></a>Zapisywanie obrazu w galerii obrazów udostępnionych
Po dołączeniu udostępnionej galerii obrazów administrator konta laboratorium lub nauczyciel może zapisać obraz w udostępnionej galerii obrazów, aby mógł być ponownie odtwarzany przez innych nauczycieli. 

1. Na stronie **Szablon** dla laboratorium wybierz pozycję **Eksportuj do galerii obrazów udostępnionych** na pasku narzędzi.

    ![Przycisk Zapisz obraz](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-button.png)
2. W oknie dialogowym **Eksportowanie do udostępnionej galerii obrazów** wprowadź **nazwę obrazu,** a następnie wybierz pozycję **Eksportuj**. 

    ![Okno dialogowe Eksportowanie do galerii obrazów udostępnionych](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-dialog.png)
3. Postęp tej operacji można zobaczyć na stronie **Szablon.** Ta operacja może kiedyś potrwać. 

    ![Eksport w toku](../media/how-to-use-shared-image-gallery/exporting-image-in-progress.png)
4. Po pomyślnym wyeksportowanie zostanie wyświetlony następujący komunikat:

    ![Eksport zakończony](../media/how-to-use-shared-image-gallery/exporting-image-completed.png)

Obraz można również przekazać do galerii obrazów udostępnionych poza kontekstem laboratorium. Aby uzyskać więcej informacji, zobacz [Omówienie galerii obrazów udostępnionych](../../virtual-machines/windows/shared-images.md). 

## <a name="use-an-image-from-the-shared-image-gallery"></a>Używanie obrazu z galerii obrazów udostępnionych
Nauczyciel/profesor może wybrać niestandardowy obraz dostępny w galerii obrazów udostępnionych dla szablonu podczas tworzenia nowego laboratorium.

![Użyj obrazu maszyny wirtualnej z galerii](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat udostępnionych galerii obrazów, zobacz [galerię obrazów udostępnionych](../../virtual-machines/windows/shared-image-galleries.md).
