---
title: Przypadek użycia fabryki danych - zalecenia produktu
description: Więcej informacji na temat przypadek użycia implementowane przy użyciu fabryki danych Azure wraz z innymi usługami.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 6f1523c7-46c3-4b8d-9ed6-b847ae5ec4ae
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 3328a7c80a05a48b3c04ad5492a7eafe62059681
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34622469"
---
# <a name="use-case---product-recommendations"></a>Przypadek użycia — rekomendacje produktów
Fabryka danych Azure to jedna z wielu usług, używaną do zaimplementowania pakietu Cortana Intelligence Suite akceleratorów rozwiązania.  Zobacz [pakietu Cortana Intelligence Suite](http://www.microsoft.com/cortanaanalytics) strony w celu uzyskania szczegółów dotyczących tego zestawu. W tym dokumencie opisano typowe przypadek użycia, które Azure użytkownicy mają już rozwiązany i implementowane przy użyciu fabryki danych Azure i innych usług składowych Cortana Intelligence.

## <a name="scenario"></a>Scenariusz
Sklepach internetowych często mają być zachęcić ich klientów do kupowania produktów, z uwzględnieniem ich z produktami, które są najczęściej zainteresować i w związku z tym najprawdopodobniej kupić. W tym celu sklepach internetowych należy dostosować ich użytkownika w trybie online, używając zalecenia spersonalizowane produktu dla tego określonego użytkownika. Te zalecenia spersonalizowane są dokonywane na podstawie na ich bieżące i historyczne zakupy zachowania danych, informacje o produkcie, marek nowo wprowadzonych i produktu i segmentacji danych klientów.  Ponadto zapewniają one oparte na analizie ogólną zachowanie użycia wszystkim użytkownikom ich łączyć zalecenia produktu użytkownika.

Celem tych sprzedawców jest Optymalizuj dla użytkownika kliknij do sprzedaży konwersje i, tym wyższy przychód ze sprzedaży.  Ta konwersja one osiągnąć dostarcza kontekstowe, na podstawie zachowania produktu zalecenia na podstawie zainteresowań klienta i akcji. Dla tego przypadku użycia używamy sklepach internetowych na przykład firm, które mają być Optymalizuj dla klientów. Jednak te zasady mają zastosowanie do dowolnego rodzaju działalności, który chce zaangażowania klientów wokół jego towarów i usług i udoskonalenie ich klientom kupowania wraz z zaleceniami spersonalizowane produktu.

## <a name="challenges"></a>Wyzwania
Istnieje wiele wyzwania napotykane przez sklepach internetowych podczas próby wykonania tego typu przypadek użycia. 

Po pierwsze, dane o różnych rozmiarach i kształty musi pozyskanych z wielu źródeł danych, zarówno lokalnie i w chmurze. Te dane obejmują dane produktów, dane zachowanie klienta historycznych i dane użytkowników, jako użytkownik będzie przeglądać witryny detalicznej online. 

Drugi, spersonalizowane produktu zalecenia należy rozsądnie i dokładnie obliczyć i przewidzieć. Oprócz produktu, marki i dane zachowanie i przeglądarka klienta sklepach internetowych trzeba także uwzględnić opinie klientów dotyczące zakupów ostatnich składników oznaczanie zaleceniach produktu dla użytkownika. 

Trzecie musi być natychmiast dostarczanego użytkownikowi, aby zapewnić bezproblemowe przeglądania i zakupu środowisko i podano zalecenia najnowszego i odpowiednie zalecenia. 

Na koniec przeszukiwarki ich podejście śledząc ogólnej sprzedaży w górę sprzedaży kliknij do konwersji sprzedaży sukcesów i Dostosuj, aby ich przyszłych zalecenia konieczne sprzedawców.

## <a name="solution-overview"></a>Przegląd rozwiązania
Ten przypadek użycia przykład został rozwiązany i zaimplementowana przez użytkowników rzeczywistym Azure przy użyciu fabryki danych Azure i innych usług składowych Cortana Intelligence, łącznie [HDInsight](https://azure.microsoft.com/services/hdinsight/) i [usługi Power BI](https://powerbi.microsoft.com/).

Detalicznej online używa magazynu obiektów Blob platformy Azure, programu SQL server lokalnej bazy danych SQL Azure i składnicy danych relacyjnych jako ich opcji przechowywania danych w całym przepływie pracy.  Magazyn obiektów blob zawiera informacje o kliencie, dane zachowanie klienta i danych informacji o produkcie. Danych informacji produktu zawiera informacje markę produktu i produktu katalogu przechowywanych lokalnie w usłudze SQL data warehouse. 

Wszystkie dane są łączone i przekazywane do system zalecenie produktów do dostarczania spersonalizowanych zalecenia na podstawie zainteresowań klienta i akcji, gdy użytkownik będzie przeglądać produktów w wykazie w witrynie sieci Web. Klientów, zobacz też produktów, które są związane z produktu, które są patrzeć oparte na wzorce użycia ogólną witryny sieci Web, które nie są powiązane z żadnym użytkownikiem jednego.

![diagram przypadków użycia](./media/data-factory-product-reco-usecase/diagram-1.png)

Gigabajtów web nieprzetworzone pliki dziennika są generowane z witryny sieci Web detalicznej online codziennie jako częściowo ustrukturyzowanych plików. Plików dziennika raw sieci web i informacje o katalogu klienta i produkt jest regularnie pozyskanych w magazynie obiektów Blob platformy Azure za pomocą przenoszenia danych globalnie wdrożone fabryki danych jako usługa. Pliki dziennika raw dzień są dzielone (przez rok i miesiąc) w magazynie obiektów blob do przechowywania długoterminowego.  [Usługa Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) jest używany do partycjonowania pierwotnych dziennika plików w magazynie obiektów blob i przetworzyć pozyskiwane dzienniki na dużą skalę skryptów zarówno Hive i Pig. Partycjonowane sieci web rejestruje dane przetwarzany wyodrębnić potrzebne dane wejściowe dla systemu zalecenie celu generowania zaleceń spersonalizowane produktu uczenia maszynowego.

System zalecenie wykorzystywany do uczenia, w tym przykładzie jest uczenia platformy zalecenia na podstawie maszynowego typu open source [Apache Mahout](http://mahout.apache.org/).  Wszelkie [usługi Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) lub niestandardowych modelu mogą być stosowane do tego scenariusza.  Mahout model służy do prognozowania podobieństwa między elementami w witrynie sieci Web na podstawie ogólnej wzorców użycia i generowania spersonalizowane rekomendacje oparte na poszczególnych użytkowników.

Na koniec zestawu wyników zaleceń spersonalizowane produktu jest przenoszona do składnicy danych relacyjnych wykorzystania przez detalicznej witryny sieci Web.  Zestaw wyników również mogą zostać dostępne bezpośrednio z magazynu obiektów blob przez inną aplikację lub przenieść do dodatkowych magazynów i innych przypadków użycia.

## <a name="benefits"></a>Korzyści
Optymalizacja ich strategii zalecenie produktu i dostosowanie jej cele biznesowe, rozwiązanie spełnić promocji detalicznej online i marketingu celów. Ponadto byli w stanie operacjonalizacji i zarządzanie nimi przepływu pracy zalecenie produktów w sposób wydajny, niezawodny i ekonomiczne. Podejście ułatwić ich aktualizowanie ich modelu i dostosować jego skuteczność na podstawie miar sprzedaży sukcesów kliknij do konwersji. Przy użyciu fabryki danych Azure, byli w stanie abandon ich zarządzanie zasobami chmury ręczne czasochłonne i kosztowne i przejdź do zarządzania zasobami chmury na żądanie. W związku z tym byli w stanie zaoszczędzić czas i pieniądze i skrócić czas ich do wdrożenia rozwiązania. Widoki elementy powiązane danych i kondycji usługi operational stał się ułatwia tworzenie wizualizacji i rozwiązywanie problemów za pomocą intuicyjnego monitorowanie fabryki danych i zarządzanie interfejsu użytkownika dostępne w portalu Azure. Teraz można zaplanowane i zarządzane, tak aby Zakończono danych niezawodnie jest utworzony i dostarczane do użytkowników oraz danych i przetwarzania zależności są automatycznie zarządzane bez udziału człowieka ich rozwiązania.

Zapewniając to spersonalizowane środowisko zakupów online sprzedaży detalicznej utworzone klienta konkurencyjności, atrakcyjne doświadczenia i bardziej sprzedaży i ogólnej zadowoleni, w związku z tym.

