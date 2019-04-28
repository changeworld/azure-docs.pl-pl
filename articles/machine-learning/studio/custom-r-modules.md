---
title: Definiowanie niestandardowych modułów R
titleSuffix: Azure Machine Learning Studio
description: W tym temacie opisano sposób tworzenia i wdrażania niestandardowego modułu R w usłudze Azure Machine Learning Studio. Wyjaśniono, czym są niestandardowych modułów R i jakie pliki są używane do definiowania ich.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 11/29/2017
ms.openlocfilehash: 0dec86eff9b9df70514be6f32f3aad60bfb311ca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60751219"
---
# <a name="define-custom-r-modules-for-azure-machine-learning-studio"></a>Definiowanie niestandardowych modułów R dla usługi Azure Machine Learning Studio

W tym temacie opisano sposób tworzenia i wdrażania niestandardowego modułu R w usłudze Azure Machine Learning Studio. Wyjaśniono, czym są niestandardowych modułów R i jakie pliki są używane do definiowania ich. Przedstawia on sposób tworzenia plików, które definiują modułu i jak można zarejestrować modułu dla wdrożenia w obszarze roboczym usługi Machine Learning. Elementy i atrybuty używane w definicji niestandardowego modułu następnie są opisane bardziej szczegółowo. Również omówiono sposób użycia funkcji pomocniczych, plików i wiele wyjść. 



## <a name="what-is-a-custom-r-module"></a>Co to jest niestandardowego modułu R?
A **niestandardowego modułu** jest modułem zdefiniowanych przez użytkownika, które mogą być przekazane do obszaru roboczego i wykonywane w ramach eksperymentu usługi Azure Machine Learning Studio. A **niestandardowego modułu R** jest niestandardowy moduł, który wykonuje funkcję R zdefiniowanych przez użytkownika. **R** to język programowania do przeprowadzania obliczeń statystycznych i grafiki, która jest powszechnie używana przez Statystyków i analityków danych wykonywania algorytmów. Obecnie języka R jest jedynym obsługiwanym w moduły niestandardowe, ale pomocy technicznej dla dodatkowych języków jest zaplanowana do przyszłych wersji językiem.

Moduły niestandardowe mają **najwyższej jakości stan** w usłudze Azure Machine Learning Studio, w tym sensie, że mogą być używane tak jak każdy inny moduł. Można je wykonać z innymi modułami uwzględnione w opublikowanych eksperymentów, lub w wizualizacjach. Masz kontrolę nad algorytm implementowana przez moduł, dane wejściowe i dane wyjściowe porty mają zostać użyte, parametry modelowania i inne różne zachowanie w czasie wykonywania. Eksperyment, który zawiera niestandardowe moduły można publikować w taki sposób, w galerii sztucznej Inteligencji platformy Azure w celu łatwego udostępniania.

## <a name="files-in-a-custom-r-module"></a>Pliki w niestandardowego modułu R
Niestandardowego modułu R jest definiowany przez plik zip, który zawiera co najmniej dwa pliki:

* A **plik źródłowy** funkcji języka R, udostępniane przez moduł, który zawiera
* **Pliku definicji XML** opisujący interfejs niestandardowego modułu

Dodatkowe pliki pomocnicze można również uwzględnić w pliku zip, który zawiera funkcje, które są dostępne z niestandardowego modułu. Ta opcja została omówiona w **argumenty** część sekcji odwołania **elementów w pliku definicji XML** poniższy przykład Szybki Start.

