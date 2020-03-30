---
title: Zarządzanie cyklem życia aplikacji
titleSuffix: ML Studio (classic) - Azure
description: Stosowanie najlepszych rozwiązań zarządzania cyklem życia aplikacji w usłudze Azure Machine Learning Studio (klasyczny)
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 10/27/2016
ms.openlocfilehash: 3f22ce3b1fb750e33e35d35ee1fe5ad1893abcfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204140"
---
# <a name="application-lifecycle-management-in-azure-machine-learning-studio-classic"></a>Zarządzanie cyklem życia aplikacji w usłudze Azure Machine Learning Studio (klasyczny)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Usługa Azure Machine Learning Studio (klasyczna) to narzędzie do tworzenia eksperymentów uczenia maszynowego, które są operationalized na platformie chmury platformy Azure. Jest jak IDE programu Visual Studio i skalowalnej usługi w chmurze scalone w jednej platformie. Można włączyć standardowe praktyki zarządzania cyklem życia aplikacji (ALM) od przechowywania różnych zasobów do automatycznego wykonywania i wdrażania, do usługi Azure Machine Learning Studio (classic). W tym artykule omówiono niektóre opcje i podejścia.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="versioning-experiment"></a>Eksperyment przechowywania wersji
Istnieją dwa zalecane sposoby przechowywania wersji eksperymentów. Można polegać na wbudowanej historii uruchamiania lub eksportować eksperyment w formacie JSON, aby zarządzać nim zewnętrznie. Każde podejście ma swoje plusy i minusy.

### <a name="experiment-snapshots-using-run-history"></a>Migawki eksperymentów przy użyciu historii uruchamiania
W modelu wykonywania eksperymentu uczenia usługi Azure Machine Learning Studio (klasyczne) niezmienne migawki eksperymentu jest przesyłany do harmonogramu zadań, gdy klikniesz **przycisk Uruchom** w edytorze eksperymentów. Aby wyświetlić tę listę migawek, kliknij pozycję **Uruchom historię** na pasku poleceń w widoku edytora eksperymentów.

![Przycisk Uruchom historię](./media/version-control/runhistory.png)

Następnie można otworzyć migawkę w trybie zablokowanym, klikając nazwę eksperymentu w momencie przesłania eksperymentu do uruchomienia i zrobiono migawkę. Należy zauważyć, że tylko pierwszy element na liście, który reprezentuje bieżący eksperyment, jest w stanie edytowalnym. Należy również zauważyć, że każda migawka może być w różnych stanach stanu, jak również, w tym zakończone (częściowe uruchomienie), Nie powiodło się, Nie powiodło się (częściowe uruchomienie) lub Wersja robocza.

![Uruchom listę historii](./media/version-control/runhistorylist.png)

Po jego otwarciu można zapisać eksperyment migawki jako nowy eksperyment, a następnie zmodyfikować go. Jeśli migawka eksperymentu zawiera zasoby, takie jak modele uczone, przekształcenia lub zestawy danych, które mają zaktualizowane wersje, migawka zachowuje odwołania do oryginalnej wersji podczas robienia migawki. Jeśli zapiszesz zablokowaną migawkę jako nowy eksperyment, usługa Azure Machine Learning Studio (klasyczna) wykryje istnienie nowszej wersji tych zasobów i automatycznie zaktualizuje je w nowym eksperymencie.

Jeśli usuniesz eksperyment, wszystkie migawki tego eksperymentu zostaną usunięte.

