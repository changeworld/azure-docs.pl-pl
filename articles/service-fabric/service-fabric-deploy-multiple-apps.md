---
title: Wdrażanie aplikacji node. js korzystającej z MongoDB
description: Wskazówki dotyczące pakowania wielu plików wykonywalnych gościa w celu wdrożenia w klastrze usługi Azure Service Fabric
author: mikkelhegn
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 4538efc8a2426fc20dd20d1a85edaf6f76bfc649
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/02/2020
ms.locfileid: "75614472"
---
# <a name="deploy-multiple-guest-executables"></a>Wdrażanie wielu aplikacji wykonywalnych — gości
W tym artykule pokazano, jak spakować i wdrożyć wiele plików wykonywalnych gościa na platformie Azure Service Fabric. Aby skompilować i wdrożyć pojedynczy pakiet Service Fabric, przeczytaj artykuł jak [wdrożyć plik wykonywalny gościa do Service Fabric](service-fabric-deploy-existing-app.md).

W tym instruktażu przedstawiono sposób wdrażania aplikacji za pomocą frontonu Node. js, który używa MongoDB jako magazynu danych, można zastosować kroki do dowolnej aplikacji, która ma zależności od innej aplikacji.   

Możesz użyć programu Visual Studio, aby utworzyć pakiet aplikacji, który zawiera wiele plików wykonywalnych gościa. Zobacz [Używanie programu Visual Studio do pakowania istniejącej aplikacji](service-fabric-deploy-existing-app.md). Po dodaniu pierwszego pliku wykonywalnego gościa kliknij prawym przyciskiem myszy projekt aplikacji i wybierz **usługę dodaj > nowe Service Fabric** , aby dodać drugi projekt wykonywalny gościa do rozwiązania. Uwaga: Jeśli zdecydujesz się połączyć źródło w projekcie programu Visual Studio, tworzenie rozwiązania programu Visual Studio, upewnij się, że pakiet aplikacji jest aktualny przy użyciu zmian w źródle. 

