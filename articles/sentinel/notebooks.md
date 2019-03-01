---
title: Możliwości myślistwo przy użyciu notesów w usłudze Azure przez wartownika w wersji zapoznawczej | Dokumentacja firmy Microsoft
description: W tym artykule opisano korzystanie z notesów możliwości myślistwo przez wartownika platformy Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: c6202f3e945d817c5e8fd7caa340a3e237bc34bd
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2019
ms.locfileid: "56993111"
---
# <a name="use-notebooks-to-hunt-for-anomalies"></a>Korzystanie z notesów do wyszukiwania anomalii

> [!IMPORTANT]
> Wartownik platformy Azure jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Wartownik Azure korzysta z możliwości interaktywne notesów programu Jupyter w celu zapewnienia wglądu i działań w celu zbadania lub możliwe anomalii zabezpieczeń w danym środowisku. Azure wartownik pochodzi załadowanych za pomocą notesów, które zostały opracowane przez analityków zabezpieczeń firmy Microsoft. Każdy Notes jest specjalistycznych niezależna przepływu pracy dla specyficznego przypadku użycia. Wizualizacje są zawarte w każdej notesu szybciej eksploracji danych i myślistwo zagrożeń. Dostosowywanie wbudowane notesów do własnych potrzeb, tworzenie nowych w notesach od podstaw lub importowanie notesów z platformy Azure, przez wartownika "społeczności usługi GitHub. Notesy Jupyter notebook są importowane jako projektu na stronie notesy platformy Azure — dzięki temu użytkownikowi na dostęp do wszystkich notesy platformy Azure przez wartownika w jednym miejscu. Notesy mogą być uruchamiane jednym kliknięciem przycisku lub mogą być konfigurowane przez użytkownika do dopasowania ich środowiska.

W pełni zintegrowane rozwiązanie umożliwia notesów do uruchamiania w chmurze zasobów obliczeniowych i magazynowych bez nie podstawowej obsługi. Możliwość wykorzystać istniejące ekosystemu notesów programu Jupyter umożliwia reagowania źródła modeli, bez udostępniania danych klientów. 


Każdy Notes jest hostowana na notesy platformy Azure (obecnie w wersji zapoznawczej), opracowywanie interakcyjne środowisko w chmurze platformy Azure. Notesy są zawsze dostępne, która jest zawsze dostępna z dowolnej przeglądarki, w dowolnym miejscu na świecie.  Azure, przez wartownika "wbudowane notesów dotyczącymi badania i myślistwo są klonowane do projektu, który należy do Ciebie i którą można modyfikować i dostosować do danego środowiska. Niektóre z najpopularniejszymi notesami wbudowane są:

- **Zgłoś alert, badania i myślistwo**: Tego notesu interakcyjnego umożliwia szybkie klasyfikacji różnych klas alerty pobierania powiązana aktywność oraz wzbogaca alert przy użyciu skojarzone działanie i dane, w którym alert został wygenerowany.

- **Host punktu końcowego z przewodnikiem myślistwo**: Służy do wyszukiwania kątem oznak naruszenia zabezpieczeń przez przechodzenie na zabezpieczenia odpowiednich działań skojarzona z hostem punktu końcowego.  

- **Office Zaloguj się z przewodnikiem myślistwo**: Umożliwia to wyszukiwania do podejrzanego logowania przez wizualizację lokalizacje geograficzne podejrzanych dzienników, a także wyświetlanie wzorce logowania nietypowe pochodzące z danych usługi Office 365.

## <a name="run-a-notebook"></a>Uruchamianie notesu
W poniższym przykładzie udostępniamy wbudowanych Notes, aby wyszukać szczegółowe zagłębieniem się w lokalizacji anomalie, aby zobaczyć, jeśli każda osoba w lokalizacji nieoczekiwany robi czegoś w sieci.

1. W portalu Azure przez wartownika kliknij **notesów** a następnie wybierz jedną z wbudowanych notesy.
  
   ![Wybierz notes](./media/notebooks/select-notebook.png)

3.  Kliknij przycisk **importu** do sklonowania repozytorium GitHub w projekcie notesy platformy Azure.
  ![Importuj notesu](./media/notebooks/import1.png)
4.  Każdy notesu przeprowadzi Cię przez kroki umożliwiające wykonywanie hunt lub badania. Modele, biblioteki i inne zależności i konfiguracji dla połączenia z usługą Azure przez wartownika jest importowany automatycznie włączyć wykonywanie jednego kliknięcia. Cały kod i bibliotek wymaganych w celu uruchomienia notesu są wstępnie załadowane. Możesz natychmiast rozpocząć uruchamiania notesu obszaru roboczego usługi Log Analytics bez konfiguracji.

   ![Importowanie repozytorium](./media/notebooks/import2.png)

5.  Eksploruj, modyfikowanie i uruchamianie notesów przykładowe podane. Mogą one używane jako bloków konstrukcyjnych dla wielu różnych scenariuszy.

  ![Wybierz notes](./media/notebooks/import3.png)



## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób uruchamiania badania myślistwo przy użyciu notesów w przez wartownika Azure. Aby dowiedzieć się więcej na temat platformy Azure przez wartownika, zobacz następujące artykuły:

- [Proaktywnie możliwe pod kątem zagrożeń](hunting.md)
- [Używanie zakładek w celu zapisania interesujące informacje podczas myślistwo](bookmarks.md)