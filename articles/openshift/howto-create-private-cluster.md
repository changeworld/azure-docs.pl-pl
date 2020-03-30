---
title: Tworzenie prywatnego klastra za pomocą usługi Azure Red Hat OpenShift 3.11 | Dokumenty firmy Microsoft
description: Tworzenie klastra prywatnego za pomocą usługi Azure Red Hat OpenShift 3.11
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/02/2020
keywords: aro, openshift, prywatny klaster, czerwony kapelusz
ms.openlocfilehash: b34b5d622527742447847102526eba9ee6ca220d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399421"
---
# <a name="create-a-private-cluster-with-azure-red-hat-openshift-311"></a>Tworzenie klastra prywatnego za pomocą usługi Azure Red Hat OpenShift 3.11

> [!IMPORTANT]
> Klastry prywatne Azure Red Hat OpenShift (ARO) są obecnie dostępne tylko w prywatnej wersji zapoznawczej we wschodnich stanach USA 2. Akceptacja prywatnej wersji zapoznawczej odbywa się wyłącznie na zaproszenie. Przed podjęciem próby włączenia tej funkcji należy zarejestrować subskrypcję.

Klastry prywatne zapewniają następujące korzyści:

* Klastry prywatne nie uwidaczniają składników płaszczyzny kontroli klastra (takich jak serwery interfejsu API) na publicznym adresie IP.
* Sieć wirtualna klastra prywatnego jest konfigurowana przez klientów, co umożliwia skonfigurowanie sieci w celu umożliwienia komunikacji równorzędnej z innymi sieciami wirtualnymi, w tym środowiskami usługi ExpressRoute. Można również skonfigurować niestandardowy system DNS w sieci wirtualnej w celu integracji z usługami wewnętrznymi.

## <a name="before-you-begin"></a>Przed rozpoczęciem

> [!NOTE]
> Ta funkcja wymaga wersji 2019-10-27-podgląd interfejsu API HTTP ARO. Nie jest jeszcze obsługiwana w platformie Azure CLI.

Pola w poniższym fragmentie kodu konfiguracji są nowe i muszą być uwzględnione w konfiguracji klastra. `managementSubnetCidr`musi znajdować się w sieci wirtualnej klastra i jest używany przez platformę Azure do zarządzania klastrem.

```json
properties:
 networkProfile:
   managementSubnetCidr: 10.0.1.0/24
 masterPoolProfile:
   apiProperties:
     privateApiServer: true
```

Klaster prywatny można wdrożyć przy użyciu przykładowych skryptów podanych poniżej. Po wdrożeniu klastra `cluster get` wykonaj polecenie `properties.FQDN` i wyświetl właściwość, aby określić prywatny adres IP serwera interfejsu API OpenShift.

Sieć wirtualna klastra zostanie utworzona z uprawnieniami, dzięki czemu można ją zmodyfikować. Następnie można skonfigurować sieć, aby uzyskać dostęp do sieci wirtualnej (Usługi ExpressRoute, VPN, komunikacja równorzędna sieci wirtualnej) zgodnie z wymaganiami dla Twoich potrzeb.

Jeśli zmienisz serwery nazw DNS w sieci wirtualnej klastra, musisz wydać `properties.RefreshCluster` aktualizację `true` klastra z właściwością ustawioną na tak, aby maszyny wirtualne mogły zostać ponownie zaaimagedowane. Ta aktualizacja pozwoli im odebrać nowe serwery nazw.

## <a name="sample-configuration-scripts"></a>Przykładowe skrypty konfiguracji

Użyj przykładowych skryptów w tej sekcji, aby skonfigurować i wdrożyć klaster prywatny.

### <a name="environment"></a>Środowisko

Wypełnij poniższe zmienne środowiskowe jako przy użyciu własnych wartości.

> [!NOTE]
> Lokalizacja musi być `eastus2` ustawiona na to, ponieważ jest to obecnie jedyna obsługiwana lokalizacja dla klastrów prywatnych.

``` bash
export CLUSTER_NAME=
export LOCATION=eastus2
export TOKEN=$(az account get-access-token --query 'accessToken' -o tsv)
export SUBID=
export TENANT_ID=
export ADMIN_GROUP=
export CLIENT_ID=
export SECRET=
```

### <a name="private-clusterjson"></a>prywatna cluster.json

Przy użyciu zmiennych środowiskowych zdefiniowanych powyżej, w tym miejscu jest konfiguracja klastra próbki z klastra prywatnego włączone.

```json
{
   "location": "$LOCATION",
   "name": "$CLUSTER_NAME",
   "properties": {
       "openShiftVersion": "v3.11",
       "networkProfile": {
           "vnetCIDR": "10.0.0.0/8",
           "managementSubnetCIDR" : "10.0.1.0/24"
       },
       "authProfile": {
           "identityProviders": [
               {
                   "name": "Azure AD",
                   "provider": {
                       "kind": "AADIdentityProvider",
                       "clientId": "$CLIENT_ID",
                       "secret": "$SECRET",
                       "tenantId": "$TENANT_ID",
                       "customerAdminGroupID": "$ADMIN_GROUP"
                   }
               }
           ]
       },
       "masterPoolProfile": {
           "name": "master",
           "count": 3,
           "vmSize": "Standard_D4s_v3",
           "osType": "Linux",
           "subnetCIDR": "10.0.0.0/24",
           "apiProperties": {
                "privateApiServer": true
            }
       },
       "agentPoolProfiles": [
           {
               "role": "compute",
               "name": "compute",
               "count": 1,
               "vmSize": "Standard_D4s_v3",
               "osType": "Linux",
               "subnetCIDR": "10.0.0.0/24"
           },
           {
               "role": "infra",
               "name": "infra",
               "count": 3,
               "vmSize": "Standard_D4s_v3",
               "osType": "Linux",
               "subnetCIDR": "10.0.0.0/24"
           }
       ],
       "routerProfiles": [
           {
               "name": "default"
           }
       ]
   }
}
```

## <a name="deploy-a-private-cluster"></a>Wdrażanie klastra prywatnego

Po skonfigurowaniu klastra prywatnego przy za pomocą przykładowych skryptów powyższych, uruchom następujące polecenie, aby wdrożyć klaster prywatny.

``` bash
az group create --name $CLUSTER_NAME --location $LOCATION
cat private-cluster.json | envsubst | curl -v -X PUT \
-H 'Content-Type: application/json; charset=utf-8' \
-H 'Authorization: Bearer '$TOKEN'' -d @- \
 https://management.azure.com/subscriptions/$SUBID/resourceGroups/$CLUSTER_NAME/providers/Microsoft.ContainerService/openShiftManagedClusters/$CLUSTER_NAME?api-version=2019-10-27-preview
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak uzyskać dostęp do konsoli OpenShift, zobacz [Przewodnik po konsoli sieci Web](https://docs.openshift.com/container-platform/3.11/getting_started/developers_console.html).
