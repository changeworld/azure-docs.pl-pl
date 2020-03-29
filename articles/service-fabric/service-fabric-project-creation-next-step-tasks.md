---
title: Tworzenie projektu sieci szkieletowej usług kolejne kroki
description: Dowiedz się więcej o projekcie aplikacji utworzonym właśnie w programie Visual Studio.  Dowiedz się, jak tworzyć usługi przy użyciu samouczków i dowiedz się więcej o tworzeniu usług dla sieci szkieletowej usług.
ms.topic: conceptual
ms.date: 12/07/2017
ms.openlocfilehash: e8fc105657bda6114851f4819be4658926ad621c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75349413"
---
# <a name="your-service-fabric-application-and-next-steps"></a>Aplikacja sieci szkieletowej usług i kolejne kroki
Twoja aplikacja usługi Azure Service Fabric została utworzona. W tym artykule opisano niektóre samouczki do wypróbowania, makijaż projektu, więcej informacji, które mogą Cię zainteresować, i potencjalne następne kroki.

## <a name="get-started-with-tutorials-walk-throughs-and-samples"></a>Wprowadzenie do samouczków, przewodników i przykładów
Chcesz zacząć?  

Przejmij pracę za pomocą samouczka aplikacji .NET. Dowiedz się, jak [utworzyć aplikację](service-fabric-tutorial-create-dotnet-app.md) z front-endem ASP.NET Core i stanowym zapleczem, [wdrożyć aplikację](service-fabric-tutorial-deploy-app-to-party-cluster.md) w klastrze, [skonfigurować ciągłą integrację/dysk CD](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)oraz [skonfigurować monitorowanie i diagnostykę.](service-fabric-tutorial-monitoring-aspnet.md)

Możesz też wypróbować jeden z poniższych przejść i stworzyć swój pierwszy...
- [Usługa niezawodne usługi C# w systemie Windows](service-fabric-reliable-services-quick-start.md) 
- [Usługa C# Reliable Actors w systemie Windows](service-fabric-reliable-actors-get-started.md) 
- [Usługa wykonywalna gościa w systemie Windows](quickstart-guest-app.md) 
- [Aplikacja kontenera dla systemu Windows](service-fabric-get-started-containers.md) 

Możesz być również zainteresowany wypróbowaniem naszych [przykładowych aplikacji.](https://aka.ms/servicefabricsamples)

## <a name="have-questions-or-feedback--need-to-report-an-issue"></a>Masz pytania lub uwagi?  Chcesz zgłosić problem?
Zapoznaj się z [typowymi pytaniami](service-fabric-common-questions.md) i znajdź odpowiedzi na temat tego, co może zrobić sieci szkieletowej usług i jak należy jej używać.

[Opcje pomocy technicznej](service-fabric-support.md) zawierają listę forów w StackOverflow i MSDN do zadawania pytań, a także opcje zgłaszania problemów, uzyskiwania pomocy technicznej i przesyłania opinii o produktach.

## <a name="the-application-project"></a>Projekt aplikacji
Każda nowa aplikacja zawiera projekt aplikacji. Może istnieć jeden lub dwa dodatkowe projekty, w zależności od wybranego rodzaju usługi.

Projekt wniosku składa się z:

* Zestaw odwołań do usług, które tworzą aplikację.
* Trzy profile publikowania (1-Node Local, 5-Node Local i Cloud), których można użyć do obsługi preferencji dotyczących pracy z różnymi środowiskami — takich jak preferencje związane z punktem końcowym klastra i czy domyślnie przeprowadzać wdrożenia uaktualniania.
* Trzy pliki parametrów aplikacji (takie same jak powyżej), których można użyć do obsługi konfiguracji aplikacji specyficznych dla środowiska, takich jak liczba partycji do utworzenia dla usługi. Dowiedz się, jak [skonfigurować aplikację dla wielu środowisk.](service-fabric-manage-multiple-environment-app-configuration.md)
* Skrypt wdrażania, który służy do wdrażania aplikacji z wiersza polecenia lub jako część zautomatyzowanej ciągłej integracji i potoku wdrażania. Dowiedz się więcej o [wdrażaniu aplikacji za pomocą programu PowerShell](service-fabric-deploy-remove-applications.md).
* Manifest aplikacji, który opisuje aplikację. Manifest można znaleźć w folderze ApplicationPackageRoot. Dowiedz się więcej o [manifestach aplikacji i usług](service-fabric-application-model.md).



## <a name="learn-more-about-the-programming-models"></a>Dowiedz się więcej o modelach programowania
Usługa Service Fabric oferuje wiele sposobów pisania i zarządzania usługami.  Oto przegląd i informacje koncepcyjne na temat [bezstanowych i stanowych niezawodnych usług,](service-fabric-reliable-services-introduction.md) [wiarygodnych aktorów,](service-fabric-reliable-actors-introduction.md) [kontenerów,](service-fabric-containers-overview.md) [plików wykonywalnych gości](service-fabric-guest-executables-introduction.md)oraz [bezpaństwowych i stanowych usług ASP.NET podstawowych.](service-fabric-reliable-services-communication-aspnetcore.md)

## <a name="learn-about-service-communication"></a>Dowiedz się więcej o komunikacji serwisowej
Aplikacja sieci szkieletowej usług składa się z różnych usług, gdzie każda usługa wykonuje specjalistyczne zadanie. Te usługi mogą komunikować się ze sobą i mogą istnieć aplikacje klienckie poza klastrem, które łączą się z usługami i komunikują się z nią. Dowiedz się, jak [skonfigurować komunikację z usługami i między nimi](service-fabric-connect-and-communicate-with-services.md) w sieci szkieletowej usług. 

## <a name="learn-about-configuring-application-security"></a>Dowiedz się więcej o konfigurowaniu zabezpieczeń aplikacji
Można zabezpieczyć aplikacje, które są uruchomione w klastrze w ramach różnych kont użytkowników. Sieci szkieletowej usług pomaga również zabezpieczyć zasoby, które są używane przez aplikacje w czasie wdrażania w ramach kont użytkowników — na przykład pliki, katalogi i certyfikaty. Dzięki temu uruchamianie aplikacji, nawet w środowisku hostowanym udostępnionego, jest bezpieczniejsze od siebie nawzajem.  Dowiedz się, jak [skonfigurować zasady zabezpieczeń dla aplikacji](service-fabric-application-runas-security.md).

Aplikacja może zawierać poufne informacje, takie jak parametry połączenia magazynu, hasła lub inne wartości, które nie powinny być obsługiwane w postaci zwykłego tekstu. Dowiedz się, jak [zarządzać wpisami tajnymi w aplikacji](service-fabric-application-secret-management.md).

## <a name="learn-about-the-application-lifecycle"></a>Dowiedz się więcej o cyklu życia aplikacji
Podobnie jak w przypadku innych platform, aplikacja sieci szkieletowej usług zwykle przechodzi przez następujące fazy: projektowanie, tworzenie, testowanie, wdrażanie, uaktualnianie, konserwacja i usuwanie. [Ten artykuł](service-fabric-application-lifecycle.md) zawiera omówienie interfejsów API i jak są one używane przez różne role w fazie cyklu życia aplikacji sieci szkieletowej usług.

## <a name="next-steps"></a>Następne kroki
- [Tworzenie klastra systemu Windows na platformie Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md).
- Wizualizuj klaster, w tym wdrożone aplikacje i układ fizyczny, za [pomocą Eksploratora sieci szkieletowej usług](service-fabric-visualizing-your-cluster.md).
- [Wersja i uaktualnienie usług](service-fabric-application-upgrade-tutorial.md)


