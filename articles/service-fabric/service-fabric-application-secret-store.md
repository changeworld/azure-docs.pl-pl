---
title: Centralny magazyn wpisów tajnych sieci szkieletowej usługi Azure
description: W tym artykule opisano sposób korzystania z centralnego magazynu wpisów tajnych w sieci szkieletowej usługi Azure.
ms.topic: conceptual
ms.date: 07/25/2019
ms.openlocfilehash: 11fb94a9fba40e6f2474ad64f5eb0c454be28ca0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589168"
---
# <a name="central-secrets-store-in-azure-service-fabric"></a>Centralny magazyn wpisów tajnych w sieci szkieletowej usług Azure 
W tym artykule opisano sposób używania centralnego magazynu wpisów tajnych (CSS) w sieci szkieletowej usługi Azure do tworzenia wpisów tajnych w aplikacjach sieci szkieletowej usług. CSS to lokalna pamięć podręczna magazynu tajne, która przechowuje poufne dane, takie jak hasło, tokeny i klucze, zaszyfrowane w pamięci.

## <a name="enable-central-secrets-store"></a>Włącz centralny magazyn tajemnic
Dodaj następujący skrypt do konfiguracji `fabricSettings` klastra w obszarze, aby włączyć CSS. Zaleca się używanie certyfikatu innego niż certyfikat klastra dla CSS. Upewnij się, że certyfikat szyfrowania jest `NetworkService` zainstalowany we wszystkich węzłach i który ma uprawnienia do odczytu klucza prywatnego certyfikatu.
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
Tajny zasób można utworzyć przy użyciu szablonu usługi Azure Resource Manager lub interfejsu API REST.

### <a name="use-resource-manager"></a>Korzystanie z Menedżera zasobów

Użyj następującego szablonu, aby utworzyć tajny zasób za pomocą Menedżera zasobów. Szablon tworzy `supersecret` tajny zasób, ale nie ma jeszcze ustawionej wartości tajnego zasobu.


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

Aby utworzyć `supersecret` tajny zasób przy użyciu interfejsu `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview`API REST, należy wykonać żądanie PUT na program . Do utworzenia tajnego zasobu potrzebny jest certyfikat klastra lub certyfikat klienta administratora.

```powershell
$json = '{"properties": {"kind": "inlinedValue", "contentType": "text/plain", "description": "supersecret"}}'
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint> -Body $json
```

## <a name="set-the-secret-value"></a>Ustawianie wartości tajnej

### <a name="use-the-resource-manager-template"></a>Korzystanie z szablonu Menedżera zasobów

Użyj następującego szablonu Menedżera zasobów, aby utworzyć i ustawić wartość tajną. Ten szablon ustawia wartość `supersecret` tajnego zasobu tajnego jako wersję `ver1`.
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

Użyj następującego skryptu, aby ustawić wartość tajną za pomocą interfejsu API REST.
```powershell
$Params = '{"properties": {"value": "mysecretpassword"}}'
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```
### <a name="examine-the-secret-value"></a>Sprawdź wartość tajną
```powershell
Invoke-WebRequest -CertificateThumbprint <ClusterCertThumbprint> -Method POST -Uri "https:<clusterfqdn>/Resources/Secrets/supersecret/values/ver1/list_value?api-version=6.4-preview"
```
## <a name="use-the-secret-in-your-application"></a>Użyj klucza tajnego w aplikacji

Wykonaj następujące kroki, aby użyć klucza tajnego w aplikacji sieci szkieletowej usług.

1. Dodaj sekcję w pliku **settings.xml** z następującym fragmentem kodu. Należy zauważyć, że wartość jest`secretname:version`w formacie { }.

   ```xml
     <Section Name="testsecrets">
      <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
     </Section>
   ```

1. Zaimportuj sekcję w **pliku ApplicationManifest.xml**.
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

   Zmienna `SecretPath` środowiskowa wskaż katalog, w którym są przechowywane wszystkie wpisy tajne. Każdy parametr wymieniony `testsecrets` w sekcji jest przechowywany w osobnym pliku. Aplikacja może teraz używać klucza tajnego w następujący sposób:
   ```C#
   secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
   ```
1. Zamontuj wpisy tajne do kontenera. Jedyną zmianą wymaganą do udostępnienia wpisów tajnych wewnątrz kontenera jest `specify` punkt instalacji w `<ConfigPackage>`.
Poniższy fragment kodu to zmodyfikowany **plik ApplicationManifest.xml**.  

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

1. Klucz tajny można powiązać ze zmienną `Type='SecretsStoreRef`środowiskową procesu, określając program . Poniższy fragment kodu jest przykładem powiązania `supersecret` wersji `ver1` ze zmienną `MySuperSecret` środowiskową w pliku **ServiceManifest.xml**.

   ```xml
   <EnvironmentVariables>
     <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
   </EnvironmentVariables>
   ```

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [zabezpieczeniach aplikacji i usług](service-fabric-application-and-service-security.md).