## <a name="quickstart-example-define-package-and-register-a-custom-r-module"></a>Przykład Szybki Start: definiowanie, pakowanie i rejestrowania niestandardowego modułu R
Ten przykład ilustruje sposób utworzenia pliki wymagane przez niestandardowego modułu R, umieścić je w postaci pliku zip, a następnie zarejestrować modułu w obszarze roboczym usługi Machine Learning. Przykład zip pakietu i przykładowe pliki można pobrać z [CustomAddRows.zip Pobierz plik](https://go.microsoft.com/fwlink/?LinkID=524916&clcid=0x409).

## <a name="the-source-file"></a>Plik źródłowy
Rozważ przykład **niestandardowe Dodaj wiersze** moduł, który modyfikuje standardowej implementacji **Dodaj wiersze** moduł używany do łączenia się z dwóch zestawów danych (ramek danych), wiersze (uwagi). Standardowa **Dodaj wiersze** modułu dołącza wiersze drugi zestaw danych wejściowych na końcu pierwszego wejściowego zestawu danych za pomocą `rbind` algorytmu. Dostosowywane `CustomAddRows` funkcja podobnie akceptuje dwa zestawy danych, ale również akceptuje parametr wymiany logiczną jako dodatkowe dane wejściowe. Jeśli ustawiono parametr wymiany **FALSE**, zwraca ten sam zestaw danych, co standardowej implementacji. Ale, jeśli parametr wymiany jest **TRUE**, funkcja dołącza wierszy pierwszego zestawu danych wejściowych na końcu drugiego zestawu danych, zamiast tego. Plik CustomAddRows.R, który zawiera implementację R `CustomAddRows` funkcji udostępnianych przez **niestandardowe Dodaj wiersze** moduł ma następujący kod R.

    CustomAddRows <- function(dataset1, dataset2, swap=FALSE) 
    {
        if (swap)
        {
            return (rbind(dataset2, dataset1));
        }
        else
        {
            return (rbind(dataset1, dataset2));
        } 
    } 

### <a name="the-xml-definition-file"></a>Plik definicji XML
Aby udostępnić to `CustomAddRows` funkcji jako moduł usługi Azure Machine Learning Studio pliku definicji XML musi zostać utworzony do określenia sposób, w jaki **niestandardowe Dodaj wiersze** moduł powinien wyglądały i zachowywały się. 

    <!-- Defined a module using an R Script -->
    <Module name="Custom Add Rows">
        <Owner>Microsoft Corporation</Owner>
        <Description>Appends one dataset to another. Dataset 2 is concatenated to Dataset 1 when Swap is FALSE, and vice versa when Swap is TRUE.</Description>

    <!-- Specify the base language, script file and R function to use for this module. -->        
        <Language name="R" 
         sourceFile="CustomAddRows.R" 
         entryPoint="CustomAddRows" />  

    <!-- Define module input and output ports -->
    <!-- Note: The values of the id attributes in the Input and Arg elements must match the parameter names in the R Function CustomAddRows defined in CustomAddRows.R. -->
        <Ports>
            <Input id="dataset1" name="Dataset 1" type="DataTable">
                <Description>First input dataset</Description>
            </Input>
            <Input id="dataset2" name="Dataset 2" type="DataTable">
                <Description>Second input dataset</Description>
            </Input>
            <Output id="dataset" name="Dataset" type="DataTable">
                <Description>The combined dataset</Description>
            </Output>
        </Ports>

    <!-- Define module parameters -->
        <Arguments>
            <Arg id="swap" name="Swap" type="bool" >
                <Description>Swap input datasets.</Description>
            </Arg>
        </Arguments>
    </Module>


Koniecznie należy pamiętać, że wartość **identyfikator** atrybuty **dane wejściowe** i **Arg** elementy w pliku XML muszą być zgodne nazwy parametrów funkcji kodu języka R CustomAddRows.R pliku dokładnie: (*dataset1*, *dataset2*, i *wymiany* w przykładzie). Podobnie, wartość **punktu wejścia** atrybutu **języka** element musi dokładnie pasować nazwą funkcji w skrypcie języka R: (*CustomAddRows* w przykładzie). 

Z kolei **identyfikator** atrybutu dla **dane wyjściowe** elementu nie odpowiada żadnych zmiennych w skrypcie języka R. Gdy wymagana jest więcej niż jedno wyjście, po prostu zwraca listę z funkcji języka R z wynikami umieszczone *w tej samej kolejności* jako **dane wyjściowe** elementy są deklarowane w pliku XML.

### <a name="package-and-register-the-module"></a>Pakowanie i Zarejestruj moduł
Zapisz te dwa pliki jako *CustomAddRows.R* i *CustomAddRows.xml* i następnie zip ze sobą dwa pliki do *CustomAddRows.zip* pliku.

Aby zarejestrować je w obszarze roboczym usługi Machine Learning, przejdź do obszaru roboczego usługi Machine Learning Studio, kliknij przycisk **+ nowy** znajdujący się u dołu i wybierz **modułu -> z pakietu ZIP** do przekazania nowego **Niestandardowe Dodaj wiersze** modułu.

![Przekazywanie pliku Zip](./media/custom-r-modules/upload-from-zip-package.png)

**Niestandardowe Dodaj wiersze** modułu jest teraz gotowy do uzyskiwał dostęp do eksperymentów uczenia maszynowego.

## <a name="elements-in-the-xml-definition-file"></a>Elementy w pliku definicji XML
### <a name="module-elements"></a>Moduł elementów
**Modułu** element jest używany do definiowania niestandardowego modułu w pliku XML. Wiele modułów można zdefiniować w jednym pliku XML przy użyciu wielu **modułu** elementów. Każdy moduł, w obszarze roboczym musi mieć unikatową nazwę. Rejestrowanie niestandardowego modułu z taką samą nazwę jak istniejący niestandardowy moduł i zastępuje istniejący moduł nowym plikiem. Niestandardowe moduły można jednak zarejestrowane w usłudze taką samą nazwę jak istniejący moduł usługi Azure Machine Learning Studio. Tak, jakby ta pojawiła się w **niestandardowe** kategorii części palety modułów.

    <Module name="Custom Add Rows" isDeterministic="false"> 
        <Owner>Microsoft Corporation</Owner>
        <Description>Appends one dataset to another...</Description>/> 


W ramach **modułu** elementu, można określić dwa dodatkowe elementy opcjonalne:

* **właściciela** element, który jest osadzony w module  
* **opis** element, który zawiera tekst po umieszczeniu wskaźnika modułu w Interfejsie użytkownika Machine Learning, która jest wyświetlana w szybką pomoc dla modułu.

Zasady ograniczeń znaków w elementach modułu:

* Wartość **nazwa** atrybutu w **modułu** element nie może przekraczać 64 znaków. 
* Zawartość **opis** element nie może przekraczać 128 znaków.
* Zawartość **właściciela** element nie może przekraczać 32 znaków.

Wyniki modułu mogą być deterministyczna lub nondeterministic.* * Domyślnie, wszystkie moduły są traktowane jako deterministyczna. Oznacza to biorąc pod uwagę niezmiennych zbiór parametrów wejściowych i danych, moduł powinien zwrócić ten sam eacRAND wyników lub czasu funkcji, które jest uruchamiane. Biorąc pod uwagę to zachowanie, Azure Machine Learning Studio uruchomienia tylko moduły oznaczone jako deterministyczna, jeśli parametr lub zmienione dane wejściowe. Zwracanie wyników buforowanych także znacznie szybsze wykonywanie eksperymentów.

Dostępne są funkcje, które są jednoznaczne wyniki, takie jak RAND lub funkcji, która zwraca bieżącą datę lub godzinę. Jeśli niedeterministyczna funkcja korzysta z modułu, można określić, czy moduł jest niejednoznaczne, ustawienie opcjonalne **isDeterministic** atrybutu **FALSE**. Temu, że moduł ponownego uruchomienia po każdym uruchomieniu eksperymentu nawet, jeśli moduł danych wejściowych i parametry nie uległy zmianie. 

### <a name="language-definition"></a>Język definicji
**Języka** elementu w pliku definicji XML jest używany do określenia języka niestandardowego modułu. Obecnie język R jest jedynym obsługiwanym językiem. Wartość **sourceFile** atrybutu musi być nazwą pliku języka R, który zawiera funkcji do wywołania po uruchomieniu modułu. Ten plik musi być częścią pakietu zip. Wartość **punktu wejścia** atrybut jest nazwę wywoływanej funkcji i musi być zgodna z prawidłową funkcją zdefiniowane za pomocą w pliku źródłowym.

    <Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />


### <a name="ports"></a>Porty
Porty wejściowe i wyjściowe dla niestandardowego modułu są określone przez elementy podrzędne elementu **porty** sekcja pliku definicji XML. Kolejność tych elementów określa układ doświadczonym (UX) przez użytkowników. Pierwszy element podrzędny **wejściowych** lub **dane wyjściowe** na liście **porty** elementu w pliku XML, staje się port wejściowy najdalej po lewej stronie w UX. Learning maszyny
Każdy wejściowe i port wyjściowy może mieć opcjonalną **opis** elementu podrzędnego, który określa tekst wyświetlany po najechaniu kursorem myszy portu w Interfejsie użytkownika Machine Learning.

**Reguły portów**:

* Maksymalna liczba **porty wejściowe i wyjściowe** wynosi 8 dla każdego.

### <a name="input-elements"></a>Elementów wejściowych
Porty wejściowe umożliwiają przekazywanie danych do funkcji języka R i obszaru roboczego. **Typy danych** obsługiwanych dla porty wejściowe są następujące: 

**DataTable:** Ten typ jest przekazywany do funkcji języka R jako data.frame. W rzeczywistości żadnych typów (na przykład, pliki CSV lub pliki ARFF), które są obsługiwane przez uczenie maszynowe i które są zgodne z **DataTable** są konwertowane na data.frame automatycznie. 

        <Input id="dataset1" name="Input 1" type="DataTable" isOptional="false">
            <Description>Input Dataset 1</Description>
           </Input>

**Identyfikator** atrybut skojarzony z każdą **DataTable** port wejściowy musi mieć unikatową wartość, a ta wartość musi być zgodny odpowiadającymi mu dostawcami nazwanych parametrów w funkcji języka R.
Opcjonalnie **DataTable** porty, które nie są przekazywane jako dane wejściowe w eksperymencie mają wartość **NULL** przekazany do funkcji języka R i opcjonalnie zip, porty są ignorowane, jeśli dane wejściowe nie jest połączony. **IsOptional** atrybut jest opcjonalny dla obu **DataTable** i **Zip** typów i jest *false* domyślnie.

**Kod pocztowy:** Niestandardowe moduły można zaakceptować pliku zip jako dane wejściowe. Te dane wejściowe to dopiero do katalogu roboczego R funkcji

        <Input id="zippedData" name="Zip Input" type="Zip" IsOptional="false">
            <Description>Zip files to be extracted to the R working directory.</Description>
           </Input>

Dla niestandardowych modułów R identyfikator portu Zip musi odpowiadać wszelkie parametry funkcji języka R. Jest to spowodowane pliku zip jest automatycznie wyodrębniane do katalogu roboczego R.

**Reguły wprowadzania:**

* Wartość **identyfikator** atrybutu **dane wejściowe** element musi być prawidłową nazwą zmiennej języka R.
* Wartość **identyfikator** atrybutu **dane wejściowe** element nie może być dłuższa niż 64 znaki.
* Wartość **nazwa** atrybutu **dane wejściowe** element nie może być dłuższa niż 64 znaki.
* Zawartość **opis** element nie może być dłuższa niż 128 znaków
* Wartość **typu** atrybutu **dane wejściowe** element musi być *Zip* lub *DataTable*.
* Wartość **isOptional** atrybutu **dane wejściowe** element nie jest wymagana (i *false* domyślnie, gdy nie określono); ale jeśli jest określona, musi to być *true* lub *false*.

### <a name="output-elements"></a>Elementy danych wyjściowych
**Wyjście standardowe porty:** Dane wyjściowe porty są mapowane na wartości zwracane w funkcji języka R, które następnie mogą być używane przez kolejne moduły. *DataTable* jest typ portu tylko standardowe dane wyjściowe, które są obecnie obsługiwane. (Obsługa *Learners* i *przekształca* nadchodzi.) A *DataTable* dane wyjściowe jest zdefiniowana jako:

    <Output id="dataset" name="Dataset" type="DataTable">
        <Description>Combined dataset</Description>
    </Output>

Aby uzyskać dane wyjściowe w niestandardowych modułów R-wartości **identyfikator** atrybut nie ma odnoszą się do żadnych czynności w skrypcie języka R, ale muszą być unikatowe. Pojedynczy moduł danych wyjściowych, wartość zwracana przez funkcję języka R musi być *data.frame*. W celu przekazania więcej niż jeden obiekt obsługiwany typ danych, porty odpowiednie dane wyjściowe muszą być określone w pliku definicji XML i muszą być zwracane w postaci listy obiektów. Obiekty danych wyjściowych są przypisywane do danych wyjściowych porty od lewej do prawej, odzwierciedlający kolejność, w których obiekty są umieszczane na liście zwracanych.

Na przykład, jeśli chcesz zmodyfikować **niestandardowe Dodaj wiersze** modułu w danych wyjściowych, oryginalnym dwa zestawy danych *dataset1* i *dataset2*, oprócz dołączonym do nowego zestawu danych *dataset*, (w kolejności od lewej do prawej, jako: *dataset*, *dataset1*, *dataset2*), następnie zdefiniuj porty wyjścia w CustomAddRows.xml pliku w następujący sposób:

    <Ports> 
        <Output id="dataset" name="Dataset Out" type="DataTable"> 
            <Description>New Dataset</Description> 
        </Output> 
        <Output id="dataset1_out" name="Dataset 1 Out" type="DataTable"> 
            <Description>First Dataset</Description> 
        </Output> 
        <Output id="dataset2_out" name="Dataset 2 Out" type="DataTable"> 
            <Description>Second Dataset</Description> 
        </Output> 
        <Input id="dataset1" name="Dataset 1" type="DataTable"> 
            <Description>First Input Table</Description>
        </Input> 
        <Input id="dataset2" name="Dataset 2" type="DataTable"> 
            <Description>Second Input Table</Description> 
        </Input> 
    </Ports> 


I zwracają listę obiektów na liście w odpowiedniej kolejności w "CustomAddRows.R":

    CustomAddRows <- function(dataset1, dataset2, swap=FALSE) { 
        if (swap) { dataset <- rbind(dataset2, dataset1)) } 
        else { dataset <- rbind(dataset1, dataset2)) 
        } 
    return (list(dataset, dataset1, dataset2)) 
    } 

