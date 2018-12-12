---
title: Usługi Machine Learning Studio — często zadawane pytania (FAQ) — Azure | Dokumentacja firmy Microsoft
description: 'Usługa Azure Machine Learning Studio: — Często zadawane pytania dotyczące rozliczeń, możliwości i ograniczeń usługi w chmurze do sprawnego modelowania predykcyjnego.'
keywords: wprowadzenie do uczenia maszynowego,modelowanie predykcyjne,co to jest uczenie maszynowe
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=hshapiro, author=heatherbshapiro)
ms.author: amlstudiodocs
ms.assetid: a4a32a06-dbed-4727-a857-c10da774ce66
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/02/2017
ms.openlocfilehash: 4d4e6a47b35d3cb98e4425c11628625818642fa0
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52867855"
---
# <a name="azure-machine-learning-studio-frequently-asked-questions-billing-capabilities-limitations-and-support"></a>Usługa Azure Machine Learning Studio — często zadawane pytania: rozliczenia, możliwości, ograniczeń i pomocy technicznej
Przedstawione tutaj często zadawane pytania i odpowiedzi dotyczą usługi Azure Machine Learning, która jest usługą w chmurze przeznaczoną do tworzenia modeli predykcyjnych i rozwiązań operacyjnych za pośrednictwem usług sieci Web. Wśród często zadawanych pytań znajdują się pytania dotyczące korzystania z samej usługi, w tym między innymi na temat modelu rozliczeń, możliwości, ograniczeń i pomocy technicznej.

**Masz pytania, których nie możesz tu znaleźć?**

Dla usługi Azure Machine Learning udostępniono forum w witrynie MSDN, gdzie członkowie społeczności skupiającej osoby zajmujące się nauką o danych mogą zadawać pytania dotyczące usługi Azure Machine Learning. Zespół ds. usługi Azure Machine Learning monitoruje forum. Przejdź do [forum poświęconego usłudze Azure Machine Learning](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning), aby poszukać odpowiedzi lub zadać własne pytanie.

## <a name="general-questions"></a>Pytania ogólne
**Co to jest Machine Learning Studio?**

Machine Learning Studio to środowisko kanwy przeciągania i upuszczania, które możesz uzyskać dostęp za pomocą przeglądarki sieci web. Usługa Machine Learning Studio udostępnia paletę modułów w interfejsie graficznym do tworzenia, który pomaga tworzyć kompletne przepływy pracy do analizy danych w formie eksperymentów.

Aby uzyskać więcej informacji o usłudze Machine Learning Studio, zobacz [What is Machine Learning Studio?](what-is-ml-studio.md) (Co to jest Machine Learning Studio?).

**Co to jest usługa Machine Learning API?**

Usługa Machine Learning API umożliwia wdrażanie modeli predykcyjnych, na przykład tworzonych w środowisku usługi Machine Learning Studio, jako skalowalnych i odpornych na błędy usług sieci Web. Usługi sieci Web utworzone przez usługę Machine Learning API są interfejsami API REST, które zapewniają interfejs do komunikacji między aplikacjami zewnętrznymi i modelami analizy predykcyjnej.

Aby uzyskać więcej informacji, zobacz [How to consume an Azure Machine Learning Web service](consume-web-services.md) (Jak korzystać z usługi internetowej Azure Machine Learning).

**Gdzie znajduje się lista klasycznych usług sieci Web? Gdzie można znaleźć wykaz nowych usług sieci Web (opartych na usłudze Azure Resource Manager)?**

