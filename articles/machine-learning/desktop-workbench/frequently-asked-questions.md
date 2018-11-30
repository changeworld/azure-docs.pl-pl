---
title: Usługi Azure Machine Learning 2017 (wersja zapoznawcza) — często zadawane pytania | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera często zadawane pytania i odpowiedzi dla usługi Azure Machine Learning w wersji zapoznawczej
services: machine-learning
author: serinakaye
ms.author: serinak
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 08/30/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 45cf987d9af7b7dd0e8f05056b49ba56835603e7
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2018
ms.locfileid: "52313948"
---
# <a name="azure-machine-learning-frequently-asked-questions"></a>Usługa Azure Machine Learning — często zadawane pytania

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 

Usługę Azure Machine Learning to w pełni zarządzana usługa platformy Azure, która pozwala na tworzenie, testowanie, zarządzanie i wdrażanie modeli AI uczenia maszynowego i. Nasze usługi i aplikacja do pobrania oferują podejście najpierw kod, który korzysta z chmury, lokalnie i urządzeniami brzegowymi w celu zapewnienia pociągu, wdrażania, zarządzania i monitorowania modeli z mocy obliczeniowej, szybkość i elastyczność. Alternatywnie Azure Machine Learning Studio oferuje oparte na przeglądarce przeciągnij i upuść środowisko tworzenia zawartości, gdy wymagane jest bez kodowania. 

## <a name="general-product-questions"></a>Pytania ogólne produktu

**Jakie inne usługi platformy Azure są potrzebne?**