**Wizualizacja danych wyjściowych:** Można także określić port wyjściowy danego typu *wizualizacji*, która wyświetla dane wyjściowe z języka R grafiki urządzenia i konsoli danych wyjściowych. Ten port nie jest częścią dane wyjściowe funkcji języka R i nie kolidują z zamówieniem inne typy danych wyjściowych portów. Aby dodać port wizualizacji niestandardowych modułów **dane wyjściowe** element z wartością *wizualizacji* dla jego **typu** atrybutu:

    <Output id="deviceOutput" name="View Port" type="Visualization">
      <Description>View the R console graphics device output.</Description>
    </Output>

**Dane wyjściowe reguły:**

* Wartość **identyfikator** atrybutu **dane wyjściowe** element musi być prawidłową nazwą zmiennej języka R.
* Wartość **identyfikator** atrybutu **dane wyjściowe** element nie może być dłuższa niż 32 znaki.
* Wartość **nazwa** atrybutu **dane wyjściowe** element nie może być dłuższa niż 64 znaki.
* Wartość **typu** atrybutu **dane wyjściowe** element musi być *wizualizacji*.

### <a name="arguments"></a>Argumenty
Dodatkowe dane mogą być przekazywane do funkcji języka R za pomocą parametrów modułów, które są zdefiniowane w **argumenty** elementu. Te parametry są wyświetlane w okienku po prawej stronie właściwości interfejsu użytkownika Machine Learning w przypadku wybrania modułu. Argumenty mogą być dowolny z obsługiwanych typów lub można utworzyć niestandardowe wyliczenia, gdy potrzebne. Podobnie jak **porty** elementów, **argumenty** elementów może mieć opcjonalną **opis** element, który określa tekst wyświetlany po umieszczeniu wskaźnika myszy Nazwa parametru.
Opcjonalne właściwości dla modułu, takie jak właściwość defaultValue, minValue i maxValue można dodać do dowolnego argumentu jako atrybuty do **właściwości** elementu. Prawidłowe właściwości dla **właściwości** elementu zależą od typu argumentu i opisano z typami argumentów obsługiwanych w następnej sekcji. Argumenty ze **isOptional** właściwością **"true"** nie wymagają od użytkownika wprowadzenia wartości. Jeśli nie podano wartości argumentu, argument nie jest przekazywany do funkcji punktu wejścia. Argumenty funkcję punktu wejścia, które są opcjonalne muszą być jawnie obsługiwany przez funkcję, np. przypisana wartość domyślna wartość NULL w definicji funkcji punktu wejścia. Opcjonalny argument będzie tylko wymuszać innych ograniczeń argumentu, czyli min lub max, jeśli wartość jest podana przez użytkownika.
Podobnie jak w przypadku danych wejściowych i wyjściowych, jest krytyczny, że każdego z parametrów ma unikatowy identyfikator skojarzonych z nimi. W naszym przykładzie szybki start został skojarzony identyfikator/parametr *wymiany*.

