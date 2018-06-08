---
title: Tworzenie niestandardowe R moduły w Azure Machine Learning | Dokumentacja firmy Microsoft
description: Szybki start do tworzenia niestandardowych modułów R w usłudze Azure Machine Learning.
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 6cbc628a-7e60-42ce-9f90-20aaea7ba630
ms.service: machine-learning
ms.component: studio
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 11/29/2017
ms.openlocfilehash: 555672df5b0b86858d460ff7606bc6ca23f4f103
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34834359"
---
# <a name="author-custom-r-modules-in-azure-machine-learning"></a>Tworzenie niestandardowych modułów R w usłudze Azure Machine Learning
W tym temacie opisano sposób tworzenia i wdrażania niestandardowego modułu R w usłudze Azure Machine Learning. Wyjaśniono, co to są niestandardowych modułów R i które pliki są używane do definiowania ich. Go ilustruje sposób tworzenia plików, które definiują modułu i jak można zarejestrować modułu do wdrożenia w obszarze roboczym uczenia maszynowego. Elementy i atrybuty używane w definicji niestandardowego modułu następnie są opisane bardziej szczegółowo. Również omówiono sposób użycia funkcji pomocniczych, plików i wielu wyjść. 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="what-is-a-custom-r-module"></a>Co to jest niestandardowego modułu R?
A **niestandardowego modułu** jest modułem zdefiniowane przez użytkownika, które można przekazać do swojego obszaru roboczego i wykonywane w ramach eksperymentu uczenia maszynowego Azure. A **niestandardowego modułu R** jest niestandardowy moduł, który wykonuje funkcję R zdefiniowane przez użytkownika. **R** to język programowania statystyczne obliczeniowych i grafiki, powszechnie używaną służące chi i dane dotyczące implementowania algorytmów. R jest obecnie jedynym obsługiwanym niestandardowe moduły, ale pomocy technicznej dla dodatkowych języków zostało zaplanowane w przyszłych wersjach językiem.

Niestandardowe moduły mają **najwyższej jakości stan** w usłudze Azure Machine Learning, w tym sensie, że mogą być używane tak samo jak inne modułu. Można je wykonać na inne moduły zawarte w opublikowanych eksperymenty lub wizualizacji. Masz kontrolę nad algorytm implementowane przez moduł, danych wejściowych i wyjściowych porty mają zostać użyte parametry modelowania i innych różne zachowania środowiska uruchomieniowego. Eksperymentu, która zawiera niestandardowe moduły również mogą być publikowane w galerii Azure AI łatwe udostępnianie.

## <a name="files-in-a-custom-r-module"></a>Pliki w niestandardowego modułu R
Niestandardowego modułu R jest zdefiniowane przez plik zip, który zawiera co najmniej dwa pliki:

* A **plik źródłowy** R funkcji udostępnianych przez moduł, który zawiera
* **Pliku definicji XML** , który opisuje interfejs niestandardowego modułu

Dodatkowe pliki pomocnicze można również uwzględnić w pliku zip, który zawiera funkcje, które są dostępne z niestandardowego modułu. Ta opcja została szczegółowo opisana w **argumenty** część sekcji odwołania **elementów w pliku definicji XML** poniższy przykład szybkiego startu.

