---
title: SMOTE
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak używać modułu SMOTE w usłudze Azure Machine Learning, aby zwiększyć liczbę przykładów o niskim poziomie w zestawie danych przy użyciu funkcji próbkowania.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: 072268af52ebf7d3bede564d8c949eec9fc9f625
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516190"
---
# <a name="smote"></a>SMOTE

W tym artykule opisano, jak używać modułu **SMOTE** w programie Azure Machine Learning Designer (wersja zapoznawcza) w celu zwiększenia liczby nieużywanych przypadków w zestawie danych służącym do uczenia maszynowego. SMOTE to lepszy sposób zwiększenia liczby rzadkich przypadków niż zwykłe duplikowanie istniejących przypadków.  

 Moduł SMOTE można połączyć z *niezrównoważonym*zestawem danych. Istnieje wiele powodów, dla których nie można zrównoważyć zestawu danych: Kategoria, do której należysz, może być rzadki w populacji lub dane mogą być trudne do zebrania. Zazwyczaj należy używać SMOTE, gdy *Klasa* , która ma zostać poddana analizie, jest poddawana w postaci reprezentowanej. 
  
 Moduł zwraca zestaw danych, który zawiera pierwotne próbki, oraz dodatkową liczbę próbek mniejszości syntetycznych, w zależności od określonej wartości procentowej.  
  
### <a name="more-about-smote"></a>Więcej informacji na temat SMOTE

**SMOTE** oznacza *technikę przepróbkowania syntetycznej mniejszości*. Jest to technika statystyczna służąca do zwiększania liczby przypadków w zestawie danych w zrównoważony sposób.  Moduł działa, generując nowe wystąpienia z istniejących przypadków mniejszości, które podano jako dane wejściowe. Ta implementacja SMOTE **nie zmienia liczby** większości przypadków.

Nowe wystąpienia nie są jedynie kopiami istniejących przypadków mniejszościowych; Zamiast tego algorytm pobiera próbki *przestrzeni funkcji* dla każdej klasy docelowej i jej najbliższej sąsiadów i generuje nowe przykłady, które łączą funkcje przypadku docelowego z funkcjami jego sąsiadów. Takie podejście zwiększa funkcje dostępne dla każdej klasy i sprawia, że przykłady są bardziej ogólne.
  
SMOTE pobiera cały zestaw danych jako dane wejściowe, ale zwiększa wartość procentową tylko przypadków mniejszościowych. Załóżmy na przykład, że masz niezrównoważony zestaw danych, gdzie tylko 1% przypadków ma wartość docelową A (klasy mniejszości), a 99% przypadków ma wartość B. Aby zwiększyć procent przypadków mniejszości do dwóch poprzednich wartości procentowych, należy wprowadzić 200 dla **SMOTE procent** we właściwościach modułu.  
  
## <a name="examples"></a>Przykłady  

Zalecamy użycie **SMOTE** z małym zestawem danych, aby zobaczyć, jak to działa. W poniższym przykładzie zastosowano zestaw danych darowizny dla krwi dostępny w programie Azure Machine Learning Designer.
  
Jeśli dodasz zestaw danych do potoku, a następnie klikniesz polecenie **Wizualizuj** w danych wyjściowych zestawu danych, zobaczysz, że w zestawie danych 570 znajdują się wartości z 748 wierszy lub przypadków (76%) w przypadku klasy 0 i 178 (24%) klasy 1. Chociaż nie jest to poważny, Klasa 1 reprezentuje osoby, które odstawią krew, a tym samym te wiersze zawierają *przestrzeń funkcji* , którą chcesz modelować.
 
Aby zwiększyć liczbę przypadków, można ustawić wartość **procentu SMOTE**przy użyciu wielokrotności 100 w następujący sposób:

||Klasa 0|Klasa 1|ogólnego|  
|-|-------------|-------------|-----------|  
|Oryginalny zestaw danych<br /><br /> (odpowiednik **wartości procentowej SMOTE** = **0**)|570<br /><br /> 76%|178<br /><br /> codzienne|748|  
|**SMOTE procent** = **100**|570<br /><br /> 62%|356<br /><br /> 38%|926|  
|**SMOTE procent** = **200**|570<br /><br /> 52%|534<br /><br /> 48%|1104|  
|**SMOTE procent** = **300**|570<br /><br /> 44%|712<br /><br /> 56%|1282|  
  
> [!WARNING]
>  Zwiększenie liczby przypadków przy użyciu SMOTE nie gwarantuje tworzenia dokładniejszych modeli. Należy próbować potoki z różnymi wartościami procentowymi, różnymi zestawami funkcji i różnymi liczbami najbliższych sąsiadów, aby zobaczyć, jak dodawanie przypadków wpływa na model.  
  
## <a name="how-to-configure-smote"></a>Jak skonfigurować SMOTE
  
1.  Dodaj moduł SMOTE do potoku. Moduł można znaleźć w obszarze moduły przekształcania danych w kategorii manipulowanie.

