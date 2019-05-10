---
title: Dołączanie lub odłączanie galerii udostępnionego obrazu w usłudze Azure Lab Services | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dołączyć galerii udostępnionego obrazu do laboratorium w usłudze Azure Lab Services.
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
ms.openlocfilehash: de4e9fb4b15f4c346926fe46f23255c668204c2e
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65413886"
---
# <a name="attach-or-detach-a-shared-image-gallery-in-azure-lab-services"></a>Dołączanie lub odłączanie galerii udostępnionego obrazu w usłudze Azure Lab Services
Administrator nauczycieli/laboratorium można zapisać obrazu szablonu maszyny Wirtualnej na platformie Azure [galerii obrazów udostępnionych](../../virtual-machines/windows/shared-image-galleries.md) aby mogła zostać ponownie użyte przez inne osoby. Pierwszym krokiem administratora laboratorium dołącza istniejących galerii udostępnionego obrazu do konta laboratorium. Po dołączeniu galerii obrazów udostępnionych labs utworzonych w ramach konta laboratorium można zapisać obrazy do galerii obrazów udostępnionych. Inne nauczycieli można wybrać ten obraz z galerii obrazów udostępnionych, aby utworzyć szablon dla ich klas. 

W tym artykule pokazano, jak dołączanie lub odłączanie galerii obrazów udostępnionych na koncie laboratorium. 

## <a name="configure-at-the-time-of-lab-account-creation"></a>Konfigurowanie w czasie tworzenia konta laboratorium
Podczas tworzenia konta laboratorium galerii udostępnionego obrazu można dołączyć do konta laboratorium. Możesz wybrać istniejące galerii obrazów udostępnionych z listy rozwijanej lub Utwórz nową. Aby utworzyć i dołączyć galerii obrazów udostępnionych na koncie laboratorium, wybierz pozycję **Utwórz nową**, wprowadź nazwę dla galerii i wprowadź **OK**. 

![Konfigurowanie galerii obrazów udostępnionych w czasie tworzenia konta laboratorium](../media/how-to-use-shared-image-gallery/new-lab-account.png)

## <a name="configure-after-the-lab-account-is-created"></a>Skonfigurować po utworzeniu konta laboratorium
Po utworzeniu konta laboratorium, należy wykonać następujące zadania:

- Tworzenie i dołączanie galerii udostępnionego obrazu
- Dołącz galerii udostępnionego obrazu do konta laboratorium
- Odłącz galerii udostępnionego obrazu z konta laboratorium

