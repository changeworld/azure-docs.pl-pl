---
title: Wdrażanie aplikacji Node.js, która używa usługi MongoDB
description: Przewodnik dotyczący sposobu pakowania wielu plików wykonywalnych gościa do wdrożenia w klastrze sieci szkieletowej usług Azure
author: mikkelhegn
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 4538efc8a2426fc20dd20d1a85edaf6f76bfc649
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614472"
---
# <a name="deploy-multiple-guest-executables"></a>Wdrażanie wielu aplikacji wykonywalnych gości
W tym artykule pokazano, jak spakować i wdrożyć wiele plików wykonywalnych gości do sieci szkieletowej usługi Azure. Aby zbudować i wdrożyć pojedynczy pakiet sieci szkieletowej usług przeczytaj sposób [wdrażania pliku wykonywalnego gościa w sieci szkieletowej usług](service-fabric-deploy-existing-app.md).

W tym przewodniku pokazano, jak wdrożyć aplikację z frontogonem Node.js, który używa mongodb jako magazynu danych, można zastosować kroki do dowolnej aplikacji, która ma zależności w innej aplikacji.   

Za pomocą programu Visual Studio można utworzyć pakiet aplikacji, który zawiera wiele plików wykonywalnych gościa. Zobacz [Używanie programu Visual Studio do pakowania istniejącej aplikacji](service-fabric-deploy-existing-app.md). Po dodaniu pierwszego pliku wykonywalnego gościa kliknij prawym przyciskiem myszy projekt aplikacji i wybierz **usługę Add->New Service Fabric,** aby dodać drugi projekt wykonywalny gościa do rozwiązania. Uwaga: Jeśli zdecydujesz się połączyć źródło w projekcie programu Visual Studio, tworzenie rozwiązania programu Visual Studio, upewnij się, że pakiet aplikacji jest aktualny ze zmianami w źródle. 

