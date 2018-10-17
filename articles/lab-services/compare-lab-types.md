---
title: Porównanie różnych typów laboratoriów w usłudze Azure Lab Services | Microsoft Docs
description: Wyjaśnienie i porównanie różnych typów laboratoriów, które można utworzyć przy użyciu usługi Azure Lab Services
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: 8cf779f203850ca03942ba2395baf07412712610
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47092973"
---
# <a name="compare-managed-labs-in-azure-lab-services-and-devtest-labs"></a>Porównanie laboratoriów zarządzanych w usługach Azure Lab Services i DevTest Labs
Tworzyć możesz dwa typy laboratoriów: **laboratoria zarządzane** za pomocą usługi Azure Lab Services i **laboratoria niestandardowe** za pomocą usługi Azure DevTest Labs. Jeśli chcesz tylko wprowadzić to, czego potrzebujesz w laboratorium, i pozwolić usłudze na skonfigurowanie infrastruktury wymaganej w laboratorium oraz zarządzanie nią, wybierz jedno z **laboratoriów zarządzanych**. Obecnie **laboratorium na potrzeby zajęć** to jedyny typ laboratorium zarządzanego, który możesz utworzyć za pomocą usługi Azure Lab Services. Jeśli chcesz zarządzać własną infrastrukturą, utwórz laboratorium za pomocą usługi Azure DevTest Labs.

Poniższe sekcje zawierają więcej szczegółowych informacji o tych laboratoriach. 

## <a name="managed-lab-types"></a>Typy laboratoriów zarządzanych
Usługa Azure Lab Services umożliwia tworzenie laboratoriów, których infrastruktura jest zarządzana przez platformę Azure. Ten artykuł mówi o nich jako o laboratoriach zarządzanych. Laboratoria zarządzane to różnego rodzaju laboratoria, które spełniają Twoje specyficzne wymagania. Obecnie jedynym obsługiwanym typem laboratorium zarządzanego jest **laboratorium na potrzeby zajęć**. 

Laboratoria zarządzane pozwalają od razu rozpocząć działanie przy minimalnej konfiguracji. Sama usługa obsługuje całe zarządzanie infrastrukturą w laboratorium, od inicjowania maszyn wirtualnych do obsługi błędów i skalowania infrastruktury. Aby utworzyć laboratorium zarządzane, takie jak laboratorium na potrzeby zajęć, musisz najpierw utworzyć konto laboratorium dla Twojej organizacji. Konto laboratorium służy jako centralne konto, w ramach którego są zarządzane wszystkie laboratoria w organizacji. 

Podczas tworzenia i korzystania z zasobów platformy Azure w tych laboratoriach zarządzanych usługa tworzy zasoby i zarządza nimi w wewnętrznych subskrypcjach firmy Microsoft. Nie są one tworzone w ramach Twojej własnej subskrypcji platformy Azure. Usługa przechowuje informacje o użyciu tych zasobów w wewnętrznych subskrypcjach firmy Microsoft. To użycie jest rozliczane w ramach Twojej subskrypcji platformy Azure, która zawiera konto laboratorium.   

Poniżej przedstawiono niektóre **przypadki użycia laboratoriów zarządzanych**: 

- Zapewnij uczniom laboratorium maszyn wirtualnych, na których skonfigurowano dokładnie to, co jest potrzebne na zajęciach. Przyznaj każdemu uczniowi ograniczoną liczbę godzin używania maszyn wirtualnych do prac domowych lub projektów osobistych.
- Skonfiguruj pulę maszyn wirtualnych o wysokiej wydajności obliczeniowej do wykonywania badań wymagających znacznej mocy obliczeniowej lub graficznej. Uruchamiaj maszyny wirtualne zgodnie z potrzebami, a następnie usuwaj je po zakończeniu pracy. 
- Przenieś fizyczne laboratorium komputerowe swojej szkoły do chmury. Automatycznie skaluj liczbę maszyn wirtualnych tylko do maksymalnego progu użycia i kosztów ustawionych dla laboratorium.  
- Szybko aprowizuj laboratorium maszyn wirtualnych na potrzeby hostowania maratonu programistycznego. Gdy skończysz pracę, usuń laboratorium jednym kliknięciem. 


## <a name="devtest-labs"></a>DevTest Labs
W przypadku niektórych scenariuszy możesz chcieć samodzielnie zarządzać całą infrastrukturą i konfiguracją w ramach własnej subskrypcji. Aby to zrobić, możesz utworzyć laboratorium za pomocą usługi Azure DevTest Labs w witrynie Azure Portal. Dla tych laboratoriów nie jest konieczne tworzenie konta laboratorium. Te laboratoria nie są wyświetlane na koncie laboratorium (które istnieje dla laboratoriów zarządzanych).  

Poniżej przedstawiono niektóre **przypadki użycia laboratoriów DevTest Labs**: 

- Szybko aprowizuj laboratorium maszyn wirtualnych w celu hostowania maratonu programistycznego albo praktycznej sesji na konferencji. Gdy skończysz pracę, usuń laboratorium jednym kliknięciem. 
- Utwórz pulę maszyn wirtualnych ze skonfigurowaną Twoją aplikacją i umożliw zespołowi łatwe korzystanie z maszyn wirtualnych do usuwania usterek.  
- Zapewnij deweloperom maszyny wirtualne, na których skonfigurowano wszystkie potrzebne narzędzia. Zaplanuj automatyczne uruchamianie i zamykanie, aby zminimalizować koszty. 
- Wielokrotnie twórz laboratorium maszyn testowych jako część Twojego wdrożenia. Testuj najnowsze fragmenty i usuń maszyny testowe, gdy skończysz pracę. 
- Skonfiguruj wiele różnorodnie skonfigurowanych maszyn wirtualnych i wielu agentów testowych na potrzeby skalowania i testowania wydajności. 
- Oferuj sesje szkoleniowe swoim klientom, używając laboratorium skonfigurowanego przy użyciu najnowszej wersji swojego produktu. Przyznaj każdemu klientowi ograniczoną liczbę godzin korzystania z laboratorium. 


## <a name="managed-lab-types-vs-devtest-labs"></a>Typy laboratoriów zarządzanych a DevTest Labs
W poniższej tabeli porównano dwa rodzaje laboratoriów, które są obsługiwane przez usługę Azure Lab Services: 

| Funkcje | Laboratoria zarządzane | DevTest Labs |
| -------- | ----------------- | ---------- |
| Zarządzanie infrastrukturą platformy Azure w laboratorium. |  Automatycznie zarządzane przez usługę | Zarządzane samodzielnie  |
| Wbudowana odporność na problemy z infrastrukturą | Automatycznie obsługiwane przez usługę | Zarządzane samodzielnie  |
| Zarządzanie subskrypcjami | Usługa obsługuje alokację zasobów w ramach subskrypcji firmy Microsoft wspierających usługę. Skalowanie jest obsługiwane automatycznie przez usługę. | Samodzielnie zarządzasz w ramach własnej subskrypcji platformy Azure. Brak automatycznego skalowania subskrypcji. |
| Wdrożenie usługi Azure Resource Manager w laboratorium | Niedostępne | Dostępne |

## <a name="next-steps"></a>Następne kroki
Wprowadzenie do konfigurowania laboratorium przy użyciu usługi Azure Lab Services:

- [Konfigurowanie laboratorium na potrzeby zajęć](classroom-labs/tutorial-setup-classroom-lab.md)
- [Konfigurowanie laboratorium](tutorial-create-custom-lab.md)
