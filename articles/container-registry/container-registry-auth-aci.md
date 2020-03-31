---
title: Dostęp z wystąpień kontenera
description: Dowiedz się, jak zapewnić dostęp do obrazów w rejestrze kontenerów prywatnych z wystąpień kontenerów platformy Azure przy użyciu jednostki usługi Azure Active Directory.
ms.topic: article
ms.date: 04/23/2018
ms.openlocfilehash: b1bc8119c495dea99c6bdc4923db198d041a1e9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456506"
---
# <a name="authenticate-with-azure-container-registry-from-azure-container-instances"></a>Uwierzytelnij się przy użyciu rejestru kontenerów platformy Azure z wystąpień kontenerów platformy Azure

Można użyć jednostki usługi Azure Active Directory (Azure AD), aby zapewnić dostęp do rejestrów kontenerów prywatnych w rejestrze kontenerów platformy Azure.

W tym artykule nauczysz się tworzyć i konfigurować jednostkę usługi Azure AD z uprawnieniami *ściągania* do rejestru. Następnie należy uruchomić kontener w instancjach kontenera azure (ACI), który pobiera jego obraz z rejestru prywatnego, przy użyciu jednostki usługi do uwierzytelniania.

## <a name="when-to-use-a-service-principal"></a>Kiedy używać jednostki usługi

Należy użyć jednostki usługi do uwierzytelniania z usługi ACI w **scenariuszach bezgłowych,** takich jak w aplikacjach lub usługach, które tworzą wystąpienia kontenera w sposób zautomatyzowany lub w inny sposób nienadzorowany.

Na przykład jeśli masz automatyczny skrypt, który działa w nocy i tworzy [wystąpienie kontenera oparte na zadaniach](../container-instances/container-instances-restart-policy.md) do przetwarzania niektórych danych, można użyć jednostki usługi z uprawnieniami tylko ściągane do uwierzytelniania w rejestrze. Następnie można obrócić poświadczenia jednostki usługi lub całkowicie odwołać jego dostęp bez wpływu na inne usługi i aplikacje.

Jednostki usługi powinny być również używane, gdy [użytkownik administratora](container-registry-authentication.md#admin-account) rejestru jest wyłączony.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="authenticate-using-the-service-principal"></a>Uwierzytelnij się przy użyciu jednostki usługi

Aby uruchomić kontener w wystąpieniach kontenera platformy Azure przy `--registry-username`użyciu jednostki `--registry-password`usługi, należy określić jego identyfikator programu i hasło do programu .

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerregistry.azurecr.io/myimage:v1 \
    --registry-login-server mycontainerregistry.azurecr.io \
    --registry-username <service-principal-ID> \
    --registry-password <service-principal-password>
```

## <a name="sample-scripts"></a>Przykładowe skrypty

Poprzednie przykładowe skrypty interfejsu wiersza polecenia platformy Azure można znaleźć w usłudze GitHub, a także wersje programu Azure PowerShell:

* [Interfejs wiersza polecenia platformy Azure][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="next-steps"></a>Następne kroki

Następujące artykuły zawierają dodatkowe szczegóły dotyczące pracy z podmiotami obsługującymi usługi i acr:

* [Uwierzytelnianie rejestru kontenerów platformy Azure z podmiotami zabezpieczeń usługi](container-registry-auth-service-principal.md)
* [Uwierzytelnij się przy użyciu usługi Azure Container Registry z usługi Azure Kubernetes Service (AKS)](../aks/cluster-container-registry-integration.md)

<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
