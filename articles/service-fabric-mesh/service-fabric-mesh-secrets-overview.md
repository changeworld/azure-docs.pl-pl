---
title: Przechowywanie i używanie wpisów tajnych aplikacji usługi Azure Service Fabric siatk
description: Usługa Service Fabric Mesh obsługuje wpisy tajne jako zasoby platformy Azure. Oto jak przechowywać wpisy tajne i zarządzać nimi za pomocą aplikacji Service Fabric siatki.
author: v-steg
ms.author: jeconnoc
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: d45b9e98c1f325e5dbd656c85655a4ac72e4c3e3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75459114"
---
# <a name="service-fabric-mesh-application-secrets"></a>Wpisy tajne aplikacji usługi Service Fabric Mesh
Usługa Service Fabric Mesh obsługuje wpisy tajne jako zasoby platformy Azure. Wpis tajny usługi Service Fabric Mesh może być dowolnym poufnym tekstem, takim jak parametry połączenia magazynu, hasła lub inne wartości, które powinny być przechowywane i przesyłane w bezpieczny sposób.

![Omówienie wpisów tajnych usługi Mesh][sf-mesh-secrets-overview]

## <a name="mesh-secrets-resources"></a>Zasoby wpisów tajnych usługi Mesh
Wpis tajny aplikacji usługi Mesh składa się z następujących elementów:
* Zasób **Wpisy tajne**, który jest kontenerem przechowującym tekstowe wpisy tajne. Wpisy tajne zawarte w zasobie **Wpisy tajne** są przechowywane i przesyłane w bezpieczny sposób.
* Co najmniej jeden zasób **Wpisy tajne/wartości**, który jest przechowywany w kontenerze zasobów **Wpisy tajne**. Zasoby **Wpisy tajne/wartości** rozróżnia się na podstawie numeru wersji.

## <a name="next-steps"></a>Następne kroki 
Aby dowiedzieć się więcej na temat wpisów tajnych usługi Service Fabric Mesh, zobacz:
- [Zarządzanie wpisami tajnymi aplikacji usługi Service Fabric Mesh](service-fabric-mesh-howto-manage-secrets.md)
- [Wprowadzenie do modelu zasobów usługi Service Fabric](service-fabric-mesh-service-fabric-resources.md)

<!-- pics -->
[sf-mesh-secrets-overview]: ./media/service-fabric-mesh-secrets-overview/MeshAppSecretsOverview.png
