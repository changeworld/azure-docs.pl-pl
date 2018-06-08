---
title: ALM w Azure Machine Learning | Dokumentacja firmy Microsoft
description: Zastosuj najlepsze rozwiązania dotyczące zarządzania cyklem życia aplikacji w usłudze Azure Machine Learning Studio
keywords: Kontrola wersji ALM, AML, Azure ML, Zarządzanie cyklem życia aplikacji,
services: machine-learning
documentationcenter: ''
author: hning86
ms.author: haining
manager: mwinkle
editor: cgronlun
ms.assetid: 1be6577d-f2c7-425b-b6b9-d5038e52b395
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2016
ms.openlocfilehash: 49d1a228132cc220b30091481bb542623b1e222d
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835869"
---
# <a name="application-lifecycle-management-in-azure-machine-learning-studio"></a>Zarządzanie cyklem życia aplikacji w usłudze Azure Machine Learning Studio
Azure Machine Learning Studio to narzędzie do tworzenia eksperymenty uczenia maszynowego, które są operationalized na platformie Azure cloud. Podobnie jak Visual Studio IDE i usługą w chmurze skalowalne scalania w pojedynczą platformę. Standardowe rozwiązania zarządzania cyklem życia aplikacji (ALM) z wersji można zastosować różne zasoby do wykonywania automatycznych i wdrażania w Azure Machine Learning Studio. W tym artykule omówiono niektóre opcje i metod.

## <a name="versioning-experiment"></a>Przechowywanie wersji eksperymentu
Istnieją dwa sposoby zalecana wersja eksperymentów. Może polegać na wbudowanych Historia uruchomień lub wyeksportować eksperymentu w formacie JSON, aby zarządzać nim zewnętrznie. Każde podejście jest dostarczany z jej zalet i wad.

### <a name="experiment-snapshots-using-run-history"></a>Za pomocą historii Uruchom migawki eksperymentu
Model wykonania Azure Machine Learning Studio eksperymentu uczenia, niezmienne migawki doświadczenia jest przesyłany do usługi Harmonogram zadań przy każdym kliknięciu **Uruchom** w edytorze eksperymentu. Aby wyświetlić tę listę migawek, kliknij przycisk **Uruchom historii** na pasku poleceń w widoku edytora eksperymentu.

![Przycisk historii wykonywania](./media/version-control/runhistory.png)

Mogą być następnie otwórz migawki w trybie zablokowany, klikając nazwę eksperymentu w momencie eksperyment zostało przesłane do uruchomienia i migawka została wykonana. Należy zauważyć, że tylko pierwszy element na liście, która reprezentuje bieżący eksperymentu, jest w stanie edycji. Również należy zauważyć, że każdy migawki mogą znajdować się w różnych stan stany również tym Zakończono (Uruchom), częściowego nie powiodła się i nie powiodło się (Uruchom), częściowego lub projekt.

![Listy historii wykonywania](./media/version-control/runhistorylist.png)

Po otwarciu, można zapisać jako nowy eksperyment eksperymentu migawki, a następnie zmodyfikować go. Jeśli migawki eksperymentu zawiera zasoby, takie jak przeszkolone modeli, transformacji lub zestawów danych, które zostały zaktualizowane wersje, migawki zachowuje odwołań do oryginalnej wersji, gdy migawki. Zablokowane migawki zostanie zapisany jako nowy eksperyment, Azure Machine Learning Studio wykrywa istnienie nowszej wersji tych zasobów i automatycznie aktualizuje je w nowy eksperyment.

Jeśli usuniesz eksperyment, są usuwane wszystkie migawki tego eksperymentu.