### <a name="arg-element"></a>ARG — element
Parametr modułu jest definiowana za pomocą **Arg** element podrzędny elementu **argumenty** sekcja pliku definicji XML. Podobnie jak w przypadku elementów podrzędnych w **porty** sekcji kolejność parametrów w **argumenty** sekcja definiuje układ w najlepsze środowisko użytkownika. Parametry są wyświetlane od góry w dół w Interfejsie użytkownika w tej samej kolejności, w którym są zdefiniowane w pliku XML. Poniżej przedstawiono typy obsługiwanych przez usługę uczenia maszynowego dla parametrów. 

**int** — parametr (32-bitowy) typu Liczba całkowita.

    <Arg id="intValue1" name="Int Param" type="int">
        <Properties min="0" max="100" default="0" />
        <Description>Integer Parameter</Description>
    </Arg>


* *Opcjonalne właściwości*: **min**, **max**, **domyślne** i **isOptional**

**podwójne** — parametr typu double.

    <Arg id="doubleValue1" name="Double Param" type="double">
        <Properties min="0.000" max="0.999" default="0.3" />
        <Description>Double Parameter</Description>
    </Arg>


* *Opcjonalne właściwości*: **min**, **max**, **domyślne** i **isOptional**

**wartość logiczna** — parametr logiczny, który jest reprezentowany przez pole wyboru w najlepsze środowisko użytkownika.

    <Arg id="boolValue1" name="Boolean Param" type="bool">
        <Properties default="true" />
        <Description>Boolean Parameter</Description>
    </Arg>