## <a name="quickstart-example-define-package-and-register-a-custom-r-module"></a>Przykład Szybki Start: zdefiniuj, pakowanie i zarejestrować niestandardowego modułu R
W tym przykładzie przedstawiono sposób tworzenia pliki wymagane przez niestandardowego modułu R, umieścić je w pliku zip, a następnie Zarejestruj moduł w obszaru roboczego uczenia maszynowego. Przykład zip pakietu i przykładowe pliki można pobrać z [CustomAddRows.zip Pobierz plik](http://go.microsoft.com/fwlink/?LinkID=524916&clcid=0x409).

## <a name="the-source-file"></a>Plik źródłowy
Rozważmy przykładową **wierszy dodać niestandardowe** moduł, który modyfikuje standardowej implementacji **Dodawanie wierszy** moduł używany do łączenia wierszy (uwagi) z dwóch zestawów danych (danych ramki). Standardowe **Dodawanie wierszy** modułu dołącza wiersze drugi zestaw danych wejściowych w celu pierwszego użycia zestawu danych wejściowych `rbind` algorytmu. Dostosowane `CustomAddRows` funkcja podobnie akceptuje dwa zestawy danych, ale również zawierać parametr wymiany logiczną jako dodatkowe dane wejściowe. Jeśli ustawiono parametr wymiany **FALSE**, zwraca ten sam zestaw danych jako standardowej implementacji. Ale jeśli parametr wymiany jest **TRUE**, funkcji dołącza wierszy pierwszego zestawu danych wejściowych w celu drugiego zestawu danych zamiast tego. Plik CustomAddRows.R, który zawiera implementację R `CustomAddRows` funkcji udostępnianych przez **wierszy dodać niestandardowe** moduł ma następujący kod R.

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
Do udostępnienia to `CustomAddRows` funkcji jako moduł usługi Azure Machine Learning pliku definicji XML musi zostać utworzony do określenia jak **wierszy dodać niestandardowe** modułu powinien wyglądu i zachowania. 

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


Warto zauważyć, że wartość **identyfikator** atrybuty **dane wejściowe** i **Arg** elementów w pliku XML muszą być zgodne nazwy parametrów funkcji kodu języka R w CustomAddRows.R dokładnie plików: (*dataset1*, *dataset2*, i *wymiany* w przykładzie). Podobnie, wartość **punktu wejścia** atrybutu **języka** elementu musi odpowiadać nazwie funkcji w skrypcie R dokładnie: (*CustomAddRows* w przykładzie) . 

Z kolei **identyfikator** atrybutu dla **dane wyjściowe** elementu nie odpowiada zmiennych w skrypcie R. Jeśli wymagane jest więcej niż jedno wyjście, po prostu zwraca listę z funkcji R z wynikami umieszczone *w tej samej kolejności* jako **dane wyjściowe** elementy są zadeklarowane w pliku XML.

### <a name="package-and-register-the-module"></a>Pakiet, a następnie zarejestrować modułu
Zapisz te dwa pliki jako *CustomAddRows.R* i *CustomAddRows.xml* , a następnie do zip ze sobą dwa pliki *CustomAddRows.zip* pliku.

Aby zarejestrować je w obszarze roboczym Machine Learning, przejdź do obszaru roboczego w Machine Learning Studio, kliknij przycisk **+ nowy** znajdującego się na dole i wybierz polecenie **modułu -> z pakietu ZIP** przekazać nowy **Wierszy dodać niestandardowe** modułu.

![Przekaż Zip](./media/custom-r-modules/upload-from-zip-package.png)

**Wierszy dodać niestandardowe** moduł jest gotowy do dostęp do eksperymentów uczenia maszynowego.

## <a name="elements-in-the-xml-definition-file"></a>Elementy w pliku definicji XML
### <a name="module-elements"></a>Moduł elementów
**Modułu** element służy do definiowania niestandardowego modułu w pliku XML. Można zdefiniować wiele modułów w jednym pliku XML przy użyciu wielu **modułu** elementów. Każdy moduł w obszarze roboczym musi mieć unikatową nazwę. Zarejestruj niestandardowego modułu o takiej samej nazwie jak istniejący moduł niestandardowych i zastępuje istniejący moduł nowym. Niestandardowe moduły można jednak zarejestrowane o takiej samej nazwie jak istniejący moduł usługi Azure Machine Learning. Jeśli tak, pojawią się one w **niestandardowy** kategorii palety modułów.

    <Module name="Custom Add Rows" isDeterministic="false"> 
        <Owner>Microsoft Corporation</Owner>
        <Description>Appends one dataset to another...</Description>/> 


W ramach **modułu** elementu, można określić dwa dodatkowe elementy opcjonalne:

* **właściciela** element, który jest osadzony w module  
* **opis** element zawierający tekst, który jest wyświetlany w szybką pomoc dla modułu i kiedy umieść kursor nad modułu w Interfejsie użytkownika Machine Learning.

Zasady ograniczeń znaków w elementach modułu:

* Wartość **nazwa** atrybutu w **modułu** element nie może przekraczać 64 znaków. 
* Zawartość **opis** element nie może przekraczać 128 znaków.
* Zawartość **właściciela** element nie może przekraczać 32 znaków.

Moduł wyniki mogą być deterministyczna lub nondeterministic.* * Domyślnie, wszystkie moduły są uważane za deterministyczna. Oznacza to mając niezmiennych zestaw parametrów wejściowych i danych, modułu powinien zwrócić tego samego eacRAND wyników lub czas functionh, który jest uruchomiony. To zachowanie, Azure Machine Learning Studio zwracające tylko moduły oznaczona jako deterministyczna, jeśli parametr lub danych wejściowych został zmieniony. Zwraca buforowane wyniki także znacznie szybsze wykonywanie eksperymentów.

Dostępne są funkcje, które są niedeterministyczne, takich jak RAND lub funkcji, która zwraca bieżącą datę lub godzinę. Jeśli niedeterministyczna funkcja korzysta z modułu, można określić, czy moduł jest deterministyczna przez ustawienie opcjonalne **isDeterministic** atrybutu **FALSE**. Dzięki temu, że moduł zostanie uruchomiona ponownie, przy każdym uruchomieniu eksperyment, nawet jeśli nie zmieniono moduł danych wejściowych i parametrów. 

### <a name="language-definition"></a>Język definicji
**Języka** elementu w pliku definicji XML jest używany do określenia niestandardowego modułu języka. R jest obecnie jedynym obsługiwanym językiem. Wartość **sourceFile** atrybutu musi być nazwą pliku R, który zawiera funkcji do wywołania po uruchomieniu modułu. Ten plik musi być częścią pakietu zip. Wartość **punktu wejścia** atrybut jest Nazwa wywoływanej funkcji i musi być zgodna z prawidłową funkcji zdefiniowanej przy w pliku źródłowym.

    <Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />


### <a name="ports"></a>Porty
Porty wejściowe i wyjściowe dla niestandardowego modułu są określone w elementach podrzędnych **porty** sekcja pliku definicji XML. Kolejność tych elementów określa układ doświadczonym (UX) przez użytkowników. Pierwszy element podrzędny **wejściowych** lub **dane wyjściowe** wymienionych w **porty** port wejściowy lewej w UX. Learning maszyny staje się element pliku XML
Każdy wejściowy i port wyjściowy może być opcjonalny **opis** elementu podrzędnego, który określa tekst wyświetlany, gdy Przesuń wskaźnik myszy za pośrednictwem portu w Interfejsie użytkownika Machine Learning.

**Porty reguły**:

* Maksymalna liczba **porty wejściowe i wyjściowe** to 8 dla każdego.

### <a name="input-elements"></a>Elementów wejściowych
Porty wejściowe umożliwiają przekazywanie danych do obszaru roboczego i funkcja R. **Typy danych** obsługiwanych dla porty wejściowe są następujące: 

**Element DataTable:** tego typu jest przekazany do funkcji R jako data.frame. W rzeczywistości żadnych typów (na przykład plików CSV lub pliki ARFF), które są obsługiwane przez uczenia maszynowego i które są zgodne z **DataTable** są konwertowane na data.frame automatycznie. 

        <Input id="dataset1" name="Input 1" type="DataTable" isOptional="false">
            <Description>Input Dataset 1</Description>
           </Input>

**Identyfikator** atrybut skojarzony z poszczególnymi **DataTable** port wejściowy musi mieć unikatową wartość i wartość ta musi odpowiadać odpowiadającego nazwany parametr w funkcji R.
Opcjonalne **DataTable** portów, które nie są przekazywane jako dane wejściowe w eksperymencie ma wartość **NULL** przekazany do funkcji R i opcjonalnie zip, porty są ignorowane, jeśli dane wejściowe nie jest połączony. **IsOptional** atrybutu jest opcjonalny dla obu **DataTable** i **Zip** typów i jest *false* domyślnie.

**ZIP:** niestandardowe moduły można zaakceptować plik zip jako dane wejściowe. Tych danych wejściowych jest rozpakowane do katalogu roboczego R funkcji

        <Input id="zippedData" name="Zip Input" type="Zip" IsOptional="false">
            <Description>Zip files to be extracted to the R working directory.</Description>
           </Input>

Dla niestandardowych modułów R identyfikator portu Zip nie musi odpowiadać żadnych parametrów funkcji R. Jest to spowodowane pliku zip jest automatycznie wyodrębniane do katalogu roboczego R.

**Reguły wprowadzania:**

* Wartość **identyfikator** atrybutu **dane wejściowe** element musi być prawidłową nazwą zmiennej języka R.
* Wartość **identyfikator** atrybutu **dane wejściowe** element nie może być dłuższa niż 64 znaki.
* Wartość **nazwa** atrybutu **dane wejściowe** element nie może być dłuższa niż 64 znaki.
* Zawartość **opis** element nie może być dłuższa niż 128 znaków
* Wartość **typu** atrybutu **dane wejściowe** element musi być *Zip* lub *DataTable*.
* Wartość **isOptional** atrybutu **dane wejściowe** element nie jest wymagana (i jest *false* domyślnie, gdy nie określono); ale jeśli jest określona, musi ona być *true* lub *false*.

### <a name="output-elements"></a>Elementy danych wyjściowych
**Standardowe dane wyjściowe porty:** porty dane wyjściowe są mapowane na wartości zwracanych z funkcji R, które mogą być następnie używane przez kolejne modułów. *Element DataTable* jest typ portu tylko standardowe wyjście obecnie obsługiwane. (Obsługę *uczących* i *przekształca* nadchodzi.) A *DataTable* danych wyjściowych jest zdefiniowany jako:

    <Output id="dataset" name="Dataset" type="DataTable">
        <Description>Combined dataset</Description>
    </Output>

Dla danych wyjściowych w niestandardowych modułów R, wartość **identyfikator** atrybutu nie musi odpowiadać dowolny skrypt języka R, ale musi być unikatowa. Dla wyjścia pojedynczy moduł, musi być wartość zwrócona przez funkcję R *data.frame*. W celu przekazania więcej niż jeden obiekt obsługiwany typ danych, porty odpowiednie dane wyjściowe muszą być określone w pliku definicji XML i muszą być zwracane w postaci listy obiektów. Obiekty danych wyjściowych są przypisywane do danych wyjściowych porty od lewej do prawej, odzwierciedlający kolejności, w których obiekty są umieszczane w liście zwracanych.

Na przykład, jeśli chcesz zmodyfikować **wierszy dodać niestandardowe** modułu do oryginalnego dwóch zestawów danych wyjściowych *dataset1* i *dataset2*, oprócz sprzężonych nowy zestaw danych *dataset*, (w kolejności od lewej do prawej, jako: *dataset*, *dataset1*, *dataset2*), następnie zdefiniuj porty wyjścia w CustomAddRows.xml pliku w następujący sposób:

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


I zwracają listę obiektów w postaci listy we właściwej kolejności w "CustomAddRows.R":

    CustomAddRows <- function(dataset1, dataset2, swap=FALSE) { 
        if (swap) { dataset <- rbind(dataset2, dataset1)) } 
        else { dataset <- rbind(dataset1, dataset2)) 
        } 
    return (list(dataset, dataset1, dataset2)) 
    } 

