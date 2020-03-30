---
title: Odwołanie do siatki sieci szkieletowej usługi Azure Maven
description: Zawiera informacje o tym, jak używać wtyczki Maven dla usługi Service Fabric Mesh
author: suhuruli
ms.author: suhuruli
ms.date: 11/26/2018
ms.topic: reference
ms.openlocfilehash: bcc3fb7c6c3adce0997d0960c4d98227089b048b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75459023"
---
# <a name="maven-plugin-for-service-fabric-mesh"></a>Wtyczka Maven do siatki sieci szkieletowej usługi

## <a name="prerequisites"></a>Wymagania wstępne

- Zestaw SDK Java
- Maven
- Narzędzie cli platformy Azure z rozszerzeniem siatki
- Interfejs wiersza polecenia usługi Service Fabric

## <a name="goals"></a>Cele

### `azure-sfmesh:init`
- Tworzy `servicefabric` folder zawierający `appresources` folder zawierający `application.yaml` plik. 

### `azure-sfmesh:addservice`
- Tworzy folder `servicefabric` wewnątrz folderu o nazwie usługi i tworzy plik YAML usługi. 

### `azure-sfmesh:addnetwork`
- Generuje `network` YAML z podana nazwa `appresources` sieciowa w folderze 

### `azure-sfmesh:addgateway`
- Generuje `gateway` YAML z podana nazwa `appresources` bramy w folderze 

#### `azure-sfmesh:addvolume`
- Generuje `volume` YAML z podana nazwa `appresources` woluminu w folderze.

### `azure-sfmesh:addsecret`
- Generuje `secret` YAML z podana tajna `appresources` nazwa w folderze 

### `azure-sfmesh:addsecretvalue`
- Generuje `secretvalue` YAML z podana nazwa wartości tajnej i tajnej w folderze `appresources` 

### `azure-sfmesh:deploy`
- Scala yamls z `servicefabric` folderu i tworzy szablon usługi Azure Resource Manager JSON w bieżącym folderze.
- Wdraża wszystkie zasoby w środowisku usługi Azure Service Fabric Mesh 

### `azure-sfmesh:deploytocluster`
- Tworzy folder`meshDeploy`( ) który zawiera jsony wdrażania generowane z yamls, które mają zastosowanie do klastrów sieci szkieletowej usług
- Wdraża wszystkie zasoby w klastrze sieci szkieletowej usług
 

## <a name="usage"></a>Sposób użycia

Aby używać wtyczki programu Maven w aplikacji Java programu Maven, dodaj poniższy fragment kodu do pliku pom.xml:

```XML
<project>
  ...
  <build>
    ...
    <plugins>
      ...
      <plugin>
        <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-sfmesh-maven-plugin</artifactId>
          <version>0.1.0</version>
      </plugin>
    </plugins>
  </build>
</project>
```

## <a name="common-configuration"></a>Wspólna konfiguracja

Wtyczka Maven obecnie nie obsługuje typowych konfiguracji wtyczek Maven dla platformy Azure.

## <a name="how-to"></a>Instrukcje

### <a name="initialize-maven-project-for-azure-service-fabric-mesh"></a>Inicjowanie projektu Maven dla usługi Azure Service Fabric Mesh
Uruchom następujące polecenie, aby utworzyć plik YAML zasobu aplikacji.

```cmd
mvn azure-sfmesh:init -DapplicationName=helloworldserver
```

- Tworzy folder `servicefabric->appresources` wywoływany w folderze głównym zawierający aplikację YAML o nazwie`app_helloworldserver`

### <a name="add-resource-to-your-application"></a>Dodawanie zasobu do aplikacji

#### <a name="add-a-new-network-to-your-application"></a>Dodawanie nowej sieci do aplikacji
Uruchom poniższe polecenie, aby utworzyć yaml zasobu sieciowego. 

```cmd
mvn azure-sfmesh:addnetwork -DnetworkName=helloworldservicenetwork -DnetworkAddressPrefix=10.0.0.0/22
```

- Tworzy sieciowy YAML `servicefabric->appresources` w folderze o nazwie`network_helloworldservicenetwork`

#### <a name="add-a-new-service-to-your-application"></a>Dodawanie nowej usługi do aplikacji
Uruchom poniższe polecenie, aby utworzyć yaml usługi. 

```cmd
mvn azure-sfmesh:addservice -DapplicationName=helloworldserver -DserviceName=helloworldservice -DimageName=helloworldserver:latest -DlistenerPort=8080 -DnetworkRef=helloworldservicenetwork
```

