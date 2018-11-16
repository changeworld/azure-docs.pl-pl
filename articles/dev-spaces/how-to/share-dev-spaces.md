---
title: Jak udostępniać usługi Azure Dev miejsca do magazynowania | Dokumentacja firmy Microsoft
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 05/11/2018
ms.topic: article
description: Szybkie tworzenie w środowisku Kubernetes za pomocą kontenerów i mikrousług na platformie Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
ms.openlocfilehash: 57ca0f7b7704b179a55ac75fea99d35e1024ee8e
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2018
ms.locfileid: "51706419"
---
# <a name="share-azure-dev-spaces"></a>Miejsca do magazynowania udziału usługi Azure Dev

Za pomocą usługi Azure Dev miejsca do magazynowania można udostępnić obszaru dev z innymi osobami w zespole. Każdy deweloper może pracować w ich własnych miejsce do magazynowania bez obawiać się, że inne istotne. Praca ze sobą w jednym miejscu może również włączyć do testowania kodu end-to-end, bez konieczności tworzenia mocks lub symulacji zależności. Zobacz [więcej informacji na temat Projektowanie zespołowe](../team-development-nodejs.md) przewodnika, aby uzyskać więcej informacji.

## <a name="set-up-a-dev-space-for-multiple-developers"></a>Konfigurowanie miejsca dev dla wielu deweloperów

1. Tworzenie przestrzeni deweloperów na platformie Azure. Wybierz [platformy .NET Core i programu VS Code](../get-started-netcore.md), [platformy .NET Core i Visual Studio](../get-started-netcore-visualstudio.md), lub [środowiska Node.js i program VS Code](../get-started-nodejs.md). Musisz mieć dostęp właściciela lub współautora do wybranej subskrypcji platformy Azure.
1. Skonfiguruj miejsce Azure Dev na **grupy zasobów** do [przyznać prawa współautora](/azure/active-directory/role-based-access-control-configure) dla każdego członka zespołu. Miejsce na deweloperów grupy zasobów można sprawdzić, uruchamiając następujące polecenie: `azds list-up`
1. Poproś członków zespołu, aby **wybierz miejsce dev** aby tworzyć w nim.
     * **Wiersz polecenia lub programu VS Code**: Aby wyświetlić istniejące usługi Azure Dev miejsca do magazynowania ma dostęp do: `azds space list`. Aby wybrać miejsce dev: `azds space select`.
     * **Środowiska IDE programu Visual Studio**: Otwórz projekt w programie Visual Studio, wybierz **miejsca do magazynowania Azure Dev** z uruchamiania ustawienia listy rozwijanej. W wyświetlonym oknie dialogowym Wybierz istniejący klaster.

    ![Visual Studio Uruchom ustawień listy rozwijanej](../media/get-started-netcore-visualstudio/LaunchSettings.png)

## <a name="next-steps"></a>Kolejne kroki

Zobacz [więcej informacji na temat Projektowanie zespołowe](../team-development-nodejs.md) Aby uzyskać więcej informacji.