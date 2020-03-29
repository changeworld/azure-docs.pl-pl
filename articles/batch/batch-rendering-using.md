---
title: Korzystanie z możliwości renderowania — usługa Azure Batch
description: Jak korzystać z możliwości renderowania usługi Azure Batch. Spróbuj użyć aplikacji Eksploratora wsadowego, bezpośrednio lub wywoływane z wtyczki aplikacji klienckiej.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 03/05/2020
ms.topic: conceptual
ms.openlocfilehash: f3b2e641ab187514a7900b2ab7cc75068df00252
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672005"
---
# <a name="using-azure-batch-rendering"></a>Korzystanie z renderowania usługi Azure Batch

Istnieje kilka sposobów korzystania z renderowania usługi Azure Batch:

* Interfejsy API:
  * Napisz kod przy użyciu dowolnego z interfejsów API partii.  Deweloperzy mogą integrować możliwości usługi Azure Batch z istniejącymi aplikacjami lub przepływem pracy, zarówno w chmurze, jak i lokalnie.
* Narzędzia wiersza polecenia:
  * Wiersz [polecenia platformy Azure](https://docs.microsoft.com/cli/azure/) lub program [PowerShell](https://docs.microsoft.com/powershell/azure/overview) może służyć do skryptu batch używać.
  * W szczególności [obsługa szablonu interfejsu wiersza polecenia usługi Batch](https://docs.microsoft.com/azure/batch/batch-cli-templates) znacznie ułatwia tworzenie pul i przesyłanie zadań.
* Interfejs użytkownika Eksploratora wsadowego:
  * [Eksplorator wsadowy](https://github.com/Azure/BatchLabs) to narzędzie klienta między platformami, które umożliwia również zarządzanie kontami usługi Batch i ich monitorowanie.
  * Dla każdej aplikacji renderowania znajduje się wiele szablonów puli i zadań, które mogą służyć do łatwego tworzenia pul i przesyłania zadań.  Zestaw szablonów jest wyświetlany w interfejsie użytkownika aplikacji, z plikami szablonów są dostępne z GitHub.
  * Szablony niestandardowe mogą być utworzone od podstaw lub dostarczone szablony z GitHub mogą być kopiowane i modyfikowane.
* Wtyczki aplikacji klienckich:
  * Dostępne są wtyczki, które umożliwiają renderowanie usługi Batch do użycia bezpośrednio w aplikacjach do projektowania i modelowania klienta.  Wtyczki głównie wywołać aplikację Eksploratora wsadowego z informacji kontekstowych o bieżącym modelu 3D i zawiera funkcje ułatwiające zarządzanie zasobami.

Najlepszym sposobem wypróbowania renderowania usługi Azure Batch i najprostszym sposobem dla użytkowników końcowych, którzy nie są deweloperami, a nie ekspertami platformy Azure, jest użycie aplikacji Eksploratora wsadowego, bezpośrednio lub wywoływane z wtyczki aplikacji klienckiej.

## <a name="using-batch-explorer"></a>Korzystanie z Eksploratora wsadowego

Aby uzyskać samouczek krok po kroku do wykonywania renderowania za pomocą Eksploratora wsadowego, zobacz [samouczek programu Blender](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender).

### <a name="download-and-install"></a>Pobieranie i instalowanie

Pliki do pobrania w eksploratorze [wsadowego są dostępne](https://azure.github.io/BatchExplorer/) dla systemów Windows, OSX i Linux.

### <a name="using-templates-to-create-pools-and-run-jobs"></a>Tworzenie pul i uruchamiania zadań za pomocą szablonów

Kompleksowy zestaw szablonów jest dostępny do użytku z Eksploratorem wsadowym, który ułatwia tworzenie pul i przesyłanie zadań dla różnych aplikacji renderowania bez konieczności określania wszystkich właściwości wymaganych do bezpośredniego tworzenia pul, zadań i zadań za pomocą Partii.  Szablony dostępne w Eksploratorze wsadowym są przechowywane i widoczne w [repozytorium GitHub](https://github.com/Azure/BatchExplorer-data/tree/master/ncj).

![Galeria Eksploratora wsadowego](./media/batch-rendering-using/batch-explorer-gallery.png)

Szablony są dostarczane, które zaspokajają wszystkie aplikacje obecne w programie Marketplace renderowania obrazów maszyn wirtualnych.  Dla każdej aplikacji istnieje wiele szablonów, w tym szablony puli w celu zaspokojenia pul procesorów CPU i GPU, pul systemu Windows i Linux; Szablony zadań obejmują renderowanie blendera pełnoklatkowego lub sąsiadującego oraz renderowanie rozproszone V-Ray. Zestaw dostarczonych szablonów zostanie rozszerzony w miarę czasu, aby zaspokoić inne możliwości usługi Batch, takie jak automatyczne skalowanie puli.

Możliwe jest również tworzenie szablonów niestandardowych od podstaw lub modyfikowanie dostarczonych szablonów. Szablony niestandardowe można używać, wybierając element "Szablony lokalne" w sekcji "Galeria" Eksploratora wsadowego.

### <a name="file-system-and-data-movement"></a>System plików i przenoszenie danych

Sekcja "Dane" w Eksploratorze wsadowym umożliwia kopiowanie plików między lokalnym systemem plików a kontami usługi Azure Storage.

## <a name="client-application-plug-ins"></a>Wtyczki aplikacji klienckich

Wtyczki są dostępne dla niektórych aplikacji klienckich.  Wtyczki umożliwiają tworzenie pul i zadań bezpośrednio z aplikacji lub wywoływanie Eksploratora wsadowego.

* [Blender 2,79](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender)
* [Blender 2,8+](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender28)
* [Autodesk 3ds Max](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/3ds-max)
* [Autodesk Maya](https://github.com/Azure/azure-batch-maya)

## <a name="next-steps"></a>Następne kroki

Na przykład renderowania batch wypróbować dwa samouczki:

* [Renderowanie przy użyciu interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)
* [Renderowanie przy użyciu programu Batch Explorer](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)