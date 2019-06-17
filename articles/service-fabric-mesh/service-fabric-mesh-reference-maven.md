---
title: Dokumentacja usługi Azure Service Fabric siatki Maven | Dokumentacja firmy Microsoft
description: Zawiera odwołanie do sposobu używania wtyczki Maven dla usługi Service Fabric siatki
services: service-fabric-mesh
keywords: narzędzia maven, java, interfejsu wiersza polecenia
author: suhuruli
ms.author: suhuruli
ms.date: 11/26/2018
ms.topic: reference
ms.service: service-fabric-mesh
manager: subramar
ms.openlocfilehash: 08e842f5b91bd0ca5f8e8b2a7866f3f9a689ac28
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60811617"
---
# <a name="maven-plugin-for-service-fabric-mesh"></a>Wtyczka maven Plugin for Service Fabric siatki

## <a name="prerequisites"></a>Wymagania wstępne

- Zestaw SDK Java
- Maven
- Wiersza polecenia platformy Azure z rozszerzeniem siatki
- Interfejs wiersza polecenia usługi Service Fabric

## <a name="goals"></a>Cele

### `azure-sfmesh:init`
- Tworzy `servicefabric` folder, który zawiera `appresources` folder, który ma `application.yaml` pliku. 

### `azure-sfmesh:addservice`
- Tworzy folder wewnątrz `servicefabric` folder o nazwie usługi i tworzy plik YAML tej usługi. 

### `azure-sfmesh:addnetwork`
- Generuje `network` YAML o nazwie podanej sieci w `appresources` folderu 

### `azure-sfmesh:addgateway`
- Generuje `gateway` YAML o nazwie podanej bramy w `appresources` folderu 

### `azure-sfmesh:addsecret`
- Generuje `secret` YAML o podanej nazwie klucza tajnego w `appresources` folderu 

### `azure-sfmesh:addsecretvalue`
- Generuje `secretvalue` YAML o nazwie podanej wartości wpisu tajnego i wpisu tajnego w `appresources` folderu 

### `azure-sfmesh:deploy`
- Scala yamls z `servicefabric` folder i tworzy kod JSON szablonu usługi Azure Resource Manager w bieżącym folderze.
- Służy do wdrażania wszystkich zasobów w środowisku usługi Azure Service Fabric siatki 

### `azure-sfmesh:deploytocluster`
- Tworzy folder (`meshDeploy`) zawierającą wdrożenia JSON Smb1sessionsetup wygenerowany na podstawie yamls, które mają zastosowanie w przypadku klastrów usługi Service Fabric
- Służy do wdrażania wszystkich zasobów do klastra usługi Service Fabric
 

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

## <a name="common-configuration"></a>Typowa konfiguracja

Wtyczka Maven nie obsługuje obecnie typowe konfiguracje wtyczki usługi Maven na platformie Azure.

## <a name="how-to"></a>Instrukcje

### <a name="initialize-maven-project-for-azure-service-fabric-mesh"></a>Zainicjuj projekt Maven dla usługi Azure Service Fabric siatki
Uruchom następujące polecenie, aby utworzyć plik YAML zasobów aplikacji.

```cmd
mvn azure-sfmesh:init -DapplicationName=helloworldserver
```

- Tworzy folder o nazwie `servicefabric->appresources` w folderze głównym, który zawiera aplikację o nazwie YAML `app_helloworldserver`

### <a name="add-resource-to-your-application"></a>Dodaj zasób do aplikacji

#### <a name="add-a-new-network-to-your-application"></a>Dodawanie nowej sieci do aplikacji
Uruchom poniższe polecenie, aby utworzyć yaml zasobów sieciowych. 

```cmd
mvn azure-sfmesh:addnetwork -DnetworkName=helloworldservicenetwork -DnetworkAddressPrefix=10.0.0.4/22
```

- Tworzy sieć YAML w folderze `servicefabric->appresources` o nazwie `network_helloworldservicenetwork`

#### <a name="add-a-new-service-to-your-application"></a>Dodaj nową usługę do aplikacji
Uruchom poniższe polecenie, aby utworzyć yaml usługi. 

```cmd
mvn azure-sfmesh:addservice -DapplicationName=helloworldserver -DserviceName=helloworldservice -DimageName=helloworldserver:latest -DlistenerPort=8080 -DnetworkRef=helloworldservicenetwork
```

- Tworzy usługę YAML w folderze `servicefabric->helloworldservice` o nazwie `service_helloworldservice` odwołujący się `helloworldservicenetwork` & `helloworldserver` aplikacji
- Usługa będzie nasłuchiwać na porcie 8080
- Jak przy użyciu usługi ***helloworldserver:latest*** postaci, w jakiej jest obraz kontenera.

