---
title: Konfigurowanie certyfikatów dla aplikacji usługi Azure Service Fabric w systemie Linux | Dokumentacja firmy Microsoft
description: Konfigurowanie certyfikatów dla aplikacji ze środowiskiem uruchomieniowym usługi Service Fabric w klastrze systemu Linux
services: service-fabric
documentationcenter: NA
author: JimacoMS2
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/26/2018
ms.author: v-jamebr
ms.openlocfilehash: c0580b75544a9613bc8caf2faaac11ba1ba6708e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60881372"
---
# <a name="certificates-and-security-on-linux-clusters"></a>Certyfikaty i zabezpieczenia w klastrach systemu Linux

Ten artykuł zawiera informacje o konfigurowaniu certyfikatów X.509 w klastrach systemu Linux.

## <a name="location-and-format-of-x509-certificates-on-linux-nodes"></a>Lokalizacja i format certyfikatów X.509 w węzłach systemu Linux

Usługa Service Fabric jest ogólnie oczekuje certyfikatów X.509, znajdować się w */var/lib/sfcerts* katalogu w węzłach klastra systemu Linux. Ta zasada obowiązuje certyfikatów klastra, certyfikaty klienta itd. W niektórych przypadkach można określić lokalizacji innych niż *var/lib/sfcerts* folderu certyfikatów. Na przykład przy użyciu usług Reliable Services utworzone przy użyciu zestawu SDK usługi Service Fabric Java, możesz określić inną lokalizację przy użyciu pakietu konfiguracji (Settings.xml) w przypadku niektórych certyfikatów specyficznych dla aplikacji. Aby dowiedzieć się więcej, zobacz [certyfikatów, do którego odwołuje się pakiet konfiguracji (Settings.xml)](#certificates-referenced-in-the-configuration-package-settingsxml).

W przypadku klastrów systemu Linux usługi Service Fabric oczekuje, że aby certyfikaty były obecne, albo w formacie PEM zawierający certyfikat i klucz prywatny lub jako plik .crt, który zawiera certyfikat oraz plik Key, który zawiera klucz prywatny. Wszystkie pliki powinny być w formacie PEM. 

W przypadku instalacji certyfikatu z usługi Azure Key Vault przy użyciu [szablonu usługi Resource Manager](./service-fabric-cluster-creation-create-template.md) lub [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/?view=latest#service_fabric) poleceń, certyfikat został zainstalowany w poprawnym formacie w */var/ lib/sfcerts* katalogu w każdym węźle. Po zainstalowaniu certyfikatu za pomocą innej metody, upewnij się, że certyfikat został poprawnie zainstalowany na węzłach klastra.

## <a name="certificates-referenced-in-the-application-manifest"></a>Certyfikaty, do którego odwołuje się manifest aplikacji

Certyfikat określony w aplikacji manifestu, na przykład za pośrednictwem [ **SecretsCertificate** ](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#secretscertificate-element) lub [ **EndpointCertificate** ](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#endpointcertificate-element)elementów, musi znajdować się w */var/lib/sfcerts* katalogu. Elementy, które są używane do określania certyfikaty w manifeście aplikacji nie mają atrybutu ścieżki, więc certyfikaty muszą być obecne w domyślnym katalogu. Te elementy, wykonaj opcjonalne **X509StoreName** atrybutu. Wartość domyślna to "Mój", która wskazuje na */var/lib/sfcerts* katalogu węzłów systemu Linux. Dowolna inna wartość jest niezdefiniowana w klastrze systemu Linux. Firma Microsoft zaleca, aby pominąć **X509StoreName** atrybutu dla aplikacji, które działały w klastrach Linux. 

## <a name="certificates-referenced-in-the-configuration-package-settingsxml"></a>Certyfikaty, do którego odwołuje się pakiet konfiguracji (Settings.xml)

W przypadku niektórych usług można skonfigurować certyfikaty X.509 [ConfigPackage](./service-fabric-application-and-service-manifests.md) (domyślnie Settings.xml). Na przykład jest tak w przypadku deklarowania certyfikatów służących do zabezpieczania kanały RPC dla usług Reliable Services utworzonych za pomocą usługi Service Fabric platformy .NET Core lub zestawów SDK języka Java. Istnieją dwa sposoby odwołania certyfikatów w pakiecie konfiguracji. Obsługa waha się między .NET Core i Java SDK.

### <a name="using-x509-securitycredentialstype"></a>Using X509 SecurityCredentialsType

Za pomocą zestawów SDK języka Java lub .NET można określić **X509** dla **SecurityCredentialsType**. Odpowiada to `X509Credentials` ([.NET](https://msdn.microsoft.com/library/system.fabric.x509credentials.aspx)/[Java](https://docs.microsoft.com/java/api/system.fabric.x509credentials)) typ `SecurityCredentials` ([.NET](https://msdn.microsoft.com/library/system.fabric.securitycredentials.aspx)/[językaJava](https://docs.microsoft.com/java/api/system.fabric.securitycredentials)).

**X509** odwołania lokalizuje certyfikat w magazynie certyfikatów. Następujący kody XML pokazuje parametry można określić lokalizację certyfikatu:

```xml
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateStoreName" Value="My" />
```

Usługi uruchomione w systemie Linux **LocalMachine**/**Moje** wskazuje domyślną lokalizację dla certyfikatów, */var/lib/sfcerts* katalogu. Dla systemu Linux, wszelkie inne kombinacje **CertificateStoreLocation** i **CertificateStoreName** są niezdefiniowane. 

Zawsze określać **LocalMachine** dla **CertificateStoreLocation** parametru. Nie ma konieczności określania **CertificateStoreName** parametru, ponieważ jego wartość domyślna to "Moje". Za pomocą **X509** odwołania, pliki certyfikatu musi znajdować się w */var/lib/sfcerts* katalogu w węźle klastra.  

Pokazano w poniższym XML **TransportSettings** sekcję na podstawie tego stylu:

```xml
<Section Name="HelloWorldStatefulTransportSettings">
    <Parameter Name="MaxMessageSize" Value="10000000" />
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
    <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
    <Parameter Name="CertificateRemoteThumbprints" Value="9FEF3950642138446CC364A396E1E881DB76B483" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
    <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
</Section>
```

### <a name="using-x5092-securitycredentialstype"></a>Using X509_2 SecurityCredentialsType

Przy użyciu zestawu SDK języka Java, możesz określić **X509_2** dla **SecurityCredentialsType**. Odpowiada to `X509Credentials2` ([Java](https://docs.microsoft.com/java/api/system.fabric.x509credentials2)) typ `SecurityCredentials` ([Java](https://docs.microsoft.com/java/api/system.fabric.securitycredentials)). 

Za pomocą **X509_2** odwołania, określisz parametr ścieżki, aby mogły zlokalizować certyfikatu w katalogu innej niż */var/lib/sfcerts*.  Następujący kody XML pokazuje parametry można określić lokalizację certyfikatu: 

```xml
     <Parameter Name="SecurityCredentialsType" Value="X509_2" />
     <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
```

Pokazano w poniższym XML **TransportSettings** sekcję na podstawie tego stylu.

```xml
<!--Section name should always end with "TransportSettings".-->
<!--Here we are using a prefix "HelloWorldStateless".-->
<Section Name="HelloWorldStatelessTransportSettings">
    <Parameter Name="MaxMessageSize" Value="10000000" />
    <Parameter Name="SecurityCredentialsType" Value="X509_2" />
    <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
    <Parameter Name="CertificateProtectionLevel" Value="EncryptandSign" />
    <Parameter Name="CertificateRemoteThumbprints" Value="BD1C71E248B8C6834C151174DECDBDC02DE1D954" />
</Section>
```

> [!NOTE]
> Certyfikat jest określony jako plik CRT na poprzedni kod XML. Oznacza to, że również jest plikiem Key, zawierający klucz prywatny w tej samej lokalizacji.

## <a name="configure-a-reliable-services-app-to-run-on-linux-clusters"></a>Skonfiguruj aplikację usług Reliable Services, aby działały w klastrach systemu Linux

Zestawy SDK usługi Service Fabric umożliwiają komunikowanie się ze środowiskiem uruchomieniowym usługi Service Fabric interfejsów API, aby korzystać z platformy. Po uruchomieniu dowolnej aplikacji, która korzysta z tej funkcji na bezpieczne klastry systemu Linux, musisz skonfigurować aplikację za pomocą certyfikatu, którego można używać do sprawdzania poprawności w środowisku uruchomieniowym usługi Service Fabric. Aplikacje, które zawierają niezawodnej usługi Service Fabric napisane przy użyciu zestawów SDK języka Java lub .NET Core wymagają tej konfiguracji. 

Aby skonfigurować aplikację, należy dodać [ **SecretsCertificate** ](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#secretscertificate-element) pod **certyfikaty** znacznik, który znajduje się w folderze **ApplicationManifest**  tagów w *ApplicationManifest.xml* pliku. Następujący kody XML pokazuje odwołuje się jego odcisk palca certyfikatu: 

```xml
   <Certificates>
       <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="0A00AA0AAAA0AAA00A000000A0AA00A0AAAA00" />
   </Certificates>   
```

Można się odwołać certyfikatu klastra lub certyfikat, który należy zainstalować na każdym węźle klastra. W systemie Linux, musi znajdować się w nim certyfikat */var/lib/sfcerts* katalogu. Aby dowiedzieć się więcej, zobacz [lokalizacji i format certyfikatów X.509 w węzłach systemu Linux](#location-and-format-of-x509-certificates-on-linux-nodes).