2. Połącz zestaw danych, który chcesz zwiększyć. Jeśli chcesz określić miejsce na potrzeby tworzenia nowych przypadków, używając tylko określonych kolumn lub wyłączając niektóre, użyj modułu [SELECT Columns in DataSet (Wybieranie kolumn w zestawie danych](select-columns-in-dataset.md) ), aby odizolować kolumny, których chcesz użyć przed użyciem **SMOTE**.
  
    W przeciwnym razie tworzenie nowych przypadków przy użyciu programu **SMOTE** jest zależne od **wszystkich** kolumn dostarczanych jako dane wejściowe. Co najmniej jedna kolumna kolumn funkcji jest wartością numeryczną.
  
3.  Upewnij się, że wybrano kolumnę zawierającą etykietę lub klasę docelową.  **SMOTE** akceptuje tylko etykiety binarne.
  
4.  Moduł **SMOTE** automatycznie identyfikuje klasę mniejszości w kolumnie etykieta, a następnie pobiera wszystkie przykłady dla klasy mniejszości. Wszystkie kolumny nie mogą mieć NaN.
  
5.  W opcji **procent SMOTE** wpisz liczbę całkowitą, która wskazuje docelowy procent przypadków mniejszości w wyjściowym zestawie danych. Na przykład:  
  
    - Wpisz **0** (%). Moduł SMOTE zwraca dokładnie ten sam zestaw danych, który został dostarczony jako dane wejściowe, dodając nie nowe przypadki mniejszościowe. W tym zestawie danych proporcja klasy nie uległa zmianie.  
  
    -   Wpisz **100** (%). Moduł SMOTE generuje nowe przypadki mniejszościowe, dodając taką samą liczbę przypadków mniejszości, które znajdowały się w oryginalnym zestawie danych. Ponieważ SMOTE nie zwiększa liczby większości przypadków, część przypadków każdej klasy została zmieniona.  
  
    -   Wpisz **200** (%). Moduł podwaja procent przypadków mniejszościowych w porównaniu z oryginalnym zestawem danych. Nie **powoduje to,** że występuje dwa razy więcej przypadków mniejszości.  Zamiast tego rozmiar zestawu danych jest zwiększany w taki sposób, że liczba większości przypadków pozostaje taka sama, a liczba przypadków mniejszościowych jest zwiększana do momentu dopasowania do żądanej wartości procentowej.  
  
    > [!NOTE]
    > Użyj tylko wielokrotności 100 dla procentu SMOTE.

6.  Użyj opcji **Liczba najbliższych sąsiadów** , aby określić rozmiar przestrzeni funkcji używanej przez algorytm SMOTE podczas tworzenia nowych przypadków. *Najbliższy sąsiad* to wiersz danych (przypadek), który jest podobny do pewnego przypadku docelowego. Odległość między dwoma przypadkami jest mierzona przez połączenie wag wektorów wszystkich funkcji.  
  
     + Zwiększając liczbę najbliższych sąsiadów, otrzymujesz funkcje z większej liczby przypadków.
     + Utrzymując liczbę najbliższych sąsiednich sąsiadów, należy używać funkcji, które są bardziej podobne do tych w pierwotnym przykładzie.  
  
7. Wpisz wartość w polu tekstowym **losowe inicjatory** , jeśli chcesz zapewnić te same wyniki w przypadku przebiegów tego samego potoku, przy użyciu tych samych danych. W przeciwnym razie moduł generuje losowy inicjator na podstawie wartości zegara procesora podczas wdrażania potoku, co może spowodować nieco inne wyniki w przypadku uruchomienia.

8. Uruchamianie potoku.  
  
     Dane wyjściowe modułu są zestawem danych zawierającym oryginalne wiersze i pewną liczbą dodanych wierszy z przypadkami mniejszości.  

## <a name="technical-notes"></a>Uwagi techniczne

+ Podczas publikowania modelu korzystającego z modułu **SMOTE** Usuń **SMOTE** z potoku predykcyjnego przed jego opublikowaniem jako usługą sieci Web.  Przyczyną jest to, że SMOTE jest przeznaczona do ulepszania modelu podczas szkolenia i nie jest przeznaczony do oceniania.  Jeśli opublikowany potok predykcyjny zawiera moduł SMOTE, może wystąpić błąd.

+ Często można uzyskać lepsze wyniki w przypadku zastosowania czyszczenia wartości brakujące lub innych transformacji w celu naprawy danych przed zastosowaniem SMOTE. 

+ Niektórzy pracownicy naukowo zbadali, czy SMOTE jest skuteczna dla danych o dużej wymiarze czy rozrzedzonych, takich jak te używane w klasyfikacji tekstu lub w zestawach DataSet. Ten dokument zawiera dobre Podsumowanie skutków i teoretyczną prawidłowość stosowania SMOTE w takich przypadkach: [Blagus i Lusa: SMOTE dla danych z niezrównoważoną klasą o wysokim wymiarze](https://bmcbioinformatics.biomedcentral.com/articles/10.1186/1471-2105-14-106)

    Jeśli SMOTE nie jest skuteczna w zestawie danych, inne podejścia do zapoznania się z innymi metodami przepróbkowania przypadków mniejszościowych lub podpróbkowania większości przypadków, a także technik, które ułatwiają naukę bezpośrednio, przy użyciu klastrowania, zwiększenie lub adaptacja.


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning usługi. 