* *Opcjonalne właściwości*: **domyślne** — FAŁSZ Jeśli nieustawiona

**ciąg**: ciąg standardowy

    <Arg id="stringValue1" name="My string Param" type="string">
        <Properties isOptional="true" />
        <Description>String Parameter 1</Description>
    </Arg>    

* *Opcjonalne właściwości*: **domyślne** i **isOptional**

**ColumnPicker**: parametr wybór kolumny. Ten typ jest renderowany w Interfejsie jako selektor kolumn. **Właściwość** element jest tutaj używany do określenia identyfikatora portu, z której kolumny są zaznaczone, gdzie typ port docelowy musi być *DataTable*. Wynik wybór kolumn jest przekazany do funkcji języka R jako listę ciągów zawierającą nazwy zaznaczonej kolumnie. 

        <Arg id="colset" name="Column set" type="ColumnPicker">      
          <Properties portId="datasetIn1" allowedTypes="Numeric" default="NumericAll"/>
          <Description>Column set</Description>
        </Arg>


* *Wymagane właściwości*: **identyfikator portu** -zgodny z identyfikatorem elementu danych wejściowych z typem *DataTable*.
* *Opcjonalne właściwości*:
  
  * **allowedTypes** — filtry kolumny typów, z której można wybrać. Prawidłowe wartości to: 
    
    * Numeryczne
    * Wartość logiczna
    * Podzielone na kategorie
    * Ciąg
    * Etykieta
    * Cecha
    * Wynik
    * Wszyscy
  * **domyślne** — obejmują opcje prawidłowy domyślny wybór kolumny: 
    
    * Brak
    * NumericFeature
    * NumericLabel
    * NumericScore
    * NumericAll
    * BooleanFeature
    * BooleanLabel
    * BooleanScore
    * BooleanAll
    * CategoricalFeature
    * CategoricalLabel
    * CategoricalScore
    * CategoricalAll
    * StringFeature
    * StringLabel
    * StringScore
    * StringAll
    * AllLabel
    * AllFeature
    * AllScore
    * Wszyscy