**Wizualizacja danych wyjściowych:** można także określić port wyjściowy typu *wizualizacji*, który zawiera dane wyjściowe z R grafiki urządzenia i konsola danych wyjściowych. Ten port nie jest częścią dane wyjściowe funkcji R i nie zakłóca kolejność inne typy portów danych wyjściowych. Aby dodać port wizualizacji na niestandardowe moduły, Dodaj **dane wyjściowe** element o wartości *wizualizacji* dla jego **typu** atrybutu:

    <Output id="deviceOutput" name="View Port" type="Visualization">
      <Description>View the R console graphics device output.</Description>
    </Output>

**Dane wyjściowe reguły:**

* Wartość **identyfikator** atrybutu **dane wyjściowe** element musi być prawidłową nazwą zmiennej języka R.
* Wartość **identyfikator** atrybutu **dane wyjściowe** element nie może być dłuższa niż 32 znaki.
* Wartość **nazwa** atrybutu **dane wyjściowe** element nie może być dłuższa niż 64 znaki.
* Wartość **typu** atrybutu **dane wyjściowe** element musi być *wizualizacji*.

### <a name="arguments"></a>Argumenty
Dodatkowe dane mogą zostać przekazane do funkcji R za pośrednictwem modułu parametrów, które są zdefiniowane w **argumenty** elementu. Te parametry są wyświetlane w okienku po prawej stronie właściwości interfejsu użytkownika nie jest Machine Learning, w przypadku wybrania modułu. Argumenty może być dowolną z obsługiwanych typów lub można utworzyć niestandardowe wyliczenie w razie potrzeby. Podobnie jak **porty** elementów, **argumenty** elementy mogą mieć opcjonalny **opis** element, który określa tekst, który jest wyświetlany, gdy wskaźnik myszy nad Nazwa parametru.
Opcjonalne właściwości dla modułu, na przykład defaultValue minValue i maxValue może zostać dodana do wszystkich argumentów jako atrybuty **właściwości** elementu. Prawidłowe właściwości dla **właściwości** elementu są zależne od typu argumentu i opisano z typami argumentów obsługiwanych w następnej sekcji. Argumenty z **isOptional** ustawioną właściwość **"true"** nie wymagają od użytkownika wprowadzenia wartości. Jeśli wartość nie zostanie przekazany do argumentu, argument nie jest przekazany do funkcji punktu wejścia. Argumenty funkcji punktu wejścia, które są opcjonalne muszą być jawnie obsługiwany przez funkcję, np. domyślna wartość NULL w definicji funkcji punktu wejścia. Opcjonalny argument będzie tylko wymuszać innych ograniczeń argumentu, tj. min lub max, jeśli wartość jest podana przez użytkownika.
Zgodnie z wejściami i wyjściami jest krytyczny, że każdego z parametrów ma unikatowy identyfikator skojarzony z nimi. W naszym przykładzie szybki start został skojarzony identyfikator i parametru *wymiany*.

