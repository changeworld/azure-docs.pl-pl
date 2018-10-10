---
title: Co się stało z aplikacją Azure Machine Learning Workbench? | Microsoft Docs
description: Dowiedz się, co się stało z aplikacją Workbench, co zmieniło się w usłudze Azure Machine Learning i jaka jest oś czasu pomocy technicznej.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: overview
ms.reviewer: jmartens
author: j-martens
ms.author: jmartens
ms.date: 09/24/2018
ms.openlocfilehash: 620d8b370b050a4d91ee6d94cba2c133b4313aed
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47159988"
---
# <a name="what-happened-to-workbench-in-azure-machine-learning-preview"></a>Co się stało z aplikacją Workbench w usłudze Azure Machine Learning (wersja zapoznawcza)?

Aplikacja Workbench i niektóre inne wczesne funkcje zostały wycofane z wersji z września 2018 r., aby zwolnić miejsce na ulepszoną [architekturę](concept-azure-machine-learning-architecture.md). Wersja zawiera wiele znaczących aktualizacji wprowadzonych w oparciu o opinie klientów, które mają na celu udoskonalenie środowiska użytkownika. Podstawowe funkcje od przebiegów eksperymentalnych do wdrożenia modelu nie zmieniły się, ale teraz można używać niezawodnego <a href="http://aka.ms/aml-sdk" target="_blank">zestawu SDK</a> i [interfejsu wiersza polecenia](reference-azure-machine-learning-cli.md) do wykonywania zadań i potoków uczenia maszynowego.  

Z tego artykułu dowiesz się, co się zmieniło i jak zmiany wpłynęły na zadania wykonywane obecnie przy użyciu usługi Azure Machine Learning.

## <a name="what-changed"></a>Co się zmieniło?

Najnowsza wersja usługi Azure Machine Learning obejmuje:
+ [Uproszczony model zasobów platformy Azure](concept-azure-machine-learning-architecture.md)
+ [Nowy interfejs użytkownika portalu](how-to-track-experiments.md) służący do zarządzania eksperymentami i celami obliczeniowymi
+ Nowy, bardziej kompleksowy <a href="http://aka.ms/aml-sdk" target="_blank">zestaw SDK</a> języka Python
+ Nowe, rozbudowane [rozszerzenie interfejsu wiersza polecenia platformy Azure](reference-azure-machine-learning-cli.md) na potrzeby uczenia maszynowego

