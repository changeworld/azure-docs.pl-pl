---
title: Przypadek użycia fabryki danych — rekomendacje produktów
description: Więcej informacji na temat przypadek użycia implementowane przy użyciu usługi Azure Data Factory razem z innymi usługami.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 6f1523c7-46c3-4b8d-9ed6-b847ae5ec4ae
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 4a3d1c513bcfb6449ca73d873c0dd9831c6fe01d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60605704"
---
# <a name="use-case---product-recommendations"></a>Przypadek użycia — rekomendacje produktów
Usługa Azure Data Factory jest jednym z wielu usług, używaną do zaimplementowania pakietu Cortana Intelligence z akceleratorami rozwiązań.  Zobacz [pakietu Cortana Intelligence](https://www.microsoft.com/cortanaanalytics) strony, aby uzyskać szczegółowe informacje na temat tego pakietu. W tym dokumencie opisano typowy przypadek użycia, w których użytkownicy platformy Azure już rozwiązanie zaimplementowane przy użyciu usługi Azure Data Factory i innych usług składników pakietu Cortana Intelligence.

## <a name="scenario"></a>Scenariusz
Sprzedawcy internetowi często mają być zachęcić ich klientom nabywać produkty, prezentując z produktami, które są najbardziej prawdopodobne zainteresować i w związku z tym najbardziej prawdopodobne kupić. Aby to osiągnąć, sprzedawcy internetowi muszą dostosować swoje użytkownika w trybie online przy użyciu spersonalizowanych rekomendacji produktu dla tego określonego użytkownika. Te spersonalizowane zalecenia są dokonywane na podstawie na ich bieżące i historyczne dane dotyczące zachowania, informacji o produkcie, nowo wprowadzonych marek i produktu i segmentacji danych klientów na zakupy.  Ponadto może zapewnić rekomendacje produktów użytkownika na podstawie analizy ogólnym zachowaniom użycia od wszystkich użytkowników w połączeniu.

Celem tych sprzedawców detalicznych jest Optymalizuj dla użytkownika kliknij przycisk do sprzedaży konwersji i Uzyskaj wyższe przychody ze sprzedaży.  Osiągną tę konwersję, zapewniając rekomendacje produktów kontekstowych, oparte na zachowanie, na podstawie zainteresowań klientów i akcji. Dla tego przypadku użycia używamy sprzedawcy internetowi jako przykład firm, które ma być optymalizowany dla swoich klientów. Jednak te zasady mają zastosowanie w przypadku każdej firmy, które chcą angażować swoich klientów na całym jego towarów i usług i poprawić funkcjonalność z perspektywy zakupu swoich klientów za pomocą spersonalizowanych rekomendacji produktu.

## <a name="challenges"></a>Wyzwania
Istnieje wiele wyzwań napotykane przez sprzedawcy internetowi podczas próby wykonania tego rodzaju przypadek użycia. 

Po pierwsze, dane z różnych rozmiarów i kształtów muszą być pozyskiwane z wielu źródeł danych, zarówno lokalnie i w chmurze. Te dane obejmują dane produktów, historycznych zachowanie danych klientów i dane użytkowników, jak użytkownik będzie przeglądać witryny detalicznego handlu online. 

Rekomendacje produktów w drugim, spersonalizowane musi być rozsądnie oraz dokładne wykonanie obliczane przewidzieć. Oprócz produktów, markę i dane zachowanie a przeglądarką klienta sprzedawcy internetowi również musi zawierać opinie klientów na zakupy w ciągu ostatnich wziąć pod uwagę przy określaniu najlepsze rekomendacje produktów dla użytkownika. 

Trzeci musi być natychmiast wynajmowania użytkownikowi, aby zapewnić bezproblemowe przeglądanie i kupowanie środowisko i przedstawić zalecenia w zakresie najnowszego i odpowiednie zalecenia. 

Na koniec sprzedawcy detaliczni, muszą Zmierz efektywność swojego podejścia, śledząc ogólną dodatkowej i wiązanej sukcesów sprzedaży kliknij do konwersji i dostosowują się do ich zaleceń na przyszłość.

## <a name="solution-overview"></a>Omówienie rozwiązania
Ten przypadek użycia przykład został rozwiązany i implementowany przez użytkowników rzeczywiste platformy Azure przy użyciu usługi Azure Data Factory i innymi usługami składników pakietu Cortana Intelligence, w tym [HDInsight](https://azure.microsoft.com/services/hdinsight/) i [usługi Power BI](https://powerbi.microsoft.com/).

Sprzedawca online korzysta z magazynu obiektów Blob platformy Azure, programu SQL server w środowisku lokalnym, bazy danych SQL Azure i składnicy danych relacyjnych jako opcje przechowywania ich danych w całym przepływie pracy.  Magazyn obiektów blob zawiera informacje o klientach, dane zachowanie klienta i danych informacji o produkcie. Danych informacji produktu obejmuje informacje o produkcie marki i produktu przechowywane lokalnie w usłudze SQL data warehouse w katalogu. 

Wszystkie dane są połączone i przekazywane do system rekomendacji produktów do dostarczania spersonalizowanych zaleceń na podstawie interesów klientów i akcji, gdy użytkownik przegląda produktów w wykazie w witrynie internetowej. Klientów, zobacz też produktów, które są powiązane z produktu, który jest wyświetlany na podstawie ogólnej witryny sieci Web wzorców użycia, które nie są powiązane z żadnym użytkownikiem jeden.

![Diagram przypadków użycia](./media/data-factory-product-reco-usecase/diagram-1.png)

Gigabajty web pierwotne pliki dziennika są generowane raz dziennie z witryny sieci Web w trybie online dla sprzedawców detalicznych jako pliki z częściową strukturą. Pliki dziennika raw sieci web i informacje katalogu klienta i produkt regularnie pobieranym do usługi Azure Blob storage za pomocą usługi Data Factory, przenoszenia globalnie wdrożonych danych jako usługa. Pierwotnych plików dziennika na ten dzień są podzielone na partycje (przez rok i miesiąc) w usłudze blob storage do przechowywania długoterminowego.  [Usługa Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) służy do partycjonowania pierwotnych plików dziennika w magazynie obiektów blob i przetwarzać odebrane dzienniki na dużą skalę za pomocą skryptów Hive i Pig. Partycjonowane sieci web rejestruje dane są następnie przetwarzane w celu wyodrębnienia wymagane dane wejściowe dla usługi machine learning zalecenie systemu do generowania spersonalizowanych rekomendacji produktu.

System zalecenie, używane do uczenia maszynowego w tym przykładzie jest uczenia platforma zalecenie maszynowego typu open source [Apache Mahout](https://mahout.apache.org/).  Wszelkie [usługi Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) lub utworzyć niestandardowy model może odnosić się do scenariusza.  Mahout model służy do prognozowania podobieństwa między elementami w witrynie sieci Web, na podstawie ogólnej wzorców użycia i generowanie spersonalizowanych zaleceń na podstawie poszczególnych użytkownika.

Na koniec zestawu wyników spersonalizowanych rekomendacji produktu jest przenoszony do składnicy danych relacyjnych do użycia w witrynie sprzedaży detalicznej.  Zestaw wyników również może być dostępne bezpośrednio z usługi blob storage przez inną aplikację lub przeniesiona do dodatkowego magazynów dla innych użytkowników i przypadkami użycia.

## <a name="benefits"></a>Korzyści
Optymalizacja ich strategii rekomendacji produktów i dostosowanie jej cele biznesowe, rozwiązanie spełnione promocji online sprzedaży detalicznej i celów marketingowych. Ponadto mieli operacjonalizacji i zarządzanie przepływem pracy z rekomendacji produktów w sposób wydajny, niezawodny i ekonomiczne. To podejście łatwe ich aktualizowanie ich modelu i dostosowywanie jej efektywność na podstawie miar sales sukcesów kliknij do konwersji. Za pomocą usługi Azure Data Factory, byli w stanie Porzuć ich funkcji zarządzania zasobami czasochłonne i kosztowne ręczne chmury, a następnie przejść do zarządzania zasobami chmury na żądanie. W związku z tym mieli zaoszczędzić czas i pieniądze i skrócić czas ich wdrożenia rozwiązania. Widoki pochodzenie danych i kondycji operacyjnej usług stało się ułatwia wizualizowanie i rozwiązywanie problemów przy użyciu intuicyjnego monitorowania usługi fabryka danych i zarządzania interfejsu użytkownika dostępnych w witrynie Azure portal. Można teraz zaplanowane swoje rozwiązanie i zarządzane tak, aby gotowych danych jest niezawodny sposób będą generowane i dostarczane do użytkowników oraz danych i przetwarzania zależności są zarządzane automatycznie bez interwencji człowieka.

Dostarczając to spersonalizowanego środowiska zakupowego, online sprzedaży detalicznej, utworzony klient zwiększają konkurencyjność, atrakcyjnych środowisko i w związku z tym zwiększyć zadowolenie klientów sprzedaży i ogólnej.

