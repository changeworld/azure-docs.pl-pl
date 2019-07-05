---
title: Rozwiązywanie problemów z błędami modułu
titleSuffix: Azure Machine Learning service
description: Rozwiązywanie problemów z wyjątkami modułu w usłudze Azure Machine Learning Studio, za pomocą kodów błędów
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 5ce3d86d37940f66065debce30b348371b7aa6af
ms.sourcegitcommit: 3107874d7559ea975e4d55ae33cdf45f4b5485e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/04/2019
ms.locfileid: "67568249"
---
# <a name="exceptions-and-error-codes-for-algorithm--module-reference"></a>Wyjątki i kody błędów dla algorytmów i odwołania do modułu

Informacje o komunikatach o błędach i kody wyjątków, które mogą wystąpić przy użyciu modułów w usłudze Azure Machine Learning Studio. 

Aby rozwiązać ten problem, Znajdź błąd w tym artykule na temat typowych przyczyn. Istnieją dwa sposoby, aby uzyskać pełny tekst komunikatu o błędzie w Studio:  
 
- Kliknij link, **danych wyjściowych w dzienniku**, w okienku po prawej stronie i przewiń w dół. Ostatnie dwa wiersze w oknie zostanie wyświetlony komunikat szczegółowy komunikat o błędzie.  
  
- Wybierz moduł, w którym występuje błąd, a następnie kliknij czerwony znak X. Tylko tekst odpowiednie błąd jest wyświetlany.  
  
