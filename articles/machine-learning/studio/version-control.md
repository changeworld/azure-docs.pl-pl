---
title: Zarządzanie cyklem życia aplikacji
titleSuffix: Azure Machine Learning Studio
description: Stosowanie najlepszych rozwiązań zarządzania cyklem życia aplikacji w usłudze Azure Machine Learning Studio
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.date: 10/27/2016
ms.openlocfilehash: 046afaa0e83fa572d6cd43a3717707892b25af69
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2019
ms.locfileid: "59051873"
---
# <a name="application-lifecycle-management-in-azure-machine-learning-studio"></a>Zarządzanie cyklem życia aplikacji w usłudze Azure Machine Learning Studio
Usługa Azure Machine Learning Studio to narzędzie do tworzenia eksperymenty usługi machine learning, w których jest przygotowany do działania na platformie chmury platformy Azure. Takich jak środowiska IDE programu Visual Studio i usługi w chmurze skalowalne scalania w pojedynczej platformy. Różne zasoby do wdrożenia i wykonywanie zautomatyzowanych standardowego rozwiązania zarządzania cyklem życia aplikacji (ALM) na podstawie wersji można zastosować w usłudze Azure Machine Learning Studio. W tym artykule omówiono niektóre opcje i metod.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="versioning-experiment"></a>Przechowywanie wersji eksperymentu
Istnieją dwa sposoby zalecana wersja eksperymenty. Możesz polegać na wbudowane historii uruchamiania lub wyeksportować eksperymentu w formacie JSON w taki sposób, aby zarządzać nim zewnętrznie. Każde podejście ma swoje zalety i wady.

### <a name="experiment-snapshots-using-run-history"></a>Migawki eksperymentu za pomocą historii uruchamiania
W modelu wykonywania usługi Azure Machine Learning Studio eksperymentu uczenia niezmienne migawki eksperymentu jest przesyłany do usługi Harmonogram zadań przy każdym kliknięciu **Uruchom** w edytorze eksperymentu. Aby wyświetlić tę listę migawek, kliknij przycisk **historii uruchamiania** na pasku poleceń, w widoku edytora eksperymentów.

![Przycisk historii uruchamiania](./media/version-control/runhistory.png)

Można, a następnie Otwórz migawkę w trybie zablokowany, klikając nazwę eksperymentu w momencie przesłania eksperyment do uruchomienia i migawka została wykonana. Zauważ, że tylko pierwszy element na liście, który reprezentuje bieżący eksperymentu, jest w stanie edycji. Ponadto należy zauważyć, że każdy migawki mogą znajdować się w różnych stan stany, łącznie z gotowego (częściowa Obsługa uruchamiania), nie powiodło się nie powiodło się (częściowa Obsługa uruchamiania), lub wersję roboczą.

![Listy historii uruchamiania](./media/version-control/runhistorylist.png)

Po jego otwarciu, możesz zapisać migawkę eksperymentu jako nowy eksperyment, a następnie zmodyfikuj go. Jeśli migawek eksperymentu zawiera zasoby, takie jak przeszkolone modele, przekształcenia lub zestawów danych, które zostały zaktualizowane wersje, migawka zachowuje odniesienia do oryginalnej wersji, gdy migawka została utworzona. Zablokowane migawka zostanie zapisany jako nowy eksperyment, Azure Machine Learning Studio wykryje obecność nowszą wersję tych zasobów i automatycznie aktualizuje je w nowy eksperyment.

Jeśli usuniesz eksperymentu, zostaną usunięte wszystkie migawki tego eksperymentu.

