---
title: Instalowanie pakietu renderowania zdalnego dla unity
description: W tym artykule wyjaśniono, jak zainstalować biblioteki DLL klienta zdalnego renderowania dla aplikacji Unity
author: florianborn71
ms.author: flborn
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 86ffe47c009f9e0ae121ffc6ab57bff8fb73210f
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681183"
---
# <a name="install-the-remote-rendering-package-for-unity"></a>Instalowanie pakietu renderowania zdalnego dla unity

Renderowanie zdalne platformy Azure używa pakietu Unity do hermetyzacji integracji z Unity.

## <a name="manage-the-remote-rendering-packages-in-unity"></a>Zarządzanie pakietami zdalnego renderowania w unity

Pakiety Unity to kontenery, którymi można zarządzać za pośrednictwem [Menedżera pakietów](https://docs.unity3d.com/Packages/com.unity.package-manager-ui@1.8/manual/index.html)Unity.
Ten pakiet zawiera cały interfejs API języka C#, a także wszystkie pliki binarne wtyczki wymagane do korzystania z renderowania zdalnego platformy Azure z unity.
Zgodnie ze schematem nazewnictwa pakietów unity nazywa się **com.microsoft.azure.remote-rendering**.

Pakiet nie jest częścią [repozytorium próbek ARR](https://github.com/Azure/azure-remote-rendering)i nie jest dostępny w wewnętrznym rejestrze pakietów Unity. Aby dodać go do projektu, należy ręcznie edytować `manifest.md` plik projektu, aby dodać następujące elementy:
```json
{
  "scopedRegistries": [
    {
      "name": "Azure Mixed Reality Services",
      "url": "https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM/",
      "scopes": ["com.microsoft.azure"]
    }
   ],
  "dependencies": {
    "com.microsoft.azure.remote-rendering": "0.1.11",
    ...existing dependencies...
  }
}
```
Po dodaniu tej funkcji można użyć Menedżera pakietów Unity, aby upewnić się, że masz najnowszą wersję.
Bardziej kompleksowe instrukcje znajdują się w [tutorialu: Konfigurowanie projektu Unity od podstaw](../../tutorials/unity/project-setup.md).

## <a name="unity-render-pipelines"></a>Potoki renderowania jedności

Renderowanie zdalne działa zarówno z **potokiem renderowania uniwersalnego,** jak i **potokiem renderowania standardowego**. Ze względu na wydajność uniwersalny potok renderowania jest zalecane.

Aby użyć **potoku renderowania uniwersalnego,** jego pakiet musi zostać zainstalowany w unity. Można to zrobić w interfejsie **Unity's Package Manager** (nazwa pakietu Universal **RP**, wersja 7.2.1 lub nowsza) lub za pośrednictwem `Packages/manifest.json` pliku, jak opisano w [samouczku konfiguracji projektu Unity](../../tutorials/unity/project-setup.md#configure-the-projects-manifest).

## <a name="next-steps"></a>Następne kroki

* [Obiekty i komponenty gry Unity](objects-components.md)
* [Samouczek: Konfigurowanie projektu Unity od podstaw](../../tutorials/unity/project-setup.md)
