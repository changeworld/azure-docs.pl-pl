---
title: Wdrażanie aplikacji Node.js, która używa bazy danych MongoDB w usłudze Azure Service Fabric | Dokumentacja firmy Microsoft
description: Przewodnik dotyczący sposobu pakowanie wielu aplikacji wykonywalnych gości, aby wdrożyć klaster usługi Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: chackdan
editor: ''
ms.assetid: b76bb756-c1ba-49f9-9666-e9807cf8f92f
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 69df9eff85d96c9cc6ca7fa1d3aabd2c54fae416
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60583869"
---
# <a name="deploy-multiple-guest-executables"></a>Wdrażanie wielu aplikacji wykonywalnych gości
Ten artykuł pokazuje, jak pakowanie i wdrażanie wielu aplikacji wykonywalnych gości w usłudze Azure Service Fabric. Do tworzenia i wdrażania jednego pakietu usługi Service Fabric przeczytaj, jak do [wdrażanie pliku wykonywalnego gościa w usłudze Service Fabric](service-fabric-deploy-existing-app.md).

Chociaż w tym instruktażu przedstawiono sposób wdrażania aplikacji za pomocą środowiska Node.js fronton, który używa produktu MongoDB jako magazynu danych, można zastosować kroki do dowolnej aplikacji, która ma zależności od innej aplikacji.   

Aby utworzyć pakiet aplikacji, która zawiera wielu aplikacji wykonywalnych gości, można użyć programu Visual Studio. Zobacz [przy użyciu programu Visual Studio do pakietu istniejącej aplikacji](service-fabric-deploy-existing-app.md). Po dodaniu pierwszego pliku wykonywalnego gościa, kliknij prawym przyciskiem myszy projekt aplikacji i wybierz pozycję **Dodaj -> Nowy usługi service Fabric** można dodać drugi projekt wykonywalny gościa do rozwiązania. Uwaga: Jeśli wybierzesz łącze do źródła w projekcie programu Visual Studio, kompilowania rozwiązania programu Visual Studio będzie upewnij się, czy pakietu aplikacji jest na bieżąco ze zmianami w źródle. 