### <a name="arg-element"></a>ARG — element
Parametr modułu jest definiowana za pomocą **Arg** elementem podrzędnym **argumenty** sekcja pliku definicji XML. W przypadku elementów podrzędnych w **porty** sekcji kolejność parametrów w **argumenty** sekcja definiuje układu w UX. Parametry są wyświetlane od góry w dół w Interfejsie użytkownika w tej samej kolejności, w którym są zdefiniowane w pliku XML. Typy obsługiwane przez usługi Machine Learning parametrów są wyświetlane tutaj. 

**int** — parametr (32-bitowy) typu Liczba całkowita.

    <Arg id="intValue1" name="Int Param" type="int">
        <Properties min="0" max="100" default="0" />
        <Description>Integer Parameter</Description>
    </Arg>


* *Opcjonalne właściwości*: **min**, **max**, **domyślne** i **isOptional**

**Podwójna** — parametr typu double.

    <Arg id="doubleValue1" name="Double Param" type="double">
        <Properties min="0.000" max="0.999" default="0.3" />
        <Description>Double Parameter</Description>
    </Arg>


* *Opcjonalne właściwości*: **min**, **max**, **domyślne** i **isOptional**

**wartość logiczna** — parametrem logicznym reprezentowanego przez pole wyboru w UX.

    <Arg id="boolValue1" name="Boolean Param" type="bool">
        <Properties default="true" />
        <Description>Boolean Parameter</Description>
    </Arg>