**Lista rozwijana**: na liście wyliczany określonych przez użytkownika (rozwijanej). Określono w elementach rozwijanych **właściwości** elementu za pomocą **elementu** elementu. **Identyfikator** dla każdego **elementu** musi być unikatowa i prawidłową zmienną R. Wartość **nazwa** z **elementu** zarówno jako tekst, który zostanie wyświetlony i wartość, która jest przekazywana do funkcji języka R.

    <Arg id="color" name="Color" type="DropDown">
      <Properties default="red">
        <Item id="red" name="Red Value"/>
        <Item id="green" name="Green Value"/>
        <Item id="blue" name="Blue Value"/>
      </Properties>
      <Description>Select a color.</Description>
    </Arg>    

* *Opcjonalne właściwości*:
  * **domyślne** — wartość domyślną właściwość musi być zgodna z wartością identyfikatora z jednego z **elementu** elementów.

### <a name="auxiliary-files"></a>Pliki pomocnicze
Dowolny plik, który znajduje się w pliku ZIP niestandardowego modułu będzie dostępna do użycia w czasie wykonywania. Wszelkich struktur katalogów obecne są zachowywane. Oznacza to, ten plik, określania źródła działa takie same lokalnie i w usłudze Azure Machine Learning Studio na wykonanie. 

