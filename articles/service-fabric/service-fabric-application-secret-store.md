---
title: Magazyn centralnych wpisów tajnych usługi Azure Service Fabric
description: W tym artykule opisano sposób korzystania z magazynu centralnych wpisów tajnych w usłudze Azure Service Fabric.
ms.topic: conceptual
ms.date: 07/25/2019
ms.openlocfilehash: bc6ea6260bf50d5b4f8e294e0a3827426f90bee3
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980937"
---
# <a name="central-secrets-store-in-azure-service-fabric"></a>Magazyn centralnych wpisów tajnych na platformie Azure Service Fabric 
W tym artykule opisano sposób korzystania z magazynu centralnych wpisów tajnych (CSS) w usłudze Azure Service Fabric do tworzenia wpisów tajnych w aplikacjach Service Fabric. CSS to lokalna pamięć podręczna magazynu lokalnego wpisu tajnego, która przechowuje dane poufne, takie jak hasło, tokeny i klucze, szyfrowane w pamięci.

## <a name="enable-central-secrets-store"></a>Włącz magazyn centralnych wpisów tajnych
Dodaj następujący skrypt do konfiguracji klastra w obszarze `fabricSettings`, aby włączyć CSS. Zalecamy użycie certyfikatu innego niż certyfikat klastra dla CSS. Upewnij się, że certyfikat szyfrowania został zainstalowany we wszystkich węzłach, a `NetworkService` ma uprawnienia do odczytu klucza prywatnego certyfikatu.
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
## <a name="declare-a-secret-resource"></a>Deklarowanie tajnego zasobu
Zasób tajny można utworzyć przy użyciu szablonu Azure Resource Manager lub interfejsu API REST.

### <a name="use-resource-manager"></a>Użyj Menedżer zasobów

Użyj następującego szablonu, aby użyć Menedżer zasobów do utworzenia tajnego zasobu. Szablon tworzy `supersecret` tajny zasób, ale nie ustawiono jeszcze żadnej wartości dla zasobu tajnego.


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

### <a name="use-the-rest-api"></a>Używanie interfejsu API REST

Aby utworzyć `supersecret` zasób tajny przy użyciu interfejsu API REST, wprowadź żądanie PUT do `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview`. Do utworzenia tajnego zasobu potrzebny jest certyfikat klastra lub certyfikat klienta administratora.

```powershell
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint>
```

## <a name="set-the-secret-value"></a>Ustaw wartość klucza tajnego

### <a name="use-the-resource-manager-template"></a>Korzystanie z szablonu Menedżer zasobów

Użyj następującego szablonu Menedżer zasobów, aby utworzyć i ustawić wartość klucza tajnego. Ten szablon ustawia wartość wpisu tajnego dla zasobu `supersecret` tajnego jako wersji `ver1`.
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
### <a name="use-the-rest-api"></a>Używanie interfejsu API REST

Użyj poniższego skryptu, aby użyć interfejsu API REST w celu ustawienia wartości klucza tajnego.
```powershell
$Params = @{"properties": {"value": "mysecretpassword"}}
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```
## <a name="use-the-secret-in-your-application"></a>Używanie wpisu tajnego w aplikacji

Wykonaj następujące kroki, aby użyć wpisu tajnego w aplikacji Service Fabric.

1. Dodaj sekcję w pliku **Settings. XML** z następującym fragmentem kodu. Zwróć uwagę, że wartość jest w formacie {`secretname:version`}.

   ```xml
     <Section Name="testsecrets">
      <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
     </Section>
   ```

1. Zaimportuj sekcję w **ApplicationManifest. XML**.
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

   Zmienna środowiskowa `SecretPath` będzie wskazywała katalog, w którym są przechowywane wszystkie wpisy tajne. Każdy parametr wymieniony w sekcji `testsecrets` jest przechowywany w osobnym pliku. Aplikacja może teraz używać klucza tajnego w następujący sposób:
   ```C#
   secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
   ```
1. Zainstaluj wpisy tajne w kontenerze. Jedyną zmianą wymaganą do udostępnienia wpisów tajnych wewnątrz kontenera jest `specify` punktu instalacji w `<ConfigPackage>`.
Poniższy fragment kodu jest zmodyfikowany **ApplicationManifest. XML**.  

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
   Wpisy tajne są dostępne w punkcie instalacji wewnątrz kontenera.

1. Wpis tajny można powiązać ze zmienną środowiskową procesu, określając `Type='SecretsStoreRef`. Poniższy fragment kodu stanowi przykład powiązania `ver1` wersji `supersecret` ze zmienną środowiskową `MySuperSecret` w **pliku servicemanifest. XML**.

   ```xml
   <EnvironmentVariables>
     <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
   </EnvironmentVariables>
   ```

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [zabezpieczeniach aplikacji i usług](service-fabric-application-and-service-security.md).
