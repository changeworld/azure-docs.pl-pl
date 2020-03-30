---
title: Konfigurowanie certyfikatów dla aplikacji w systemie Linux
description: Konfigurowanie certyfikatów dla aplikacji za pomocą środowiska uruchomieniowego sieci szkieletowej usług w klastrze systemu Linux
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: pepogors
ms.openlocfilehash: 802e76614f51e1f6479a311e61a49d83b8125546
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282577"
---
# <a name="certificates-and-security-on-linux-clusters"></a>Certyfikaty i zabezpieczenia w klastrach systemu Linux

Ten artykuł zawiera informacje dotyczące konfigurowania certyfikatów X.509 w klastrach systemu Linux.

## <a name="location-and-format-of-x509-certificates-on-linux-nodes"></a>Lokalizacja i format certyfikatów X.509 w węzłach Systemu Linux

Usługa Service Fabric zazwyczaj oczekuje, że certyfikaty X.509 będą obecne w katalogu */var/lib/sfcerts* w węzłach klastra systemu Linux. Dotyczy to certyfikatów klastra, certyfikatów klientów itp. W niektórych przypadkach można określić lokalizację inną niż folder *var/lib/sfcerts* dla certyfikatów. Na przykład za pomocą niezawodnych usług zbudowanych przy użyciu zestawu SDK Java sieci szkieletowej usług można określić inną lokalizację za pośrednictwem pakietu konfiguracyjnego (Settings.xml) dla niektórych certyfikatów specyficznych dla aplikacji. Aby dowiedzieć się więcej, zobacz [Certyfikaty, do których odwołuje się pakiet konfiguracyjny (Settings.xml)](#certificates-referenced-in-the-configuration-package-settingsxml).

W przypadku klastrów systemu Linux sieć szkieletowa usług oczekuje, że certyfikaty będą obecne jako plik pem zawierający zarówno certyfikat, jak i klucz prywatny lub jako plik crt zawierający certyfikat i plik .key zawierający klucz prywatny. Wszystkie pliki powinny być w formacie PEM. 

Jeśli certyfikat zostanie zainstalowany z usługi Azure Key Vault przy użyciu [szablonu Menedżera zasobów](./service-fabric-cluster-creation-create-template.md) lub poleceń [programu PowerShell,](https://docs.microsoft.com/powershell/module/az.servicefabric/?view=azps-2.6.0) certyfikat jest instalowany w poprawnym formacie w katalogu */var/lib/sfcerts* w każdym węźle. W przypadku instalowania certyfikatu za pomocą innej metody należy upewnić się, że certyfikat jest poprawnie zainstalowany w węzłach klastra.

## <a name="certificates-referenced-in-the-application-manifest"></a>Certyfikaty, do których odwołuje się manifest aplikacji

Certyfikaty określone w manifeście aplikacji, na przykład za pośrednictwem [**SecretsCertificate**](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#secretscertificate-element) lub [**EndpointCertificate**](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#endpointcertificate-element) elementów, muszą być obecne w *katalogu /var/lib/sfcerts.* Elementy, które są używane do określania certyfikatów w manifeście aplikacji nie przyjmują atrybutu ścieżki, więc certyfikaty muszą być obecne w katalogu domyślnym. Te elementy mają opcjonalny atrybut **X509StoreName.** Wartość domyślna to "My", która wskazuje katalog */var/lib/sfcerts* w węzłach systemu Linux. Każda inna wartość jest niezdefiniowana w klastrze linuksa. Zaleca się pominięcie atrybutu **X509StoreName** dla aplikacji uruchamianych w klastrach systemu Linux. 

## <a name="certificates-referenced-in-the-configuration-package-settingsxml"></a>Certyfikaty, do których odwołuje się pakiet konfiguracyjny (Settings.xml)

W przypadku niektórych usług certyfikaty X.509 można skonfigurować w [pliku ConfigPackage](./service-fabric-application-and-service-manifests.md) (domyślnie w pliku Settings.xml). Na przykład jest to przypadek, gdy deklarujesz certyfikaty używane do zabezpieczania kanałów RPC dla usług niezawodnych usług utworzonych za pomocą sieci szkieletowej .NET Core lub java SDK. Istnieją dwa sposoby odwoływania się do certyfikatów w pakiecie konfiguracyjnym. Obsługa różni się między .NET Core i Java SDK.

### <a name="using-x509-securitycredentialstype"></a>Korzystanie z programu X509 SecurityCredentialsType

WIth .NET lub Java SDK, można określić **X509** dla **SecurityCredentialsType**. Odpowiada to `X509Credentials` typowi ( .NET `SecurityCredentials` [Java](https://docs.microsoft.com/java/api/system.fabric.x509credentials)([.NET](https://msdn.microsoft.com/library/system.fabric.securitycredentials.aspx)/[.NET](https://msdn.microsoft.com/library/system.fabric.x509credentials.aspx)/[Java](https://docs.microsoft.com/java/api/system.fabric.securitycredentials)).

Odwołanie **X509** lokalizuje certyfikat w magazynie certyfikatów. Następujący kod XML przedstawia parametry używane do określania lokalizacji certyfikatu:

```xml
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateStoreName" Value="My" />
```

W przypadku usługi uruchomionej w systemie Linux **LocalMachine**/**My** wskazuje domyślną lokalizację certyfikatów, katalog */var/lib/sfcerts.* W przypadku systemu Linux wszelkie inne kombinacje **CertificateStoreLocation** i **CertificateStoreName** są niezdefiniowane. 

Zawsze określ **LocalMachine** dla **parametru CertificateStoreLocation.** Nie ma potrzeby określania **parametru CertificateStoreName,** ponieważ domyślnie jest on "Mój". W odwołaniu **do x509** pliki certyfikatów muszą znajdować się w katalogu */var/lib/sfcerts* w węźle klastra.  

Na podstawie następującego stylu w poniższym formacie XML przedstawiono sekcję **TransportSettings:**

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

### <a name="using-x509_2-securitycredentialstype"></a>Korzystanie z X509_2 SecurityCredentialsType

Za pomocą sdk Java można określić **X509_2** dla **securityCredentialsType**. Odpowiada `X509Credentials2` to typowi[Java](https://docs.microsoft.com/java/api/system.fabric.x509credentials2) `SecurityCredentials` ( Java ([Java](https://docs.microsoft.com/java/api/system.fabric.securitycredentials)). 

Za pomocą odwołania **X509_2** należy określić parametr ścieżki, dzięki czemu można zlokalizować certyfikat w katalogu innym niż */var/lib/sfcerts*.  Następujący kod XML przedstawia parametry używane do określania lokalizacji certyfikatu: 

```xml
     <Parameter Name="SecurityCredentialsType" Value="X509_2" />
     <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
```

Poniższy kod XML przedstawia **sekcję TransportSettings** na podstawie tego stylu.

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
> Certyfikat jest określony jako plik crt w poprzednim pliku XML. Oznacza to, że istnieje również plik .key zawierający klucz prywatny w tej samej lokalizacji.

## <a name="configure-a-reliable-services-app-to-run-on-linux-clusters"></a>Konfigurowanie aplikacji niezawodne usługi do uruchamiania w klastrach systemu Linux

SDK sieci szkieletowej usług umożliwiają komunikowanie się z interfejsami API środowiska wykonawczego sieci szkieletowej usług w celu wykorzystania platformy. Po uruchomieniu dowolnej aplikacji, która używa tej funkcji w bezpiecznych klastrów systemu Linux, należy skonfigurować aplikację z certyfikatem, który może służyć do sprawdzania poprawności za pomocą środowiska uruchomieniowego sieci szkieletowej usług. Aplikacje, które zawierają usługi niezawodnej usługi sieci szkieletowej usług napisane przy użyciu .NET Core lub Java SDK wymagają tej konfiguracji. 

Aby skonfigurować aplikację, dodaj [**SecretsCertificate**](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#secretscertificate-element) element pod **certyfikaty** tag, który znajduje się w **obszarze ApplicationManifest** tag w *pliku ApplicationManifest.xml.* Następujący kod XML przedstawia certyfikat, do którego odwołuje się odcisk palca: 

```xml
   <Certificates>
       <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="0A00AA0AAAA0AAA00A000000A0AA00A0AAAA00" />
   </Certificates>   
```

Można odwoływać się do certyfikatu klastra lub certyfikatu zainstalowanego w każdym węźle klastra. W systemie Linux pliki certyfikatów muszą znajdować się w katalogu */var/lib/sfcerts.* Aby dowiedzieć się więcej, zobacz [Lokalizacja i format certyfikatów X.509 w węzłach systemu Linux](#location-and-format-of-x509-certificates-on-linux-nodes).



