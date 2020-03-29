---
title: Zrozumienie biznesowe w procesie nauki o danych zespołowych
description: Cele, zadania i rezultaty na etapie zrozumienia biznesowego projektów nauki o danych w procesie nauki o danych zespołu.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: a7aaed519f8f97a9be77a263568aeed5257c16d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76710333"
---
# <a name="the-business-understanding-stage-of-the-team-data-science-process-lifecycle"></a>Etap zrozumienia biznesowego cyklu życia procesu nauki o danych zespołu

W tym artykule opisano cele, zadania i rezultaty skojarzone z etapem zrozumienia biznesowego procesu nauki o danych zespołu (TDSP). Ten proces zapewnia zalecany cykl życia, którego można użyć do struktury projektów nauki o danych. Cykl życia przedstawia główne etapy, które projekty zazwyczaj wykonują, często iteracyjne:

   1. **Poznawanie firmy**
   2. **Pozyskiwanie danych i ich analiza**
   3. **Modelowanie**
   4. **wdrażania**
   5. **Akceptacja klienta**

Oto wizualna reprezentacja cyklu życia TDSP: 

![Cykl życia TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Cele
* Określ kluczowe zmienne, które mają służyć jako cele modelu i których powiązane metryki są używane określać powodzenie projektu.
* Zidentyfikuj odpowiednie źródła danych, do których firma ma dostęp lub które muszą uzyskać.

## <a name="how-to-do-it"></a>Jak to zrobić
Na tym etapie zajęto się dwoma głównymi zadaniami: 

   * **Zdefiniuj cele:** Współpracuj z klientem i innymi zainteresowanymi stronami, aby zrozumieć i zidentyfikować problemy biznesowe. Formułuj pytania, które definiują cele biznesowe, na które mogą kierować techniki nauki o danych.
   * **Identyfikowanie źródeł danych:** Znajdź odpowiednie dane, które pomogą Ci odpowiedzieć na pytania definiujące cele projektu.

### <a name="define-objectives"></a>Definiowanie celów
1. Głównym celem tego kroku jest określenie kluczowych zmiennych biznesowych, które analiza musi przewidzieć. Odnosimy się do tych zmiennych jako *cele modelu*i używamy metryk skojarzonych z nimi do określenia sukcesu projektu. Dwa przykłady takich celów to prognozy sprzedaży lub prawdopodobieństwo oszustwa zamówienia.

2. Zdefiniuj cele projektu, zadając i udoskonalając "ostre" pytania, które są istotne, specyficzne i jednoznaczne. Data science to proces, który używa nazw i numerów, aby odpowiedzieć na takie pytania. Zazwyczaj używasz nauki o danych lub uczenia maszynowego, aby odpowiedzieć na pięć typów pytań:
 
   * Ile lub ile? (regresja)
   * Która kategoria? (klasyfikacja)
   * Która grupa? (klastrowanie)
   * Czy to dziwne? (wykrywanie anomalii)
   * Którą opcję należy podjąć? (zalecenie)

   Określ, które z tych pytań zadajesz i jak odpowiedzi na nie osiągną Twoje cele biznesowe.

3. Zdefiniuj zespół projektu, określając role i obowiązki jego członków. Opracuj plan kamieni milowych wysokiego poziomu, który można iterować na jak odkryć więcej informacji. 

4. Zdefiniuj metryki sukcesu. Na przykład można osiągnąć przewidywanie zmian klientów. Do końca tego trzymiesięcznego projektu potrzebujesz wskaźnika dokładności "x" procent. Dzięki tym danym możesz oferować promocje dla klientów w celu zmniejszenia zmian. Metryki muszą być **SMART:** 

   * **S**pecific 
   * **M**easurable
   * **Chievable** 
   * **R**elevant ( elevant ) 
   * **T**ime-bound 

### <a name="identify-data-sources"></a>Identyfikowanie źródeł danych
Identyfikowanie źródeł danych zawierających znane przykłady odpowiedzi na ostre pytania. Poszukaj następujących danych:

* Dane, które są istotne dla pytania. Czy masz środki docelowe i funkcje, które są związane z celem?
* Dane, które są dokładną miarą docelowego modelu i interesujących cech.

Na przykład może się okazać, że istniejące systemy muszą zbierać i rejestrować dodatkowe rodzaje danych, aby rozwiązać problem i osiągnąć cele projektu. W tej sytuacji można wyszukać zewnętrzne źródła danych lub zaktualizować systemy do zbierania nowych danych.

## <a name="artifacts"></a>Artefakty
Oto rezultaty na tym etapie:

   * [Dokument czarteru](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Charter.md): Standardowy szablon znajduje się w definicji struktury projektu TDSP. Dokument czarterowy jest żywym dokumentem. Szablon można zaktualizować w całym projekcie, gdy dokonujesz nowych odkryć i zmieniasz wymagania biznesowe. Kluczem jest iterować w tym dokumencie, dodając więcej szczegółów, w miarę postępów w procesie odnajdywania. Utrzymuj klienta i inne zainteresowane strony zaangażowane w wprowadzanie zmian i jasno informują o przyczynach ich zmian.  
   * [Źródła danych:](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/Data%20Defintion.md#raw-data-sources)Sekcja **Nieprzetworzone źródła danych** raportu **Definicje danych** znaleziona w **folderze raportu danych** projektu TDSP zawiera źródła danych. W tej sekcji określono lokalizacje oryginalne i docelowe dla nieprzetworzonych danych. W późniejszych etapach należy wypełnić dodatkowe szczegóły, takie jak skrypty, aby przenieść dane do środowiska analitycznego.  
   * [Słowniki danych:](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Data_Dictionaries)Ten dokument zawiera opisy danych dostarczanych przez klienta. Opisy te zawierają informacje o schemacie (typy danych i informacje na temat reguł sprawdzania poprawności, jeśli istnieją) i diagramy relacji jednostki, jeśli są dostępne.

## <a name="next-steps"></a>Następne kroki

Oto łącza do każdego kroku w cyklu życia TDSP:

   1. [Poznawanie firmy](lifecycle-business-understanding.md)
   2. [Pozyskiwanie danych i ich analiza](lifecycle-data.md)
   3. [Modelowanie](lifecycle-modeling.md)
   4. [wdrażania](lifecycle-deployment.md)
   5. [Akceptacja klienta](lifecycle-acceptance.md)

Zapewniamy pełne wskazówki, które pokazują wszystkie kroki w procesie dla określonych scenariuszy. [Przykładowy](walkthroughs.md) artykuł zawiera listę scenariuszy z łączami i opisami miniatur. W instruktażu pokazano, jak połączyć chmurę, narzędzia lokalne i usługi w przepływie pracy lub potoku w celu utworzenia inteligentnej aplikacji. 
