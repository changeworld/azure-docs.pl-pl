---
title: Magazyn danych tajnych Service Fabric
description: W tym artykule opisano sposób korzystania z magazynu wpisów tajnych Service Fabric.
ms.topic: conceptual
ms.date: 07/25/2019
ms.openlocfilehash: 16608d9eaf12fc9abc535ef316d7b5e8b74a8b37
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75457506"
---
#  <a name="service-fabric-secrets-store"></a>Magazyn danych tajnych Service Fabric
W tym artykule opisano sposób tworzenia i używania wpisów tajnych w aplikacjach Service Fabric przy użyciu magazynu Service Fabric Secret (CSS). CSS to lokalna pamięć podręczna magazynu lokalnych wpisów tajnych, używana do przechowywania poufnych danych, takich jak hasło, tokeny i klucze szyfrowane w pamięci.

## <a name="enabling-secrets-store"></a>Włączanie magazynu wpisów tajnych
 Dodaj poniższy kod do konfiguracji klastra w obszarze `fabricSettings`, aby włączyć CSS. Zalecane jest użycie certyfikatu innego niż certyfikat klastra dla CSS. Upewnij się, że certyfikat szyfrowania został zainstalowany we wszystkich węzłach, a `NetworkService` ma uprawnienia do odczytu klucza prywatnego certyfikatu.
  ```json
    "fabricSettings": 
    [
        ...
    {
        "name":  "CentralSecretService",
        "parameters":  [
                {
                    "name":  "IsEnabled",
                    "value":  "true"
                },
                {
                    "name":  "MinReplicaSetSize",
                    "value":  "3"
                },
                {
                    "name":  "TargetReplicaSetSize",
                    "value":  "3"
                },
                 {
                    "name" : "EncryptionCertificateThumbprint",
                    "value": "<thumbprint>"
                 }
                ,
                ],
            },
            ]
     }
        ...
     ]
```
## <a name="declare-secret-resource"></a>Zadeklaruj zasób tajny
Zasób tajny można utworzyć przy użyciu szablonu Menedżer zasobów lub interfejsu API REST.

* Korzystanie z szablonu usługi Resource Manager
```json
   "resources": [
      {
        "apiVersion": "2018-07-01-preview",
        "name": "supersecret",
        "type": "Microsoft.ServiceFabricMesh/secrets",
        "location": "[parameters('location')]", 
        "dependsOn": [],
        "properties": {
          "kind": "inlinedValue",
            "description": "Application Secret",
            "contentType": "text/plain",
          }
        }
      ]
```
Powyższy szablon tworzy `supersecret` tajny zasób, ale nie ustawiono jeszcze żadnej wartości dla zasobu tajnego.

* Korzystanie z interfejsu API REST

Aby utworzyć zasób tajny, `supersecret` wprowadź żądanie PUT `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview`. Do utworzenia wpisu tajnego potrzebny jest certyfikat klastra lub administrator.

```powershell
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint>
```

## <a name="set-secret-value"></a>Ustaw wartość klucza tajnego
* Korzystanie z szablonu usługi Resource Manager

Poniższy szablon Menedżer zasobów tworzy i ustawia wartość wpisu tajnego `supersecret` z wersją `ver1`.
```json
  {
  "parameters": {
  "supersecret": {
      "type": "string",
      "metadata": {
        "description": "supersecret value"
      }
   }
  },
  "resources": [
    {
      "apiVersion": "2018-07-01-preview",
        "name": "supersecret",
        "type": "Microsoft.ServiceFabricMesh/secrets",
        "location": "[parameters('location')]", 
        "dependsOn": [],
        "properties": {
          "kind": "inlinedValue",
            "description": "Application Secret",
            "contentType": "text/plain",
        }
    },
    {
      "apiVersion": "2018-07-01-preview",
      "name": "supersecret/ver1",
      "type": "Microsoft.ServiceFabricMesh/secrets/values",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/secrets/supersecret"
      ],
      "properties": {
        "value": "[parameters('supersecret')]"
      }
    }
  ],
  ```
* Korzystanie z interfejsu API REST

```powershell
$Params = @{"properties": {"value": "mysecretpassword"}}
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```
## <a name="using-the-secret-in-your-application"></a>Używanie wpisu tajnego w aplikacji

1.  Dodaj sekcję w pliku Settings. XML z poniższą zawartością. Zwróć uwagę, że wartość jest w formacie {`secretname:version`}

```xml
  <Section Name="testsecrets">
   <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
  </Section>
```
2. Teraz zaimportuj sekcję w ApplicationManifest. XML
```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="testservicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ConfigPackagePolicies CodePackageRef="Code">
        <ConfigPackage Name="Config" SectionName="testsecrets" EnvironmentVariableName="SecretPath" />
        </ConfigPackagePolicies>
    </Policies>
  </ServiceManifestImport>
```

Zmienna środowiskowa "SecretPath" wskazuje katalog, w którym są przechowywane wszystkie wpisy tajne. Każdy parametr wymieniony w sekcji `testsecrets` zostanie zapisany w osobnym pliku. Aplikacja może teraz używać wpisu tajnego, jak pokazano poniżej
```C#
secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
```
3. Instalowanie wpisów tajnych w kontenerze

Tylko zmiana wymagana do udostępnienia wpisów tajnych wewnątrz kontenera polega na określeniu MountPoint w `<ConfigPackage>`.
Oto zmodyfikowana ApplicationManifest. XML  

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="testservicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ConfigPackagePolicies CodePackageRef="Code">
        <ConfigPackage Name="Config" SectionName="testsecrets" MountPoint="C:\secrets" EnvironmentVariableName="SecretPath" />
        <!-- Linux Container
         <ConfigPackage Name="Config" SectionName="testsecrets" MountPoint="/mnt/secrets" EnvironmentVariableName="SecretPath" />
        -->
      </ConfigPackagePolicies>
    </Policies>
  </ServiceManifestImport>
```
Wpisy tajne będą dostępne w punkcie instalacji wewnątrz kontenera.

4. Powiązanie klucza tajnego ze zmienną środowiskową 

Można powiązać klucz tajny ze zmienną środowiskową procesu przez określenie typu Type = "SecretsStoreRef". Poniżej przedstawiono przykład powiązania `supersecret` wersji `ver1` ze zmienną środowiskową `MySuperSecret` pliku servicemanifest. XML.

```xml
<EnvironmentVariables>
  <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
</EnvironmentVariables>
```
## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [zabezpieczeniach aplikacji i usługi](service-fabric-application-and-service-security.md)
