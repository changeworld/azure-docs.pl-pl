---
title: Renderowanie możliwości — w usłudze Azure Batch
description: Jak używać usługi Azure Batch możliwości renderowania
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 2dff44f0b5b4b02c39c4c63f23ff64d55ca9d833
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60337621"
---
# <a name="using-azure-batch-rendering"></a>Za pomocą usługi Azure Batch rendering

Istnieje kilka sposobów, aby używać usługi Azure Batch rendering:

* APIs:
  * Napisz kod przy użyciu dowolnej z interfejsów API usługi Batch.  Deweloperzy mogą integrować możliwości usługi Azure Batch istniejącej aplikacji lub przepływu pracy, czy chmura lub oparte na środowisku lokalnym.
* Narzędzia wiersza polecenia:
  * [Wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/) lub [PowerShell](https://docs.microsoft.com/powershell/azure/overview) może służyć do skryptu Użyj usługi Batch.
  * W szczególności [Obsługa szablonów interfejsu wiersza polecenia usługi Batch](https://docs.microsoft.com/azure/batch/batch-cli-templates) sprawia, że znacznie łatwiej tworzyć pule oraz przesyłania zadań.
* Narzędzie Batch Explorer interfejsu użytkownika:
  * [Batch Explorer](https://github.com/Azure/BatchLabs) to narzędzie klienta dla wielu platform, które umożliwia także kont usługi Batch, zarządzane i monitorowane.
  * Dla każdego z aplikacjami do renderowania szablony puli i zadania są dostarczane mogą służyć do łatwego tworzenia pul i przesyłać zadania.  Zestaw szablonów znajduje się w aplikacji interfejsu użytkownika, za pomocą plików szablonów, którego uzyskiwany jest dostęp z usługi GitHub.
  * Szablony niestandardowe można tworzyć od podstaw lub dostarczane szablony z witryny GitHub można kopiować i modyfikować.
* Wtyczki klienta aplikacji:
  * Dostępne są wtyczki umożliwiające renderowania usługi Batch można używać bezpośrednio z poziomu projektu klienta i aplikacjach do modelowania.  Dodatki plug-in głównie wywołania aplikacji Batch Explorer informacje kontekstowe dotyczące modelu 3D i zawiera funkcje pomagające w zarządzaniu zasobami.

Najlepszym sposobem do wypróbowania usługi Azure Batch rendering i najprostszy sposób dla użytkowników końcowych, którzy nie są deweloperzy i ekspertów nie platformy Azure, jest za pomocą programu Batch Explorer aplikacji albo bezpośrednio lub wywołać z aplikacji klienckiej wtyczki.

## <a name="using-batch-explorer"></a>Używanie Eksploratora usługi Batch

Aby uzyskać samouczek krok po kroku dotyczące korzystania z programu Batch Explorer przeprowadzić renderowania zobacz [samouczek Blender](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender).

### <a name="download-and-install"></a>Pobierz i zainstaluj

Batch Explorer [pliki do pobrania są dostępne](https://azure.github.io/BatchExplorer/) dla Windows, OS x i Linux.

### <a name="using-templates-to-create-pools-and-run-jobs"></a>Tworzenie pul i uruchamiać zadania przy użyciu szablonów

Kompleksowy zestaw szablonów jest dostępny do użycia z usługą Batch Explorer, który ułatwia tworzenie pul i przesyłanie zadań dla różnych aplikacji renderowanie, bez konieczności określania wszystkich właściwości, które są wymagane do tworzenia pul, zadań i zadań bezpośrednio za pomocą Usługi Batch.  Szablonów dostępnych w Eksploratorze usługi Batch są przechowywane i są widoczne w [repozytorium GitHub](https://github.com/Azure/BatchExplorer-data/tree/master/ncj).

![Galeria Eksplorator usługi Batch](./media/batch-rendering-using/batch-explorer-gallery.png)

Szablony są pod warunkiem, obsługę wszystkich aplikacji znajduje się on na obrazów maszyn wirtualnych renderowanie witryny Marketplace.  Dla każdej aplikacji wielu istnieją szablony, w tym szablony puli w celu zaspokojenia GPU i CPU pule, Windows i Linux pule; Szablony zadań obejmują pełne ramki lub sąsiadująco Blender renderowania i V-Ray rozproszonych renderowania. Zestaw szablonów podana zostanie rozszerzona wraz z upływem czasu w celu zaspokojenia innych funkcji usługi Batch, takie jak skalowanie automatyczne puli.

Użytkownik może również dla szablonów niestandardowych, aby tworzone od podstaw lub przez zmodyfikowanie dostarczane szablony. Niestandardowe szablony mogą być używane przez wybranie elementu "Szablony lokalnej" w sekcji "Galerii" programu Batch Explorer.

### <a name="file-system-and-data-movement"></a>Przenoszenie danych i system plików

W sekcji "Dane" w Eksploratorze usługi Batch pozwala pliki będą kopiowane między lokalnym systemie plików i kont usługi Azure Storage.

## <a name="client-application-plug-ins"></a>Wtyczki aplikacji klienta

Dodatki plug-in są dostępne dla niektórych aplikacji klienckich.  Dodatki plug-in umożliwiają pul i zadań, które można utworzyć bezpośrednio z aplikacji lub wywołanie programu Batch Explorer.

* [Blender](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender)
* [Autodesk 3ds Max](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/3ds-max)
* [Programy Autodesk Maya](https://github.com/Azure/azure-batch-maya)

## <a name="next-steps"></a>Kolejne kroki

Przykłady usługi Batch renderowania Wypróbuj samouczki dwa:

* [Renderowanie przy użyciu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)
* [Renderowanie przy użyciu programu Batch Explorer](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)