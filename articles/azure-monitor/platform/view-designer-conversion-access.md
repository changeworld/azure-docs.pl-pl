---
title: Projektant widoku usługi Azure Monitor do podsumowania konwersji skoroszytów i dostępu
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: fb53a966b0dc4959253ac0786ef09ef3c497e809
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658850"
---
# <a name="view-designer-to-workbooks-conversion-summary-and-access"></a>Wyświetlanie projektanta w podsumowaniu konwersji skoroszytów i dostępie
[Projektant widoku](view-designer.md) jest funkcją usługi Azure Monitor, która umożliwia tworzenie widoków niestandardowych ułatwiające wizualizację danych w obszarze roboczym usługi Log Analytics za pomocą wykresów, list i osi czasu. Są one stopniowo wycofywane i zastępowane skoroszytami, które zapewniają dodatkowe funkcje. W tym artykule opisano, jak utworzyć podsumowanie przeglądu i uprawnienia wymagane do uzyskania dostępu do skoroszytów.

## <a name="creating-your-workspace-summary-from-azure-dashboard"></a>Tworzenie podsumowania obszaru roboczego na podstawie pulpitu nawigacyjnego platformy Azure
Użytkownicy widoku projektanta mogą być zaznajomieni z kafelkiem przeglądu reprezentującym zestaw widoków. Aby zachować przegląd wizualny, taki jak podsumowanie obszaru roboczego projektanta widoku, skoroszyty oferują przypięte kroki, które można przypiąć do [pulpitu nawigacyjnego portalu Azure.](../../azure-portal/azure-portal-dashboards.md) Podobnie jak kafelki przeglądu w podsumowaniu obszaru roboczego, przypięte elementy skoroszytu będą łączyć się bezpośrednio z widokiem skoroszytu.

Możesz korzystać z funkcji wysokiego poziomu dostosowywania dostarczanych z pulpitami nawigacyjnymi platformy Azure, co umożliwia automatyczne odświeżanie, przenoszenie, dobieranie rozmiaru i dodatkowe filtrowanie przypiętych elementów i wizualizacji. 

![Pulpit nawigacyjny](media/view-designer-conversion-access/dashboard.png)

Utwórz nowy pulpit nawigacyjny platformy Azure lub wybierz istniejący pulpit nawigacyjny, aby rozpocząć przypinanie elementów skoroszytów.

Aby przypiąć pojedynczy element, musisz włączyć ikonę pinezki dla określonego kroku. Aby to zrobić, wybierz odpowiedni przycisk **Edytuj** dla swojego kroku, a następnie wybierz ikonę koła zębatego, aby otworzyć **ustawienia zaawansowane**. Zaznacz opcję **Zawsze pokazuj ikonę pinezki w tym kroku,** a w prawym górnym rogu kroku pojawi się ikona pinezki. Ten przypin umożliwia przypinanie określonych wizualizacji do pulpitu nawigacyjnego, takich jak kafelki przeglądowe.

![Krok przypięcia](media/view-designer-conversion-access/pin-step.png)


Możesz też przypiąć wiele wizualizacji ze skoroszytu lub całej zawartości skoroszytu do pulpitu nawigacyjnego. Aby przypiąć cały skoroszyt, wybierz pozycję **Edytuj** na górnym pasku narzędzi, aby przełączyć **tryb edycji**. Pojawi się ikona pinezki, która umożliwia przypięcie całego elementu skoroszytu lub wszystkich pojedynczych kroków i wizualizacji w skoroszycie.

![Przypnij wszystkie](media/view-designer-conversion-access/pin-all.png)



## <a name="sharing-and-viewing-permissions"></a>Uprawnienia do udostępniania i wyświetlania 
Skoroszyty mają zaletę, że są dokumentem prywatnym lub udostępnionym. Domyślnie zapisane skoroszyty zostaną zapisane w obszarze **Moje raporty,** co oznacza, że tylko twórca może wyświetlać ten skoroszyt.

Skoroszyty można udostępniać, wybierając ikonę **Udostępnij** na górnym pasku narzędzi w **trybie edycji**. Zostanie wyświetlony monit o przeniesienie skoroszytu do **raportów udostępnionych,** które wygenerują łącze zapewniające bezpośredni dostęp do skoroszytu.

Aby użytkownik mógł wyświetlić skoroszyt udostępniony, musi mieć dostęp zarówno do subskrypcji, jak i do grupy zasobów, w których skoroszyt jest zapisywany.

![Dostęp oparty na subskrypcji](media/view-designer-conversion-access/subscription-access.png)

## <a name="next-steps"></a>Następne kroki

- [Typowe zadania](view-designer-conversion-tasks.md)