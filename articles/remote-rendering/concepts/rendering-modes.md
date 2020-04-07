---
title: Tryby renderowania
description: Zawiera opis różnych trybów renderowania po stronie serwera
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 7f2b1031659864ae338bb0aa320c048ea23c21f3
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681703"
---
# <a name="rendering-modes"></a>Tryby renderowania

Zdalne renderowanie oferuje dwa główne tryby pracy: tryb **TileBasedComposition** i tryb **DepthBasedComposition.** Te tryby określają sposób rozmieszczenia obciążenia między wieloma procesorami GPU na serwerze. Tryb musi być określony w czasie połączenia i nie można go zmienić w czasie wykonywania.

Oba tryby mają zalety, ale także z nieodłącznymi ograniczeniami funkcji, więc wybranie najbardziej odpowiedniego trybu jest specyficzne dla przypadku użycia.

## <a name="modes"></a>Tryby

Oba tryby są teraz omówione bardziej szczegółowo.

### <a name="tilebasedcomposition-mode"></a>Tryb "TileBasedComposition"

W trybie **TileBasedComposition** każdy zaangażowany procesor GPU renderuje określone podkligle (kafelki) na ekranie. Główny procesor graficzny komponuje ostateczny obraz z kafelków, zanim zostanie wysłany jako klatka wideo do klienta. W związku z tym wszystkie procesory GPU wymagają tego samego zestawu zasobów do renderowania, więc załadowane zasoby muszą zmieścić się w pamięci pojedynczego procesora GPU.

Jakość renderowania w tym trybie jest nieco lepsza niż w trybie **DepthBasedComposition,** ponieważ msaa może pracować nad pełnym zestawem geometrii dla każdego procesora GPU. Poniższy zrzut ekranu pokazuje, że antyaliasing działa poprawnie dla obu krawędzi również:

![MSAA w tileBasedComposition](./media/service-render-mode-quality.png)

Ponadto w tym trybie każda część może być przełączona na przezroczysty materiał lub przełączana w tryb **przezroczystości** za pośrednictwem [hierarchicznego komponentu](../overview/features/override-hierarchical-state.md)

### <a name="depthbasedcomposition-mode"></a>Tryb "DepthBasedComposition"

W trybie **DepthBasedComposition** każdy zaangażowany procesor GPU renderuje w rozdzielczości pełnoekranowej, ale tylko podzbiór siatek. Ostateczna kompozycja obrazu na głównym GPU dba o to, aby części były prawidłowo scalane zgodnie z informacjami o głębokości. Oczywiście ładunek pamięci jest rozprowadzany między procesorami GRAFICZNYmi, co pozwala na renderowanie modeli, które nie pasowałyby do pamięci pojedynczego procesora GPU.

Każdy procesor GPU używa msaa do antialias lokalnej zawartości. Jednak może istnieć nieodłączne aliasing między krawędziami z różnych procesorów GPU. Efekt ten jest złagodzony przez postprocessing ostatecznego obrazu, ale jakość MSAA jest jeszcze gorsza niż w trybie **TileBasedComposition.**

Artefakty MSAA są zilustrowane na ![poniższej ilustracji: MSAA in DepthBasedComposition](./media/service-render-mode-balanced.png)

Antialiasing działa prawidłowo między rzeźbą a kurtyną, ponieważ obie części są renderowane na tym samym GPU. Z drugiej strony krawędź między kurtyną a ścianą pokazuje aliasing, ponieważ te dwie części składają się z różnych procesorów graficznych.

Największym ograniczeniem tego trybu jest to, że części geometrii nie mogą być dynamicznie przełączane na przezroczyste materiały, ani nie działa tryb **przezroczysty** dla [hierarchicznegostateoverridecomponent](../overview/features/override-hierarchical-state.md). Inne funkcje zastępowania stanu (kontur, odcień koloru, ...) działają jednak. Również materiały, które zostały oznaczone jako przezroczyste w czasie konwersji działają poprawnie w tym trybie.

### <a name="performance"></a>Wydajność

Charakterystyka wydajności dla obu trybów różnią się w zależności od przypadku użycia i trudno jest u uzasadnienia lub przedstawić ogólne zalecenia. Jeśli nie jesteś ograniczony przez wyżej wymienione ograniczenia (pamięć lub przezroczystość/przezroczystość), zaleca się wypróbowanie obu trybów i monitorowanie wydajności przy użyciu różnych pozycji kamery.

## <a name="setting-the-render-mode"></a>Ustawianie trybu renderowania

Tryb renderowania używany na maszynie wirtualnej `AzureSession.ConnectToRuntime` zdalnego renderowania jest określony podczas za pośrednictwem pliku `ConnectToRuntimeParams`.

```cs
async void ExampleConnect(AzureSession session)
{
    ConnectToRuntimeParams parameters = new ConnectToRuntimeParams();

    // Connect with one rendering mode
    parameters.mode = ServiceRenderMode.TileBasedComposition;
    await session.ConnectToRuntime(parameters).AsTask();

    session.DisconnectFromRuntime();

    // Wait until session.IsConnected == false

    // Reconnect with a different rendering mode
    parameters.mode = ServiceRenderMode.DepthBasedComposition;
    await session.ConnectToRuntime(parameters).AsTask();
}
```

## <a name="next-steps"></a>Następne kroki

* [Sesje](../concepts/sessions.md)
* [Składnik zastępowania stanu hierarchicznego](../overview/features/override-hierarchical-state.md)
