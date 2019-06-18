---
title: Uwierzytelnianie za pomocą usługi Azure Container Registry w usłudze Azure Container Instances
description: Dowiedz się, jak zapewnić dostęp do obrazów w prywatnego rejestru kontenera z usługi Azure Container Instances za pomocą jednostki usługi Azure Active Directory.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 04/23/2018
ms.author: danlep
ms.openlocfilehash: 8a2d19a09233e510055e147fa1cf95dd4471768b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61333591"
---
# <a name="authenticate-with-azure-container-registry-from-azure-container-instances"></a>Uwierzytelnianie za pomocą usługi Azure Container Registry w usłudze Azure Container Instances

Aby uzyskać dostęp do Twojego rejestry prywatne kontenerów w usłudze Azure Container Registry, można użyć jednostki usługi Azure Active Directory (Azure AD).

W tym artykule dowiesz się utworzyć i skonfigurować jednostkę usługi Azure AD za pomocą *ściągnięcia* uprawnienia do rejestru. Następnie należy uruchomić kontener w usłudze Azure Container Instances (ACI) która ściąga obraz z rejestru prywatnego, uwierzytelniania przy użyciu nazwy głównej usługi.

## <a name="when-to-use-a-service-principal"></a>Kiedy należy używać nazwy głównej usługi

Należy użyć jednostki usługi do uwierzytelniania z poziomu usługi ACI w **bezobsługowego scenariuszy**, na przykład w aplikacji lub usług, które tworzyć wystąpienia kontenera w trybie nienadzorowanym automatycznych lub w inny sposób.

Na przykład, jeśli masz zautomatyzowanego skryptu jest uruchamiane co noc, która tworzy [wystąpienia kontenera opartego na zadaniach](../container-instances/container-instances-restart-policy.md) do przetwarzania niektórych danych, jej użyć jednostki usługi przy użyciu tylko ściąganych uprawnień do uwierzytelniania w rejestrze. Można następnie Obróć poświadczenia nazwy głównej usługi lub odwołać dostępu całkowicie bez wywierania wpływu na inne usługi i aplikacje.

Nazwy główne usług powinny być używane podczas rejestru [administrator](container-registry-authentication.md#admin-account) jest wyłączona.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="authenticate-using-the-service-principal"></a>Uwierzytelnianie za pomocą nazwy głównej usługi

Do uruchomienia kontenera w usłudze Azure Container Instances za pomocą nazwy głównej usługi, należy określić jego identyfikator `--registry-username`i jego hasło na potrzeby `--registry-password`.

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

Poprzedni przykładowe skrypty można znaleźć wiersza polecenia platformy Azure w serwisie GitHub, jak dobrze wersjami dla programu Azure PowerShell:

* [Wiersza polecenia platformy Azure][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="next-steps"></a>Kolejne kroki

Następujące artykuły zawierają dodatkowe informacje na temat pracy z jednostek usługi i ACR:

* [Uwierzytelnianie systemu Azure Container Registry za pomocą jednostki usługi](container-registry-auth-service-principal.md)
* [Uwierzytelnianie za pomocą usługi Azure Container Registry z usługi Azure Kubernetes Service (AKS)](container-registry-auth-aks.md)

<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
