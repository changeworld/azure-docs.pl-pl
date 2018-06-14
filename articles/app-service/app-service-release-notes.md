---
title: Informacje o wersji zestawu Azure SDK dla platformy .NET 2.5.1
description: Informacje o wersji zestawu Azure SDK dla platformy .NET 2.5.1
services: app-service
documentationcenter: .net,nodejs,java
author: Juliako
manager: erikre
editor: ''
ms.assetid: 8d3d815f-bb58-447e-8ff0-f9b9603c7b00
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/10/2016
ms.author: juliako
ms.openlocfilehash: 0a422b02623a18ac6a1eef460bbada681672e69f
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2017
ms.locfileid: "23957419"
---
# <a name="azure-sdk-for-net-251-release-notes"></a>Informacje o wersji zestawu Azure SDK dla platformy .NET 2.5.1
Ten dokument zawiera informacje o wersji zestawu Azure SDK dla wersji .NET 2.5.1. 

## <a name="azure-sdk-for-net-251-release-notes"></a>Informacje o wersji zestawu Azure SDK dla platformy .NET 2.5.1
Poniżej przedstawiono nowe funkcje i aktualizacje w zestawie Azure SDK dla platformy .NET 2.5.1.

* Nowe features\scenarios związane z **rozszerzeń narzędzi sieci Web**. 
  
  * Witryn sieci Web Azure została zmieniona w usłudze Azure App Service. 
  * Dodano obsługę aplikacji interfejsu API (wersja zapoznawcza) platformy Azure, tak aby klienci publikowanie projektów programu ASP.NET, jak aplikacje interfejsu API, a następnie użyć dodawania > projektów gestu klienta aplikacji interfejsu API platformy Azure w języku C#, aby wygenerować kod na podstawie struktury wdrożonej aplikacji interfejsu API. 
  * Zamiast tego węzła usługi aplikacji Azure, zawiera obsługę zasobów na podstawie grupy grupowanie Azure API Apps, Mobile Apps i aplikacje sieci Web została zastąpiona węzła witryn sieci Web w Eksploratorze serwera.
  * Pomoc techniczna platformy Azure Mobile Apps (wersja zapoznawcza) został dodany, aby klienci mogą tworzyć nowe projekty Mobile Apps, Dodaj kontrolery Mobile Apps, publikowanie projektów i zdalne debugowanie aplikacji.
  * Dodaj > gestu klienta aplikacji interfejsu API Azure obsługuje teraz lokalne pliki JSON programu Swagger, więc interfejsu API sieci Web deweloperzy mogą używać NuGets innych firm, takich jak pakiet Swashbuckle do generowania Swagger lub tworzyć go ręcznie. Dzięki temu klient deweloperzy mogą używać funkcji generowania kodu do pracy z dowolnego punktu końcowego struktury Swagger w projektów C#. 
  * Aplikacja sieci Web i aplikacji interfejsu API publikowania okien dialogowych zostały rozszerzone do obsługi portalu Azure pojęcie grupowanie zasobów i wyboru/tworzenia grup zasobów platformy Azure i plany usługi App Service są reprezentowane w nowej aplikacji sieci Web i aplikacji interfejsu API dialogowym inicjowania obsługi administracyjnej. 
  * Węzły w Eksploratorze serwera aplikacji interfejsu API platformy Azure zawierają łącza do link bezpośredni do aplikacji interfejsu API w portalu Azure, a także inne funkcje, takie jak dzienników przesyłania strumieniowego i debugowanie zdalne.
    
    Znane problemy i bieżących ograniczeń w .NET SDK usługi Azure 2.5.1 [to](app-service-release-notes.md#known_issues_2_5_1) poniższej sekcji.
* Nowe features\scenarios związane z **narzędzi HDInsight Tools** w programie Visual Studio są włączone w tej wersji. 
  
  * Lokalna Weryfikacja skryptów hive. Kliknij przycisk Sprawdź poprawność skryptu na pasku narzędzi, aby zobaczyć, czy występują błędy skryptu. 
  * Ulepszone debugowanie zadań Hive. Można teraz debugowania zadań Hive, uzyskując dostęp do dzienników Yarn w programie Visual Studio. Jeśli aplikacja ma problemy z wydajnością, analizowanie dzienników YARN dostarczające przydatnych informacji...
  * (W publicznej wersji zapoznawczej) IntelliSense i autouzupełniania — słowo kluczowe obsługę Hive. Aby ułatwić tworzenie skryptów Hive, narzędzia HDInsight Tools for Visual Studio dodane, IntelliSense i autouzupełniania — słowo kluczowe obsługę Hive.
  * Obsługa platformy STORM. Narzędzia HDInsight Tools for Visual Studio umożliwia teraz tworzenie Storm topologies/elementach Spout/elementów Bolt w języku C#. Można przesłać rozwinięte topologii do klastra Storm i wyświetlić stan spout/bolt/topologii. Dzienniki systemu i dzienniki klienta umożliwia rozwiązywanie problemów z Storm topologies/elementów Bolt/elementach Spout. Umożliwia także istniejące zasoby JAVA w Storm w usłudze HDInsight.
    
    Aby uzyskać więcej informacji, zobacz [rozpocząć korzystanie z narzędzi Hadoop w HDInsight dla programu Visual Studio](../hdinsight/hadoop/apache-hadoop-visual-studio-tools-get-started.md).

## <a id="known_issues_2_5_1"></a>Zestaw Azure SDK dla platformy .NET 2.5.1 znane problemy i ograniczenia
* Aplikacje interfejsu API platformy Azure jest widoczny jako cel wdrożenia dla aplikacji mobilnych. Aplikacje sieci Web powinna być tylko lokalizacja docelowa Mobile Apps do późniejszych wersji. 
* Inicjowanie obsługi aplikacji interfejsu API platformy Azure może spowodować Powodzenie, ale niekiedy nie można zaktualizować postęp w oknie działanie usługi Azure App Service. Obejście jest sprawdzać stan nowej aplikacji interfejsu API platformy Azure w portalu Azure. 
* Plik > Nowy Projekt > Aplikacja interfejsu API > środowisko F5 powoduje błąd HTTP, ponieważ nie istnieje żadne default/index.html. Obejście tego problemu jest ręcznie przejdź do adresu URL /api/values. 
* Sporadycznie ikony Eksploratora serwera są wyświetlane spłaszczony. Ponowne uruchomienie programu VS rozwiązuje ten problem. 
* Jeśli w aplikacji sieci Web lub aplikacji interfejsu API inicjowania obsługi administracyjnej (np. Przekroczono limit przydziału błędy lub zduplikowaną nazwę bramy aplikacji interfejsu API Azure), jest zgłaszany wyjątek, błędy Pokaż nieprzetworzone tekst JSON. 
* Problemy sporadyczne tworzenia projektu, gdy zaznaczone jest pole w czasie tworzenia projektu usługi Application Insights.
* Czasami wygenerowanego kodu klienta aplikacji interfejsu API Azure brakuje przestrzeni nazw, potrzebują dołączanych ręcznie (lub automatycznie importowane przy użyciu programu Visual Studio wskaźników) kodu do kompilacji. 
* Projekty aplikacji mobilnej powinien zostać opublikowany, do aplikacji sieci web, ale należy wybrać utworzony jako aplikacja mobilna, w portalu Azure, ponieważ projekt aplikacji mobilnej wymaga bazy danych lokacji. 
* Termin "usługi mobilnej" zamiast "aplikacje mobilne" używa strony początkowej dla aplikacji mobilnych 
* Tworzenie projektu aplikacji mobilnej może potrwać kilka minut do utworzenia. 
* W aplikacji interfejsu API inicjowania obsługi administracyjnej (w niektórych przypadkach) wystąpił błąd wróci z interfejsu API Azure odbicia, że uprawnienia nie można ustawić poprawnie, natomiast w aplikacji interfejsu API został prawidłowo zainicjowany i jest gotowy do użycia. Możesz ręcznie ustawić uprawnienia za pomocą portalu Azure.
* Usługa Application Insights nie jest obsługiwana na szablony aplikacji interfejsu API i aplikacji mobilnej.
* Projekty aplikacji interfejsu API nie można używać w połączeniu z projektami usługi w chmurze.
* Szablony projektu aplikacji interfejsu API są dostępne tylko w języku C#.
* Użycie aplikacji interfejsu API za pomocą menu kontekstowego "Dodawanie Azure klienta interfejsu API aplikacji" jest obsługiwana tylko w języku C#.

