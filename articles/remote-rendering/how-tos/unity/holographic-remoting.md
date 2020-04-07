---
title: Użyj holograficznego zdalnego sterowania i zdalnego renderowania w unity
description: Jak można używać podglądu holograficznego zdalnego sterowania w połączeniu z renderowaniem zdalnym platformy Azure
author: christophermanthei
ms.author: chmant
ms.date: 03/23/2020
ms.topic: how-to
ms.openlocfilehash: ac47a2922e92233f0acabf75817a712671306bc1
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681209"
---
# <a name="use-holographic-remoting-and-remote-rendering-in-unity"></a>Użyj holograficznego zdalnego sterowania i zdalnego renderowania w unity

[Holograficzne funkcje zdalnego sterowania](https://docs.microsoft.com/windows/mixed-reality/holographic-remoting-player) i zdalne renderowanie platformy Azure wzajemnie się wykluczają w ramach jednej aplikacji. W związku z tym [tryb odtwarzania Unity](https://docs.microsoft.com/windows/mixed-reality/unity-play-mode) również nie jest dostępny.

Dla każdego uruchomienia edytora Unity można użyć tylko jeden z dwóch. Aby użyć drugiego, najpierw uruchom ponownie Unity.

## <a name="use-unity-play-mode-to-preview-on-hololens-2"></a>Użyj trybu gry Unity, aby wyświetlić podgląd na Hololens 2

 Tryb odtwarzania unity nadal można używać, na przykład do testowania interfejsu użytkownika aplikacji. Jednak ważne jest, aby ARR nigdy nie został zainicjowany. W przeciwnym razie upaść.

## <a name="use-a-wmr-vr-headset-to-preview-on-desktop"></a>Wyświetlanie podglądu na komputerze za pomocą zestawu słuchawkowego WMR VR

Jeśli zestaw słuchawkowy Windows Mixed Reality VR jest obecny, może służyć do podglądu wewnątrz Unity. W takim przypadku można zainicjować ARR, jednak nie będzie możliwe połączenie z sesją, gdy używany jest zestaw słuchawkowy WMR.
