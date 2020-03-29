---
title: Role i zadania procesu nauki o danych zespołowych
description: Konspekt kluczowych składników, ról personelu i skojarzonych zadań dla grupy nauki o danych.
author: marktab
manager: marktab
editor: marktab
services: machine-learning
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c1ed731943abf0efdd99ea54d2318fa402835e08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720014"
---
# <a name="team-data-science-process-roles-and-tasks"></a>Role i zadania procesu nauki o danych zespołowych

Proces nauki o danych zespołu (TDSP) to struktura opracowana przez firmę Microsoft, która zapewnia ustrukturyzowana metodologię efektywnego tworzenia rozwiązań do analizy predykcyjnej i inteligentnych aplikacji. W tym artykule opisano kluczowe role personelu i skojarzone zadania dla zespołu nauki o danych standaryzującego ten proces.

Ten artykuł wprowadzający zawiera łącza do samouczków dotyczących konfigurowania środowiska TDSP. Samouczki zawierają szczegółowe wskazówki dotyczące korzystania z projektów devops platformy Azure, repozytoriów repozytoriów usługi Azure i planów platformy Azure.  Motywujący celem jest przejście od koncepcji poprzez modelowanie i wdrażanie.

Samouczki używają usługi Azure DevOps, ponieważ tak jest, jak zaimplementować TDSP w firmie Microsoft. Usługa Azure DevOps ułatwia współpracę, integrując zabezpieczenia oparte na rolach, zarządzanie i śledzenie elementów roboczych oraz hosting kodu, udostępnianie i kontrolę źródła. Samouczki używają również [maszyny wirtualnej](https://aka.ms/dsvm) do nauki o danych platformy Azure (DSVM) jako pulpitu analizy, który ma kilka popularnych narzędzi do nauki o danych wstępnie skonfigurowanych i zintegrowanych z oprogramowaniem firmy Microsoft i usługami platformy Azure. 

Samouczki można użyć do zaimplementowania TDSP przy użyciu innych narzędzi i środowisk hostingu kodu, planowania agile i programowania, ale niektóre funkcje mogą nie być dostępne.

## <a name="structure-of-data-science-groups-and-teams"></a>Struktura grup i zespołów analityki danych

Funkcje nauki o danych w przedsiębiorstwach są często zorganizowane w następującej hierarchii:

- Grupa analityki danych
  - Zespół/zespoły do nauki o danych w grupie

W takiej strukturze są liderzy grupowi i liderzy zespołu. Zazwyczaj projekt nauki o danych jest wykonywany przez zespół do nauki o danych. Zespoły do nauki o danych mają potencjalnych klientów projektu do zarządzania projektami i zadań związanych z zarządzaniem, a poszczególni analitycy danych i inżynierowie wykonują części projektu do nauki o danych i inżynierii danych. Początkowa konfiguracja projektu i zarządzanie są wykonywane przez potencjalnych klientów grupy, zespołu lub projektu.

## <a name="definition-and-tasks-for-the-four-tdsp-roles"></a>Definicja i zadania dla czterech ról TDSP
Przy założeniu, że jednostka do nauki o danych składa się z zespołów w grupie, istnieją cztery różne role dla personelu TDSP:

1. **Menedżer grupy:** Zarządza całą jednostką do nauki o danych w przedsiębiorstwie. Jednostka do nauki o danych może mieć wiele zespołów, z których każdy pracuje nad wieloma projektami nauki o danych w różnych branżach biznesowych. Menedżer grupy może delegować swoje zadania do surogatu, ale zadania skojarzone z rolą nie zmieniają się.
   
2. **Kierownik zespołu:** Zarządza zespołem w jednostce do nauki o danych w przedsiębiorstwie. Zespół składa się z wielu analityków danych. W przypadku małej jednostki do nauki o danych menedżer grupy i kierownik zespołu mogą być tą samą osobą.
   
3. **Kierownik projektu**: Zarządza codziennymi działaniami poszczególnych analityków danych w konkretnym projekcie do nauki o danych.
   
4. **Indywidualni współautorzy projektu:** Analitycy danych, analitycy biznesowi, inżynierowie danych, architekci i inni, którzy realizują projekt nauki o danych.

> [!NOTE]
> W zależności od struktury i wielkości przedsiębiorstwa jedna osoba może odgrywać więcej niż jedną rolę lub więcej niż jedna osoba może pełnić rolę.

### <a name="tasks-to-be-completed-by-the-four-roles"></a>Zadania, które mają zostać wykonane przez cztery role

Na poniższym diagramie przedstawiono zadania najwyższego poziomu dla każdej roli procesu nauki o danych zespołu. Ten schemat i następujący, bardziej szczegółowy zarys zadań dla każdej roli TDSP może pomóc wybrać samouczek, który jest potrzebny na podstawie swoich obowiązków.

![Omówienie ról i zadań](./media/roles-tasks/overview-tdsp-top-level.png)

## <a name="group-manager-tasks"></a>Zadania Menedżera grupy

Menedżer grupy lub wyznaczony administrator systemu TDSP wypełnia następujące zadania, aby przyjąć TDSP:

- Tworzy **organizację** Azure DevOps i projekt grupy w organizacji. 
- Tworzy **repozytorium szablonów projektu** w projekcie grupy Azure DevOps i wysiewa go z repozytorium szablonów projektu opracowanego przez zespół Microsoft TDSP. Repozytorium szablonów projektu TDSP firmy Microsoft zawiera:
  - **Znormalizowana struktura katalogów**, w tym katalogi danych, kodu i dokumentów.
  - Zestaw **standardowych szablonów dokumentów** do kierowania wydajnym procesem nauki o danych.
- Tworzy **repozytorium narzędzi**i zalążki go z repozytorium narzędzi opracowanego przez zespół Microsoft TDSP. Repozytorium narzędzi TDSP firmy Microsoft udostępnia zestaw przydatnych narzędzi, aby praca analityka danych była bardziej wydajna. Repozytorium narzędzi firmy Microsoft zawiera narzędzia do interaktywnego eksploracji danych, analizy, raportowania oraz modelowania i raportowania według planu bazowego.
- Konfiguruje **zasady kontroli zabezpieczeń** dla konta organizacji.

Aby uzyskać szczegółowe instrukcje, zobacz [Zadania menedżera grupy dla zespołu do nauki o danych](group-manager-tasks.md).

## <a name="team-lead-tasks"></a>Zadania prowadzące zespół

Kierownik zespołu lub wyznaczony administrator projektu wypełnia następujące zadania, aby przyjąć TDSP:

- Tworzy **projekt** zespołowy w organizacji Azure DevOps grupy.
- Tworzy **repozytorium szablonów projektu** w projekcie i wysiewa go z repozytorium szablonów projektu grupy skonfigurowane przez Menedżera grupy lub pełnomocnika.
- Tworzy **repozytorium narzędzi zespołu,** wysiewa go z repozytorium narzędzi grupy i dodaje narzędzia specyficzne dla zespołu do repozytorium.
- Opcjonalnie tworzy [magazyn plików platformy Azure](https://azure.microsoft.com/services/storage/files/) do przechowywania przydatnych zasobów danych dla zespołu. Inni członkowie zespołu mogą zainstalować ten udostępniony magazyn plików w chmurze na swoich pulpitach analizy.
- Opcjonalnie instaluje magazyn plików platformy Azure w **systemie DSVM** zespołu i dodaje do niego zasoby danych zespołu.
- Konfiguruje **kontrolę zabezpieczeń,** dodając członków zespołu i konfigurując ich uprawnienia.

Aby uzyskać szczegółowe instrukcje, zobacz [Zadania zespołu Lead dla zespołu do nauki o danych.](team-lead-tasks.md)


## <a name="project-lead-tasks"></a>Zadania prowadzące projekt

Kierownik projektu wypełnia następujące zadania, aby przyjąć TDSP:

- Tworzy **repozytorium projektu** w projekcie zespołowym i wysiewa go z repozytorium szablonów projektu.
- Opcjonalnie tworzy **magazyn plików platformy Azure** do przechowywania zasobów danych projektu.
- Opcjonalnie instaluje magazyn plików platformy Azure do **dsvm** i dodaje do niego zasoby danych projektu.
- Konfiguruje **kontrolę zabezpieczeń,** dodając członków projektu i konfigurując ich uprawnienia.

Aby uzyskać szczegółowe instrukcje, zobacz [Zadania prowadzące projekt dla zespołu do nauki o danych](project-lead-tasks.md).

## <a name="project-individual-contributor-tasks"></a>Zadania indywidualnego współautora projektu

Indywidualny współautor projektu, zwykle data scientist, przeprowadza następujące zadania przy użyciu TDSP:

- Klonuje **repozytorium projektu** utworzone przez prowadzącego projekt.
- Opcjonalnie montuje udostępnionego zespołu i projektu **magazynu plików platformy Azure** na ich maszyny **wirtualnej nauki o danych** (DSVM).
- Wykonuje projekt.

Aby uzyskać szczegółowe instrukcje dotyczące dołączania do projektu, zobacz [Zadania indywidualnego współautora projektu dla zespołu do nauki o danych.](project-ic-tasks.md)

## <a name="data-science-project-execution-workflow"></a>Przepływ pracy realizacji projektu nauki o danych

Postępować zgodnie z odpowiednimi samouczkami, analitycy danych, potencjalni klienci projektu i potencjalni klienci zespołu mogą tworzyć elementy robocze do śledzenia wszystkich zadań i etapów projektu od początku do końca. Korzystanie z usługi Azure Reppos promuje współpracę między analitykami danych i zapewnia, że artefakty generowane podczas wykonywania projektu są kontrolowane przez wersję i udostępniane przez wszystkich członków projektu. Usługa Azure DevOps umożliwia łączenie elementów roboczych usługi Azure Boards z gałęziami repozytorium repozytorium usługi Azure i łatwe śledzenie, co zostało zrobione dla elementu roboczego.

Na poniższym rysunku przedstawiono przepływ pracy TDSP do wykonania projektu:

![Typowy przepływ pracy projektu nauki o danych](./media/roles-tasks/overview-project-execute.png)

Kroki przepływu pracy można podzielić na trzy działania:

- Project Leads przeprowadzi planowanie sprintu
- Analitycy danych opracowują artefakty w gałęziach w `git` celu rozwiązania elementów roboczych
- Potencjalni klienci projektu lub inni członkowie zespołu wykonują przeglądy kodu i scalają działające gałęzie z gałęzią główną

Aby uzyskać szczegółowe instrukcje dotyczące przepływu pracy realizacji projektu, zobacz [Zwinne opracowywanie projektów do nauki o danych.](agile-development.md)

## <a name="tdsp-project-template-repository"></a>Repozytorium szablonów projektu TDSP

Użyj [repozytorium szablonów projektu](https://github.com/Azure/Azure-TDSP-ProjectTemplate) zespołu Microsoft TDSP, aby wspierać efektywne wykonywanie projektów i współpracę. Repozytorium zapewnia znormalizowaną strukturę katalogów i szablony dokumentów, których można używać do własnych projektów TDSP.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z bardziej szczegółowymi opisami ról i zadań zdefiniowanych przez proces nauki o danych zespołu:

- [Zadania menedżera grupy dla zespołu do nauki o danych](group-manager-tasks.md)
- [Zadania zespołu Lead dla zespołu do nauki o danych](team-lead-tasks.md)
- [Zadania prowadzące projekt dla zespołu do nauki o danych](project-lead-tasks.md)
- [Zadania indywidualnego współautora projektu dla zespołu do nauki o danych](project-ic-tasks.md)