Usługa Azure Blob Storage i Azure Container Registry, są używane przez usługi Azure Machine Learning. Ponadto należy aprowizować zasobów obliczeniowych, takich jak maszyna wirtualna do nauki o danych lub HDInsight klastra. Moc obliczeniowa i hosting również są wymagane w przypadku wdrażania usług internetowych, takich jak [usługi Azure Container Service](https://docs.microsoft.com/azure/aks).

**Jaki jest związek między usługi Azure Machine Learning Microsoft Machine Learning Services w programie SQL Server 2017?**   

Machine Learning Services w programie SQL Server 2017 to rozszerzalny i skalowalnej platformy integracji zadania uczenia maszynowego w ramach przepływów pracy w bazie danych. Jest idealnym rozwiązaniem w scenariuszach, gdzie jest wymagane, na przykład, gdzie przenoszenie danych jest kosztowne lub trudną rozwiązania lokalnego. Z kolei obciążenia w chmurze czy hybrydowa to doskonałe rozwiązanie dla naszej nowych usług platformy Azure. 

**Jak usługi Azure Machine Learning odnoszą się do usługi Microsoft Machine Learning dla platformy Spark?**

MMLSpark zapewnia uczenia głębokiego i narzędzia do analizy danych dla platformy Apache Spark z naciskiem na wydajność, do jej obsługi ułatwiają eksperymentowanie i z najnowocześniejszych algorytmów. MMLSpark oferuje integrację potoków uczenia maszynowego platformy Spark z biblioteki OpenCV i Microsoft Cognitive Toolkit. Można tworzyć zaawansowane, wysoko skalowane modele predykcyjne i analityczne, w przypadku danych tekstowych i obrazów. MMLSpark jest dostępna w ramach licencji open source i znajduje się w aplikacji Workbench AML jako zbiór algorytmów i modeli w użyciu. Aby uzyskać więcej informacji na temat MMLSpark odwiedź stronę naszej dokumentacji produktu. 

**Które wersje platformy Spark są obsługiwane przez nowe narzędzia i usługi?**

Środowisko robocze obecnie zawiera i obsługuje MMLSpark wersji 0.8, która jest zgodna z platformy Apache Spark 2.1. Istnieje również możliwość użycia obraz platformy Docker z włączonymi procesorami GPU MMLSpark 0,8 maszyn wirtualnych systemu Linux.

## <a name="experimentation-service"></a>Usługa eksperymentowanie w usłudze

**Co to jest usługa eksperymentowanie w usłudze Azure Machine Learning?**

Usługa eksperymentowanie w usłudze to zarządzana usługa platformy Azure, która przyjmuje eksperymentowanie w usłudze machine learning na następny poziom. Eksperymenty można tworzyć lokalnie lub w chmurze. Szybko Utwórz prototyp na pulpicie, następnie Skaluj do maszyn wirtualnych lub klastry Spark. Maszyny wirtualne platformy Azure z najnowszą technologię procesorów GPU umożliwiają prowadzenie uczenia głębokiego, szybko i skutecznie. Dołączono również ścisłą integrację z usługą Git, dzięki czemu można łatwo podłączyć do istniejących przepływów pracy do śledzenia kodu, konfiguracji i współpracy. 

**Jak będzie rozliczany dla usługi eksperymentowanie?**

Pierwszych dwóch użytkowników skojarzonych z Twojej usługi eksperymentowanie w usłudze Azure Machine Learning są bezpłatne. Dodatkowych użytkowników będą naliczane zgodnie ze stawką publicznej wersji zapoznawczej w wysokości 50 USD/miesiąc. Aby uzyskać więcej informacji dotyczących cen i rozliczeń odwiedź naszą stronę cennika.

**Zostanie naliczona zależnie od liczby przeprowadzonych eksperymentów?**

Nie, usługa eksperymentowanie umożliwia wykonanie dowolnej liczby eksperymentów, jak potrzeby i opłaty tylko na podstawie liczby użytkowników. Opłaty za zasoby obliczeniowe usługi Eksperymentowanie są naliczane oddzielnie. Firma Microsoft zachęca do przeprowadzania licznych eksperymentów, dzięki czemu można znaleźć najbardziej odpowiednią modelu dla danego rozwiązania.   

**Jakich konkretnych rodzajów zasobów obliczeniowych i magazynu można używać?**

Usługa eksperymentowanie umożliwia przeprowadzanie eksperymentów na komputerach lokalnych (bezpośrednio lub na platformie docker), [maszyn wirtualnych platformy Azure](https://azure.microsoft.com/services/virtual-machines/), i [HDInsight](https://azure.microsoft.com/services/hdinsight/). Usługa również uzyskuje dostęp do [usługi Azure Storage](https://azure.microsoft.com/services/storage/) konta do przechowywania wyników wykonywania i można korzystać z [Visual Studio Team Service](https://azure.microsoft.com/services/visual-studio-team-services/) konto do kontroli wersji oraz magazynu usługi Git. Należy pamiętać, że opłata zostanie naliczona niezależnie dla wszystkie użyte zasoby obliczeniowe i magazynowe, na podstawie odpowiednich cenników.  


## <a name="model-management"></a>Zarządzanie modelami

**Co to jest zarządzanie modelami w usłudze Azure Machine Learning?**

Zarządzanie modelami usługi Azure Machine Learning to zarządzana usługa platformy Azure, który umożliwia zespołom naukowców i deweloperów i operatorów danych niezawodne wdrażanie modeli predykcyjnych w bardzo różnych środowiskach. Repozytoria Git i kontenerów platformy Docker zapewniają możliwości śledzenia i powtarzalność. Modele można wdrożyć niezawodne w chmurze, lokalnie lub krawędzi. Jeden raz w środowisku produkcyjnym można Zarządzaj wydajnością modeli, a następnie aktywnie Ponowne szkolenie w przypadku spadku wydajności. Można wdrażać modele na komputerach lokalnych do [maszyn wirtualnych platformy Azure](https://azure.microsoft.com/services/virtual-machines/), platformy Spark na [HDInsight](https://azure.microsoft.com/services/hdinsight/) lub Kubernetes, zorganizowanych [usługi Azure Container Service](https://azure.microsoft.com/services/container-service/) klastrów.  

**Co to jest "model"?**

Model jest, czy dane wyjściowe eksperymentowania uruchomienia został podniesiony do zarządzania modelami. Model, który jest zarejestrowany na koncie hostingu są przeliczane względem plan, tym zaktualizować za pośrednictwem iteracji wersji lub ponowne trenowanie modeli.

**Co to jest "model zarządzany"?**

Model to wynik procesu uczenia, który jest zastosowaniem algorytmu uczenia maszynowego do danych treningowych. Zarządzanie modelami umożliwia wdrażanie modeli jako usług sieci web, zarządzanie różnymi wersjami modeli oraz monitorowanie ich wydajności i metryki. "Zarządzany" modele zostały zarejestrowane przy użyciu konta Zarządzanie modelami w usłudze Azure Machine Learning. Jako przykład rozważ scenariusz, w którym próbujesz prognozować sprzedaż. W fazie eksperymentowanie możesz wygenerować wiele modeli za pomocą różnych zestawów danych lub algorytmów. Wygenerowania czterech modeli o różnej dokładności, ale wybrać zarejestrowanie tylko model o największej dokładności. Model, który jest zarejestrowany staje się pierwszym modelu zarządzanych.
 
**Co to jest "wdrożenie?"**

Zarządzanie modelami umożliwia wdrażać modele w formie spakowanych kontenerów usług internetowych na platformie Azure. Usługi sieci web może być wywoływany przy użyciu interfejsów API REST. Każda usługa internetowa jest liczona jako pojedyncze wdrożenie, a łączna liczba aktywnych wdrożeń liczy się do planu. Za pomocą przykładzie prognozowania sprzedaży, wdrażając najlepiej działający model, plan jest zwiększany przez jedno wdrożenie. Jeśli następnie wytrenujesz i wdrożyć innej wersji, możesz mieć dwa wdrożenia. Jeśli stwierdzisz, że nowy model jest lepszy i Usuń oryginał, liczba Twoich wdrożeń jest zmniejszana o jeden.  

**Jakie zasoby obliczeniowe określone są dostępne dla mojego wdrożenia?** 

Zarządzanie modelami umożliwia uruchamianie wdrożeń, jako kontenery platformy Docker zarejestrowany [usługi Azure Container Service](https://azure.microsoft.com/services/container-service/), jako [maszyn wirtualnych platformy Azure](https://azure.microsoft.com/services/virtual-machines/), lub na maszynach lokalnych. Cele wdrożenia dodatkowych zostaną dodane wkrótce. Należy pamiętać, że opłata zostanie naliczona niezależnie dla wszystkie użyte zasoby obliczeniowe, na podstawie odpowiednich cenników.     

**Czy można użyć Zarządzanie modelami w usłudze Machine Learning, aby wdrażać modele utworzone przy użyciu narzędzi innych niż Usługa eksperymentowanie?**

Możesz uzyskać najlepsze wyniki, wdrażając modele utworzone przy użyciu usługi eksperymentowanie w usłudze. Jednak można wdrażać modele utworzone przy użyciu innych struktur i narzędzi. Firma Microsoft wspiera wiele modeli, w tym MMLSpark, TensorFlow, Microsoft Cognitive Toolkit, scikit-dowiedzieć się więcej, Keras itd. 

**Można użyć własnych zasobów platformy Azure?**

Tak, usługa eksperymentowanie i zarządzanie modelami działają w połączeniu z wielu magazynów danych na platformie Azure, obciążenia i innych usług obliczeniowych. Zapoznaj się z naszą dokumentację techniczną, aby uzyskać więcej szczegółowych informacji dotyczących wymaganych usług platformy Azure.

**Sposób obsługi swoich lokalnych i scenariuszy wdrażania w chmurze?**

Tak. Firma Microsoft obsługuje w środowisku lokalnym i scenariusze wdrażania za pomocą kontenerów platformy Docker w chmurze. Wykonanie lokalne obiekty docelowe obejmują: wdrożenia platformy Docker w pojedynczym węźle [programu Microsoft SQL Server z usługami uczenia Maszynowego](https://docs.microsoft.com/sql/advanced-analytics/r/r-services), Hadoop i Spark. Obsługujemy również chmury wdrożeń za pomocą platformy Docker, w tym: klastrowany wdrożeń przy użyciu klastrów usługi Azure Container Service i Kubernetes, HDInsight i platformy Spark. Scenariusze Edge są obsługiwane za pośrednictwem kontenerów platformy Docker i Azure IOT Edge. 

**Czy można uruchomić obrazu platformy Docker, który został utworzony na innym hoście za pomocą interfejsu wiersza polecenia usługi Azure Machine Learning?**

Tak. Obraz, który służy jako usługi sieci web na dowolnym hoście platformy docker tak długo, jak host ma wystarczające zasoby obliczeniowe do hostowania obrazu platformy docker.

**Czy ponowne trenowanie modeli wdrożone jest obsługiwana?**

Tak, można wdrożyć wiele wersji tego samego modelu. Zarządzanie modelami w usłudze będzie obsługiwać aktualizacje usług dla wszystkich zaktualizowane modeli i obrazy.

## <a name="workbench"></a>Workbench

**Co to jest Azure Machine Learning Workbench?**

Azure Machine Learning Workbench jest aplikacją pomocnika, stworzona z myślą o profesjonalnych analityków. Dostępne dla Windows i Mac, Machine Learning Workbench zawiera omówienie, zarządzania i sterowania dla rozwiązania uczenia maszynowego. Machine Learning Workbench obejmuje dostęp do stworzenia struktury sztucznej Inteligencji przez firmę Microsoft i społeczności "open source". Dołączyliśmy najbardziej popularnych narzędzi do analizy danych w tym TensorFlow, Microsoft Cognitive Toolkit, Spark ML i scikit-Dowiedz się i nie tylko. Umożliwiliśmy również integrację z popularnymi do nauki o danych środowiska IDE, takie jak notesów programu Jupyter, platformy PyCharm i programu Visual Studio Code. Machine Learning Workbench ma możliwości przygotowania danych wbudowane, szybko przykładowy, zrozumieć i przygotować dane ze strukturą lub bez. Nasze nowe narzędzia do przygotowywania danych, nazywane [PROSE](https://microsoft.github.io/prose/), jest oparta na najnowocześniejszych technologii przez firmę Microsoft Research.  

**To środowisko robocze środowisko IDE?**

Nie. Machine Learning Workbench została zaprojektowana jako dodatek do popularnych środowisk IDE, takich jak notesów Jupyter i programu Visual Studio Code, platformy PyCharm, ale nie jest to w pełni funkcjonalne środowisko IDE. Machine Learning Workbench oferuje kilka tekst podstawowych funkcji edycji, ale debugowanie, funkcję intellisense i inne często używane funkcje środowiska IDE nie są obsługiwane. Zalecamy użycie ulubionego środowiska IDE do tworzenia kodu, edytowanie i debugowanie. Możesz również spróbować [narzędzia Visual Studio Code dla sztucznej Inteligencji](https://www.visualstudio.com/downloads/ai-tools-vscode).

**Czy jest opłata za pomocą usługi Azure Machine Learning Workbench?**

Nie. Usługa Azure Machine Learning Workbench jest aplikacją bezpłatną. Możesz ją pobrać dla tylu komputerów i użytkowników, dla ilu potrzebujesz. Aby korzystać z aplikacji Azure Machine Learning Workbench, musisz mieć konto usługi Eksperymentowanie. .  

**Funkcje wiersza polecenia są obsługiwane?**

Tak, usługa Azure Machine Learning oferuje pełny interfejs wiersza polecenia. Interfejsu wiersza polecenia Machine Learning jest instalowany domyślnie z aplikacji Azure Machine Learning Workbench. Jest również udostępniany w ramach maszyny wirtualnej do analizy danych z systemem Linux na platformie Azure i zostanie zintegrowana z [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)


**Czy można używać notesów programu Jupyter, z aplikacji Workbench?**

Tak! Możesz uruchomić notesów programu Jupyter w aplikacji Workbench przy użyciu aplikacji Workbench jako aplikacja obsługi klienta, tak samo, jak możesz użyć przeglądarki jako klienta. 

**Które jądra notesów programu Jupyter są obsługiwane?**

Bieżąca wersja programu Jupyter, dołączone do aplikacji Workbench uruchamia jądra Python 3 i dodatkowe jądra dla każdego pliku "runconfig" w folderze aml_config. Obsługiwane konfiguracje obejmują:
- Lokalne języka Python
- Język Python w lokalnym lub zdalnym platformy Docker

## <a name="data-formats-and-capabilities"></a>Formaty danych i możliwości

**Formaty plików, które są obecnie obsługiwane w celu pozyskiwania danych w aplikacji Workbench?**

Narzędzia do przygotowywania danych w aplikacji Workbench obecnie obsługuje pozyskiwanie z następujących formatów: 
- Pliki rozdzielane CSV, TSV, np.  
- Pliki o stałej szerokości
- Pliki w postaci zwykłego tekstu
- Excel (xls/xlsx)
- Pliki JSON
- Pliki parquet 
- Niestandardowe pliki (skrypt), jeśli rozwiązanie wymaga wprowadzania danych z dodatkowych źródeł, kod języka Python może służyć do... 

**Lokalizacji magazynu danych, które są obecnie obsługiwane?**

W publicznej wersji zapoznawczej aplikacja Workbench obsługuje pozyskiwanie danych z: 
- Lokalny dysk twardy lub lokalizacji magazynu mapowanej sieci
- Usługa Azure blob Storage lub Azure Storage (wymaga subskrypcji platformy Azure)
- Serwer usługi SQL Azure
- Microsoft SQL Server


**Jakiego rodzaju inteligencji danych, przygotowywania i przekształcenia są dostępne?**

W publicznej wersji zapoznawczej aplikacja Workbench obsługuje "Pochodzi kolumny według przykładu", "Podział kolumny według przykładu", "Klaster tekst", "Obsługi brakuje wartości" i wiele innych.  Środowisko robocze obsługuje również konwersja typu danych, agregacji danych (liczba, średnia, odchylenie itp.) i danych złożonych sprzężeń. Aby uzyskać pełną listę obsługiwanych możliwości odwiedź stronę naszej dokumentacji produktu. 

**Czy istnieją jakiekolwiek ograniczenia rozmiaru danych wymuszane przez aplikację Azure Machine Learning Workbench, eksperymentowanie w usłudze lub Zarządzanie modelami w usłudze?**

Nie, nowych usług nie nakładają żadnych ograniczeń danych. Istnieją jednak ograniczenia wprowadzone przez środowisko, w którym działają przygotowywania danych, szkoleń modelowych, eksperymentowanie w usłudze lub wdrożenia. Na przykład jeśli obiektem docelowym środowisku lokalnym, szkolenia, możesz jest ograniczona ilość wolnego miejsca na dysku twardym. Jeśli obiektem docelowym HDInsight, możesz są ograniczone według dowolnego rozmiaru skojarzone lub obliczeń ograniczenia. 

## <a name="algorithms-and-libraries"></a>Algorytmy i bibliotek

**Jakie algorytmy są obsługiwane w usłudze Azure Machine Learning Workbench?**

(Wersja zapoznawcza), produktów i usług obejmują najlepsze społeczności "open source". Obsługujemy szeroki zakres algorytmów i bibliotek, w tym TensorFlow, scikit-dowiedzieć się, Apache Spark i Microsoft Cognitive Toolkit. Również pakiety Azure Machine Learning Workbench [biblioteki Microsoft](https://docs.microsoft.com/sql/advanced-analytics/python/what-is-revoscalepy) pakietu.

**Jaki jest związek między usługi Azure Machine Learning Microsoft Cognitive Toolkit?**

[Microsoft Cognitive Toolkit](https://docs.microsoft.com/cognitive-toolkit/) jest jednym z wielu platform obsługiwanych przez naszych nowych narzędzi i usług. Zestawu narzędzi Cognitive Toolkit to ujednolicone narzędzi uczenia głębokiego, która pozwala na wykorzystanie i łączyć popularne usługi machine learning modeli, w tym głębokie sieci neuronowe do przodu źródła danych, sieci Splotowe Sequence-Sequence oraz sieci. Aby uzyskać więcej informacji na temat zestawu narzędzi Microsoft Cognitive Toolkit, odwiedź stronę naszej [dokumentacji produktu](https://docs.microsoft.com/cognitive-toolkit/). 