---
title: Wdrażanie aplikacji Node.js, która używa bazy danych MongoDB sieci szkieletowej usług Azure | Dokumentacja firmy Microsoft
description: Przewodnik dotyczący sposobu pakietu wiele plików wykonywalnych gościa do wdrożenia klastra usługi sieć szkieletowa usług Azure
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: ''
ms.assetid: b76bb756-c1ba-49f9-9666-e9807cf8f92f
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 9a7ab3881cd1058a60ff7d5f6e50c296f042e76e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206083"
---
# <a name="deploy-multiple-guest-executables"></a>Wdrażanie wielu aplikacji wykonywalnych gości
W tym artykule przedstawiono sposób pakietów i wdrożyć wiele plików wykonywalnych gościa sieć szkieletowa usług Azure. Umożliwiające tworzenie i wdrażanie pojedynczego pakietu sieci szkieletowej usług przeczytać artykuł dotyczący sposobu do [wdrażanie pliku wykonywalnego gościa sieci szkieletowej usług](service-fabric-deploy-existing-app.md).

Jeśli w tym przewodniku pokazano, jak wdrożyć aplikację z Node.js fronton, który używa produktu MongoDB do przechowywania danych, możesz zastosować kroki do dowolnej aplikacji, która zawiera zależności na inną aplikację.   

Visual Studio można użyć do utworzenia pakietu aplikacji, która zawiera wiele plików wykonywalnych gościa. Zobacz [przy użyciu programu Visual Studio do pakietu istniejącej aplikacji](service-fabric-deploy-existing-app.md). Po dodaniu pierwszego pliku wykonywalnego gościa, kliknij prawym przyciskiem myszy projekt aplikacji i wybierz opcję **Dodaj -> Usługa sieci szkieletowej usług nowe** można dodać do rozwiązania projekt wykonywalny drugi gościa. Uwaga: Jeśli wybierzesz połączenia źródła w projekcie programu Visual Studio, budowania rozwiązania Visual Studio będzie upewnić, że pakiet aplikacji jest aktualne ze zmianami w źródle. 

