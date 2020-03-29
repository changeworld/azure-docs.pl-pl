---
title: Dowiedz się więcej o trybach aranżacji dla zestawów skalowania maszyny wirtualnej na platformie Azure
description: Dowiedz się więcej o trybach aranżacji dla zestawów skalowania maszyny wirtualnej na platformie Azure.
author: shandilvarun
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: vashan
ms.openlocfilehash: 4a0be30f181921461ad0bacea6f18ce439d22353
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76279061"
---
# <a name="orchestration-mode-preview"></a>Tryb aranżacji (podgląd)

Zestawy skalowania maszyn wirtualnych zapewniają logiczne grupowanie maszyn wirtualnych zarządzanych przez platformę. Za pomocą zestawów skalowania można utworzyć model konfiguracji maszyny wirtualnej, automatycznie dodać lub usunąć dodatkowe wystąpienia na podstawie obciążenia procesora CPU lub pamięci oraz automatycznie uaktualnić do najnowszej wersji systemu operacyjnego. Tradycyjnie zestawy skalowania umożliwiają tworzenie maszyn wirtualnych przy użyciu modelu konfiguracji maszyny wirtualnej dostarczonego w czasie tworzenia zestawu skalowania, a zestaw skalowania może zarządzać tylko maszynami wirtualnymi, które są niejawnie tworzone na podstawie modelu konfiguracji.

Za pomocą trybu aranżacji zestawu skalowania (podgląd) można teraz wybrać, czy zestaw skalowania powinien aranżować maszyny wirtualne, które są tworzone jawnie poza modelem konfiguracji zestawu skalowania, czy wystąpienia maszyn wirtualnych utworzone niejawnie na podstawie modelu konfiguracji. Tryb aranżacji zestawu skalowania ułatwia również projektowanie infrastruktury maszyny Wirtualnej pod kątem wysokiej dostępności, wdrażając te maszyny wirtualne w domenach błędów i strefach dostępności.


Zestawy skalowania maszyny wirtualnej będą obsługiwać 2 różne tryby aranżacji:

- ScaleSetVM — wystąpienia maszyny wirtualnej dodane do zestawu skalowania są oparte na modelu konfiguracji zestawu skalowania. Cykl życia wystąpienia maszyny wirtualnej — tworzenie, aktualizowanie, usuwanie — jest zarządzany przez zestaw skalowania.
- Maszyny wirtualne (maszyny wirtualne) — maszyny wirtualne utworzone poza zestawem skalowania można jawnie dodać do zestawu skalowania. 
 

> [!IMPORTANT]
> Tryb aranżacji jest definiowany podczas tworzenia zestawu skalowania i nie można go później zmienić ani zaktualizować. 
> 
> Ta funkcja zestawów skalowania maszyny wirtualnej jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. 
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="orchestration-modes"></a>Tryby aranżacji

|                             | "orchestrationMode": "VM" (VirtualMachine) | "orchestrationMode": "ScaleSetVM" (VirtualMachineScaleSetVM) |
|-----------------------------|--------------------------------------------|--------------------------------------------------------------|
| Model konfiguracji maszyny Wirtualnej      | Brak                                       | Wymagany |
| Dodawanie nowej maszyny Wirtualnej do zestawu skalowania  | Maszyny wirtualne są jawnie dodawane do zestawu skalowania podczas tworzenia maszyny wirtualnej. | Maszyny wirtualne są niejawnie tworzone i dodawane do zestawu skalowania na podstawie modelu konfiguracji maszyny Wirtualnej, liczby wystąpień i reguł skalowania automatycznego | |
| Usuwanie maszyny wirtualnej                   | Maszyny wirtualne muszą być usuwane indywidualnie, zestaw skalowania nie zostaną usunięte, jeśli ma żadnych maszyn wirtualnych w nim. | Maszyny wirtualne można usunąć indywidualnie, usunięcie zestawu skalowania spowoduje usunięcie wszystkich wystąpień maszyny Wirtualnej.  |
| Dołączanie/odłączanie maszyn wirtualnych           | Nieobsługiwane                              | Nieobsługiwane |
| Cykl życia wystąpienia (tworzenie za pomocą usuwania) | Maszyny wirtualne i ich artefakty (takie jak dyski i karty sieciowe) mogą być zarządzane niezależnie. | Wystąpienia i ich artefakty (takie jak dyski i karty sieciowe) są niejawne dla wystąpień zestawu skalowania, które je tworzą. Nie można ich odłączyć ani zarządzać oddzielnie poza zestawem skalowania |
| Domeny błędów               | Można definiować domeny błędów. 2 lub 3 w oparciu o wsparcie regionalne i 5 dla strefy dostępności. | Może definiować domeny błędów od 1 do 5 |
| Domeny aktualizacji              | Domeny aktualizacji są automatycznie mapowane na domeny błędów | Domeny aktualizacji są automatycznie mapowane na domeny błędów |
| Strefy dostępności          | Obsługuje wdrażanie regionalne lub maszyny wirtualne w jednej strefie dostępności | Obsługuje wdrażanie regionalne lub wiele stref dostępności; Może definiować strategię równoważenia stref |
| Skalowanie automatyczne                   | Nieobsługiwane                              | Obsługiwane |
| Uaktualnienie systemu operacyjnego                  | Nieobsługiwane                              | Obsługiwane |
| Aktualizacje modelu               | Nieobsługiwane                              | Obsługiwane |
| Formant wystąpienia            | Pełna kontrola maszyny Wirtualnej. Maszyny wirtualne mają w pełni kwalifikowany identyfikator URI, który obsługuje pełny zakres funkcji zarządzania maszynami wirtualnymi platformy Azure (takich jak zasady platformy Azure, usługa Azure Backup i usługa Azure Site Recovery) | Maszyny wirtualne są zasobami zależnymi zestawu skalowania. Wystąpienia są dostępne do zarządzania tylko za pośrednictwem zestawu skalowania. |
| Model wystąpienia              | Definicja modelu Microsoft.Compute/VirtualMachines. | Definicja modelu Microsoft.Compute/VirtualMachineScaleSets/VirtualMachines. |
| Pojemność                    | Można utworzyć pusty zestaw skalowania; do zestawu wag można dodać do 200 maszyn wirtualnych | Zestawy skalowania można zdefiniować za pomocą liczby wystąpień od 0 do 1000 |
| Move                        | Obsługiwane                                  | Obsługiwane |
| Pojedyncza grupa miejsc docelowych == fałsz | Nieobsługiwane                          | Obsługiwane |


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [Omówienie opcji dostępności](availability.md).