* *Opcjonalne właściwości*: **domyślne** — wartość false, gdy nie jest ustawiona

**ciąg**: ciąg standardowy

    <Arg id="stringValue1" name="My string Param" type="string">
        <Properties isOptional="true" />
        <Description>String Parameter 1</Description>
    </Arg>    

* *Opcjonalne właściwości*: **domyślne** i **isOptional**

**ColumnPicker**: parametr wybór kolumny. Ten typ renderowania w środowiska użytkownika jako selektora kolumn. **Właściwości** element jest tu używany do określenia identyfikatora portu, z której są wybierane kolumny, której typ port docelowy musi być *DataTable*. Wynik kolumnę zaznaczenia jest przekazany do funkcji R jako listę ciągów zawierającą nazwy zaznaczonej kolumny. 

        <Arg id="colset" name="Column set" type="ColumnPicker">      
          <Properties portId="datasetIn1" allowedTypes="Numeric" default="NumericAll"/>
          <Description>Column set</Description>
        </Arg>


* *Wymagane właściwości*: **portId** — identyfikator elementu danych wejściowych jest zgodny z typem *DataTable*.
* *Opcjonalne właściwości*:
  
  * **allowedTypes** — filtry kolumny typów, z której można wybrać. Prawidłowe wartości to: 
    
    * Liczbowy
    * Wartość logiczna
    * Podzielone na kategorie
    * Ciąg
    * Etykieta
    * Cecha
    * Wynik
    * Wszyscy
  * **domyślne** -prawidłowy domyślny wybór selektor kolumn obejmują: 
    
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