Jeśli tekst komunikatu o błędzie nie jest przydatne, Wyślij do nas informacje o kontekście i żądany dodatki lub zmiany. Możesz przesłać opinię na temat błędu, lub odwiedź [forum usługi Azure Machine Learning STUDIO](https://aka.ms/aml-forum-studio) i zadać pytanie.  


## <a name="error-0001"></a>Błąd 0001  
 Wyjątek występuje, gdy nie można odnaleźć co najmniej jedną kolumnę określonego zestawu danych.  
  
 Zostanie wyświetlony ten błąd, wybór kolumn jest wykonywane dla modułu, ale nie istnieją wybranych kolumn w zestawie danych wejściowych. Ten błąd może wystąpić, jeśli ręcznie wpisano nazwę kolumny lub selektora kolumn udostępnił sugerowane kolumny, która nie istnieje w zestawie danych po uruchomieniu eksperymentu.  
  
**Rozwiązanie:** Ponownie moduł zostanie zgłoszony wyjątek i sprawdź poprawność nazwy kolumn są poprawne i czy istnieją wszystkie kolumny odwołania.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Nie znaleziono co najmniej jeden z określonych kolumn|  
|Kolumna o nazwie lub indeksu "{0}" nie znaleziono|  
|Kolumna o nazwie lub indeksu "{0}"nie istnieje w"{1}"|  
 

## <a name="error-0002"></a>Błąd 0002  
 Wystąpi wyjątek, jeśli jeden lub więcej parametrów nie można być przeanalizowane lub przekonwertowane z określonego typu w wymagane przez typ metody docelowej.  
  
 Ten błąd występuje w usłudze Azure Machine Learning, gdy określony parametr jako dane wejściowe i typ wartości jest inny niż typ, który jest oczekiwany, a nie można wykonać niejawnej konwersji.  
  
**Rozwiązanie:** Sprawdzanie wymagań dotyczących modułu i ustalić, jakiego typu wartości jest wymagana (string, integer, double, itp.)  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Nie można przeanalizować parametru|  
|Nie można przeanalizować "{0}" parametru|  
|Nie można przeanalizować (konwersja) "{0}"parametr"{1}"|  
|Nie można skonwertować "{0}"parametr"{1}"do"{2}"|  
|Nie można skonwertować "{0}"wartość parametru"{1}"od"{2}"do"{3}"|  
|Nie można przekonwertować wartości "{0}"w kolumnie"{1}"od"{2}"do"{3}"z użyciem formatu"{4}" podano|  
  

## <a name="error-0003"></a>Błąd 0003  
 Wystąpi wyjątek, jeśli jeden lub kilku danych wejściowych o wartości null ani być pusta.  
  
 Jeśli jakiekolwiek dane wejściowe lub parametry do modułu o wartości null ani być pusta, zostanie wyświetlony ten błąd w usłudze Azure Machine Learning.  Ten błąd może wystąpić na przykład, jeśli nie została wpisana w żadnej wartości dla parametru. Możliwe również, jeśli została wybrana opcja zestawu danych, który ma brakujące wartości lub pusty zestaw danych.  
  
**Rozwiązanie:**
 
+ Otwórz moduł, który wyjątek wytwarzany i sprawdź, czy wszystkie dane wejściowe, które zostały określone. Upewnij się, że wszystkie wymagane dane wejściowe zostały określone. 
+ Upewnij się, że dane, który jest ładowany z usługi Azure storage są dostępne, i że nazwa konta lub klucz nie został zmieniony.  
+ Sprawdź dane wejściowe dla brakujących wartości lub wartości null.
+ Jeśli przy użyciu zapytania w źródle danych, należy sprawdzić, czy dane zostały zwrócone w formacie, których oczekujesz. 
+ Sprawdź, czy błędy pisowni lub inne zmiany w specyfikacji danych.
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Jeden lub więcej danych wejściowych jest wartość null lub jest pusty|  
|Dane wejściowe "{0}" ma wartość null lub jest pusty|  
  

## <a name="error-0004"></a>Błąd 0004  
 Wystąpi wyjątek, jeśli parametr jest mniejsza niż lub równa określonej wartości.  
  
 Jeśli parametr w komunikacie jest poniżej granic wartości wymagane dla modułu do przetwarzania danych, zostanie wyświetlony ten błąd w usłudze Azure Machine Learning.  
  
**Rozwiązanie:** Ponownie moduł zostanie zgłoszony wyjątek, a następnie zmodyfikuj parametr powinien być większy niż określona wartość.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Parametr powinien być większy niż wartość granicy.|  
|Parametr "{0}" wartość powinna być większa niż {1}.|  
|Parametr "{0}"ma wartość"{1}" powinna być większa niż {2}|  
  


## <a name="error-0005"></a>Błąd 0005  
 Wystąpi wyjątek, jeśli parametr jest mniejsza niż określona wartość.  
  
 Będzie ten błąd jest wyświetlany w usłudze Azure Machine Learning, jeśli parametr w komunikacie jest mniejsza lub równa wartość granicy, jest wymagana dla modułu do przetwarzania danych.  
  
**Rozwiązanie:** Ponownie moduł zostanie zgłoszony wyjątek, a następnie zmodyfikuj parametr większa lub równa określonej wartości.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Parametr powinien być większy niż lub równa wartości granicy.|  
|Parametr "{0}" wartość powinna być większa lub równa {1}.|  
|Parametr "{0}"ma wartość"{1}" powinien być większy niż lub równa {2}.|  
  

## <a name="error-0006"></a>Błąd 0006  
 Wystąpi wyjątek, jeśli parametr jest większa lub równa określonej wartości.  
  
 Jeśli parametr w komunikacie jest większa lub równa wartości granicy, wymagane dla modułu do przetwarzania danych, zostanie wyświetlony ten błąd w usłudze Azure Machine Learning.  
  
**Rozwiązanie:** Ponownie moduł zostanie zgłoszony wyjątek, a następnie zmodyfikuj parametr jest mniejsza niż określona wartość.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Niezgodność parametrów. Jeden z parametrów powinien być mniejszy niż inny.|  
|Parametr "{0}"wartość powinna być mniejsza niż parametr"{1}" wartość.|  
|Parametr "{0}"ma wartość"{1}" powinien być mniejszy niż {2}.|  
  

## <a name="error-0007"></a>Błąd 0007  
 Wystąpi wyjątek, jeśli parametr jest większa niż określona wartość.  
  
 Jeśli we właściwościach dla modułu, określona wartość, która jest większa niż jest dozwolone, zostanie wyświetlony ten błąd w usłudze Azure Machine Learning. Na przykład można określić dane, które są poza zakresem dat obsługiwanych lub może wskazywać, że używane pięciu kolumn, gdy tylko trzy kolumny są dostępne. 
 
 Ten błąd może być również wyświetlany, jeśli określasz dwa zestawy danych, które muszą być zgodne w jakiś sposób. Na przykład jeśli są zmienianie nazw kolumn, a następnie określ kolumny, według indeksu, liczbę nazw, które podasz musi odpowiadać liczba indeksów kolumn. Innym przykładem mogą być operacji matematycznych, która używa dwóch kolumn, których kolumny musi mieć taką samą liczbę wierszy. 
  
**Rozwiązanie:**
 
 + Otwórz moduł w danym i przejrzyj ustawienia, właściwość numeryczną.
 + Pozostawały w wartości parametrów w obsługiwanym zakresie wartości dla tej właściwości.
 + Jeśli moduł przyjmuje wielu danych wejściowych, upewnij się, że dane wejściowe są takie same.
<!-- + If the module has multiple properties that can be set, ensure that related properties have appropriate values. For example, when using [Group Data into Bins](group-data-into-bins.md), if you use the option to specify custom bin edges, the number of bins must match the number of values you provide as bin boundaries.-->
 + Sprawdź, czy źródło danych lub zestaw danych został zmieniony. Czasami wartości, który pracuje przy użyciu poprzedniej wersji danych zakończy się niepowodzeniem po zmianie liczby kolumn, typy danych kolumn lub rozmiaru danych.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Niezgodność parametrów. Jeden z parametrów powinna być mniejsza niż lub równe do innego.|  
|Parametr "{0}"wartość powinna być mniejsza niż parametr"{1}" wartość.|  
|Parametr "{0}"ma wartość"{1}" powinien być mniejszy niż lub równe {2}.|  
  

## <a name="error-0008"></a>Błąd 0008  
 Wyjątek występuje, gdy parametr nie jest w zasięgu.  
  
 Jeśli parametr w komunikacie znajduje się poza granicami wymagane dla modułu do przetwarzania danych, zostanie wyświetlony ten błąd w usłudze Azure Machine Learning.  
  
 Na przykład ten błąd jest wyświetlany, jeśli zostanie podjęta próba użycia [Dodaj wiersze](add-rows.md) połączyć dwa zestawy danych, które mają różną liczbę kolumn.  
  
**Rozwiązanie:** Ponownie moduł zostanie zgłoszony wyjątek, a następnie zmodyfikuj parametr mieścić się w określonym zakresie.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Wartość parametru nie jest w określonym zakresie.|  
|Parametr "{0}" wartość nie jest w zasięgu.|  
|Parametr "{0}" wartość musi należeć do zakresu [{1}, {2}].|  
  

## <a name="error-0009"></a>Błąd 0009  
 Wyjątek występuje, gdy nazwa konta magazynu platformy Azure lub nazwę kontenera została określona nieprawidłowo.  
  
Ten błąd występuje w usłudze Azure Machine Learning Studio, gdy określić parametry dla konta usługi Azure storage, ale nie można rozpoznać nazwy lub hasła. Błędy haseł lub nazwy konta może wystąpić wiele przyczyn:
 
 + Konto jest nieprawidłowego typu. Niektóre nowe typy konta nie są obsługiwane do użytku z usługi Machine Learning Studio. Zobacz [importu danych](import-data.md) Aby uzyskać szczegółowe informacje.
 + Wprowadzono nieprawidłowe konto
 + Konto już istnieje.
 + Hasło dla konta magazynu jest nieprawidłowa lub została zmieniona
 + Nie określono nazwy kontenera lub kontener nie istnieje.
 + Nie w pełni Określ ścieżkę do pliku (ścieżka do obiektu blob)
   
**Rozwiązanie:**

Takich problemów często występować podczas próby ręcznie wprowadzić ścieżkę nazwy, hasła lub kontener konta. Zalecamy użycie Kreatora nowej dla [importu danych](import-data.md) moduł, który pomaga wyszukiwać i Sprawdź nazwy.

Sprawdź także, czy konto, kontener lub obiekt blob został usunięty. Skorzystaj z innego narzędzia usługi Azure storage, aby sprawdzić, czy nazwy konta i hasła została wprowadzona poprawnie i czy kontener istnieje. 

Niektóre nowsze typy kont nie są obsługiwane przez usługi Azure Machine Learning. Na przykład nowe oznaczenie "gorąca" lub nie można używać typów przechowywania "zimnych" dla usługi machine learning. Klasyczne konta magazynu i kont magazynu utworzonych w miarę postępów prac prawidłowo "Zastosowania ogólne".

Jeśli określono pełną ścieżkę do obiektu blob, sprawdź, czy ścieżka jest określona jako **kontenera/blobname**, oraz że obiekt blob i kontenera istnieje w ramach konta.  
  
 Ścieżka nie może zawierać ukośnika. Na przykład **obiekt blob/kontenera/** jest nieprawidłowy i należy wprowadzić w formacie **kontenera/obiekt blob**.  

  
|Komunikaty o wyjątkach|  
|------------------------|  
|Nazwa konta usługi Azure storage lub nazwa kontenera jest niepoprawna.|  
|Nazwa konta usługi Azure storage "{0}"lub nazwa kontenera"{1}" jest nieprawidłowy; oczekiwano nazwę kontenera w formacie kontenera/obiekt blob.|  
  

## <a name="error-0010"></a>Błąd 0010  
 W przypadku wejściowych zestawów danych mają nazwy kolumn, które należy dopasować, ale nie wystąpi wyjątek.  
  
 Jeśli indeks kolumny w komunikacie ma inne nazwy kolumn w dwóch danych wejściowych zestawów danych, zostanie wyświetlony ten błąd w usłudze Azure Machine Learning.  
  
**Rozwiązanie:** Użyj [edytować metadane](edit-metadata.md) lub modyfikowania oryginalnego zestawu danych można mieć taką samą nazwę kolumny, indeksu określonej kolumny.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Kolumny z odpowiedni indeks w zestawach danych wejściowych mają różne nazwy.|  
|Nazwy kolumn nie są takie same dla kolumny {0} (liczony od zera) wejściowych zestawów danych ({1} i {2} odpowiednio).|  
  

## <a name="error-0011"></a>Błąd 0011  
 Wyjątek występuje, jeśli przekazany argument zestawu kolumn nie ma zastosowania do żadnej kolumny zestawu danych.  
  
 Jeśli wybór określonej kolumny nie pasuje do żadnego z kolumn w danym zestawie danych, zostanie wyświetlony ten błąd w usłudze Azure Machine Learning.  
  
 Można również otrzymać ten błąd, jeśli jeszcze nie wybrano kolumnę i co najmniej jedna kolumna jest wymagany dla modułu do pracy.  
  
**Rozwiązanie:** Zmodyfikować kolumn wybór w module, dzięki czemu będzie stosowana do kolumn w zestawie danych.  
  
 Jeśli moduł wymaga wybrania określonej kolumny, takiego jak kolumna etykiety, sprawdź, czy wybrano prawa kolumna.  
  
 Jeśli wybrano niewłaściwe kolumn, usuń je i ponownie wykonać eksperyment.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Określonego zestawu kolumn nie ma zastosowania do żadnej kolumny zestawu danych.|  
|Określony zestaw kolumn "{0}" nie ma zastosowania do żadnej kolumny zestawu danych.|  
  

## <a name="error-0012"></a>Błąd 0012  
 Wyjątek występuje, gdy nie można utworzyć wystąpienia klasy przy użyciu zestawu przekazanych argumentów.  
  
**Rozwiązanie:** Ten błąd nie jest możliwością wykonywania akcji przez użytkownika i zostaną wycofane w przyszłej wersji.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Nieprzeszkolonych modelu, najpierw uczenia modelu.|  
|Model nieprzeszkolonych ({0}), użyj uczonego modelu.|  
  

## <a name="error-0013"></a>Błąd 0013  
 Wyjątek występuje, gdy uczeń przekazywany do modułu jest nieprawidłowym typem.  
  
 Ten błąd występuje zawsze, gdy uczonego modelu jest niezgodna z połączonych moduł oceny. <!--For example, connecting the output of [Train Matchbox Recommender](train-matchbox-recommender.md) to [Score Model](score-model.md) (instead of [Score Matchbox Recommender](score-matchbox-recommender.md)) will generate this error when the experiment is run.  -->
  
**Rozwiązanie:**

Określanie typu uczeń, które są generowane przez moduł szkolenia i określają oceniania moduł, który jest odpowiedni dla uczeń. 

Jeśli model został uczony przy użyciu dowolnej z modułów specjalistyczne szkolenie, łączenie uczonego modelu tylko odpowiednie specjalne oceniania modułu. 


|Typ modelu|Moduł szkolenia| Moduł oceny|
|----|----|----|
|wszelkie klasyfikatora|[Trenowanie modelu](train-model.md) |[Score Model](score-model.md)|
|Każdy model regresji|[Trenowanie modelu](train-model.md) |[Score Model](score-model.md)|

<!--| clustering models| [Train Clustering Model](train-clustering-model.md) or [Sweep Clustering](sweep-clustering.md)| [Assign Data to Clusters](assign-data-to-clusters.md)|
| anomaly detection - One-Class SVM | [Train Anomaly Detection Model](train-anomaly-detection-model.md) |[Score Model](score-model.md)|
| anomaly detection - PCA |[Train Model](train-model.md) |[Score Model](score-model.md) </br> Some additional steps are required to evaluate the model. |
| anomaly detection - time series|  [Time Series Anomaly Detection](time-series-anomaly-detection.md) |Model trains from data and generates scores. The module does not create a trained learner and no additional scoring is required. |
| recommendation model| [Train Matchbox Recommender](train-matchbox-recommender.md) | [Score Matchbox Recommender](score-matchbox-recommender.md) |
| image classification | [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md) | [Score Model](score-model.md) |
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) | [Score Vowpal Wabbit Version 7-4 Model](score-vowpal-wabbit-version-7-4-model.md) |   
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 7-10 Model](train-vowpal-wabbit-version-7-10-model.md) | [Score Vowpal Wabbit Version 7-10 Model](score-vowpal-wabbit-version-7-10-model.md) |
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 8 Model](score-vowpal-wabbit-version-8-model.md) | [Score Vowpal Wabbit Version 8 Model](score-vowpal-wabbit-version-8-model.md) |-->
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Uczeń nieprawidłowego typu jest przekazywany.|  
|Uczeń "{0}" ma nieprawidłowy typ.|  


## <a name="error-0014"></a>Błąd 0014  
 Wyjątek występuje, gdy liczba unikatowych wartości kolumny jest większa niż dozwolona.  
  
 Ten błąd występuje, gdy kolumna zawiera zbyt wiele unikatowych wartości.  Na przykład można napotkać ten błąd, jeśli określisz obsługiwania kolumnę jako dane podzielone na kategorie, ale istnieje zbyt wiele unikatowych wartości w kolumnie, aby zezwolić na ukończenie przetwarzania. Ten błąd może być również wyświetlany, jeśli występuje niezgodność między liczbą unikatowe wartości w dwóch danych wejściowych.   
  
**Rozwiązanie:**

Otwórz moduł, który wygenerował błąd i zidentyfikować kolumny używane jako dane wejściowe. Dla niektórych modułów, kliknąć prawym przyciskiem myszy zestaw danych wejściowych i wybierz **Visualize** to zebranie statystyk na poszczególnych kolumnach, w tym liczbę unikatowych wartości i ich dystrybucji.

Dla kolumn, które zamierzasz używać na potrzeby grupowania lub kategoryzacji wykonaj kroki, aby zmniejszyć liczbę unikatowych wartości w kolumnach. Można zmniejszyć na różne sposoby, w zależności od typu danych kolumny. 
<!--
+ For text data, you might be able to use [Preprocess Text](preprocess-text.md) to collapse similar entries. 
+ For numeric data, you can create a smaller number of bins using [Group Data into Bins](group-data-into-bins.md), remove or truncate values using [Clip Values](clip-values.md), or use machine learning methods such as [Principal Component Analysis](principal-component-analysis.md) or [Learning with Counts](data-transformation-learning-with-counts.md) to reduce the dimensionality of the data.  
-->
> [!TIP]
> Nie można znaleźć odpowiadającej Twojemu scenariuszowi rozwiązania? Możesz przekazywać opinie na ten temat, który zawiera nazwę modułu, który wygenerował błąd i typu danych i kardynalności objętej kolumny. Firma Microsoft użyje informacji zapewnienie bardziej docelowe, rozwiązywanie problemów dla typowych scenariuszy.   
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Liczba unikatowych wartości kolumny jest większa niż dozwolona.|  
|Liczba unikatowych wartości w kolumnie: "{0}" przekracza liczbę krotki {1}.|  
  

## <a name="error-0015"></a>Błąd 0015  
 Wyjątek występuje wtedy, gdy połączenie z bazą danych zakończyło się niepowodzeniem.  
  
 Zostanie wyświetlony ten błąd, jeśli wprowadzasz Nieprawidłowa nazwa konta SQL, hasła, serwer bazy danych lub nazwa bazy danych lub jeśli nie można ustanowić połączenia z bazą danych z powodu problemów dotyczących bazy danych lub serwera.  
  
**Rozwiązanie:** Sprawdź, czy nazwa konta, hasła, serwer bazy danych i baza danych została wprowadzona poprawnie i czy określone konto ma odpowiedniego poziomu uprawnień. Sprawdź, czy baza danych jest obecnie dostępna.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Błąd tworzenia połączenia z bazą danych.|  
|Błąd tworzenia połączenia z bazą danych: {0}.|  
  


## <a name="error-0016"></a>Błąd 0016  
 Jeśli wejściowe zestawy danych przekazane do modułu powinien się typy zgodne kolumn, ale nie wystąpi wyjątek.  
  
 Jeśli nie są ze sobą zgodne typy kolumn przekazywane w co najmniej dwóch zestawów danych, zostanie wyświetlony ten błąd w usłudze Azure Machine Learning.  
  
**Rozwiązanie:** Użyj [edytować metadane](edit-metadata.md) lub modyfikowania oryginalnego zestawu danych wejściowych<!--, or use [Convert to Dataset](convert-to-dataset.md)--> Aby upewnić się, że typy w kolumnach są zgodne.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Kolumny z odpowiedni indeks w zestawach danych wejściowych mają niezgodne typy.|  
|Kolumny {0} i {1} są niezgodne.|  
|Typy elementów kolumny są niezgodne dla kolumny {0} (liczony od zera) wejściowych zestawów danych ({1} i {2} odpowiednio).|  
  

## <a name="error-0017"></a>Błąd 0017  
 Wyjątek występuje, gdy wybranej kolumny korzysta z typu danych, który nie jest obsługiwany przez bieżącego modułu.  
  
 Na przykład możesz ten błąd może występować w usłudze Azure Machine Learning Jeśli wybór kolumny zawiera kolumnę o typie danych, który nie może przetworzyć modułu, na przykład kolumna typu ciąg dla operacji matematycznych lub kolumna wynik, których kolumny podzielone na kategorie funkcji jest Wymagane.  
  
**Rozwiązanie:**
 1. Określ kolumny, która jest problem.
 2. Zapoznaj się z wymaganiami modułu.
 3. Modyfikowanie kolumny, aby był zgodny z wymaganiami. Konieczne może korzystać z kilku następujących modułów wprowadzić zmiany, w zależności od kolumny i konwersji, który próbujesz:
    + Użyj [edytować metadane](edit-metadata.md) można zmienić typu danych kolumny lub zmienić użycie kolumn z funkcji numeryczne, podzielone na kategorie do innych niż-podzielonych na kategorie, i tak dalej.
<!--    + Use [Convert to Dataset](convert-to-dataset.md) to ensure that all included columns use data types that are supported by Azure Machine Learning.  If you cannot convert the columns, consider removing them from the input dataset.
    + Use the [Apply SQL Transformation](apply-sql-transformation.md) or [Execute R Script](execute-r-script.md) modules to cast or convert any columns that cannot be modified using [Edit Metadata](edit-metadata.md). These modules provide more flexibility for working with datetime data types.
    + For numeric data types, you can use the [Apply Math Operation](apply-math-operation.md) module to round or truncate values, or use the [Clip Values](clip-values.md) module to remove out of range values.  -->
 4. W ostateczności może być konieczne zmodyfikowanie oryginalnego zestawu danych wejściowych.

> [!TIP]
> Nie można znaleźć odpowiadającej Twojemu scenariuszowi rozwiązania? Możesz przekazywać opinie na ten temat, który zawiera nazwę modułu, który wygenerował błąd i typu danych i kardynalności objętej kolumny. Firma Microsoft użyje informacji zapewnienie bardziej docelowe, rozwiązywanie problemów dla typowych scenariuszy. 
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Nie można przetworzyć kolumny bieżącego typu. Typ nie jest obsługiwany przez moduł.|  
|Nie można przetworzyć kolumny typu {0}. Typ nie jest obsługiwany przez moduł.|  
|Nie można przetworzyć kolumny "{1}" o typie {0}. Typ nie jest obsługiwany przez moduł.|  
|Nie można przetworzyć kolumny "{1}" o typie {0}. Typ nie jest obsługiwany przez moduł. Nazwa parametru: {2}|  
  

## <a name="error-0018"></a>Błąd 0018  
 Wyjątek występuje, gdy wejściowy zestaw danych jest nieprawidłowa.  
  
**Rozwiązanie:** Ten błąd wystąpił w usłudze Azure Machine Learning może znajdować się w wielu kontekstach, zatem nie pojedynczego rozwiązania. Ogólnie rzecz biorąc ten błąd wskazuje, czy danych podana jako dane wejściowe do modułu ma złą liczbę kolumn, lub że typ danych jest niezgodny wymagania dotyczące modułu. Na przykład:  
  
-   Moduł wymaga kolumnę etykiety, ale żadna kolumna nie jest oznaczony jako etykieta lub jeszcze nie zostały wybrane kolumny etykiety.  
  
-   Moduł wymaga, aby dane być podzielone na kategorie, ale danych jest wartością liczbową.  
  
<!---   The module requires a specific data type. For example, ratings provided to [Train Matchbox Recommender](train-matchbox-recommender.md) can be either numeric or categorical, but cannot be floating point numbers.  -->
  
-   Dane są w nieprawidłowym formacie.  
  
-   Zaimportowane dane zawiera nieprawidłowe znaki, nieprawidłowe wartości lub wartości spoza zakresu.  
-   Kolumna jest pusta lub zawiera zbyt wiele brakujące wartości.  
  
 Aby określić wymagania i jak może dane, przejrzyj temat pomocy modułów, które będą korzystać z zestawu danych jako dane wejściowe.  
  
 <!--We also recommend that you use [Summarize Data](summarize-data.md) or [Compute Elementary Statistics](compute-elementary-statistics.md) to profile your data, and use these modules to fix metadata and clean values: [Edit Metadata](edit-metadata.md) and [Clean Missing Data](clean-missing-data.md), [Clip Values](clip-values.md)-->.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Zestaw danych jest nieprawidłowa.|  
|{0} zawiera nieprawidłowe dane.|  
|{0} i {1} spójne kolumna powinna być oparta.|  
  

## <a name="error-0019"></a>Błąd 0019  
 Jeśli kolumna powinna zawierać posortowane wartości, ale nie wystąpi wyjątek.  
  
 Jeśli wartości określonej kolumny są poza kolejnością, zostanie wyświetlony ten błąd w usłudze Azure Machine Learning.  
  
**Rozwiązanie:** Sortowania wartości w kolumnach, modyfikując ręcznie wejściowy zestaw danych i ponownie uruchom moduł.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Wartości w kolumnie nie są sortowane.|  
|Wartości w kolumnie "{0}" nie są sortowane.|  
|Wartości w kolumnie "{0}"zestawu danych"{1}" nie są sortowane.|  
  

## <a name="error-0020"></a>Błąd 0020  
 Wyjątek występuje, gdy liczba kolumn w niektóre zestawy danych przekazane do modułu jest za mały.  
  
 Jeśli zostanie wyświetlony ten błąd w usłudze Azure Machine Learning nie zostały wybrane za mało kolumn dla modułu.  
  
**Rozwiązanie:** Ponownie moduł i upewnij się, że poprawna liczba zaznaczonych kolumn tego selektora kolumn.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Liczba kolumn w zestawie danych wejściowych jest mniejszy od dopuszczalnego minimum.|  
|Liczba kolumn w zestawie danych wejściowych jest mniejszy od dopuszczalnego minimum równego {0} kolumny.|  
|Liczba kolumn w zestawie danych wejściowych "{0}" jest mniejszy od dopuszczalnego minimum równego {1} kolumny.|

## <a name="error-0021"></a>Błąd 0021  
 Wyjątek występuje, gdy liczba wierszy w niektóre zestawy danych przekazane do modułu jest za mały.  
  
 Ten błąd występuje w usłudze Azure Machine Learning w przypadku nie ma wystarczającej ilości wierszy w zestawie danych, które można wykonać określonej operacji. Na przykład zostanie wyświetlony ten błąd, jeśli wejściowy zestaw danych jest puste lub jeśli próbujesz wykonać operację, która wymaga niektóre minimalne liczby wierszy, które mają być prawidłowy. Działania te mogą obejmować (ale nie są ograniczone do) i klasyfikacji grupowania oparte na metody statystyczne, niektóre rodzaje pakowania i uczenie przy użyciu liczenia.  
  
**Rozwiązanie:**
 
 + Otwórz moduł, który zwrócił błąd i sprawdź właściwości wejściowego zestawu danych i modułu. 
 + Sprawdź wejściowy zestaw danych nie jest pusty i że ma wystarczającej liczby wierszy danych pod kątem wymagań opisanych w pomocy modułu.  
 + Jeśli dane są ładowane z zewnętrznego źródła, upewnij się, że źródło danych jest dostępny i że nie ma błędów lub zmienić w definicji danych, które spowodują, że proces importu, aby uzyskać mniej wierszy.
 + W przypadku przeprowadzania operacji na danych nadrzędne moduł, który może mieć wpływ na typ danych lub liczby wartości, takich jak czyszczenie, dzielenie, lub operacje połączone, sprawdź dane wyjściowe z tych operacji, aby określić liczbę zwracanych wierszy.  



## <a name="error-0022"></a>Błąd 0022  
 Wyjątek występuje, jeśli liczba wybranych kolumn w zestawie danych wejściowych nie ma wartości oczekiwanej liczby.  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning może wystąpić, gdy modułu podrzędnego lub operacja wymaga określonej liczby kolumn lub danych wejściowych, a podano zbyt małej lub zbyt wiele kolumn lub danych wejściowych. Na przykład:  
  
-   Określ pojedynczą etykietę kolumny lub kolumny klucza i przypadkowo wybrano wiele kolumn.  
  
-   Czy zmienianie nazw kolumn, ale podano nazw więcej lub mniej, niż liczba kolumn.  
  
-   Liczba kolumn w źródle lub miejscu docelowym został zmieniony lub nie jest zgodna z liczbą kolumn używany przez moduł.  
  
-   Podano listę wartości rozdzielonych przecinkami dla danych wejściowych, ale liczba wartości jest niezgodny lub wielu danych wejściowych nie są obsługiwane.  
  
**Rozwiązanie:** Ponownie moduł i sprawdź kolumnę zaznaczenia, aby upewnić się, że wybrano poprawnej liczby kolumn. Sprawdź dane wyjściowe modułów nadrzędnego i wymagań niższego rzędu.  
  
 Jeśli używany jest jedną z opcji do wyboru kolumny, które można wybrać wiele kolumn (indeksów kolumn, wszystkie funkcje wszystkich numeryczne, itp.), sprawdź poprawność dokładna liczba kolumn zwracanych przez zaznaczenie.  
  
 <!--If you are trying to specify a comma-separated list of datasets as inputs to [Unpack Zipped Datasets](unpack-zipped-datasets.md), unpack only one dataset at a time. Multiple inputs are not supported.  -->
  
 Upewnij się, że numer lub typ nadrzędny kolumn nie została zmieniona.  
  
 Jeśli używasz zalecenie dotyczące zestawu danych do nauczenia modelu, należy pamiętać, że polecania oczekuje ograniczoną liczbę kolumn, odpowiadający par elementu użytkownika lub użytkowników — element klasyfikacji. Usuń dodatkowe kolumny, przed przystąpieniem do uczenia modelu lub dzielenie zalecenie zestawów danych. Aby uzyskać więcej informacji, zobacz [podziału danych](split-data.md).  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Liczba wybranych kolumn w zestawie danych wejściowych nie jest równa do oczekiwanej liczby.|  
|Liczba wybranych kolumn w zestawie danych wejściowych nie ma wartości {0}.|  
|Wzorzec wyboru kolumny "{0}" zawiera liczbę wybranych kolumn w wejściowego zestawu danych nie jest równa {1}.|  
|Wzorzec wybór kolumny "{0}" powinien zapewniać {1} kolumny wybranego w wejściowego zestawu danych, ale {2} kolumny jest/znajdują się.|  



## <a name="error-0023"></a>Błąd 0023  
 Jeśli kolumna docelowa zestawu danych wejściowych jest nieprawidłowa dla bieżącego modułu trainer wystąpi wyjątek.  
  
 Ten błąd w usłudze Azure Machine Learning występuje, jeśli kolumna docelowa (jako zaznaczone w parametrach modułu) nie jest prawidłowym typem danych, zawiera wszystkie brakujące wartości lub nie jest podzielone na kategorie zgodnie z oczekiwaniami.  
  
**Rozwiązanie:** Ponownie moduł danych wejściowych w celu sprawdź zawartość kolumny etykiety lub docelowym. Upewnij się, że nie ma wszystkie brakujące wartości. Jeśli moduł oczekuje kolumny docelowej, aby były podzielone na kategorie, upewnij się, że istnieją więcej niż jednym różne wartości w kolumnie docelowej.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Wejściowy zestaw danych zawiera nieobsługiwane kolumna docelowa.|  
|Wejściowy zestaw danych zawiera nieobsługiwane kolumna docelowa "{0}".|  
|Wejściowy zestaw danych zawiera nieobsługiwane kolumna docelowa "{0}" dla uczeń typu {1}.|  
 

## <a name="error-0024"></a>Błąd 0024  
Wystąpi wyjątek, jeśli zestaw danych nie zawiera kolumny etykiety.  

 Ten błąd wystąpił w usłudze Azure Machine Learning występuje, gdy moduł wymaga kolumny etykiety i zestawu danych nie ma kolumny etykiety. Na przykład oceny ocenami zestawu danych zazwyczaj wymaga kolumny etykiety obecne można obliczyć metryki dokładności.  
 
Możliwe również, kolumna etykiety jest obecny w zestawie danych, ale nie wykryto poprawnie przez uczenie maszynowe Azure.
  
**Rozwiązanie:**

+ Otwórz moduł, który wygenerował błąd i określić, czy kolumna etykiety jest obecny. Nazwa lub typ danych kolumny nie ma znaczenia, tak długo, jak kolumna zawiera pojedynczy wynik (lub zmienna zależnych od ustawień lokalnych), który chcesz przewidzieć. Jeśli nie masz pewności, która kolumna ma etykietę, wyszukaj nazwę rodzajową takich jak *klasy* lub *docelowej*. 
+  Jeśli zestaw danych nie zawiera kolumny etykiety, istnieje możliwość, że kolumna etykiety jawnie lub przypadkowo usunięto nadrzędne. Można również, że zestaw danych nie jest dane wyjściowe moduł oceniania usługi nadrzędnej.
+ Aby wyraźnie oznaczyć kolumny jako kolumny etykiety, należy dodać [edytować metadane](edit-metadata.md) modułu i Połącz z zestawu danych. Wybierz tylko wartości w kolumnie etykietę, a następnie wybierz pozycję **etykiety** z **pola** listy rozwijanej. 
+ Jeśli niewłaściwej kolumnie jest wybierany jako etykiety, możesz wybrać **etykiety zwykłego** z **pola** można ustalić metadanych, w kolumnie. 
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Brak etykiety kolumn w zestawie danych.|  
|Brak żadnej kolumny etykiety w "{0}".|  
  

## <a name="error-0025"></a>Błąd 0025  
 Jeśli zestaw danych nie zawiera kolumny score wystąpi wyjątek.  
  
 Ten błąd w usłudze Azure Machine Learning występuje, jeśli dane wejściowe evaluate model nie zawiera nieprawidłowy wynik kolumn. Na przykład użytkownik próbuje obliczyć zestawu danych, przed jego zostało ocenione z poprawną uczonego modelu lub wynik został jawnie porzucić kolumny nadrzędne. Ten wyjątek ma miejsce, gdy wynik kolumny na dwa zestawy danych są niezgodne. Na przykład użytkownik może próbować porównania dokładność regresor liniowej z tym Klasyfikator binarny.  
  
**Rozwiązanie:** Kontroluj dane wejściowe do modelu Oceń i sprawdź, czy zawiera on co najmniej jedną kolumnę wynik. Jeśli nie, zestaw danych nie zostało ocenione lub kolumny oceny zostały usunięte w module nadrzędnego.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Nie ma żadnej kolumny wynik w zestawie danych.|  
|Brak żadnej kolumny wynik w "{0}".|  
|Brak żadnej kolumny wynik w "{0}" który jest wytwarzany przez "{1}". Ocena zestawu danych, przy użyciu poprawnego typu uczeń.|  
  

## <a name="error-0026"></a>Błąd 0026  
 Wyjątek występuje, gdy nie mogą istnieć kolumny o takiej samej nazwie.  
  
 Ten błąd w usłudze Azure Machine Learning występuje, jeśli wiele kolumn mają taką samą nazwę. Jednym ze sposobów, może zostać wyświetlony ten błąd jest, jeśli zestaw danych nie ma wiersz nagłówka, a nazwy kolumn są automatycznie przypisywane: Col0 Col1, itp.  
  
**Rozwiązanie:** Jeśli kolumny mają takiej samej nazwie, Wstaw [edytować metadane](edit-metadata.md) modułu między wejściowy zestaw danych i modułem. Użyj selektora kolumn w [edytować metadane](edit-metadata.md) aby wybrać kolumny, aby zmienić nazwę, wpisując nowe nazwy do **nowych nazw kolumn** pola tekstowego.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Nazwy kolumn równy są określone w argumenty. Nazwy kolumn w taki sam nie są dozwolone przez moduł.|  
|Równa się nazwy kolumn w argumentach "{0}"i"{1}" są niedozwolone. Określ różne nazwy.|  
  

## <a name="error-0027"></a>Błąd 0027  
 Wyjątek występuje w przypadku, gdy dwa obiekty muszą być takie same, ale nie są.  
  
 To jest typowym błędem w usłudze Azure Machine Learning i może być spowodowany przez wiele warunków.  
  
**Rozwiązanie:** Nie ma określonych podejmowania żadnych działań. Można jednak Sprawdź warunki, takie jak następujące:  
  
-   Jeśli zmieniasz nazwę kolumny, upewnij się, że każda lista (kolumny wejściowe i listę nowych nazw) ma taką samą liczbę elementów.  
  
-   W przypadku dołączania lub łączenia dwóch zestawów danych, upewnij się, że mają one ten sam schemat.  
  
-   Jeśli są Sprzęganie dwóch zestawów danych, które mają wiele kolumn, upewnij się, że kolumny klucza mają te same dane, wpisz i wybierz opcję **zezwala na istnienie duplikatów i zachować kolejność kolumn w zaznaczeniu**.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Rozmiar obiektów przekazanych jest niespójny.|  
|Rozmiar "{0}"jest niespójna o rozmiarze"{1}".|  
  

## <a name="error-0028"></a>Błąd 0028  
 Wyjątek występuje w przypadku, gdy zestaw kolumn zawiera nazwy kolumn zduplikowane i nie jest dozwolone.  
  
 Ten błąd w usłudze Azure Machine Learning występuje, gdy nazwy kolumn są duplikowane; oznacza to, że nie jest unikatowa.  
  
**Rozwiązanie:** Jeśli wszystkie kolumny mają takie same nazwy, dodać wystąpienia [edytować metadane](edit-metadata.md) między wejściowy zestaw danych i module zgłaszania błędu. Użyj selektora kolumn w [edytować metadane](edit-metadata.md) aby wybrać kolumny, aby zmienić nazwę, a następnie wpisz nowe nazwy kolumn do **nowych nazw kolumn** pola tekstowego. Aby zmienić wiele kolumn, upewnij się, że wartości, wpisz w **nowych nazw kolumn** są unikatowe.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Zestaw kolumn zawiera zduplikowane kolumny nazwy.|  
|Nazwa "{0}" jest zduplikowana.|  
|Nazwa "{0}"jest zduplikowana w"{1}".|  
  

## <a name="error-0029"></a>Błąd 0029  
 Wyjątek występuje w przypadku, gdy kiedy przekazywana jest nieprawidłowym identyfikatorem URI.  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning występuje w przypadku, gdy kiedy przekazywana jest nieprawidłowym identyfikatorem URI.  Zostanie wyświetlony ten błąd w przypadku spełnienia dowolnego z następujących warunków:, lub.  
  
-   Publiczny lub identyfikator URI sygnatury dostępu Współdzielonego podana dla usługi Azure Blob Storage do odczytu lub zapisu zawiera błąd.  
  
-   Przedział czasu dla sygnatury dostępu Współdzielonego wygasł.  
  
-   Adres URL sieci Web za pośrednictwem źródła HTTP reprezentuje plik lub sprzężenia zwrotnego identyfikatora URI.  
  
-   Adres URL sieci Web za pośrednictwem protokołu HTTP zawiera nieprawidłowo sformatowany adres URL.  
  
-   Nie można rozpoznać adresu URL przez zdalnego źródła.  
  
**Rozwiązanie:** Ponownie moduł i Zweryfikuj format identyfikatora URI. Źródło danych jest adres URL sieci Web za pośrednictwem protokołu HTTP, sprawdź, czy zamierzony źródła nie jest plikiem lub sprzężenia zwrotnego identyfikatora URI (localhost).  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Nieprawidłowy identyfikator Uri jest przekazywany.|  
  

## <a name="error-0030"></a>Błąd 0030  
 Wyjątek występuje w przypadku, gdy nie jest możliwe pobrać plik.  
  
 Ten wyjątek w usłudze Azure Machine Learning występuje, gdy nie jest możliwe pobrać plik. Zostanie wyświetlony ten wyjątek podczas próby odczytu ze źródła HTTP nie powiodło się po ponownej próbie trzech (3) prób.  
  
**Rozwiązanie:** Sprawdź, czy identyfikator URI do źródła HTTP jest prawidłowa i że lokacja jest obecnie dostępna za pośrednictwem Internetu.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Nie można pobrać plik.|  
|Wystąpił błąd podczas pobierania pliku: {0}.|  
  

## <a name="error-0031"></a>Błąd 0031  
 Wyjątek występuje, gdy liczba kolumn w zestawie kolumn jest mniejsza niż jest to potrzebne.  
  
 Ten błąd w usłudze Azure Machine Learning występuje, jeśli liczba zaznaczonych kolumn jest mniejsza niż jest to potrzebne.  Zostanie wyświetlony ten błąd, jeśli minimalna wymagana liczba kolumn nie są wybrane.  
  
**Rozwiązanie:** Dodaj dodatkowe kolumny do zaznaczenia kolumny przy użyciu **selektora kolumn**.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Liczba kolumn w zestawie kolumn jest mniejsza niż jest to wymagane.|  
|{0} powinny być określone kolumny. Rzeczywista liczba określonych kolumn jest {1}.|  

## <a name="error-0032"></a>Błąd 0032  
 Jeśli argument nie jest liczbą, wystąpi wyjątek.  
  
 Jeśli argument ma wartość o podwójnej precyzji lub NaN, zostanie wyświetlony ten błąd w usłudze Azure Machine Learning.  
  
**Rozwiązanie:** Zmodyfikuj określonego argumentu, aby użyć prawidłowej wartości.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Argument nie jest liczbą.|  
|"{0}" nie jest liczbą.|  
  

## <a name="error-0033"></a>Błąd 0033  
 Jeśli argument jest nieskończoności wystąpi wyjątek.  
  
 Ten błąd w usłudze Azure Machine Learning występuje, jeśli argument jest nieskończony. Zostanie wyświetlony ten błąd, jeśli argument jest albo `double.NegativeInfinity` lub `double.PositiveInfinity`.  
  
**Rozwiązanie:** Zmodyfikuj określony argument jest prawidłową wartością.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Argument musi być skończoną.|  
|"{0}" nie jest jednak ograniczona.|  
  

## <a name="error-0034"></a>Błąd 0034  
 Wystąpi wyjątek, jeśli istnieje więcej niż jedną ocenę pary element danego użytkownika.  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning występuje w zaleceniu, jeśli pary element użytkownika ma wartość więcej niż jednej klasyfikacji.  
  
**Rozwiązanie:** Upewnij się, że pary element użytkownika posiada jedną wartość oceny.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Istnieje więcej niż jednej klasyfikacji dla wartości w zestawie danych.|  
|Więcej niż jednej klasyfikacji dla użytkownika {0} elementu i {1} w tabeli danych prognozowania oceny.|  
  

## <a name="error-0035"></a>Błąd 0035  
 Wyjątek występuje, gdy żadne funkcje nie zostały dostarczone dla danego użytkownika lub elementu.  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning występuje próbujesz użyć modelu zalecenie do oceniania, ale nie można odnaleźć funkcji wektora.  
  
**Rozwiązanie:**

Polecania Matchbox wiążą się pewne wymagania, które muszą zostać spełnione w przypadku korzystania z funkcji elementu lub funkcji użytkownika.  Ten błąd wskazuje, że wektor funkcji brakuje dla użytkownika lub element, który podana jako dane wejściowe.  Należy się upewnić, że wektor funkcji jest dostępny w danych dla każdego użytkownika lub elementu.  
  
 Na przykład, jeśli szkolenia zalecenie modelu przy użyciu funkcji, takich jak wiek, lokalizacji lub dochodów użytkownika, ale teraz chcemy utworzyć oceny dla nowych użytkowników, którzy nie zostały zaobserwowane w trakcie szkolenia, należy podać niektóre równoważne zestaw funkcji (to znaczy, wiek, lokalizację i wartości dochodu) dla nowych użytkowników w celu prognozowania odpowiedni dla nich. 
 
 Jeśli nie masz żadnych funkcji dla tych użytkowników, należy wziąć pod uwagę technicznego opracowywania funkcji, aby wygenerować odpowiednie funkcje.  Na przykład w przypadku braku wartości wiek lub dochodów indywidualnego użytkownika może generować przybliżone wartości do użycia dla grupy użytkowników. 
 
<!--When you are scoring from a recommendation mode, you can use item or user features only if you previously used item or user features during training. For more information, see [Score Matchbox Recommender](score-matchbox-recommender.md).
 
For general information about how the Matchbox recommendation algorithm works, and how to prepare a dataset of item features or user features, see [Train Matchbox Recommender](train-matchbox-recommender.md).  -->
  
 > [!TIP]
 > Nie ma zastosowania w danym przypadku rozwiązania? Zachęcamy do wysyłania opinii, w tym artykule i informacji na temat scenariusza, w tym module i liczbę wierszy w kolumnie. Używamy tych informacji do zapewnienia bardziej szczegółowe kroki rozwiązywania problemów w przyszłości.
   
|Komunikaty o wyjątkach|  
|------------------------|  
|Żadne funkcje nie podano dla wymaganego użytkownika lub elementu.|  
|Funkcje dla {0} wymagane, ale nie została podana.|  
  

## <a name="error-0036"></a>Błąd 0036  
 Wyjątek występuje, jeśli podano wiele funkcji wektorów dla danego użytkownika lub elementu.  
  
 Ten błąd w usłudze Azure Machine Learning występuje, jeśli wektor funkcji jest zdefiniowany więcej niż jeden raz.  
  
**Rozwiązanie:** Upewnij się, że wektor funkcja nie jest zdefiniowany więcej niż jeden raz.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Zduplikowana definicja funkcji dla użytkownika lub elementu.|  
|Zduplikowana definicja funkcji {0}.|  
  

## <a name="error-0037"></a>Błąd 0037  
 Jeśli określono wiele kolumn etykiety, a dozwolona jest tylko jedna wystąpi wyjątek.  
  
 Ten błąd w usłudze Azure Machine Learning występuje, jeśli wybrano więcej niż jedną kolumnę jako nowe kolumny etykiety. Najbardziej algorytmów uczenia nadzorowanego wymagają jedną kolumnę jako obiektu docelowego lub etykiety.  
  
**Rozwiązanie:** Pamiętaj wybrać pojedynczą kolumnę jako nowe kolumny etykiety.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Określono wiele kolumn etykiety.|  
  

## <a name="error-0038"></a>Błąd 0038  
 Wyjątek występuje, gdy liczba oczekiwanych elementów powinna być dokładna wartość, ale nie jest.  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning występuje, gdy liczba oczekiwanych elementów powinna być dokładna wartość, ale nie jest.  Zostanie wyświetlony ten błąd, jeśli liczba elementów, które nie jest prawidłową wartość oczekiwana.  
  
**Rozwiązanie:** Zmodyfikuj dane wejściowe ma poprawnej liczby elementów.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Liczba elementów, które nie jest prawidłowy.|  
|Liczba elementów w "{0}" jest nieprawidłowa.|  
|Liczba elementów w "{0}" nie jest prawidłową liczbą {1} następującą liczbę elementów.|  
  

## <a name="error-0039"></a>Błąd 0039  
 Wyjątek występuje, gdy operacja nie powiodła się.  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning występuje, gdy nie można ukończyć operacji wewnętrznej.  
  
**Rozwiązanie:** Ten błąd jest spowodowany przez wiele warunków i nie ma żadnych szczególnych którąkolwiek ze stron.  
 Poniższa tabela zawiera ogólne komunikaty dla tego błędu, które następują opis określonego warunku. 
 
 Jeśli nie są dostępne żadne informacje [Wyślij opinię](https://social.msdn.microsoft.com/forums/azure/home?forum=MachineLearning) i podaj informacje dotyczące modułów, które wygenerowany błąd i odpowiednich warunków.
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Operacja nie powiodła się.|  
|Wystąpił błąd podczas wykonywania operacji: {0}.|  
  

## <a name="error-0040"></a>Błąd 0040  
 Wyjątek występuje podczas wywoływania modułu przestarzałe.  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning jest generowany podczas wywoływania modułu przestarzałe.  
  
**Rozwiązanie:** Zastąp moduł przestarzałe obsługiwanym elementem. Zobacz dziennik danych wyjściowych modułu informacje dotyczące modułu, którego można użyć.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Uzyskiwanie dostępu do modułu przestarzałe.|  
|Moduł "{0}" jest przestarzały. Użyj modułu "{1}" zamiast niego.|  
 

## <a name="error-0041"></a>Błąd 0041  
 Wyjątek występuje podczas wywoływania modułu przestarzałe.  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning jest generowany podczas wywoływania modułu przestarzałe.  
  
**Rozwiązanie:** Zastąp moduł przestarzałe zestaw obsługiwanych z nich. Te informacje powinny być widoczne w dzienniku danych wyjściowych modułu.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Uzyskiwanie dostępu do modułu przestarzałe.|  
|Moduł "{0}" jest przestarzały. Używać modułów "{1}" dla żądanej funkcji.|  
 

## <a name="error-0042"></a>Błąd 0042  
 Wyjątek występuje, gdy nie jest możliwe do przekonwertowania na kolumnę do innego typu.  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning występuje, gdy nie jest możliwe przekonwertować kolumnę do określonego typu.  Zostanie wyświetlony ten błąd, jeśli moduł wymaga określonego typu danych, takich jak daty/godziny, tekst, liczba zmiennoprzecinkowa lub liczba całkowita, ale nie jest możliwe do przekonwertowania istniejącej kolumny na wymagany typ.  
 
Na przykład możesz wybrać kolumnę i spróbuj przekonwertować typ danych numerycznych do użycia w ramach operacji matematycznych i ten błąd, jeśli kolumna zawiera nieprawidłowe dane. 

Kolejnym powodem, czego mogą uzyskać ten błąd, Jeśli spróbujesz użyć kolumny zawierające liczb zmiennoprzecinkowych lub wiele unikatowych wartości podzielonych na kategorie kolumny. 
  
**Rozwiązanie:**

+ Otwórz stronę pomocy dla modułu, który wygenerował błąd i sprawdź wymagania dotyczące typu danych.
+ Sprawdź typy danych kolumn w zestawie danych wejściowych.
+ Sprawdź, czy dane pochodzące ze źródeł danych bez schematu tzw.
+ Sprawdź zestaw danych dla brakujących wartości lub znaki specjalne, które mogą blokować konwersja na typ żądanych danych. 
    + Numeryczne typy danych powinny być zgodne: na przykład wyszukać liczb zmiennoprzecinkowych w kolumnie liczb całkowitych.
    + Poszukaj ciągów tekstowych lub NA wartości w kolumnie liczb. 
    + Wartości logiczne można przekonwertować na reprezentację w postaci odpowiednich w zależności od wymaganego typu danych.
    + Sprawdź kolumn tekstowych nie były znakami unicode, znaków tabulacji lub znaki kontrolne
    + Dane daty/godziny powinny być zgodne, aby uniknąć błędów do modelowania, ale czyszczenie mogą być złożone z powodu wielu formatach. Należy rozważyć użycie <!--the [Execute R Script](execute-r-script.md) or -->[Uruchom skrypt języka Python](execute-python-script.md) modułów do wykonywania oczyszczania.  
+ W razie potrzeby zmodyfikuj wartości w wejściowego zestawu danych, tak aby kolumna może być zostały pomyślnie skonwertowane. Modyfikacja może obejmować pakowania, obcięcie lub operacje zaokrąglania, eliminacja elementy odstające lub przypisywania brakujące wartości. Zobacz następujące artykuły dla niektórych typowych scenariuszy przekształcania danych w usłudze machine learning:
    + [Czyszczenie brakujących danych](clean-missing-data.md)
    + [Normalizacji danych](normalize-data.md)
<!--+ [Clip Values](clip-values.md) 
    + [Group Data Into Bins](group-data-into-bins.md)
  -->
 
> [!TIP]
> Rozpoznawanie niejasne, lub nie ma zastosowania w danym przypadku? Zachęcamy do wysyłania opinii, w tym artykule i informacji na temat scenariusza, w tym module i typ danych kolumny. Używamy tych informacji do zapewnienia bardziej szczegółowe kroki rozwiązywania problemów w przyszłości.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Niedozwolona konwersja.|  
|Nie można przekonwertować kolumny typu {0} z kolumną typu {1}.|  
|Nie można przekonwertować kolumnę "{2}" o typie {0} z kolumną typu {1}.|  
|Nie można przekonwertować kolumny "{2}" o typie {0} do kolumny "{3}" o typie {1}.|  
  

## <a name="error-0043"></a>Błąd 0043  
 Wyjątek występuje, gdy typ elementu nie jawnie implementować Equals.  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning jest nieużywana i staną się przestarzałe.  
  
**Rozwiązanie:** Brak.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Żadna z dostępnych metod jawne równa znaleziono.|  
|Nie można porównać wartości dla kolumny \\"{0}\\" o typie {1}. Żadna z dostępnych metod jawne równa znaleziono.|  


## <a name="error-0044"></a>Błąd 0044  
 Wyjątek występuje, gdy nie jest możliwe określenie elementu typu kolumny z istniejącymi wartościami.  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning występuje, gdy nie jest możliwe do wywnioskowania typu kolumny lub kolumn w zestawie danych. Dzieje się tak zazwyczaj, gdy łączenie dwóch lub więcej zestawów danych przy użyciu elementu różnych typów. Jeśli nie można określić wspólny typ, który jest w stanie odtworzyć wszystkich wartości w kolumnie lub kolumnach bez utraty informacji jest usługi Azure Machine Learning, generuje ten błąd.  
  
**Rozwiązanie:** Upewnij się, że wszystkie wartości w danej kolumnie w obu zestawach danych łączonych albo tego samego typu (liczbową, logiczną, podzielone na kategorie, string, Data itp.) lub może być przekształcone do tego samego typu.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Nie może pochodzić typ elementu kolumny.|  
|Nie może pochodzić z typu elementu dla kolumny "{0}" — wszystkie elementy są odwołania o wartości null.|  
|Nie może pochodzić z typu elementu dla kolumny "{0}"zestawu danych"{1}" — wszystkie elementy są odwołania o wartości null.|  
  

## <a name="error-0045"></a>Błąd 0045  
 Wyjątek występuje, gdy nie jest możliwe utworzyć kolumnę ze względu na typy mieszane elementów w źródle.  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning jest generowany w przypadku różnych typów elementów z dwóch zestawów danych, które są połączone.  
  
**Rozwiązanie:** Upewnij się, że wszystkie wartości w danej kolumnie w obu zestawach danych są łączone są tego samego typu (liczbową, logiczną, podzielone na kategorie, string, Data itp.).  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Nie można utworzyć kolumny z typami elementu mieszane.|  
|Nie można utworzyć kolumny o identyfikatorze "{0}" z elementu mieszane typy: \n\tType danych [{1}, {0}] jest {2}\n\tType danych [{3}, {0}] jest {4}.|  
  

## <a name="error-0046"></a>Błąd 0046  
 Wyjątek występuje, gdy nie jest możliwe utworzyć katalog w określonej ścieżce.  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning występuje, gdy nie jest możliwe utworzyć katalog w określonej ścieżce. Zostanie wyświetlony ten błąd, jeśli dowolnej części ścieżki do katalogu wyjściowego dla zapytania programu Hive jest niepoprawny lub niedostępny.  
  
**Rozwiązanie:** Ponownie moduł i Sprawdź ścieżkę katalogu jest poprawnie sformatowany i czy jest ona dostępna przy użyciu bieżących poświadczeń.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Określ katalog wyjściowy prawidłowe.|  
|Katalog: {0} nie można utworzyć. Określ prawidłową ścieżkę.|  
  

## <a name="error-0047"></a>Błąd 0047  
 Wyjątek występuje, gdy liczba kolumn funkcji w niektóre zestawy danych przekazane do modułu jest za mały.  
  
 Ten błąd w usłudze Azure Machine Learning występuje, jeśli wejściowy zestaw danych do szkolenia nie zawiera minimalną liczbę kolumn wymagane przez algorytm. Zazwyczaj albo zestaw danych jest pusta lub zawiera tylko kolumny szkolenia.  
  
**Rozwiązanie:** Ponownie wejściowego zestawu danych, aby upewnić się, że występują co najmniej jeden dodatkowe kolumny oprócz kolumn etykiety.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Liczba kolumn w funkcji w zestawie danych wejściowych jest mniejszy od dopuszczalnego minimum.|  
|Liczba kolumn w funkcji w zestawie danych wejściowych jest mniejszy od dopuszczalnego minimum równego {0} kolumny.|  
|Liczba kolumn w funkcji w zestawie danych wejściowych "{0}" jest mniejszy od dopuszczalnego minimum równego {1} kolumny.|  
  

## <a name="error-0048"></a>Błąd 0048  
 Wyjątek występuje w przypadku, gdy nie jest możliwe otworzyć plik.  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning występuje, gdy nie jest możliwe do otwarcia pliku do odczytu lub zapisu. Ten błąd może występować z tych powodów:  
  
-   Kontenera lub pliku (obiektów blob) nie istnieje  
  
-   Poziom dostępu do pliku lub kontenera nie zezwala na dostęp do pliku  
  
-   Plik jest zbyt duży do odczytu lub nieprawidłowy format  
  
**Rozwiązanie:** Ponownie w module, plik, który chcesz odczytać.  
  
 Sprawdź, czy nazwy kontenera i pliku są poprawne.  
  
 Sprawdź, czy masz uprawnienie dostępu do pliku za pomocą klasycznego portalu Azure lub usługi Azure storage.  
  
  <!--If you are trying to read an image file, make sure that it meets the requirements for image files in terms of size, number of pixels, and so forth. For more information, see [Import Images](import-images.md).  -->
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Nie można otworzyć pliku.|  
|Wystąpił błąd podczas otwierania pliku: {0}.|  


## <a name="error-0049"></a>Błąd 0049  
 Wyjątek występuje w przypadku, gdy nie jest możliwe analizować plik.  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning występuje, gdy nie jest możliwe analizować plik. Zostanie wyświetlony ten błąd, jeśli wybrany format pliku w [importu danych](import-data.md) modułu jest niezgodny z rzeczywisty format pliku, lub jeśli plik zawiera nierozpoznany znak.  
  
**Rozwiązanie:** Ponownie moduł i popraw Wybieranie formatu plików, jeśli jest niezgodny format pliku. Jeśli to możliwe Sprawdź plik, aby potwierdzić, że nie zawiera żadnych niedozwolonych znaków.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Nie można przeanalizować pliku.|  
|Wystąpił błąd podczas analizowania pliku: {0}.|  
  

## <a name="error-0050"></a>Błąd 0050  
 Wyjątek występuje w przypadku, gdy dane wejściowe i wyjściowe pliki są takie same.  
  
**Rozwiązanie:** Ten błąd wystąpił w usłudze Azure Machine Learning jest nieużywana i staną się przestarzałe.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Określone pliki, dane wejściowe i wyjściowe nie może być taka sama.|


## <a name="error-0051"></a>Błąd 0051  
 W przypadku kilku plików wyjściowych są takie same, wystąpi wyjątek.  
  
**Rozwiązanie:** Ten błąd wystąpił w usłudze Azure Machine Learning jest nieużywana i staną się przestarzałe.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Określone pliki dla danych wyjściowych nie może być taka sama.|


## <a name="error-0052"></a>Błąd 0052  
 Wyjątek występuje, gdy klucz konta magazynu platformy Azure została określona nieprawidłowo.  
  
 Ten błąd w usłudze Azure Machine Learning występuje, jeśli klucz używany do uzyskania dostępu do konta usługi Azure storage jest nieprawidłowa. Na przykład zostanie wyświetlony ten błąd, jeśli klucz usługi Azure storage została obcięta po skopiowany i wklejony lub użyto nieprawidłowego klucza.  
  
 Aby uzyskać więcej informacji na temat pobierania klucza konta usługi Azure storage, zobacz [wyświetlanie, kopiowanie i ponowne generowanie magazynu, klucze dostępu](https://azure.microsoft.com/documentation/articles/storage-create-storage-account-classic-portal/).  
  
**Rozwiązanie:** Ponownie moduł i sprawdź poprawność konta klucza magazynu Azure ponownie skopiuj klucz z klasycznego portalu Azure w razie potrzeby.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Klucz konta usługi Azure storage jest niepoprawna.|  
  

## <a name="error-0053"></a>Błąd 0053  
 Wyjątek występuje w przypadku, gdy nie ma żadnych funkcji użytkownika lub elementy matchbox zaleceń dotyczących.  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning jest generowana, gdy nie można odnaleźć wektor funkcji.  
  
**Rozwiązanie:** Upewnij się, czy wektor funkcji jest obecna w wejściowego zestawu danych.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Funkcje użytkownika lub / i elementy są wymagane, ale nie została podana.|  

## <a name="error-0054"></a>Błąd 0054  
 Wyjątek występuje, gdy jest za mało odrębnych wartości w kolumnie, aby ukończyć operację.  
  
**Rozwiązanie:** Ten błąd wystąpił w usłudze Azure Machine Learning jest nieużywana i staną się przestarzałe.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Danych ma za mało odrębnych wartości w określonej kolumnie, aby ukończyć operację.|  
|Danych ma za mało odrębnych wartości w określonej kolumnie, aby ukończyć operację. Jest wymagany co najmniej {0} elementów.|  
|Danych ma za mało odrębnych wartości w kolumnie "{1}" Aby ukończyć operację. Jest wymagany co najmniej {0} elementów.|  
  

## <a name="error-0055"></a>Błąd 0055  
 Wyjątek występuje podczas wywoływania modułu przestarzałe.  Ten błąd wystąpił w usłudze Azure Machine Learning pojawia się, jeśli zostanie podjęta próba wywołania modułu, która została wycofana.
  
**Rozwiązanie:**
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Uzyskiwanie dostępu do modułu przestarzałe.|  
|Moduł "{0}" jest przestarzały.|  

## <a name="error-0056"></a>Błąd 0056  
 Wyjątek występuje, gdy kolumny wybranych do operacji narusza wymagania.  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning występuje, gdy decyduje się kolumny dla operacji, która wymaga, aby z określonego typu danych kolumny. 
 
 Ten błąd może też być kolumna jest poprawnym typie danych, ale moduł, którego używasz wymaga, aby kolumna również oznaczone jako funkcja, etykiety lub podzielonych na kategorie kolumny.  
  
  <!--For example, the [Convert to Indicator Values](convert-to-indicator-values.md) module requires that columns be categorical, and will raise this error if you select a feature column or label column.  -->
  
**Rozwiązanie:**
  
1.  Sprawdź typ danych kolumny, które są obecnie wybrane. 

2. Sprawdzenia, czy wybrane kolumny są podzielone na kategorie, etykiety lub kolumn funkcji.  
  
3.  Zapoznać się z tematem Pomocy dla modułu, w którym zostały wprowadzone wybór kolumn, aby ustalić, czy istnieją określone wymagania dotyczące użycia typu lub kolumnę danych.  
  
3.  Użyj [edytować metadane](edit-metadata.md) Aby zmienić typ kolumny na czas trwania tej operacji. Pamiętaj zmienić typ kolumny oryginalnej wartości, przy użyciu innego wystąpienia programu [edytować metadane](edit-metadata.md), jeśli potrzebujesz uzyskać niższego rzędu.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Co najmniej jeden z zaznaczonych kolumn nie znajdowały się w dozwolonych kategorii.|  
|Kolumna o nazwie "{0}" nie znajduje się w dozwolonych kategorii.|  
  

## <a name="error-0057"></a>Błąd 0057  
 Wyjątek występuje podczas próby utworzenia pliku lub obiektu blob, który już istnieje.  
  
 Ten wyjątek występuje, gdy używasz [Eksport danych](export-data.md) modułu lub inny moduł, aby zapisać wyniki eksperymentu w usłudze Azure Machine Learning, usługi Azure blob storage, ale próba utworzenia pliku lub obiektu blob, który już istnieje.   
  
**Rozwiązanie:**
 
 Tylko wtedy, gdy właściwość jest ustawiona wcześniej otrzymają ten błąd **tryb zapisu w magazynie Azure blob storage** do **błąd**. Zgodnie z projektem ten moduł zgłasza błąd, jeśli użytkownik podejmie próbę zapisu zestawu danych obiektów blob, który już istnieje.
 
 - Otwórz właściwości modułu i zmień wartość właściwości **tryb zapisu w magazynie Azure blob storage** do **Zastąp**.
 - Można też, wpisz nazwę pliku lub inny docelowy obiekt blob i należy określić obiekt blob, który już nie istnieje.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Plik bądź obiekt Blob już istnieje.|  
|Plik bądź obiekt Blob "{0}" już istnieje.|  
  

## <a name="error-0058"></a>Błąd 0058  
 Ten błąd w usłudze Azure Machine Learning występuje, jeśli zestaw danych nie zawiera kolumny oczekiwano etykiety.  
  
 Ten wyjątek może również wystąpić, gdy kolumna etykiety, pod warunkiem jest niezgodna, danych lub typu danych, oczekiwany przez uczeń lub ma nieprawidłowe wartości. Na przykład ten wyjątek jest generowany, korzystając z kolumną przechowywanymi w rzeczywistym etykiety podczas szkolenia Klasyfikator binarny.  
  
**Rozwiązanie:** Rozdzielczość zależy od uczeń lub trainer, którego używasz i typy danych kolumn w zestawie danych. Po pierwsze sprawdź wymagania dotyczące algorytmu uczenia maszynowego lub modułu szkolenia.  
  
 Ponownie wejściowego zestawu danych. Sprawdź kolumnę oczekujesz traktowane jako etykieta ma odpowiednie dane, typ modelu, który tworzysz.  
  
 Sprawdź dane wejściowe dla brakujących wartości i wyeliminować lub je zastąpić, jeśli to konieczne.  
  
 W razie potrzeby dodaj [edytować metadane](edit-metadata.md) modułu i upewnij się, że kolumna etykiety jest oznaczony jako etykieta.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Kolumna etykiety jest niezgodne z oczekiwaniami|  
|Kolumna etykiety jest niezgodne z oczekiwaniami w "{0}".|  
|Kolumna etykiety "{0}"nie jest oczekiwany w"{1}".|  
  

## <a name="error-0059"></a>Błąd 0059  
 Wyjątek występuje, gdy indeks kolumny określone w selektorze kolumn nie może zostać przeanalizowany.  
  
 Ten błąd w usłudze Azure Machine Learning występuje, jeśli nie można przeanalizować indeks kolumny, określony przy użyciu selektora kolumn.  Zostanie wyświetlony ten błąd, gdy indeks kolumny jest w nieprawidłowym formacie, którego nie można przeanalizować.  
  
**Rozwiązanie:** Zmodyfikuj indeks kolumny, aby korzystała z wartości nieprawidłowy indeks.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Nie można przeanalizować jednego lub kilku indeksów określonej kolumny lub zakresy indeksu.|  
|Indeks kolumny lub zakresu "{0}" nie można przeanalizować.|  
  

## <a name="error-0060"></a>Błąd 0060  
 Wyjątek występuje, gdy określono poza zakres kolumny zakresu w selektorze kolumn.  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning występuje, gdy w przypadku określenia zakresu spoza zakresu kolumny w selektorze kolumn. Zostanie wyświetlony ten błąd, jeśli zakres kolumny w selektorze kolumn nie odpowiadają kolumn w zestawie danych.  
  
**Rozwiązanie:** Zmodyfikuj zakres kolumny w selektorze kolumn do odnoszą się do kolumn w zestawie danych.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Nieprawidłowy lub poza zakres określony zakres kolumny indeksu.|  
|Zakres kolumny "{0}" jest nieprawidłowy lub poza zakresem.|  
  

## <a name="error-0061"></a>Błąd 0061  
 Wyjątek występuje, gdy spróbujesz dodać wiersz do elementu DataTable, który ma różną liczbę kolumn niż tabela.  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning występuje, gdy spróbujesz dodać wiersz do zestawu danych, który ma różną liczbę kolumn niż zestaw danych.  Zostanie wyświetlony ten błąd, jeśli wiersz, który jest dodawany do zestawu danych ma inną liczbę kolumn z wejściowego zestawu danych.  Nie można dołączyć wiersz do zestawu danych, jeśli liczba kolumn jest inna.  
  
**Rozwiązanie:** Zmodyfikuj wejściowy zestaw danych, które mają tę samą liczbę kolumn jako wiersz dodany lub zmodyfikuj wierszy dodane do mają tę samą liczbę kolumn jako zestawu danych.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Wszystkie tabele muszą mieć tę samą liczbę kolumn.|  
  

## <a name="error-0062"></a>Błąd 0062  
 Wyjątek występuje podczas próby do porównywania dwóch modeli uczeń różnych typów.  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning jest generowany, gdy nie można porównać metryki oceny dla dwóch różnych zestawów danych ocenami. W tym przypadku nie jest możliwe do porównania modele do produkcji dwa zestawy danych z ocenami skuteczność.  
  
**Rozwiązanie:** Sprawdź, czy ocenami wyniki są produkowane przez tego samego rodzaju modelu uczenia maszynowego (Klasyfikacja binarna, regresji, klasyfikacji wieloklasowej, zalecenia, klastrów, wykrywanie anomalii itp.) Wszystkie modele, które można porównać muszą mieć ten sam typ uczeń.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Wszystkie modele muszą mieć ten sam typ uczeń.|  
  

 <!--## Error 0063  
 This exception is raised when R script evaluation fails with an error.  
  
 This error occurs when you have provided an R script in one of the [R language modules](r-language-modules.md) in Azure Machine Learning, and the R code contains internal syntax errors. The exception can also occur if you provide the wrong inputs to the R script. 
 
 The error can also occur if the script is too large to execute in the workspace. The maximum script size for the **Execute R Script** module is 1,000 lines or 32 KB of work space, whichever is lesser.
  
**Resolution:**

1. In Azure Machine Learning Studio, right-click the module that has the error, and select **View Log**.
2. Examine the standard error log of the module, which contains the stack trace.
    + Lines beginning with [ModuleOutput] indicate output from R.
    + Messages from R marked as **warnings** typically do not cause the experiment to fail.
3. Resolve script issues.  
    + Check for R syntax errors. Check for variables that are defined but never populated.
    + Review the input data and the script to determine if either the data or variables in the script use characters not supported by Azure Machine Learning.
    + Check whether all package dependencies are installed.
    + Check whether your code loads required libraries that are not loaded by default.
    + Check whether the required packages are the correct version.
    + Make sure that any dataset that you want to output is converted to a data frame.  
4.  Resubmit the experiment.

 <!--
> [!NOTE]
> These topics contains examples of R code that you can use, as well as links to experiments in the [Cortana Intelligence Gallery](https://gallery.cortanaintelligence.com) that use R script.
> + [Execute R Script](execute-r-script.md)
> + [Create R Model](create-r-model.md)
-->  
|Komunikaty o wyjątkach|  
|------------------------|  
|Wystąpił błąd podczas obliczania skrypt języka R.|  
|Wystąpił następujący błąd podczas obliczania skrypt języka R:---początek komunikat o błędzie z języka R--- {0} ---koniec komunikat o błędzie z języka R---|  
|Podczas obliczania skrypt języka R "{1}" Wystąpił następujący błąd:---początek komunikat o błędzie z języka R--- {0} ---koniec komunikat o błędzie z języka R---|  
  


## <a name="error-0064"></a>Błąd 0064  
 Wyjątek występuje, gdy nazwa konta usługi Azure storage lub klucz magazynu została określona nieprawidłowo.  
  
 Ten błąd w usłudze Azure Machine Learning występuje, jeśli nazwa konta usługi Azure storage lub klucz magazynu została określona nieprawidłowo. Zostanie wyświetlony ten błąd, jeśli wprowadzasz nieprawidłowa nazwę użytkownika lub hasło dla konta magazynu. Może to nastąpić, jeśli ręcznie wprowadzić nazwę użytkownika lub hasło. Może również wystąpić, jeśli konto zostało usunięte.  
  
**Rozwiązanie:** Sprawdź, czy nazwa konta i hasło została wprowadzona poprawnie i czy konto istnieje.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Nazwa konta usługi Azure storage lub klucz magazynu jest niepoprawna.|  
|Nazwa konta usługi Azure storage "{0}" lub klucz magazynu dla nazwy konta jest nieprawidłowa.|  
  

## <a name="error-0065"></a>Błąd 0065  
 Wyjątek występuje, gdy nazwa obiektu blob platformy Azure została określona nieprawidłowo.  
  
 Ten błąd w usłudze Azure Machine Learning występuje, jeśli określono nieprawidłowe nazwy obiektów blob platformy Azure.  Zostanie wyświetlony błąd, jeśli:  
  
-   Nie można odnaleźć obiektu blob w określonym kontenerze.  
  
 <!---   The fully qualified name of the blob specified for output in one of the [Learning with Counts](data-transformation-learning-with-counts.md) modules is greater than 512 characters.  -->
  
-   Tylko kontener został określony jako źródło w [importu danych](import-data.md) żądania podczas formatu programu Excel lub CSV z kodowaniem; łączenia zawartość wszystkich obiektów blob w kontenerze jest niedozwolone w tych formatach.  
  
-   Identyfikator URI sygnatury dostępu Współdzielonego nie zawiera nazwy obiektu blob prawidłowe.  
  
**Rozwiązanie:** Ponownie moduł zostanie zgłoszony wyjątek. Sprawdź, czy określony obiekt blob istnieje w kontenerze na koncie magazynu i że uprawnienia umożliwiają wyświetlanie obiektu blob. Sprawdź, czy dane wejściowe formularza **containername/filename** w przypadku programu Excel lub CSV z kodowaniem formatów. Sprawdź, czy identyfikator URI sygnatury dostępu Współdzielonego zawiera nazwę prawidłowy obiekt blob.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Usługa Azure storage blob jest nieprawidłowy.|  
|Nazwa obiektu blob magazynu platformy Azure "{0}" jest nieprawidłowa|  
  

## <a name="error-0066"></a>Błąd 0066  
 Wyjątek występuje, jeśli zasób nie można przekazać do obiektu Blob platformy Azure.  
  
 Ten błąd w usłudze Azure Machine Learning występuje, jeśli zasób nie można przekazać do obiektu Blob platformy Azure.  <!--You will receive this message if [Train Vowpal Wabbit 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) encounters an error attempting to save either the model or the hash created when training the model.--> Oba są zapisywane do tego samego konta magazynu platformy Azure jako konto zawierające plik wejściowy.  
  
**Rozwiązanie:** Ponownie moduł. Sprawdź, czy nazwa konta platformy Azure, klucza magazynu i kontener są poprawne i czy konto ma uprawnienia do zapisu do kontenera.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Nie można przekazać zasób do usługi Azure storage.|  
|Plik "{0}" nie można przekazać do usługi Azure storage jako {1}.|  
  

## <a name="error-0067"></a>Błąd 0067  
 Wyjątek występuje, gdy zestaw danych zawiera inną liczbę kolumn niż oczekiwano.  
  
 Ten błąd w usłudze Azure Machine Learning występuje, jeśli zestaw ma różną liczbę kolumn niż oczekiwano.  Zostanie wyświetlony ten błąd, gdy liczba kolumn w zestawie danych różnią się od liczby kolumn, które moduł oczekuje, że podczas wykonywania.  
  
**Rozwiązanie:** Modyfikowanie parametrów lub wejściowego zestawu danych.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Nieoczekiwana liczba kolumn w tabeli danych.|  
|Oczekiwano "{0}"ale znaleziono kolumny"{1}" kolumn zamiast tego.|  
  

## <a name="error-0068"></a>Błąd 0068  
 Wystąpi wyjątek, jeśli określony skrypt programu Hive jest nieprawidłowy.  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning występuje, gdy istnieją błędy składniowe skryptu programu Hive SQL lub jeśli Hive interpreter napotka błąd podczas wykonywania kwerendy lub skryptu.  
  
**Rozwiązanie:**

Komunikat o błędzie z programu Hive jest zwykle raportowane w dzienniku błędów, aby można podjąć odpowiednie działania powiązane z określonym błędem. 

+ Otwórz moduł i sprawdź zapytania dla błędów.  
+ Sprawdź, czy zapytanie działa prawidłowo poza usługi Azure Machine Learning na, logując się do konsoli programu Hive, klastra usługi Hadoop i uruchamiając zapytania.  
+ Spróbuj umieścić komentarzy w skrypcie programu Hive w osobnym wierszu zamiast mieszanie instrukcji wykonywalnych i komentarzy w jednym wierszu.  

### <a name="resources"></a>Zasoby

Zobacz następujące artykuły, aby uzyskać pomoc przy użyciu zapytań programu Hive dla usługi machine learning:

+ [Tworzenie tabel programu Hive i ładowanie danych z usługi Azure Blob Storage](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-move-hive-tables)
+ [Eksplorowanie danych w tabelach za pomocą zapytań Hive](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-explore-data-hive-tables)
+ [Tworzenie funkcji dla danych w klastrze usługi Hadoop przy użyciu zapytań Hive](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-create-features-hive)
+ [Gałąź dla SQL — Ściągawka użytkowników (PDF)](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf)

  
|Komunikaty o wyjątkach|  
|------------------------|  
|Skrypt hive jest nieprawidłowy.|  
|Wykonanie skryptu technologii hive {0} jest nieprawidłowy.|  
  

## <a name="error-0069"></a>Błąd 0069  
 Wystąpi wyjątek, jeśli określony skrypt SQL jest nieprawidłowy.  
  
 Ten błąd w usłudze Azure Machine Learning występuje, jeśli określony skrypt SQL ma składniowych, lub jeśli określono kolumny lub tabeli w skryptu jest nieprawidłowa. 
 
 Zostanie wyświetlony ten błąd, jeśli aparat SQL napotka jakiś błąd podczas wykonywania kwerendy lub skryptu. Komunikat o błędzie SQL jest zwykle raportowane w dzienniku błędów, aby można podjąć odpowiednie działania powiązane z określonym błędem.  
  
**Rozwiązanie:** Ponownie moduł i sprawdź zapytania SQL dla błędów.  
  
 Sprawdź, czy zapytanie działa prawidłowo poza Azure ML na, logując się do serwera bazy danych bezpośrednio i uruchamiając zapytania.  
  
 W przypadku wiadomości SQL generowane zgłosił wyjątek modułu podjąć działania, w oparciu o zgłoszonego błędu. Komunikaty o błędach obejmują czasami dokładne wskazówki dotyczące prawdopodobnie błąd:
+ *Brak kolumny lub Brak bazy danych*, wskazujący, że może być wpisana nazwa kolumny, która jest nieprawidłowy. Jeśli masz pewność, że nazwa kolumny jest poprawna, spróbuj użyć nawiasów ani znaków cudzysłowu należy ująć identyfikator kolumny.
+ *Błąd logiczny SQL niemal \<SQL — słowo kluczowe\>* , wskazujący, że może być błąd składniowy przed określonym słowem kluczowym

  
|Komunikaty o wyjątkach|  
|------------------------|  
|Skrypt SQL jest niepoprawna.|  
|Zapytanie SQL "{0}" jest nieprawidłowy.|  
|Zapytanie SQL "{0}" jest nieprawidłowy: {1}|  
  

## <a name="error-0070"></a>Błąd 0070  
 Wyjątek występuje podczas próby uzyskania dostępu do nieistniejącej tabeli platformy Azure.  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning występuje, gdy użytkownik podejmie próbę uzyskania dostępu do nieistniejącej tabeli platformy Azure. Zostanie wyświetlony ten błąd, jeśli określisz tabelę w usłudze Azure storage, która nie istnieje podczas odczytu / zapisu do usługi Azure Table Storage. Może to nastąpić, jeśli wpisujesz nazwę żądanej tabeli lub nazwa docelowego jest niezgodny z typem magazynu. Na przykład przeznaczony do odczytu z tabeli, ale wprowadzona nazwa obiektu blob w zamian.  
  
**Rozwiązanie:** Ponownie moduł, aby sprawdzić, czy nazwa tabeli jest poprawna.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Tabela platformy Azure nie istnieje.|  
|Usługa Azure table "{0}" nie istnieje.|  
  
## <a name="error-0071"></a>Błąd 0071  
 Wyjątek występuje, gdy pod warunkiem, że poświadczenia są nieprawidłowe.  
  
 Ten błąd w usłudze Azure Machine Learning występuje, jeśli podane poświadczenia są niepoprawne.  
  
 Ten błąd może również występować, jeśli moduł nie może połączyć się z klastrem usługi HDInsight.  
  
**Rozwiązanie:** Przejrzyj dane wejściowe do modułu, a następnie sprawdź nazwę konta i hasło.  
  
 Sprawdź, czy następujące problemy, które mogą spowodować wystąpienie błędu:  
  
-   Schemat zestawu danych nie jest zgodny ze schematem, DataTable docelowego.  
  
-   Nazwy kolumn to brakujące lub błędnie  
  
-   Dane będą zapisywane do tabeli, która zawiera nazwy kolumn ze niedozwolone znaki. Zazwyczaj można umieścić takie nazwy kolumn w nawiasach kwadratowych, ale jeśli to nie pomoże, edytować nazwy kolumn do użycia tylko z liter i znaki podkreślenia (_)  
  
-   Ciągi, które chcesz zapisać zawierają znaki pojedynczego cudzysłowu  
  
 Jeśli próbujesz nawiązać połączenie z klastra usługi HDInsight, sprawdź, czy klaster docelowy jest dostępny za pomocą podanych poświadczeń.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Nieprawidłowe poświadczenia są przekazywane.|  
|Niepoprawna nazwa użytkownika "{0}" lub hasło są przekazywane|  
  

## <a name="error-0072"></a>Błąd 0072  
 Wyjątek występuje w przypadku przekroczenia limitu czasu połączenia.  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning występuje, gdy upłynie limit czasu połączenia. Zostanie wyświetlony ten błąd, czy obecnie nie istnieją problemy z łącznością ze źródła danych lub miejsca docelowego, takich jak wolne połączenie z Internetem, czy zestaw danych jest duży i/lub zapytanie SQL do odczytywania danych wykonuje skomplikowane przetwarzanie.  
  
**Rozwiązanie:** Ustal, czy ma obecnie problemów z wolnego połączenia z usługi Azure storage lub Internetu.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Przekroczono limit czasu połączenia.|  
  

## <a name="error-0073"></a>Błąd 0073  
 Jeśli wystąpi błąd podczas konwertowania kolumny do innego typu, wystąpi wyjątek.  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning występuje, gdy nie jest możliwe do przekonwertowania na kolumnę do innego typu.  Zostanie wyświetlony ten błąd, jeśli moduł wymaga określonego typu, a nie jest możliwe przekonwertować kolumny na nowy typ.  
  
**Rozwiązanie:** Zmodyfikuj wejściowego zestawu danych, tak, aby kolumny mogą być konwertowane na podstawie na wyjątek wewnętrzny.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Nie można przekonwertować kolumny.|  
|Nie można przekonwertować kolumny na {0}.|  
  

## <a name="error-0074"></a>Błąd 0074  
 Wystąpił wyjątek podczas [edytować metadane](edit-metadata.md) podejmuje próbę przekonwertowania kolumn rozrzedzonych do kategorii.  
  
 Ten błąd w usłudze Azure Machine Learning występuje podczas [edytować metadane](edit-metadata.md) podejmuje próbę przekonwertowania kolumn rozrzedzonych do kategorii.  Zostanie wyświetlony ten błąd, podczas próby skonwertowania kolumn rozrzedzonych do kategorii z **wprowadzić podzielonych na kategorie** opcji.  Usługi Azure machine Learning nie obsługuje rzadkimi tablicami podzielonych na kategorie, więc moduł zakończy się niepowodzeniem.  
  
 <!--**Resolution:**
 Make the column dense by using [Convert to Dataset](convert-to-dataset.md) first or do not convert the column to categorical.  -->
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Kolumny rozrzedzonej nie można przekonwertować na podzielone.|  
  

## <a name="error-0075"></a>Błąd 0075  
Wyjątek występuje, gdy jest używana nieprawidłowa funkcja pakowania, podczas quantizing zestawu danych.  
  
Ten błąd w usłudze Azure Machine Learning występuje, gdy próbujesz bin danych za pomocą nieobsługiwanej metody lub kombinacji parametrów są nieprawidłowe.  
  
**Rozwiązanie:**

Obsługę błędów dla tego zdarzenia została wprowadzona w starszej wersji usługi Azure Machine Learning dozwolone większym stopniu z kwantowania metody. Obecnie wszystkie metody pakowania są oparte na odpowiednią pozycję z listy rozwijanej, więc z technicznego punktu widzenia nie powinno być możliwe do tego błędu.

 <!--If you get this error when using the [Group Data into Bins](group-data-into-bins.md) module, consider reporting the issue in the [Azure Machine Learning forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=MachineLearning), providing the data types, parameter settings, and the exact error message.  -->
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Nieprawidłowa funkcja pakowania używana.|  
  

## <a name="error-0077"></a>Błąd 0077  
 Wyjątek występuje, gdy tryb przekazywane zapisu pliku nieznanego obiektu blob.  
  
 Ten błąd w usłudze Azure Machine Learning występuje, jeśli nieprawidłowy argument jest przekazywany w specyfikacjach dla obiektu blob plik docelowy lub źródłowy.  
  
**Rozwiązanie:** W prawie wszystkie moduły, które importowanie lub eksportowanie danych do i z usługi Azure blob storage wartości parametrów kontrolowanie tryb zapisu są przypisywane przy użyciu listy rozwijanej; dlatego nie jest możliwe przekazać wartość jest nieprawidłowa i nie powinien pojawić się ten błąd. Ten błąd będzie przestarzały w nowszej wersji.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Nieobsługiwana blob zapisuje trybu.|  
|Nieobsługiwana blob zapisuje tryb: {0}.|  
  

## <a name="error-0078"></a>Błąd 0078  
 Wyjątek występuje kiedy opcja HTTP dla [importu danych](import-data.md) odbiera 3xx kod stanu wskazujący przekierowania.  
  
 Występuje ten błąd wystąpił w usłudze Azure Machine Learning po opcji HTTP dla [importu danych](import-data.md) odbiera 3xx (301, 302, 304, itp.) kod stanu wskazujący przekierowania. Zostanie wyświetlony ten błąd, jeśli użytkownik podejmie próbę połączenia ze źródłem HTTP, który przekierowuje przeglądarki do innej strony. Zabezpieczeń ze względu na przekierowywanie witryn sieci Web nie są dozwolone jako źródła danych dla usługi Azure Machine Learning.  
  
**Rozwiązanie:** Jeśli witryna sieci Web jest zaufane witryny sieci Web, wprowadź bezpośrednio adresu URL.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Przekierowywanie HTTP nie jest dozwolone|  
  

## <a name="error-0079"></a>Błąd 0079  
 Wyjątek występuje, gdy nazwa kontenera usługi Azure storage została określona nieprawidłowo.  
  
 Ten błąd w usłudze Azure Machine Learning występuje, jeśli nazwa kontenera usługi Azure storage została określona nieprawidłowo. Zostanie wyświetlony ten błąd, jeśli nie podano kontenera i przy użyciu nazwy obiektu blob (plik) **ścieżki do obiektu blob zaczynający się od kontenera** opcji podczas zapisywania do magazynu obiektów Blob platformy Azure.  
  
**Rozwiązanie:** Kontroluj [Eksport danych](export-data.md) modułu i sprawdź, czy podana ścieżka do obiektu blob zawiera zarówno kontenera, jak i nazwę pliku w formacie **kontenera/filename**.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Nazwa kontenera usługi Azure storage jest niepoprawna.|  
|Nazwa kontenera usługi Azure storage "{0}" jest nieprawidłowy; oczekiwano nazwę kontenera w formacie kontenera/obiekt blob.|  
  

## <a name="error-0080"></a>Błąd 0080  
 Wyjątek występuje, gdy kolumna z wartościami wszystkie brakujące nie jest dozwolona przez moduł.  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning jest generowany, gdy co najmniej jedna z kolumn, używane przez moduł zawiera wszystkie brakujące wartości. Na przykład jeśli moduł jest przetwarzanie zagregowanych danych statystycznych dla każdej kolumny, nie może działać na kolumnę zawierającą żadnych danych. W takich przypadkach modułu wykonywanie zostało zatrzymane z tym wyjątkiem.  
  
**Rozwiązanie:** Odwiedzaj wejściowego zestawu danych, a następnie usuń wszystkie kolumny, które zawierają wszystkie brakujące wartości.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Kolumny z wartościami wszystkie brakujące nie są dozwolone.|  
|Kolumna {0} zawiera wszystkie wartości Brak.|  
  

## <a name="error-0081"></a>Błąd 0081  
 W module UPW wystąpi wyjątek, jeśli liczba wymiarów, aby zmniejszyć jest równa liczbie kolumn funkcji w wejściowego zestawu danych zawierającego co najmniej jedną kolumnę rozrzedzoną funkcji.  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning jest generowany, jeśli są spełnione następujące warunki: (wejściowy zestaw danych zawiera co najmniej jedną kolumnę rozrzedzoną oraz (b) końcowej liczby wymiarów wymagane jest taka sama jak liczba wymiarów w danych wejściowych.  
  
**Rozwiązanie:** Rozważ zmniejszenie liczby wymiarów w danych wyjściowych, aby być mniejsza niż liczba wymiarów w danych wejściowych. Jest to typowa sytuacja dla aplikacji UPW.   <!--For more information, see [Principal Component Analysis](principal-component-analysis.md).  -->
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Dla zestawu danych zawierających kolumny rozrzedzone funkcji liczbę wymiarów, aby zmniejszyć powinna być mniejsza niż liczba kolumn w funkcji.|  
 

## <a name="error-0082"></a>Błąd 0082  
 Wyjątek występuje, gdy model nie może być zdeserializowany pomyślnie.  
  
 Ten błąd w usłudze Azure Machine Learning występuje, gdy model uczenia maszynowego zapisane lub nie można załadować przekształcenia przy użyciu nowszej wersji środowiska uruchomieniowego usługi Azure Machine Learning, w wyniku istotnej zmiany.  
  
**Rozwiązanie:** Eksperymentu szkolenia, wytworzonego modelu lub przekształcania musi być ponownie i modelu, lub musi być resaved transformacji.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Nie można zdeserializować modelu, ponieważ prawdopodobnie jest serializowana za pomocą starszego formatu serializacji. Ponowne szkolenie i ponownie zapisać modelu.|  
  

## <a name="error-0083"></a>Błąd 0083  
 Jeśli zestaw danych używany na potrzeby szkolenia nie można użyć dla konkretnego typu uczeń wystąpi wyjątek.  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning jest generowany, gdy zestaw danych jest niezgodny z uczeń czasu zakończenia uczenia. Na przykład zestaw danych może zawierać co najmniej jedną wartość Brak w każdym wierszu, a w rezultacie, cały zestaw danych będzie pominięte podczas szkolenia. W innych przypadkach niektóre algorytmów uczenia maszynowego, takich jak wykrywanie anomalii nie powinna mieć etykiety, aby być obecny i może zgłosić wyjątek, jeśli etykiety są obecne w zestawie danych.  
  
**Rozwiązanie:** Zajrzyj do dokumentacji uczeń używany do sprawdzenia wymagań dla wejściowego zestawu danych. Sprawdź kolumny, aby wyświetlić wszystkich wymaganych kolumn są obecne.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Zestaw danych umożliwiający szkolenie jest nieprawidłowy.|  
|{0} zawiera nieprawidłowe dane na potrzeby szkolenia.|  
|{0} zawiera nieprawidłowe dane na potrzeby szkolenia. Typ uczeń: {1}.|  
  

## <a name="error-0084"></a>Błąd 0084  
 Wyjątek występuje, gdy wyniki z skrypt języka R są oceniane. Jest to obecnie obsługiwane.  
  
 Ten błąd w usłudze Azure Machine Learning występuje, Jeśli spróbujesz użyć jednego z modułów do oceniania modelu z danych wyjściowych skryptu języka R, który zawiera wyniki.  
  
**Rozwiązanie:**
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Ocena wyniki generowane przez języka R jest obecnie obsługiwana.|  
  

## <a name="error-0085"></a>Błąd 0085  
 Wyjątek występuje, gdy wyznaczanie wartości skryptu zakończy się niepowodzeniem z powodu błędu.  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning występuje, gdy używasz niestandardowego skryptu, który zawiera błędy składni.  
  
**Rozwiązanie:** Przejrzyj kod w edytorze zewnętrznym i sprawdź błędy.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Wystąpił błąd podczas obliczania wartości skryptu.|  
|Wystąpił następujący błąd podczas obliczania skryptów Wyświetlanie dziennika danych wyjściowych, aby uzyskać więcej informacji:---początek komunikat o błędzie z {0} interpreter--- {1} ---koniec komunikat o błędzie z {0} interpreter--- ------|  
  

## <a name="error-0086"></a>Błąd 0086  
 Wyjątek występuje, gdy przekształcenie zliczania jest nieprawidłowe.  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning występuje, gdy wybierzesz przekształcenie na podstawie tabeli liczba, ale wybrany przekształcenie jest niezgodny z bieżącymi danymi lub za pomocą nowej tabeli liczba.  
  
**Rozwiązanie:** Ten moduł umożliwia zapisywanie liczby i reguł, które tworzą przekształcania w dwóch różnych formatach. Jeśli scalasz liczba tabel, sprawdź, czy obu tabel, które chcesz scalić używać tego samego formatu.  
  
Ogólnie rzecz biorąc przekształcenie na podstawie liczby będzie stosowany tylko do zestawów danych, które mają ten sam schemat jako zestaw pierwotnie utworzono transformacji.  
  
 <!-- For general information, see [Learning with Counts](data-transformation-learning-with-counts.md). For requirements specific to creating and merging count-based features, see these topics:  
  
-   [Merge Count Transform](merge-count-transform.md)  
  
-   [Import Count Table](import-count-table.md)  
  
-   [Modify Count Table Parameters](modify-count-table-parameters.md)  
  -->
|Komunikaty o wyjątkach|  
|------------------------|  
|Określono nieprawidłowy przekształcenie zliczania.|  
|Zliczania przekształcenia na port wejściowy "{0}" jest nieprawidłowy.|  
|Zliczania przekształcenia na port wejściowy "{0}"nie można scalić z zliczania przekształcenia na port wejściowy"{1}". Zaznacz, aby zweryfikować metadanych używany do zliczania dopasowań.|  
  

## <a name="error-0087"></a>Błąd 0087  
 Wyjątek występuje, gdy określono typu tabeli Nieprawidłowa liczba do uczenia liczba modułów.  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning występuje, gdy próbujesz zaimportować istniejącej tabeli liczby, ale tabela jest niezgodny z bieżącymi danymi lub za pomocą nowej tabeli count.  
  
**Rozwiązanie:** Istnieją różne formaty zapisywania liczby i reguł, które tworzą transformacji. Jeśli scalasz liczba tabel, sprawdź, czy obaj za pomocą tego samego formatu.  
  
 Ogólnie rzecz biorąc przekształcenie na podstawie liczby będzie stosowany tylko do zestawów danych, które mają ten sam schemat jako zestaw pierwotnie utworzono transformacji.  
  
  <!--For general information, see [Learning with Counts](data-transformation-learning-with-counts.md). -->
  

## <a name="error-0088"></a>Błąd 0088  
 Wyjątek występuje, gdy zliczanie nieprawidłowy typ został określony na potrzeby uczenia liczba modułów.  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning występuje, gdy spróbujesz przy użyciu innej metody zliczania, niż jest obsługiwana w przypadku cechowania na podstawie liczby.  
  
**Rozwiązanie:** Ogólnie rzecz biorąc metod zliczania są wybierane z listy rozwijanej, aby nie powinien zostać wyświetlony ten błąd.  
  
  <!--For general information, see [Learning with Counts](data-transformation-learning-with-counts.md). For requirements specific to creating and merging count-based features, see these topics:  
  
-   [Merge Count Transform](merge-count-transform.md)  
  
-   [Import Count Table](import-count-table.md)  
  
-   [Modify Count Table Parameters](modify-count-table-parameters.md)  
  -->
|Komunikaty o wyjątkach|  
|------------------------|  
|Określono nieprawidłowy typ zliczania.|  
|Określony typ inwentaryzacji "{0}" nie jest prawidłowym typem zliczania.|  
  

## <a name="error-0089"></a>Błąd 0089  
 Wyjątek występuje, gdy określona liczba klas jest mniejsza niż rzeczywista liczba klasy w zestawie danych używany do zliczania.  
  
 Ten błąd w usłudze Azure Machine Learning występuje, gdy tworzysz tabelę liczba kolumn etykieta zawiera inną liczbę klas niż podana w parametrach modułu.  
  
**Rozwiązanie:** Sprawdź swój zestaw danych i Dowiedz się dokładnie tak jak wiele odrębne wartości (możliwe klasy) istnieją w kolumnie Etykieta. Podczas tworzenia tabeli liczba Podaj co najmniej tę liczbę klas.  
  
 Tabela liczba nie może automatycznie określić liczbę dostępnych klas.  
  
 Podczas tworzenia tabeli liczby, nie można określić 0 lub numer jest mniejsza niż rzeczywista liczba klas w etykiecie kolumny.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Liczba klas jest niepoprawna. Upewnij się, że liczba klas, które określisz w okienku parametrów jest większa lub równa liczbie klas w kolumnie Etykieta.|  
|Liczba określonych klas jest "{0}", która nie jest większa niż wartość etykiety"{1}" w zestawie danych, używane do zliczenia. Upewnij się, że liczba klas, które określisz w okienku parametrów jest większa lub równa liczbie klas w kolumnie Etykieta.|  
  

## <a name="error-0090"></a>Błąd 0090  
 Wyjątek występuje w przypadku tworzenia tabeli programu Hive nie powiodło się.  
  
 Ten błąd w usłudze Azure Machine Learning występuje, gdy używasz [Eksport danych](export-data.md) lub nie można utworzyć inną opcję, aby zapisać dane w klastrze usługi HDInsight i określonej tabeli programu Hive.  
  
**Rozwiązanie:** Sprawdź nazwę konta usługi Azure storage skojarzonym z klastrem i upewnij się, że używasz tego samego konta, w oknie właściwości modułu.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Nie można utworzyć tabelę programu Hive. W przypadku klastra HDInsight upewnij się, nazwa konta magazynu platformy Azure, które są skojarzone z klastrem, jest taka sama jak co to jest przekazywana w parametrze modułu.|  
|W tabeli programu Hive "{0}" nie mógł zostać utworzony. W przypadku klastra HDInsight upewnij się, nazwa konta magazynu platformy Azure, które są skojarzone z klastrem, jest taka sama jak co to jest przekazywana w parametrze modułu.|  
|W tabeli programu Hive "{0}" nie mógł zostać utworzony. W przypadku klastra HDInsight upewnij się, nazwa konta magazynu platformy Azure, które są skojarzone z klastrem "{1}".|  
 

## <a name="error-0100"></a>Błąd 0100  
 Wyjątek występuje, gdy określono nieobsługiwany język dla niestandardowego modułu.  
  
 Ten błąd w usłudze Azure Machine Learning występuje podczas tworzenia niestandardowego modułu i właściwości name obiektu **języka** elementu w pliku definicji xml niestandardowego modułu ma nieprawidłową wartość. Obecnie jedyne prawidłowe wartości dla tej właściwości jest `R`. Na przykład:  
  
 `<Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />`  
  
**Rozwiązanie:** Upewnij się, że właściwości name obiektu **języka** element w pliku definicji xml niestandardowego modułu jest ustawiony na wartość `R`. Zapisz plik pakietu zip niestandardowego modułu aktualizacji i spróbuj ponownie dodać niestandardowego modułu.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Podany język nieobsługiwany niestandardowego modułu|  
  

## <a name="error-0101"></a>Błąd 0101  
 Wszystkie identyfikatory portu i parametru musi być unikatowa.  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning występuje, gdy portów lub parametry są przypisywane ta sama wartość Identyfikatora w pliku definicji XML niestandardowego modułu.  
  
**Rozwiązanie:** Sprawdź, czy wartości Identyfikatora w ramach wszystkich portów i parametry są unikatowe. Zapisz plik xml, a następnie zaktualizować pakiet zip niestandardowego modułu i spróbuj ponownie dodać niestandardowego modułu.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Wszystkich portów i parametr identyfikatorów modułu muszą być unikatowe|  
|Moduł "{0}" ma zduplikowane portu/argument identyfikatorów. Wszystkie identyfikatory portu/argumentu musi być unikatowa dla modułu.|  
  

## <a name="error-0102"></a>Błąd 0102  
 Element zgłaszany, gdy nie można wyodrębnić pliku ZIP.  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning występuje przy importowany pakiet skompresowany z rozszerzeniem .zip, ale pakiet nie jest plikiem zip lub plik nie używa formatu zip obsługiwane.  
  
**Rozwiązanie:** Upewnij się, że wybrany plik jest plikiem zip prawidłowe i czy był skompresowany za pomocą jednego z algorytmów obsługiwanych kompresji.  
  
 Jeśli ten błąd podczas importowania zestawów danych w formacie skompresowany, sprawdź, czy wszystkie pliki zawarte użyj jednej z obsługiwanych formatów plików i są w formacie Unicode.  <!--For more information, see [Unpack Zipped Datasets](unpack-zipped-datasets.md).  -->
  
 Spróbuj ponownie dodać pliki do nowego skompresowanego folderu zip, a następnie spróbuj ponownie dodać niestandardowego modułu.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Biorąc pod uwagę POCZTOWY plik nie jest w niepoprawnym formacie.|  


## <a name="error-0103"></a>Błąd 0103  
 Zgłaszany, gdy plik ZIP zawiera wszystkie pliki XML  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning występuje, gdy niestandardowego modułu spakowany pakiet nie zawiera żadnych plików definicji (.xml) modułu. Te pliki muszą znajdować się w katalogu głównym pakietu zip (na przykład w podfolderze.)  
  
**Rozwiązanie:** Sprawdź, czy co najmniej jeden plik definicji modułu xml w folderze głównym pliku zip pakietu, wyodrębniając go do folderu tymczasowego na dysku. Pliki xml należy bezpośrednio w folderze, do którego wyodrębniono pakiet zip. Upewnij się, że podczas tworzenia pakietu zip, który nie zaznaczaj folderu zawierającego pliki xml, aby zip, ponieważ spowoduje to utworzenie podfolderem w ramach pakietu zip z taką samą nazwę jak folder, który wybrano zip.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Biorąc pod uwagę POCZTOWY plik nie zawiera żadnych pliki definicji modułu (pliki .xml)|  


## <a name="error-0104"></a>Błąd 0104  
 Zgłaszany, gdy skrypt, którego nie można zlokalizować odwołuje się plik definicji modułu  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning jest zgłaszany, gdy plik definicji xml niestandardowego modułu odwołuje się do pliku skryptu w **języka** element, który nie istnieje w pliku zip pakietu. Ścieżka pliku skryptu jest zdefiniowany w **sourceFile** właściwość **języka** elementu. Ścieżka do pliku źródłowego jest względem katalogu głównego pakietu zip (tej samej lokalizacji co pliki definicji xml modułu). Jeśli plik skryptu znajduje się w folderze sub, należy określić ścieżkę względną do pliku skryptu. Na przykład jeśli wszystkie skrypty zostały zapisane w **myScripts** folder wewnątrz pakietu zip **języka** musi dodać tę ścieżkę do elementu **sourceFile** właściwość jako poniżej. Na przykład:  
  
 `<Language name="R" sourceFile="myScripts/CustomAddRows.R" entryPoint="CustomAddRows" />`  
  
**Rozwiązanie:** Upewnij się, że wartość **sourceFile** właściwość **języka** elementu niestandardowego modułu definicji xml jest poprawna i że plik źródłowy istnieje poprawną ścieżkę względną do pliku zip pakietu.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Przywoływany plik skryptu języka R nie istnieje.|  
|Przywoływany plik skryptu języka R "{0}" nie można odnaleźć. Upewnij się, że ścieżka względna do pliku poprawne z lokalizacji definicje.|  


## <a name="error-0105"></a>Błąd 0105  
 Ten błąd jest wyświetlany, gdy plik definicji modułu zawiera nieobsługiwany typ parametru  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning jest generowany podczas tworzenia niestandardowego modułu definicji xml i typ parametr lub argument w definicji jest niezgodny z obsługiwanego typu.  
  
**Rozwiązanie:** Upewnij się, że właściwość type dowolnego **Arg** elementu w pliku definicji xml niestandardowego modułu jest obsługiwanym typem.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Nieobsługiwany typ parametru.|  
|Nieobsługiwany typ parametru "{0}" określony.|  


## <a name="error-0106"></a>Błąd 0106  
 Zgłaszany, gdy plik definicji modułu definiuje nieobsługiwany typ danych wejściowych  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning jest generowany, gdy typ portu wejściowego w przypadku niestandardowego modułu definicji XML jest niezgodny z obsługiwanego typu.  
  
**Rozwiązanie:** Upewnij się, że właściwość type elementu danych wejściowych w pliku definicji XML niestandardowego modułu jest obsługiwanym typem.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Nieobsługiwany typ danych wejściowych.|  
|Nieobsługiwany typ danych wejściowych "{0}" określony.|  


## <a name="error-0107"></a>Błąd 0107  
 Zgłaszany, gdy plik definicji modułu definiuje typ nieobsługiwanych danych wyjściowych  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning jest generowana, gdy typ portu wyjściowego w definicji xml niestandardowego modułu jest niezgodny z obsługiwanego typu.  
  
**Rozwiązanie:** Upewnij się, że właściwość type elementu danych wyjściowych w pliku definicji xml niestandardowego modułu jest obsługiwanym typem.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Typ danych wyjściowych nieobsługiwany.|  
|Nieobsługiwany typ danych wyjściowych "{0}" określony.|  


## <a name="error-0108"></a>Błąd 0108  
 Zgłaszany, gdy plik definicji modułu definiuje więcej portów wejściowych lub wyjściowych nie są obsługiwane  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning jest generowany, gdy za dużo portów wejściowych lub wyjściowych są zdefiniowane w definicji xml niestandardowego modułu.  
  
**Rozwiązanie:** Gwarantuje, że maksymalną liczbę portów wejściowych i wyjściowych, określone w definicji xml niestandardowego modułu nie przekracza maksymalną liczbę obsługiwanych portów.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Przekroczono liczbę obsługiwanych portów wejściowych lub wyjściowych.|  
|Przekroczona liczba obsługiwana "{0}" portów. Maksymalna dozwolona liczba "{0}"portów jest"{1}".| 

## <a name="error-0109"></a>Błąd 0109  
 Zgłaszany, gdy plik definicji modułu definiuje niepoprawnie selektor kolumn  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning jest generowany, gdy składnia dla argumentu selektora kolumny zawiera błąd w definicji xml niestandardowego modułu.  
  
**Rozwiązanie:** Ten błąd jest generowany, gdy składnia dla argumentu selektora kolumny zawiera błąd w definicji xml niestandardowego modułu.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Nieobsługiwana składnia selektora kolumn.|  
  

## <a name="error-0110"></a>Błąd 0110  
 Zgłaszany, gdy plik definicji modułu definiuje selektor kolumn, który odwołuje się do Identyfikatora portu wejściowego nieistniejącej  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning jest generowany podczas *identyfikator portu* właściwość w ramach elementu właściwości argumentu typu ColumnPicker jest niezgodna z wartości Identyfikatora portu wejściowego.  
  
**Rozwiązanie:** Upewnij się, że właściwość identyfikator portu odpowiada wartości Identyfikatora portu wejściowego określone w definicji xml niestandardowego modułu.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Selektor kolumn odwołuje się do identyfikatora portu wejściowego nieistniejącej|  
|Selektor kolumn odwołuje się do Identyfikatora portu wejściowego nieistniejącej "{0}".|  
  

## <a name="error-0111"></a>Błąd 0111  
 Zgłaszany, gdy plik definicji modułu definiuje nieprawidłową właściwość  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning jest generowany, gdy nieprawidłową właściwość zostanie przypisany do elementu w niestandardowego modułu definicji XML.  
  
**Rozwiązanie:** Upewnij się, że właściwość jest obsługiwana przez element niestandardowego modułu.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Definicja właściwości jest nieprawidłowa.|  
|Definicja właściwości "{0}" jest nieprawidłowy.|  
  

## <a name="error-0112"></a>Błąd 0112  
 Zgłaszany, gdy nie można przeanalizować pliku definicji modułu  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning jest generowany po wystąpieniu błędu w formacie xml, który uniemożliwia niestandardowego modułu definicji XML z przeanalizowany jako prawidłowy plik XML.  
  
**Rozwiązanie:** Upewnij się, że każdy element jest otwarty i poprawnie zamknięty. Upewnij się, że nie ma żadnych błędów w formacie XML.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Nie można przeanalizować plik definicji modułu.|  
|Nie można przeanalizować plik definicji modułu "{0}".|  
  

## <a name="error-0113"></a>Błąd 0113  
 Element zgłaszany, gdy plik definicji modułu zawiera błędy.  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning jest generowany, gdy można przeanalizować niestandardowego modułu pliku definicji XML, ale zawiera błędy, takie jak definicji elementów, które nie są obsługiwane przez moduły niestandardowe.  
  
**Rozwiązanie:** Upewnij się, że plik definicji modułu niestandardowy definiuje elementów i właściwości, które są obsługiwane przez moduły niestandardowe.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Plik definicji modułu zawiera błędy.|  
|Plik definicji modułu "{0}" zawiera błędy.|  
|Plik definicji modułu "{0}" zawiera błędy. {1}|  
  

## <a name="error-0114"></a>Błąd 0114  
 Element zgłaszany, podczas tworzenia niestandardowego modułu zakończy się niepowodzeniem.  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning jest generowany, gdy kompilacja niestandardowego modułu nie powiodło się. Ten problem wystąpi, jeśli jeden lub więcej błędów niestandardowych związane z modułu zostaną napotkane podczas dodawania niestandardowego modułu. Dodatkowe błędy są zgłaszane w ramach tego komunikatu o błędzie.  
  
**Rozwiązanie:** Rozwiązywanie błędów zgłaszanych w ramach ten komunikat o wyjątku.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Nie powiodło się utworzenie niestandardowego modułu.|  
|Niestandardowy moduł kompilacji zakończyło się niepowodzeniem z błędami: {0}|  
  

## <a name="error-0115"></a>Błąd 0115  
 Element zgłaszany, gdy skrypt domyślny niestandardowego modułu ma nieobsługiwane rozszerzenie.  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning występuje, gdy możesz podać skryptu niestandardowego modułu, który używa nieznane rozszerzenie nazwy pliku.  
  
**Rozwiązanie:** Sprawdź format i nazwę pliku rozszerzenie pliku skryptu, objęte niestandardowego modułu.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Nieobsługiwane rozszerzenie skryptu domyślne.|  
|Nieobsługiwany plik rozszerzenie {0} domyślne skryptu.|  
  

## <a name="error-0121"></a>Błąd 0121  
 Zgłaszany, gdy SQL zapisuje kończy się niepowodzeniem, ponieważ tabela jest nie do zapisania  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning jest generowany, gdy używasz [Eksport danych](export-data.md) nie można zapisać modułu do zapisywania wyników w tabeli w bazie danych SQL, a tabela. Zwykle, zostanie wyświetlony ten błąd, jeśli [Eksport danych](export-data.md) modułu pomyślnie nawiąże połączenie z wystąpieniem programu SQL Server, ale następnie nie może zapisać zawartości zestawu danych usługi Azure ML do tabeli.  
  
**Rozwiązanie:**
 - Otwórz w okienku właściwości [Eksport danych](export-data.md) modułu i sprawdź, czy nazwy bazy danych i tabeli zostały poprawnie wprowadzone. 
 - Przegląd schematu zestawu danych, które eksportujesz lub upewnij się, że dane są zgodne z tabeli docelowej.
 - Sprawdź, czy zalogować SQL skojarzone z nazwą użytkownika i hasła ma uprawnienia do zapisu w tabeli. 
 - Jeśli wyjątek zawiera dodatkowe informacje o błędzie z programu SQL Server, należy użyć tych informacji do wprowadzania poprawek.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Połączono z serwerem, nie można zapisać do tabeli.|  
|Nie można zapisać do tabeli Sql: {0}|  


## <a name="error-0122"></a>Błąd 0122  
 Jeśli określono wiele kolumn wagi, a dozwolona jest tylko jedna wystąpi wyjątek.  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning występuje, gdy wybrano zbyt wiele kolumn, jako kolumny wagi.  
  
**Rozwiązanie:** Przejrzyj wejściowy zestaw danych i metadanych. Upewnij się, że tylko jedna kolumna zawiera wagi.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Określono wiele kolumn wagi.|  


## <a name="error-0123"></a>Błąd 0123  
 Wyjątek występuje, gdy kolumna wektorów została określona z kolumną etykiety.  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning występuje, jeśli używasz wektora jako kolumna etykiety.  
  
**Rozwiązanie:** Zmień format danych kolumny, jeśli to konieczne, lub wybierz inną kolumnę.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Kolumna wektorów jest określona jako kolumna etykiety.|  


## <a name="error-0124"></a>Błąd 0124  
 Wystąpi wyjątek, jeśli określono nieliczbową kolumny jako kolumny wagi.  
  
**Rozwiązanie:**
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Kolumna nieliczbowe jest określona jako kolumna wagi.|  
  


## <a name="error-0125"></a>Błąd 0125  
 Element zgłaszany, gdy schemat dla wielu zestawów danych nie jest zgodny.  
  
**Rozwiązanie:**
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Schemat zestawu danych jest niezgodny.|  


## <a name="error-0126"></a>Błąd 0126  
 Wyjątek występuje, gdy użytkownik określi domeny SQL, która nie jest obsługiwana w usłudze Azure ML.  
  
 Ten błąd jest generowany, gdy użytkownik określi domeny SQL, która nie jest obsługiwana w usłudze Azure Machine Learning. Zostanie wyświetlony ten błąd, jeśli próbujesz nawiązać połączenie z serwerem bazy danych w domenie, która nie jest umieszczona na białej liście. Obecnie są dozwolone domen SQL: ". database.windows.net",". cloudapp.net", lub ". database.secure.windows.net". Oznacza to serwer musi być z serwerem Azure SQL lub serwer, na maszynie wirtualnej na platformie Azure.  
  
**Rozwiązanie:** Ponownie moduł. Sprawdź, czy serwer bazy danych SQL należy do jednej z zaakceptowanych domen:  
  
-   .database.windows.net  
  
-   .cloudapp.net  
  
-   .database.secure.windows.net  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Nieobsługiwana domena SQL.|  
|Domeny SQL {0} nie jest obecnie obsługiwane w usłudze Azure ML|  
  

## <a name="error-0127"></a>Błąd 0127  
 Rozmiar piksela obrazu przekracza dozwolony limit  
  
 Ten błąd występuje podczas odczytu z zestawu danych obrazu do klasyfikowania obrazów i obrazów są większe, niż może obsłużyć modelu.  
  
 <!--**Resolution:**
 For more information about the image size and other requirements, see these topics:  
  
-   [Import Images](import-images.md)  
  
-   [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md)  -->
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Rozmiar piksela obrazu przekracza dozwolony limit.|  
|Rozmiar piksela w pliku obrazu "{0}" przekracza dozwolony limit: "{1}"|  


## <a name="error-0128"></a>Błąd 0128  
 Liczba prawdopodobieństwa warunkowe dla kategorii kolumn przekracza limit.  
  
**Rozwiązanie:**
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Liczba prawdopodobieństwa warunkowe dla kategorii kolumn przekracza limit.|  
|Liczba prawdopodobieństwa warunkowe dla kategorii kolumn przekracza limit. Kolumn{0}"i"{1}"są problematyczne pary.|  


## <a name="error-0129"></a>Błąd 0129  
 Liczba kolumn w zestawie danych przekracza dozwolony limit.  
  
**Rozwiązanie:**
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Liczba kolumn w zestawie danych przekracza dozwolony limit.|  
|Liczba kolumn w zestawie danych w "{0}"przekracza dozwolone".|  
|Liczba kolumn w zestawie danych w "{0}"przekracza dozwolony limit wynoszący"{1}". "|  
|Liczba kolumn w zestawie danych w "{0}"przekracza dozwolony"{1}"limit"{2}". "|  
## <a name="error-0130"></a>Błąd 0130  
 Wyjątek występuje, gdy wszystkie wiersze w zestawie danych szkoleniowych zawierają brakujące wartości.  
  
 Dzieje się tak, gdy niektóre kolumny w zestawie danych szkoleniowych jest pusta.  
  
**Rozwiązanie:** Użyj [Clean Missing Data](clean-missing-data.md) modułu, aby usunąć kolumny z wszystkie brakujące wartości.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Wszystkie wiersze w zestawie danych szkoleniowych zawierają brakujące wartości.  Należy rozważyć użycie modułu Clean Missing Data można usunąć brakujące wartości.|  
 

## <a name="error-0131"></a>Błąd 0131  
 Wystąpi wyjątek, jeśli jeden lub więcej zestawów danych w formie pliku zip zakończy się niepowodzeniem rozpakowano i poprawnie zarejestrowany  
  
 Ten błąd jest generowany, gdy jeden lub więcej zestawów danych w formie pliku zip nie powiodło się rozpakowano i odczytać poprawnie. Zostanie wyświetlony ten błąd, jeśli wypakowania zakończy się niepowodzeniem, ponieważ sam plik zip lub jeden z plików jest uszkodzony lub ma błąd systemu podczas próby rozpakowania i Rozwiń plik.  
  
**Rozwiązanie:** Użyj informacje zawarte w komunikacie o błędzie, aby określić sposób kontynuować.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Przekazywanie plików z rozszerzeniem zip zestawy danych nie powiodło się|  
|Pliki z rozszerzeniem zip zestawu danych {0} nie powiodło się z następującym komunikatem: {1}|  
|Pliki z rozszerzeniem zip zestawu danych {0} nie powiodło się z {1} wyjątek z komunikatem: {2}|  
  

## <a name="error-0132"></a>Błąd 0132  
 Nazwa pliku nie została określona dla rozpakowywania; Znaleziono wiele plików w pliku zip.  
  
 Ten błąd jest generowany, gdy nazwa pliku nie została określona dla rozpakowywania; Znaleziono wiele plików w pliku zip. Zostanie wyświetlony ten błąd, jeśli plik zip zawiera więcej niż jeden plik skompresowany, ale nie określono pliku do wyodrębnienia w **zestaw danych do rozpakowywania** w polu tekstowym **właściwość** okienko modułu. Obecnie tylko jeden plik można wyodrębnić każdorazowo, gdy moduł jest uruchamiany.  
  
**Rozwiązanie:** Komunikat o błędzie zawiera listę plików znalezionych w pliku .zip. Nazwa żądanego pliku skopiuj i wklej go w **zestaw danych do rozpakowywania** pola tekstowego.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Plik zip zawiera wiele plików; należy określić plik, aby rozwinąć.|  
|Plik zawiera więcej niż jeden plik. Określ plik, aby rozwinąć. Znaleziono następujące pliki: {0}|  
  

## <a name="error-0133"></a>Błąd 0133  
 Nie można odnaleźć określonego pliku w pliku zip  
  
 Ten błąd jest generowany, jeśli nazwa pliku wprowadzony w **zestaw danych do rozpakowywania** pole **właściwość** okienko nie jest zgodna z nazwą, każdego pliku, znaleziono w pliku zip. Najbardziej typowe przyczyny tego błędu są, wpisując błąd lub wyszukiwanie pliku archiwum niewłaściwego pliku rozwinąć.  
  
**Rozwiązanie:** Ponownie moduł. Jeśli nazwa pliku, przeznaczonych do zdekompresowania pojawi się na liście plików znalezionych, skopiuj nazwę pliku i wklej go w **zestaw danych do rozpakowywania** okno właściwości. Jeśli nie ma odpowiednią nazwę pliku na liście, sprawdź, czy masz plik zip poprawne i poprawną nazwę pliku odpowiednią.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Określony plik nie odnaleziono int pliku zip.|  
|Nie można odnaleźć określonego pliku. Znaleziono następujące pliki: {0}|  
  

## <a name="error-0134"></a>Błąd 0134
Wyjątek występuje, gdy kolumna etykiety jest nieobecna lub ma wystarczającej liczby wierszy etykietami.  
  
Ten błąd występuje, gdy moduł wymaga kolumnę etykiety, ale nie zawiera jedną kolumnę zaznaczenia. lub kolumnie Etykieta nie ma za dużo wartości.

Ten błąd może również wystąpić, gdy poprzednia operacja zmiany zestawu danych, taki sposób, że wiersze niewystarczające są dostępne dla podrzędnego operacji. Załóżmy na przykład, można użyć wyrażenia w **partycja i próbka** moduł dzielenia zestawu danych według wartości. Nie znaleziono żadnych dopasowań dla wyrażenia, jeden z zestawów danych, wynikające z partycji może być pusty.

Rozwiązanie: 

 Jeśli kolumna etykiety są objęte wybór kolumn, ale nie został rozpoznany, należy użyć [edytować metadane](edit-metadata.md) modułu, aby oznaczyć je jako kolumna etykiety.
  
  <!--Use the [Summarize Data](summarize-data.md) module to generate a report that shows how many values are missing in each column. -->Następnie należy użyć [Clean Missing Data](clean-missing-data.md) modułu do usunięcia wierszy z brakującymi wartościami w kolumnie Etykieta. 

 Sprawdź swoje wejściowych zestawów danych, aby upewnić się, że zawierają one prawidłowe dane i wystarczającą liczbę wierszy w celu spełnienia wymagań operacji. Wiele algorytmów wygeneruje komunikat o błędzie, jeśli wymagają one niektóre minimalne numerowanie wierszy danych, ale dane zawierają tylko kilka wierszy lub tylko nagłówek.
  
|Komunikaty o wyjątkach|
|------------------------|
|Wyjątek występuje, gdy kolumna etykiety jest nieobecna lub ma wystarczającej liczby wierszy etykietami.|  
|Wyjątek występuje w przypadku kolumny etykiety jest nieobecna lub ma mniej niż {0} oznaczone wiersze|  
  

## <a name="error-0135"></a>Błąd 0135  
 Tylko na podstawie centroida — oś klastra jest obsługiwana.  
  
**Rozwiązanie:** Ten komunikat o błędzie może wystąpić, jeśli próbujesz obliczyć klastrowania model, który jest oparty na niestandardowego algorytmu klastrowania, która nie korzysta z centroids zainicjować klastra.  
  
  <!--You can use [Evaluate Model](evaluate-model.md) to evaluate clustering models that are based on the  [K-Means Clustering](k-means-clustering.md) module. For custom algorithms, use the [Execute R Script](execute-r-script.md) module to create a custom evaluation script.  -->
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Tylko na podstawie centroida — oś klastra jest obsługiwana.|  
  

## <a name="error-0136"></a>Błąd 0136  
 Nazwa pliku nie został zwrócony; Nie można przetworzyć pliku w wyniku.  
  
**Rozwiązanie:**
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Nazwa pliku nie został zwrócony; Nie można przetworzyć pliku w wyniku.|  
  

## <a name="error-0137"></a>Błąd 0137  
 Zestaw SDK usługi Azure Storage napotkał błąd podczas konwertowania między właściwościami tabel i kolumn w zestawie danych podczas odczytu lub zapisu.  
  
**Rozwiązanie:**
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Błąd konwersji między kolumnami właściwości i zestaw danych magazynu tabel platformy Azure.|  
|Błąd konwersji między kolumnami właściwości i zestaw danych magazynu tabel platformy Azure. Aby uzyskać dodatkowe informacje: {0}|  

## <a name="error-0138"></a>Błąd 0138  
 Pamięci została wyczerpana, na pełną uruchomiony modułu. Próbkowanie zestawu danych może pomóc rozwiązać problem.  
  
 Ten błąd występuje, gdy moduł, który jest uruchomiony wymaga więcej pamięci niż jest dostępne w kontenerze platformy Azure. Może to nastąpić, jeśli pracujesz z dużego zestawu danych, a bieżąca operacja nie pasują do pamięci.  
  
**Rozwiązanie:** Jeśli chcesz odczytać duży zestaw danych i nie można ukończyć operacji, próbkowanie zestawu danych może pomóc.  
  
  <!--If you use the visualizations on datasets to check the cardinality of columns, only some rows are sampled. To get a full report, use [Summarize Data](summarize-data.md). You can also use the [Apply SQL Transformation](apply-sql-transformation.md) to check for the number of unique values in each column.  
  
 Sometimes transient loads can lead to this error. Machine support also changes over time. 
  
 Try using [Principal Component Analysis](principal-component-analysis.md) or one of the provided feature selection methods to reduce your dataset to a smaller set of more feature-rich columns: [Feature Selection](feature-selection-modules.md)  -->
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Pamięci została wyczerpana, na pełną uruchomiony modułu.|  
  

## <a name="error-0139"></a>Błąd 0139  
 Wyjątek występuje, gdy nie jest możliwe do przekonwertowania na kolumnę do innego typu.  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning występuje, gdy użytkownik próbuje przekonwertować kolumnę do innego typu danych, ale typ nie jest obsługiwany przez bieżącą operację lub modułu.  
  
 Ten błąd może również zostać wyświetlony, gdy moduł próbuje niejawnie przekonwertować danych w celu spełnienia wymagań bieżącego modułu, ale konwersja nie jest możliwe.  
  
**Rozwiązanie:**

1. Przejrzyj dane wejściowe i określić dokładny typ danych kolumny, której chcesz użyć, a typ danych kolumny, który daje błędu. Czasami może się wydawać, typ danych jest poprawna, a także Dowiedz się, że zmodyfikował nadrzędnego operacji typu danych lub użycie kolumny. Użyj [edytować metadane](edit-metadata.md) modułu, aby zresetować kolumny metadanych do stanu pierwotnego. 
2. Spójrz na stronie pomocy modułu, aby sprawdzić wymagania dla określonej operacji. Określ typy danych są obsługiwane przez bieżący moduł, a zakres wartości jest obsługiwana. 
 <!--3. If values need to be truncated, rounded, or outliers removed, use the [Apply Math Operation](apply-math-operation.md) or [Clip Values](clip-values.md) modules to make corrections.-->
4. Należy rozważyć, czy istnieje możliwość konwersji lub Zastosuj rzutowanie kolumnę do innego typu danych. Następujących modułów wszystkich zapewniają dużą elastyczność i moc do modyfikowania danych: 
 <!--
   + [Apply SQL Transformation](apply-sql-transformation.md)
   + [Execute R Script](execute-r-script.md)
-->   
   + [Uruchom skrypt języka Python](execute-python-script.md).  

> [!NOTE]
> Nadal nie działa? Rozważ podanie dodatkowych informacji zwrotnych na ten problem, która pomoże nam tworzyć wskazówki dotyczące lepiej rozwiązywania problemów. Po prostu o przesyłanie opinii na tej stronie, a następnie podaj nazwę modułu, który wygenerował błąd i konwersja typu danych, który uległ awarii.
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Niedozwolona konwersja.|  
|Nie można skonwertować: {0}.|  
|Nie można skonwertować: {0}, w wierszu {1}.|  
|Nie można przekonwertować kolumny typu {0} z kolumną typu {1} w wierszu {2}.|  
|Nie można przekonwertować kolumnę "{2}" o typie {0} z kolumną typu {1} w wierszu {3}.|  
|Nie można przekonwertować kolumnę "{2}" o typie {0} do kolumny "{3}" typu {1} w wierszu {4}.| 

## <a name="error-0140"></a>Błąd 0140  
 Wyjątek występuje, jeśli przekazany argument zestawu kolumn nie zawiera innych kolumn, z wyjątkiem kolumny etykiety.  
  
 Ten błąd występuje, jeśli zestaw danych jest podłączony do modułu, który wymaga wiele kolumn, w tym funkcje, ale zostały podane tylko wartości w kolumnie etykiety.  
  
**Rozwiązanie:** Wybierz co najmniej jedną kolumnę funkcji, aby uwzględnić w zestawie danych.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Określona kolumna zestaw nie zawiera innych kolumn, z wyjątkiem kolumny etykiety.|  
  

## <a name="error-0141"></a>Błąd 0141  
 Wyjątek występuje, jeśli liczba wybranych kolumn liczbowych i unikatowe wartości w kategorii i kolumnach typu ciąg jest za mały.  
  
 Ten błąd w usłudze Azure Machine Learning występuje, gdy nie ma wystarczającej liczby unikatowych wartości w zaznaczonej kolumnie do wykonania tej operacji.  
  
**Rozwiązanie:** Niektóre operacje wykonaj operacje statystyczne dotyczące funkcji i podzielone na kategorie kolumny, a jeśli nie są wystarczająco dużo wartości, operacja może zakończyć się niepowodzeniem lub zwrócić nieprawidłowy wynik. Sprawdź zestaw danych, aby zobaczyć, jak wiele wartości w kolumnach funkcji i etykiety i ustalić, czy operacja, którą próbujesz wykonać, jest statystycznie.  
  
 Jeśli źródłowy zestaw danych jest prawidłowy, może również sprawdzać, czy pewnej operacji modyfikowania lub metadanych nadrzędnego dane zmienione dane i usunąć niektóre wartości.  
  
 Nadrzędne operacje obejmują podziału, pobieranie próbek lub próbkowanie, sprawdź, czy dane wyjściowe zawierają oczekiwanej liczby wierszy i wartości.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Liczba wybranych kolumn liczbowych i unikatowe wartości w kategorii i kolumnach typu ciąg jest za mały.|  
|Całkowita liczba wybranych kolumn liczbowych i unikatowe wartości w kategorii i kolumnach typu ciąg (obecnie {0}) powinien wynosić co najmniej {1}|  
  

## <a name="error-0142"></a>Błąd 0142  
 Wyjątek występuje, gdy system nie może załadować certyfikatu w celu uwierzytelnienia.  
  
**Rozwiązanie:**
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Nie można załadować certyfikatu.|  
|Certyfikat {0} nie może zostać załadowany. Jest jego odcisk palca {1}.|  
  

## <a name="error-0143"></a>Błąd 0143  
 Nie można przeanalizować podanego przez użytkownika adresu URL, który powinien być z usługi GitHub.  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning występuje, gdy określono nieprawidłowy adres URL i moduł wymaga prawidłowego adresu URL usługi GitHub.  
  
**Rozwiązanie:** Upewnij się, że adres URL odwołuje się do prawidłowe repozytorium GitHub. Inne typy lokacji nie są obsługiwane.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Adres URL nie pochodzi z github.com.|  
|Adres URL nie pochodzi z github.com: {0}|  

## <a name="error-0144"></a>Błąd 0144  
 Dostarczony przez użytkownika adresu url usługi GitHub brakuje oczekiwanego części.  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning występuje, gdy należy określić źródło pliku GitHub przy użyciu nieprawidłowy format adresu URL.  
  
**Rozwiązanie:** Sprawdź, czy adres URL repozytorium GitHub jest prawidłowy i kończy się \blob\ lub \tree\\.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Nie można przeanalizować adresu URL usługi GitHub.|  
|Nie można przeanalizować adresu URL usługi GitHub (Oczekiwano ' \blob\\"lub" \tree\\"po nazwie repozytorium): {0}|  

## <a name="error-0145"></a>Błąd 0145  
 Nie można utworzyć katalogu replikacji przyczyny.  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning występuje, gdy moduł nie może utworzyć określonego katalogu.  
  
**Rozwiązanie:**
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Nie można utworzyć katalogu replikacji.|  
  

## <a name="error-0146"></a>Błąd 0146  
 W przypadku rozpakowany do katalogu lokalnego do plików użytkownika połączone ścieżka może być zbyt długa.  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning występuje, gdy zostaną wyodrębnione pliki, ale niektóre nazwy pliku jest zbyt długa, gdy rozpakowano.  
  
**Rozwiązanie:** Edytowanie nazwy plików w taki sposób, że ścieżka w połączeniu, a nazwa pliku jest dłuższa niż 248 znaków.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Ścieżka replikacji jest dłuższy niż 248 znaków, Skróć nazwę skryptu lub ścieżkę.|  

## <a name="error-0147"></a>Błąd 0147  
 Nie można pobrać zadań z witryny GitHub przyczyny  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning występuje, gdy nie można odczytać lub pobieranie określone pliki z usługi GitHub.  
  
**Rozwiązanie:** Ten problem może być tymczasowe; Możesz spróbować dostęp do plików w innym czasie. Lub upewnij się, że masz odpowiednie uprawnienia i czy źródło jest nieprawidłowy.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Błąd dostępu do usługi GitHub.|  
|Błąd dostępu do usługi GitHub. {0}|  
  

## <a name="error-0148"></a>Błąd 0148  
 Problemy przed nieautoryzowanym dostępem podczas wyodrębniania danych lub Tworzenie katalogu.  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning występuje, gdy próbujesz utworzyć katalog lub odczytać danych z magazynu, ale nie ma wystarczających uprawnień.  
  
**Rozwiązanie:**
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Wyjątek nieautoryzowanego dostępu podczas wyodrębniania danych.|  
  

## <a name="error-0149"></a>Błąd 0149  
 Plik użytkownika nie istnieje wewnątrz pakietu usługi GitHub.  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning występuje, gdy nie można odnaleźć określonego pliku.  
  
Rozwiązanie: 
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Nie znaleziono pliku w usłudze GitHub.|  
|Nie można odnaleźć pliku w usłudze GitHub.: {0}|  
  

## <a name="error-0150"></a>Błąd 0150  
 Skrypty, które pochodzą z pakietów użytkownika nie można rozpakować, najprawdopodobniej z powodu konfliktu z plikami w usłudze GitHub.  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning występuje, gdy skrypt nie można wyodrębnić, zwykle po istniejący plik o takiej samej nazwie.  
  
Rozwiązanie:
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Nie można rozpakować pakiet; kolizji nazw możliwe z plikami w usłudze GitHub.|  
  

## <a name="error-0151"></a>Błąd 0151  
 Wystąpił błąd podczas zapisywania do magazynu w chmurze. Sprawdź adres URL.  
  
 Ten błąd wystąpił w usłudze Azure Machine Learning występuje, gdy moduł próbuje zapisać danych do magazynu w chmurze, ale adres URL jest niedostępny lub nieprawidłowy.  
  
Rozwiązanie: Sprawdź adres URL i sprawdź, czy jest zapisywalny.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Błąd podczas zapisywania do magazynu (prawdopodobnie nieprawidłowy adres url) w chmurze.|  
|Wystąpił błąd podczas zapisywania do magazynu w chmurze: {0}. Sprawdź adres url.|  
  
## <a name="error-0152"></a>Błąd 0152  
 Typ chmury platformy Azure została niepoprawnie określona w module kontekst.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Typ zły chmury platformy Azure|  
|Typ zły chmury platformy Azure: {0}|  
  
## <a name="error-0153"></a>Błąd 0153  
 Określony punkt końcowy magazynu jest nieprawidłowy.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Typ zły chmury platformy Azure|  
|Punkt końcowy zły magazynu: {0}|  

## <a name="error-0154"></a>Błąd 0154  
 Nie można rozpoznać określonej nazwy serwera  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Nie można rozpoznać określonej nazwy serwera|  
|Określony serwer {0}. nie można rozpoznać adresu documents.azure.com|

## <a name="error-0155"></a>Błąd 0155  
 Klient DocDb zwrócił wyjątek  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Klient DocDb zwrócił wyjątek|  
|DocDb Client: {0}|

## <a name="error-0156"></a>Błąd 0156  
 Nieprawidłowe odpowiedzi HCatalog serwera.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Nieprawidłowe odpowiedzi HCatalog serwera. Upewnij się, że wszystkie usługi są uruchomione.|  
|Nieprawidłowe odpowiedzi HCatalog serwera. Upewnij się, że wszystkie usługi są uruchomione. Szczegóły błędu: {0}|

## <a name="error-0157"></a>Błąd 0157  
 Wystąpił błąd podczas odczytywania z usługi Azure Cosmos DB z powodu schematów niespójne lub innego dokumentu. Czytnik wymaga wszystkich dokumentów mają ten sam schemat.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Wykryto dokumenty z różnymi schematami. Upewnij się, że wszystkie dokumenty mają ten sam schemat|

## <a name="error-1000"></a>Błąd 1000  
Wewnętrzna biblioteka wyjątek.  
  
Ten błąd jest dostarczany do przechwytywania, w przeciwnym razie braku obsługi błędów aparatu wewnętrznej. W związku z tym przyczyny wystąpienia tego błędu, mogą się różnić w zależności od tego, moduł, który wygenerował błąd.  
  
Aby uzyskać dodatkową pomoc, firma Microsoft zaleca, opublikowaniem szczegółowy komunikat, który towarzyszy błąd na forum usługi Azure Machine Learning, wraz z opisem scenariusza, w tym dane używane jako dane wejściowe. Ta opinia pomoże nam określić priorytety błędów i identyfikowanie najważniejszych problemów dalszej pracy.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Wyjątek biblioteki.|  
|Biblioteka wyjątek: {0}|  
|{0} Biblioteka wyjątek: {1}|  