Usługi sieci Web utworzone przy użyciu klasycznego modelu wdrażania oraz usługi sieci Web utworzone za pomocą nowego modelu wdrażania przy użyciu usługi Azure Resource Manager zostały wymienione w portalu [usług sieci Web usługi Microsoft Azure Machine Learning](https://services.azureml.net/).

Klasyczne usługi sieci Web są również wyszczególnione w obszarze [Machine Learning Studio](http://studio.azureml.net) na karcie **Usługi sieci Web**.

## <a name="azure-machine-learning-questions"></a>Pytania dotyczące usługi Azure Machine Learning
**Co to są usługi sieci Web Azure Machine Learning?**

Usługi sieci Web Machine Learning zapewniają interfejs między aplikacją a modelem oceniania przepływu pracy usługi Machine Learning. Dzięki użyciu usługi Azure Machine Learning aplikacja zewnętrzna może komunikować się z modelem oceniania przepływu pracy usługi Machine Learning w czasie rzeczywistym. Wywołanie usługi sieci Web Machine Learning zwraca wyniki prognozowania do aplikacji zewnętrznej. Wywołanie usługi sieci Web polega na przekazaniu klucza interfejsu API utworzonego podczas wdrażania tej usługi. Usługa sieci Web Machine Learning korzysta z interfejsu REST — popularnej architektury w projektach programistycznych dla sieci Web.

Usługa Azure Machine Learning udostępnia dwa typy usług sieci Web:

* Usługa odpowiedzi na żądanie (RRS, Request-Response Service): wysoce skalowalna usługa o małych opóźnieniach, która udostępnia interfejs dla bezstanowych modeli utworzonych i wdrożonych przy użyciu usługi Machine Learning Studio.
* Usługa wykonywania wsadowego (BES, Batch Execution Service): asynchroniczna usługa przeznaczona do oceniania partii rekordów danych.

Istnieje kilka sposobów uzyskiwania dostępu do usługi sieci Web za pomocą interfejsu API REST. Można na przykład napisać aplikację w języku C#, R lub Python korzystającą z przykładowego kodu automatycznie wygenerowanego podczas wdrażania usługi sieci Web.

Kod przykładowy można znaleźć na:
- stronie dotyczącej wykorzystania danej usługi sieci Web w portalu usług sieci Web Azure Machine Learning,
- stronie pomocy interfejsu API na pulpicie nawigacyjnym usługi sieci Web w usłudze Machine Learning Studio.

Można też użyć automatycznie utworzonego przykładowego skoroszytu programu Microsoft Excel dostępnego na pulpicie nawigacyjnym usługi sieci Web w usłudze Machine Learning Studio.

**Jakie są najważniejsze aktualności dotyczące usługi Azure Machine Learning?**

Aby uzyskać informacje o najnowszych aktualizacjach, zobacz [Co nowego w usłudze Azure Machine Learning](../../active-directory/fundamentals/whats-new.md).

## <a name="machine-learning-studio-questions"></a>Pytania dotyczące usługi Machine Learning Studio
### <a name="import-and-export-data-for-machine-learning"></a>Importowanie i eksportowanie danych na potrzeby usługi Machine Learning
**Jakie źródła danych obsługuje usługa Machine Learning?**

Dostępne są trzy sposoby pobierania danych do eksperymentu usługi Machine Learning Studio:

- Przekazanie pliku lokalnego jako zestawu danych.
- Użycie modułu do zaimportowania danych z usług danych w chmurze.
- Zaimportowanie zestawu danych zapisanego w innym eksperymencie.

Aby dowiedzieć się więcej na temat obsługiwanych formatów plików, zobacz temat [Import training data into Machine Learning Studio](import-data.md) (Importowanie danych szkoleniowych do środowiska usługi Machine Learning Studio).

#### <a id="ModuleLimit"></a>Jak duży może być zestaw danych dla moich modułów?
W typowych przypadkach użycia moduły w usłudze Machine Learning Studio obsługują zestawy danych o rozmiarze maksymalnie 10 GB, zawierające gęsto upakowane dane liczbowe. Jeśli moduł przyjmuje więcej niż jedną operację wprowadzania danych wejściowych, wówczas 10 GB to łączny rozmiar wszystkich danych wejściowych. Większe zestawy danych można przed pozyskaniem próbkować przy użyciu zapytań programu Hive lub usługi Azure SQL Database albo stosując przetwarzanie wstępne metodą uczenia przez liczenie.  

Podczas normalizacji funkcji następujące typy danych mogą ulegać rozszerzaniu do większych zestawów danych. Takie dane muszą być mniejsze niż 10 GB:

* Rozrzedzone
* Podzielone na kategorie
* Ciągi
* Dane binarne

W przypadku następujących modułów obowiązuje ograniczenie do zestawów danych mniejszych niż 10 GB:

* Moduły polecania
* Moduł Synthetic Minority Oversampling Technique (SMOTE)
* Moduły skryptów: R, Python, SQL
* Moduły, w których rozmiar danych wyjściowych może być większy niż rozmiar danych wejściowych, na przykład Przyłączenie lub Tworzenie skrótu funkcji
* Krzyżowa weryfikacja, Hiperparametry modelu strojenia, Regresja porządkowa oraz Multiklasa Jedna kontra wszystkie, gdy liczba iteracji jest bardzo duża

#### <a id="UploadLimit"></a>Jakie są ograniczenia przekazywania danych?
W przypadku zestawów danych o rozmiarach większych niż kilka GB dane należy przekazać do usługi Azure Storage lub usługi Azure SQL Database albo użyć usługi Azure HDInsight, zamiast przekazywać dane bezpośrednio z pliku lokalnego.

**Czy mogę odczytywać dane z usługi Amazon S3?**

Jeśli masz niewielką ilość danych i chcesz je ujawnić za pośrednictwem adresu URL HTTP, możesz użyć modułu [Import danych][import-data]. W przypadku większych ilości danych należy najpierw przenieść dane do usługi Azure Storage, a następnie użyć modułu [Import danych][import-data], aby wprowadzić dane do eksperymentu.
<!--

<SEE CLOUD DS PROCESS>
-->

**Czy istnieje wbudowana możliwość wprowadzania obrazów?**

Informacje na temat możliwości wprowadzania obrazu są dostępne w temacie [Import Images][image-reader] (Import obrazów).

### <a name="modules"></a>Moduły
**W środowisku usługi Azure Machine Learning Studio nie ma poszukiwanego przeze mnie algorytmu, źródła danych, formatu danych lub szukanej operacji transformacji danych. Jakie są moje opcje?**

Możesz przejść do [forum opinii użytkowników](https://go.microsoft.com/fwlink/?LinkId=404231), aby zobaczyć żądania funkcji, które śledzimy. Jeśli pojawiło się już żądanie dotyczące możliwości, której poszukujesz, zagłosuj na to żądanie. Jeśli możliwość, której poszukujesz, nie istnieje, utwórz nowe żądanie. Na tym forum możesz również sprawdzić stan swojego żądania. Ściśle śledzimy tę listę i często aktualizujemy stan dostępności funkcji. Ponadto możesz użyć wbudowanej obsługi języków R i Python, aby utworzyć niestandardowe przekształcenia wedle potrzeby.

**Czy mogę przenieść mój istniejący kod do środowiska usługi Machine Learning Studio?**

Tak. Możesz przenieść istniejący kod w języku R lub Python do środowiska usługi Azure Machine Learning Studio, uruchomić go w tym samym eksperymencie co procesy uczące usługi Azure Machine Learning, a następnie wdrożyć rozwiązanie jako usługę sieci web za pośrednictwem usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz tematy [Extend your experiment with R](extend-your-experiment-with-r.md) (Rozszerzanie eksperymentu, korzystając z języka R) i [Execute Python machine learning scripts in Azure Machine Learning Studio](execute-python-scripts.md) (Wykonywanie skryptów uczenia maszynowego w języku Python w usłudze Azure Machine Learning Studio).

**Czy w celu definiowania modeli można używać języków, takich jak [PMML](http://en.wikipedia.org/wiki/Predictive_Model_Markup_Language)?**

Nie, język PMML (Predictive Model Markup Language) nie jest obsługiwany. Możesz użyć niestandardowego kodu języka R lub Python, aby zdefiniować moduł.

**Jak wiele modułów mogę równolegle uruchomić w moim eksperymencie?**  

Równolegle w eksperymencie można uruchomić maksymalnie cztery moduły.

### <a name="data-processing"></a>Przetwarzanie danych
**Czy istnieje możliwość interaktywnej wizualizacji danych (poza wizualizacjami języka R) w ramach eksperymentu?**

Kliknij wyjście modułu, aby zwizualizować dane i uzyskać statystyki.

**Podczas przeglądania wyników lub danych w przeglądarce liczba wierszy i kolumn jest ograniczona. Dlaczego?**

Do przeglądarki mogą być wysyłane duże ilości danych, dlatego rozmiar danych jest ograniczony, aby zapobiegać spowalnianiu usługi Machine Learning Studio. W celu zwizualizowania wszystkich danych/wyników lepiej jest pobrać dane i użyć programu Excel lub innego narzędzia.

### <a name="algorithms"></a>Algorytmy
**Jakie istniejące algorytmy są obsługiwane w środowisku usługi Machine Learning Studio?**

Usługa Machine Learning Studio udostępnia najnowocześniejsze algorytmy, takie jak skalowalne wzmocnione drzewa decyzyjne, bayesowskie systemy rekomendacji, głębokie sieci neuronowe i dżungle decyzyjne opracowywane w dziale badań firmy Microsoft. Dostępne są również skalowalne pakiety uczenia maszynowego typu open source, takie jak Vowpal Wabbit. Usługa Machine Learning Studio obsługuje algorytmy uczenia maszynowego na potrzeby binarnej i wieloklasowej klasyfikacji i regresji oraz klastrowania. Zobacz pełną listę [modułów usługi Machine Learning][machine-learning-modules].

**Czy algorytm uczenia maszynowego właściwy dla moich danych jest sugerowany automatycznie?**

Nie. Jednak w usłudze Machine Learning Studio istnieją różne sposoby porównywania wyników poszczególnych algorytmów w celu wyznaczenia algorytmu właściwego dla danego problemu.

**Czy dostępne są jakiekolwiek wskazówki dotyczące wyboru jednego udostępnionego algorytmu zamiast innego?**

Zobacz [How to choose an algorithm](algorithm-choice.md) (Jak wybrać algorytm).

**Czy udostępnione algorytmy są napisane w języku R lub Python?**

Nie. Te algorytmy są w większości napisane w językach kompilowanych w celu zapewnienia lepszej wydajności.

**Czy dostępne są jakiekolwiek szczegóły dotyczące udostępnionych algorytmów?**

W dokumentacji udostępniono pewne informacje o algorytmach i opisano parametry przeznaczone do strojenia w celu optymalizacji algorytmu do użycia.  

**Czy są dostępne jakieś rozwiązania do uczenia online?**

Nie. Obecnie jest obsługiwane tylko programowe ponowne trenowanie.

**Czy mogę zwizualizować warstwy modelu sieci neuronowej z użyciem modułu wbudowanego?**

Nie.

**Czy mogę tworzyć własne moduły w języku C# lub innym?**

Obecnie możesz użyć wyłącznie języka R do tworzenia nowych modułów niestandardowych.

### <a name="r-module"></a>Moduł R
**Jakie pakiety języka R są dostępne w usłudze Machine Learning Studio?**

Usługa Machine Learning Studio obsługuje obecnie ponad 400 pakietów języka R z sieci CRAN, a tutaj jest [aktualna lista](http://az754797.vo.msecnd.net/docs/RPackages.xlsx) wszystkich dostępnych pakietów. Zobacz też temat [Extend your experiment with R](extend-your-experiment-with-r.md) (Rozszerz swój eksperyment, korzystając z języka R), aby dowiedzieć się, jak pobrać tę listę samodzielnie. Jeśli żądanego pakietu nie ma na tej liście, należy podać nazwę pakietu na [forum opinii użytkowników](https://go.microsoft.com/fwlink/?LinkId=404231).

**Czy jest możliwe utworzenie niestandardowego modułu R?**

Tak. Zobacz temat [Author custom R modules in Azure Machine Learning](custom-r-modules.md) (Tworzenie niestandardowych modułów R w usłudze Azure Machine Learning).

**Czy dostępne jest środowisko REPL dla języka R?**

Nie. W studio nie ma środowiska REPL (Read-Eval-Print-Loop) dla języka R.

### <a name="python-module"></a>Moduł Python
**Czy jest możliwe utworzenie niestandardowego modułu Python?**

Obecnie nie jest to możliwe, ale można użyć dowolnej liczby modułów [Wykonanie skryptu Python][python] w celu uzyskania tego samego wyniku.

**Czy dostępne jest środowisko REPL dla języka Python?**

W usłudze Machine Learning Studio można skorzystać z notesów Jupyter. Aby uzyskać więcej informacji, zobacz temat [Manage experiment iterations in Machine Learning Studio](http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx) (Zarządzanie iteracjami eksperymentów w usłudze Machine Learning Studio).

## <a name="web-service"></a>Usługa sieci Web
### <a name="retrain"></a>Ponowne próbkowanie
**W jaki sposób ponownie trenować modele usługi Azure Machine Learning programowo?**

Użyj interfejsów API do ponownego trenowania. Aby uzyskać więcej informacji, zobacz temat [Retrain Machine Learning models programmatically](retrain-models-programmatically.md) (Ponowne trenowanie modeli uczenia maszynowego programowo). Przykładowy kod jest również dostępny w [demonstracji ponownego trenowania w usłudze Microsoft Azure Machine Learning](https://azuremlretrain.codeplex.com/).

### <a name="create"></a>Przycisk Utwórz
**Czy mogę wdrożyć model lokalnie lub w aplikacji bez połączenia z Internetem?**

Nie.

**Czy istnieje opóźnienie bazowe oczekiwane dla wszystkich usługi sieci Web?**

Zobacz [limity subskrypcji platformy Azure](../../azure-subscription-service-limits.md).

### <a name="use"></a>Użycie
**Kiedy należy uruchomić model predykcyjny jako usługę wykonywania wsadowego, a kiedy jako usługę odpowiedzi na żądanie?**

Usługa odpowiedzi na żądanie (RRS, Request Response Service) to usługa sieci Web o małym opóźnieniu i dużej skali. Zapewnia interfejs do modeli bezstanowych, które są tworzone i wdrażane ze środowiska, w którym wykonywane są eksperymenty. Usługa wykonywania wsadowego (BES, Batch Execution Service) jest usługą przeznaczoną do asynchronicznego oceniania partii rekordów danych. Dane wejściowe dla usługi BES przypominają dane wejściowe używane przez usługę RRS. Główna różnica polega na tym, że usługa BES odczytuje blok rekordów z różnych źródeł, takich jak usługa Azure Blob Storage, usługa Azure Table Storage, usługa Azure SQL Database, usługa HDInsight (zapytanie Hive) i źródła HTTP. Aby uzyskać więcej informacji, zobacz [How to consume an Azure Machine Learning Web service](consume-web-services.md) (Jak korzystać z usługi internetowej Azure Machine Learning).

**Jak zaktualizować model dla wdrożonej usługi sieci Web?**

Aby zaktualizować model predykcyjny dla już wdrożonej usługi, należy zmodyfikować i ponownie wykonać eksperyment, którego użyto w celu utworzenia i zapisania trenowanego modelu. Gdy dostępna jest nowa wersja trenowanego modelu, usługa Machine Learning Studio pyta, czy chcesz zaktualizować usługę sieci Web. Szczegółowe informacje na temat aktualizowania wdrożonej usługi sieci Web zawiera temat [Deploy a Machine Learning web service](publish-a-machine-learning-web-service.md) (Wdrażanie usługi sieci Web Machine Learning).

Można również użyć interfejsów API do ponownego trenowania.
Aby uzyskać więcej informacji, zobacz temat [Retrain Machine Learning models programmatically](retrain-models-programmatically.md) (Ponowne trenowanie modeli uczenia maszynowego programowo). Przykładowy kod jest również dostępny w [demonstracji ponownego trenowania w usłudze Microsoft Azure Machine Learning](https://azuremlretrain.codeplex.com/).

**Jak mogę monitorować moją usługę sieci Web wdrożoną w środowisku produkcyjnym?**

Po wdrożeniu modelu predykcyjnego można go monitorować z poziomu portalu usług sieci Web Azure Machine Learning. Dla każdej wdrożonej usługi istnieje osobny pulpit nawigacyjny, w którym dostępne są informacje pozwalające na monitorowanie tej usługi. Więcej informacji o zarządzaniu wdrożonymi usługami sieci Web można znaleźć w tematach [Manage a Web service using the Azure Machine Learning Web Services portal](manage-new-webservice.md) (Zarządzanie usługą sieci Web przy użyciu portalu usług sieci Web Azure Machine Learning) oraz [Manage an Azure Machine Learning workspace](manage-workspace.md) (Zarządzanie obszarem roboczym usługi Azure Machine Learning).

**Czy jest jakieś miejsce, w którym mogę zobaczyć dane wyjściowe moich usług RRS/BES?**

W przypadku usługi RRS wynik jest zwykle widoczny w odpowiedzi usługi sieci Web. Można go także zapisać do usługi Azure Blob Storage. W przypadku usługi BES dane wyjściowe są domyślnie zapisywane do obiektu blob. Dane wyjściowe można również zapisać w bazie danych lub tabeli, używając modułu [Eksport danych][export-data].

**Czy usługi sieci Web można tworzyć tylko z modeli utworzonych w usłudze Machine Learning Studio?**

Nie. Usługi sieci Web można również tworzyć bezpośrednio przy użyciu notesów Jupyter i programu RStudio.

**Gdzie można znaleźć informacje o kodach błędów?**

Listę kodów błędów i opisy zawiera temat [Machine Learning Module Error Codes](https://msdn.microsoft.com/library/azure/dn905910.aspx) (Kody błędów modułów usługi Machine Learning).

## <a name="scalability"></a>Skalowalność
**Jaka jest skalowalność usługi sieci Web?**

Aktualnie każdemu domyślnemu punktowi końcowemu przydzielone jest 20 równoczesnych żądań RRS. Tę wartość można skalować do 200 równoczesnych żądań na punkt końcowy, a każdą usługę sieci Web można skalować do 10 000 punktów końcowych, zgodnie z opisem w temacie [Scaling a Web service](scaling-webservice.md) (Skalowanie usługi sieci Web). W przypadku usługi BES każdy punkt końcowy może przetwarzać 40 żądań jednocześnie, a dodatkowe żądania (po przekroczeniu 40 żądań) są dodawane do kolejki. Żądania z kolejki są wykonywane automatycznie w miarę opróżniania kolejki.

**Czy zadania R są dystrybuowane między węzłami?**

Nie.  

**Ile danych mogę użyć do trenowania?**

W typowych przypadkach użycia moduły w usłudze Machine Learning Studio obsługują zestawy danych o rozmiarze maksymalnie 10 GB, zawierające gęsto upakowane dane liczbowe. Jeśli moduł przyjmuje więcej niż jeden zestaw danych wejściowych, wówczas łączny rozmiar wszystkich danych wejściowych wynosi 10 GB. Większe zestawy danych można przed pozyskaniem próbkować przy użyciu zapytań programu Hive lub zapytań usługi Azure SQL Database albo stosując przetwarzanie wstępne za pomocą modułów [Uczenie przy użyciu liczenia][counts].  

Podczas normalizacji funkcji następujące typy danych mogą ulegać rozszerzaniu do większych zestawów danych. Takie dane muszą być mniejsze niż 10 GB:

* Rozrzedzone
* Podzielone na kategorie
* Ciągi
* Dane binarne

W przypadku następujących modułów obowiązuje ograniczenie do zestawów danych mniejszych niż 10 GB:

* Moduły polecania
* Moduł Synthetic Minority Oversampling Technique (SMOTE)
* Moduły skryptów: R, Python, SQL
* Moduły, w których rozmiar danych wyjściowych może być większy niż rozmiar danych wejściowych, na przykład Przyłączenie lub Tworzenie skrótu funkcji
* Krzyżowa weryfikacja, Hiperparametry modelu strojenia, Regresja porządkowa oraz Multiklasa Jedna kontra wszystkie, gdy liczba iteracji jest bardzo duża

W przypadku zestawów danych o rozmiarach większych niż kilka GB należy przekazać dane do usługi Azure Storage lub usługi Azure SQL Database albo użyć usługi HDInsight, zamiast przekazywać dane bezpośrednio z pliku lokalnego.

**Czy istnieją jakiekolwiek ograniczenia dotyczące rozmiaru wektora?**

Wiersze i kolumny są ograniczone zgodnie z ograniczeniem .NET dla maksymalnej liczby całkowitej: 2 147 483 647.

**Czy można dostosować rozmiar pamięci maszyny wirtualnej, na której jest uruchomiona usługa sieci Web?**

Nie.  

## <a name="security-and-availability"></a>Bezpieczeństwo i dostępność
**Kto domyślnie ma dostęp do punktu końcowego HTTP usługi sieci Web? Jak ograniczyć dostęp do tego punktu końcowego?**

Po wdrożeniu usługi sieci Web dla tej usługi tworzony jest domyślny punkt końcowy. Domyślny punkt końcowy może być wywoływany przy użyciu właściwego dla niego klucza interfejsu API. Możesz dodać dodatkowe punkty końcowe z właściwymi dla nich kluczami z poziomu portalu usług sieci Web albo programowo z użyciem interfejsów API zarządzania usługami sieci Web. W celu wykonywania wywołań do usługi sieci Web potrzebne są klucze dostępu. Aby uzyskać więcej informacji, zobacz [How to consume an Azure Machine Learning Web service](consume-web-services.md) (Jak korzystać z usługi internetowej Azure Machine Learning).

**Co się stanie, jeśli nie można odnaleźć konta magazynu platformy Azure?**

Usługa Machine Learning Studio jest oparta na udostępnionym przez użytkownika koncie magazynu platformy Azure, w którym zapisywane są dane pośrednie podczas wykonywania przepływu pracy. To konto magazynu jest udostępniane na rzecz środowiska usługi Machine Learning Studio w momencie utworzenia obszaru roboczego. Jeśli po utworzeniu obszaru roboczego konto magazynu zostanie usunięte i nie można go znaleźć, obszar roboczy przestanie działać, a wszystkie eksperymenty w tym obszarze zakończą się niepowodzeniem.

Jeśli konto magazynu zostanie przypadkowo usunięte, konieczne będzie odtworzenie konta magazynu z tą samą nazwą w tym samym regionie co usunięte konto magazynu. Następnie należy ponownie zsynchronizować klucz dostępu.

**Co się dzieje, gdy mój klucz dostępu do konta magazynu nie jest zsynchronizowany?**

Usługa Machine Learning Studio jest oparta na udostępnionym przez użytkownika koncie magazynu platformy Azure, w którym przechowywane są dane pośrednie podczas wykonywania przepływu pracy. To konto magazynu jest udostępniane na rzecz środowiska usługi Machine Learning Studio w momencie utworzenia obszaru roboczego, a klucz dostępu jest skojarzony z tym obszarem roboczym. Jeśli po utworzeniu obszaru roboczego klucze dostępu zostaną zmienione, obszar roboczy nie będzie mógł uzyskać dostępu do konta magazynu. W rezultacie przestanie działać, a wszystkie eksperymenty w tym obszarze roboczym zakończą się niepowodzeniem.

Jeśli doszło do zmiany kluczy dostępu do konta magazynu, należy ponownie zsynchronizować klucze dostępu w obszarze roboczym, korzystając z witryny Azure Portal.  

## <a name="support-and-training"></a>Pomoc techniczna i szkolenia
**Gdzie można znaleźć szkolenia dotyczące usługi Azure Machine Learning?**

[Centrum dokumentacji usługi Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) udostępnia samouczki wideo i przewodniki dotyczące wykonywania określonych zadań. Te przewodniki krok po kroku stanowią wprowadzenie do usług i wyjaśniają cykl życia analizy danych, który obejmuje importowanie danych, czyszczenie danych, budowanie modeli predykcyjnych i wdrażanie ich w środowisku produkcyjnym z użyciem usługi Azure Machine Learning.

Stale wzbogacamy centrum usługi Machine Learning o nowe materiały. Żądania dotyczące dodatkowych materiałów szkoleniowych w centrum uczenia maszynowego można zamieszczać na [forum opinii użytkowników](https://windowsazure.uservoice.com/forums/257792-machine-learning).

Szkolenia można również znaleźć w witrynie [Microsoft Virtual Academy](http://www.microsoftvirtualacademy.com/training-courses/getting-started-with-microsoft-azure-machine-learning).

**Jak uzyskać pomoc techniczną dotyczącą usługi Azure Machine Learning?**

Aby uzyskać pomoc techniczną dotyczącą usługi Azure Machine Learning, przejdź do witryny [Pomoc techniczna platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję **Machine Learning**.

Dla usługi Azure Machine Learning udostępniono również forum społeczności w witrynie MSDN, gdzie można zadawać pytania dotyczące usługi Azure Machine Learning. Zespół ds. usługi Azure Machine Learning monitoruje forum. Przejdź do [Forum platformy Azure](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning).

## <a name="billing-questions"></a>Pytania dotyczące rozliczeń
**Jak działa rozliczanie w usłudze Machine Learning?**

Usługa Azure Machine Learning ma dwa składniki: usługę Machine Learning Studio i usługi sieci Web Machine Learning.

Podczas oceniania usługi Machine Learning Studio możesz korzystać z bezpłatnej warstwy rozliczeń, która pozwala na wdrażanie klasycznych usług sieci Web z ograniczoną funkcjonalnością.

Jeśli okaże się, że usługa Azure Machine Learning spełnia Twoje wymagania, możesz zasubskrybować warstwę Standardowa. W tym celu musisz uzyskać subskrypcję platformy Microsoft Azure.

W warstwie Standardowa opłata za użycie usługi Machine Learning Studio jest naliczana co miesiąc i zależy od liczby utworzonych obszarów roboczych. Opłata jest naliczana na podstawie zasobów obliczeniowych zużywanych na potrzeby eksperymentu uruchomionego w środowisku Studio. W przypadku wdrażania klasycznej usługi sieci Web transakcje i godziny obliczeniowe są rozliczane zgodnie z ich rzeczywistym użyciem.

W nowych usługach sieci Web (opartych na usłudze Resource Manager) wprowadzono plany rozliczeniowe, które zwiększają przewidywalność kosztów. Warstwy cenowe zapewniają możliwość korzystania z taryf rabatowych klientom, którzy potrzebują dużej wydajności.

Utworzenie planu wiąże się z podjęciem zobowiązania na stałą kwotę, w ramach którego można korzystać z wliczonej liczby transakcji i godzin obliczeniowych interfejsu API. Jeśli jest potrzebnych więcej transakcji, do planu można dodać wystąpienia. W przypadku konieczności dalszego zwiększenia ilości dostępnych zasobów można wybrać plan z wyższej warstwy, który zapewnia znacznie większą liczbę transakcji i oferuje korzystniejszą taryfę.

Po wyczerpaniu wliczonych transakcji w istniejących wystąpieniach opłata za użycie zasobów jest naliczana według nadwyżkowej stawki skojarzonej z warstwą rozliczeniową.

> [!NOTE]
Wliczone transakcje są ponownie przydzielane co 30 dni, a niewykorzystane zasoby nie przechodzą na następny okres.

Dodatkowe informacje o rozliczeniach i cenach zawiera temat [Machine Learning — cennik](https://azure.microsoft.com/pricing/details/machine-learning/).

**Czy istnieje bezpłatna wersja próbna usługi Machine Learning?**

 Usługa Azure Machine Learning ma opcję bezpłatnej subskrypcji, która została wyjaśniona w temacie [Machine Learning — cennik](https://azure.microsoft.com/pricing/details/machine-learning/). Usługa Machine Learning Studio ma wersję próbną do szybkiej oceny dostępną po zalogowaniu się do usługi [Machine Learning Studio](https://studio.azureml.net/?selectAccess=true&o=2).

 Ponadto gdy utworzysz konto umożliwiające uzyskanie bezpłatnej wersji próbnej platformy Azure, możesz przez miesiąc korzystać z dowolnych usług Azure. Aby dowiedzieć się więcej na temat bezpłatnej wersji próbnej platformy Azure, odwiedź stronę [Bezpłatna wersja próbna platformy Azure — często zadawane pytania](https://azure.microsoft.com/pricing/free-trial-faq/).

**Co to jest transakcja?**

Transakcja odpowiada wywołaniu interfejsu API, na które odpowiada usługa Azure Machine Learning. Opłata za zagregowane transakcje powiązane z wywołaniami usług RRS i BES jest naliczana zgodnie z planem rozliczeniowym.

**Czy mogę wykorzystać transakcje wliczone w ramach planu na potrzeby wywołań zarówno usług RRS, jak i BES?**

Tak. Transakcje powiązane z wywołaniami usług RRS i BES są agregowane, a opłata za ich używanie jest naliczana zgodnie z planem rozliczeniowym.

**Co to jest godzina obliczeniowa interfejsu API?**

Godzina obliczeniowa interfejsu API jest jednostką rozliczeniową czasu uruchomienia wywołań interfejsu API korzystających z zasobów obliczeniowych usługi Machine Learning. Wszystkie wywołania są agregowane na potrzeby rozliczeń.

**Jak długo trwa typowe wywołanie interfejsu API w środowisku produkcyjnym?**

Czas wywołania interfejsu API w środowisku produkcyjnym może znacznie się różnić, zazwyczaj w zakresie od kilkuset milisekund do kilku sekund. Niektóre wywołania interfejsu API mogą wymagać kilku minut w zależności od złożoności przetwarzania danych i modelu uczenia maszynowego. Najlepsza metoda oszacowania tego czasu polega na przeprowadzeniu testów porównawczych modelu za pomocą usługi Machine Learning.

**Co to jest godzina obliczeniowa środowiska Studio?**

Godzina obliczeniowa środowiska Studio jest jednostką rozliczeniową zagregowanego czasu używania zasobów obliczeniowych w ramach eksperymentów prowadzonych w środowisku Studio.

**Dla kogo jest przeznaczona warstwa tworzenia i testowania w nowych usługach sieci Web (opartych na usłudze Azure Resource Manager)?**

Usługi sieci Web oparte na usłudze Resource Manager udostępniają wiele warstw służących do aprowizacji planu rozliczeniowego. Warstwa cenowa tworzenia i testowania udostępnia ograniczoną liczbę wliczonych zasobów, które umożliwiają przetestowanie eksperymentu jako usługi sieci Web bez ponoszenia kosztów. Dzięki temu możesz sprawdzić przebieg eksperymentu.

**Czy są naliczane oddzielne opłaty za magazyn?**

Usługa Machine Learning w warstwie Bezpłatna nie wymaga osobnej przestrzeni dyskowej ani nie zezwala na korzystanie z niej. Usługa Machine Learning w warstwie Standardowa wymaga od użytkowników posiadania konta usługi Azure Storage. Opłata za korzystanie z usługi Azure Storage jest [naliczana oddzielnie](https://azure.microsoft.com/pricing/details/storage/).

**Czy usługa Machine Learning obsługuje wysoką dostępność?**

Tak. Aby uzyskać szczegółowe informacje, zobacz [Machine Learning — cennik](https://azure.microsoft.com/pricing/details/machine-learning/) z opisem umowy dotyczącej poziomu usług (SLA).

**Z jakiego konkretnego rodzaju zasobów obliczeniowych będą korzystać moje wywołania interfejsu API w środowisku produkcyjnym?**

Usługa Machine Learning to usługa wielodostępna. Faktyczne zasoby obliczeniowe używane na zapleczu różnią się i są zoptymalizowane pod kątem wydajności i przewidywalności.

### <a name="management-of-new-resource-manager-based-web-services"></a>Zarządzanie nowymi usługami sieci Web (opartymi na usłudze Resource Manager)
**Co się stanie w przypadku usunięcia planu?**

Plan zostanie usunięty z subskrypcji, a opłaty za użycie będą naliczane proporcjonalnie.

> [!NOTE]
Nie można usunąć planu, którego używa usługa sieci Web. Aby usunąć plan, musisz przypisać nowy plan do usługi sieci Web lub usunąć tę usługę.

**Co to jest wystąpienie planu?**

Wystąpienie planu jest jednostką wliczonych zasobów, które można dodać do planu rozliczeniowego. Wybranie warstwy rozliczeniowej dla planu powoduje dodanie jej do jednego wystąpienia. Jeśli jest potrzebnych więcej transakcji, do planu można dodać wystąpienia wybranej warstwy rozliczeniowej.

**Ile wystąpień planu mogę dodać?**

W ramach jednej subskrypcji możesz mieć jedno wystąpienie warstwy cenowej tworzenia i testowania.

W przypadku warstwy Standardowa S1, S2 i S3 możesz dodać tyle wystąpień, ile potrzebujesz.

> [!NOTE]
W zależności od przewidywanego użycia uaktualnienie do warstwy zapewniającej większą liczbę wliczonych zasobów może być bardziej opłacalne niż dodawanie wystąpień do bieżącej warstwy.

**Co się stanie w przypadku zmiany warstw planu (uaktualnienia bądź obniżenia poziomu)?**

Stary plan zostanie usunięty, a opłata za bieżące użycie zostanie naliczona proporcjonalnie. Zostanie utworzony nowy plan z kompletem wliczonych zasobów dostępnych w uaktualnionej lub obniżonej warstwie. Nowo utworzony plan będzie obowiązywał przez resztę okresu.

> [!NOTE]
Wliczone transakcje są przydzielane na podstawie okresu, a niewykorzystane zasoby nie przechodzą na następny okres.

**Co się stanie w przypadku zwiększenia liczby wystąpień w planie?**

Liczba wliczonych transakcji jest określana proporcjonalnie, a udostępnianie nowych zasobów może potrwać do 24 godzin.

**Co się stanie w przypadku usunięcia wystąpienia planu?**

Wystąpienie zostanie usunięte z subskrypcji, a opłaty za użycie będą naliczane proporcjonalnie.

### <a name="sign-up-for-new-resource-manager-based-web-services-plans"></a>Subskrypcja planów nowych usług sieci Web (opartych na usłudze Resource Manager)
**Jak zasubskrybować plan?**

Plany rozliczeniowe można tworzyć na dwa sposoby.

Przy pierwszym wdrożeniu usługi sieci Web opartej na usłudze Resource Manager możesz wybrać istniejący plan lub utworzyć nowy.

Plany tworzone w ten sposób znajdują się w domyślnym regionie, w którym zostanie również wdrożona usługa sieci Web.

Jeśli planujesz wdrażanie usług poza domyślnym regionem, warto zdefiniować plany rozliczeniowe przed wdrożeniem usługi.

Aby to zrobić, wystarczy zalogować się do portalu usług sieci Web Azure Machine Learning i przejść do strony planów. Pozwala ona dodawać, usuwać i modyfikować plany.

**Który plan wybrać na początek?**

Zalecamy rozpoczęcie od warstwy Standardowa S1 i monitorowanie użycia usługi. Jeśli okaże się, że zużycie wliczonych zasobów postępuje szybko, możesz dodać wystąpienia lub uaktualnić warstwę, uzyskując korzystniejszą taryfę. Plan rozliczeniowy można dostosowywać zgodnie z potrzebami w całym cyklu rozliczeniowym.

**W jakich regionach są dostępne nowe plany?**

Nowe plany rozliczeniowe są dostępne w trzech regionach produkcyjnych, w których są obsługiwane nowe usługi sieci Web:

* Środkowo-południowe stany USA
* Europa Zachodnia
* Azja Południowo-Wschodnia

**Moje usługi sieci Web działają w kilku regionach. Czy muszę mieć plan dla każdego regionu?**

Tak. Ceny planów zależą od regionu. Gdy wdrażasz usługę sieci Web w innym regionie, musisz przypisać do niej plan obsługiwany w tym regionie. Aby uzyskać więcej informacji, zobacz [Dostępność produktów według regionów]( https://azure.microsoft.com/regions/services/).

### <a name="new-web-services-overages"></a>Nowe usługi sieci Web: użycie nadwyżkowe
**Jak sprawdzić, czy przekroczono limit użycia usługi sieci Web?**

W portalu usług sieci Web Azure Machine Learning na stronie Plany można wyświetlić użycie usługi we wszystkich planach. Zaloguj się do portalu i kliknij opcję **Plany** w menu.

W kolumnach tabeli dotyczących **transakcji** i **zasobów obliczeniowych** są zawarte informacje o wartościach wliczonych w ramach planu oraz ich użyciu w ujęciu procentowym.

**Co się stanie po wyczerpaniu wliczonych wartości w warstwie cenowej tworzenia i testowania?**

Usługi z przypisaną warstwą cenową tworzenia i testowania zostaną zatrzymane do następnego okresu lub do momentu przeniesienia ich do dowolnej warstwy płatnej.

**Jak są naliczane ceny obciążeń RRS (Request Response) i BES (Batch) w przypadku klasycznych usług sieci Web oraz nadwyżkowego użycia nowych usług sieci Web (opartych na usłudze Resource Manager)?**

W przypadku obciążeń RRS opłata jest naliczana za każde wywołanie transakcji interfejsu API oraz czas obliczeń skojarzony z tymi żądaniami. Koszt transakcji RRS interfejsu API w środowisku produkcyjnym jest obliczany przez pomnożenie łącznej liczby wywołań interfejsu API przez cenę 1000 transakcji (proporcjonalnie do liczby indywidualnych transakcji). Koszt godzin obliczeniowych RRS interfejsu API w środowisku produkcyjnym jest obliczany przez pomnożenie czasu wymaganego do uruchomienia poszczególnych wywołań interfejsu API przez łączną liczbę transakcji interfejsu API oraz przez cenę godziny obliczeniowej interfejsu API w środowisku produkcyjnym.

Na przykład w ramach użycia nadwyżkowego w warstwie Standardowa S1 1 000 000 transakcji interfejsu API, z których każda trwa 0,72 s, spowoduje naliczenie opłaty 500 USD (1 000 000 * 0,50 USD/1000 transakcji interfejsu API) z tytułu kosztów transakcji interfejsu API w środowisku produkcyjnym oraz 400 USD (1 000 000 * 0,72 s * 2 USD / godz.) z tytułu godzin obliczeniowych interfejsu API w środowisku produkcyjnym, co daje łączną kwotę 900 USD.

W przypadku obciążeń usługi BES opłata jest naliczana w ten sam sposób. Niemniej koszt transakcji interfejsu API odpowiada liczbie przesłanych zadań wsadowych, a koszt obliczeń odpowiada czasowi obliczeń skojarzonemu z tymi zadaniami wsadowymi. Z tego względu koszt transakcji interfejsu API usługi BES w środowisku produkcyjnym jest obliczany przez pomnożenie łącznej liczby przesłanych zadań przez cenę 1000 transakcji (proporcjonalnie do liczby indywidualnych transakcji). Koszt godzin obliczeniowych interfejsu API usługi BES w środowisku produkcyjnym jest obliczany przez pomnożenie czasu wymaganego do uruchomienia poszczególnych wierszy w zadaniu przez łączną liczbę wierszy w zadaniu przez łączną liczbę zadań oraz przez cenę godziny obliczeniowej interfejsu API w środowisku produkcyjnym. Podczas korzystania z kalkulatora usługi Machine Learning licznik transakcji reprezentuje liczbę zadań, które zamierzasz przesłać, a wartość w polu czasu transakcji reprezentuje łączny czas wymagany do uruchomienia wszystkich wierszy w poszczególnych zadaniach.

Jeśli w ramach użycia nadwyżkowego w warstwie Standardowa S1 przesyłasz na przykład 100 zadań dziennie, z których każde zawiera 500 wierszy o czasie uruchamiania 0,72 s, miesięczny koszt nadwyżkowych transakcji interfejsu API w środowisku produkcyjnym wyniesie 1,55 USD (100 zadań dziennie = 3100 zadań/mies. * 0,50 USD/1000 transakcji interfejsu API), a koszt godzin obliczeniowych interfejsu API w środowisku produkcyjnym wyniesie 620 USD (500 wierszy * 0,72 s * 3100 zadań * 2 USD/godz.), co daje łączną kwotę 621,55 USD.

### <a name="azure-machine-learning-classic-web-services"></a>Klasyczne usługi sieci Web Azure Machine Learning
**Czy model płatności zgodnie z rzeczywistym użyciem jest wciąż dostępny?**

Tak, klasyczne usługi sieci Web są wciąż dostępne w usłudze Azure Machine Learning.  

### <a name="azure-machine-learning-free-and-standard-tier"></a>Usługa Azure Machine Learning w warstwie Bezpłatna i Standardowa
**Co obejmuje usługa Azure Machine Learning w warstwie Bezpłatna?**

Usługa Azure Machine Learning w warstwie Bezpłatna zapewnia szczegółowe wprowadzenie do usługi Azure Machine Learning Studio. Do korzystania z tej usługi wystarczy konto Microsoft. Warstwa Bezpłatna umożliwia bezpłatne korzystanie z jednego obszaru roboczego usługi Azure Machine Learning Studio w ramach [konta Microsoft](https://account.microsoft.com/account). W tej warstwie możesz użyć do 10 GB pamięci i operacjonalizować modele jako tymczasowe interfejsy API. Obciążenia warstwy Bezpłatna nie są objęte umową SLA i są przeznaczone tylko do użytku osobistego i do opracowywania rozwiązań. 

Obszary robocze warstwy Bezpłatna mają następujące ograniczenia:

* Obciążenia nie mogą uzyskiwać dostępu do danych, łącząc się z lokalnym serwerem z programem SQL Server.
* Nie można wdrażać nowych podstawowych usług sieci Web usługi Resource Manager.


**Co obejmują plany usługi Azure Machine Learning w warstwie Standardowa?**

Usługa Azure Machine Learning w warstwie Standardowa to płatna wersja produkcyjna usługi Azure Machine Learning Studio. Opłata miesięczna za usługę Azure Machine Learning Studio jest naliczana na podstawie liczby obszarów roboczych i proporcjonalnie w przypadku niepełnych miesięcy. Godziny prowadzenia eksperymentów w usłudze Azure Machine Learning Studio są rozliczane na podstawie godzin obliczeniowych w ramach aktywnych eksperymentów. W przypadku niepełnych godzin opłaty są naliczane proporcjonalnie.  

Sposób rozliczania korzystania z usługi Azure Machine Learning API zależy od tego, czy jest to klasyczna czy nowa usługa sieci Web (oparta na usłudze Resource Manager).

Następujące opłaty są agregowane dla danego obszaru roboczego w ramach subskrypcji.

* Subskrypcja obszarów roboczych usługi Machine Learning: subskrypcja obszarów roboczych usługi Machine Learning to miesięczna opłata, która zapewnia dostęp do obszaru roboczego usługi Machine Learning Studio. Subskrypcja jest wymagana do uruchamiania eksperymentów w środowisku Studio oraz korzystania z interfejsów API środowiska produkcyjnego.
* Godziny prowadzenia eksperymentów w Studio: ten licznik agreguje wszystkie opłaty za zasoby obliczeniowe naliczane w wyniku przeprowadzania eksperymentów w usłudze Machine Learning Studio i wykonywania wywołań produkcyjnych interfejsów API w środowisku przejściowym.
* Dostęp do danych przez połączenie z lokalnym serwerem z programem SQL Server w ramach modeli na potrzeby szkolenia i oceniania.
* W przypadku klasycznych usług sieci Web:
  * Godziny obliczeń dla interfejsu API produkcji: ten licznik obejmuje opłaty za zasoby obliczeniowe naliczane przez usługi sieci Web w środowisku produkcyjnym.
  * Transakcje obliczeń dla interfejsu API produkcji (w 1000): ten licznik obejmuje opłaty naliczane za wywołania produkcyjnej usługi sieci Web.

W przypadku usług sieci Web opartych na usłudze Resource Manager opłaty są dodatkowo agregowane zgodnie z wybranym planem:

* Plan interfejsu API w warstwie Standardowa S1/S2/S3 (jednostki): ten licznik reprezentuje typ wystąpienia wybranego dla usług sieci Web opartych na usłudze Resource Manager.
* Nadwyżkowe godziny obliczeniowe interfejsu API w warstwie Standardowa S1/S2/S3: ten licznik obejmuje opłaty za zasoby obliczeniowe naliczane z tytułu uruchomienia usług sieci Web opartych na usłudze Resource Manager w środowisku produkcyjnym po zużyciu wliczonych wartości w istniejących wystąpieniach. Opłata za dodatkowe użycie jest naliczana zgodnie z nadwyżkową stawką skojarzoną z warstwą planów S1/S2/S3.
* Nadwyżkowe transakcje interfejsu API w warstwie Standardowa S1/S2/S3 (w 1000): ten licznik obejmuje opłaty naliczane za wywołania usług sieci Web opartych na usłudze Resource Manager w środowisku produkcyjnym po zużyciu wliczonych wartości w istniejących wystąpieniach. Opłata za dodatkowe użycie jest naliczana zgodnie z nadwyżkową stawką skojarzoną z warstwą planów S1/S2/S3.
* Godziny obliczeniowe interfejsu API w uwzględnionej ilości: w przypadku usług sieci Web opartych na usłudze Resource Manager ten licznik obejmuje uwzględnioną ilość godzin obliczeniowych interfejsu API.
* Transakcje interfejsu API w uwzględnionej ilości (w 1000): w przypadku usług sieci Web opartych na usłudze Resource Manager ten licznik obejmuje uwzględnioną ilość transakcji interfejsu API.

**Jak zasubskrybować usługę Azure Machine Learning w warstwie Bezpłatna?**

Do korzystania z tej usługi wystarczy konto Microsoft. Przejdź do [strony głównej usługi Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) i kliknij przycisk **Rozpocznij teraz**. Zaloguj się przy użyciu konta Microsoft — zostanie utworzony obszar roboczy w warstwie Bezpłatna. Możesz od razu zacząć przeglądać i tworzyć eksperymenty usługi Machine Learning.

**Jak zasubskrybować usługę Azure Machine Learning w warstwie Standardowa?**

Aby utworzyć obszar roboczy usługi Machine Learning w warstwie standardowej, musisz mieć dostęp do subskrypcji platformy Azure. Możesz utworzyć konto umożliwiające subskrypcję 30-dniowej bezpłatnej wersji próbnej platformy Azure i później wykonać uaktualnienie do płatnej subskrypcji lub od razu wykupić płatną subskrypcję platformy Azure. Po uzyskaniu dostępu do subskrypcji będzie można utworzyć obszar roboczy usługi Machine Learning w witrynie Microsoft Azure Portal. Zobacz [szczegółowe instrukcje](https://azure.microsoft.com/trial/get-started-machine-learning-b/).

Dostęp do obszaru roboczego usługi Machine Learning w warstwie standardowej możesz też uzyskać za pomocą zaproszenia od właściciela tego obszaru roboczego.

**Czy w ramach warstwy Bezpłatna można używać własnego konta usługi Azure Blob Storage?**

Nie. Warstwa Standardowa odpowiada wersji usługi Machine Learning, która była dostępna przed wprowadzeniem warstw.

**Czy można wdrażać modele uczenia maszynowego jako interfejsy API w ramach warstwy Bezpłatna?**

Tak. Można zoperacjonalizować modele uczenia maszynowego w tymczasowych usługach interfejsu API w ramach warstwy Bezpłatna. Aby wprowadzić przejściową usługę interfejsu API do produkcji i uzyskać produkcyjny punkt końcowy zoperacjonalizowanej usługi, musisz używać warstwy Standardowa.

**Czym różni się bezpłatna wersja próbna platformy Azure i usługa Azure Machine Learning w warstwie Bezpłatna?**

[Bezpłatna wersja próbna platformy Microsoft Azure](https://azure.microsoft.com/free/) udostępnia środki, które można stosować do dowolnej usługi platformy Azure przez miesiąc. Usługa Azure Machine Learning w warstwie Bezpłatna oferuje ciągły dostęp do usługi Azure Machine Learning w przypadku obciążeń nieprodukcyjnych.

**Jak przenieść eksperyment z warstwy Bezpłatna do warstwy Standardowa?**

Aby skopiować eksperymenty z warstwy Bezpłatna do warstwy Standardowa:

1. Zaloguj się w usłudze Azure Machine Learning Studio i upewnij się, że w selektorze obszaru roboczego na górnym pasku nawigacyjnym są widoczne obszary robocze warstw Bezpłatna i Standardowa.
2. Jeśli obecnie jest używany obszar roboczy warstwy Standardowa, przełącz się do obszaru roboczego warstwy Bezpłatna.
3. W widoku listy eksperymentów wybierz eksperyment, który chcesz skopiować, a następnie kliknij przycisk polecenia **Kopiuj**.
4. W otwartym oknie dialogowym wybierz obszar roboczy warstwy Standardowa i kliknij przycisk **Kopiuj**.
   Wszystkie skojarzone zestawy danych, nauczone modele itp. zostaną skopiowane razem z eksperymentem do obszaru roboczego warstwy Standardowa.
5. Konieczne będzie ponowne uruchomienie eksperymentu i ponowne opublikowanie usługi sieci Web w obszarze roboczym warstwy Standardowa.

### <a name="studio-workspace"></a>Obszar roboczy Studio
**Czy otrzymam różne rachunki za różne obszary robocze?**

Opłaty za obszar roboczy są naliczane oddzielnie za każdy mający zastosowanie licznik i przedstawiane na jednym rachunku.

**Z jakiego konkretnego rodzaju zasobów obliczeniowych będą korzystać moje eksperymenty?**

Usługa Machine Learning to usługa wielodostępna. Faktyczne zasoby obliczeniowe używane na zapleczu różnią się i są zoptymalizowane pod kątem wydajności i przewidywalności.

### <a name="guest-access"></a>Dostęp gościa
**Co to jest dostęp gościa w usłudze Azure Machine Learning Studio?**

Dostęp gościa jest ograniczoną wersją próbną środowiska użytkownika. Możesz tworzyć i uruchamiać eksperymenty w usłudze Azure Machine Learning Studio bezpłatnie i bez uwierzytelniania. Sesje gościa są nietrwałe (nie można ich zapisać), a ich czas trwania jest ograniczony do ośmiu godzin. Pozostałe ograniczenia to brak obsługi języków R i Python, brak przejściowych interfejsów API oraz ograniczony rozmiar zestawów danych i przestrzeni dyskowej. Natomiast użytkownicy logujący się przy użyciu konta Microsoft mają pełny dostęp do warstwy Bezpłatna usługi Machine Learning Studio opisanej powyżej, udostępniającej trwały obszar roboczy i większe możliwości. Aby wybrać bezpłatną wersję usługi Machine Learning, kliknij przycisk **Rozpocznij** na stronie [https://studio.azureml.net](https://studio.azureml.net) i wybierz **Dostęp gościa** lub zaloguj się przy użyciu konta Microsoft.

<!-- Module References -->
[image-reader]: https://msdn.microsoft.com/library/azure/893f8c57-1d36-456d-a47b-d29ae67f5d84/
[join]: https://msdn.microsoft.com/library/azure/124865f7-e901-4656-adac-f4cb08248099/
[machine-learning-modules]: https://msdn.microsoft.com/library/azure/6d9e2516-1343-4859-a3dc-9673ccec9edc/
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[python]: https://msdn.microsoft.com/library/azure/CDB56F95-7F4C-404D-BDE7-5BB972E6F232
[counts]: https://msdn.microsoft.com/library/azure/dn913056.aspx
