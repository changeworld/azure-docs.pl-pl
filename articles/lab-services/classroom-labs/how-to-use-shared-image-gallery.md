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
ms.date: 03/28/2019
ms.author: spelluru
ms.openlocfilehash: 93136c7d685bd9fc8ec4bcdea3a900b28029059b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60695211"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Użyj galerii udostępnionego obrazu w usłudze Azure Lab Services
Ten artykuł pokazuje, jak administrator nauczycieli/lab zapisać szablon obrazu maszyny wirtualnej, aby mogła zostać ponownie użyte przez inne osoby. Te obrazy są zapisywane na platformie Azure [galerii obrazów udostępnionych](../../virtual-machines/windows/shared-image-galleries.md). Pierwszym krokiem administratora laboratorium dołącza istniejących galerii udostępnionego obrazu do konta laboratorium. Po dołączeniu galerii obrazów udostępnionych labs utworzonych w ramach konta laboratorium można zapisać obrazy do galerii obrazów udostępnionych. Inne nauczycieli można wybrać ten obraz z galerii obrazów udostępnionych, aby utworzyć szablon dla ich klas. 

## <a name="prerequisites"></a>Wymagania wstępne
Utwórz galerię udostępnionego obrazu przy użyciu [programu Azure PowerShell](../../virtual-machines/windows/shared-images.md) lub [wiersza polecenia platformy Azure](../../virtual-machines/linux/shared-images.md).

## <a name="attach-a-shared-image-gallery-to-a-lab-account"></a>Dołącz galerii udostępnionego obrazu do konta laboratorium
Poniższa procedura pokazuje, jak dołączyć galerii udostępnionego obrazu do konta laboratorium. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **Wszystkie usługi** w menu po lewej stronie. Wybierz **Lab Services** w **DEVOPS** sekcji. Jeśli wybierzesz gwiazdkę (`*`) obok pozycji **Lab Services**, jest ona dodawana do **ulubione** sekcji, w menu po lewej stronie. W następnym roku, wybierz **Lab Services** w obszarze **ulubione**.

    ![All Services -> Lab Services](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Wybierz konto usługi laboratorium, aby zobaczyć **konta laboratorium** strony. 
4. Wybierz **galerii obrazów współdzielona** w menu po lewej stronie, a następnie wybierz pozycję **Dołącz** na pasku narzędzi. 

    ![Udostępnione obrazu z galerii — Dodawanie przycisku](../media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. Na **dołączanie istniejących galerii obrazów współdzielona** stronie, wybierz z galerii obrazów udostępnionych, a następnie wybierz **OK**.

    ![Wybieranie istniejącej kontrolki Galeria](../media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. Zostanie wyświetlony następujący ekran: 

    ![Moja galeria na liście](../media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    W tym przykładzie Brak obrazów w galerii obrazów udostępnionych jeszcze.

Tożsamość platformy Azure Lab Services jest dodawany jako współautora do galerii obrazów udostępnionego, który jest dołączony do laboratorium. Umożliwia nauczycielom / administrator IT, aby zapisać maszynę wirtualną obrazy do galerii obrazów udostępnionych. Laboratoria wszystkich utworzonych w tym laboratorium konta mają dostęp do galerii dołączonego obrazu udostępnionych. 

Wszystkie obrazy w galerii obrazów udostępnionych dołączone są domyślnie włączone. Można włączyć lub wyłączyć wybrane obrazy, wybierając je na liście i za pomocą **włączyć wybrane obrazy** lub **Wyłącz wybrane obrazy** przycisku. 

## <a name="detach-a-shared-image-gallery"></a>Odłącz galerii udostępnionego obrazu
Tylko jedna Galeria udostępnionego obrazu można dołączyć do laboratorium. Jeśli chcesz dołączyć inny galerii obrazów udostępnionego, należy odłączyć bieżącej subskrypcji, przed dołączeniem na nową. Aby odłączyć galerii udostępnionego obrazu działanie w środowisku laboratoryjnym, wybierz **Odłącz** na pasku narzędzi i upewnij się, operacja odłączenia. 

## <a name="save-an-image-to-the-shared-image-gallery"></a>Zapisz obraz do galerii obrazów udostępnionych
Po dołączeniu galerii udostępnionego obrazu nauczyciel można zapisać obrazu szablonu do galerii obrazów udostępnionych, dzięki czemu mogą być ponownie używane przez inne nauczycieli.

![Zapisz obraz maszyny wirtualnej w galerii](../media/how-to-use-shared-image-gallery/save-virtual-machine.png)

## <a name="use-an-image-from-the-shared-image-gallery"></a>Użyj obrazu z galerii udostępnionego obrazu
Nauczyciel/Profesor można wybrać obraz niestandardowy dostępnych w galerii obrazów udostępnionych szablonu podczas tworzenia nowego laboratorium.

![Użyj obrazu maszyny wirtualnej z galerii](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat galerie obrazów udostępnionych zobacz [galerii obrazów udostępnionych](../../virtual-machines/windows/shared-image-galleries.md).