## <a name="samples"></a>Samples
* [Przykład dla pakowania i wdrażania pliku wykonywalnego gościa](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Przykład dwóch plików wykonywalnych gościaC# (i NodeJS) komunikujących się za pośrednictwem usługi nazewnictwa przy użyciu REST](https://github.com/Azure-Samples/service-fabric-containers)

## <a name="manually-package-the-multiple-guest-executable-application"></a>Ręczne pakowanie wielu aplikacji wykonywalnych gościa
Alternatywnie można ręcznie spakować plik wykonywalny gościa. Aby uzyskać szczegółowe informacje, zobacz [Ręczne pakowanie i wdrażanie istniejącego pliku wykonywalnego](service-fabric-deploy-existing-app.md#manually-package-and-deploy-an-existing-executable).

### <a name="packaging-the-nodejs-application"></a>Pakowanie aplikacji node. js
W tym artykule przyjęto założenie, że Node. js nie jest zainstalowany na węzłach w klastrze Service Fabric. W związku z tym należy dodać Node. exe do katalogu głównego aplikacji węzła przed opakowaniem. Struktura katalogów aplikacji node. js (za pomocą języka Express Web Framework i aparatu szablonów Jade) powinna wyglądać podobnie do przedstawionego poniżej:

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

Następnym krokiem jest utworzenie pakietu aplikacji dla aplikacji node. js. Poniższy kod tworzy Service Fabric pakiet aplikacji, który zawiera aplikację Node. js.

```
.\ServiceFabricAppPackageUtil.exe /source:'[yourdirectory]\MyNodeApplication' /target:'[yourtargetdirectory] /appname:NodeService /exe:'node.exe' /ma:'bin/www' /AppType:NodeAppType
```

Poniżej znajduje się opis używanych parametrów:

* **/Source** wskazuje katalog aplikacji, która powinna być spakowana.
* **/Target** definiuje katalog, w którym ma zostać utworzony pakiet. Ten katalog musi się różnić od katalogu źródłowego.
* **/APPNAME** definiuje nazwę aplikacji istniejącej aplikacji. Ważne jest, aby zrozumieć, że to tłumaczenie na nazwę usługi w manifeście, a nie na nazwę aplikacji Service Fabric.
* **/exe** definiuje plik wykonywalny, który Service Fabric być uruchamiany, w tym przypadku `node.exe`.
* **/ma** definiuje argument, który jest używany do uruchamiania pliku wykonywalnego. Ponieważ nie zainstalowano środowiska Node. js, Service Fabric musi uruchomić serwer sieci Web Node. js, wykonując `node.exe bin/www`.  `/ma:'bin/www'` informuje narzędzie pakowania, aby użyć `bin/www` jako argumentu dla Node. exe.
* **/AppType** definiuje nazwę typu aplikacji Service Fabric.

W przypadku przechodzenia do katalogu, który został określony w parametrze/Target, można zobaczyć, że narzędzie utworzyło w pełni funkcjonalny Service Fabric pakiet, jak pokazano poniżej:

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
Wygenerowany plik servicemanifest. xml zawiera teraz sekcję opisującą sposób uruchamiania serwera sieci Web w języku Node. js, jak pokazano w poniższym fragmencie kodu:

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
W tym przykładzie serwer sieci Web Node. js nasłuchuje na porcie 3000, dlatego należy zaktualizować informacje o punkcie końcowym w pliku servicemanifest. XML, jak pokazano poniżej.   

```xml
<Resources>
      <Endpoints>
         <Endpoint Name="NodeServiceEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
</Resources>
```
### <a name="packaging-the-mongodb-application"></a>Pakowanie aplikacji MongoDB
Teraz, gdy masz spakowaną aplikację Node. js, możesz przejść do MongoDB pakietu. Jak wspomniano wcześniej, kroki wykonywane przez użytkownika nie są specyficzne dla środowiska Node. js i MongoDB. W rzeczywistości są one stosowane do wszystkich aplikacji, które są przeznaczone do spakowania jako jedna aplikacja Service Fabric.  

Aby spakować MongoDB, należy upewnić się, że pakiet Mongod. exe i Mongo. exe zostały utworzone. Oba pliki binarne znajdują się w katalogu `bin` katalogu instalacyjnego MongoDB. Struktura katalogów wygląda podobnie do przedstawionego poniżej.

```
|-- MongoDB
    |-- bin
        |-- mongod.exe
        |-- mongo.exe
        |-- anybinary.exe
```
Service Fabric musi rozpocząć MongoDB z poleceniem podobnym do przedstawionego poniżej, dlatego należy użyć parametru `/ma` podczas pakowania MongoDB.

```
mongod.exe --dbpath [path to data]
```
> [!NOTE]
> Dane nie są zachowywane w przypadku awarii węzła, jeśli umieścisz katalog danych MongoDB w lokalnym katalogu węzła. Aby zapobiec utracie danych, należy użyć trwałego magazynu lub wdrożyć zestaw replik MongoDB.  
>
>

W programie PowerShell lub w powłoce poleceń uruchamiamy narzędzie pakowanie z następującymi parametrami:

```
.\ServiceFabricAppPackageUtil.exe /source: [yourdirectory]\MongoDB' /target:'[yourtargetdirectory]' /appname:MongoDB /exe:'bin\mongod.exe' /ma:'--dbpath [path to data]' /AppType:NodeAppType
```

Aby dodać MongoDB do pakietu aplikacji Service Fabric, należy upewnić się, że parametr/Target wskazuje na ten sam katalog, który zawiera już manifest aplikacji wraz z aplikacją Node. js. Należy również upewnić się, że jest używana ta sama nazwa aplikacji.

Przejdźmy do katalogu i sprawdź, jakie narzędzie zostało utworzone.

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
Jak widać, narzędzie dodaliśmy nowy folder MongoDB do katalogu, który zawiera pliki binarne MongoDB. Jeśli otworzysz plik `ApplicationManifest.xml`, zobaczysz, że pakiet zawiera teraz zarówno aplikację Node. js, jak i MongoDB. Poniższy kod przedstawia zawartość manifestu aplikacji.

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
Ostatnim krokiem jest opublikowanie aplikacji w lokalnym klastrze Service Fabric przy użyciu skryptów programu PowerShell poniżej:

```
Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath '[yourtargetdirectory]' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'NodeAppType'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'NodeAppType'

New-ServiceFabricApplication -ApplicationName 'fabric:/NodeApp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0  
```

Po pomyślnym opublikowaniu aplikacji w klastrze lokalnym można uzyskać dostęp do aplikacji node. js na porcie wprowadzonym w manifeście usługi aplikacji node. js — na przykład http:\//localhost: 3000.

W tym samouczku pokazano, jak łatwo spakować dwa istniejące aplikacje jako jedną aplikację Service Fabricową. Wiesz również, jak wdrożyć go w Service Fabric, aby można było korzystać z niektórych funkcji Service Fabric, takich jak wysoka dostępność i integracja systemu kondycji.

## <a name="adding-more-guest-executables-to-an-existing-application-using-yeoman-on-linux"></a>Dodawanie większej liczby plików wykonywalnych gościa do istniejącej aplikacji przy użyciu usługi narzędzia Yeoman w systemie Linux

Aby dodać kolejną usługę do aplikacji utworzonej już przy użyciu polecenia `yo`, wykonaj następujące czynności: 
1. Zmień katalog na katalog główny istniejącej aplikacji.  Na przykład wpisz polecenie `cd ~/YeomanSamples/MyApplication`, jeśli aplikacja `MyApplication` to aplikacja utworzona przez narzędzie Yeoman.
2. Uruchom `yo azuresfguest:AddService` i podaj niezbędne szczegóły.

## <a name="next-steps"></a>Następne kroki
* Informacje o wdrażaniu kontenerów za pomocą [Service Fabric i kontenerów — Omówienie](service-fabric-containers-overview.md)
* [Przykład dla pakowania i wdrażania pliku wykonywalnego gościa](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Przykład dwóch plików wykonywalnych gościaC# (i NodeJS) komunikujących się za pośrednictwem usługi nazewnictwa przy użyciu REST](https://github.com/Azure-Samples/service-fabric-containers)
