---
title: Używanie galerii obrazów udostępnionych w Azure Lab Services | Microsoft Docs
description: Dowiedz się, jak skonfigurować konto laboratorium do korzystania z galerii obrazów udostępnionych, aby użytkownik mógł udostępnić obraz innym osobom, a inny użytkownik może użyć obrazu, aby utworzyć szablon maszyny wirtualnej w laboratorium.
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
ms.openlocfilehash: 79aea7d8a4a969e0ccd42ef94d77a9aa396ab91c
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77591323"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Używanie galerii obrazów udostępnionych w Azure Lab Services
W tym artykule pokazano, jak nauczyciele/administrator laboratorium mogą zapisać obraz maszyny wirtualnej szablonu, aby był on ponownie używany przez inne osoby. Te obrazy są zapisywane w [galerii udostępnionych obrazów](../../virtual-machines/windows/shared-image-galleries.md)systemu Azure. Pierwszym krokiem jest dołączenie istniejącej galerii udostępnionych obrazów do konta laboratorium przez administratora laboratorium. Po dołączeniu do galerii obrazów udostępnionych laboratoria utworzone na koncie laboratorium mogą zapisywać obrazy w galerii obrazów udostępnionych. Inni nauczyciele mogą wybrać ten obraz z galerii obrazów udostępnionych, aby utworzyć szablon dla ich klas. 

> [!NOTE]
> Obecnie Azure Lab Services obsługuje tworzenie maszyn wirtualnych na podstawie **uogólnionych** obrazów maszyn wirtualnych (niewyspecjalizowanych obrazów) w galerii obrazów udostępnionych. 

## <a name="prerequisites"></a>Wymagania wstępne
- Utwórz udostępnioną galerię obrazów przy użyciu [Azure PowerShell](../../virtual-machines/windows/shared-images.md) lub [interfejsu wiersza polecenia platformy Azure](../../virtual-machines/linux/shared-images.md).
- Dołączono galerię obrazów udostępnionych do konta laboratorium. Instrukcje krok po kroku znajdują się w temacie [How to dołączanie lub odłączanie galerii obrazów udostępnionych](how-to-attach-detach-shared-image-gallery.md).


## <a name="save-an-image-to-the-shared-image-gallery"></a>Zapisywanie obrazu w galerii obrazów udostępnionych
Po dołączeniu udostępnionej galerii obrazów administrator konta laboratorium lub nauczyciel może zapisać obraz do galerii obrazów udostępnionych, aby można go było ponownie wykorzystać przez innych nauczycieli. 

1. Na stronie **szablon** laboratorium wybierz pozycję **Eksportuj do galerii obrazów udostępnionych** na pasku narzędzi.

    ![Przycisk zapisywania obrazu](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-button.png)
2. W oknie dialogowym **Eksportuj do galerii obrazów udostępnionych** wprowadź **nazwę obrazu**, a następnie wybierz pozycję **Eksportuj**. 

    ![Okno dialogowe eksportowania do udostępnionej galerii obrazów](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-dialog.png)
3. Postęp tej operacji można zobaczyć na stronie **szablonu** . Ta operacja może trwać jakiś czas. 

    ![Eksportowanie w toku](../media/how-to-use-shared-image-gallery/exporting-image-in-progress.png)
4. Po pomyślnym zakończeniu operacji eksportowania zostanie wyświetlony następujący komunikat:

    ![Eksportowanie zakończone](../media/how-to-use-shared-image-gallery/exporting-image-completed.png)

Możesz również przekazać obraz do galerii obrazów udostępnionych poza kontekstem laboratorium. Aby uzyskać więcej informacji, zobacz [Omówienie galerii obrazów udostępnionych](../../virtual-machines/windows/shared-images.md). 

## <a name="use-an-image-from-the-shared-image-gallery"></a>Korzystanie z obrazu z galerii obrazów udostępnionych
Nauczyciel/profesor może wybrać niestandardowy obraz dostępny w galerii obrazów udostępnionych dla szablonu podczas tworzenia nowego laboratorium.

![Korzystanie z obrazu maszyny wirtualnej z galerii](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat udostępnionych galerii obrazów, zobacz [Galeria obrazów udostępnionych](../../virtual-machines/windows/shared-image-galleries.md).
