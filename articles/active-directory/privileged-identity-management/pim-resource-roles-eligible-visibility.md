---
title: Privileged Identity Management zasobów Azure - kwalifikujących się zadania i widoczność zasobów | Dokumentacja firmy Microsoft
description: Zawiera opis sposobu przypisywania elementów członkowskich jako kwalifikujące się do zasobu ról.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 273b06c91d68a764fe814374c0eca6ed1698cc2e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="eligible-assignments-and-resource-visibility"></a>Kwalifikujące się zadania i widoczność zasobów

PIM ról zasobów platformy Azure udostępnia zwiększone zabezpieczenia dla organizacji mających krytyczne resoruces platformy Azure. PIM daje administratorom zasobów możliwość przypisania członków pomocy kwalifikują się do zasobu ról. Więcej informacji na temat przypisania różne typy i stanów poniższe role zasobów platformy Azure. 

## <a name="assignment-types"></a>Typy przypisania

PIM zasobów Azure oferuje dwa typy różne przypisania:

- Uprawniona
- Aktywne

Przydziały kwalifikujących się wymagają należącego do roli do wykonania akcji, aby użyć roli. Działania te mogą obejmować sukcesy wyboru uwierzytelnianie wieloskładnikowe, zapewniając uzasadnienie i żądania zatwierdzenia z wyznaczonych osób zatwierdzających.

Przydziały Active nie wymagają elementu członkowskiego do wykonywania dowolnych akcji, aby użyć roli. Elementy członkowskie przypisany jako aktywny mają uprawnienia dostarczane przez rolę przez cały czas.

## <a name="assignment-duration"></a>Czas trwania przypisania

Administratorzy zasobów można wybrać jedną z dwóch opcji dla każdego typu przydziału podczas konfigurowania ustawień usługi PIM w roli. Te opcje stają się domyślny maksymalny czas, kiedy element członkowski jest przypisany do roli w PIM.

- Zezwalaj na stałych przypisań kwalifikujących się
- Zezwalaj na stałych przypisań active

lub

- Przydziały kwalifikujących się po wygaśnie
- Ważność active przypisania po

Jeśli administrator zasobów wybierze opcję "Zezwalaj stałe kwalifikujących się przydziału" i/lub "Zezwalaj stałe aktywne przypisanie", w przypadku wszystkich administratorów, które przypisania członków do zasobu ma możliwość przypisania stałego członkostwa.

Wybieranie "Wygaśnięcia przypisania kwalifikujących się po" i/lub "Wygaśnięcia przypisania active po" zapewnia kontrolę nad cyklem życia przypisania, gdyż się, że wszystkie przypisania mają określonej daty rozpoczęcia i zakończenia.

>[!NOTE] 
>Wszystkie przypisania z datą zakończenia mogą być odnawiane przez administratorów zasobów i elementy Członkowskie mogą inicjować żądań samoobsługi, aby [rozszerzać lub odnowić przypisania](pim-resource-roles-renew-extend.md).


## <a name="assignment-states"></a>Stany przypisania

PIM dla zasobów platformy Azure ma dwa stany przypisania unikatowych pojawiające się na karcie "Aktywnych ról" Mój ról, ról i elementów członkowskich widoków PIM. Są następujące stany:

- Przypisany
- Aktywowane

Podczas wyświetlania członkostwa na liście "Aktywnych ról" kolumny "Stan" umożliwia odróżnienie użytkowników, którzy są "Przypisane" jako aktywny lub użytkowników tej "aktywny" przypisania kwalifikujących się i są obecnie aktywne.

## <a name="next-steps"></a>Kolejne kroki

[Przypisywanie ról usługi PIM](pim-resource-roles-assign-roles.md)

