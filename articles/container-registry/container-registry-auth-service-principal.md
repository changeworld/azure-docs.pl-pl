---
title: Uwierzytelnianie systemu Azure Container Registry przy użyciu jednostki usługi
description: Zapewniają dostęp do obrazów w prywatnego rejestru kontenera za pomocą jednostki usługi Azure Active Directory.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 12/13/2018
ms.author: danlep
ms.openlocfilehash: 5d8904b5906adbdab68989b3a5cf9c3975c23533
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61347085"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>Uwierzytelnianie systemu Azure Container Registry za pomocą jednostki usługi

Jednostki usługi Azure Active Directory (Azure AD) umożliwia udostępnianie obrazu kontenera `docker push` i `pull` dostępu do rejestru kontenerów. Za pomocą nazwy głównej usługi, można zapewnić dostęp do "bezobsługowe" usług i aplikacji.

## <a name="what-is-a-service-principal"></a>Co to jest jednostka usługi?

Usługa Azure AD *jednostki usług* zapewniają dostęp do zasobów platformy Azure w ramach Twojej subskrypcji. Można traktować usługę podmiotu zabezpieczeń jako tożsamość użytkownika dla usługi "Usługa" w przypadku dowolnej aplikacji, usługi lub platformy, która wymaga dostępu do zasobów. Nazwy głównej usługi można skonfigurować przy użyciu praw dostępu ograniczone tylko do tych zasobów, które określisz. Następnie należy skonfigurować poświadczenia nazwy głównej usługi umożliwia dostęp do tych zasobów, Twoja aplikacja lub usługa.

W kontekście usługi Azure Container Registry można utworzyć usługi Azure AD nazwę główną usługi za pomocą ściągania, wypychania i ściągania lub inne uprawnienia do rejestru prywatnego platformy Azure. Aby uzyskać pełną listę, zobacz [uprawnienia i role usługi Azure Container Registry](container-registry-roles.md).

## <a name="why-use-a-service-principal"></a>Dlaczego warto używać nazwy głównej usługi?

Za pomocą jednostki usługi Azure AD, można zapewnić dostęp o określonym zakresie prywatnego rejestru kontenera. Można utworzyć jednostki różnych usług dla każdej z aplikacji lub usług, a każda z prawami dostępu dostosowane do Twojego rejestru. A ponieważ można uniknąć, udostępnianie poświadczeń między usługami i aplikacjami, możesz zamiana poświadczeń lub odwołać dostęp do jednostki usługi (i dlatego aplikacja) wybranych.

Na przykład, aplikacja sieci web można użyć jednostki usługi, zapewniająca go z obrazem `pull` dostęp tylko wtedy, gdy system kompilacji można użyć jednostki usługi, który ją obsługuje zarówno `push` i `pull` dostępu. Jeśli rozwoju aplikacji zmieni się ćwiczenia, można też obrócić jego poświadczenia zasady usługi bez wpływu na system kompilacji.

## <a name="when-to-use-a-service-principal"></a>Kiedy należy używać nazwy głównej usługi

Należy używać nazwy głównej usługi, aby zapewnić dostęp do rejestru w **bezobsługowego scenariuszy**. Oznacza to dowolnej aplikacji, usług lub skrypt, który należy wypchnąć ani ściągnąć kontenera obrazów w trybie nienadzorowanym automatycznych lub w inny sposób.

Dla indywidualnej operacji dostępu do rejestru, takie jak podczas ręcznego ściągania obrazu kontenera na deweloperskiej stacji roboczej, należy w zamian użyć własnego [tożsamości usługi Azure AD](container-registry-authentication.md#individual-login-with-azure-ad) uzyskać dostęp do rejestru (na przykład za pomocą [az acr Zaloguj się][az-acr-login]).

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="sample-scripts"></a>Przykładowe skrypty

Poprzedni przykładowe skrypty można znaleźć wiersza polecenia platformy Azure w serwisie GitHub, jak dobrze wersjami dla programu Azure PowerShell:

* [Wiersza polecenia platformy Azure][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="next-steps"></a>Kolejne kroki

Po utworzeniu jednostki usługi, że został udzielony dostęp do usługi container registry, można użyć jego poświadczeń w aplikacji i usług do interakcji z bezobsługowego rejestru. Za pomocą poświadczeń jednostki usługi z dowolnej usługi platformy Azure, która może uwierzytelniać za pomocą usługi Azure container registry. Przykłady:

* [Uwierzytelnianie za pomocą usługi Azure Container Registry z usługi Azure Kubernetes Service (AKS)](container-registry-auth-aks.md)
* [Uwierzytelnianie za pomocą usługi Azure Container Registry w usłudze Azure Container Instances (ACI)](container-registry-auth-aci.md)

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
