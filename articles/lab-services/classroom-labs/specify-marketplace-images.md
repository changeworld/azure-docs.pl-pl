---
title: Określanie obrazów witryny marketplace dla laboratorium w usługach Azure Lab Services
description: W tym artykule pokazano, jak określić obrazy portalu Marketplace, których twórca laboratorium może używać do tworzenia laboratoriów na koncie laboratoryjnym w usłudze Azure Lab Services.
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
ms.date: 04/10/2020
ms.author: spelluru
ms.openlocfilehash: a64dee6da521764a38fc60bee06545f6a561c297
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257706"
---
# <a name="specify-marketplace-images-available-to-lab-creators"></a>Określanie obrazów w portalu Marketplace dostępnych dla twórców laboratorium
Jako właściciel konta laboratorium możesz określić obrazy witryny Marketplace, których twórcy laboratorium mogą używać do tworzenia laboratoriów na koncie laboratorium. 

## <a name="select-images-available-for-labs"></a>Wybieranie obrazów dostępnych dla laboratoriów
W menu po lewej stronie wybierz pozycję **Obrazy w portalu Marketplace**. Domyślnie zobaczysz pełną listę obrazów (włączonych i wyłączonych). Listę można filtrować, aby wyświetlić tylko włączone/wyłączone obrazy, wybierając opcję **Włączono tylko**/**wyłączone** z listy rozwijanej u góry. 
    
![Strona Obrazy w portalu Marketplace](../media/tutorial-setup-lab-account/marketplace-images-page.png)

Obrazy witryny Marketplace wyświetlane na liście to tylko obrazy spełniające następujące warunki:
    
- Tworzą jedną maszynę wirtualną
- Używają usługi Azure Resource Manager do aprowizowania maszyn wirtualnych
- Nie wymagają zakupu dodatkowego planu licencjonowania

## <a name="disable-images-for-a-lab"></a>Wyłączanie obrazów w laboratorium 
Aby wyłączyć pojedynczy obraz dla laboratorium, wybierz **... (wielokropek)** w ostatniej kolumnie, a następnie wybierz pozycję **Wyłącz obraz**. 

![Wyłączanie jednego obrazu](../media/tutorial-setup-lab-account/disable-one-image.png) 

Alternatywnie zaznacz pole wyboru przed nazwą obrazu i wybierz pozycję **Wyłącz wybrane obrazy** na pasku narzędzi. 

Aby wyłączyć wiele obrazów jednocześnie, zaznacz pola wyboru przed nazwami obrazów i wybierz pozycję **Wyłącz zaznaczone obrazy** na pasku narzędzi. 

![Wyłączanie wielu obrazów](../media/tutorial-setup-lab-account/disable-multiple-images.png) 


## <a name="enable-images-for-a-lab"></a>Włączanie obrazów dla laboratorium
Aby włączyć wyłączony obraz, wybierz **... (wielokropek)** w ostatniej kolumnie, a następnie wybierz pozycję **Włącz obraz**. Alternatywnie zaznacz pole wyboru przed nazwą obrazu i wybierz pozycję **Włącz zaznaczone obrazy** na pasku narzędzi. 

Aby wyłączyć wiele obrazów jednocześnie, zaznacz pola wyboru przed nazwami obrazów i wybierz pozycję **Włącz zaznaczone obrazy** na pasku narzędzi. 

## <a name="enable-images-at-the-time-of-lab-creation"></a>Włączanie obrazów w momencie tworzenia laboratorium
Można włączyć więcej obrazów w czasie tworzenia laboratorium: 

1. Logowanie się do [witryny usługi Azure Lab Services](https://labs.azure.com) przy użyciu poświadczeń właściciela konta **laboratorium**
2. Wybierz domyślny obraz maszyny wirtualnej lub strzałkę w dół. 
3. Wybierz **pozycję Włącz więcej opcji obrazu**. 

    ![Włącz więcej opcji obrazu](../media/specify-marketplace-images/enable-more-images-menu.png)
4. Postępuj zgodnie z instrukcjami z poprzedniej sekcji, aby włączyć wybrane obrazy. 
5. Może być konieczne zamknięcie okna **Nowe laboratorium** i ponowne otwarcie go, aby wyświetlić obrazy wybrane w poprzednim kroku. 



## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [As a lab owner, create and manage labs (Tworzenie laboratoriów i zarządzanie nimi jako właściciel laboratorium)](how-to-manage-classroom-labs.md)
- [As a lab owner, set up and publish templates (Konfigurowanie i tworzenie szablonów jako właściciel laboratorium)](how-to-create-manage-template.md)
- [As a lab owner, configure and control usage of a lab (Konfigurowanie i kontrolowanie użycia laboratorium jako właściciel laboratorium)](how-to-configure-student-usage.md)
- [Jako użytkownik laboratorium, uzyskaj dostęp do laboratoriów szkolnych](how-to-use-classroom-lab.md)
