---
title: Następne kroki tworzenia projektu sieci szkieletowej usług | Dokumentacja firmy Microsoft
description: Więcej informacji na temat projekt aplikacji utworzony w programie Visual Studio.  Informacje o sposobie tworzenia usług za pomocą samouczki i dowiedzieć się więcej na temat tworzenia usługi dla usługi Service Fabric.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: 299d1f97-1ca9-440d-9f81-d1d0dd2bf4df
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/07/2017
ms.author: rwike77
ms.openlocfilehash: a87dd6f4afa152aebafdde24defcabe841ae2e9c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206469"
---
# <a name="your-service-fabric-application-and-next-steps"></a>Sieć szkieletowa usług aplikacji i następne kroki
Utworzono aplikację sieci szkieletowej usług Azure. W tym artykule opisano niektóre samouczkami, aby wypróbować, w skład projektu, pewne dodatkowe informacje, które mogą być zainteresowane i potencjalne następne kroki.

## <a name="get-started-with-tutorials-walk-throughs-and-samples"></a>Rozpoczynanie pracy z samouczkami, przewodników i przykłady
Chcesz zacząć?  

Pracy za pośrednictwem platformy .NET — samouczek aplikacji. Dowiedz się, jak [tworzenia aplikacji](service-fabric-tutorial-create-dotnet-app.md) z platformy ASP.NET Core frontonu i stanowe zaplecza, [wdrożyć aplikację](service-fabric-tutorial-deploy-app-to-party-cluster.md) do klastra, [skonfigurować CI/CD](service-fabric-tutorial-deploy-app-with-cicd-vsts.md), i [— Konfiguracja monitorowania i diagnostyki](service-fabric-tutorial-monitoring-aspnet.md).

Lub, wypróbuj jedną z następujących przewodników i tworzenie pierwszej...
- [C# niezawodnej usługi w systemie Windows](service-fabric-reliable-services-quick-start.md) 
- [C# Reliable Actors usługi w systemie Windows](service-fabric-reliable-actors-get-started.md) 
- [Usługa wykonywalna gościa w systemie Windows](quickstart-guest-app.md) 
- [Aplikacja kontenera dla systemu Windows](service-fabric-get-started-containers.md) 

Może być również zainteresowana wypróbowaniem naszej [przykładowe aplikacje](http://aka.ms/servicefabricsamples).

## <a name="have-questions-or-feedback--need-to-report-an-issue"></a>Masz pytania lub opinie?  Należy zgłosić problem?
Zapoznaj się z artykułem [często zadawane pytania](service-fabric-common-questions.md) i odpowiedzi na czynności sieci szkieletowej usług i jak należy jej używać.

[Opcje pomocy technicznej](service-fabric-support.md) wymieniono fora StackOverflow i MSDN zadać pytania, a także opcje raportowania problemów, uzyskiwania pomocy technicznej i przesyłanie opinię o produkcie.

## <a name="the-application-project"></a>Projekt aplikacji
Każdej nowej aplikacji zawiera projekt aplikacji. Może to być jeden lub dwa dodatkowe projekty, w zależności od typu wybranych usług.

Projekt aplikacji obejmuje:

* Zestaw odwołania do usług, które składają się na aplikację.
* Profilów (węzła 1 lokalnego, 5 węzła lokalnego i chmura) używanych do obsługi preferencje dotyczące pracy z różnych środowiskach — takie jak preferencje dotyczące punktu końcowego klastra oraz czy można wykonywać wdrożenia uaktualnienia domyślnie trzy publikowania.
* Parametr aplikacji trzy pliki (taki sam jak powyżej) można do obsługi konfiguracje specyficzne dla środowiska aplikacji, takie jak liczba partycji można utworzyć dla usługi. Dowiedz się, jak [konfigurowania aplikacji w wielu środowiskach](service-fabric-manage-multiple-environment-app-configuration.md).
* Skrypt wdrożenia, który służy do wdrażania aplikacji z poziomu wiersza polecenia lub w ramach automatycznych ciągłego potoku integracji i wdrażania. Dowiedz się więcej o [wdrażanie aplikacji przy użyciu programu PowerShell](service-fabric-deploy-remove-applications.md).
* Manifest aplikacji zawiera opis aplikacji. Manifest można znaleźć w folderze ApplicationPackageRoot. Dowiedz się więcej o [manifestów aplikacji i usług](service-fabric-application-model.md).



## <a name="learn-more-about-the-programming-models"></a>Dowiedz się więcej o modelach programowania
Sieć szkieletowa usług oferuje wiele sposobów, aby zapisać i zarządzania usługami.  Poniżej przedstawiono omówienie i podstawowe pojęcia [bezstanowe i stanowe niezawodne usługi](service-fabric-reliable-services-introduction.md), [Reliable Actors](service-fabric-reliable-actors-introduction.md), [kontenery](service-fabric-containers-overview.md), [gościa pliki wykonywalne ](service-fabric-guest-executables-introduction.md), i [bezstanowych i stanowych usług platformy ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md).

## <a name="learn-about-service-communication"></a>Dowiedz się więcej o komunikacji usługi
Aplikacji usługi sieć szkieletowa składa się z różnych usług, w którym każda usługa wykonuje zadanie specjalne. Te usługi może komunikować się ze sobą i mogą być aplikacje klienckie poza klastrem, które nawiązać połączenie i łączyć się z usługami. Dowiedz się, jak [ustawienia komunikacji z i od usług](service-fabric-connect-and-communicate-with-services.md) w sieci szkieletowej usług. 

## <a name="learn-about-configuring-application-security"></a>Informacje na temat konfigurowania zabezpieczeń aplikacji
Możesz zabezpieczyć aplikacji uruchomionych w klastrze, w obszarze konta innego użytkownika. Zabezpieczanie zasobów, które są używane przez aplikacje w czasie wdrażania na kontach użytkowników — na przykład, plików, katalogów i certyfikatów pomaga również w sieci szkieletowej usług. Dzięki temu uruchamianie aplikacji, nawet w środowisku hostowanej udostępnionego bardziej bezpieczne od siebie nawzajem.  Dowiedz się, jak [konfigurowania zasad zabezpieczeń dla aplikacji](service-fabric-application-runas-security.md).

Twoja aplikacja mogą zawierać poufne informacje, takie jak parametry połączenia magazynu, hasła lub inne wartości, które nie powinny być traktowane w postaci zwykłego tekstu. Dowiedz się, jak [Zarządzanie kluczy tajnych w aplikacji](service-fabric-application-secret-management.md).

## <a name="learn-about-the-application-lifecycle"></a>Więcej informacji na temat cyklu życia aplikacji
Zgodnie z innych platform aplikacji usługi Service Fabric zwykle odbywa się przez następujących faz: projekt, programowanie testowania, wdrożenia, uaktualnianie, obsługi i usuwania. [W tym artykule](service-fabric-application-lifecycle.md) zawiera omówienie interfejsów API i jak są używane przez różne role w etapy cyklu życia aplikacji sieci szkieletowej usług.

## <a name="next-steps"></a>Kolejne kroki
- [Tworzenie klastra systemu Windows na platformie Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md).
- Wizualizowanie klastra, w tym wdrożone aplikacje i fizycznego układu z [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
- [Wersja i uaktualniania usług](service-fabric-application-upgrade-tutorial.md)