## <a name="samples"></a>Przykłady
* [Przykład dla pakowanie i wdrażanie pliku wykonywalnego gościa](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Przykład dwóch gościa pliki wykonywalne (C# i nodejs) podczas komunikacji za pośrednictwem usługi nazw za pomocą usługi REST](https://github.com/Azure-Samples/service-fabric-containers)

## <a name="manually-package-the-multiple-guest-executable-application"></a>Ręcznie pakietu wielu aplikacja wykonywalna gościa
Można też ręcznie pakietu pliku wykonywalnego gościa. Dla pakietów ręcznego, w tym artykule używa narzędzia pakowania sieci szkieletowej usług, które jest dostępne w [ http://aka.ms/servicefabricpacktool ](http://aka.ms/servicefabricpacktool).

### <a name="packaging-the-nodejs-application"></a>Pakowanie aplikacji Node.js
W tym artykule przyjęto założenie, że Node.js nie jest zainstalowany w węzłach klastra sieci szkieletowej usług. W rezultacie musisz dodać Node.exe do katalogu głównego aplikacji węzeł przed opakowania. Struktura katalogów aplikacji Node.js (przy użyciu aparatu Jade szablonu i platforma sieci web Express) powinien wyglądać podobnie do poniższego:

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

Jako kolejny krok należy utworzyć pakiet aplikacji dla aplikacji Node.js. Poniższy kod tworzy pakiet aplikacji sieci szkieletowej usług, który zawiera aplikację Node.js.

```
.\ServiceFabricAppPackageUtil.exe /source:'[yourdirectory]\MyNodeApplication' /target:'[yourtargetdirectory] /appname:NodeService /exe:'node.exe' /ma:'bin/www' /AppType:NodeAppType
```

Poniżej znajduje się opis parametrów, które są używane:

* **/ source** wskazuje katalog aplikacji, który powinien być spakowany.
* **/ target** definiuje katalogu, w którym można utworzyć pakietu. Ten katalog musi być inny niż katalog źródłowy.
* **elementów/appname** definiuje nazwę istniejącej aplikacji w aplikacji. Należy zrozumieć przekłada nazwy usługi w manifeście, a nie nazwę aplikacji sieci szkieletowej usług.
* **/exe** definiuje plik wykonywalny, który można uruchomić w tym przypadku powinien sieci szkieletowej usług `node.exe`.
* **/ma** definiuje argumentu, który jest używany do uruchomienia pliku wykonywalnego. Ponieważ nie zainstalowano środowiska Node.js, sieć szkieletowa usług musi uruchomić serwera sieci web Node.js, wykonując `node.exe bin/www`.  `/ma:'bin/www'` informuje narzędzie tworzenia pakietów do użycia `bin/www` jako argument dla node.exe.
* **/ Typ aplikacji** definiuje nazwę typu sieci szkieletowej usług aplikacji.

Po przejściu do katalogu, który został określony w parametrze/TARGET, można wyświetlić, narzędzie utworzył pełni funkcjonalnej pakietu sieci szkieletowej usług w sposób przedstawiony poniżej:

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
Wygenerowanego pliku ServiceManifest.xml ma teraz sekcja, która opisuje, jak można uruchomić serwera sieci web Node.js, jak pokazano w poniższym fragmencie kodu:

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
W tym przykładzie serwer sieci web Node.js nasłuchuje portem 3000, więc musisz zaktualizować informacje o punkcie końcowym w pliku ServiceManifest.xml, jak pokazano poniżej.   

```xml
<Resources>
      <Endpoints>
         <Endpoint Name="NodeServiceEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
</Resources>
```
### <a name="packaging-the-mongodb-application"></a>Pakowanie aplikacji bazy danych MongoDB
Teraz, gdy spakowaniu aplikacji Node.js, można teraz i pakietu bazy danych MongoDB. Jak wspomniano wcześniej, kroki, które można przejść przez obecnie nie są specyficzne dla środowiska Node.js i bazy danych MongoDB. W rzeczywistości mają one zastosowanie do wszystkich aplikacji, które mają być dostarczana razem w jednej aplikacji sieci szkieletowej usług.  

Aby pakiet bazy danych MongoDB, chcesz upewnij się, że pakiet Mongod.exe i Mongo.exe. Oba pliki binarne znajdują się w `bin` katalogu z katalogu instalacji bazy danych MongoDB. Struktura katalogów jest podobny do poniższego.

```
|-- MongoDB
    |-- bin
        |-- mongod.exe
        |-- mongo.exe
        |-- anybinary.exe
```
Sieć szkieletowa usług musi uruchomić bazy danych MongoDB za pomocą polecenia podobnej do tej, poniżej, należy użyć `/ma` parametru podczas tworzenia pakietu bazy danych MongoDB.

```
mongod.exe --dbpath [path to data]
```
> [!NOTE]
> Dane nie są są zachowywane w przypadku awarii węzła, jeśli katalog danych MongoDB zostanie umieszczony w katalogu lokalnego węzła. Należy używać magazynu trwałego lub implementować repliki bazy danych MongoDB, aby zapobiec utracie danych.  
>
>

W programie PowerShell lub powłoki poleceń narzędzia pakowania przeprowadzana z następującymi parametrami:

```
.\ServiceFabricAppPackageUtil.exe /source: [yourdirectory]\MongoDB' /target:'[yourtargetdirectory]' /appname:MongoDB /exe:'bin\mongod.exe' /ma:'--dbpath [path to data]' /AppType:NodeAppType
```

Aby dodać bazy danych MongoDB do pakietu aplikacji usługi Service Fabric, należy się upewnić, że punkty parametru/TARGET na tym samym katalogu, który już zawiera aplikację manifestu wraz z aplikacji Node.js. Należy również upewnić się, że używasz takiej samej nazwie atrybutów ApplicationType.

Teraz przejdź do katalogu i sprawdź, czy co narzędzie zostało utworzone.

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
Jak widać, narzędzie dodany nowy folder bazy danych MongoDB, do katalogu, który zawiera pliki binarne bazy danych MongoDB. Po otwarciu `ApplicationManifest.xml` pliku, zobaczysz, że pakiet zawiera obecnie zarówno aplikacji Node.js, jak i bazy danych MongoDB. Poniższy kod przedstawia zawartość manifest aplikacji.

```xml
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyNodeApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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
Ostatnim krokiem jest aby opublikować aplikację na lokalny klaster sieci szkieletowej usług za pomocą skryptów środowiska PowerShell poniżej:

```
Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath '[yourtargetdirectory]' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'NodeAppType'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'NodeAppType'

New-ServiceFabricApplication -ApplicationName 'fabric:/NodeApp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0  
```

Gdy aplikacja zostanie opublikowana pomyślnie z lokalnym klastrem, można uzyskać dostępu do aplikacji Node.js na porcie, który mamy zostały wprowadzone w manifeście usługi aplikacji Node.js — na przykład http://localhost:3000.

W tym samouczku jak już wspomniano łatwo pakowaniu dwóch istniejących aplikacji jako jednej aplikacji sieci szkieletowej usług. Ma przedstawiono również sposób wdrażania sieci szkieletowej usług, dzięki czemu mogą korzystać z niektórych funkcji usługi Service Fabric, takie jak wysoka dostępność i kondycji systemu integracji.


## <a name="adding-more-guest-executables-to-an-existing-application-using-yeoman-on-linux"></a>Dodawanie więcej plików wykonywalnych gościa do istniejącej aplikacji w systemie Linux przy użyciu narzędzia Yeoman

Aby dodać kolejną usługę do aplikacji utworzonej już przy użyciu polecenia `yo`, wykonaj następujące czynności: 
1. Zmień katalog na katalog główny istniejącej aplikacji.  Na przykład wpisz polecenie `cd ~/YeomanSamples/MyApplication`, jeśli aplikacja `MyApplication` to aplikacja utworzona przez narzędzie Yeoman.
2. Uruchom `yo azuresfguest:AddService` i podaj odpowiednie szczegóły.

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o wdrażaniu kontenerów [sieci szkieletowej usług i kontenery — omówienie](service-fabric-containers-overview.md)
* [Przykład dla pakowanie i wdrażanie pliku wykonywalnego gościa](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Przykład dwóch gościa pliki wykonywalne (C# i nodejs) podczas komunikacji za pośrednictwem usługi nazw za pomocą usługi REST](https://github.com/Azure-Samples/service-fabric-containers)
