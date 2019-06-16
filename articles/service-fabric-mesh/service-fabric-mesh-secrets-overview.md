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
ms.openlocfilehash: 4268356db5f46e92862e19d6391cfe5a94511270
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60810657"
---
# <a name="service-fabric-mesh-application-secrets"></a>Wpisy tajne aplikacji usługi Service Fabric Mesh
Usługa Service Fabric Mesh obsługuje wpisy tajne jako zasoby platformy Azure. Wpis tajny usługi Service Fabric Mesh może być dowolnym poufnym tekstem, takim jak parametry połączenia magazynu, hasła lub inne wartości, które powinny być przechowywane i przesyłane w bezpieczny sposób.

![Omówienie wpisów tajnych usługi Mesh][sf-mesh-secrets-overview]

## <a name="mesh-secrets-resources"></a>Zasoby wpisów tajnych usługi Mesh
Wpis tajny aplikacji usługi Mesh składa się z następujących elementów:
* Zasób **Wpisy tajne**, który jest kontenerem przechowującym tekstowe wpisy tajne. Wpisy tajne zawarte w zasobie **Wpisy tajne** są przechowywane i przesyłane w bezpieczny sposób.
* Co najmniej jeden zasób **Wpisy tajne/wartości**, który jest przechowywany w kontenerze zasobów **Wpisy tajne**. Zasoby **Wpisy tajne/wartości** rozróżnia się na podstawie numeru wersji.

## <a name="next-steps"></a>Kolejne kroki 
Aby dowiedzieć się więcej na temat wpisów tajnych usługi Service Fabric Mesh, zobacz:
- [Zarządzanie wpisami tajnymi aplikacji usługi Service Fabric Mesh](service-fabric-mesh-howto-manage-secrets.md)
- [Wprowadzenie do modelu zasobów usługi Service Fabric](service-fabric-mesh-service-fabric-resources.md)

<!-- pics -->
[sf-mesh-secrets-overview]: ./media/service-fabric-mesh-secrets-overview/MeshAppSecretsOverview.png
