---
title: Porównanie produktów z zakresu uczenia maszynowego
titleSuffix: Microsoft
description: Porównanie różnych produktów firmy Microsoft do tworzenia i wdrażania modeli uczenia maszynowego oraz zarządzania nimi. Zdecyduj, które produkty wybrać dla danego rozwiązania.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: overview
ms.reviewer: jmartens
author: garyericson
ms.author: garye
ms.date: 1/17/2019
ms.openlocfilehash: d5660d1a4d5d2a26af57e2a2411552e3f6267379
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2019
ms.locfileid: "54389143"
---
# <a name="what-are-the-machine-learning-products-at-microsoft"></a>Jakie produkty z zakresu uczenia maszynowego oferuje firma Microsoft?

Firma Microsoft dostarcza różne rozwiązania z zakresu tworzenia i wdrażania modeli uczenia maszynowego oraz zarządzania nimi. Porównaj te produkty i wybierz najodpowiedniejsze do tworzenia swoich rozwiązań z zakresu uczenia maszynowego w najbardziej efektywny sposób.


**Opcje oparte na chmurze**

Następujące opcje są dostępne dla uczenia maszynowego w chmurze platformy Azure.

| Opcje&nbsp;w chmurze | Co to jest | Co można zrobić za jego pomocą |
|-|-|-|
| [Usługa Azure Machine Learning](#azure-machine-learning-services) | Zarządzana usługa uczenia maszynowego w chmurze  | Szkolenie i wdrażanie modeli na platformie Azure przy użyciu języka Python oraz interfejsu wiersza polecenia i zarządzanie nimi |
| [Azure Machine Learning Studio](#azure-machine-learning-studio) | Interfejs wizualny uczenia maszynowego z funkcją „przeciągnij i upuść” | Tworzenie i wdrażanie modeli przy użyciu wstępnie skonfigurowanych algorytmów oraz eksperymentowanie z nimi (języki Python i R)|
| [Azure Databricks](#azure-databricks) | Platforma analityczna oparta na projekcie Spark | Tworzenie i wdrażanie modeli i przepływów pracy danych |
| [Azure Cognitive Services](#azure-cognitive-services) | Usługi platformy Azure wykorzystujące gotowe modele sztucznej inteligencji i uczenia maszynowego | Łatwe dodawanie do aplikacji funkcji inteligentnych |
| [Azure Data Science Virtual Machine](#azure-data-science-virtual-machine) | Maszyna wirtualna ze wstępnie zainstalowanymi narzędziami do analizy danych | Tworzenie rozwiązań uczenia maszynowego we wstępnie skonfigurowanym środowisku |

**Opcje lokalne**

Następujące opcje są dostępne dla uczenia maszynowego w środowisku lokalnym. Serwery lokalne mogą być także uruchamiane na maszynie wirtualnej w chmurze.

| Opcje&nbsp;lokalne | Co to jest | Co można zrobić za jego pomocą |
|-|-|-|
| [SQL Server Machine Learning Services](#sql-server-machine-learning-services) | Aparat analityczny osadzony w języku SQL | Tworzenie i wdrażanie modeli w ramach platformy SQL Server |
| [Microsoft Machine Learning Server](#microsoft-machine-learning-server) | Autonomiczny serwer przedsiębiorstwa do analizy predykcyjnej | Tworzenie i wdrażanie modeli za pomocą języków R i Python |

**Narzędzia programistyczne**

Następujące narzędzia programistyczne są dostępne dla uczenia maszynowego.

| Narzędzia&nbsp;programistyczne | Co to jest | Co można zrobić za jego pomocą |
|-|-|-|
| [ML.NET](#mlnet) | Międzyplatformowy zestaw ML SDK typu „open source” | Tworzenie rozwiązań uczenia maszynowego dla aplikacji platformy .NET |
| [Windows ML](#windows-ml) | Platforma Windows 10 ML | Ocena przeszkolonych modeli na urządzeniu z systemem Windows 10 |







## <a name="azure-machine-learning-service"></a>Usługa Azure Machine Learning

[Azure Machine Learning Service](overview-what-is-azure-ml.md) to w pełni zarządzana usługa w chmurze umożliwiająca szkolenie i wdrażanie modeli ML oraz zarządzanie nimi na dużą skalę. W pełni obsługuje technologie typu „open source”, dzięki czemu można używać dziesiątków tysięcy pakietów języka Python, np. TensorFlow, PyTorch czy scikit-learn. Dostępne są również zaawansowane narzędzia, np. [Notesy Azure](https://notebooks.azure.com/), [Notesy Jupyter](http://jupyter.org) lub rozszerzenie [Azure Machine Learning for Visual Studio Code](https://aka.ms/vscodetoolsforai), które ułatwiają analizowanie i przekształcanie danych, a następnie szkolenie i wdrażanie modeli. Usługa Azure Machine Learning obejmuje funkcje, które automatyzują generowanie i dostrajanie modeli w sposób prosty, wydajny i prawidłowy.

Usługa Azure Machine Learning umożliwia szkolenie i wdrażanie modeli uczenia maszynowego oraz zarządzanie nimi przy użyciu języka Python oraz interfejsu wiersza polecenia w skali chmury.

Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](http://aka.ms/AMLFree) już dziś.

## <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio

[Azure Machine Learning Studio](../studio/what-is-ml-studio.md) zapewnia interaktywny, wizualny obszar roboczy, który umożliwia łatwe i szybkie tworzenie, testowanie i wdrażanie modeli za pomocą wbudowanych algorytmów uczenia maszynowego. Usługa Machine Learning Studio publikuje modele jako usługi sieci Web, które mogą być łatwo używane w niestandardowych aplikacjach albo narzędziach do analiz biznesowych, takich jak program Excel.
Programowanie nie jest wymagane — model uczenia maszynowego jest konstruowany przez połączenie zestawów danych i modułów analizy na interaktywnym panelu kanwy, a następnie wdrożenie go za pomocą kilku kliknięć.

Korzystaj z usługi Machine Learning Studio, gdy chcesz tworzyć i wdrażać modele bez konieczności użycia kodu.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="azure-databricks"></a>Azure Databricks

[Azure Databricks](/azure/azure-databricks/what-is-azure-databricks) to platforma analizy oparta na usłudze Apache Spark i zoptymalizowana pod kątem platformy usług w chmurze Microsoft Azure. Usługa Databricks jest zintegrowana z platformą Azure w celu zapewnienia konfigurowania jednym kliknięciem, usprawnionych przepływów pracy oraz interaktywnego obszaru roboczego, który umożliwia współpracę między analitykami danych, inżynierami danych i analitykami biznesowymi.
Użyj kodu języka Python, R, Scala i SQL w notesach opartych na sieci Web do wysyłania zapytań o dane, ich wizualizacji i modelowania.

Użyj usługi Databricks, gdy chcesz współpracować przy tworzeniu rozwiązań w zakresie uczenia maszynowego na platformie Apache Spark.

## <a name="azure-cognitive-services"></a>Azure Cognitive Services

Usługi [Azure Cognitive Services](/azure/cognitive-services/welcome) to zestaw interfejsów API, które umożliwiają tworzenie aplikacji korzystających z naturalnych metod komunikacji. Te interfejsy API pozwalają aplikacjom dostrzegać, słyszeć, wypowiadać, rozumieć i interpretować potrzeby użytkowników — wystarczy tylko kilka wierszy kodu. Można łatwo dodawać do aplikacji inteligentne funkcje, takie jak: 

- Wykrywanie emocji i tonacji
- Rozpoznawanie obrazów i mowy
- Language Understanding (LUIS)
- Wiedza i wyszukiwanie

Usług Cognitive Services można używać do opracowywania aplikacji przeznaczonych dla różnych urządzeń i platform. Interfejsy API są stale udoskonalane i łatwe do skonfigurowania.

## <a name="azure-data-science-virtual-machine"></a>Maszyna wirtualna do analizy danych na platformie Azure

[Azure Data Science Virtual Machine](../data-science-virtual-machine/overview.md) to dostosowane środowisko maszyny wirtualnej znajdujące się w chmurze Microsoft Azure, opracowane specjalnie na potrzeby analizy danych. Zawiera ona wiele wstępnie zainstalowanych i skonfigurowanych, popularnych narzędzi do analizy danych, które pozwalają szybko rozpocząć tworzenie inteligentnych, zaawansowanych aplikacji analitycznych.

Usługa Data Science Virtual Machine jest obsługiwana jako miejsce docelowe usługi Azure Machine Learning.
Jest ona dostępna w wersjach zarówno dla systemu Windows, jak i Linux Ubuntu (usługa Azure Machine Learning Service nie jest obsługiwana w systemie Linux CentOS).
Aby uzyskać informacje dotyczące określonej wersji oraz listę dołączonych elementów, zobacz temat [Wprowadzenie do usługi Azure Data Science Virtual Machine](../data-science-virtual-machine/overview.md).

Maszyna wirtualna do analizy danych pozwala uruchamiać lub hostować zadania w jednym węźle. Można również jej użyć, gdy trzeba zdalnie skalować w górę przetwarzanie na jednej maszynie.

## <a name="sql-server-machine-learning-services"></a>SQL Server Machine Learning Services

[Usługa SQL Server Microsoft Machine Learning](https://docs.microsoft.com/sql/advanced-analytics/r/r-services) dodaje analizę statystyczną, wizualizację danych i analizę predykcyjną w językach R i Python dla danych relacyjnych w bazach danych platformy SQL Server. Biblioteki języków R i Python firmy Microsoft obejmują zaawansowane modelowanie i algorytmy uczenia maszynowego, które mogą być uruchamiane równolegle i na dużą skalę na platformie SQL Server.

Usług SQL Server Machine Learning Services należy używać, gdy potrzebna jest wbudowana sztuczna inteligencja i analiza predykcyjna danych relacyjnych na platformie SQL Server.

## <a name="microsoft-machine-learning-server"></a>Serwer Microsoft Machine Learning

Przeznaczony dla przedsiębiorstw [serwer Microsoft Machine Learning](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server) umożliwia hostowanie równoległych i rozproszonych obciążeń procesów języków R i Python oraz zarządzanie nimi. Serwer Microsoft Machine Learning działa w systemach Linux i Windows oraz na platformach Hadoop i Apache Spark oraz jest dostępny w usłudze [HDInsight](https://azure.microsoft.com/services/hdinsight/r-server/). Zapewnia aparat wykonywania dla rozwiązań utworzonych za pomocą [RevoScaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), [revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package) i [pakietów MicrosoftML](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package) oraz rozszerza języki R i Python typu „open source” o obsługę analizy o wysokiej wydajności, analizę statystyczną, uczenie maszynowe oraz bardzo duże zestawy danych. Ta funkcja jest udostępniana za pośrednictwem zastrzeżonych pakietów instalowanych razem z serwerem. Programowanie odbywa się w środowiskach IDE, takich jak [R Tools for Visual Studio](https://www.visualstudio.com/vs/rtvs/) i [Python Tools for Visual Studio](https://www.visualstudio.com/vs/python/).

Serwera Microsoft Machine Learning należy użyć, gdy trzeba skompilować modele utworzone w językach R i Python i obsługiwać ich operacje na serwerze lub rozpowszechnić szkolenie dotyczące języków R i Python na dużą skalę w klastrze usługi Hadoop lub Spark.

## <a name="mlnet"></a>ML.NET

[ML.NET](https://docs.microsoft.com/dotnet/machine-learning/) jest bezpłatną międzyplatformową strukturą do uczenia maszynowego typu „open source”, która umożliwia tworzenie niestandardowych rozwiązań uczenia maszynowego oraz ich integrowanie z aplikacjami platformy .NET.

Używaj struktury ML.NET, gdy chcesz integrować rozwiązania uczenia maszynowego z aplikacjami platformy .NET.

## <a name="windows-ml"></a>Windows ML

Platforma [Windows ML](https://docs.microsoft.com/windows/uwp/machine-learning/) pozwala na używanie w swoich aplikacjach przeszkolonych modeli uczenia maszynowego, oceniając lokalnie przeszkolone modele na urządzeniach z systemem Windows 10.

Używaj platformy Windows ML, gdy chcesz korzystać z przeszkolonych modeli uczenia maszynowego w swoich aplikacjach systemu Windows.

## <a name="next-steps"></a>Następne kroki

- Aby poznać wszystkie produkty programistyczne korzystające ze sztucznej inteligencji (AI) udostępniane przez firmę Microsoft, zobacz informacje dotyczące [platformy Microsoft AI](https://www.microsoft.com/ai)
- Aby uzyskać dostęp do szkolenia na temat tworzenia rozwiązań AI, odwiedź stronę [Microsoft AI School](https://aischool.microsoft.com/learning-paths)
