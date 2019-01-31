---
title: Usługa Machine Learning Studio — często zadawane pytania (FAQ)
titleSuffix: Azure Machine Learning Studio
description: 'Usługa Azure Machine Learning Studio: Często zadawane pytania dotyczące rozliczeń, możliwości i ograniczeń usługi w chmurze do sprawnego modelowania predykcyjnego.'
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 06/02/2017
ms.openlocfilehash: 8c381ca02dcfb194b2807735415ef894d72f74fa
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55462283"
---
# <a name="azure-machine-learning-studio-faq-capabilities-and-limitations"></a>Usługi Azure Machine Learning Studio — często zadawane pytania: możliwości i ograniczeń
Przedstawione tutaj często zadawane pytania i odpowiedzi dotyczą usługi Azure Machine Learning, która jest usługą w chmurze przeznaczoną do tworzenia modeli predykcyjnych i rozwiązań operacyjnych za pośrednictwem usług sieci Web. 

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

* Usługa odpowiedzi na żądanie (RRS): O niskich opóźnieniach i wysoce skalowalna usługa, która udostępnia interfejs dla bezstanowych modeli utworzonych i wdrożonych przy użyciu usługi Machine Learning Studio.
* Usługa wykonywania wsadowego (BES): Asynchroniczna Usługa przeznaczona do oceniania partii rekordów danych.

Istnieje kilka sposobów uzyskiwania dostępu do usługi sieci Web za pomocą interfejsu API REST. Można na przykład napisać aplikację w języku C#, R lub Python korzystającą z przykładowego kodu automatycznie wygenerowanego podczas wdrażania usługi sieci Web.

Kod przykładowy można znaleźć na:
- stronie dotyczącej wykorzystania danej usługi sieci Web w portalu usług sieci Web Azure Machine Learning,
- stronie pomocy interfejsu API na pulpicie nawigacyjnym usługi sieci Web w usłudze Machine Learning Studio.

Można też użyć automatycznie utworzonego przykładowego skoroszytu programu Microsoft Excel dostępnego na pulpicie nawigacyjnym usługi sieci Web w usłudze Machine Learning Studio.

**Jakie są najważniejsze aktualności dotyczące usługi Azure Machine Learning?**

Aby uzyskać informacje o najnowszych aktualizacjach, zobacz [Co nowego w usłudze Azure Machine Learning](../../active-directory/fundamentals/whats-new.md).

## <a name="import-and-export-data-for-machine-learning"></a>Importowanie i eksportowanie danych na potrzeby usługi Machine Learning
**Jakie źródła danych obsługuje usługa Machine Learning?**

Dostępne są trzy sposoby pobierania danych do eksperymentu usługi Machine Learning Studio:

- Przekazanie pliku lokalnego jako zestawu danych.
- Użycie modułu do zaimportowania danych z usług danych w chmurze.
- Zaimportowanie zestawu danych zapisanego w innym eksperymencie.

Aby dowiedzieć się więcej na temat obsługiwanych formatów plików, zobacz temat [Import training data into Machine Learning Studio](import-data.md) (Importowanie danych szkoleniowych do środowiska usługi Machine Learning Studio).

### <a id="ModuleLimit"></a>Jak duży może być zestaw danych dla moich modułów?
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

### <a id="UploadLimit"></a>Jakie są ograniczenia przekazywania danych?
W przypadku zestawów danych o rozmiarach większych niż kilka GB dane należy przekazać do usługi Azure Storage lub usługi Azure SQL Database albo użyć usługi Azure HDInsight, zamiast przekazywać dane bezpośrednio z pliku lokalnego.

**Czy mogę odczytywać dane z usługi Amazon S3?**

Jeśli masz niewielką ilość danych i chcesz je ujawnić za pośrednictwem adresu URL HTTP, możesz użyć modułu [Import danych][import-data]. W przypadku większych ilości danych należy najpierw przenieść dane do usługi Azure Storage, a następnie użyć modułu [Import danych][import-data], aby wprowadzić dane do eksperymentu.
<!--

<SEE CLOUD DS PROCESS>
-->

**Czy istnieje wbudowana możliwość wprowadzania obrazów?**

Informacje na temat możliwości wprowadzania obrazu są dostępne w temacie [Import Images][image-reader] (Import obrazów).

## <a name="modules"></a>Moduły
**W środowisku usługi Azure Machine Learning Studio nie ma poszukiwanego przeze mnie algorytmu, źródła danych, formatu danych lub szukanej operacji transformacji danych. Jakie są moje opcje?**

