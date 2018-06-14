---
title: Informacje o wersji zestawu Azure SDK dla platformy .NET w wersji 2.6
description: Informacje o wersji zestawu Azure SDK dla platformy .NET w wersji 2.6
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
ms.openlocfilehash: 21817b09440fc98a54dc45c9129d104b01fa387d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23836462"
---
# <a name="azure-sdk-for-net-26-release-notes"></a>Informacje o wersji zestawu Azure SDK dla platformy .NET w wersji 2.6
Ten dokument zawiera informacje o wersji zestawu Azure SDK w wersji .NET 2.6. 

2.6 zestawu SDK platformy Azure mogą programować aplikacje usługi chmury (PaaS) przeznaczonych dla platformy .NET w wersji 4.5.2 i .NET 4.6 pod warunkiem, że ręcznie zainstalować docelowej platformy .NET Framework w roli usługi chmury. Zobacz [zainstalować program .NET dla roli usługi chmury](http://go.microsoft.com/fwlink/?LinkID=309796).

## <a name="service-bus-updates"></a>Aktualizacje usługi Service Bus
* Centra zdarzeń: 
  
  * Kontrola dostępu docelowej umożliwia teraz podczas wysyłania zdarzeń w przypadku wystawianego dodatkowej wydawcę punktu końcowego usługi Event hubs.
  * Dodatkowe stabilności i usprawnienia dodane do funkcji usługi Event Hubs.
  * Dodawanie obsługi protokołu Amqp za pośrednictwem protokołu WebSocket do obsługi wiadomości i usługi Event Hubs.

## <a name="hdinsight-tools-for-visual-studio-updates"></a>Narzędzia HDInsight Tools for Visual Studio aktualizacji
* **Rozszerzenie IntelliSense**: sugestię zdalnych metadanych
  
    Narzędzia HDInsight Tools for Visual Studio obsługuje obecnie pobierania zdalnych metadanych podczas edycji skryptu Hive. Na przykład możesz wpisać **wybierz * FROM** i zostaną wyświetlone wszystkie nazwy tabeli. Ponadto po określeniu tabeli będą wyświetlane nazwy kolumn.
* **Obsługa emulatora usługi HDInsight**
  
    Obecnie narzędzia HDInsight Tools for Visual Studio obsługują połączenia emulatorze usługi HDInsight, więc skrypty programu Hive można opracować lokalnie, bez jakichkolwiek kosztów, wykonujących te skrypty przed klastrów usługi HDInsight. 
  
    Aby uzyskać więcej informacji, zapoznaj się [tego podręcznika](http://go.microsoft.com/fwlink/?LinkID=529540&clcid=0x409).
* **Narzędzia HDInsight Tools for Visual Studio obsługują ogólnego klastrów platformy Hadoop** (wersja zapoznawcza)
  
    Narzędzia HDInsight Tools for Visual Studio obsługują teraz ogólnego klastrów platformy Hadoop, dzięki czemu można użyć narzędzia HDInsight Tools for Visual Studio wykonywać następujące czynności:
  
  * połączyć się z klastrem 
  * Pisanie zapytań Hive z rozszerzoną obsługę funkcji IntelliSense — autouzupełniania, 
  * Wyświetl wszystkie zadania w klastra za pomocą intuicyjnego interfejsu użytkownika. 
    
    Aby uzyskać więcej informacji, zapoznaj się [tego podręcznika](http://go.microsoft.com/fwlink/?LinkID=529540&clcid=0x409).

## <a name="in-role-cache-updates"></a>Operacji aktualizacji pamięci podręcznej w roli
* **Pamięć podręczna w roli** zostało zaktualizowane do używania **zestawu SDK usługi Magazyn Microsoft Azure** wersji 4.3. Do tej pory **pamięci podręcznej w roli** został przy użyciu zestawu SDK usługi Magazyn Azure w wersji 1.7.
  
    Klientów przy użyciu 2.5 zestawu SDK platformy Azure lub poniżej należy zaktualizować do zestawu SDK platformy Azure w wersji 2.6 i przejść do nowej wersji zestawu SDK usługi Magazyn Azure. 
  
    W tej chwili magazynu Azure w wersji 2011-08-18 jest zaplanowane do usunięcia 1 sierpnia 2016 r. Wszelkie migracji pamięci podręcznej w roli z 2.5 zestawu SDK platformy Azure lub niższy do 2.6 musi być ukończenia tego czasu. Aby uzyskać więcej informacji na wycofanie wersji 2011-08-18 usługi Magazyn Azure, zobacz [aktualizację usuwania wersji magazynu usługi Microsoft Azure: rozszerzenie 2016](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx).

> [!IMPORTANT]
> Firma Microsoft jest announcing wycofanie 30 listopada 2016 dla usługi zarządzana pamięć podręczna Azure i pamięci podręcznej na roli Azure. Firma Microsoft zaleca migracji z pamięcią podręczną Redis Azure w ramach przygotowań do tego wycofania. Aby uzyskać więcej informacji na daty i wskazówki dotyczące migracji, zobacz [oferty które pamięć podręczna Azure jest dla mnie odpowiednia?](../redis-cache/cache-faq.md#which-azure-cache-offering-is-right-for-me)
> 
> 

## <a name="azure-app-service-tools"></a>Narzędzia usługi aplikacji Azure
Następujące elementy zostały zaktualizowane w wersji 2.6 zestawu SDK platformy Azure.

* Publikowanie Azure rozszerzone i obejmują aplikacje interfejsu API Azure jako cel wdrożenia.
* Aplikacje interfejsu API funkcji udostępniania umożliwia użytkownikom z funkcją tworzenia i inicjowania obsługi administracyjnej dla aplikacji interfejsu API.
* W Eksploratorze serwera zmienione w celu uwzględnienia nowego węzła usługi aplikacji, przy użyciu aplikacji interfejsu API, mobilne i sieci Web pogrupowany według grupy zasobów.
* Dodaj klienta aplikacji interfejsu API Azure gestu dodane do większości projektów C# umożliwiające automatyczne generowanie włączone Swagger interfejsu API aplikacji działających w subskrypcji platformy Azure przez użytkownika.
* Aplikacje interfejsu API narzędzi i węzły usługi App Service w Eksploratorze serwera są dostępne w programie Visual Studio 2013 tylko. 

## <a name="azure-resource-manager-tools-updates"></a>Aktualizacje narzędzia Menedżera zasobów Azure
Narzędzia Menedżera zasobów Azure zostały zaktualizowane do zawierają szablony maszyn wirtualnych, sieci i magazynu. Aby dołączyć nowy widok konspektu szablonów i możliwość edytowania szablonów przy użyciu fragmentów JSON została zaktualizowana JSON edytowania. Szablony wdrożone w programie Visual Studio za pomocą skryptu programu PowerShell dostarczane z projektu, więc zmiany wprowadzone do skryptu, które będą używane przez program Visual Studio.

## <a name="diagnostics-improvements-for-cloud-services"></a>Ulepszenia diagnostyki dla usług w chmurze
2.6 zestawu SDK platformy Azure wstecz wprowadzono obsługę zbierania dzienników diagnostycznych w emulatorze obliczeń platformy Azure i przekazywanie ich do tworzenia magazynu. Rejestruje wszystkie diagnostyki (w tym aplikacji śledzenia dzienników, śledzenie dzienniki systemu Windows (ETW), liczników wydajności, infrastruktury dzienniki zdarzeń dzienników i windows zdarzeń) generowane gdy aplikacja działa w emulatorze mogą zostać przeniesione do rozwoju magazynu Aby sprawdzić, czy Twoje rejestrowania diagnostyki działa na komputerze lokalnym. 

Teraz można określić konto magazynu diagnostyki w pliku konfiguracji (cscfg) usługi, ułatwiając użyć konta magazynu diagnostyki różne dla różnych środowisk. Brak niektórych istotnych różnic między jak parametry połączenia działał 2.4 zestawu SDK platformy Azure i 2.6 zestawu SDK platformy Azure. Aby uzyskać więcej informacji na temat użycia parametrów połączenia magazynu diagnostyki i ich wpływ projektów zobacz [Konfigurowanie diagnostyki dla usług w chmurze Azure](http://go.microsoft.com/fwlink/?LinkID=532784).

## <a name="breaking-changes"></a>Fundamentalne zmiany
### <a name="azure-resource-manager-tools"></a>Narzędzia Menedżera zasobów Azure
* **Projekty wdrażania chmury** typ projektu jest dostępny w 2.5 zestawu SDK platformy Azure została zmieniona na **grupy zasobów platformy Azure**.
* **Projekty wdrażania w chmurze** typu projektów utworzonych w 2.5 zestawu SDK platformy Azure mogą być używane w wersji 2.6, ale wdrażania szablonu z programu Visual Studio zakończy się niepowodzeniem. Jednak wdrażanie przy użyciu skryptu programu PowerShell będą nadal działać tak jak poprzednio.  Aby uzyskać informacje dotyczące sposobu używania **projekty wdrażania chmury** w wersji 2.6 przeczytać ten tekst [post](http://go.microsoft.com/fwlink/?LinkID=534086).

## <a name="known-issues"></a>Znane problemy
* Zbierania dzienników diagnostycznych w emulatorze wymaga 64-bitowym systemie operacyjnym. Dzienniki diagnostyczne nie będą zbierane podczas uruchamiania na 32-bitowym systemie operacyjnym. Nie ma to wpływu na innych funkcji emulatora. 
* Azure 2.6 SDK wydanej w dniu 2015-4-29 ma dwa problemy: 
  
  * Nie można załadować aplikacji uniwersalnej w programie Visual Studio 2015, gdy 2.6 zestawu SDK platformy Azure został zainstalowany na komputerze.
  * Debugowanie projektu usługi w chmurze nie powiedzie się w programie Visual Studio 2013 i Visual Studio 2015, gdy przestanie odpowiadać Visual Studio i ulega awarii podczas wyświetlania okna dialogowego z komunikatem "Konfigurowanie diagnostyki dla emulatora".
    
    Aktualizacja Azure SDK w wersji 2.6 został wydany 2015-5-18. Zaktualizowana wersja jest 2.6.30508.1601; go rozwiązuje dwa problemy opisane powyżej. Można zidentyfikować kompilacji zestawu SDK z poziomu Panelu sterowania -> programy i funkcje -> Microsoft Azure Tools dla programu Microsoft Visual Studio 2013 – v 2.6. W kolumnie wersji zostaną wyświetlone numer kompilacji.
    
    Jeśli powyższe problemy nadal jest skierowany, zainstaluj najnowszą wersję zestawu SDK platformy Azure 2.6 dla [VS 2012](http://go.microsoft.com/fwlink/p/?linkid=323511&clcid=0x409), [VS 2013](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409) lub [VS 2015](http://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409).

## <a name="see-also"></a>Zobacz też
[Obsługa i wycofania informacji dla zestawu Azure SDK dla platformy .NET i interfejsów API](https://msdn.microsoft.com/library/azure/dn479282.aspx/)

