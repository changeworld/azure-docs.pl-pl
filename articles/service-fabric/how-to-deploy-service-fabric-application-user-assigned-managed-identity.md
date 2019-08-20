---
title: Azure Service Fabric — wdrażanie aplikacji przy użyciu tożsamości zarządzanej przypisanej przez użytkownika | Microsoft Docs
description: W tym artykule opisano sposób wdrażania aplikacji Service Fabric przy użyciu tożsamości zarządzanej przypisanej przez użytkownika
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: article
ms.date: 08/09/2019
ms.author: atsenthi
ms.openlocfilehash: f8dfaa39f02aefbdda1f34afa5011ce5fadbae49
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624915"
---
# <a name="deploy-service-fabric-application-with-a-user-assigned-managed-identity-preview"></a>Wdróż aplikację Service Fabric przy użyciu tożsamości zarządzanej przypisanej przez użytkownika (wersja zapoznawcza)

Aby wdrożyć aplikację Service Fabric z tożsamością zarządzaną, aplikacja musi zostać wdrożona za pomocą Azure Resource Manager, zazwyczaj z szablonem Azure Resource Manager. Aby uzyskać więcej informacji na temat wdrażania Service Fabric aplikacji za poorednictwem Azure Resource Manager, zobacz [Zarządzanie aplikacjami i usługami jako Azure Resource Manager zasobami](service-fabric-application-arm-resource.md).

> [!NOTE] 
> 
> Aplikacje, które nie są wdrożone jako zasób platformy Azure, **nie mogą** mieć tożsamości zarządzanych. 
>
> Wdrożenie aplikacji Service Fabric z tożsamością zarządzaną jest obsługiwane w `"2019-06-01-preview"`wersji interfejsu API. Możesz także użyć tej samej wersji interfejsu API dla typu aplikacji, wersji typu aplikacji i zasobów usługi.
>

## <a name="user-assigned-identity"></a>Tożsamość przypisana przez użytkownika

Aby włączyć aplikację z tożsamością przypisaną przez użytkownika, należy najpierw dodać właściwość **Identity** do zasobu aplikacji z typem **userAssigned** i PRZYWOŁYWANYMI tożsamościami przypisanymi przez użytkownika. Następnie Dodaj sekcję **managedIdentities** w sekcji **Właściwości** zasobu **aplikacji** , która zawiera listę przyjaznych nazw mapowania principalId dla każdej tożsamości przypisanej do użytkownika.

### <a name="application-template"></a>Szablon aplikacji

Aby włączyć aplikację z tożsamością przypisaną przez użytkownika, najpierw Dodaj właściwość **Identity** do zasobu aplikacji z typem **userAssigned** i przywoływaną tożsamością użytkownika, a następnie Dodaj obiekt **managedIdentities** wewnątrz **elementu Sekcja właściwości** , która zawiera listę przyjaznych nazw mapowania principalId dla każdej tożsamości przypisanej do użytkownika.

    {
      "apiVersion": "2019-06-01-preview",
      "type": "Microsoft.ServiceFabric/clusters/applications",
      "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]",
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]"
      ],
      "identity": {
        "type" : "userAssigned",
        "userAssignedIdentities": {
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]": {}
        }
      },
      "properties": {
        "typeName": "[parameters('applicationTypeName')]",
        "typeVersion": "[parameters('applicationTypeVersion')]",
        "parameters": {
        },
        "managedIdentities": [
          {
            "name" : "[parameters('userAssignedIdentityName')]",
            "principalId" : "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName')), '2018-11-30').principalId]"
          }
        ]
      }
    }

W powyższym przykładzie nazwa zasobu tożsamości przypisanej do użytkownika jest używana jako przyjazna nazwa tożsamości zarządzanej dla aplikacji. W poniższych przykładach przyjęto, że rzeczywista przyjazna nazwa to "AdminUser".

### <a name="application-package"></a>Pakiet aplikacji

1. Dla każdej tożsamości zdefiniowanej w `managedIdentities` sekcji w szablonie Azure Resource Manager `<ManagedIdentity>` Dodaj tag w manifeście aplikacji w sekcji **podmioty zabezpieczeń** . Atrybut musi pasować do właściwości zdefiniowanej w `managedIdentities`sekcji. `name` `Name`

    **ApplicationManifest. XML**

    ```xml
      <Principals>
        <ManagedIdentities>
          <ManagedIdentity Name="AdminUser" />
        </ManagedIdentities>
      </Principals>
    ```

2. W sekcji **ServiceManifestImport** Dodaj **IdentityBindingPolicy** dla usługi, która korzysta z tożsamości zarządzanej. Te zasady mapują `AdminUser` tożsamość na nazwę tożsamości specyficznej dla usługi, która musi zostać dodana do manifestu usługi później.

    **ApplicationManifest. XML**

    ```xml
      <ServiceManifestImport>
        <Policies>
          <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="AdminUser" />
        </Policies>
      </ServiceManifestImport>
    ```

3. Zaktualizuj manifest usługi, aby dodać **ManagedIdentity** w sekcji Resources o `ServiceIdentityRef` nazwie zgodnej z elementem w `IdentityBindingPolicy` manifeście aplikacji:

    **Servicemanifest. XML**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```

## <a name="next-steps"></a>Następne kroki

* [Jak używać tożsamości zarządzanej w Service Fabric kodzie aplikacji](how-to-managed-identity-service-fabric-app-code.md)
* [Jak udzielić Service Fabric aplikacji dostępu do innych zasobów platformy Azure](how-to-grant-access-other-resources.md)