### <a name="exportimport-experiment-in-json-format"></a>Operacja eksportu/importu eksperymentu w formacie JSON
Migawki historii uruchamiania zachować niezmienne wersję eksperymentu w usłudze Azure Machine Learning Studio, za każdym razem, gdy zostanie przesłana do uruchomienia. Możesz także zapisać lokalną kopię eksperymentu i go zaewidencjonować w systemie kontroli źródła Ulubione, takie jak Team Foundation Server i później ponownie utworzyć eksperyment z tego pliku lokalnego. Możesz użyć [programu PowerShell usługi Azure Machine Learning](https://aka.ms/amlps) polecenia cmdlet [ *Export-AmlExperimentGraph* ](https://github.com/hning86/azuremlps#export-amlexperimentgraph) i [  *Import-AmlExperimentGraph* ](https://github.com/hning86/azuremlps#import-amlexperimentgraph) to zrobić.

Plik JSON jest tekstową reprezentację wartości wykres eksperymentu, który może zawierać odwołania do zasobów w obszarze roboczym, takich jak zestaw danych lub uczonego modelu. Nie zawiera Zserializowany wersję zasobu. Jeśli użytkownik podejmie próbę ponownego importowania danych dokumentów JSON do obszaru roboczego, zasoby, do którego istnieje odwołanie musi już istnieć z tej samej zawartości identyfikatorów, które są określone w eksperymencie. W przeciwnym razie nie można uzyskać dostępu importowanych eksperymentu.

## <a name="versioning-trained-model"></a>Przechowywanie wersji uczonego modelu
Uczonego modelu w usłudze Azure Machine Learning Studio jest serializowany do formatu, znany jako plik iLearner (`.iLearner`) i jest przechowywany na koncie magazynu obiektów Blob platformy Azure, które są skojarzone z obszarem roboczym. Jest jednym ze sposobów, aby pobrać kopię pliku iLearner za pośrednictwem interfejsu API ponownego trenowania. [W tym artykule](/azure/machine-learning/studio/retrain-machine-learning-model) wyjaśniono, jak działa interfejs API ponownego trenowania. Ogólne kroki:

1. Skonfiguruj eksperymentu szkolenia.
2. Dodaj port wyjściowy usługi sieci web do modułu Train Model lub moduł, który tworzy trenowanego modelu, takie jak dostrajanie Hiperparametrycznego Model lub utworzyć Model języka R.
3. Uruchomienie eksperymentu szkolenia, a następnie wdrożyć go jako usługę sieci web szkolenia modelu.
4. Wywołanie punktu końcowego usługi BES usługi sieci web, szkolenia i określ nazwę pliku odpowiednią iLearner i gdzie będą przechowywane lokalizacji konta magazynu obiektów Blob.
5. Po zakończeniu wywołania usługi BES, należy zebrać plik iLearner utworzone.

Innym sposobem, aby pobrać plik iLearner jest za pomocą polecenia cmdlet programu PowerShell [ *AmlExperimentNodeOutput pobierania*](https://github.com/hning86/azuremlps#download-amlexperimentnodeoutput). Może to być łatwiejsze, jeśli po prostu chcesz pobrać kopię pliku iLearner, bez konieczności programowe ponowne trenowanie modelu.

Po utworzeniu plik iLearner, zawierający trenowanego modelu, można następnie zastosować strategię obsługi wersji. Strategia mogą być proste i polega na zastosowaniu wstępnie/przyrostkowe jako konwencji nazewnictwa i pozostawienie plik iLearner w magazynie obiektów Blob lub kopiowanie/zaimportowanie go w systemie kontroli wersji.

Plik iLearner zapisany, następnie może służyć do oceniania za pośrednictwem wdrożonymi usługami sieci web.

## <a name="versioning-web-service"></a>Przechowywanie wersji usługi sieci web
Możesz wdrożyć dwa typy sieci web usługi Azure Machine Learning Studio eksperymentować. Klasyczna usługa sieci web jest ściśle powiązany z eksperymentu, a także obszaru roboczego. W ramach usługi Azure Resource Manager korzysta z nowej usługi sieci web, a nie jest już połączone z oryginalnego eksperymentu lub obszar roboczy.

### <a name="classic-web-service"></a>Klasyczna usługa sieci web
Do wersji klasyczna usługa sieci web możesz korzystać z zalet konstrukcji punktu końcowego usługi sieci web. Oto typowy przepływ:

1. Z eksperymentu predykcyjnego możesz wdrożyć nowe klasyczne usługi sieci web, która zawiera domyślny punkt końcowy.
2. Możesz utworzyć nowy punkt końcowy o nazwie ep2, który uwidacznia bieżącą wersję eksperymentu/uczony model.
3. Przejdź wstecz i zaktualizować eksperyment predykcyjny i uczonego modelu.
4. Możesz ponownie wdrożyć eksperyment predykcyjny, który następnie zostanie zaktualizowana domyślnego punktu końcowego. Ale to nie ma wpływu na ep2.
5. Możesz utworzyć dodatkowe punktu końcowego o nazwie ep3, który udostępnia nową wersję eksperyment i uczonego modelu.
6. Wróć do kroku 3, jeśli to konieczne.

Wraz z upływem czasu może mieć wiele punktów końcowych, utworzony w tej samej usługi sieci web. Każdy punkt końcowy reprezentuje punkt w czasie kopię eksperymentu, zawierającą wersję w momencie nauczony model. Następnie można użyć zewnętrznego logika do określenia, który punkt końcowy do wywołania, oznacza to efektywne Wybieranie wersja trenowanego modelu dla wyników przebiegu.

Można również utworzyć wiele punktów końcowych usługi sieci web identyczne, a następnie stosowanie poprawek do różnych wersji plik iLearner do punktu końcowego, aby osiągnąć ten sam efekt. [W tym artykule](create-models-and-endpoints-with-powershell.md) wyjaśnia, bardziej szczegółowo, jak to zrobić.

### <a name="new-web-service"></a>Nowa usługa sieci web
Jeśli tworzysz nową usługę sieci web opartych na usłudze Azure Resource Manager, konstrukcja punkt końcowy nie jest już dostępna. Zamiast tego możesz wygenerować plików definicji (WSD) usługi sieci web, w formacie JSON z eksperymentu predykcyjnego przy użyciu [AmlWebServiceDefinitionFromExperiment eksportu](https://github.com/hning86/azuremlps#export-amlwebservicedefinitionfromexperiment) polecenia cmdlet programu PowerShell lub za pomocą [ *AzMlWebservice eksportu* ](https://docs.microsoft.com/powershell/module/az.machinelearning/export-azmlwebservice) polecenia cmdlet programu PowerShell z usługą sieci web opartych na usłudze Resource Manager wdrożone.

Po utworzeniu wyeksportowany plik WSD i nad nim kontroli wersji, można także wdrożyć WSD jako nowej usługi sieci web w ramach planu usługi sieci web w różnych w innym regionie platformy Azure. Po prostu upewnij się, że użytkownik poda prawidłowego magazynu konfiguracji konta, a także nowy identyfikator planu usługi sieci web. Zastosowania poprawki w plikach różnych iLearner, można zmodyfikować plik WSD i zaktualizuj odwołanie do lokalizacji trenowanego modelu i wdrożyć go jako nowej usługi sieci web.

## <a name="automate-experiment-execution-and-deployment"></a>Automatyzowanie wykonywania eksperymentów i wdrażania
Ważnym aspektem ALM jest zautomatyzować proces wdrażania aplikacji i wykonywanie. W usłudze Azure Machine Learning Studio, można to zrobić za pomocą [modułu programu PowerShell](https://aka.ms/amlps). Oto przykład czynności end-to-end, które są istotne dla standardowych ALM zautomatyzować proces wykonywania/ciągłego wdrażania przy użyciu [modułu programu PowerShell usługi Azure Machine Learning Studio](https://aka.ms/amlps). Każdy krok jest połączony z co najmniej jeden polecenia cmdlet środowiska PowerShell służących do wykonywania tego kroku.

1. [Przekaż zestaw danych](https://github.com/hning86/azuremlps#upload-amldataset).
2. Skopiuj eksperyment do obszaru roboczego z [obszaru roboczego](https://github.com/hning86/azuremlps#copy-amlexperiment) lub z [galerii](https://github.com/hning86/azuremlps#copy-amlexperimentfromgallery), lub [zaimportować](https://github.com/hning86/azuremlps#import-amlexperimentgraph) [wyeksportowane](https://github.com/hning86/azuremlps#export-amlexperimentgraph) eksperymentów na podstawie danych lokalnych dysk.
3. [Aktualizowanie zestawu danych](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) w eksperymentu szkolenia.
4. [Uruchomienie eksperymentu szkolenia](https://github.com/hning86/azuremlps#start-amlexperiment).
5. [Podwyższanie poziomu uczonego modelu](https://github.com/hning86/azuremlps#promote-amltrainedmodel).
6. [Skopiuj eksperyment predykcyjny](https://github.com/hning86/azuremlps#copy-amlexperiment) do obszaru roboczego.
7. [Aktualizowanie uczonego modelu](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) w eksperyment predykcyjny.
8. [Uruchom eksperyment predykcyjny](https://github.com/hning86/azuremlps#start-amlexperiment).
9. [Wdrażanie usługi sieci web](https://github.com/hning86/azuremlps#new-amlwebservice) z eksperyment predykcyjny.
10. Test usługi sieci web [RRS](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) lub [BES](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint) punktu końcowego.

## <a name="next-steps"></a>Kolejne kroki
* Pobierz [programu PowerShell usługi Azure Machine Learning Studio](https://aka.ms/amlps) moduł i początek w celu zautomatyzowania zadań ALM.
* Dowiedz się, jak [tworzenie i zarządzanie dużą liczbą modeli uczenia Maszynowego przy użyciu tylko jednego eksperymentu](create-models-and-endpoints-with-powershell.md) za pośrednictwem programów PowerShell i ponownego trenowania interfejsu API.
* Dowiedz się więcej o [wdrażanie usług sieci web Azure Machine Learning](publish-a-machine-learning-web-service.md).
