---
title: Przechowywanie i używanie wpisów tajnych aplikacji usługi Azure Service Fabric Mesh | Microsoft Docs
description: Przechowywanie i używanie wpisów tajnych usługi Service Fabric Mesh.
services: service-fabric-mesh
keywords: wpisy tajne
author: v-steg
ms.author: v-steg
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: jeanpaul.connock
ms.openlocfilehash: b6db9b6cb2ccbf1c8d29edb817d35677109ae755
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2019
ms.locfileid: "57768625"
---
# <a name="service-fabric-mesh-application-secrets"></a>Wpisy tajne aplikacji usługi Service Fabric Mesh
Usługa Service Fabric Mesh obsługuje wpisy tajne jako zasoby platformy Azure. Wpis tajny usługi Service Fabric Mesh może być dowolnym poufnym tekstem, takim jak parametry połączenia magazynu, hasła lub inne wartości, które powinny być przechowywane i przesyłane w bezpieczny sposób.

![Omówienie wpisów tajnych usługi Mesh][sf-mesh-secrets-overview]

## <a name="mesh-secrets-resources"></a>Zasoby wpisów tajnych usługi Mesh
Wpis tajny aplikacji usługi Mesh składa się z następujących elementów:
* Zasób **Wpisy tajne**, który jest kontenerem przechowującym tekstowe wpisy tajne. Wpisy tajne zawarte w zasobie **Wpisy tajne** są przechowywane i przesyłane w bezpieczny sposób.
* Co najmniej jeden zasób **Wpisy tajne/wartości**, który jest przechowywany w kontenerze zasobów **Wpisy tajne**. Zasoby **Wpisy tajne/wartości** rozróżnia się na podstawie numeru wersji.

## <a name="inline-or-stored-in-azure-key-vault"></a>Osadzanie lub przechowywanie w usłudze Azure Key Vault
- Aplikacje i zasoby usług dla usługi Mesh obejmują tożsamość usługi zarządzanej (MSI, Managed Service Identity) przy użyciu usługi Azure Active Directory (AAD), co umożliwia dostęp do wpisów tajnych w usłudze Azure Key Vault.
- Wpisy tajne i certyfikaty mogą być automatycznie przerzucane za pomocą zasad.

## <a name="next-steps"></a>Następne kroki 
Aby dowiedzieć się więcej na temat wpisów tajnych usługi Service Fabric Mesh, zobacz:
- [Zarządzanie wpisami tajnymi aplikacji usługi Service Fabric Mesh](service-fabric-mesh-howto-manage-secrets.md)
- [Wprowadzenie do modelu zasobów usługi Service Fabric](service-fabric-mesh-service-fabric-resources.md)

<!-- pics -->
[sf-mesh-secrets-overview]: ./media/service-fabric-mesh-secrets-overview/MeshAppSecretsOverview.png
