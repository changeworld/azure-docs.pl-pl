---
title: Przypadek użycia w fabryce danych — zalecenia dotyczące produktów
description: Dowiedz się więcej o przypadku użycia zaimplementowanym przy użyciu usługi Azure Data Factory wraz z innymi usługami.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: 6f1523c7-46c3-4b8d-9ed6-b847ae5ec4ae
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 49ad9be7c70602132436b14234f01a4086d8e1fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70139136"
---
# <a name="use-case---product-recommendations"></a>Przypadek użycia - Zalecenia dotyczące produktu
Usługa Azure Data Factory jest jedną z wielu usług używanych do implementowania pakietu Cortana Intelligence Suite akceleratorów rozwiązań.  Szczegółowe informacje o tym pakiecie można znaleźć na stronie [pakietu Cortana Intelligence Suite.](https://www.microsoft.com/cortanaanalytics) W tym dokumencie opisano typowy przypadek użycia, który użytkownicy platformy Azure już rozwiązali i zaimplementowali przy użyciu usługi Azure Data Factory i innych usług składników analizy Cortany.

## <a name="scenario"></a>Scenariusz
Sprzedawcy internetowi często chcą zachęcić swoich klientów do zakupu produktów, prezentując im produkty, które są najbardziej prawdopodobne, a zatem najprawdopodobniej kupią. Aby to osiągnąć, sprzedawcy internetowi muszą dostosować środowisko online swojego użytkownika za pomocą spersonalizowanych rekomendacji produktów dla tego konkretnego użytkownika. Te spersonalizowane rekomendacje mają być sformułowane na podstawie ich aktualnych i historycznych danych o zachowaniach zakupowych, informacji o produktach, nowo wprowadzonych marek oraz danych segmentacji produktów i klientów.  Ponadto mogą one dostarczyć zalecenia dotyczące produktów użytkownika na podstawie analizy ogólnego zachowania użycia od wszystkich użytkowników razem wziętych.

Celem tych sprzedawców jest optymalizacja pod kątem konwersji typu "kliknij do sprzedaży" i uzyskanie wyższych przychodów ze sprzedaży.  Osiągają tę konwersję, dostarczając kontekstowe, oparte na zachowaniu rekomendacje produktów oparte na zainteresowaniach i działaniach klientów. W tym przypadku używamy sprzedawców internetowych jako przykład firm, które chcą zoptymalizować dla swoich klientów. Jednak zasady te mają zastosowanie do każdej firmy, która chce zaangażować swoich klientów wokół swoich towarów i usług i zwiększyć doświadczenie zakupowe swoich klientów dzięki spersonalizowanym rekomendacjom produktów.

## <a name="challenges"></a>Wyzwania
Istnieje wiele wyzwań, przed którymi stoją sprzedawcy internetowi, próbując wdrożyć ten rodzaj przypadku użycia. 

Po pierwsze dane o różnych rozmiarach i kształtach muszą być pozyskiwane z wielu źródeł danych, zarówno lokalnie, jak i w chmurze. Dane te obejmują dane produktów, historyczne dane o zachowaniu klientów i dane użytkownika podczas przeglądania witryny sieci sprzedaży online. 

Po drugie, spersonalizowane rekomendacje produktów muszą być racjonalnie i dokładnie obliczone i przewidywane. Oprócz produktów, marki i zachowań klientów oraz danych przeglądarki, sprzedawcy internetowi muszą również uwzględnić opinie klientów na temat wcześniejszych zakupów, aby uwzględnić w określeniu najlepszych rekomendacji produktów dla użytkownika. 

Po trzecie, zalecenia muszą być natychmiast dostarczane użytkownikowi, aby zapewnić bezproblemowe przeglądanie i zakup oraz dostarczać najnowsze i odpowiednie zalecenia. 

Wreszcie, detaliści muszą zmierzyć skuteczność swojego podejścia, śledząc ogólne sukcesy sprzedaży i sprzedaży krzyżowej, a także dostosowywać się do swoich przyszłych zaleceń.

## <a name="solution-overview"></a>Omówienie rozwiązania
Ten przykładowy przypadek użycia został rozwiązany i zaimplementowany przez prawdziwych użytkowników platformy Azure przy użyciu usługi Azure Data Factory i innych usług składników Analizy Cortany, w tym [HDInsight](https://azure.microsoft.com/services/hdinsight/) i [Power BI](https://powerbi.microsoft.com/).

Sprzedawca internetowy używa magazynu obiektów Blob platformy Azure, lokalnego serwera SQL, usługi Azure SQL DB i relacyjnej tabeli danych jako opcji magazynu danych w całym przepływie pracy.  Magazyn obiektów blob zawiera informacje o klientach, dane o zachowaniu klienta i dane informacji o produkcie. Dane informacji o produkcie obejmują informacje o marce produktu i katalogu produktów przechowywanych lokalnie w magazynie danych SQL. 

Wszystkie dane są łączone i wprowadzane do systemu rekomendacji produktów w celu dostarczania spersonalizowanych rekomendacji w oparciu o zainteresowania i działania klientów, podczas gdy użytkownik przegląda produkty w katalogu na stronie internetowej. Klienci widzą również produkty, które są związane z produktem, na który patrzą, na podstawie ogólnych wzorców użycia witryny, które nie są powiązane z jednym użytkownikiem.

![diagram przypadków użycia](./media/data-factory-product-reco-usecase/diagram-1.png)

Gigabajty surowych plików dziennika internetowego są generowane codziennie ze strony internetowej sprzedawcy online jako pliki o częściowo ustrukturyzowanej strukturze. Nieprzetworzone pliki dziennika sieci web oraz informacje o kliencie i katalogu produktów są regularnie pozyskiwane do magazynu obiektów Blob platformy Azure przy użyciu globalnie wdrożonego przenoszenia danych usługi Data Factory. Pliki dziennika nieprzetworzonego dla dnia są podzielone na partycje (według roku i miesiąca) w magazynie obiektów blob dla magazynu długoterminowego.  [Usługa Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) służy do partycjonowania nieprzetworzonych plików dziennika w magazynie obiektów blob i przetwarzania pozyskiwania dzienników na dużą skalę przy użyciu skryptów Hive i Pig. Dane dzienników sieci web na partycje są następnie przetwarzane w celu wyodrębnienia potrzebnych danych wejściowych dla systemu rekomendacji uczenia maszynowego w celu wygenerowania spersonalizowanych zaleceń dotyczących produktów.

System rekomendacji używany w tym przykładzie do uczenia maszynowego jest platformą rekomendacji uczenia maszynowego typu open source firmy [Apache Mahout.](https://mahout.apache.org/)  Do [scenariusza](https://azure.microsoft.com/services/machine-learning/) można zastosować dowolne usługi Azure Machine Learning lub model niestandardowy.  Model Mahout służy do przewidywania podobieństwa między elementami w witrynie sieci Web na podstawie ogólnych wzorców użycia i do generowania spersonalizowanych rekomendacji na podstawie indywidualnego użytkownika.

Wreszcie, zestaw wyników spersonalizowanych rekomendacji produktów jest przenoszony do relacyjnej tabeli danych do spożycia przez stronę internetową sprzedawcy.  Zestaw wyników można również uzyskać dostęp bezpośrednio z magazynu obiektów blob przez inną aplikację lub przeniósł się do dodatkowych magazynów dla innych konsumentów i przypadków użycia.

## <a name="benefits"></a>Korzyści
Optymalizując strategię rekomendacji produktów i dostosowując ją do celów biznesowych, rozwiązanie spełniło cele merchandisingu i marketingu sprzedawcy internetowego. Ponadto byli w stanie operacjonalizacji i zarządzania przepływem pracy rekomendacji produktu w sposób wydajny, niezawodny i opłacalny. Podejście to ułatwiło im aktualizację modelu i dostosowanie jego skuteczności w oparciu o miary sukcesów sprzedaży w zakresie konwersji. Korzystając z usługi Azure Data Factory, mogli zrezygnować z czasochłonnego i kosztownego ręcznego zarządzania zasobami w chmurze i przejść do zarządzania zasobami w chmurze na żądanie. W związku z tym byli w stanie zaoszczędzić czas, pieniądze i skrócić swój czas do wdrożenia rozwiązania. Widoki linii danych i kondycja usługi operacyjnej stały się łatwe do wizualizacji i rozwiązywania problemów za pomocą intuicyjnego interfejsu użytkownika monitorowania i zarządzania fabryki danych dostępnego w witrynie Azure portal. Ich rozwiązanie można teraz zaplanować i zarządzać, dzięki czemu gotowe dane są niezawodnie produkowane i dostarczane do użytkowników, a zależności danych i przetwarzania są automatycznie zarządzane bez interwencji człowieka.

Zapewniając to spersonalizowane doświadczenie zakupów, sklep internetowy stworzył bardziej konkurencyjne, angażujące doświadczenie klienta, a tym samym zwiększyć sprzedaż i ogólne zadowolenie klienta.

