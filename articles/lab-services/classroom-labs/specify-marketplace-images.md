---
title: Określ obrazy z witryny Marketplace dla laboratorium w Azure Lab Services
description: W tym artykule opisano sposób określania obrazów z portalu Marketplace, których twórca laboratorium może użyć do tworzenia laboratoriów w ramach konta laboratorium w Azure Lab Services.
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
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444669"
---
# <a name="specify-marketplace-images-available-to-lab-creators"></a>Określanie obrazów w portalu Marketplace dostępnych dla twórców laboratorium
Jako właściciel konta laboratorium możesz określić obrazy witryny Marketplace, których twórcy laboratorium mogą używać do tworzenia laboratoriów na koncie laboratorium. 

## <a name="select-images-available-for-labs"></a>Wybierz obrazy dostępne dla laboratoriów
W menu po lewej stronie wybierz pozycję **Obrazy w portalu Marketplace**. Domyślnie zobaczysz pełną listę obrazów (włączonych i wyłączonych). Tę listę można filtrować, aby wyświetlić tylko obrazy włączone/wyłączone, wybierając z listy rozwijanej u góry opcję **Tylko włączone**/**Tylko wyłączone**. 
    
![Strona Obrazy w portalu Marketplace](../media/tutorial-setup-lab-account/marketplace-images-page.png)

Obrazy witryny Marketplace wyświetlane na liście to tylko obrazy spełniające następujące warunki:
    
- Tworzą jedną maszynę wirtualną
- Używają usługi Azure Resource Manager do aprowizowania maszyn wirtualnych
- Nie wymagają zakupu dodatkowego planu licencjonowania

## <a name="disable-images-for-a-lab"></a>Wyłącz obrazy dla laboratorium 
Aby wyłączyć pojedynczy obraz dla laboratorium, wybierz pozycję **... (wielokropek)** w ostatniej kolumnie, a następnie wybierz pozycję **Wyłącz obraz**. 

![Wyłączanie jednego obrazu](../media/tutorial-setup-lab-account/disable-one-image.png) 

Alternatywnie możesz zaznaczyć pole wyboru przed nazwą obrazu, a następnie wybrać pozycję **Wyłącz wybrane obrazy** na pasku narzędzi. 

Aby wyłączyć wiele obrazów w tym samym czasie, zaznacz pola wyboru przed nazwami obrazów, a następnie wybierz pozycję **Wyłącz wybrane obrazy** na pasku narzędzi. 

![Wyłączanie wielu obrazów](../media/tutorial-setup-lab-account/disable-multiple-images.png) 


## <a name="enable-images-for-a-lab"></a>Włącz obrazy dla laboratorium
Aby włączyć wyłączony obraz, wybierz pozycję **... (wielokropek)** w ostatniej kolumnie, a następnie wybierz pozycję **Włącz obraz**. Alternatywnie możesz zaznaczyć pole wyboru przed nazwą obrazu, a następnie wybrać pozycję **Włącz wybrane obrazy** na pasku narzędzi. 

Aby wyłączyć wiele obrazów w tym samym czasie, zaznacz pola wyboru przed nazwami obrazów, a następnie wybierz pozycję **Włącz wybrane obrazy** na pasku narzędzi. 

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [As a lab owner, create and manage labs (Tworzenie laboratoriów i zarządzanie nimi jako właściciel laboratorium)](how-to-manage-classroom-labs.md)
- [As a lab owner, set up and publish templates (Konfigurowanie i tworzenie szablonów jako właściciel laboratorium)](how-to-create-manage-template.md)
- [As a lab owner, configure and control usage of a lab (Konfigurowanie i kontrolowanie użycia laboratorium jako właściciel laboratorium)](how-to-configure-student-usage.md)
- [Jako użytkownik laboratorium Uzyskuj dostęp do laboratoriów zajęć](how-to-use-classroom-lab.md)