### <a name="exportimport-experiment-in-json-format"></a>Eksperymentu eksportu/importu w formacie JSON
Migawki historii wykonywania zachować niezmienne wersję eksperymentu w usłudze Azure Machine Learning Studio, za każdym razem, gdy jest przesyłany do uruchomienia. Można także zapisać lokalną kopię eksperyment i go zaewidencjonować w systemie kontroli źródła Ulubione, takich jak Team Foundation Server i później ponownie utworzyć eksperyment z tym plikiem lokalnym. Można użyć [programu PowerShell usługi Azure Machine Learning](http://aka.ms/amlps) polecenia cmdlet [ *AmlExperimentGraph eksportu* ](https://github.com/hning86/azuremlps#export-amlexperimentgraph) i [ *AmlExperimentGraph importu* ](https://github.com/hning86/azuremlps#import-amlexperimentgraph) znajdują się.

Plik JSON jest tekstowa reprezentacja wykresu eksperymentu, która może zawierać odwołania do zasobów w obszarze roboczym, takie jak element dataset lub uczonego modelu. Nie zawiera on wersja po serializacji jest trwałego. Próba importowania dokument JSON do obszaru roboczego zasoby, do którego istnieje odwołanie musi już istnieć z tej samej zawartości identyfikatorów, które są używane w eksperymencie. W przeciwnym razie nie można uzyskać dostępu importowanych eksperymentu.

## <a name="versioning-trained-model"></a>Przechowywanie wersji uczonego modelu
Uczonego modelu w usłudze Azure Machine Learning jest serializowany w formacie znane jako plik iLearner (`.iLearner`) i są przechowywane na koncie magazynu obiektów Blob platformy Azure skojarzone z obszaru roboczego. Jest jednym ze sposobów uzyskać kopię pliku iLearner za pośrednictwem interfejsu API ponownego trenowania. [W tym artykule](retrain-models-programmatically.md) wyjaśniono sposób działania ponownego trenowania interfejsu API. Ogólne kroki:

1. Konfigurowanie eksperymentu szkolenia.
2. Dodaj port wyjście usługi sieci web do modułu Train Model lub moduł, który tworzy trenowanego modelu, takie jak dostosować Hyperparameter modelu lub utworzyć Model R.
3. Uruchamianie eksperymentu uczenia, a następnie wdrożyć go jako usługi sieci web szkolenia modelu.
4. Wywołanie usługi BES punkt końcowy usługi sieci web szkolenia i określ nazwę pliku żądanego iLearner i lokalizacja konta magazynu obiektów Blob którym będą przechowywane.
5. Zebrać plik iLearner utworzonych po zakończeniu wywołania BES.

Inny sposób, aby pobrać plik iLearner jest za pomocą polecenia programu PowerShell [ *AmlExperimentNodeOutput pobierania*](https://github.com/hning86/azuremlps#download-amlexperimentnodeoutput). Może to być łatwiejsze, jeśli chcesz uzyskać kopię pliku iLearner bez konieczności programowo retrain modelu.

Po umieszczeniu pliku iLearner zawierającego trenowanego modelu, można następnie wdrożyć strategii przechowywania wersji. Strategia może być tak proste, jak stosowanie sprzed/przyrostek jako konwencji nazewnictwa i pozostawienie plik iLearner w magazynie obiektów Blob lub kopiowania/importowania go do systemu kontroli wersji.

Następnie można plik iLearner zapisanych przez oceniania za pośrednictwem usług sieci web wdrożone.

## <a name="versioning-web-service"></a>Przechowywanie wersji usługi sieci web
Można wdrożyć dwa typy usług sieci web z eksperymentu uczenia maszynowego Azure. Usługi sieci web klasycznego jest ściśle powiązane z doświadczenia, jak również w obszarze roboczym. Nowa usługa sieci web używa w ramach usługi Azure Resource Manager i nie jest już połączone z oryginalnym eksperymentu lub obszar roboczy.

### <a name="classic-web-service"></a>Usługa sieci web klasycznego
Do wersji usługi sieci web klasycznego można wykorzystać konstrukcji punkt końcowy usługi sieci web. Oto typowy przepływ:

1. Z predykcyjnej eksperymentu możesz wdrożyć nowe klasycznego usługi sieci web, który zawiera domyślny punkt końcowy.
2. Możesz utworzyć nowy punkt końcowy o nazwie ep2, który udostępnia bieżącą wersję eksperymentu/uczenia modelu.
3. Przejdź wstecz i aktualizowanie eksperyment predykcyjny i trenowanego modelu.
4. Należy ponownie wdrożyć eksperyment predykcyjny, który zostanie następnie zaktualizuj domyślnego punktu końcowego. Ale nie zmieni ep2.
5. Możesz utworzyć dodatkowe punktu końcowego o nazwie ep3, który udostępnia nową wersję eksperymentu i trenowanego modelu.
6. Wróć do kroku 3 w razie potrzeby.

W czasie może mieć wiele punktów końcowych utworzone w tej samej usługi sieci web. Każdy punkt końcowy reprezentuje kopię w momencie eksperymentu zawierającą wersję w momencie trenowanego modelu. Następnie można użyć zewnętrznej logika do określenia, który punkt końcowy do wywołania, co skutecznie oznacza wybranie wersja trenowanego modelu dla wyników przebiegu.

Można również utworzyć wiele punktów końcowych usługi sieci web identyczne i zastosować poprawki różne wersje pliku iLearner do punktu końcowego, aby osiągnąć ten sam efekt. [W tym artykule](create-models-and-endpoints-with-powershell.md) omówiono bardziej szczegółowo znajdują się.

### <a name="new-web-service"></a>Nowa usługa sieci web
Jeśli utworzysz nową usługę sieci web opartej na usłudze Azure Resource Manager konstrukcja punktu końcowego nie jest już dostępny. Zamiast tego można wygenerować plików definicji (WSD) usługi sieci web w formacie JSON z predykcyjnej eksperymentu przy użyciu [AmlWebServiceDefinitionFromExperiment eksportu](https://github.com/hning86/azuremlps#export-amlwebservicedefinitionfromexperiment) polecenia programu PowerShell lub przy użyciu [ *AzureRmMlWebservice eksportu* ](https://msdn.microsoft.com/library/azure/mt767935.aspx) polecenia programu PowerShell z usługi sieci web na podstawie Resource Manager wdrożone.

Po umieszczeniu wyeksportowany plik WSD i nad nim kontroli wersji, można także wdrożyć WSD jako nową usługę sieci web w planie usługi innej witryny sieci web w innym regionie Azure. Po prostu upewnij się, że użytkownik poda prawidłowego magazynu konfiguracji konta, a także nowy identyfikator planu usługi sieci web. Zastosować poprawki w różnych iLearner pliki, do można zmodyfikować pliku WSD i zaktualizuj odwołanie do lokalizacji trenowanego modelu i wdróż je jako nową usługę sieci web.

## <a name="automate-experiment-execution-and-deployment"></a>Automatyzacja wdrażania i wykonywania eksperymentu
Ważnym aspektem ALM ma być w stanie zautomatyzować proces wdrażania aplikacji i wykonywanie. W usłudze Azure Machine Learning, można to zrobić za pomocą [modułu PowerShell](http://aka.ms/amlps). Oto przykład end-to-end kroki, które mają zastosowanie w standardowe ALM zautomatyzowanego procesu wdrożenia/wykonywania przy użyciu [modułu programu PowerShell usługi Azure Machine Learning Studio](http://aka.ms/amlps). Każdy krok jest połączony z co najmniej jednego polecenia cmdlet środowiska PowerShell służące do wykonania tego kroku.

1. [Przekaż zestawu danych](https://github.com/hning86/azuremlps#upload-amldataset).
2. Skopiuj eksperyment uczenia do obszaru roboczego z [obszaru roboczego](https://github.com/hning86/azuremlps#copy-amlexperiment) lub [galerii](https://github.com/hning86/azuremlps#copy-amlexperimentfromgallery), lub [zaimportować](https://github.com/hning86/azuremlps#import-amlexperimentgraph) [wyeksportowane](https://github.com/hning86/azuremlps#export-amlexperimentgraph) Poeksperymentuj z dysku lokalnego.
3. [Zaktualizować dataset](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) w eksperymencie szkolenia.
4. [Uruchom eksperyment uczenia](https://github.com/hning86/azuremlps#start-amlexperiment).
5. [Podwyższ poziom trenowanego modelu](https://github.com/hning86/azuremlps#promote-amltrainedmodel).
6. [Skopiuj eksperyment predykcyjny](https://github.com/hning86/azuremlps#copy-amlexperiment) do obszaru roboczego.
7. [Zaktualizuj trenowanego modelu](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) w eksperyment predykcyjny.
8. [Uruchom eksperyment predykcyjny](https://github.com/hning86/azuremlps#start-amlexperiment).
9. [Wdrażanie usługi sieci web](https://github.com/hning86/azuremlps#new-amlwebservice) z eksperyment predykcyjny.
10. Testowanie usługi sieci web [RRS](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) lub [BES](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint) punktu końcowego.

## <a name="next-steps"></a>Kolejne kroki
* Pobierz [programu PowerShell usługi Azure Machine Learning Studio](http://aka.ms/amlps) modułu i początek w celu zautomatyzowania zadań ALM.
* Dowiedz się, jak [tworzenie i zarządzanie dużą liczbą modeli uczenia Maszynowego przy użyciu tylko jednego eksperymentu](create-models-and-endpoints-with-powershell.md) za pośrednictwem programu PowerShell i ponownego trenowania interfejsu API.
* Dowiedz się więcej o [wdrażanie usług sieci web Azure Machine Learning](publish-a-machine-learning-web-service.md).
