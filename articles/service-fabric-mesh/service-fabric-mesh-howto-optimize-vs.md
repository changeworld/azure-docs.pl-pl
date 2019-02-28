---
title: Optymalizacja wydajności programu Visual Studio dla projektów usługi Azure Service Fabric Mesh | Microsoft Docs
description: Optymalizacja wydajności programu Visual Studio dla aplikacji usługi Azure Service Fabric Mesh
services: service-fabric-mesh
keywords: optymalizacja wydajności debugowania
author: dkkapur
ms.author: dekapur
ms.date: 11/29/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: chakdan
ms.openlocfilehash: c7c9f9e72ae7ec2807c8fea08a8cc8d3e8a9e382
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2019
ms.locfileid: "56804811"
---
# <a name="optimize-visual-studio-performance-for-service-fabric-mesh-projects"></a>Optymalizacja wydajności programu Visual Studio dla projektów usługi Service Fabric Mesh

W tym artykule przedstawiono, jak zoptymalizować wydajność programu Visual Studio pod kątem projektów usługi Service Fabric Mesh, tak aby uruchomienie pierwszego debugowania (F5) trwało znacznie krócej.  

## <a name="change-visual-studio-settings"></a>Zmienianie ustawień programu Visual Studio
 
W programie Visual Studio w obszarze **Narzędzia** > **Opcje**  > **Narzędzia Service Fabric Mesh** > **Ogólne** możesz dostosować następujące ustawienia:

- Ustawienie **Ściągaj wymagane obrazy platformy Docker przy ładowaniu projektu** umożliwia szybsze rozpoczęcie pierwszego debugowania (F5) przez uruchomienie procesu pobierania obrazu podczas ładowania projektu.  
- Ustawienie **Wdróż aplikację przy otwieraniu projektu** umożliwia szybsze rozpoczęcie pierwszego debugowania (F5) przez uruchomienie procesu wdrażania przy otwieraniu projektu.  
- Opcja **Usuń aplikację przy zamykaniu projektu** umożliwia odzyskanie zasobów (procesora, pamięci RAM) przydzielonych do aplikacji przez usunięcie aplikacji usługi Mesh przy zamykaniu projektu.  

Gdy w oknie danych wyjściowych narzędzi Service Fabric Tools są wyświetlane komunikaty odnoszące się do „ściągania obrazów”, „przygotowywania” i „usuwania aplikacji” przez program Visual Studio, dotyczy to powyższych ustawień. Te ustawienia można wyłączyć.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z artykułem [Debugowanie aplikacji usługi Mesh](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)