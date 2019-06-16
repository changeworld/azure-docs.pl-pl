---
title: Więcej informacji na temat tworzenia i publikowania.Net Core z aplikacji do zdalnego klastra usługi Azure Service Fabric dla systemu Linux | Dokumentacja firmy Microsoft
description: Tworzenie i publikowanie.Net Core aplikacji przeznaczonych dla zdalnej klastra systemu Linux w programie Visual Studio
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 5/20/2019
ms.author: pepogors
ms.openlocfilehash: 46d76edbe8cede12e8c7811f43c28a65c1ebaed0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67078666"
---
# <a name="use-visual-studio-to-create-and-publish-net-core-applications-targeting-a-remote-linux-service-fabric-cluster"></a>Tworzenie i publikowanie.Net Core przy użyciu programu Visual Studio aplikacje przeznaczone na zdalny klaster systemu Linux usługi Service Fabric
Za pomocą programu Visual Studio Ci narzędzi mogą tworzyć i publikować.Net Core usługi Service Fabric aplikacji przeznaczonych dla klastra systemu Linux usługi Service Fabric. Wersja zestawu SDK musi być 3.4 lub nowszej, aby wdrożyć.Net Core aplikacji przeznaczonych dla systemu Linux usługi Service Fabric clusters z programu Visual Studio.

> [!Note]
> Program Visual Studio nie obsługuje debugowanie aplikacji usługi Service Fabric, których wskazać system Linux.
>

## <a name="create-a-service-fabric-application-targeting-net-core"></a>Tworzenie aplikacji usługi Service Fabric, przeznaczone dla.Net Core
1. Uruchom program Visual Studio jako **administrator**.
2. Tworzenie projektu przy użyciu **Plik -> Nowy -> Projekt**.
3. W **nowy projekt** okno dialogowe, wybierz **Cloud -> Aplikacja usługi Service Fabric**.
![create-application]
4. Nazwij aplikację, a następnie kliknij przycisk **Ok**.
5. Na **Nowa usługa Service Fabric** wybierz typ usługi, które chcesz utworzyć w obszarze **.Net Core sekcji**.
![create-service]

## <a name="deploy-to-a-remote-linux-cluster"></a>Wdrażanie do zdalnego klastra systemu Linux
1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy aplikację a następnie wybierz pozycję **kompilacji**.
![aplikacji dla kompilacji]
2. Po zakończeniu procesu kompilacji dla aplikacji w usłudze kliknij prawym przyciskiem myszy i wybierz pozycję Edytuj **pliku csproj**.
![edit-csproj]
3. Edytuj właściwość UpdateServiceFabricManifestEnabled wartość True, aby **False** jest usługa **typ projektu aktora**. Jeśli aplikacja nie ma usługi aktora, przejdź do kroku 4.
```xml
    <UpdateServiceFabricManifestEnabled>False</UpdateServiceFabricManifestEnabled>
```
> [!Note]
> Ustawienie wartości false dla UpdateServiceFabricManifestEnabled, wyłącz aktualizacje ServiceManifest.xml podczas kompilacji. Wszelkie zmiany, takie jak dodawanie, usuwanie lub zmiana nazwy w usłudze nie zostaną odzwierciedlone w ServiceManifest.xml. Jeśli wszystkie zmiany zostaną wprowadzone, możesz musi zaktualizować pliku ServiceManifest ręcznie lub tymczasowo ustawić UpdateServiceFabricManifestEnabled true i utworzyć usługę, która zaktualizuje ServiceManifest.xml, a następnie przywróć ją z powrotem do wartość false.
>

4. Zaktualizuj RuntimeIndetifier z win7 x64 do platformy docelowej w projekcie usługi.
```xml
    <RuntimeIdentifier>ubuntu.16.04-x64</RuntimeIdentifier>
```
5. W pliku ServiceManifest należy zaktualizować program punktu wejścia, aby usunąć .exe. 
```xml
    <EntryPoint> 
    <ExeHost> 
        <Program>Actor1</Program> 
    </ExeHost> 
    </EntryPoint>
```
6. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy aplikację a następnie wybierz pozycję **Publikuj**. Zostanie wyświetlone okno dialogowe **Publikowanie**.
7. W **punkt końcowy połączenia**, wybierz punkt końcowy na zdalny klaster usługi Service Fabric systemu Linux, który ma pod kątem.
![Publikowanie aplikacji]

<!--Image references-->
[create-application]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-application-remote-linux.png
[create-service]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-service-remote-linux.png
[aplikacji dla kompilacji]:./media/service-fabric-how-to-vs-remote-linux-cluster/build-application-remote-linux.png
[edit-csproj]:./media/service-fabric-how-to-vs-remote-linux-cluster/edit-csproj-remote-linux.png
[Publikowanie aplikacji]:./media/service-fabric-how-to-vs-remote-linux-cluster/publish-remote-linux.png

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [wprowadzenie do usługi Service Fabric za pomocą.Net Core](https://azure.microsoft.com/resources/samples/service-fabric-dotnet-core-getting-started/)
