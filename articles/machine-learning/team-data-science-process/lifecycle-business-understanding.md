---
title: Poznawanie firmy w procesie nauki o danych zespołu
description: Cele, zadania i elementy dostarczane dla etapu opis firm Twoje projekty do nauki o danych w procesie nauki o danych zespołu.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 35d03a52125bd2646f86b96bcffe123d9fab7f64
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60303557"
---
# <a name="the-business-understanding-stage-of-the-team-data-science-process-lifecycle"></a>Na etapie opis firm cyklu życia zespołowego danych dla celów naukowych

W tym artykule opisano cele, zadania i cele do zrealizowania skojarzone z etapem opis firm procesu do nauki o danych zespołu (TDSP). Ten proces obejmuje zalecane cyklu życia, który umożliwia tworzenie struktury projektów do nauki o danych. Cykl życia przedstawia główne etapy, które projekty zazwyczaj są wykonywane, często iteracyjne:

   1. **Poznawanie firmy**
   2. **Pozyskiwanie danych i opis**
   3. **Modelowanie**
   4. **Wdrożenie**
   5. **Akceptacja klienta**

Oto wizualnej reprezentacji cyklu przetwarzania TDSP: 

![Cykl życia przetwarzania TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Cele
* Określ klucza zmienne, które mają służyć jako obiekty docelowe modelu i którego powiązane metryki są używane ustalenia powodzenia projektu.
* Określenie odpowiednich źródeł danych, które ma dostęp do firmy, lub musi uzyskać.

## <a name="how-to-do-it"></a>Jak to zrobić
Istnieją dwa główne zadania, które zostały rozwiązane podczas tego etapu: 

   * **Zdefiniuj cele**: Praca z klientów i innych zainteresowanych stron, aby zrozumieć i zidentyfikować problemy biznesowe. Formułowanie pytania, które definiują cele biznesowe, przeznaczonych dla metody do nauki o danych.
   * **Identyfikowanie źródeł danych**: Znajdź odpowiednie dane, które pomaga w uzyskaniu odpowiedzi na pytania, które definiują cele projektu.

### <a name="define-objectives"></a>Zdefiniuj cele
1. Głównym celem tego kroku jest do identyfikowania zmienne klucza biznesowych, które niezbędne do analizy do prognozowania. Będziemy odwoływać się do tych zmiennych jako *modelu obiektów docelowych*, i używamy metryki skojarzonych z nimi do ustalenia powodzenia projektu. Dwa przykłady takich celów są prognoz sprzedaży lub prawdopodobieństwo, że kolejność jest fałszywe.

2. Zdefiniuj cele projektu, pytania i rafinacja "ostrych" zadawane pytania, które są istotne, określonej i jednoznaczna. Do nauki o danych jest procesem, który używa nazwy i numery odpowiedzi na takie pytania. Zazwyczaj używasz do nauki o danych i uczenia maszynowego, aby odpowiedzieć pięć typów pytań:
 
   * Ile lub ile? (Regresja)
   * Jakiej kategorii? (Klasyfikacja)
   * Która grupa? (klastrowania)
   * Jest to nieco dziwne? (wykrywanie anomalii)
   * Opcję, która ma być wykonywana? (zalecenie)

   Określ, który z tych pytań jest wysyłane żądanie i jak odpowiadający powoduje to osiągnięcie celów biznesowych.

3. Definiowanie zespołu projektu, określając ról i obowiązków, jego członków. Opracowywanie planu wysokiego poziomu punktu kontrolnego, który możesz powtarzanie czynności w odkrywaniu większej ilości informacji. 

4. Zdefiniuj metryki sukcesu. Na przykład można osiągnąć przewidywanie zmienności klientów. Szybkość dokładność "x" procent jest konieczne do końca tego projektu trzech miesięcy. Dzięki tym danym może zaoferować, postęp dokonany w promocji klienta, aby zmniejszyć. Metryki musi być **INTELIGENTNE**: 

   * **S**bierz określone 
   * **M**easurable
   * **A**chievable 
   * **R**elevant 
   * **T**powiązane z edytora ime 

### <a name="identify-data-sources"></a>Identyfikowanie źródeł danych
Identyfikowanie źródeł danych, które zawierają przykłady znanych odpowiedzi na swoje pytania sharp. Wyszukaj następujące dane:

* Dane, która jest odpowiednia do pytania. Czy masz miary docelowej i funkcje, które są powiązane z obiektem docelowym?
* Dane, które jest dokładne miary docelowej modelu i funkcji.

Może na przykład, okaże się, że istniejące systemy trzeba zbierać i rejestrować dodatkowych typów danych, aby rozwiązać problem i osiągać cele projektu. W takiej sytuacji można szukać zewnętrznych źródeł danych lub zaktualizować systemy zbierania nowych danych.

## <a name="artifacts"></a>Artefakty
Oto elementy dostarczane podczas tego etapu:

   * [Karty dokumentów](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Charter.md): Standardowy szablon znajduje się w definicji struktury przetwarzania TDSP projektu. Dokument karty jest życia. Należy zaktualizować szablon w całym projekcie, wprowadzić nowe operacje odnajdywania, a jako firma zmienią się wymagania. Klucz jest do iteracji na tego dokumentu, dodając więcej szczegółów, w czasie wykonywania procesu odnajdywania. Zachowaj klienta i innych uczestników projektu w wprowadzania zmian oraz wyraźnego powiadomienia powody zmiany do nich.  
   * [Źródła danych](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/Data%20Defintion.md#raw-data-sources): **Źródeł danych pierwotnych** części **definicje danych** raport, który znajduje się w projekcie środowiska TDSP **raport danych** folder zawiera źródła danych. W tej sekcji Określa lokalizacje oryginału i docelowego dla nieprzetworzonych danych. W późniejszym etapie należy wypełnić dodatkowe szczegóły, takie jak skrypty, aby przenieść dane do środowiska analityczne.  
   * [Słowniki danych](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Data_Dictionaries): Ten dokument zawiera opis danych, które są dostarczane przez klienta. Opisy te obejmują informacje schematu (typy danych i informacji na temat reguł sprawdzania poprawności, jeśli istnieje) i diagramy relacji jednostki, jeśli jest dostępny.

## <a name="next-steps"></a>Kolejne kroki

Poniżej podano linki do każdego kroku w cyklu życia przetwarzania TDSP:

   1. [Poznawanie firmy](lifecycle-business-understanding.md)
   2. [Pozyskiwanie danych i opis](lifecycle-data.md)
   3. [Modelowanie](lifecycle-modeling.md)
   4. [Wdrożenie](lifecycle-deployment.md)
   5. [Akceptacja klienta](lifecycle-acceptance.md)

Firma Microsoft oferuje instruktaży pełnej end-to-end, które przedstawiają wszystkie kroki w procesie dla konkretnych scenariuszy. [Przykładowe przewodniki](walkthroughs.md) artykuł zawiera listę scenariuszy wraz z linkami i opisy miniatur. Przewodniki pokazują, jak połączyć chmury, lokalnego narzędzia i usługi w przepływie pracy lub potoku do tworzenia inteligentnych aplikacji. 
