---
title: Biznesowe opis etapie cyklu życia procesu nauki danych zespołu - Azure | Dokumentacja firmy Microsoft
description: Cele, zadań i elementów dostarczanych na etapie opis firm projektów analizy danych
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: 6d8eedbbf4a682443e73ecb9cf9496f3cdd1cd9d
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837205"
---
# <a name="business-understanding"></a>Poznawanie firmy

W tym artykule omówiono cele, zadań i elementów dostarczanych skojarzone z etapu opis firm zespołu danych nauki procesu (TDSP). Ten proces obejmuje zalecane cykl służy do struktury projektów analizy danych. Cykl życia przedstawiono główne etapy, które projekty zazwyczaj wykonywane, często wielokrotnie powtarzane:

   1. **Opis biznesowa**
   2. **Uzyskiwanie danych i zrozumienie**
   3. **Modelowanie**
   4. **Wdrożenie**
   5. **Akceptacji klienta**

W tym miejscu jest wizualną reprezentacją życia TDSP: 

![Cykl życia TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Cele
* Określ klucza zmienne, które mają służyć jako miejsca docelowe modelu i których powiązane metryki są używane ocenić powodzenie projektu.
* Określenie odpowiednich źródeł danych do firmy musi uzyskać lub ma dostęp do.

## <a name="how-to-do-it"></a>Jak to zrobić
Istnieją dwa główne zadania zostały omówione w tym etapie: 

   * **Zdefiniuj cele**: Praca z klienta oraz innych zainteresowanych osób do zrozumienia i identyfikację problemów biznesowych. Sformułować pytania, na które zdefiniuj cele biznesowe, które można kierować techniki analizy danych.
   * **Źródła danych**: Znajdź odpowiednie dane, które pomaga odpowiedzieć na pytania, które określają cele projektu.

### <a name="define-objectives"></a>Zdefiniuj cele
1. Głównym celem tego etapu jest ustalenie zmiennych klucza biznesowe, które wymaga analizy do prognozowania. Firma Microsoft odnosi się do tych zmiennych jako *modelu obiektów docelowych*, i używamy metryki skojarzonych z nimi, aby określić powodzenie projektu. Dwa przykłady takich miejsc docelowych są prognozy sprzedaży lub prawdopodobieństwo kolejności jest fałszywe.

2. Zdefiniuj cele projektu pytania i uściślenie "ostrych" pytania, które są istotne, określonych i jednoznaczne. Nauki danych jest procesem, który używa nazwy i numery odpowiedzi na takie pytania. Aby uzyskać więcej informacji na zadawanie pytań sharp, zobacz [sposób wykonywania analizy danych](https://blogs.technet.microsoft.com/machinelearning/2016/03/28/how-to-do-data-science/) blogu. Używane zwykle nauki danych lub odpowiedzi na pytania pięć typów uczenia maszynowego:
 
   * Ile lub ile? (Regresja)
   * Kategorii? (Klasyfikacja)
   * Grupę? (klastrowania)
   * Jest to nieco dziwne? (wykrywanie anomalii)
   * Należy podjąć, która opcja? (zalecane)

   Ustal, które z tych pytań jest wysyłane żądanie i jak odpowiadający osiągnięcia celów biznesowych.

3. Zdefiniuj zespołu projektu, określając role i obowiązki związane z jego elementów członkowskich. Opracuj plan wysokiego poziomu punktu kontrolnego, który można iterację w miarę odnajdywania więcej informacji. 

4. Zdefiniuj metryki sukcesu. Na przykład można osiągnąć prognozowania przenoszenie klienta. Szybkość dokładność "procent x" należy do końca tego projektu trzech miesięcy. Przy użyciu tych danych możesz zaoferować się, że churn — promocji klienta, aby zmniejszyć. Metryki musi być **INTELIGENTNE**: 

   * **S**bierz określone 
   * **M**easurable
   * **A**chievable 
   * **R**elevant 
   * **T**powiązane z edytora ime 

### <a name="identify-data-sources"></a>Źródła danych
Zidentyfikuj źródeł danych, które zawierają przykłady znanych odpowiedzi na pytania sharp. Wyszukaj następujące dane:

* Dane na pytanie. Czy istnieją środki docelowych i funkcje, które są powiązane z obiektem docelowym?
* Dane, które są dokładne miary z modelu docelowego i funkcji do zainteresowań.

Na przykład znaleźć, że istniejące systemy należy zbierać i rejestrować dodatkowych typów danych w celu rozwiązania problemu i osiągnięcia celów projektu. W takiej sytuacji można wyszukać zewnętrznych źródeł danych lub zaktualizować systemy zbierania danych nowego.

## <a name="artifacts"></a>Artefakty
Poniżej przedstawiono elementy dostarczane na tym etapie:

   * [Dokument karty](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Charter.md): standardowy szablon znajduje się w definicji struktury TDSP projektu. Dokument karty jest życia. Należy zaktualizować szablon w projekcie wprowadzeniu nowych odnajdywania i zmiany wymagań biznesowych. Należy przejść na ten dokument, dodając więcej szczegółów, w czasie wykonywania w procesie odnajdowania. Zachowaj klienta i inni uczestnicy projektu w wprowadzania zmian i dokładniej informują powody zmiany do nich.  
   * [Źródła danych](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/Data%20Defintion.md#raw-data-sources): **źródeł danych pierwotnych** sekcji **definicje danych** raport, który znajduje się w projekcie TDSP **raport danych** folder zawiera dane źródeł. Ta sekcja określa oryginał i miejsce docelowe lokalizacje nieprzetworzone dane. W późniejszym etapie należy wypełnić dodatkowe szczegóły, takie jak skrypty w celu przenoszenia danych do środowiska analitycznych.  
   * [Słowniki danych](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Data_Dictionaries): ten dokument zawiera opis danych, które są udostępniane przez klienta. Opisy te zawierają informacje o schematu (typy danych i informacji na temat reguł sprawdzania poprawności, jeśli istnieje) i diagramy jednostki relacji, jeśli jest dostępna.

## <a name="next-steps"></a>Kolejne kroki

Oto łącza do każdego kroku w cyklu TDSP:

   1. [Opis biznesowa](lifecycle-business-understanding.md)
   2. [Uzyskiwanie danych i zrozumienie](lifecycle-data.md)
   3. [Modelowanie](lifecycle-modeling.md)
   4. [Wdrożenie](lifecycle-deployment.md)
   5. [Akceptacji klienta](lifecycle-acceptance.md)

Firma Microsoft udostępnia pełnej end-to-end wskazówki, które pokazują wszystkie kroki w procesie w określonych scenariuszach. [Wskazówki przykład](walkthroughs.md) artykuł zawiera listę scenariuszy łącza i opisy miniatur. Wskazówki dotyczące ilustrują sposób łączenia chmury, narzędzia lokalnych i usług w przepływie pracy lub potoku, aby utworzyć aplikację inteligentnego. 

Przykłady tego, jak wykonać kroki opisane w TDSPs, które używają usługi Azure Machine Learning Studio, zobacz [TDSP za pomocą usługi Azure Machine Learning](http://aka.ms/datascienceprocess).