#### <a name="add-a-new-gateway-resource-to-your-application"></a>Dodać nowego zasobu bramy aplikacji
Uruchom poniższe polecenie, aby utworzyć yaml zasobów bramy. 

```cmd
mvn azure-sfmesh:addgateway -DapplicationName=helloworldserver -DdestinationNetwork=helloworldservicenetwork -DgatewayName=helloworldgateway -DlistenerName=helloworldserviceListener -DserviceName=helloworldservice -DsourceNetwork=open -DtcpPort=80
```

- Tworzy nową bramę YAML w folderze `servicefabric->appresources` o nazwie `gateway_helloworldgateway`
- Odwołania `helloworldservicelistener` jako odbiornik usługi, który nasłuchuje połączeń z tą bramą. Odwołuje się `helloworldservice` jako usługa, `helloworldservicenetwork` co sieć i `helloworldserver` co aplikacja. 
- Nasłuchuje żądań na porcie 80

#### <a name="add-a-new-volume-to-your-application"></a>Dodawanie nowego woluminu do aplikacji
Uruchom poniższe polecenie, aby utworzyć yaml zasobu woluminu. 

```cmd
mvn azure-sfmesh:addvolume -DvolumeAccountKey=key -DvolumeAccountName=name -DvolumeName=vol1 -DvolumeShareName=share
```

- Tworzy wolumin YAML w folderze `servicefabric->appresources` o nazwie `volume_vol1`
- Ustawia właściwości dla wymaganych parametrów `volumeAccountKey`, i `volumeShareName` tak jak powyżej
- Aby uzyskać więcej informacji na temat sposobu ten wolumin utworzone z następującej, [Wdróż aplikację, korzystając z woluminu plików platformy Azure](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md)

#### <a name="add-a-new-secret-resource-to-your-application"></a>Dodaj nowy zasób wpisu tajnego aplikacji
Uruchom poniższe polecenie, aby utworzyć yaml wpisu tajnego zasobów. 

```cmd
mvn azure-sfmesh:addsecret -DsecretName=secret1
```

- Tworzy YAML wpisu tajnego w folderze `servicefabric->appresources` o nazwie `secret_secret1`
- Aby uzyskać więcej informacji na temat sposobu ten wpis tajny utworzono z następującej, [zarządzania wpisami tajnymi](service-fabric-mesh-howto-manage-secrets.md)

#### <a name="add-a-new-secretvalue-resource-to-your-application"></a>Dodaj nowy zasób secretvalue do aplikacji
Uruchom poniższe polecenie, aby utworzyć yaml zasobów secretvalue. 

```cmd
mvn azure-sfmesh:addsecretvalue -DsecretValue=someVal -DsecretValueName=secret1/v1
```

- Utwórz secretvalue YAML w folderze `servicefabric->appresources` o nazwie `secretvalue_secret1_v1`

### <a name="run-the-application-locally"></a>Uruchamianie aplikacji lokalnie

Za pomocą celem `azure-sfmesh:deploytocluster`, możesz uruchomić aplikację lokalnie przy użyciu poniższego polecenia:

```cmd
mvn azure-sfmesh:deploytocluster
```

Domyślnie ten cel wdrażania zasobów z lokalnym klastrem. Jeśli wdrażasz z lokalnym klastrem, przyjęto założenie, że możesz mieć lokalny klaster usługi Service Fabric działanie. Obecnie obsługiwane tylko na lokalny klaster usługi Service Fabric na potrzeby zasobów [Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

- Tworzy JSON Smb1sessionsetup z yamls, które mają zastosowanie w przypadku klastrów usługi Service Fabric
- Następnie wdraża w punkcie końcowym klastra

### <a name="deploy-application-to-azure-service-fabric-mesh"></a>Wdrażanie aplikacji w usłudze Azure Service Fabric siatki

Za pomocą celem `azure-sfmesh:deploy`, można wdrożyć środowisko siatki usługi Service Fabric, uruchamiając poniższe polecenie:

```cmd
mvn azure-sfmesh:deploy -DresourceGroup=todoapprg -Dlocation=eastus
```

- Tworzy grupę zasobów o nazwie `todoapprg` Jeśli nie istnieje.
- Scalanie YAMLs tworzy kod JSON szablonu usługi Azure Resource Manager. 
- Służy do wdrażania za pomocą pliku JSON do środowiska usługi Azure Service Fabric siatki.