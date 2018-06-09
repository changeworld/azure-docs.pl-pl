---
title: Jak udostępnić spacje deweloperów platformy Azure | Dokumentacja firmy Microsoft
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: article
description: Szybkie tworzenie w środowisku Kubernetes za pomocą kontenerów i mikrousług na platformie Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
manager: douge
ms.openlocfilehash: ca644cc412ad0c8f0e2a0781d9419fba58ed8d12
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35247708"
---
# <a name="share-azure-dev-spaces"></a>Funkcja miejsca do udziału deweloperów platformy Azure

Spacjami deweloperów platformy Azure można udostępniać obszaru deweloperów z innymi osobami w zespole. Każdy deweloper może współpracować własnej przestrzeni bez obawy złamanie innych użytkowników. Ponadto pracujących razem w jednym miejscu można włączyć do testowania kodu end-to-end bez konieczności tworzenia mocks lub symulować zależności. Zobacz [więcej informacji na temat tworzenia zespołu](../get-started-nodejs.md#learn-about-team-development) przewodnika, aby uzyskać więcej informacji.

Aby skonfigurować odstęp deweloperów dla wielu deweloperów:
1. Tworzenie miejsca deweloperów na platformie Azure. Wybierz [oprogramowanie .NET Core i kodzie VS](../get-started-netcore.md), [.NET Core i Visual Studio](../get-started-netcore-visualstudio.md), lub [Node.js i kodzie VS](../get-started-nodejs.md). Musisz być właścicielem lub współautorem dostępu do wybranej subskrypcji Azure.
1. Skonfiguruj miejsce deweloperów Azure **grupy zasobów** do [udzielić dostępu współautora](/azure/active-directory/role-based-access-control-configure) dla każdego członka zespołu. Miejsce na deweloperów grupy zasobów można sprawdzić, uruchamiając poniższe polecenie: `azds list`
1. Poproś członkom zespołu **wybierz miejsce deweloperów** aby tworzyć w nim.
     * **Wiersz polecenia lub kodzie VS**: Aby wyświetlić istniejące pomieszczenia deweloperów platformy Azure masz dostęp do: `azds space list`. Aby wybrać miejsce dev: `azds space select`.
     * **Visual Studio IDE**: Otwórz projekt w programie Visual Studio, wybierz **spacje deweloperów Azure** z rozwijanej ustawień uruchamiania. W otwartym oknie dialogowym Wybierz istniejącego klastra.

![Visual Studio Uruchom ustawienia listy rozwijanej](../media/get-started-netcore-visualstudio/LaunchSettings.png)