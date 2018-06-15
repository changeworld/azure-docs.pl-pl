---
title: Zestaw Azure SDK dla platformy .NET 2.7 i .NET 2.7.1 — informacje o wersji
description: Zestaw Azure SDK dla platformy .NET 2.7 i .NET 2.7.1 — informacje o wersji
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
ms.openlocfilehash: 9a69253129cdedc4f5d7e736d5bd8d6a68f95a1e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23836721"
---
# <a name="azure-sdk-for-net-27-and-net-271-release-notes"></a>Zestaw Azure SDK dla platformy .NET 2.7 i .NET 2.7.1 — informacje o wersji
## <a name="overview"></a>Omówienie
Ten dokument zawiera informacje o wersji dla zestawu Azure SDK w wersji .NET 2.7. 

Dokument zawiera również informacje o wersji dla zestawu Azure SDK dla wersji .NET 2.7.1.

2.7 zestawu SDK platformy Azure jest obsługiwana tylko w programie Visual Studio 2015 i Visual Studio 2013. [2.6 zestawu SDK platformy Azure](https://azure.microsoft.com/downloads/) ostatniego obsługiwane zestawu SDK dla programu Visual Studio 2012.

Aby uzyskać szczegółowe informacje na temat tej wersji, zobacz [post anonsu Azure SDK 2.7](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/) i [zestawu Azure SDK 2.7.1 anonsu post](http://go.microsoft.com/fwlink/?LinkId=623850).

## <a name="azure-sdk-for-net-27"></a>Azure SDK for .NET 2.7
### <a name="sign-in-improvements-for-visual-studio-2015"></a>Zaloguj się ulepszenia dla programu Visual Studio 2015
Azure 2.7 zestawu SDK dla programu Visual Studio 2015 obsługuje nowe funkcje zarządzania tożsamościami w programie Visual Studio 2015.  Dotyczy to również obsługi kont dostępu do platformy Azure za pomocą kontroli dostępu opartej na rolach, dostawców rozwiązań w chmurze, DreamSpark i innych typów konto i subskrypcję.

Ulepszenia logowania dołączonego 2.7 zestawu SDK platformy Azure są dostępne tylko w programie Visual Studio 2015. Dla programu Visual Studio 2013 są obsługiwane w zestawie Azure SDK 2.7.1.

### <a name="mobile-sdk"></a>Przenośne zestawu SDK
Zaktualizowano **Mobile Apps** szablonów w celu uwzględnienia najnowszych [pakietu NuGet](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) i konfiguracji.

### <a name="service-bus"></a>Service Bus
Ogólne poprawki i ulepszenia. Szczegółowe informacje na temat aktualizacji i funkcji, zapoznaj się z informacjami o najnowszych [NuGet usługi Service Bus](http://www.nuget.org/packages/WindowsAzure.ServiceBus/).

### <a name="hdinsight-tools"></a>Narzędzia HDInsight Tools
W tej wersji wprowadzono następujące aktualizacje. Te aktualizacje są w wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [ten blog](http://go.microsoft.com/fwlink/?LinkId=619108).

* Wykresy gałęzi do gałęzi w aplikacji Tez zadania
* Pełna obsługa Hive DML IntelliSense
* Obsługa szablonów pig
* Szablony STORM do usług platformy Azure

#### <a name="breaking-changes"></a>Fundamentalne zmiany
* Stary **Storm** projekt musi zostać uaktualniony, korzystając z tej wersji narzędzi. Aby uzyskać więcej informacji, zobacz [ten blog](http://go.microsoft.com/fwlink/?LinkId=619108).
* Visual Studio Web Express nie jest już obsługiwana. Aby uzyskać więcej informacji, zobacz [ten blog](http://go.microsoft.com/fwlink/?LinkId=619108).

### <a name="azure-app-service-tools"></a>Narzędzia usługi aplikacji Azure
W tej wersji wprowadzono następujące aktualizacje rozszerzeń narzędzi sieci Web. Aby uzyskać więcej informacji, zobacz [to](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/) blogu. 

* Obsługa kont DreamSpark dodane
* Pełna zmiany do obsługi nowych interfejsów API zarządzania zasobów Azure narzędzia Azure
* Obsługa usługi Azure App Service dodane do [Eksplorator chmury](#cloud_explorer)

#### <a name="known-issues"></a>Znane problemy
Węzły miejsca wdrożenia aplikacji sieci Web nie są wyświetlane w węźle miejsc, w Eksploratorze serwera i węzłów podrzędnych miejsca wdrożenia aplikacji sieci Web nie są ładowane w obszarze Eksplorator chmury. Ten problem zostało rozwiązane i przygotowane do następnej wersji zestawu SDK. 

### <a name="cloud_explorer"></a>Cloud Explorer for Visual Studio 2015
2.7 zestawu SDK platformy Azure obejmuje Cloud Explorer for Visual Studio 2015, dzięki czemu można wyświetlić zasobów platformy Azure, sprawdzić ich właściwości i akcje developer klucza z poziomu programu Visual Studio. 

Eksplorator chmury obsługuje następujące funkcje:

* Typ zasobu i grupy zasobów widoków zasobów platformy Azure 
* Wyszukiwanie zasobów według nazwy (dostępne w widoku typu zasobu)
* Obsługa subskrypcji i zasoby, które mają roli na podstawie kontroli dostępu (RBAC) stosowane 
* Zintegrowane panelu akcji, które zawiera akcje fokus developer specyficzne dla wybranych zasobów. Na przykład: Dołącz debugera zdalnego dla maszyn wirtualnych utworzonych przy użyciu stosie usługi Resource Manager wyświetlić dane diagnostyczne dla maszyn wirtualnych itd.
* Zintegrowane panelu Właściwości, które są wyświetlane właściwości fokus developer zazwyczaj potrzebne podczas tworzenia/testowania 
* Szybkie przełączanie konto do użycia podczas wyliczania zasobów (Użyj ustawień polecenia na pasku narzędzi) 
* Filtrowanie subskrypcji do użycia podczas wyliczania zasobów (Użyj ustawień polecenia na pasku narzędzi) 
* Linków bezpośrednich do portalu Azure do zarządzania, zasobów i grup zasobów 

### <a name="azure-resource-manager-tools"></a>Narzędzia Menedżera zasobów Azure
Narzędzia Menedżera zasobów Azure zostały zaktualizowane do pracy z kontroli dostępu na podstawie ról (RBAC) i nowe typy subskrypcji.  Dołączone do tych zmian jest możliwość używania nowego konta magazynu, oprócz klasycznego magazynu do przechowywania artefaktów podczas wdrażania.  

Jeśli używasz projektu grupy zasobów platformy Azure z poprzedniej wersji zestawu SDK z 2.7 zestawu SDK, nowy skrypt wdrożenia jest potrzebny do wdrożenia przy użyciu nowego konta magazynu, zamiast klasycznego magazynu.  Zostanie wyświetlony monit, aby zmiany zostały wprowadzone do projektu, aby dodać nowy skrypt.  Starego skryptu zostanie zmieniona i trzeba będzie ręcznie wprowadź wszelkie zmiany, aby nowy skrypt.

### <a name="storage-explorer-tools"></a>Narzędzia Eksploratora magazynu
* Obsługa wyświetlania Dołącz obiektów blob. Aby dowiedzieć się więcej w [ten wpis w blogu](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/13/introducing-azure-storage-append-blob.aspx). 
* Obsługa wyświetlania kont Premium magazynu za pomocą Eksploratora serwera. Eksploratora serwera będą wyświetlane tylko stronicowych obiektów blob dla kont premium magazynu, ponieważ są one jedynym obsługiwanym typem dla kont premium magazynu.

### <a name="azure-data-factory-tools-for-visual-studio"></a>Narzędzia fabryki danych Azure dla programu Visual Studio
Wprowadzenie do **narzędzia fabryki danych Azure** dla programu Visual Studio. Poniżej są włączone funkcje. Zobacz [ten blog](http://go.microsoft.com/fwlink/?LinkId=617530) Aby uzyskać więcej informacji.

* **Szablon na podstawie tworzenia**: wybierz opcję Użyj — z uwzględnieniem wielkości liter na podstawie szablonów, szablony przepływu danych i przetwarzania danych szablonów do wdrożenia rozwiązania integracji danych na całej trasie, a następnie rozpocznij praktyczne szybko z fabryką danych. 
* **Integracja z Eksploratora rozwiązań do tworzenia i wdrażania jednostek fabryki danych**: tworzenie i wdrażanie potoki i powiązanych jednostek jako projektów programu Visual Studio. 
* **Integracja z widoku diagramu do visual interakcji, podczas tworzenia**: wizualnie tworzyć potoki i zestawów danych o pomoc w widoku diagramu. 
* **Integracja z Eksploratora serwera do przeglądania i interakcji z obiektami już wdrożonych**: korzystać z Eksploratora serwera do fabryki danych przeglądania już wdrożony i odpowiednich jednostek. Zaimportuj fabryki danych wdrożonych lub dowolnej jednostki (potoku, połączonej usługi, zbiory danych) do projektu. 
* **Edytowanie JSON za pomocą intellisense schemat sprawdzania poprawności i rozbudowane**: efektywnego konfigurowania i edycji dokumentów JSON jednostek fabryki danych z zaawansowanych funkcji intellisense i schemat sprawdzania poprawności 
* **Publikowanie środowisko**: publikowanie utworzone potoki deweloperów, testowego lub środowisko produkcyjną przez utworzenie oddzielnych config plików dla każdego środowiska.
* **Obsługa przetwarzania danych na podstawie pig, Hive i .net**: Obsługa Pig i Hive skryptów w projekcie fabryki danych. Obsługa odwołujące się do projektu C# .net zarządzania działania.

## <a name="azure-sdk-for-net-271"></a>Zestaw Azure SDK dla programu .NET 2.7.1
Poniższa sekcja zawiera aktualizacje, które zostały wprowadzone przy użyciu zestawu Azure SDK dla wersji .NET 2.7.1.

### <a name="hdinsight-tools"></a>Narzędzia HDInsight Tools
Aby uzyskać szczegółowe informacje na temat aktualizacji narzędzi HDInsight, zobacz [ten blog](http://go.microsoft.com/fwlink/?LinkId=623831).

* Widok operatorów hive zadania (Nowa funkcja)
  
    Aby lepiej zrozumieć zapytanie Hive lepsze, funkcja Widok operatorów Hive został dodany. Aby zobaczyć wszystkie operatory wewnątrz wierzchołka, kliknij dwukrotnie wierzchołki wykresu zadania. Aby wyświetlić szczegółowe informacje o konkretnym operatorze, umieść kursor nad tego operatora.
* Znacznik błąd gałąź (Nowa funkcja)
  
    Umożliwia wyświetlanie błędy gramatyczne natychmiast, funkcja Hive znacznika błąd został dodany. Ponadto zostały rozszerzone komunikaty o błędach i będą teraz widoczne błędy gramatyczne szczegółowe natychmiast (do tej wersji, trzeba było przesłać skrypt Hive do klastra, a następnie zaczekaj przez pewien czas, przed pobraniem szczegółowe informacje o Twojej komunikat o błędzie).  
* Wykres topologii STORM (Nowa funkcja)
  
    Wizualizacja jest bardzo ważne, jeśli chcesz zobaczyć, czy ta topologia działa zgodnie z oczekiwaniami. W tej wersji dodano wizualizacji wykresu Storm. Można zwizualizować metryki istotne dla topologii (na przykład kolor oznacza pogody niektórych Bolt "zajęty" lub nie). Możesz również dwukrotnie kliknąć elementy Bolt/Spout, aby wyświetlić więcej szczegółów.
* Obsługa klastrów usługi HDInsight, które zostały utworzone w portalu Azure (poprawkę)
  
    Visual Studio umożliwia teraz wyświetlanie i przesyłanie zadań do wszystkich klastrów HDInsight niezależnie od tego, gdzie zostały utworzone klastra.
* Więcej obsługę funkcji IntelliSense & metadanych Hive ładowania szybciej (poprawa)
  
    Firma Microsoft ulepszyła funkcji IntelliSense, dodając więcej wskazówek przyjazny dla użytkownika. Na przykład alias tabeli można teraz również sugerowane w IntelliSense, łatwiej można napisać zapytanie. Ponadto firma Microsoft ulepszyła metadanych Hive ładowania, dlatego potrwa tylko kilka sekund, aby wyświetlić listę wszystkich baz danych, tabel i kolumn Twoje potrzeby magazynu metadanych Hive.

Aby uzyskać szczegółowe informacje na temat aktualizacji narzędzi HDInsight, zobacz [ten blog](http://go.microsoft.com/fwlink/?LinkId=623831).

### <a name="improvements-in-visual-studio-2013"></a>Ulepszenia w programie Visual Studio 2013
* Zestaw Azure SDK 2.7.1 umożliwia programu Visual Studio 2013 dostęp do konta platformy Azure i subskrypcji za pomocą kontroli dostępu opartej na rolach, dostawców rozwiązań w chmurze i Dreamspark.
* Przy użyciu zestawu Azure SDK 2.7.1 nowego okna narzędzia Eksplorator chmury jest obecnie również dostępna w programie Visual Studio 2013.

### <a name="known-issues"></a>Znane problemy
Instalowanie zestawu SDK platformy Azure w wersji 2.6 lub 2.7.1 dla programu Visual Studio Community 2013 na nie - angielskiej wersji językowej systemu operacyjnego wyświetli ostrzeżenie w języku angielskim i innej niż angielska zasobów programu Visual Studio może pasować. To ostrzeżenie może zostać bezpiecznie odwołany. Tylko wystąpi, jeśli komputer nie zawierał wcześniejszego zainstalowania programu Visual Studio Community 2013 i zestawu SDK jest instalowany na nie - angielskiej wersji językowej systemu operacyjnego. Ostrzeżenie jest wyświetlane po pakiet językowy dotyczy zasobów RTM programu Visual Studio, ale przed zastosowaniem aktualizacji w wersji 4. Odrzucanie ostrzeżenia umożliwi pakiet językowy, aby kontynuować i ukończyć stosowania wersji aktualizacji 4 zawartości pakietu języka.

LightSwitch — projekty nie są compatibile w tej wersji. Ten problem zostanie rozwiązany w następnej wersji zestawu SDK.

## <a name="also-see"></a>Zobacz też
[Zestaw Azure SDK 2.7.1 post anonsu](http://go.microsoft.com/fwlink/?LinkId=623850)

[Azure post anonsu 2.7 zestawu SDK](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/)

[Obsługa i wycofania informacji dla zestawu Azure SDK dla platformy .NET i interfejsów API](https://msdn.microsoft.com/library/azure/dn479282.aspx/)

