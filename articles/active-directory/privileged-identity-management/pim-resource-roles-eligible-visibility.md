---
title: Kwalifikujące się przypisania i widoczność zasobów w usłudze PIM — Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak przypisać członków jako kwalifikuje się role zasobów platformy Azure w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: fb52bc92c86261831d0e8d8e9e863a4863fe8fb9
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666893"
---
# <a name="eligible-assignments-and-resource-visibility-in-pim"></a>Kwalifikujące się przypisania i widoczność zasobów w usłudze PIM

Privileged Identity Management (PIM) dla ról zasobów platformy Azure zapewnia zwiększone zabezpieczenia dla organizacji, które mają zasoby platformy Azure o znaczeniu krytycznym. Zasób Administratorzy mogą używać usługi PIM, aby przypisać członków jako kwalifikuje się role zasobów. Dowiedz się więcej na temat przypisania różnych typów i Stany przypisania dla ról zasobów platformy Azure w poniższych sekcjach. 

## <a name="assignment-types"></a>Typy przypisań

Usługa PIM dla zasobów platformy Azure udostępnia dwa typy distinct przypisania:

- Uprawniona
- Aktywne

Kwalifikującymi się przypisaniami wymagają członek roli do wykonania działania, aby korzystać z roli. Akcje mogą obejmować sukcesy wyboru usługi Multi-Factor authentication, zapewniając uzasadnienie biznesowe lub żądanie zatwierdzenia od wyznaczone osoby zatwierdzające.

Aktywne przypisania nie wymagają elementu członkowskiego do wykonywania dowolnych akcji, aby użyć roli. Członkowie przypisani jako aktywny mają uprawnienia przypisane do roli przez cały czas.

## <a name="assignment-duration"></a>Czas trwania przypisania

Zasób Administratorzy mogą wybrać spośród dwóch opcji dla każdego typu przypisania, podczas konfigurowania ustawień usługi PIM dla roli. Te opcje stają się domyślny maksymalny czas, gdy członek jest przypisany do roli w usłudze PIM. 

Administrator może wybrać jeden z tych typów przypisania:

- Zezwalaj na przypisanie trwałego kwalifikowania się
- Zezwalaj na trwałe aktywne przypisanie

Lub administrator może wybrać jeden z następujących typów przypisania:

- Wygaszaj przypisania kwalifikowania się po
- Aktywne przypisania wygasają po

Jeśli administrator zasobów wybierze **Zezwalaj na trwałe kwalifikujące się przypisanie** lub **Zezwalaj na trwałe aktywne przypisanie**, wszystkich administratorów, Przypisz elementy członkowskie do zasobu, które można przypisać stałe w grupach.

Jeśli administrator zasobów wybierze **kwalifikujące się przypisania wygasają po** lub **aktywne przypisania wygasają po**, administrator zasobów kontroluje cykl życia przypisania poprzez wymaganie, wszystkie przydziały mają określoną datę początkową i końcową.

> [!NOTE] 
> Wszystkie przydziały, które mają określonej daty zakończenia, można ją odnawiać każdorazowo administratorom zasobów. Ponadto członkowie mogą inicjować żądań samoobsługi, aby [rozszerzanie lub odnawianie przypisania](pim-resource-roles-renew-extend.md).


## <a name="assignment-states"></a>Stany przypisania

Usługa PIM dla zasobów platformy Azure ma dwa stany distinct przypisania, które pojawiają się na **aktywnych ról** karcie **Moje role**, **role**, i **członków**widoków PIM. Te stany są następujące:

- Przypisany
- Aktywowano

Po wyświetleniu członkostwa, który znajduje się w **aktywnych ról**, możesz użyć wartości w **stanu** kolumny do rozróżniania użytkowników, którzy są **przypisane** jako aktywny i Użytkownicy, **aktywowano** kwalifikującego się przypisania i są obecnie aktywne.