## <a name="samples"></a>Przykłady
* [Przykład pakowanie i wdrażanie wykonywalnej gościa](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Przykład dwóch gościa pliki wykonywalne (C# i nodejs) podczas komunikowania się za pośrednictwem usługi nazewnictwa przy użyciu usługi REST](https://github.com/Azure-Samples/service-fabric-containers)

## <a name="manually-package-the-multiple-guest-executable-application"></a>Ręcznie pakowanie wielu aplikacji wykonywalnych gościa
Można też ręcznie spakować plik wykonywalny gościa. Ręczne pakowania, w tym artykule używa narzędzia tworzenia pakietów usługi Service Fabric, które znajduje się w temacie [ https://aka.ms/servicefabricpacktool ](https://aka.ms/servicefabricpacktool).

### <a name="packaging-the-nodejs-application"></a>Pakowanie aplikacji Node.js
W tym artykule założono, że Node.js nie jest zainstalowany na węzłach w klastrze usługi Service Fabric. W rezultacie musisz dodać Node.exe do katalogu głównego w węźle przed pakowania aplikacji. Struktura katalogów aplikacji Node.js (przy użyciu środowiska internetowego Express i aparatu Jade szablonu) powinien wyglądać podobnie do poniższego:

```
|-- NodeApplication
    |-- bin
        |-- www
    |-- node_modules
        |-- .bin
        |-- express
        |-- jade
        |-- etc.
    |-- public
        |-- images
        |-- etc.
    |-- routes
        |-- index.js
        |-- users.js
    |-- views
        |-- index.jade
        |-- etc.
    |-- app.js
    |-- package.json
    |-- node.exe
```

Kolejnym krokiem tworzenia pakietu aplikacji dla aplikacji Node.js. Poniższy kod tworzy pakiet aplikacji usługi Service Fabric, który zawiera aplikację Node.js.

```
.\ServiceFabricAppPackageUtil.exe /source:'[yourdirectory]\MyNodeApplication' /target:'[yourtargetdirectory] /appname:NodeService /exe:'node.exe' /ma:'bin/www' /AppType:NodeAppType
```

Poniżej przedstawiono opis parametrów, które są używane:

* **/ source** wskazuje katalog aplikacji, która powinna być w pakiecie.
* **/ target** Określa katalog, w którym można utworzyć pakietu. Ten katalog musi różnić się od katalogu źródłowego.
* **operacji** definiuje nazwę istniejącej aplikacji w aplikacji. Należy zrozumieć, czy tłumaczy nazwy usługi w manifeście, a nie nazwa aplikacji usługi Service Fabric.
* **/exe** definiuje plik wykonywalny, który powinien usługi Service Fabric można uruchomić w tym przypadku `node.exe`.
* **/ma** definiuje argumentu, który jest używany do uruchomienia pliku wykonywalnego. Jak Node.js nie jest zainstalowany, Usługa Service Fabric musi uruchomić serwera sieci web środowiska Node.js, wykonując `node.exe bin/www`.  `/ma:'bin/www'` informuje o narzędzia pakowania używanego `bin/www` jako argument dla node.exe.
* **/ Typ aplikacji** definiuje nazwę typu aplikacji usługi Service Fabric.

Jeśli możesz przejść do katalogu, który został określony w parametrze/TARGET, zostanie wyświetlony, narzędzie utworzył pełnej funkcjonalności pakietu usługi Service Fabric jak pokazano poniżej:

```
|--[yourtargetdirectory]
    |-- NodeApplication
        |-- C
              |-- bin
              |-- data
              |-- node_modules
              |-- public
              |-- routes
              |-- views
              |-- app.js
              |-- package.json
              |-- node.exe
        |-- config
              |--Settings.xml
        |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```
Wygenerowany ServiceManifest.xml ma teraz sekcja, która opisuje, jak można uruchomić serwera sieci web środowiska Node.js, jak pokazano w poniższym fragmencie kodu:

```xml
<CodePackage Name="C" Version="1.0">
    <EntryPoint>
        <ExeHost>
            <Program>node.exe</Program>
            <Arguments>'bin/www'</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
        </ExeHost>
    </EntryPoint>
</CodePackage>
```
W tym przykładzie serwer sieci web Node.js nasłuchuje na port 3000, więc musisz zaktualizować informacje o punkcie końcowym w pliku ServiceManifest.xml, jak pokazano poniżej.   

```xml
<Resources>
      <Endpoints>
         <Endpoint Name="NodeServiceEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
</Resources>
```
### <a name="packaging-the-mongodb-application"></a>Pakowanie aplikacji bazy danych MongoDB
Teraz, gdy spakowaniu aplikacji Node.js, możesz Przejdź dalej i pakietu bazy danych MongoDB. Jak wspomniano wcześniej, kroki, które przeczytanie teraz nie są specyficzne dla środowiska Node.js i MongoDB. W rzeczywistości odnoszą się do wszystkich aplikacji, które są przeznaczone do umieszczenia w pakiecie ze sobą jako jedna aplikacja usługi Service Fabric.  

Aby spakować bazy danych MongoDB, chcesz upewnij się, że pakiet Mongod.exe i Mongo.exe. Oba pliki binarne znajdują się w `bin` katalogu katalogu instalacji bazy danych MongoDB. Struktura katalogów jest podobny do poniższego.

```
|-- MongoDB
    |-- bin
        |-- mongod.exe
        |-- mongo.exe
        |-- anybinary.exe
```
Usługa Service Fabric potrzebuje, aby rozpocząć bazy danych MongoDB za pomocą polecenia podobnego do poniżej, więc należy użyć `/ma` parametru podczas pakowania bazy danych MongoDB.

```
mongod.exe --dbpath [path to data]
```
> [!NOTE]
> Dane nie są są zachowywane w przypadku awarii węzła, jeśli katalog danych bazy danych MongoDB zostanie umieszczony w katalogu lokalnym węzła. Należy korzystać z magazynu trwałego lub zaimplementować repliki bazy danych MongoDB, ustaw, aby zapobiec utracie danych.  
>
>

W programie PowerShell lub powłoki poleceń firma Microsoft Uruchom narzędzie tworzenia pakietów z następującymi parametrami:

```
.\ServiceFabricAppPackageUtil.exe /source: [yourdirectory]\MongoDB' /target:'[yourtargetdirectory]' /appname:MongoDB /exe:'bin\mongod.exe' /ma:'--dbpath [path to data]' /AppType:NodeAppType
```

Aby dodać bazy danych MongoDB do pakietu aplikacji usługi Service Fabric, należy się upewnić, że punkty parametr/target na tym samym katalogu, który już zawiera aplikację manifestu wraz z aplikacji Node.js. Należy również upewnić się, że używasz takiej samej nazwie ApplicationType.

Teraz przejdź do katalogu i sprawdzić, jakie narzędzia został utworzony.

```
|--[yourtargetdirectory]
    |-- MyNodeApplication
    |-- MongoDB
        |-- C
            |--bin
                |-- mongod.exe
                |-- mongo.exe
                |-- etc.
        |-- config
            |--Settings.xml
        |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```
Jak widać, narzędzie dodać nowy folder bazy danych MongoDB, do katalogu, który zawiera pliki binarne bazy danych MongoDB. Jeśli otworzysz `ApplicationManifest.xml` pliku, zobaczysz, że pakiet zawiera teraz aplikacją w technologii Node.js i MongoDB. Poniższy kod pokazuje zawartość manifestu aplikacji.

```xml
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyNodeApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MongoDB" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeService" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MongoDBService">
         <StatelessService ServiceTypeName="MongoDB">
            <SingletonPartition />
         </StatelessService>
      </Service>
      <Service Name="NodeServiceService">
         <StatelessService ServiceTypeName="NodeService">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
</ApplicationManifest>  
```

### <a name="publishing-the-application"></a>Publikowanie aplikacji
Ostatnim krokiem jest, aby opublikować aplikację w lokalnym klastrze usługi Service Fabric, korzystając z poniższych skryptów programu PowerShell:

```
Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath '[yourtargetdirectory]' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'NodeAppType'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'NodeAppType'

New-ServiceFabricApplication -ApplicationName 'fabric:/NodeApp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0  
```

Po pomyślnym opublikowaniu aplikacji w klastrze lokalnym, można uzyskać dostęp aplikacji Node.js przy użyciu portu, który możemy wprowadzony w manifeście usługi aplikacji Node.js — na przykład http:\//localhost:3000.

W ramach tego samouczka wiesz jak łatwo spakować dwa istniejące aplikacje jako jedna aplikacja usługi Service Fabric. Masz również pokazaliśmy, jak je wdrożyć do usługi Service Fabric, dzięki czemu mogą korzystać z niektórych funkcji usługi Service Fabric, takich jak wysoka dostępność i kondycji, integracji systemów.


## <a name="adding-more-guest-executables-to-an-existing-application-using-yeoman-on-linux"></a>Dodawanie więcej plików wykonywalnych gościa do istniejącej aplikacji w systemie Linux przy użyciu narzędzia Yeoman

Aby dodać kolejną usługę do aplikacji utworzonej już przy użyciu polecenia `yo`, wykonaj następujące czynności: 
1. Zmień katalog na katalog główny istniejącej aplikacji.  Na przykład wpisz polecenie `cd ~/YeomanSamples/MyApplication`, jeśli aplikacja `MyApplication` to aplikacja utworzona przez narzędzie Yeoman.
2. Uruchom `yo azuresfguest:AddService` i podaj odpowiednie szczegóły.

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o wdrażanie kontenerów przy użyciu [Omówienie usługi Service Fabric i kontenery](service-fabric-containers-overview.md)
* [Przykład pakowanie i wdrażanie wykonywalnej gościa](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Przykład dwóch gościa pliki wykonywalne (C# i nodejs) podczas komunikowania się za pośrednictwem usługi nazewnictwa przy użyciu usługi REST](https://github.com/Azure-Samples/service-fabric-containers)