## <a name="create-and-attach-a-shared-image-gallery"></a>Tworzenie i dołączanie galerii udostępnionego obrazu
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **Wszystkie usługi** w menu po lewej stronie. Wybierz **Lab Services** w **DEVOPS** sekcji. Jeśli wybierzesz gwiazdkę (`*`) obok pozycji **Lab Services**, jest ona dodawana do **ulubione** sekcji, w menu po lewej stronie. W następnym roku, wybierz **Lab Services** w obszarze **ulubione**.

    ![All Services -> Lab Services](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Wybierz konto usługi laboratorium, aby zobaczyć **konta laboratorium** strony. 
4. Wybierz **galerii obrazów współdzielona** w menu po lewej stronie, a następnie wybierz pozycję **+ Utwórz** na pasku narzędzi.  

    ![Utwórz przycisk galerii udostępnionego obrazu](../media/how-to-use-shared-image-gallery/new-shared-image-gallery-button.png)
5. W **galerii obrazów udostępnionych utwórz** oknie wprowadź **nazwa** galerii i wprowadź **OK**. 

    ![Utwórz okno Galeria udostępnionego obrazu](../media/how-to-use-shared-image-gallery/create-shared-image-gallery-window.png)

    Usługa Azure Lab Services tworzy galerii obrazów udostępnionych i dołączono go do konta laboratorium. Laboratoria wszystkich utworzonych w tym laboratorium konta mają dostęp do galerii dołączonego obrazu udostępnionych. 

    ![Galeria dołączonego obrazu](../media/how-to-use-shared-image-gallery/image-gallery-in-list.png)

    W dolnym okienku zobaczysz obrazów w galerii obrazów udostępnionych. W tej nowej galerii Brak obrazów. Podczas przekazywania obrazów w galerii, zobaczysz je na tej stronie.     

    Wszystkie obrazy w galerii obrazów udostępnionych dołączone są domyślnie włączone. Można włączyć lub wyłączyć wybrane obrazy, wybierając je na liście i za pomocą **włączyć wybrane obrazy** lub **Wyłącz wybrane obrazy** przycisku.

## <a name="attach-an-existing-shared-image-gallery"></a>Dołącz istniejące galerii udostępnionego obrazu
Poniższa procedura pokazuje, jak dołączyć istniejący galerii udostępnionego obrazu do konta laboratorium. 

1. Na **konta laboratorium** wybierz opcję **galerii obrazów współdzielona** w menu po lewej stronie, a następnie wybierz pozycję **Dołącz** na pasku narzędzi. 

    ![Udostępnione obrazu z galerii — Dodawanie przycisku](../media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. Na **dołączanie istniejących galerii obrazów współdzielona** stronie, wybierz z galerii obrazów udostępnionych, a następnie wybierz **OK**.

    ![Wybieranie istniejącej kontrolki Galeria](../media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. Zostanie wyświetlony następujący ekran: 

    ![Moja galeria na liście](../media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    W tym przykładzie Brak obrazów w galerii obrazów udostępnionych jeszcze.

    Tożsamość platformy Azure Lab Services jest dodawany jako współautora do galerii obrazów udostępnionego, który jest dołączony do laboratorium. Umożliwia nauczycielom / administrator IT, aby zapisać maszynę wirtualną obrazy do galerii obrazów udostępnionych. Laboratoria wszystkich utworzonych w tym laboratorium konta mają dostęp do galerii dołączonego obrazu udostępnionych. 

    Wszystkie obrazy w galerii obrazów udostępnionych dołączone są domyślnie włączone. Można włączyć lub wyłączyć wybrane obrazy, wybierając je na liście i za pomocą **włączyć wybrane obrazy** lub **Wyłącz wybrane obrazy** przycisku. 

## <a name="save-an-image-to-the-shared-image-gallery"></a>Zapisz obraz do galerii obrazów udostępnionych
Po dołączeniu galerii udostępnionego obrazu administrator konta laboratorium lub nauczyciel można zapisać lub przekazać obraz do galerii obrazów udostępnionych, dzięki czemu mogą być ponownie używane przez inne nauczycieli. Aby uzyskać instrukcje dotyczące przekazywania obrazu do galerii obrazów udostępnionych, zobacz [Przegląd galerii obrazów współdzielona](../../virtual-machines/windows/shared-images.md). 

> [!NOTE]
> Curently laboratoriów na potrzeby zajęć interfejsu użytkownika (UI) nie obsługuje zapisywania obrazu laboratorium do galerii obrazów udostępnionych. 

## <a name="detach-a-shared-image-gallery"></a>Odłącz galerii udostępnionego obrazu
Tylko jedna Galeria udostępnionego obrazu można dołączyć do laboratorium. Jeśli chcesz dołączyć inny galerii obrazów udostępnionego, należy odłączyć bieżącej subskrypcji, przed dołączeniem na nową. Aby odłączyć galerii udostępnionego obrazu działanie w środowisku laboratoryjnym, wybierz **Odłącz** na pasku narzędzi i upewnij się, operacja odłączenia. 

![Odłącz galerii udostępnionego obrazu z konta laboratorium](../media/how-to-use-shared-image-gallery/detach.png)

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej o tym, jak zapisać obraz laboratorium do galerii obrazów udostępnionych lub użyć obrazu z galerii obrazów udostępnionych, utworzyć maszynę Wirtualną, zobacz [sposób korzystania z galerii obrazów udostępnionych](how-to-use-shared-image-gallery.md).

Aby uzyskać więcej informacji na temat udostępnionych galerie obrazów, ogólnie rzecz biorąc, zobacz [galerii obrazów udostępnionych](../../virtual-machines/windows/shared-image-galleries.md).