> [!NOTE]
> Należy zauważyć, że wszystkie pliki są wyodrębniane do katalogu "src", więc powinny mieć wszystkie ścieżki "src /" prefiks.
> 
> 

Na przykład załóżmy, że chcesz usunąć wszystkie wiersze z NAs z zestawu danych, a także usunąć wszelkie zduplikowane wiersze przed podawania go do CustomAddRows, i zostały już wpisane funkcji języka R, tak, że w pliku RemoveDupNARows.R:

    RemoveDupNARows <- function(dataFrame) {
        #Remove Duplicate Rows:
        dataFrame <- unique(dataFrame)
        #Remove Rows with NAs:
        finalDataFrame <- dataFrame[complete.cases(dataFrame),]
        return(finalDataFrame)
    }
Można źródłowego pliku pomocniczego RemoveDupNARows.R w funkcji CustomAddRows:

    CustomAddRows <- function(dataset1, dataset2, swap=FALSE) {
        source("src/RemoveDupNARows.R")
            if (swap) { 
                dataset <- rbind(dataset2, dataset1))
             } else { 
                  dataset <- rbind(dataset1, dataset2)) 
             } 
        dataset <- removeDupNARows(dataset)
        return (dataset)
    }

Następnie przekaż plik zip zawierający "CustomAddRows.R", "CustomAddRows.xml" i "RemoveDupNARows.R" jako niestandardowego modułu R.

## <a name="execution-environment"></a>Środowisko wykonawcze
Środowisko do wykonywania skryptu języka R korzysta z tej samej wersji języka R jako **wykonanie skryptu języka R** modułu i używać tych samych domyślne pakiety. Dodatkowe pakiety R można również dodać do Twojego niestandardowego modułu, umieszczając je w pakiecie niestandardowego modułu w pliku zip. Po prostu załadować je w skrypcie języka R tak jak w środowisku R. 

**Ograniczenia środowiska wykonawczego** obejmują:

* System plików-trwałe: Pliki zapisane po uruchomieniu niestandardowego modułu nie są utrwalane w wielu uruchomień tego samego modułu.
* Brak dostępu do sieci

