---
title: Informacje o wersji zestawu Azure SDK dla platformy .NET w wersji 2.7 i platformy .NET 2.7.1
description: Informacje o wersji zestawu Azure SDK dla platformy .NET w wersji 2.7 i platformy .NET 2.7.1
services: app-service\web
documentationcenter: .net
author: chrissfanos
editor: ''
ms.assetid: 877d070a-9bd5-49b3-8fac-6bb5f65c3554
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 02/24/2017
ms.author: juliako
ms.openlocfilehash: 01a02296354ebe3d60f0e1fda6a6da8554a265fd
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51239070"
---
# <a name="azure-sdk-for-net-27-and-net-271-release-notes"></a>Informacje o wersji zestawu Azure SDK dla platformy .NET w wersji 2.7 i platformy .NET 2.7.1
## <a name="overview"></a>Przegląd
Ten dokument zawiera informacje o wersji zestawu Azure SDK dla wersji platformy .NET w wersji 2.7. 

Dokument zawiera również informacje o wersji zestawu Azure SDK dla wersji platformy .NET 2.7.1.

Zestaw Azure SDK 2.7 jest obsługiwana tylko w programie Visual Studio 2015 i Visual Studio 2013. [Azure SDK 2.6](https://azure.microsoft.com/downloads/) jest obsługiwana przez ostatni zestaw SDK dla programu Visual Studio 2012.

Aby uzyskać szczegółowe informacje na temat tej wersji, zobacz [Azure SDK 2.7 ogłoszenie wpisu](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/) i [zestawu Azure SDK 2.7.1 ogłoszenie wpisu](https://go.microsoft.com/fwlink/?LinkId=623850).

## <a name="azure-sdk-for-net-27"></a>Azure SDK for .NET 2.7
### <a name="sign-in-improvements-for-visual-studio-2015"></a>Zaloguj się, ulepszenia programu Visual Studio 2015
Zestaw Azure SDK 2.7 dla programu Visual Studio 2015 obsługuje nowe funkcje zarządzania tożsamościami w programie Visual Studio 2015.  Obejmuje to obsługę kont platformy Azure za pomocą kontroli dostępu opartej na rolach, dostawców rozwiązań w chmurze, DreamSpark i innych kont i typów subskrypcji.

Ulepszenia logowania w dołączonej usłudze Azure SDK 2.7 są dostępne tylko w programie Visual Studio 2015. Dla programu Visual Studio 2013 są obsługiwane w zestawie Azure SDK 2.7.1.

### <a name="mobile-sdk"></a>Zestaw SDK mobilnych
Zaktualizowano **Mobile Apps** szablonów w celu odzwierciedlenia najnowszych [pakietu NuGet](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) i procesu instalacji.

### <a name="service-bus"></a>Service Bus
Ogólne poprawki błędów i ulepszenia. Informacje szczegółowe na temat aktualizacji i funkcji, można znaleźć na stronie z informacjami o najnowszych [NuGet usługi Service Bus](http://www.nuget.org/packages/WindowsAzure.ServiceBus/).

### <a name="hdinsight-tools"></a>Narzędzia HDInsight
W tej wersji wprowadzono następujące aktualizacje. Te aktualizacje są w wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [ten blog](https://go.microsoft.com/fwlink/?LinkId=619108).

* Wykresy gałęzi programu Hive w aplikacji Tez zadania
* Pełna obsługa IntelliSense DML programu Hive
* Obsługa szablonów pig
* Szablony STORM dla usług platformy Azure

#### <a name="breaking-changes"></a>Zmiany powodujące niezgodność
* Stary **Storm** projekt musi zostać uaktualniony, korzystając z tej wersji narzędzia. Aby uzyskać więcej informacji, zobacz [ten blog](https://go.microsoft.com/fwlink/?LinkId=619108).
* Visual Studio Web Express nie jest już obsługiwana. Aby uzyskać więcej informacji, zobacz [ten blog](https://go.microsoft.com/fwlink/?LinkId=619108).

### <a name="azure-app-service-tools"></a>Narzędzia usługi Azure App Service
W tej wersji wprowadzono następujące aktualizacje rozszerzeń narzędzi sieci Web. Aby uzyskać więcej informacji, zobacz [to](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/) blogu. 

* Obsługa kont programu DreamSpark dodanych
* Pełne narzędzi Azure zmieniane do obsługi nowych interfejsów API zarządzania zasobów platformy Azure
* Obsługa usługi Azure App Service dodana do [Eksploratora chmury](#cloud_explorer)

#### <a name="known-issues"></a>Znane problemy
Węzły miejsce wdrożenia aplikacji sieci Web nie są wyświetlane w węźle miejsc, w Eksploratorze serwera i węzłów podrzędnych miejsce wdrożenia aplikacji sieci Web nie są ładowane w ramach programu Cloud Explorer. Ten problem został rozwiązany i przygotowane do następnej wersji zestawu SDK. 

### <a name="cloud_explorer"></a>Eksplorator chmury dla programu Visual Studio 2015
Zestaw Azure SDK 2.7 zawiera Eksploratora chmury dla programu Visual Studio 2015, co pozwala na wyświetlanie zasobów platformy Azure, badania ich właściwości i akcje klucza dla deweloperów z poziomu programu Visual Studio. 

Eksplorator chmury obsługuje następujące funkcje:

* Grupa zasobów i typu zasobu widoki zasobów platformy Azure 
* Wyszukaj zasoby przez nazwę (dostępną w widoku typu zasobu)
* Obsługa subskrypcji i zasobów, które mają roli na podstawie kontroli dostępu (RBAC) stosowane 
* Zintegrowane panelu akcji, które pokazuje akcje dla deweloperów, specyficzne dla wybranych zasobów. Na przykład: Dołącz debugera zdalnego dla maszyn wirtualnych utworzonych za pomocą stosu usługi Resource Manager przejrzeć dane diagnostyczne dla maszyn wirtualnych itd.
* Zintegrowane panelu Właściwości, które są wyświetlane właściwości dla deweloperów, najczęściej potrzebne podczas tworzenia i testowania 
* Szybkie przełączanie konta do użycia podczas wyliczania zasobów (Użyj polecenia ustawień na pasku narzędzi) 
* Filtrowanie subskrypcji do użycia podczas wyliczania zasobów (Użyj polecenia ustawień na pasku narzędzi) 
* Linki bezpośrednie do witryny Azure Portal do zarządzania zasobami i grupami zasobów 

### <a name="azure-resource-manager-tools"></a>Narzędzia usługi Azure Resource Manager
Narzędzia usługi Azure Resource Manager zostały zaktualizowane do pracy z kontroli dostępu na podstawie ról (RBAC) i nowych typów subskrypcji.  Dołączone do tych zmian jest możliwość używania nowych kont magazynu, oprócz klasycznego magazynu do przechowywania artefaktów podczas wdrażania.  

Jeśli używasz projektu grupy zasobów platformy Azure z poprzedniej wersji zestawu SDK przy użyciu zestawu SDK w wersji 2.7, nowy skrypt wdrażania jest konieczne do wdrożenia przy użyciu nowego konta magazynu, a nie klasycznego magazynu.  Zostanie wyświetlony monit zanim zostały wprowadzone zmiany do swojego projektu można dodać nowego skryptu.  Nazwa starego skryptu zostanie zmieniona i trzeba będzie ręcznie wprowadzać żadnych zmian do nowego skryptu.

### <a name="storage-explorer-tools"></a>Narzędzia programu Storage Explorer
* Obsługa wyświetlania Uzupełnialnych obiektów blob. Więcej informacji w [ten wpis w blogu](https://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/13/introducing-azure-storage-append-blob.aspx). 
* Obsługa wyświetlania kont usługi Premium Storage za pomocą Eksploratora serwera. Eksplorator serwera będą wyświetlane tylko stronicowych obiektów blob dla kont usługi premium storage, ponieważ są one jedynym obsługiwanym typem dla kont usługi premium storage.

### <a name="azure-data-factory-tools-for-visual-studio"></a>Narzędzia usługi Azure Data Factory dla programu Visual Studio
Wprowadzenie do **narzędzia usługi Azure Data Factory** dla programu Visual Studio. Poniżej przedstawiono włączone funkcje. Zobacz [ten blog](https://go.microsoft.com/fwlink/?LinkId=617530) Aby uzyskać więcej informacji.

* **Szablon na podstawie tworzenia**: wybierz opcję Użyj — z uwzględnieniem wielkości liter, na podstawie szablonów, szablony przepływu danych lub przetwarzania danych szablonów do wdrażania rozwiązania integracji danych end-to-end i rozpocząć praktyczne szybko z usługą Data Factory. 
* **Integracja z Eksploratora rozwiązań do tworzenia i wdrażania jednostek usługi Data Factory**: tworzenie i wdrażanie potoków i powiązanymi jednostkami jako projektów programu Visual Studio. 
* **Integracja z widoku diagramu interakcji wizualnych podczas tworzenia**: wizualne tworzenie potoków i zestawów danych przy pomocy w widoku diagramu. 
* **Integracja za pomocą Eksploratora serwera do przeglądania i interakcji z jednostkami już wdrożonej**: Korzystanie z Eksploratora serwera do fabryki danych przeglądania już wdrożony i odpowiadających im elementów. Importuj fabrykę danych wdrożonej lub dowolnej jednostki (potok, połączonej usługi, zestawy danych) do projektu. 
* **Edycja JSON za pomocą funkcji intellisense sprawdzania poprawności i rozbudowane schematu**: efektywnego konfigurowania i edycji dokumentów JSON dla jednostek usługi Data Factory przy użyciu rozbudowanych funkcji intellisense i schematu sprawdzania poprawności 
* **Publikowanie Wielośrodowiskowego**: publikowanie utworzone potoki dev, test lub środowiska produkcyjnego, tworząc pliki konfiguracji osobne dla każdego środowiska.
* **Pig, Hive i .net na podstawie przetwarzania danych, obsługa**: Obsługa programów Pig i Hive skryptów w projekt usługi fabryka danych. Obsługa odwołujące się do C# projektami na potrzeby zarządzania .net działania.

## <a name="azure-sdk-for-net-271"></a>Zestaw Azure SDK dla programu .NET 2.7.1
Poniższa sekcja zawiera aktualizacje, które zostały wprowadzone w zestawie Azure SDK dla platformy .NET 2.7.1 wydania.

### <a name="hdinsight-tools"></a>Narzędzia HDInsight
Aby uzyskać szczegółowe informacje o aktualizacjach narzędzi HDInsight, zobacz [ten blog](https://go.microsoft.com/fwlink/?LinkId=623831).

* Widok operatorów hive — zadanie (Nowa funkcja)
  
    Aby lepiej zrozumieć zapytania Hive lepsze, funkcja Widok operatorów Hive został dodany. Aby zobaczyć wszystkie operatory wewnątrz wierzchołka, dwukrotnie kliknąć wierzchołki wykresu zadania. Aby wyświetlić więcej szczegółów dotyczących danego operatora, umieść kursor nad tego operatora.
* Znacznik błąd hive (Nowa funkcja)
  
    Aby umożliwić wyświetlanie błędy gramatyczne natychmiast, funkcja Hive znacznika błąd został dodany. Ponadto zostały rozszerzone komunikaty o błędach i możesz teraz wyświetlić błędy gramatyczne szczegółowe natychmiast (aż do tej wersji można było przesłać skrypt Hive w klastrze i poczekaj przez pewien czas, zanim uzyska szczegółowe informacje o Twojej komunikat o błędzie).  
* Wykres topologii STORM (Nowa funkcja)
  
    Wizualizacja jest bardzo ważne, jeśli chcesz zobaczyć, czy ta topologia działa zgodnie z oczekiwaniami. W tej wersji dodaliśmy wizualizacji dla wykresów Storm. Można wizualizować ważne metryki dla topologii (na przykład kolor, który wskazuje pogody niektórych Bolt jest "zajęta" lub nie). Możesz także dwukrotnie kliknąć element Bolt/Spout, aby wyświetlić więcej szczegółów.
* Obsługa klastrów HDInsight, które zostały utworzone w witrynie Azure Portal (poprawki)
  
    Visual Studio umożliwia teraz wyświetlanie i przesyłania zadań na wszystkie klastry usługi HDInsight niezależnie od tego, w którym zostały utworzone klastra.
* Więcej obsługę funkcji IntelliSense i metadanych programu Hive ładowania szybciej (poprawa)
  
    Firma Microsoft ulepszyła funkcja IntelliSense, dodając więcej sugestii przyjazny dla użytkownika. Na przykład aliasu tabeli można teraz również sugerowane przez funkcję IntelliSense, dzięki czemu można łatwiej napisać zapytanie. Ponadto firma Microsoft ulepszyła ładowania, potrwa ono tylko kilka sekund, aby wyświetlić listę wszystkich baz danych, tabele i kolumny z magazynu metadanych Hive metadanych programu Hive.

Aby uzyskać szczegółowe informacje o aktualizacjach narzędzi HDInsight, zobacz [ten blog](https://go.microsoft.com/fwlink/?LinkId=623831).

### <a name="improvements-in-visual-studio-2013"></a>Ulepszenia w programie Visual Studio 2013
* Zestaw Azure SDK 2.7.1 umożliwia programu Visual Studio 2013 do dostępu do konta platformy Azure i subskrypcje za pośrednictwem kontroli dostępu opartej na rolach, dostawców rozwiązań w chmurze i Dreamspark.
* Przy użyciu zestawu Azure SDK 2.7.1 nowego okna narzędzi programu Cloud Explorer jest teraz również dostępne w programie Visual Studio 2013.

### <a name="known-issues"></a>Znane problemy
Instalowanie zestawu SDK platformy Azure w wersji 2.6 lub 2.7.1 dla Visual Studio Community 2013 na inne niż angielskie systemu operacyjnego wyświetli ostrzeżenie, że zasobów angielski i innej niż angielska programu Visual Studio może pasować do siebie. To ostrzeżenie może bezpiecznie odrzucić. Tylko wystąpi, jeśli komputer nie zawierała wcześniejszej instalacji programu Visual Studio Community 2013 i zestawu SDK jest instalowany na inne niż angielskie systemu operacyjnego. To ostrzeżenie jest wyświetlany po pakiet językowy dotyczy zasobów wersji RTM programu Visual Studio, ale przed zastosowaniem aktualizacji Update 4. Odrzucanie ostrzeżenia umożliwi pakiet językowy, aby kontynuować, a ukończenie stosowania zawartości pakietu języka w wersji Update 4.

LightSwitch — projekty nie są compatibile w tej wersji. Ten problem zostanie rozwiązany w następnej wersji zestawu SDK.

## <a name="also-see"></a>Zobacz też
[Zestaw Azure SDK 2.7.1 ogłoszenie wpisu](https://go.microsoft.com/fwlink/?LinkId=623850)

[Azure SDK 2.7 ogłoszenie wpisu](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/)

[Pomoc techniczna i informacje o wycofaniu dla zestawu Azure SDK dla platformy .NET i interfejsów API](https://msdn.microsoft.com/library/azure/dn479282.aspx/)

