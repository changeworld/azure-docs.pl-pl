---
title: Konfigurowanie certyfikatów dla aplikacji sieci szkieletowej usług Azure w systemie Linux | Dokumentacja firmy Microsoft
description: Konfigurowanie certyfikatów dla aplikacji ze środowiskiem uruchomieniowym usługi sieć szkieletowa w klastrze systemu Linux
services: service-fabric
documentationcenter: NA
author: JimacoMS2
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/26/2018
ms.author: v-jamebr
ms.openlocfilehash: 2d6d387ed12e7261d09669686c0710786a4302dd
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37025813"
---
# <a name="certificates-and-security-on-linux-clusters"></a>Certyfikaty i zabezpieczeń w systemie Linux klastrów

Ten artykuł zawiera informacje o konfigurowaniu certyfikatów X.509 na klastry z systemem Linux.

## <a name="location-and-format-of-x509-certificates-on-linux-nodes"></a>Lokalizacja i format certyfikatów X.509 w węzłach systemu Linux

Sieć szkieletowa usług zazwyczaj oczekuje certyfikatów X.509 znajdować się w */var/lib/sfcerts* katalogu w węzłach klastra w systemie Linux. Dotyczy to certyfikatów klastra certyfikaty klienta, itp. W niektórych przypadkach można określić lokalizacji innych niż *var/lib/sfcerts* folderu certyfikatów. Na przykład z usługami Reliable Services utworzony za pomocą zestawu Java SDK usługi sieci szkieletowej, można określić inną lokalizację za pomocą pakietu konfiguracji (Settings.xml) dla niektórych certyfikatów specyficzne dla aplikacji. Aby dowiedzieć się więcej, zobacz [certyfikatów, do którego odwołuje się pakiet konfiguracji (Settings.xml)](#certificates-referenced-in-the-configuration-package-settingsxml).

W przypadku klastrów systemu Linux platformy Service Fabric oczekuje certyfikaty istnieć jako albo plik PEM, który zawiera certyfikat i klucz prywatny lub plik .crt, który zawiera certyfikat oraz pliku .key, który zawiera klucz prywatny. Wszystkie pliki powinna mieć format PEM. 

W przypadku instalacji certyfikatu z usługi Azure Key Vault za pomocą [szablonu usługi Resource Manager](./service-fabric-cluster-creation-via-arm.md#create-a-service-fabric-cluster-resource-manager-template) lub [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/?view=latest#service_fabric) polecenia certyfikat został zainstalowany w poprawnym formacie w */var/ lib/sfcerts* katalogu w każdym węźle. Po zainstalowaniu certyfikatu za pomocą innej metody, należy się upewnić, że certyfikat został poprawnie zainstalowany na węzłach klastra.

## <a name="certificates-referenced-in-the-application-manifest"></a>Certyfikaty, do którego odwołuje się manifest aplikacji

Certyfikatami określonymi w aplikacji manifestu, na przykład za pomocą [ **element** ](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#secretscertificate-element) lub [ **EndpointCertificate** ](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-service-model-schema-elements#endpointcertificate-element)elementów, musi znajdować się w */var/lib/sfcerts* katalogu. Elementy, które są używane, aby określić certyfikaty w manifeście aplikacji nie mają atrybut ścieżki, więc certyfikaty muszą znajdować się w domyślnym katalogiem. Te elementy zająć opcjonalny **X509StoreName** atrybutu. Wartość domyślna to "My", która wskazuje na */var/lib/sfcerts* katalogu w węzłach systemu Linux. Dowolna inna wartość jest niezdefiniowana na klastrze z systemem Linux. Firma Microsoft zaleca, aby pominąć **X509StoreName** atrybutu dla aplikacji uruchamianych w systemie Linux klastrów. 

## <a name="certificates-referenced-in-the-configuration-package-settingsxml"></a>Certyfikaty, do którego odwołuje się pakiet konfiguracji (Settings.xml)

W przypadku niektórych usług można skonfigurować certyfikatów X.509 w [lokalizacji ConfigPackage](./service-fabric-application-and-service-manifests.md) (domyślnie Settings.xml). Na przykład dotyczy to przy deklarowaniu certyfikatów służących do zabezpieczania kanały RPC dla usługi niezawodne usługi utworzone przy użyciu usługi sieci szkieletowej .NET Core lub Java SDK. Istnieją dwa sposoby odwołania certyfikatów w pakiet konfiguracji. Obsługa różnie .NET Core i zestawy SDK Java.

### <a name="using-x509-securitycredentialstype"></a>Przy użyciu X509 SecurityCredentialsType

.NET lub zestawów SDK Java, można określić **X509** dla **SecurityCredentialsType**. Odpowiada to `X509Credentials` ([.NET](https://msdn.microsoft.com/library/system.fabric.x509credentials.aspx)/[Java](https://docs.microsoft.com/java/api/system.fabric._x509_credentials)) typu `SecurityCredentials` ([.NET](https://msdn.microsoft.com/library/system.fabric.securitycredentials.aspx)/[Java](https://docs.microsoft.com/java/api/system.fabric._security_credentials)).

**X509** odwołania lokalizuje certyfikat w magazynie certyfikatów. Następujący kod XML zawiera parametry można określić lokalizację certyfikatu:

```xml
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateStoreName" Value="My" />
```

Usługi uruchomione w systemie Linux **LocalMachine**/**Moje** wskazuje domyślną lokalizację certyfikatów, */var/lib/sfcerts* katalogu. Dla systemu Linux, wszelkie inne kombinacje **CertificateStoreLocation** i **CertificateStoreName** jest nieokreślona. 

Określ zawsze **LocalMachine** dla **CertificateStoreLocation** parametru. Nie istnieje potrzeba do określenia **CertificateStoreName** parametru ponieważ domyślne "Moje". Z **X509** odwołania, plik certyfikatu musi się znajdować w */var/lib/sfcerts* katalogu w węźle klastra.  

Poniżej przedstawiono XML **TransportSettings** sekcji na podstawie tego stylu:

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

### <a name="using-x5092-securitycredentialstype"></a>Przy użyciu X509_2 SecurityCredentialsType

Przy użyciu zestawu SDK Java, można określić **X509_2** dla **SecurityCredentialsType**. Odpowiada to `X509Credentials2` ([Java](https://docs.microsoft.com/java/api/system.fabric._x509_credentials2)) typu `SecurityCredentials` ([Java](https://docs.microsoft.com/java/api/system.fabric._security_credentials)). 

Z **X509_2** odwołanie, należy określić parametr path, aby mogły zlokalizować certyfikatu w katalogu innym niż */var/lib/sfcerts*.  Następujący kod XML zawiera parametry można określić lokalizację certyfikatu: 

```xml
     <Parameter Name="SecurityCredentialsType" Value="X509_2" />
     <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
```

Poniżej przedstawiono XML **TransportSettings** sekcji oparte na tym stylem.

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
> Certyfikat jest określony jako plik .crt w poprzednim XML. Oznacza to, że jest również plik .key zawierający klucz prywatny w tej samej lokalizacji.

## <a name="configure-a-reliable-services-app-to-run-on-linux-clusters"></a>Skonfiguruj aplikację niezawodne usługi, do uruchamiania na klastry z systemem Linux

Zestawy SDK sieci szkieletowej usług umożliwiają komunikowanie się ze środowiskiem uruchomieniowym usługi sieć szkieletowa interfejsów API, aby korzystać z platformy. Po uruchomieniu dowolnej aplikacji, która korzysta z tej funkcji w bezpiecznych klastrów systemu Linux, musisz skonfigurować aplikację przy użyciu certyfikatu, którego można użyć do zweryfikowania ze środowiskiem uruchomieniowym usługi sieć szkieletowa usług. Aplikacje, które zawierają usługi Service Fabric niezawodnej usługi napisane przy użyciu platformy .NET Core i zestawy SDK Java wymaga tej konfiguracji. 

Aby skonfigurować aplikację, należy dodać [ **element** ](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#secretscertificate-element) pod **certyfikaty** tagu, który znajduje się w obszarze **ApplicationManifest**  tagów w *ApplicationManifest.xml* pliku. Następujący kod XML zawiera odwołuje się jego odcisk palca certyfikatu: 

```xml
   <Certificates>
       <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="0A00AA0AAAA0AAA00A000000A0AA00A0AAAA00" />
   </Certificates>   
```

Możesz odwoływać się certyfikatu klastra lub certyfikat, który należy zainstalować na każdym węźle klastra. W systemie Linux, plik certyfikatu musi być obecny w */var/lib/sfcerts* katalogu. Aby dowiedzieć się więcej, zobacz [lokalizacji i format certyfikatów X.509 w węzłach Linux](#location-and-format-of-x509-certificates-on-linux-nodes).