Możesz przejść do [forum opinii użytkowników](https://go.microsoft.com/fwlink/?LinkId=404231), aby zobaczyć żądania funkcji, które śledzimy. Jeśli pojawiło się już żądanie dotyczące możliwości, której poszukujesz, zagłosuj na to żądanie. Jeśli możliwość, której poszukujesz, nie istnieje, utwórz nowe żądanie. Na tym forum możesz również sprawdzić stan swojego żądania. Ściśle śledzimy tę listę i często aktualizujemy stan dostępności funkcji. Ponadto możesz użyć wbudowanej obsługi języków R i Python, aby utworzyć niestandardowe przekształcenia wedle potrzeby.

**Czy mogę przenieść mój istniejący kod do środowiska usługi Machine Learning Studio?**

Tak. Możesz przenieść istniejący kod w języku R lub Python do środowiska usługi Azure Machine Learning Studio, uruchomić go w tym samym eksperymencie co procesy uczące usługi Azure Machine Learning, a następnie wdrożyć rozwiązanie jako usługę sieci web za pośrednictwem usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz tematy [Extend your experiment with R](extend-your-experiment-with-r.md) (Rozszerzanie eksperymentu, korzystając z języka R) i [Execute Python machine learning scripts in Azure Machine Learning Studio](execute-python-scripts.md) (Wykonywanie skryptów uczenia maszynowego w języku Python w usłudze Azure Machine Learning Studio).

**Czy w celu definiowania modeli można używać języków, takich jak [PMML](http://en.wikipedia.org/wiki/Predictive_Model_Markup_Language)?**

Nie, język PMML (Predictive Model Markup Language) nie jest obsługiwany. Możesz użyć niestandardowego kodu języka R lub Python, aby zdefiniować moduł.

**Jak wiele modułów mogę równolegle uruchomić w moim eksperymencie?**  

Równolegle w eksperymencie można uruchomić maksymalnie cztery moduły.

## <a name="data-processing"></a>Przetwarzanie danych
**Czy istnieje możliwość interaktywnej wizualizacji danych (poza wizualizacjami języka R) w ramach eksperymentu?**

Kliknij wyjście modułu, aby zwizualizować dane i uzyskać statystyki.

**Podczas przeglądania wyników lub danych w przeglądarce liczba wierszy i kolumn jest ograniczona. Dlaczego?**

Do przeglądarki mogą być wysyłane duże ilości danych, dlatego rozmiar danych jest ograniczony, aby zapobiegać spowalnianiu usługi Machine Learning Studio. W celu zwizualizowania wszystkich danych/wyników lepiej jest pobrać dane i użyć programu Excel lub innego narzędzia.

## <a name="algorithms"></a>Algorytmy
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

## <a name="r-module"></a>Moduł R
**Jakie pakiety języka R są dostępne w usłudze Machine Learning Studio?**

Usługa Machine Learning Studio obsługuje obecnie ponad 400 pakietów języka R z sieci CRAN, a tutaj jest [aktualna lista](http://az754797.vo.msecnd.net/docs/RPackages.xlsx) wszystkich dostępnych pakietów. Zobacz też temat [Extend your experiment with R](extend-your-experiment-with-r.md) (Rozszerz swój eksperyment, korzystając z języka R), aby dowiedzieć się, jak pobrać tę listę samodzielnie. Jeśli żądanego pakietu nie ma na tej liście, należy podać nazwę pakietu na [forum opinii użytkowników](https://go.microsoft.com/fwlink/?LinkId=404231).

**Czy jest możliwe utworzenie niestandardowego modułu R?**

Tak. Zobacz temat [Author custom R modules in Azure Machine Learning](custom-r-modules.md) (Tworzenie niestandardowych modułów R w usłudze Azure Machine Learning).

**Czy dostępne jest środowisko REPL dla języka R?**

Nie. W studio nie ma środowiska REPL (Read-Eval-Print-Loop) dla języka R.

## <a name="python-module"></a>Moduł Python
**Czy jest możliwe utworzenie niestandardowego modułu Python?**

Obecnie nie jest to możliwe, ale można użyć dowolnej liczby modułów [Wykonanie skryptu Python][python] w celu uzyskania tego samego wyniku.

**Czy dostępne jest środowisko REPL dla języka Python?**

W usłudze Machine Learning Studio można skorzystać z notesów Jupyter. Aby uzyskać więcej informacji, zobacz temat [Manage experiment iterations in Machine Learning Studio](http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx) (Zarządzanie iteracjami eksperymentów w usłudze Machine Learning Studio).

## <a name="web-service"></a>Usługa sieci Web

**W jaki sposób ponownie trenować modele usługi Azure Machine Learning programowo?**

Użyj interfejsów API do ponownego trenowania. Aby uzyskać więcej informacji, zobacz temat [Retrain Machine Learning models programmatically](retrain-models-programmatically.md) (Ponowne trenowanie modeli uczenia maszynowego programowo). Przykładowy kod jest również dostępny w [demonstracji ponownego trenowania w usłudze Microsoft Azure Machine Learning](https://azuremlretrain.codeplex.com/).

**Czy mogę wdrożyć model lokalnie lub w aplikacji bez połączenia z Internetem?**

Nie.

**Czy istnieje opóźnienie bazowe oczekiwane dla wszystkich usługi sieci Web?**

Zobacz [limity subskrypcji platformy Azure](../../azure-subscription-service-limits.md).

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

Wiersze i kolumny są ograniczone zgodnie z ograniczeniem .NET dla maksymalnej liczby całkowitej każdego: 2,147,483,647.

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


## <a name="billing-questions"></a>Pytania dotyczące rozliczeń

Informacje o rozliczeniach i cenach zawiera temat [Machine Learning — cennik](https://azure.microsoft.com/pricing/details/machine-learning/).


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
