---
title: Znajdowanie przykładów usługi Azure Service Fabric Mesh | Microsoft Docs
description: Dowiedz się, jak znaleźć różne przykładowe aplikacje usługi Service Fabric Mesh.
services: service-fabric-mesh
keywords: ''
author: v-vasuke
ms.author: v-vasuke
ms.date: 12/03/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: jeconnoc
ms.openlocfilehash: d31ee60977bb4ac11a93cfe1e72134c65baf839c
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2018
ms.locfileid: "53161249"
---
# <a name="find-service-fabric-mesh-samples"></a>Znajdowanie przykładów usługi Service Fabric Mesh

W tej tabeli opisano dostępne przykładowe aplikacje usługi Service Fabric Mesh. Kod źródłowy w tych przykładach pokazuje, jak obsłużyć konkretne scenariusze za pomocą modelu zasobów usługi Service Fabric.

Aby uzyskać więcej informacji na temat wdrażania szablonów bezpośrednio na platformie Azure, zobacz [stronę przykładowego szablonu w usłudze GitHub.](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/README.md)


|Przykładowy szablon|Opis scenariusza|Kod źródłowy|Narzędzia deweloperskie|
|------------|--------------------|----------|----------------------|
| [Aplikacja Hello World](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/helloworld) | Statyczna strona internetowa hostowana w kontenerze. W przypadku systemu Linux jest używany serwer nginx, a w przypadku systemu Windows — usługi IIS | [Kod źródłowy](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/helloworld) | Brak wymagań |
| [Aplikacja licznika dla woluminów usługi Azure Files](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter) | Przechowywanie stanu przez zainstalowanie woluminów opartych na usłudze Azure Files wewnątrz kontenera. <br><br> **Uwaga:** Ten szablon wymaga, aby udział plików usługi Azure Files był już aprowizowany [(Instrukcje)](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share) | [Kod źródłowy](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Narzędzia usługi Mesh dla programu Visual Studio |
| [TodoListApp](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/todolistapp) | Tworzenie aplikacji z usługą frontonu i zaplecza, która używa rozwiązania opartego na usłudze DNS. Używane jako samouczek [tutaj](https://docs.microsoft.com/azure/service-fabric-mesh/service-fabric-mesh-tutorial-create-dotnetcore) | [Kod źródłowy](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/todolistapp) | Narzędzia usługi Mesh dla programu Visual Studio |
| [Aplikacja obiektów wizualnych](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/visualobjects) | Skalowanie i uaktualnianie mikrousług w ramach aplikacji. | [Kod źródłowy](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/visualobjects) |  Narzędzia usługi Mesh dla programu Visual Studio |
| [Aplikacja do głosowania](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/votingapp) | Tworzenie aplikacji z usługą frontonu i zaplecza, która używa rozwiązania opartego na usłudze DNS | [Kod źródłowy](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp) | Narzędzia usługi Mesh dla programu Visual Studio są przeznaczone dla systemu Windows. W systemie Linux można użyć programu VS Code lub interfejsu wiersza polecenia platformy dotnet. |
| [Aplikacja licznika dla niezawodnych woluminów usługi Service Fabric](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter)| Przechowywanie stanu przez zainstalowanie niezawodnych woluminów dyskowych usługi Service Fabric wewnątrz kontenera.| [Kod źródłowy](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Narzędzia usługi Mesh dla programu Visual Studio |