### <a name="exportimport-experiment-in-json-format"></a>Eksperyment eksportu/importu w formacie JSON
Migawki historii uruchamiania zachowują niezmienną wersję eksperymentu w usłudze Azure Machine Learning Studio (klasyczna) za każdym razem, gdy jest przesyłana do uruchomienia. Można również zapisać lokalną kopię eksperymentu i zaewidencjonować go do ulubionego systemu kontroli źródła, takiego jak Team Foundation Server, a później ponownie utworzyć eksperyment z tego pliku lokalnego. Aby to osiągnąć, można użyć poleceń [programu Azure Machine Learning PowerShell](https://aka.ms/amlps) [*Export-AmlExperimentGraph*](https://github.com/hning86/azuremlps#export-amlexperimentgraph) i [*Import-AmlExperimentGraph.*](https://github.com/hning86/azuremlps#import-amlexperimentgraph)

Plik JSON jest tekstową reprezentacją wykresu eksperymentu, która może zawierać odwołanie do zasobów w obszarze roboczym, takich jak zestaw danych lub przeszkolony model. Nie zawiera serializowanej wersji zasobu. Jeśli spróbujesz zaimportować dokument JSON z powrotem do obszaru roboczego, zasoby, do których istnieje odwołanie, muszą już istnieć z tymi samymi identyfikatorami zasobów, do których odwołuje się eksperyment. W przeciwnym razie nie można uzyskać dostępu do importowanego eksperymentu.

## <a name="versioning-trained-model"></a>Model uczony w wersji
Przeszkolony model w usłudze Azure Machine Learning Studio (klasyczny) jest serializowany`.iLearner`do formatu znanego jako plik iLearner ( ) i jest przechowywany na koncie magazynu obiektów Blob platformy Azure skojarzonym z obszarem roboczym. Jednym ze sposobów, aby uzyskać kopię pliku iLearner jest za pośrednictwem interfejsu API ponownego szkolenia. [W tym artykule](/azure/machine-learning/studio/retrain-machine-learning-model) wyjaśniono, jak działa interfejs API ponownego szkolenia. Kroki wysokiego poziomu:

1. Skonfiguruj eksperyment szkoleniowy.
2. Dodaj port wyjściowy usługi sieci web do modułu Train Model lub modułu, który tworzy przeszkolony model, taki jak Tune Model Hyperparameter lub Create R Model.
3. Uruchom eksperyment szkoleniowy, a następnie wdrożyć go jako usługę sieci web szkolenia modelu.
4. Wywołanie punktu końcowego BES usługi sieci web szkolenia i określ żądaną nazwę pliku iLearner i lokalizację konta magazynu obiektów Blob, gdzie będą przechowywane.
5. Zbierz wyprodukowany plik iLearner po zakończeniu połączenia BES.

Innym sposobem na pobranie pliku iLearner jest polecenie programu PowerShell [*Download-AmlExperimentNodeOutput*](https://github.com/hning86/azuremlps#download-amlexperimentnodeoutput). Może to być łatwiejsze, jeśli chcesz po prostu uzyskać kopię pliku iLearner bez konieczności ponownego trenowania modelu programowo.

Po pliku iLearner zawierającym przeszkolony model możesz zastosować własną strategię przechowywania wersji. Strategia może być tak proste, jak stosowanie pre/postfix jako konwencji nazewnictwa i po prostu pozostawiając plik iLearner w magazynie obiektów Blob lub kopiowanie / importowanie go do systemu kontroli wersji.

Zapisany plik iLearner może następnie służyć do oceniania za pośrednictwem wdrożonych usług sieci web.

## <a name="versioning-web-service"></a>Usługa sieci web przechowywania wersji
Można wdrożyć dwa typy usług sieci web z eksperymentu usługi Azure Machine Learning Studio (klasyczny). Klasyczna usługa sieci web jest ściśle połączona z eksperymentem, a także z obszarem roboczym. Nowa usługa sieci web korzysta z platformy Azure Resource Manager i nie jest już w połączeniu z oryginalnego eksperymentu lub obszaru roboczego.

### <a name="classic-web-service"></a>Klasyczna usługa internetowa
Aby wersja klasycznej usługi sieci web, można skorzystać z konstrukcji punktu końcowego usługi sieci web. Oto typowy przepływ:

1. W eksperymencie predykcyjnym wdrażasz nową klasyczną usługę sieci web, która zawiera domyślny punkt końcowy.
2. Tworzenie nowego punktu końcowego o nazwie ep2, który udostępnia bieżącą wersję eksperymentu/uczony model.
3. Wracasz i aktualizujesz swój eksperyment predykcyjny i przeszkolony model.
4. Ponownie wdrożyć eksperyment predykcyjny, który następnie zaktualizuje domyślny punkt końcowy. Ale to nie zmieni ep2.
5. Utworzyć dodatkowy punkt końcowy o nazwie ep3, który udostępnia nową wersję eksperymentu i przeszkolony model.
6. W razie potrzeby wróć do kroku 3.

Z biegiem czasu może mieć wiele punktów końcowych utworzonych w tej samej usłudze sieci web. Każdy punkt końcowy reprezentuje kopię punktu w czasie eksperymentu zawierającą wersję w czasie uczonego modelu. Następnie można użyć logiki zewnętrznej, aby określić, który punkt końcowy do wywołania, co skutecznie oznacza wybranie wersji modelu przeszkolonego dla przebiegu oceniania.

Można również utworzyć wiele identycznych punktów końcowych usługi sieci web, a następnie załatać różne wersje pliku iLearner do punktu końcowego, aby osiągnąć podobny efekt. [W tym artykule](create-models-and-endpoints-with-powershell.md) opisano bardziej szczegółowo, jak to osiągnąć.

### <a name="new-web-service"></a>Nowa usługa internetowa
Jeśli utworzysz nową usługę sieci web opartą na usłudze Azure Resource Manager, konstrukcja punktu końcowego nie będzie już dostępna. Zamiast tego można wygenerować pliki definicji usługi sieci web (WSD) w formacie JSON z eksperymentu predykcyjnego przy użyciu polecenia [Export-AmlWebServiceDefinitionFromExperiment](https://github.com/hning86/azuremlps#export-amlwebservicedefinitionfromexperiment) PowerShell lub przy użyciu polecenia [*Export-AzMlWebservice*](https://docs.microsoft.com/powershell/module/az.machinelearning/export-azmlwebservice) PowerShell z wdrożonej usługi sieci web opartej na Menedżerze zasobów.

Po wyeksportowanym pliku WSD i kontroli wersji można również wdrożyć wyeksportowany plik WSD jako nową usługę sieci web w innym planie usługi sieci web w innym regionie platformy Azure. Upewnij się tylko, że podasz odpowiednią konfigurację konta magazynu, a także nowy identyfikator planu usługi sieci web. Aby załatać w różnych plikach iLearner, można zmodyfikować plik WSD i zaktualizować odwołanie do lokalizacji przeszkolonego modelu i wdrożyć go jako nową usługę sieci web.

## <a name="automate-experiment-execution-and-deployment"></a>Automatyzacja wykonywania i wdrażania eksperymentów
Ważnym aspektem ALM jest możliwość automatyzacji procesu wykonywania i wdrażania aplikacji. W usłudze Azure Machine Learning Studio (klasycznym) można to osiągnąć za pomocą [modułu programu PowerShell.](https://aka.ms/amlps) Oto przykład kroków end-to-end, które są istotne dla standardowego procesu automatycznego wykonywania/wdrażania ALM przy użyciu [modułu Azure Machine Learning Studio (klasyczny) PowerShell.](https://aka.ms/amlps) Każdy krok jest połączony z co najmniej jednym poleceniem programu PowerShell, których można użyć do wykonania tego kroku.

1. [Prześlij zestaw danych](https://github.com/hning86/azuremlps#upload-amldataset).
2. Skopiuj eksperyment szkoleniowy do obszaru roboczego z [obszaru roboczego](https://github.com/hning86/azuremlps#copy-amlexperiment) lub z [galerii](https://github.com/hning86/azuremlps#copy-amlexperimentfromgallery)lub [zaimportuj](https://github.com/hning86/azuremlps#import-amlexperimentgraph) [eksportowany](https://github.com/hning86/azuremlps#export-amlexperimentgraph) eksperyment z dysku lokalnego.
3. [Zaktualizuj zestaw danych](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) w eksperymencie szkoleniowym.
4. [Uruchom eksperyment szkoleniowy](https://github.com/hning86/azuremlps#start-amlexperiment).
5. [Promuj wyszkolony model](https://github.com/hning86/azuremlps#promote-amltrainedmodel).
6. [Skopiuj eksperyment predykcyjny](https://github.com/hning86/azuremlps#copy-amlexperiment) do obszaru roboczego.
7. [Zaktualizuj przeszkolony model](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) w eksperymencie predykcyjnym.
8. [Uruchom eksperyment predykcyjny](https://github.com/hning86/azuremlps#start-amlexperiment).
9. [Wdrażanie usługi sieci web](https://github.com/hning86/azuremlps#new-amlwebservice) z eksperymentu predykcyjnego.
10. Przetestuj usługę sieci web [RRS](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) lub punkt końcowy [BES.](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint)

## <a name="next-steps"></a>Następne kroki
* Pobierz moduł [programu PowerShell usługi Azure Machine Learning Studio (klasyczny)](https://aka.ms/amlps) i zacznij automatyzować zadania usługi ALM.
* Dowiedz się, jak [tworzyć dużą liczbę modeli uczenia maszynowego i zarządzać nimi przy użyciu tylko jednego eksperymentu](create-models-and-endpoints-with-powershell.md) za pośrednictwem programu PowerShell i ponownego szkolenia interfejsu API.
* Dowiedz się więcej o [wdrażaniu usług azure machine learning.](deploy-a-machine-learning-web-service.md)
