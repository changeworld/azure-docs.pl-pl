---
title: Dołączanie lub odłączenie udostępnionej galerii obrazów w usłudze Azure Lab Services | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób dołączania udostępnionej galerii obrazów do laboratorium w klasie w usłudze Azure Lab Services.
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
ms.openlocfilehash: 00dbef7b4453ffcb54020340bde51f55827759a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284319"
---
# <a name="attach-or-detach-a-shared-image-gallery-in-azure-lab-services"></a>Dołączanie lub odłączanie udostępnionej galerii obrazów w usłudze Azure Lab Services
Nauczyciele/administrator laboratorium można zapisać obraz maszyny Wirtualnej szablonu w [galerii obrazów udostępnionych](../../virtual-machines/windows/shared-image-galleries.md) platformy Azure, aby był ponownie przez innych. W pierwszym kroku administrator laboratorium dołącza istniejącą galerię obrazów udostępnionych do konta laboratorium. Po dołączeniu udostępnionej galerii obrazów laboratoria utworzone na koncie laboratorium mogą zapisywać obrazy w galerii obrazów udostępnionych. Inni nauczyciele mogą wybrać ten obraz z udostępnionej galerii obrazów, aby utworzyć szablon dla swoich zajęć. 

Gdy obraz jest zapisywany w galerii obrazów udostępnionych, usługa Azure Lab Services replikuje zapisany obraz do innych regionów dostępnych w tej samej [lokalizacji geograficznej.](https://azure.microsoft.com/global-infrastructure/geographies/) Zapewnia, że obraz jest dostępny dla laboratoriów utworzonych w innych regionach w tej samej lokalizacji geograficznej. Zapisanie obrazów w udostępnionej galerii obrazów wiąże się z dodatkowymi kosztami, które obejmują koszt wszystkich replikowanych obrazów. Ten koszt jest oddzielony od kosztu użycia usług Azure Lab Services. Aby uzyskać więcej informacji na temat cennika udostępnionej galerii obrazów, zobacz [Galeria obrazów udostępnionych — rozliczenia]( https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#billing).

W tym artykule pokazano, jak dołączyć lub odłączyć galerię obrazów udostępnionych do konta laboratorium. 

> [!NOTE]
> Obecnie usługa Azure Lab Services obsługuje tworzenie szablonów maszyn wirtualnych na podstawie tylko **uogólnionych** obrazów maszyn wirtualnych (nie wyspecjalizowanych obrazów) w galerii obrazów udostępnionych. 


## <a name="configure-at-the-time-of-lab-account-creation"></a>Konfigurowanie w momencie tworzenia konta w laboratorium
Podczas tworzenia konta laboratorium można dołączyć galerię obrazów udostępnionych do konta laboratorium. Możesz wybrać istniejącą galerię obrazów udostępnionych z listy rozwijanej lub utworzyć nową. Aby utworzyć i dołączyć galerię obrazów udostępnionych do konta laboratorium, wybierz pozycję **Utwórz nowy**, wprowadź nazwę galerii i wprowadź **przycisk OK**. 

![Konfigurowanie udostępnionej galerii obrazów w momencie tworzenia konta w laboratorium](../media/how-to-use-shared-image-gallery/new-lab-account.png)

## <a name="configure-after-the-lab-account-is-created"></a>Konfigurowanie po utworzeniu konta laboratorium
Po utworzeniu konta laboratorium można wykonać następujące zadania:

- Tworzenie i dołączanie udostępnionej galerii obrazów
- Dołączanie udostępnionej galerii obrazów do konta laboratorium
- Odłączenie udostępnionej galerii obrazów z konta laboratorium

## <a name="create-and-attach-a-shared-image-gallery"></a>Tworzenie i dołączanie udostępnionej galerii obrazów
1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Wybierz pozycję **Wszystkie usługi** w menu po lewej stronie. Wybierz **usługi lab** w sekcji **DEVOPS.** Jeśli wybierzesz`*`gwiazdkę ( ) obok **pozycji Usługi laboratoryjne,** zostanie ono dodane do sekcji **ULUBIONE** w menu po lewej stronie. Od następnego razu wybierz **usługi lab** w obszarze **ULUBIONE**.

    ![Wszystkie usługi -> Usługi laboratoryjne](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Wybierz konto laboratorium, aby wyświetlić stronę **Konto laboratorium.** 
4. Wybierz **pozycję Galeria obrazów udostępnionych** w menu po lewej stronie i wybierz polecenie + **Utwórz** na pasku narzędzi.  

    ![Przycisk Utwórz galerię obrazów udostępnionych](../media/how-to-use-shared-image-gallery/new-shared-image-gallery-button.png)
5. W oknie **Tworzenie galerii obrazów udostępnionych** wprowadź **nazwę** galerii i wprowadź **przycisk OK**. 

    ![Tworzenie okna galerii obrazów udostępnionych](../media/how-to-use-shared-image-gallery/create-shared-image-gallery-window.png)

    Usługa Azure Lab Services tworzy galerię obrazów udostępnionych i dołącza ją do konta laboratorium. Wszystkie laboratoria utworzone na tym koncie laboratorium mają dostęp do dołączonej galerii obrazów udostępnionych. 

    ![Załączona galeria zdjęć](../media/how-to-use-shared-image-gallery/image-gallery-in-list.png)

    W dolnym okienku widoczne są obrazy w galerii obrazów udostępnionych. W tej nowej galerii nie ma żadnych obrazów. Gdy przesyłasz obrazy do galerii, zobaczysz je na tej stronie.     

    Wszystkie obrazy w dołączonej galerii obrazów udostępnionych są domyślnie włączone. Wybrane obrazy można włączyć lub wyłączyć, zaznaczając je na liście i używając przycisku **Włącz wybrane obrazy** lub Wyłącz wybrane **obrazy.**

## <a name="attach-an-existing-shared-image-gallery"></a>Dołączanie istniejącej galerii obrazów udostępnionych
Poniższa procedura pokazuje, jak dołączyć istniejącą galerię obrazów udostępnionych do konta laboratorium. 

1. Na stronie **Konto laboratorium** wybierz galerię **obrazów udostępnionych** po lewej stronie menu, a następnie wybierz pozycję **Dołącz** na pasku narzędzi. 

    ![Galeria zdjęć udostępnionych — przycisk Dodaj](../media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. Na stronie **Dołączanie istniejącej udostępnionej galerii obrazów** wybierz galerię obrazów udostępnionych i wybierz przycisk **OK**.

    ![Wybieranie istniejącej galerii](../media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. Zostanie wyświetlony następujący ekran: 

    ![Moja galeria na liście](../media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    W tym przykładzie nie ma jeszcze żadnych obrazów w galerii obrazów udostępnionych.

    Tożsamość usługi Azure Lab Services jest dodawana jako współautor do galerii obrazów udostępnionych, która jest dołączona do laboratorium. Umożliwia nauczycielom/administratorowi IT zapisywanie obrazów maszyn wirtualnych w udostępnionej galerii obrazów. Wszystkie laboratoria utworzone na tym koncie laboratorium mają dostęp do dołączonej galerii obrazów udostępnionych. 

    Wszystkie obrazy w dołączonej galerii obrazów udostępnionych są domyślnie włączone. Wybrane obrazy można włączyć lub wyłączyć, zaznaczając je na liście i używając przycisku **Włącz wybrane obrazy** lub Wyłącz wybrane **obrazy.** 

## <a name="detach-a-shared-image-gallery"></a>Odłączaj galerię obrazów udostępnionych
Tylko jedna udostępniona galeria zdjęć może być dołączona do laboratorium. Jeśli chcesz dołączyć inną galerię obrazów udostępnionych, odłącz bieżącą przed dołączeniem nowej. Aby odłączyć galerię obrazów udostępnionych od laboratorium, wybierz pozycję **Odłącz** na pasku narzędzi i potwierdź operację odłączania. 

![Odłącz galerię obrazów udostępnionych z konta laboratorium](../media/how-to-use-shared-image-gallery/detach.png)

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się, jak zapisać obraz laboratoryjny w galerii obrazów udostępnionych lub użyć obrazu z galerii obrazów udostępnionych do utworzenia maszyny Wirtualnej, zobacz [Jak używać udostępnionej galerii obrazów](how-to-use-shared-image-gallery.md).

Aby uzyskać więcej informacji na temat udostępnionych galerii obrazów w ogóle, zobacz [galerię zdjęć udostępnionych](../../virtual-machines/windows/shared-image-galleries.md).
