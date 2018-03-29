---
title: Zarządzaj aplikacjami Azure Servic sieci szkieletowej w programie Visual Studio | Dokumentacja firmy Microsoft
description: Tworzenie, tworzenie, pakietów, wdrażanie i debugowania aplikacji usługi Azure Service Fabric i usług za pomocą programu Visual Studio.
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: ''
ms.assetid: c317cb7e-7eae-466e-ba41-6aa2518be5cf
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2018
ms.author: mikhegn
ms.openlocfilehash: 5c5c3dc20996fbbdee9f9ed61eb3b09e4b274d92
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="use-visual-studio-to-simplify-writing-and-managing-your-service-fabric-applications"></a>Uproszczenie pisanie aplikacji i zarządzanie nimi z sieci szkieletowej usług za pomocą programu Visual Studio
Można zarządzać sieć szkieletowa usług Azure, aplikacji i usług za pomocą programu Visual Studio. Po wprowadzeniu [Konfigurowanie środowiska deweloperskiego](service-fabric-get-started.md), można użyć programu Visual Studio do tworzenia aplikacji usługi Service Fabric, Dodaj rejestru usług lub pakietu i wdrażania aplikacji w klastrze lokalnym programowanie.

## <a name="deploy-your-service-fabric-application"></a>Wdrażanie aplikacji sieci szkieletowej usług
Domyślnie wdrażania aplikacji zawierają następujące kroki w jednej operacji prosty:

1. Tworzenie pakietu aplikacji
2. Przekazywanie pakietu aplikacji do magazynu w obrazie
3. Rejestracja typu aplikacji
4. Usunąć wszystkie uruchomione wystąpienia aplikacji
5. Tworzenie wystąpienia aplikacji

W programie Visual Studio naciskając klawisz **F5** wdraża aplikację i Dołącz debuger do wszystkich wystąpień aplikacji. Można użyć **Ctrl + F5** do wdrożenia aplikacji bez debugowania, lub można opublikować do klastra lokalnego lub zdalnego za pomocą profilu publikowania.

### <a name="application-debug-mode"></a>Tryb debugowania aplikacji
Visual Studio Udostępnij właściwość o nazwie **tryb debugowania aplikacji**, która kontroluje sposób Visual Studio do obsługi wdrażania aplikacji w ramach debugowania.

#### <a name="to-set-the-application-debug-mode-property"></a>Aby ustawić właściwość tryb debugowania aplikacji
1. W sieci szkieletowej usług aplikacji projektu (*.sfproj) menu skrótów wybierz **właściwości** (lub naciśnij klawisz **F4** klucza).
2. W **właściwości** ustaw **tryb debugowania aplikacji** właściwości.

![Ustaw właściwość tryb debugowania aplikacji][debugmodeproperty]

#### <a name="application-debug-modes"></a>Tryb debugowania aplikacji

1. **Odświeżanie aplikacji** ten tryb pozwala na szybkie zmiany i debugować kod i obsługuje edycję plików statycznych sieci web podczas debugowania. W tym trybie działa tylko wtedy, jeśli klaster lokalny rozwój jest w trybie [węzła 1]. Jest to domyślny tryb debugowania aplikacji. (/ service-fabric-get-started-with-a-local-cluster.md#one-node-and-five-node-cluster-mode).
2. **Usuń aplikację** powoduje, że aplikacja ma zostać usunięty podczas kończenia sesji debugowania.
3. **Automatyczne uaktualnienie** aplikacji będzie kontynuował działanie podczas kończenia sesji debugowania. Następnej sesji debugowania, będą traktować jako uaktualnienia wdrożenia. Proces uaktualniania zachowuje dane, które wprowadzono w poprzedniej sesji debugowania.
4. **Zachowaj aplikacji** aplikacja przechowuje uruchomiona w klastrze podczas kończenia sesji debugowania. Na początku następnej sesji debugowania aplikacji zostaną usunięte.

Aby uzyskać **automatycznego uaktualnienia** dane zostaną zachowane, stosując możliwości uaktualnienia aplikacji sieci szkieletowej usług. Aby uzyskać więcej informacji o uaktualnianiu aplikacji i jak może wykonać uaktualnienie w środowisku prawdziwe, zobacz [uaktualniania aplikacji sieci szkieletowej usług](service-fabric-application-upgrade.md).

## <a name="add-a-service-to-your-service-fabric-application"></a>Dodawanie usługi do aplikacji sieci szkieletowej usług
Możesz dodać nowe usługi do aplikacji, aby rozszerzyć jego funkcjonalność. Aby zapewnić, że usługa jest zawarte w pakiecie aplikacji, Dodaj usługi za pośrednictwem **nowa sieci szkieletowej usług...**  elementu menu.

![Dodaj nową usługę sieci szkieletowej usług][newservice]

Wybierz typ projektu sieci szkieletowej usług, aby dodać do aplikacji, a następnie określ nazwę usługi.  Zobacz [Wybieranie framework usługi](service-fabric-choose-framework.md) Aby określić typ usługi do użycia.

![Wybierz typ projektu usługi sieci szkieletowej usług, aby dodać do aplikacji][addserviceproject]

Nowa usługa zostanie dodany do rozwiązania, a istniejący pakiet aplikacji. Odwołania do usług i domyślnego wystąpienia usługi zostaną dodane do manifestu aplikacji, co powoduje usługi można utworzyć i uruchomić przy następnym możesz wdrożyć aplikację.

![Nowa usługa jest dodawany do manifest aplikacji][newserviceapplicationmanifest]

## <a name="package-your-service-fabric-application"></a>Pakiet aplikacji sieci szkieletowej usług
Aby wdrożyć aplikację i jej usługi do klastra, należy utworzyć pakiet aplikacji.  Pakiet organizuje manifest aplikacji, usługi manifesty i inne niezbędne pliki w określonym układzie.  Visual Studio konfiguruje i zarządza pakietu w folderze projektu aplikacji, w katalogu "pkg".  Kliknięcie przycisku **pakietu** z **aplikacji** menu kontekstowe tworzy lub aktualizuje pakiet aplikacji.

## <a name="remove-applications-and-application-types-using-cloud-explorer"></a>Usuwanie aplikacji i typów aplikacji, w Eksploratorze chmury
Można wykonać operacji zarządzania podstawowy klaster z poziomu programu Visual Studio za pomocą Eksploratora chmury, które można uruchomić z **widoku** menu. Na przykład można usunąć aplikacji i wstrzymał obsługi administracyjnej typy aplikacji w klastrze lokalnym lub zdalnym.

![Usuwanie aplikacji][removeapplication]

> [!TIP]
> Aby uzyskać bardziej rozbudowane funkcje zarządzania klastrem, zobacz [wizualizacja klastra za pomocą Eksploratora usługi sieć szkieletowa](service-fabric-visualizing-your-cluster.md).
>
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Kolejne kroki
* [Model aplikacji sieci szkieletowej usług](service-fabric-application-model.md)
* [Wdrażanie aplikacji sieci szkieletowej usług](service-fabric-deploy-remove-applications.md)
* [Zarządzanie parametry aplikacji dla wielu środowisk](service-fabric-manage-multiple-environment-app-configuration.md)
* [Debugowanie aplikacji sieci szkieletowej usług](service-fabric-debugging-your-application.md)
* [Wizualizacja klastra przy użyciu Eksploratora usługi sieć szkieletowa](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]:./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]:./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]:./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
[debugmodeproperty]:./media/service-fabric-manage-application-in-visual-studio/debugmodeproperty.png
[removeapplication]:./media/service-fabric-manage-application-in-visual-studio/removeapplication.png