## <a name="samples"></a>Samples
* [Przykład do pakowania i wdrażania pliku wykonywalnego gościa](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Próbka dwóch plików wykonywalnych gościa (C# i nodejs) komunikujących się za pośrednictwem usługi nazewnictwa przy użyciu rest](https://github.com/Azure-Samples/service-fabric-containers)

## <a name="manually-package-the-multiple-guest-executable-application"></a>Ręczne pakowanie aplikacji wykonywalnej dla wielu gości
Alternatywnie można ręcznie spakować plik wykonywalny gościa. Aby uzyskać szczegółowe informacje, zobacz [Ręczne pakowanie i wdrażanie istniejącego pliku wykonywalnego](service-fabric-deploy-existing-app.md#manually-package-and-deploy-an-existing-executable).

### <a name="packaging-the-nodejs-application"></a>Pakowanie aplikacji Node.js
W tym artykule przyjęto założenie, że plik Node.js nie jest zainstalowany w węzłach w klastrze sieci szkieletowej usług. W związku z tym należy dodać Node.exe do katalogu głównego aplikacji węzła przed pakowaniem. Struktura katalogów aplikacji Node.js (przy użyciu platformy sieci Web Express i aparatu szablonu Jade) powinna wyglądać podobnie do poniższej:

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

W następnym kroku należy utworzyć pakiet aplikacji dla aplikacji Node.js. Poniższy kod tworzy pakiet aplikacji sieci szkieletowej usług, który zawiera aplikację Node.js.

```
.\ServiceFabricAppPackageUtil.exe /source:'[yourdirectory]\MyNodeApplication' /target:'[yourtargetdirectory] /appname:NodeService /exe:'node.exe' /ma:'bin/www' /AppType:NodeAppType
```

Poniżej znajduje się opis parametrów, które są używane:

* **/source** wskazuje do katalogu aplikacji, która powinna być spakowana.
* **/target** definiuje katalog, w którym pakiet powinien zostać utworzony. Ten katalog musi się różnić od katalogu źródłowego.
* **/appname** definiuje nazwę aplikacji istniejącej aplikacji. Ważne jest, aby zrozumieć, że przekłada się to na nazwę usługi w manifeście, a nie na nazwę aplikacji sieci szkieletowej usług.
* **/exe** definiuje plik wykonywalny, który ma `node.exe`zostać uruchomiony w sieci szkieletowej usług, w tym przypadku .
* **/ma** definiuje argument, który jest używany do uruchomienia pliku wykonywalnego. Ponieważ plik Node.js nie jest zainstalowany, sieć szkieletowa usług musi `node.exe bin/www`uruchomić serwer sieci Web Node.js, wykonując program .  `/ma:'bin/www'`informuje narzędzie pakujące, aby używało `bin/www` jako argumentu dla node.exe.
* **/AppType** definiuje nazwę typu aplikacji sieci szkieletowej usług.

Jeśli przejdziesz do katalogu, który został określony w parametrze /target, widać, że narzędzie utworzyło w pełni funkcjonujący pakiet sieci szkieletowej usług, jak pokazano poniżej:

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
Wygenerowany servicemanifest.xml ma teraz sekcję, która opisuje, jak serwer sieci Web Node.js powinny być uruchamiane, jak pokazano w fragment kodu poniżej:

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
W tym przykładzie serwer sieci web Node.js nasłuchuje portu 3000, więc należy zaktualizować informacje o punkcie końcowym w pliku ServiceManifest.xml, jak pokazano poniżej.   

```xml
<Resources>
      <Endpoints>
         <Endpoint Name="NodeServiceEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
</Resources>
```
### <a name="packaging-the-mongodb-application"></a>Pakowanie aplikacji MongoDB
Teraz, gdy zostały spakowane node.js aplikacji, można iść do przodu i pakiet MongoDB. Jak wspomniano wcześniej, kroki, które można przejść teraz nie są specyficzne dla Node.js i MongoDB. W rzeczywistości mają one zastosowanie do wszystkich aplikacji, które mają być pakowane razem jako jedna aplikacja sieci szkieletowej usług.  

Aby spakować MongoDB, chcesz upewnić się, że pakiet Mongod.exe i Mongo.exe. Oba pliki binarne `bin` znajdują się w katalogu katalogu instalacji MongoDB. Struktura katalogów wygląda podobnie do poniższej.

```
|-- MongoDB
    |-- bin
        |-- mongod.exe
        |-- mongo.exe
        |-- anybinary.exe
```
Usługa Service Fabric musi uruchomić MongoDB z poleceniem podobnym do `/ma` poniższego, więc musisz użyć parametru podczas pakowania MongoDB.

```
mongod.exe --dbpath [path to data]
```
> [!NOTE]
> Dane nie są zachowywane w przypadku awarii węzła, jeśli umieścisz katalog danych MongoDB w katalogu lokalnym węzła. Należy użyć trwałego magazynu lub zaimplementować zestaw replik MongoDB, aby zapobiec utracie danych.  
>
>

W programie PowerShell lub w powłoce poleceń uruchamiamy narzędzie pakujące z następującymi parametrami:

```
.\ServiceFabricAppPackageUtil.exe /source: [yourdirectory]\MongoDB' /target:'[yourtargetdirectory]' /appname:MongoDB /exe:'bin\mongod.exe' /ma:'--dbpath [path to data]' /AppType:NodeAppType
```

Aby dodać mongodb do pakietu aplikacji sieci szkieletowej usług, należy upewnić się, że parametr /target wskazuje ten sam katalog, który zawiera już manifest aplikacji wraz z aplikacją Node.js. Należy również upewnić się, że używasz tej samej nazwy ApplicationType.

Przejdźmy do katalogu i sprawdźmy, co stworzyło narzędzie.

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
Jak widać, narzędzie dodało nowy folder, MongoDB, do katalogu zawierającego pliki binarne MongoDB. Po otwarciu `ApplicationManifest.xml` pliku, widać, że pakiet zawiera teraz zarówno node.js aplikacji i MongoDB. Poniższy kod przedstawia zawartość manifestu aplikacji.

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
Ostatnim krokiem jest opublikowanie aplikacji w lokalnym klastrze sieci szkieletowej usług przy użyciu skryptów programu PowerShell poniżej:

```
Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath '[yourtargetdirectory]' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'NodeAppType'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'NodeAppType'

New-ServiceFabricApplication -ApplicationName 'fabric:/NodeApp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0  
```

Po pomyślnym opublikowaniu aplikacji w klastrze lokalnym można uzyskać dostęp do aplikacji Node.js na porcie, który wprowadziliśmy w manifeście usługi aplikacji Node.js — na przykład http:\//localhost:3000.

W tym samouczku zobaczysz, jak łatwo spakować dwie istniejące aplikacje jako jedną aplikację sieci szkieletowej usług. Dowiedzono się również, jak wdrożyć go w sieci szkieletowej usług, dzięki czemu mogą korzystać z niektórych funkcji sieci szkieletowej usług, takich jak wysoka dostępność i integracja systemu kondycji.

## <a name="adding-more-guest-executables-to-an-existing-application-using-yeoman-on-linux"></a>Dodawanie kolejnych plików wykonywalnych gości do istniejącej aplikacji przy użyciu yeoman w systemie Linux

Aby dodać kolejną usługę do aplikacji utworzonej już przy użyciu polecenia `yo`, wykonaj następujące czynności: 
1. Zmień katalog na katalog główny istniejącej aplikacji.  Na przykład wpisz polecenie `cd ~/YeomanSamples/MyApplication`, jeśli aplikacja `MyApplication` to aplikacja utworzona przez narzędzie Yeoman.
2. Uruchom `yo azuresfguest:AddService` i podaj niezbędne szczegóły.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o wdrażaniu kontenerów z [siecią szkieletową usług i kontenerami — omówienie](service-fabric-containers-overview.md)
* [Przykład do pakowania i wdrażania pliku wykonywalnego gościa](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Próbka dwóch plików wykonywalnych gościa (C# i nodejs) komunikujących się za pośrednictwem usługi nazewnictwa przy użyciu rest](https://github.com/Azure-Samples/service-fabric-containers)
