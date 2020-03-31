---
title: Tworzenie i publikowanie aplikacji core a.Net w zdalnym klastrze linuksa
description: Tworzenie i publikowanie aplikacji .Net Core kierowanych na zdalny klaster systemu Linux z programu Visual Studio
author: peterpogorski
ms.topic: troubleshooting
ms.date: 5/20/2019
ms.author: pepogors
ms.openlocfilehash: c30eedb6782e4172d677f16e27441f28c78cdd89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614353"
---
# <a name="use-visual-studio-to-create-and-publish-net-core-applications-targeting-a-remote-linux-service-fabric-cluster"></a>Tworzenie i publikowanie aplikacji .Net Core kierowanych na zdalny klaster sieci szkieletowej usług Linux za pomocą programu Visual Studio
Za pomocą narzędzi programu Visual Studio można tworzyć i publikować aplikacje net.Net Core sieciowej przeznaczone dla klastra sieci szkieletowej usług systemu Linux. Wersja zestawu SDK musi mieć wartość 3.4 lub wyższa, aby wdrożyć aplikację .Net Core kierowaną na klastry sieci szkieletowej usług systemu Linux z programu Visual Studio.

> [!Note]
> Visual Studio nie obsługuje debugowania aplikacji sieci szkieletowej usług, które są przeznaczone dla systemu Linux.
>

## <a name="create-a-service-fabric-application-targeting-net-core"></a>Tworzenie kierowania na aplikację sieci szkieletowej usług .Net Core
1. Uruchom program Visual Studio jako **administrator**.
2. Utwórz projekt za pomocą **projektu >>.**
3. W oknie dialogowym **Nowy projekt** wybierz pozycję **Cloud -> Service Fabric Application**.
![tworzenie aplikacji]
4. Nazwij aplikację i kliknij przycisk **Ok**.
5. Na stronie **Nowa usługa sieci szkieletowej usług** wybierz typ usługi, którą chcesz utworzyć w sekcji **.Net Core**.
![tworzenie-usługa]

## <a name="deploy-to-a-remote-linux-cluster"></a>Wdrażanie w zdalnym klastrze systemu Linux
1. W eksploratorze rozwiązań kliknij prawym przyciskiem myszy aplikację i wybierz polecenie **Buduj**.
![kompilacja-aplikacja]
2. Po zakończeniu procesu kompilacji aplikacji kliknij prawym przyciskiem myszy usługę i wybierz edytuj **plik csproj**.
![edit-csproj]
3. Edytuj UpdateServiceFabricManifestWładka właściwość z Wartości true do **false,** jeśli usługa jest **typu projektu aktora**. Jeśli aplikacja nie ma usługi aktora, przejdź do kroku 4.
```xml
    <UpdateServiceFabricManifestEnabled>False</UpdateServiceFabricManifestEnabled>
```
> [!Note]
> Ustawienie UpdateServiceFabricManifestEnabled false, spowoduje wyłączenie aktualizacji ServiceManifest.xml podczas kompilacji. Wszelkie zmiany, takie jak dodawanie, usuwanie lub zmienianie nazwy usługi, nie będą odzwierciedlane w pliku ServiceManifest.xml. Jeśli zostaną wprowadzone jakiekolwiek zmiany, należy ręcznie zaktualizować ServiceManifest lub tymczasowo ustawić UpdateServiceFabricManifestEnabled true i utworzyć usługę, która zaktualizuje ServiceManifest.xml, a następnie przywrócić ją z powrotem do false.
>

4. Zaktualizuj runtimeIndetifier z win7-x64 do platformy docelowej w projekcie usługi.
```xml
    <RuntimeIdentifier>ubuntu.16.04-x64</RuntimeIdentifier>
```
5. W servicemanifest zaktualizuj program entrypoint, aby usunąć program .exe. 
```xml
    <EntryPoint> 
    <ExeHost> 
        <Program>Actor1</Program> 
    </ExeHost> 
    </EntryPoint>
```
6. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy aplikację i wybierz polecenie **Publikuj**. Zostanie wyświetlone okno dialogowe **Publikowanie**.
7. W **punkcie końcowym połączenia**wybierz punkt końcowy dla zdalnego klastra sieci szkieletowej sieci szkieletowej systemu Linux, który chcesz kierować.
![publikujemy-wniosek]

<!--Image references-->
[tworzenie aplikacji]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-application-remote-linux.png
[tworzenie-usługa]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-service-remote-linux.png
[kompilacja-aplikacja]:./media/service-fabric-how-to-vs-remote-linux-cluster/build-application-remote-linux.png
[edit-csproj]:./media/service-fabric-how-to-vs-remote-linux-cluster/edit-csproj-remote-linux.png
[publikujemy-wniosek]:./media/service-fabric-how-to-vs-remote-linux-cluster/publish-remote-linux.png

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej [o wprowadzenie do sieci szkieletowej usług w rdzeniu .Net](https://azure.microsoft.com/resources/samples/service-fabric-dotnet-core-getting-started/)