## <a name="azure-resource-role-approval-workflow"></a>Przepływ pracy zatwierdzania ról zasobów platformy Azure

Za pomocą przepływu pracy zatwierdzania w usłudze PIM dla ról zasobów platformy Azure Administratorzy mogą dodatkowo ochrony lub ograniczyć dostęp do kluczowych zasobów. Oznacza to, że administratorzy mogą wymagać od zatwierdzenia do aktywowania przypisań ról.

Koncepcja hierarchii zasobu jest unikatowe dla ról zasobów platformy Azure. Taka hierarchia umożliwia dziedziczenia przypisań ról z obiektu zasobu nadrzędnego w dół do wszystkich zasobów podrzędnych w kontenerze nadrzędnym. 

Na przykład: Roberta, administratorem zasobów przypisuje Alicja jako kwalifikującego się elementu członkowskiego do roli właściciel, w ramach subskrypcji firmy Contoso przy użyciu usługi PIM. Z tym przypisaniem Alicja jest kwalifikujących się właścicielem wszystkich kontenerów grupy zasobów w ramach subskrypcji firmy Contoso. Alicja jest również właścicielem kwalifikujących się wszystkie zasoby (takie jak maszyny wirtualne) w każdej grupie zasobów subskrypcji.

Załóżmy, że istnieją trzy grupy zasobów w subskrypcji firmy Contoso: testów firmy Fabrikam, deweloperów firmy Fabrikam i produkcyjne firmy Fabrikam. Każda z tych grup zasobów zawiera jedną maszynę wirtualną.

Ustawień usługi PIM są skonfigurowane dla każdej roli zasobu. W przeciwieństwie do przypisania te ustawienia nie są dziedziczone i zastosować wyłącznie do roli zasobu.

Przykładzie: Robert korzysta z usługi PIM będą musieli wszystkich elementów członkowskich w roli właściciela zatwierdzenia żądania subskrypcji Contoso zostanie uaktywniony. Aby lepiej chronić zasoby w grupie zasobów Prod firmy Fabrikam, Robert wymaga zatwierdzenia dla członków roli właściciela tego zasobu. Role właściciela w firmie Fabrikam testowych i deweloperskich firmy Fabrikam nie wymagają zatwierdzenia aktywacji.

Gdy Alicja żądań aktywacji jej roli właściciela subskrypcji firmy Contoso, osoba zatwierdzająca musi zatwierdzić lub odrzucić swoje żądanie, zanim użytkownik staje się aktywny w roli. Jeśli Alicja zdecyduje się na [zakres jej aktywacji](pim-resource-roles-activate-your-roles.md) do grupy zasobów Prod firmy Fabrikam, osoba zatwierdzająca musi Zatwierdź lub Odrzuć to żądanie zbyt. Ale jeśli Alicja zdecyduje się na zakres jej aktywacji do jednego lub obu tych testów Fabrikam lub deweloperów firmy Fabrikam, zatwierdzenia nie jest wymagane.

Przepływ pracy zatwierdzania nie może być wymagane dla wszystkich członków roli. Rozważmy scenariusz, w których Twoja organizacja zatrudnia kilka kojarzy umowy, aby pomóc w rozwoju aplikacji, która jest uruchamiana w subskrypcji platformy Azure. Będąc administratorem zasobów pracownicy mają mieć dostępu uprawnionego bez wymagane zatwierdzenie, ale kojarzy kontraktu musi on zażądać zatwierdzenia. Aby skonfigurować przepływ pracy zatwierdzania dla tylko kojarzy kontraktu, utworzeniem roli niestandardowej takie same uprawnienia, jak rola przypisana do pracowników. Może wymagać zatwierdzenia do aktywowania tej roli niestandardowej. [Dowiedz się więcej o niestandardowych rolach](pim-resource-roles-custom-role-policy.md).

## <a name="next-steps"></a>Kolejne kroki

- [Przypisz role zasobów platformy Azure w usłudze PIM](pim-resource-roles-assign-roles.md)
