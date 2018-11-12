---
title: Informacje o wersji zestawu Azure SDK dla platformy .NET 2.6
description: Informacje o wersji zestawu Azure SDK dla platformy .NET 2.6
services: app-service/web
documentationcenter: .net
author: chrissfanos
editor: ''
ms.assetid: b45853d5-a2b8-4962-a22d-579cb36ae14c
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 02/24/2017
ms.author: juliako
ms.openlocfilehash: 040eea1efd3abdbbfa65c24b2950ebece9d4acbc
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51258395"
---
# <a name="azure-sdk-for-net-26-release-notes"></a>Informacje o wersji zestawu Azure SDK dla platformy .NET 2.6
Ten dokument zawiera informacje o wersji zestawu Azure SDK dla platformy .NET 2.6 wersji. 

2.6 zestawu SDK platformy Azure można tworzyć usługi aplikacji w chmurze (PaaS) przeznaczone dla platformy .NET 4.5.2 i .NET 4.6, pod warunkiem, że ręcznie zainstalować docelową aplikację .NET Framework w roli usługi w chmurze. Zobacz [zainstalować program .NET w roli usługi w chmurze](https://go.microsoft.com/fwlink/?LinkID=309796).

## <a name="service-bus-updates"></a>Aktualizacje usługi Service Bus
* Usługa Event Hubs: 
  
  * Kontrola dostępu do docelowych umożliwia teraz podczas wysyłania zdarzeń, zapewniając dodatkowe wydawcy punktu końcowego dla usługi Event Hubs.
  * Dodatkowe stabilności i usprawnienia dodane do funkcji usługi Event Hubs.
  * Dodanie obsługi protokołu Amqp przez WebSocket, do obsługi wiadomości i centrów zdarzeń.

## <a name="hdinsight-tools-for-visual-studio-updates"></a>Aktualizacje narzędzi HDInsight Tools for Visual Studio
* **Rozszerzenie IntelliSense**: sugestii zdalnych metadanych
  
    Narzędzia HDInsight Tools for Visual Studio obsługuje teraz pobierania zdalnych metadanych podczas edycji skryptu Hive. Na przykład można wpisać **wybierz * FROM** i będą wyświetlane wszystkie nazwy tabeli. Ponadto nazwy kolumn będą wyświetlane po określeniu tabeli.
* **Obsługa emulatora usługi HDInsight**
  
    Narzędzia HDInsight Tools for Visual Studio obsługują obecnie trwa łączenie z emulatorem HDInsight, dzięki czemu można tworzysz skrypty Hive lokalnie bez wprowadzania żadnych kosztów, wykonaj te skrypty przed klastry usługi HDInsight. 
  
    Aby uzyskać więcej informacji, zobacz [tego podręcznika](https://go.microsoft.com/fwlink/?LinkID=529540&clcid=0x409).
* **Narzędzia HDInsight Tools for Visual Studio obsługuje ogólny klastrów platformy Hadoop** (wersja zapoznawcza)
  
    Narzędzia HDInsight Tools for Visual Studio obsługuje teraz ogólnego klastrów Hadoop, aby można było używać narzędzi HDInsight Tools for Visual Studio, wykonaj następujące czynności:
  
  * Łączenie z klastrem 
  * Napisz zapytanie Hive o rozszerzoną obsługę funkcji IntelliSense/automatycznego uzupełniania, 
  * Wyświetl wszystkie zadania w klastrze za pomocą intuicyjnego interfejsu użytkownika. 
    
    Aby uzyskać więcej informacji, zobacz [tego podręcznika](https://go.microsoft.com/fwlink/?LinkID=529540&clcid=0x409).

## <a name="in-role-cache-updates"></a>Aktualizacje w roli pamięci podręcznej
* **Pamięć podręczna oparta na roli** został zaktualizowany do użycia **zestawu SDK usługi Microsoft Azure Storage** wersji 4.3. Do tej pory **pamięć podręczna oparta na roli** został przy użyciu zestawu SDK usługi Azure Storage w wersji 1.7.
  
    Klientów przy użyciu zestawu SDK Azure 2.5 lub poniżej należy zaktualizować do wersji 2.6 zestawu SDK platformy Azure i przejść do nowej wersji zestawu SDK usługi Azure Storage. 
  
    W tej chwili usługi Azure Storage w wersji 2011-08-18 jest zaplanowane do usunięcia 1 sierpnia 2016 r. Wszystkie migracje podręczna oparta na roli Azure SDK 2.5 lub poniżej do 2.6 musi być kompletny tego czasu. Aby uzyskać więcej informacji na temat wycofania usługi Azure Storage w wersji 2011-08-18, zobacz [Microsoft Azure Storage usługi wersji usuwania Update: rozszerzenie 2016](https://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx).

> [!IMPORTANT]
> Z przyjemnością ogłaszamy wycofanie 30 listopada 2016 r. dla usługi Azure Managed Cache Service i pamięć podręczna oparta na roli platformy Azure. Firma Microsoft zaleca przeprowadzenie migracji do usługi Azure Redis Cache w ramach przygotowania do wycofania tego. Aby uzyskać więcej informacji na temat dat i wskazówki dotyczące migracji, zobacz [oferty którego pamięć podręczna systemu Azure jest dla mnie odpowiednia?](../redis-cache/cache-faq.md#which-azure-cache-offering-is-right-for-me)
> 
> 

## <a name="azure-app-service-tools"></a>Narzędzia usługi Azure App Service
Następujące elementy zostały zaktualizowane w wersji 2.6 zestawu SDK platformy Azure.

* Rozszerzone i obejmują usługi Azure API Apps jako cel wdrożenia publikowania platformy Azure.
* Aplikacje interfejsu API aprowizacji funkcji aby umożliwić użytkownikom przy użyciu funkcji tworzenia i udostępniania aplikacji interfejsu API.
* Eksplorator serwera zmienione w celu odzwierciedlenia nowego węzła usługi App Service, za pomocą aplikacji sieci Web, mobilnych i interfejsu API, pogrupowane według grupy zasobów.
* Dodaj gest klienta aplikacji interfejsu API platformy Azure dodane do większości C# projektów, które spowoduje włączenie automatycznego generowania aplikacji interfejsu API obsługującego strukturę Swagger działających w subskrypcji platformy Azure przez użytkownika.
* Aplikacje interfejsu API narzędzia i usługi App Service węzły w Eksploratorze serwera są dostępne w programie Visual Studio 2013 tylko. 

## <a name="azure-resource-manager-tools-updates"></a>Aktualizacje narzędzia usługi Azure Resource Manager
Narzędzia usługi Azure resource manager zostały zaktualizowane do obejmują szablony maszyn wirtualnych, sieci i magazynu. JSON środowisko edytowania plików został zaktualizowany tak, aby uwzględnić nowy widok konspektu, szablonów i możliwość edytowania szablonów przy użyciu fragmentów kodu JSON. Szablony wdrażanych za pomocą programu Visual Studio Użyj skryptu programu PowerShell dostarczane z projektem, więc wszelkie zmiany wprowadzone do skryptu, które będą używane przez program Visual Studio.

## <a name="diagnostics-improvements-for-cloud-services"></a>Ulepszenia diagnostyki w usługach Cloud Services
Azure SDK 2.6 powoduje zwrócenie obsługę zbierania dzienników diagnostycznych w emulatorze obliczeń platformy Azure i przekazywanie ich do tworzenia magazynu. Dzienniki wszelkie diagnostyczne (w tym śledzenie aplikacji dzienniki, zdarzenia śledzenia dla dzienników Windows (ETW), liczniki wydajności i infrastruktury dzienniki zdarzeń Dzienniki i systemu windows) generowany gdy aplikacja jest uruchomiona w emulatorze mogą zostać przeniesione do magazynem projektowym Aby sprawdzić, czy usługi rejestrowania diagnostycznego działa na komputerze lokalnym. 

Teraz można określić konto magazynu diagnostyki w pliku konfiguracji (cscfg) usługi, ułatwiając korzystanie z kont magazynu diagnostyki różne dla różnych środowisk. Istnieją pewne istotne różnice między w Azure SDK 2.4 i 2.6 zestawu SDK platformy Azure, jak działał parametry połączenia. Aby uzyskać więcej informacji na temat sposobu używania parametrów połączenia magazynu diagnostyki i jak wpływa na swoje projekty zobacz [Konfigurowanie diagnostyki Azure Cloud Services](https://go.microsoft.com/fwlink/?LinkID=532784).

## <a name="breaking-changes"></a>Zmiany powodujące niezgodność
### <a name="azure-resource-manager-tools"></a>Narzędzia usługi Azure Resource Manager
* **Projektów wdrożeń w chmurze** dostępne w Azure SDK 2.5 dla typu projektu została zmieniona na **grupy zasobów platformy Azure**.
* **Projekty wdrażania w chmurze** typu projektów utworzonych w usłudze Azure 2.5 SDK mogą być używane w wersji 2.6, ale wdrażania szablonu z programu Visual Studio zakończy się niepowodzeniem. Jednak wdrażanie przy użyciu skryptu programu PowerShell nadal będzie działać tak jak wcześniej.  Aby uzyskać informacje dotyczące sposobu używania **projektów wdrożeń w chmurze** w wersji 2.6 przeczytać ten tekst [wpis](https://go.microsoft.com/fwlink/?LinkID=534086).

## <a name="known-issues"></a>Znane problemy
* Zbieranie dzienników diagnostyki w emulatorze wymaga 64-bitowym systemie operacyjnym. Dzienniki diagnostyczne nie będą zbierane podczas uruchamiania na 32-bitowym systemie operacyjnym. Nie wpływa to na inne funkcje emulatora. 
* Azure SDK 2.6 wydanej w dniu 2015-4-29 ma dwa problemy: 
  
  * Nie można załadować aplikacji uniwersalnych w programie Visual Studio 2015, Azure SDK 2.6 został zainstalowany na maszynie.
  * Debugowanie projektu usługi w chmurze zakończy się niepowodzeniem w programie Visual Studio 2013 i Visual Studio 2015, gdzie program Visual Studio przestaje odpowiadać i ulega awarii podczas wyświetlania okna dialogowego z komunikatem "Konfigurowanie diagnostyki dla emulatora".
    
    Aktualizacja Azure SDK 2.6 został wydany w 2015-5-18. Zaktualizowana wersja jest 2.6.30508.1601; zawiera ona poprawki dotyczące problemów dwóch opisanych powyżej. Można zidentyfikować kompilacji zestawu SDK z poziomu Panelu sterowania -> programy i funkcje -> Microsoft Azure Tools dla programu Microsoft Visual Studio 2013 — v 2.6. W kolumnie wersja wyświetli numer kompilacji.
    
    Jeśli nadal występują powyższe problemy, zainstaluj najnowszą wersję zestawu Azure SDK 2.6 dla [VS 2012](https://go.microsoft.com/fwlink/p/?linkid=323511&clcid=0x409), [VS 2013](https://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409) lub [programu VS 2015](https://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409).

## <a name="see-also"></a>Zobacz też
[Pomoc techniczna i informacje o wycofaniu dla zestawu Azure SDK dla platformy .NET i interfejsów API](https://msdn.microsoft.com/library/azure/dn479282.aspx/)