[Architektura](concept-azure-machine-learning-architecture.md) została zaprojektowana od nowa z myślą o ułatwieniu użycia. Zamiast wielu zasobów i kont platformy Azure, będziesz potrzebować tylko aplikacji [Azure Machine Learning Workspace](concept-azure-machine-learning-architecture.md#workspace).  Obszary robocze można szybko tworzyć w witrynie [Azure Portal](quickstart-get-started.md).  Obszar roboczy może być używany przez wielu użytkowników do przechowywania celów obliczeniowych szkoleń i wdrożeń, eksperymentów dotyczących modeli, obrazów platformy Docker, wdrożonych modeli itd.

Bieżąca wersja oferuje nowych, ulepszonych klientów interfejsu wiersza polecenia i zestawu SDK, ale sama klasyczna aplikacja Workbench jest traktowana jako przestarzała. Teraz możesz monitorować eksperymenty na [pulpicie nawigacyjnym obszaru roboczego w internetowej witrynie Azure Portal](how-to-track-experiments.md#view-the-experiment-in-the-azure-portal). Pulpit nawigacyjny umożliwia pobieranie historii, zarządzanie celami obliczeniowymi dołączonymi do obszaru roboczego, zarządzanie modelami i obrazami platformy Docker, a nawet wdrażanie usług internetowych.

## <a name="how-do-i-migrate"></a>Jak przeprowadzić migrację?

Większość artefaktów utworzonych w starszej wersji usługi Azure Machine Learning jest przechowywanych we własnym magazynie lokalnym lub w chmurze. Te artefakty nigdy nie zostaną usunięte. Aby przeprowadzić migrację, należy ponownie zarejestrować artefakty w zaktualizowanej usłudze Azure Machine Learning. Dowiedz się, co można migrować i jak to zrobić, z tego [artykułu dotyczącego migracji](how-to-migrate.md).

<a name="timeline"></a>

## <a name="support-timeline"></a>Oś czasu pomocy technicznej

Z kont eksperymentowania i zarządzania modelami oraz z aplikacji Workbench można korzystać jeszcze przez jakiś czas po wrześniu 2018 r. Pomoc techniczna dla wymienionych zasobów będzie wycofywana stopniowo w ciągu 3–4 miesięcy po tym wydaniu. Dokumentację dla starych funkcji nadal można znaleźć w [sekcji Zasoby](../desktop-workbench/tutorial-classifying-iris-part-1.md) w dolnej części spisu treści.

|Etap|Szczegóły pomocy technicznej dla wcześniejszych funkcji|
|:---:|----------------|
|1|Możliwość tworzenia _konta usługi Eksperymentowanie w usłudze Azure Machine Learning_ i _konta usługi Zarządzanie modelami_ w witrynie Azure Portal i za pomocą interfejsu wiersza polecenia. Możliwość tworzenia środowisk obliczeniowych usługi ML z poziomu interfejsu wiersza polecenia także jest wycofywana. Jeśli masz istniejące konto, interfejs wiersza polecenia i aplikacja klasyczna Workbench na tym etapie nadal będą działać.|
|2|Bazowe interfejsy API do tworzenia starych obszarów roboczych i projektów w klasycznej aplikacji Workbench i za pomocą interfejsu wiersza polecenia są wycofywane. Na tym etapie nadal można otwierać istniejące projekty, dodawać do nich skrypty, uruchamiać skrypty w istniejących projektach i wdrażać usługi internetowe w istniejących środowiskach obliczeniowych usługi ML.|
|3|Pomoc techniczna dla wszystkich innych zasobów, w tym dla pozostałych interfejsów API i klasycznej aplikacji Workbench jest na tym etapie wycofywana.|

[Zacznij migrację](how-to-migrate.md) już dziś. Wszystkie najnowsze możliwości są dostępne za pomocą nowego zestawu <a href="http://aka.ms/aml-sdk" target="_blank">SDK</a>, [interfejsu wiersza polecenia](reference-azure-machine-learning-cli.md) i witryny [Portal](quickstart-get-started.md).

## <a name="what-about-run-histories"></a>A co z historią uruchamiania?

Historie uruchamiania będą nadal dostępne przez pewien czas. Gdy wszystko będzie gotowe do przejścia do zaktualizowanej wersji usługi Azure Machine Learning, będzie można wyeksportować te historie uruchamiania, aby zachować ich kopię.

W bieżącej wersji historie uruchamiania są teraz nazywane _eksperymentami_. Eksperymenty modelu można gromadzić i zapoznawać się z nimi przy użyciu zestawu SDK, interfejsu wiersza polecenia lub portalu internetowego.

Pulpit nawigacyjny obszaru roboczego w portalu jest obsługiwany tylko w przeglądarkach Edge, Chrome i Firefox.

[ ![Portal online](./media/overview-what-happened-to-workbench/image001.png) ] (./media/overview-what-happened-to-workbench/image001.png#lightbox)


## <a name="can-i-still-prep-data"></a>Czy nadal można przygotowywać dane?

Istniejących plików przygotowywania danych nie można przenosić do najnowszej wersji, ponieważ nie mamy już aplikacji Workbench. Można jednak nadal przygotować dane do modelowania.  

W przypadku mniejszych zestawów danych można używać <a href="http://aka.ms/aml-sdk" target="_blank">zestawu Data Prep SDK usługi Azure Machine Learning</a>, aby szybko przygotować dane przed rozpoczęciem modelowania. 

W przypadku dużych zestawów danych można użyć tego samego <a href="http://aka.ms/aml-sdk" target="_blank">zestawu SDK</a> albo użyć usługi Azure Databricks, aby przygotować zestawy danych big data. 

## <a name="will-projects-persist"></a>Czy projekty będą utrwalane?

Nie stracisz żadnego kodu ani wykonanej pracy. W starszej wersji projekty są jednostkami chmury z katalogiem lokalnym. W najnowszej wersji można dołączać katalogi lokalne do aplikacji Azure Machine Learning Workspace przy użyciu lokalnego pliku konfiguracji. [Zobacz diagram przedstawiający najnowszą architekturę](concept-azure-machine-learning-architecture.md).

Ponieważ większość zawartości projektu była już na komputerze lokalnym, wystarczy utworzyć plik konfiguracji w tym katalogu i odwołać się do niego w kodzie, aby nawiązać połączenie z obszarem roboczym. [Dowiedz się, jak migrować istniejące projekty.](how-to-migrate.md#projects)

Dowiedz się, jak rozpocząć pracę [w języku Python za pomocą głównego zestawu SDK](quickstart-get-started.md).

## <a name="what-about-my-registers-models-and-images"></a>Co się stanie z obrazami i modelami w rejestrach?
 
Modele zarejestrowane w starym rejestrze modeli trzeba migrować do nowego obszaru roboczego, aby nadal z nich korzystać. Można to zrobić, [pobierając modele i ponownie rejestrując je](how-to-migrate.md) w nowym obszarze roboczym. 

Obrazy, które zostały utworzone w starym rejestrze obrazów, należy ponownie utworzyć w nowym obszarze roboczym, aby nadal z nich korzystać. W tym celu można wykonać instrukcje z sekcji dotyczącej [tworzenia obrazu platformy Docker](how-to-deploy-to-aci.md#configure-an-image). 

## <a name="what-about-deployed-web-services"></a>Co się stanie z wdrożonymi usługami internetowymi?

Modele wdrożone jako usługi internetowe przy użyciu konta usługi Zarządzanie modelami będą nadal działać tak długo, jak będzie obsługiwana usługa Azure Container Service (ACS). Te usługi internetowe będą działać nawet po zakończeniu świadczenia pomocy technicznej dla kont usługi Zarządzanie modelami. Jednak gdy zakończy się pomoc techniczna dla starego interfejsu wiersza polecenia, nie będzie już można zarządzać usługami internetowymi.

W nowszej wersji modele są wdrażane jako usługi internetowe w klastrach usługi [Azure Container Instances](how-to-deploy-to-aci.md) (ACI) lub [Azure Kubernetes Service](how-to-deploy-to-aks.md) (AKS). Można również [wdrażać w układach FPGA oraz na urządzeniach brzegowych IoT](how-to-deploy-and-where.md). Nie zmieniając schematów, zależności ani plików oceniania, można ponownie wdrażać modele przy użyciu nowego zestawu SDK lub interfejsu wiersza polecenia. 

## <a name="what-about-the-old-sdk--cli"></a>Co stanie się ze starym zestawem SDK i interfejsem wiersza polecenia?

Będą one nadal działać przez pewien czas (zobacz [oś czasu](#timeline) powyżej). Zalecamy rozpoczęcie tworzenia nowych eksperymentów i modeli przy użyciu najnowszego zestawu SDK i/lub interfejsu wiersza polecenia.

W najnowszej wersji nowy zestaw SDK języka Python umożliwia interakcję z usługą Azure Machine Learning w dowolnym środowisku języka Python. Dowiedz się, jak zainstalować najnowszy <a href="http://aka.ms/aml-sdk" target="_blank">zestaw SDK</a>.  Można również użyć [zaktualizowanego rozszerzenia uczenia maszynowego dla interfejsu wiersza polecenia platformy Azure](reference-azure-machine-learning-cli.md) z bogatym zestawem poleceń `az ml`, aby pracować z usługą w dowolnym środowisku wiersza polecenia, w tym w usłudze Cloud Shell witryny Azure Portal.

## <a name="what-about-vs-code-tools-for-ai"></a>Co się stanie z narzędziami VS Code Tools for AI?

W najnowszej wersji rozszerzenie Visual Studio (VS) Code Tools for AI zostało rozbudowane i ulepszone w celu umożliwienia współpracy z wspomnianymi powyżej nowymi funkcjami.

[ ![Visual Studio Code Tools for AI](./media/overview-what-happened-to-workbench/vscode.png) ] (./media/overview-what-happened-to-workbench/vscode-big.png#lightbox)

## <a name="what-about-domain-packages"></a>Co się stanie z pakietami domen?

Pakietów domen funkcji [przetwarzania obrazów, analizy tekstu i prognozowania](../desktop-workbench/reference-python-package-overview.md) nie można używać z najnowszą wersją usługi Azure Machine Learning. Można jednak nadal kompilować i uczyć modele przetwarzania obrazów, analizy tekstu oraz prognozowania przy użyciu najnowszego zestawu <a href="http://aka.ms/aml-sdk" target="_blank">SDK</a> języka Python dla usługi Azure Machine Learning. Aby dowiedzieć się, jak przeprowadzić migrację istniejących wcześniej modeli utworzonych przy użyciu pakietów przetwarzania obrazów, analizy tekstu i prognozowania, skontaktuj się z nami pod adresem [AML-Packages@microsoft.com](mailto:AML-Packages@microsoft.com).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [najnowszej architekturze usługi Azure Machine Learning](concept-azure-machine-learning-architecture.md) i wypróbuj jeden z przewodników Szybki start lub samouczków:

* [Co to jest usługa Azure Machine Learning](overview-what-is-azure-ml.md)
* [Quickstart: Create a workspace with Python](quickstart-get-started.md) (Szybki start: tworzenie obszaru roboczego przy użyciu języka Python)
* [Tutorial: Train a model](tutorial-train-models-with-aml.md) (Samouczek: uczenie modelu)
