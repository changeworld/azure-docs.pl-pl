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
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: ad56041f853d030e3a286610fe4872bffecaee12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444669"
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

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [As a lab owner, create and manage labs (Tworzenie laboratoriów i zarządzanie nimi jako właściciel laboratorium)](how-to-manage-classroom-labs.md)
- [As a lab owner, set up and publish templates (Konfigurowanie i tworzenie szablonów jako właściciel laboratorium)](how-to-create-manage-template.md)
- [As a lab owner, configure and control usage of a lab (Konfigurowanie i kontrolowanie użycia laboratorium jako właściciel laboratorium)](how-to-configure-student-usage.md)
- [Jako użytkownik laboratorium, uzyskaj dostęp do laboratoriów szkolnych](how-to-use-classroom-lab.md)
