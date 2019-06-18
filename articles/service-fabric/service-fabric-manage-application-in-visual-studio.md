---
title: Zarządzaj aplikacjami usługi Azure Service Fabric w programie Visual Studio | Dokumentacja firmy Microsoft
description: Visual Studio umożliwia tworzenie, opracowywanie, pakietów, wdrażanie i debugowanie aplikacji usługi Azure Service Fabric i usług.
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: chackdan
editor: ''
ms.assetid: c317cb7e-7eae-466e-ba41-6aa2518be5cf
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.custom: vs-azure
ms.workload: azure-vs
ms.date: 03/26/2018
ms.author: mikhegn
ms.openlocfilehash: 4744858869e10094389be58ddd3960cb8cc2773a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60720101"
---
# <a name="use-visual-studio-to-simplify-writing-and-managing-your-service-fabric-applications"></a>Upraszczanie pisania i zarządzania nimi aplikacje usługi Service Fabric przy użyciu programu Visual Studio
Możesz zarządzać aplikacje usługi Azure Service Fabric i usługi za pomocą programu Visual Studio. Po [Konfigurowanie środowiska projektowego](service-fabric-get-started.md), można użyć programu Visual Studio do tworzenia aplikacji usługi Service Fabric, dodawanie usług lub pakietu, zarejestruj się i wdrażanie aplikacji w lokalnego klastra projektowego.

## <a name="deploy-your-service-fabric-application"></a>Wdrażanie aplikacji usługi Service Fabric
Domyślnie wdrażanie aplikacji obejmuje następujące kroki w jednej operacji proste:

1. Tworzenie pakietu aplikacji
2. Przekazywanie pakietu aplikacji do magazynu obrazów
3. Rejestrowanie typu aplikacji
4. Usuwając wszelkie uruchomione wystąpienia aplikacji
5. Tworzenie instancji aplikacji

W programie Visual Studio naciskając klawisz **F5** wdraża twoją aplikację i dołączyć debuger do wszystkich wystąpień aplikacji. Możesz użyć **kombinację klawiszy Ctrl + F5** wdrożyć aplikację bez debugowania, lub można opublikować w klastrze lokalnym lub zdalnym za pomocą profilu publikowania.

### <a name="application-debug-mode"></a>Tryb debugowania aplikacji
Program Visual Studio zapewniają właściwość o nazwie **tryb debugowania aplikacji**, która kontroluje, w jaki sposób program Visual Studio do obsługi wdrażania aplikacji w ramach debugowania.

#### <a name="to-set-the-application-debug-mode-property"></a>Aby ustawić właściwość tryb debugowania aplikacji
1. W usłudze Service Fabric application projektu (*.sfproj) menu skrótów wybierz **właściwości** (lub naciśnij **F4** klucza).
2. W **właściwości** oknie **tryb debugowania aplikacji** właściwości.

![Ustaw właściwość tryb debugowania aplikacji][debugmodeproperty]

#### <a name="application-debug-modes"></a>Tryb debugowania aplikacji

1. **Aktualizovat aplikaci** ten tryb pozwala szybko zmienić, i debugowania kodu oraz obsługuje edycję plików statyczną sieci web podczas debugowania. Ten tryb działa tylko jeśli trybu 1 węzła lokalnego klastra projektowego. Jest to domyślny tryb debugowania aplikacji.
2. **Usuń aplikację** powoduje, że aplikacja ma zostać usunięty podczas kończenia sesji debugowania.
3. **Automatyczne uaktualnienie** aplikacji będzie kontynuował działanie podczas kończenia sesji debugowania. Następnej sesji debugowania traktują wdrożenia jako uaktualnienie. Proces uaktualniania zachowuje wszelkie dane, które zostały wprowadzone w poprzedniej sesji debugowania.
4. **Aplikacja** aplikacji utrzymuje uruchomionych w klastrze podczas kończenia sesji debugowania. Po rozpoczęciu następnej sesji debugowania aplikacji zostaną usunięte.

Aby uzyskać **automatycznego uaktualniania** dane zostaną zachowane, stosując możliwości uaktualniania aplikacji usługi Service Fabric. Aby uzyskać więcej informacji na temat uaktualnienia aplikacji i jak może wykonać uaktualnienie w środowisku rzeczywistych zobacz [uaktualnianie aplikacji usługi Service Fabric](service-fabric-application-upgrade.md).

## <a name="add-a-service-to-your-service-fabric-application"></a>Dodać usługę do aplikacji usługi Service Fabric
Możesz dodać nowe usługi do aplikacji w taki sposób, w celu rozszerzenia ich funkcji. Aby upewnić się, że usługa znajduje się w pakiecie aplikacji, Dodaj usługi za pośrednictwem **Nowa usługa Service Fabric...**  elementu menu.

![Dodaj nową usługę Service Fabric][newservice]

Wybierz typ projektu usługi Service Fabric, aby dodać do swojej aplikacji, a następnie określ nazwę usługi.  Zobacz [wybierając framework dla usługi](service-fabric-choose-framework.md) ułatwią podjęcie decyzji o jakiego typu Usługa do użycia.

![Wybierz typ projektu usługi Service Fabric można dodać do aplikacji][addserviceproject]

Nowa usługa jest dodawany do rozwiązania i istniejący pakiet aplikacji. Odwołania do usług i domyślnego wystąpienia usługi zostaną dodane do manifestu aplikacji, co powoduje usługi zostać utworzona i uruchomiona przy następnym wdrożenia aplikacji.

![Nowa usługa zostanie dodany do manifest aplikacji][newserviceapplicationmanifest]

## <a name="package-your-service-fabric-application"></a>Pakiet aplikacji usługi Service Fabric
Do wdrożenia aplikacji i usług w klastrze, należy utworzyć pakiet aplikacji.  Pakiet organizuje manifest aplikacji, manifesty usługi i inne niezbędne pliki w określonym układzie.  Program Visual Studio ustawia i zarządza pakietu w folderze projektu aplikacji w katalogu "pakietu".  Klikając **pakietu** z **aplikacji** menu kontekstowe tworzy lub aktualizuje pakiet aplikacji.

## <a name="remove-applications-and-application-types-using-cloud-explorer"></a>Usuwanie aplikacji i typów aplikacji za pomocą Eksploratora chmury
Można wykonać operacji zarządzania podstawowy klaster z poziomu programu Visual Studio za pomocą Eksploratora chmury, który można uruchomić z **widoku** menu. Na przykład można usunąć aplikacje i wstrzymał obsługi administracyjnej typy aplikacji w klastrach lokalnym lub zdalnym.

![Usuwanie aplikacji][removeapplication]

> [!TIP]
> Aby uzyskać bardziej zaawansowane funkcje zarządzania klastrem, zobacz [wizualizowanie klastra przy użyciu narzędzia Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
>
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Kolejne kroki
* [Model aplikacji usługi Service Fabric](service-fabric-application-model.md)
* [Wdrażanie aplikacji usługi Service Fabric](service-fabric-deploy-remove-applications.md)
* [Zarządzanie parametrami aplikacji dla wielu środowisk](service-fabric-manage-multiple-environment-app-configuration.md)
* [Debugowanie aplikacji usługi Service Fabric](service-fabric-debugging-your-application.md)
* [Wizualizowanie klastra przy użyciu narzędzia Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]:./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]:./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]:./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
[debugmodeproperty]:./media/service-fabric-manage-application-in-visual-studio/debugmodeproperty.png
[removeapplication]:./media/service-fabric-manage-application-in-visual-studio/removeapplication.png
