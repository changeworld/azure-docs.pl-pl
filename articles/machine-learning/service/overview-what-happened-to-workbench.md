---
title: Co się stało z aplikacją Machine Learning Workbench?
titleSuffix: Azure Machine Learning service
description: Dowiedz się, co się stało z aplikacją Machine Learning Workbench, co się zmieniło w usłudze Azure Machine Learning i jak wygląda oś czasu pomocy technicznej.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: overview
ms.reviewer: jmartens
author: j-martens
ms.author: jmartens
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: ff6b61874363bbc869bd509174e58640a2487f56
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/28/2018
ms.locfileid: "53811311"
---
# <a name="whats-happening-to-machine-learning-workbench-in-azure-machine-learning-service"></a>Co dzieje się z aplikacją Machine Learning Workbench w usłudze Azure Machine Learning?

Aplikacja Azure Machine Learning Workbench i niektóre inne wczesne funkcje zostały wycofane i zastąpione w wersji z września 2018 r., aby zwolnić miejsce na ulepszoną [architekturę](concept-azure-machine-learning-architecture.md). Wersja zawiera wiele znaczących aktualizacji wprowadzonych w oparciu o opinie klientów, które mają na celu udoskonalenie środowiska użytkownika. Podstawowe funkcje z przebiegów eksperymentu w zakresie wdrażania modelu nie uległy zmianie. Jednak teraz można używać zaawansowanego zestawu <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a> i [interfejsu wiersza polecenia platformy Azure](reference-azure-machine-learning-cli.md) do wykonywania zadań i potoków uczenia maszynowego.  

Z tego artykułu dowiesz się, co się zmieniło i jak zmiany wpłynęły na zadania wykonywane obecnie przy użyciu aplikacji Azure Machine Learning Workbench i jej interfejsów API.

## <a name="what-changed"></a>Co się zmieniło?

Najnowsza wersja usługi Azure Machine Learning Service obejmuje następujące funkcje:
+ [Uproszczony model zasobów platformy Azure](concept-azure-machine-learning-architecture.md).
+ [Nowy interfejs użytkownika portalu](how-to-track-experiments.md) służący do zarządzania eksperymentami i celami obliczeniowymi.
+ Nowy, bardziej kompleksowy <a href="https://aka.ms/aml-sdk" target="_blank">zestaw SDK</a> języka Python.
+ Nowe, rozbudowane [rozszerzenie interfejsu wiersza polecenia platformy Azure](reference-azure-machine-learning-cli.md) na potrzeby uczenia maszynowego.