- Tworzy usługę YAML `servicefabric->helloworldservice` w `service_helloworldservice` folderze `helloworldservicenetwork` o nazwie, `helloworldserver` która odwołuje się & aplikacji
- Usługa będzie słuchać na porcie 8080
- Usługa będzie używać ***helloworldserver:latest,*** ponieważ jest to obraz kontenera.

#### <a name="add-a-new-gateway-resource-to-your-application"></a>Dodawanie nowego zasobu bramy do aplikacji
Uruchom poniższe polecenie, aby utworzyć yaml zasobu bramy. 

```cmd
mvn azure-sfmesh:addgateway -DapplicationName=helloworldserver -DdestinationNetwork=helloworldservicenetwork -DgatewayName=helloworldgateway -DlistenerName=helloworldserviceListener -DserviceName=helloworldservice -DsourceNetwork=open -DtcpPort=80
```

- Tworzy nową bramę YAML w folderze `servicefabric->appresources` o nazwie`gateway_helloworldgateway`
- Odwołania `helloworldservicelistener` jako odbiornik usług, który nasłuchuje wywołań z tej bramy. Odwołuje się `helloworldservice` również do `helloworldservicenetwork` usługi jako, `helloworldserver` jako sieci i jako aplikacji. 
- Nasłuchuje żądań na porcie 80

#### <a name="add-a-new-volume-to-your-application"></a>Dodawanie nowego woluminu do aplikacji
Uruchom poniższe polecenie, aby utworzyć yaml zasobu woluminu. 

```cmd
mvn azure-sfmesh:addvolume -DvolumeAccountKey=key -DvolumeAccountName=name -DvolumeName=vol1 -DvolumeShareName=share
```

- Tworzy wolumin YAML `servicefabric->appresources` w folderze o nazwie`volume_vol1`
- Ustawia właściwości dla wymaganych `volumeAccountKey`parametrów i `volumeShareName` jak wyżej
- Aby uzyskać więcej informacji na temat odwoływania się do tego utworzonego woluminu, odwiedź następującą stronę [Wdrażanie aplikacji przy użyciu woluminu plików platformy Azure](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md)

#### <a name="add-a-new-secret-resource-to-your-application"></a>Dodawanie nowego tajnego zasobu do aplikacji
Uruchom poniższe polecenie, aby utworzyć tajny zasób yaml. 

```cmd
mvn azure-sfmesh:addsecret -DsecretName=secret1
```

- Tworzy tajny YAML `servicefabric->appresources` w folderze o nazwie`secret_secret1`
- Aby uzyskać więcej informacji na temat odwoływania się do tego utworzonego klucza tajnego, odwiedź następujące [strony: Zarządzanie wpisami tajnymi](service-fabric-mesh-howto-manage-secrets.md)

#### <a name="add-a-new-secretvalue-resource-to-your-application"></a>Dodawanie nowego zasobu tajnej wartości do aplikacji
Uruchom poniższe polecenie, aby utworzyć yaml zasobu secretvalue. 

```cmd
mvn azure-sfmesh:addsecretvalue -DsecretValue=someVal -DsecretValueName=secret1/v1
```

- Tworzenie kodu YAML zgodnego `servicefabric->appresources` z tajemnicą w folderze o nazwie`secretvalue_secret1_v1`

### <a name="run-the-application-locally"></a>Uruchamianie aplikacji lokalnie

Za pomocą celu `azure-sfmesh:deploytocluster`można uruchomić aplikację lokalnie, korzystając z poniższego polecenia:

```cmd
mvn azure-sfmesh:deploytocluster
```

Domyślnie ten cel wdraża zasoby do klastra lokalnego. Jeśli wdrażasz w klastrze lokalnym, zakłada się, że masz lokalnego klastra sieci szkieletowej usług i jest uruchomiony. Klaster lokalnej sieci szkieletowej usług dla zasobów jest obecnie obsługiwany tylko w systemie [Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

- Tworzy sieci JSOns z yamls, które mają zastosowanie do klastrów sieci szkieletowej usług
- Wdraża następnie do punktu końcowego klastra

### <a name="deploy-application-to-azure-service-fabric-mesh"></a>Wdrażanie aplikacji w usłudze Azure Service Fabric Mesh

Za pomocą celu `azure-sfmesh:deploy`można wdrożyć w środowisku siatki sieci szkieletowej usług, uruchamiając poniższe polecenie:

```cmd
mvn azure-sfmesh:deploy -DresourceGroup=todoapprg -Dlocation=eastus
```

- Tworzy grupę `todoapprg` zasobów o nazwie, jeśli nie istnieje.
- Tworzy szablon usługi Azure Resource Manager JSON przez scalanie yamls. 
- Wdraża JSON w środowisku usługi Azure Service Fabric Mesh.
