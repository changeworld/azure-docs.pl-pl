---
title: Użyj galerii udostępnionego obrazu w usłudze Azure Lab Services | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować konta laboratorium do użycia galerii udostępnionego obrazu tak, aby użytkownik współużytkować obrazu z innymi i innego użytkownika, można użyć obrazu do utworzenia szablonu maszyny Wirtualnej w środowisku laboratoryjnym.
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
ms.date: 05/07/2019
ms.author: spelluru
ms.openlocfilehash: 8d8b6fffe197d4180b091518dcd1615d0e0b9d19
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65412853"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Użyj galerii udostępnionego obrazu w usłudze Azure Lab Services
Ten artykuł pokazuje, jak administrator nauczycieli/lab zapisać szablon obrazu maszyny wirtualnej, aby mogła zostać ponownie użyte przez inne osoby. Te obrazy są zapisywane na platformie Azure [galerii obrazów udostępnionych](../../virtual-machines/windows/shared-image-galleries.md). Pierwszym krokiem administratora laboratorium dołącza istniejących galerii udostępnionego obrazu do konta laboratorium. Po dołączeniu galerii obrazów udostępnionych labs utworzonych w ramach konta laboratorium można zapisać obrazy do galerii obrazów udostępnionych. Inne nauczycieli można wybrać ten obraz z galerii obrazów udostępnionych, aby utworzyć szablon dla ich klas. 

## <a name="prerequisites"></a>Wymagania wstępne
- Utwórz galerię udostępnionego obrazu przy użyciu [programu Azure PowerShell](../../virtual-machines/windows/shared-images.md) lub [wiersza polecenia platformy Azure](../../virtual-machines/linux/shared-images.md).
- Galeria obrazów udostępnionych dołączono do konta laboratorium. Aby uzyskać instrukcje krok po kroku, zobacz [jak dołączanie lub odłączanie udostępnione w galerii obrazów](how-to-attach-detach-shared-image-gallery.md).


## <a name="save-an-image-to-the-shared-image-gallery"></a>Zapisz obraz do galerii obrazów udostępnionych
Po dołączeniu galerii udostępnionego obrazu nauczyciel można zapisać lub przekazać obraz do galerii obrazów udostępnionych, dzięki czemu mogą być ponownie używane przez inne nauczycieli. Aby uzyskać instrukcje dotyczące przekazywania obrazu do galerii obrazów udostępnionych, zobacz [Przegląd galerii obrazów współdzielona](../../virtual-machines/windows/shared-images.md). 

> [!NOTE]
> Curently laboratoriów na potrzeby zajęć interfejsu użytkownika (UI) nie obsługuje zapisywania obrazu laboratorium do galerii obrazów udostępnionych. 

## <a name="use-an-image-from-the-shared-image-gallery"></a>Użyj obrazu z galerii udostępnionego obrazu
Nauczyciel/Profesor można wybrać obraz niestandardowy dostępnych w galerii obrazów udostępnionych szablonu podczas tworzenia nowego laboratorium.

![Użyj obrazu maszyny wirtualnej z galerii](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat galerie obrazów udostępnionych zobacz [galerii obrazów udostępnionych](../../virtual-machines/windows/shared-image-galleries.md).