[Architektura](concept-azure-machine-learning-architecture.md) została zaprojektowana od nowa, aby ułatwić obsługę. Zamiast wielu zasobów i kont platformy Azure, będziesz potrzebować tylko aplikacji [Azure Machine Learning Service Workspace](concept-azure-machine-learning-architecture.md#workspace). Obszary robocze można szybko tworzyć w witrynie [Azure Portal](quickstart-get-started.md). Dzięki użyciu obszaru roboczego wielu użytkowników może przechowywać cele obliczeniowe szkoleń i wdrożeń, eksperymenty dotyczące modeli, obrazy platformy Docker, wdrożone modele itd.

Bieżąca wersja udostępnia nowe, ulepszone klienty interfejsu wiersza polecenia i zestawu SDK, ale sama klasyczna aplikacja Workbench jest traktowana jako przestarzała. Teraz możesz monitorować eksperymenty na [pulpicie nawigacyjnym obszaru roboczego w witrynie Azure Portal](how-to-track-experiments.md#view-the-experiment-in-the-azure-portal). Pulpit nawigacyjny umożliwia pobieranie historii, zarządzanie celami obliczeniowymi dołączonymi do obszaru roboczego, zarządzanie modelami i obrazami platformy Docker, a nawet wdrażanie usług internetowych.

## <a name="how-do-i-migrate"></a>Jak przeprowadzić migrację?

Większość artefaktów utworzonych w starszej wersji usługi Azure Machine Learning jest przechowywanych we własnym magazynie lokalnym lub w chmurze. Te artefakty nigdy nie zostaną usunięte. Aby przeprowadzić migrację, należy ponownie zarejestrować artefakty w zaktualizowanej usłudze Azure Machine Learning. Dowiedz się, co można migrować i jak to zrobić, z tego [artykułu dotyczącego migracji](how-to-migrate.md).

<a name="timeline"></a>

## <a name="support-timeline"></a>Oś czasu pomocy technicznej

Po 30 września 2018 r. nadal będzie można używać kont Eksperymentowania i Zarządzania modelami w usłudze Machine Learning oraz aplikacji Machine Learning Workbench. Pomoc techniczna dla wymienionych zasobów będzie wycofywana stopniowo w ciągu trzech do czterech miesięcy po tym wydaniu. Dokumentację dla starych funkcji nadal można znaleźć w [sekcji Zasoby](../desktop-workbench/tutorial-classifying-iris-part-1.md) w dolnej części spisu treści.

|Faza&nbsp;wycofania|Szczegóły pomocy technicznej dla wcześniejszych funkcji|
|:---:|----------------|
|4 grudnia 2018 r.|Nie ma już możliwości tworzenia konta usługi Eksperymentowanie w usłudze Azure Machine Learning i konta usługi Zarządzanie modelami w witrynie Azure Portal i za pomocą interfejsu wiersza polecenia. Możliwość tworzenia środowisk obliczeniowych usługi Machine Learning z poziomu interfejsu wiersza polecenia także została wycofana. Jeśli masz istniejące konto, interfejs wiersza polecenia i aplikacja klasyczna Machine Learning Workbench na tym etapie nadal będą działać.|
|9 stycznia 2019 r.|W tym dniu kończy się pomoc techniczna dla wszystkich pozostałych składników. Przykładami są pozostałe interfejsy API i aplikacja klasyczna Machine Learning Workbench.|

[Zacznij migrację](how-to-migrate.md) już dziś. Wszystkie najnowsze możliwości są dostępne za pomocą nowego zestawu <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a>, [interfejsu wiersza polecenia](reference-azure-machine-learning-cli.md) i witryny [Portal](quickstart-get-started.md).

## <a name="what-about-run-histories"></a>A co z historią uruchamiania?

Historie uruchamiania będą dostępne jeszcze przez pewien czas. Gdy wszystko będzie gotowe do przejścia do zaktualizowanej wersji usługi Azure Machine Learning, będzie można wyeksportować te historie uruchamiania, aby zachować ich kopię.

W bieżącej wersji historie uruchamiania są nazywane **eksperymentami**. Eksperymenty modelu można gromadzić i zapoznawać się z nimi przy użyciu zestawu SDK, interfejsu wiersza polecenia lub witryny Azure Portal.

Pulpit nawigacyjny obszaru roboczego w portalu jest obsługiwany tylko w przeglądarkach Microsoft Edge, Chrome i Firefox:

[ ![Portal online](./media/overview-what-happened-to-workbench/image001.png)] (./media/overview-what-happened-to-workbench/image001.png#lightbox)


## <a name="can-i-still-prep-data"></a>Czy nadal można przygotowywać dane?

Istniejących plików przygotowywania danych nie można przenosić do najnowszej wersji, ponieważ nie ma już aplikacji Machine Learning Workbench. Można jednak nadal przygotować dane do modelowania.  

W przypadku zestawów danych o dowolnym rozmiarze można używać [zestawu Data Prep SDK usługi Azure Machine Learning](http://aka.ms/data-prep-sdk), aby szybko przygotować dane przed rozpoczęciem modelowania, pisząc kod języka Python. 

W [tym samouczku](tutorial-data-prep.md) dowiesz się więcej na temat używania zestawu Data Prep SDK usługi Azure Machine Learning.

## <a name="will-projects-persist"></a>Czy projekty będą utrwalane?

Nie stracisz żadnego kodu ani wykonanej pracy. W starszej wersji projekty są jednostkami chmury z katalogiem lokalnym. W najnowszej wersji można dołączać katalogi lokalne do aplikacji Azure Machine Learning Service Workspace przy użyciu lokalnego pliku konfiguracji. Zobacz [diagram przedstawiający najnowszą architekturę](concept-azure-machine-learning-architecture.md).

Większa część zawartości projektu znajduje się już na komputerze lokalnym. Wystarczy utworzyć w odpowiednim katalogu plik konfiguracji i odwołać się do niego w kodzie, aby nawiązać połączenie z obszarem roboczym. Dowiedz się, jak [migrować istniejące projekty](how-to-migrate.md#projects).

Dowiedz się, jak rozpocząć pracę w [języku Python za pomocą głównego zestawu SDK](quickstart-create-workspace-with-python.md) lub witryny [Azure Portal](quickstart-get-started.md).

## <a name="what-about-my-registered-models-and-images"></a>Co się stanie z moimi zarejestrowanymi modelami i obrazami?
 
Modele zarejestrowane w starym rejestrze modeli trzeba migrować do nowego obszaru roboczego, aby nadal z nich korzystać. Aby przeprowadzić migrację modeli, [pobierz modele i ponownie je zarejestruj](how-to-migrate.md) w nowym obszarze roboczym. 

Obrazy, które zostały utworzone w starym rejestrze obrazów, należy ponownie utworzyć w nowym obszarze roboczym, aby nadal z nich korzystać. W tym celu należy wykonać instrukcje z sekcji dotyczącej [konfigurowania i tworzenia obrazu](how-to-deploy-and-where.md#configureimage). 

## <a name="what-about-deployed-web-services"></a>Co się stanie z wdrożonymi usługami internetowymi?

Modele wdrożone jako usługi internetowe przy użyciu konta Zarządzania modelami usługi Machine Learning będą działać, dopóki będzie obsługiwana usługa Azure Container Service. Te usługi sieci Web będą działać nawet po zakończeniu obsługi dla kont Zarządzania modelami usługi Machine Learning. Jednak gdy zakończy się pomoc techniczna dla starego interfejsu wiersza polecenia, nie będzie już można zarządzać usługami internetowymi.

W nowszej wersji modele są wdrażane jako usługi internetowe w klastrach usługi Azure Container Instances lub Azure Kubernetes Service (AKS). Można je również wdrażać w układach FPGA oraz w usłudze Azure IoT Edge. Aby uzyskać więcej informacji, zobacz artykuł opisujący, [jak i gdzie wdrażać](how-to-deploy-and-where.md). Nie zmieniając schematów, zależności ani plików oceniania, można ponownie wdrażać modele przy użyciu nowego zestawu SDK lub interfejsu wiersza polecenia. 

## <a name="what-about-the-old-sdk-and-cli"></a>Co stanie się ze starym zestawem SDK i interfejsem wiersza polecenia?

Tak, będą one nadal działały do stycznia. Zobacz powyższą [oś czasu](#timeline). Zalecamy rozpoczęcie tworzenia nowych eksperymentów i modeli przy użyciu najnowszego zestawu SDK lub interfejsu wiersza polecenia.

W najnowszej wersji nowy zestaw SDK języka Python umożliwia interakcję z usługą Azure Machine Learning w dowolnym środowisku języka Python. Dowiedz się, jak zainstalować najnowszy <a href="https://aka.ms/aml-sdk" target="_blank">zestaw SDK</a>. Można również użyć [zaktualizowanego rozszerzenia interfejsu wiersza polecenia usługi Azure Machine Learning](reference-azure-machine-learning-cli.md) z bogatym zestawem poleceń `az ml`, aby pracować z usługą w dowolnym środowisku wiersza polecenia, w tym w usłudze Azure Cloud Shell.

## <a name="what-about-azure-machine-learning-for-visual-studio-code"></a>A co z usługą Azure Machine Learning dla programu Visual Studio Code?

W najnowszej wersji usługa Azure Machine Learning dla programu Visual Studio Code została rozbudowana i ulepszona w celu umożliwienia współpracy ze wspomnianymi powyżej nowymi funkcjami.

[ ![Azure Machine Learning dla programu Visual Studio Code](./media/overview-what-happened-to-workbench/vscode.png)] (./media/overview-what-happened-to-workbench/vscode-big.png#lightbox)

## <a name="what-about-domain-packages"></a>Co się stanie z pakietami domen?

Pakietów domen funkcji [przetwarzania obrazów, analizy tekstu i prognozowania](../desktop-workbench/reference-python-package-overview.md) nie można używać z najnowszą wersją usługi Azure Machine Learning. Można jednak nadal kompilować i uczyć modele przetwarzania obrazów, analizy tekstu oraz prognozowania przy użyciu najnowszego zestawu <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a> języka Python dla usługi Azure Machine Learning. Aby dowiedzieć się, jak przeprowadzić migrację istniejących wcześniej modeli utworzonych przy użyciu pakietów przetwarzania obrazów, analizy tekstu i prognozowania, skontaktuj się z nami pod adresem [AML-Packages@microsoft.com](mailto:AML-Packages@microsoft.com).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [najnowszej architekturze usługi Azure Machine Learning](concept-azure-machine-learning-architecture.md). Wypróbuj jeden z przewodników Szybki start lub z samouczków:

* [Co to jest usługa Azure Machine Learning?](overview-what-is-azure-ml.md)
* [Szybki start: Tworzenie obszaru roboczego przy użyciu języka Python](quickstart-get-started.md)
* [Samouczek: trenowanie modelu](tutorial-train-models-with-aml.md)
