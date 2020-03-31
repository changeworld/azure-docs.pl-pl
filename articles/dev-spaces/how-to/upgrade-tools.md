---
title: Jak uaktualnić narzędzia azure dev spaces
services: azure-dev-spaces
ms.date: 07/03/2018
ms.topic: conceptual
description: Dowiedz się, jak uaktualnić narzędzia wiersza polecenia usługi Azure Dev Spaces, rozszerzenie kodu programu Visual Studio i rozszerzenie programu Visual Studio
keywords: Docker, Kubernetes, Azure, AKS, Usługa kontenera azure, kontenery
ms.openlocfilehash: 748ed4699591a21a5a09da924e093011b0813b46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265235"
---
# <a name="how-to-upgrade-azure-dev-spaces-tools"></a>Jak uaktualnić narzędzia azure dev spaces

Jeśli istnieje nowa wersja i już używasz usługi Azure Dev Spaces, może być konieczne uaktualnienie narzędzi klienckich usługi Azure Dev Spaces.

## <a name="update-the-azure-cli"></a>Aktualizowanie interfejsu wiersza polecenia platformy Azure

Po zaktualizowaniu najnowszego interfejsu wiersza polecenia platformy Azure otrzymujesz również najnowszą wersję rozszerzenia interfejsu wiersza polecenia dev spaces.

Nie musisz odinstalowywać poprzedniej wersji, po prostu znajdź odpowiednie pobieranie w [usłudze Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).


## <a name="update-the-dev-spaces-cli-extension-and-command-line-tools"></a>Aktualizowanie rozszerzenia interfejsu wiersza polecenia i narzędzi wiersza polecenia dev spaces

Uruchom następujące polecenie:

```azurecli
az aks use-dev-spaces -n <your-aks-cluster> -g <your-aks-cluster-resource-group> --update
```

## <a name="update-the-vs-code-extension"></a>Aktualizowanie rozszerzenia kodu VS

Po zainstalowaniu rozszerzenie zostanie zaktualizowane automatycznie. Może być konieczne ponowne załadowanie rozszerzenia, aby korzystać z nowych funkcji. W programie VS Code otwórz okienko **Rozszerzenia,** wybierz rozszerzenia **usługi Azure Dev Spaces** i wybierz pozycję **Załaduj ponownie**.

## <a name="update-the-visual-studio-extension"></a>Aktualizowanie rozszerzenia programu Visual Studio

Podobnie jak w przypadku innych rozszerzeń i aktualizacji, program Visual Studio powiadomi Cię, gdy dostępna jest aktualizacja dla narzędzi programu Visual Studio dla aplikacji Kubernetes, która obejmuje usługi Azure Dev Spaces. Poszukaj ikony flagi w prawym górnym rogu ekranu.

Aby zaktualizować narzędzia w programie Visual Studio, wybierz pozycję menu **Narzędzia > rozszerzenia i aktualizacje,** a po lewej stronie wybierz pozycję **Aktualizacje**. Znajdź **narzędzia programu Visual Studio dla witryn Kubernetes** i wybierz przycisk **Aktualizuj.**

## <a name="next-steps"></a>Następne kroki

Przetestuj nowe narzędzia, tworząc nowy klaster. Wypróbuj przewodniki Szybki start i samouczki dostępne w [witrynie Azure Dev Spaces](/azure/dev-spaces).