**Lista rozwijana**: listę wyliczany określone przez użytkownika (rozwijaną). Elementy listy rozwijanej są określane w **właściwości** przy użyciu elementu **elementu** elementu. **Identyfikator** dla każdego **elementu** musi być unikatowa i prawidłową zmienną R. Wartość **nazwa** z **elementu** służy jako tekst, który zostanie wyświetlony i wartość, która została przekazana do funkcji R.

    <Arg id="color" name="Color" type="DropDown">
      <Properties default="red">
        <Item id="red" name="Red Value"/>
        <Item id="green" name="Green Value"/>
        <Item id="blue" name="Blue Value"/>
      </Properties>
      <Description>Select a color.</Description>
    </Arg>    

* *Opcjonalne właściwości*:
  * **domyślne** — wartość domyślna właściwości musi być zgodna z wartość spośród **elementu** elementów.

### <a name="auxiliary-files"></a>Pliki pomocnicze
Każdego pliku, który znajduje się w pliku ZIP niestandardowego modułu będzie można używać w czasie wykonywania. Wszelkich struktur katalogów obecne są zachowywane. Oznacza to, czy plik sourcing działa takie same lokalnie i w usłudze Azure Machine Learning na wykonanie. 

> [!NOTE]
> Zwróć uwagę, że wszystkie pliki są wyodrębniane do katalogu 'src', powinien mieć wszystkie ścieżki ' src / "prefiks.
> 
> 

Załóżmy na przykład, że chcesz usunąć wszystkie wiersze z NAs z zestawu danych, a także usunąć wszystkie zduplikowane wiersze przed wyprowadzanie go do CustomAddRows i zostały już zapisane funkcję R, która robi to w pliku RemoveDupNARows.R:

    RemoveDupNARows <- function(dataFrame) {
        #Remove Duplicate Rows:
        dataFrame <- unique(dataFrame)
        #Remove Rows with NAs:
        finalDataFrame <- dataFrame[complete.cases(dataFrame),]
        return(finalDataFrame)
    }
Może pobierać plików pomocniczych RemoveDupNARows.R w funkcji CustomAddRows:

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

## <a name="execution-environment"></a>Środowiska wykonawczego
Środowisko wykonanie skryptu języka R używa tej samej wersji R jako **wykonanie skryptu języka R** modułu i używać tego samego domyślne pakiety. Można również dodać dodatkowe pakiety języka R do niestandardowego modułu przez włączenie ich do pakietu zip niestandardowego modułu. Wystarczy załadować je w skrypcie R tak jak w środowisku R. 

**Ograniczenia środowiska wykonawczego** obejmują:

* System plików nie trwała: pliki napisane po uruchomieniu niestandardowego modułu nie są zachowywane w wielu uruchomień tego samego modułu.
* Nie dostępu do sieci

