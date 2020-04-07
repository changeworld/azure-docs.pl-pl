---
title: Oprawa graficzna
description: Konfiguracja powiązań graficznych i przypadków użycia
author: florianborn71
manager: jlyons
services: azure-remote-rendering
titleSuffix: Azure Remote Rendering
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.service: azure-remote-rendering
ms.openlocfilehash: 8b5db0532f3dcc8b6dfb024238d0cacff2e6d2a1
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681885"
---
# <a name="graphics-binding"></a>Oprawa graficzna

Aby móc korzystać z renderowania zdalnego platformy Azure w aplikacji niestandardowej, musi być zintegrowany z potokiem renderowania aplikacji. Ta integracja jest odpowiedzialny za powiązanie grafiki.

Po skonfigurowaniu powiązania grafiki daje dostęp do różnych funkcji, które wpływają na renderowany obraz. Funkcje te można podzielić na dwie kategorie: ogólne funkcje, które są zawsze `Microsoft.Azure.RemoteRendering.GraphicsApiType`dostępne, oraz określone funkcje, które są istotne tylko dla wybranego .

## <a name="graphics-binding-in-unity"></a>Oprawa graficzna w unity

W unity, całe powiązanie jest `RemoteUnityClientInit` obsługiwane przez `RemoteManagerUnity.InitializeManager`struktury przekazywane do . Aby ustawić tryb graficzny, `GraphicsApiType` pole musi być ustawione na wybrane powiązanie. Pole zostanie automatycznie wypełnione w zależności od tego, czy xrdevice jest obecny. Zachowanie można ręcznie zastąpić następującymi zachowaniami:

* **HoloLens 2**: zawsze używane jest wiązanie [graficzne Windows Mixed Reality.](#windows-mixed-reality)
* **Płaska aplikacja klasyczna PLATFORMY UNIWERSALNEJ**SYSTEMU [Windows: Symulacja](#simulation) jest zawsze używana. Aby korzystać z tego trybu, wykonaj kroki opisane w [samouczku: Konfigurowanie projektu Unity od podstaw](../tutorials/unity/project-setup.md).
* **Edytor Unity:** [Symulacja](#simulation) jest zawsze używana, chyba że podłączony jest zestaw słuchawkowy WMR VR, w którym to przypadku ARR zostanie wyłączony, aby umożliwić debugowanie części aplikacji powiązanych z ARR. Zobacz też [holograficzna remoting](../how-tos/unity/holographic-remoting.md).

Jedyną istotną częścią unity jest dostęp do [podstawowego powiązania,](#access)wszystkie inne poniższe sekcje można pominąć.

## <a name="graphics-binding-setup-in-custom-applications"></a>Konfiguracja powiązania grafiki w aplikacjach niestandardowych

Aby wybrać powiązanie graficzne, należy wykonać następujące dwa kroki: Po pierwsze, powiązanie graficzne musi zostać zainicjowane statycznie podczas inicjowania programu:

``` cs
RemoteRenderingInitialization managerInit = new RemoteRenderingInitialization;
managerInit.graphicsApi = GraphicsApiType.WmrD3D11;
managerInit.connectionType = ConnectionType.General;
managerInit.right = ///...
RemoteManagerStatic.StartupRemoteRendering(managerInit);
```

Powyższe wywołanie jest niezbędne do zainicjowania zdalnego renderowania platformy Azure do holograficznych interfejsów API. Ta funkcja musi zostać wywołana przed wywołaniem dowolnego holograficznego interfejsu API i przed dostępem do innych interfejsów API zdalnego renderowania. Podobnie należy wywołać odpowiednią funkcję `RemoteManagerStatic.ShutdownRemoteRendering();` de-init po tym, jak nie są już wywoływane żadne holograficzne interfejsy API.

## <a name="span-idaccessaccessing-graphics-binding"></a><span id="access">Uzyskiwanie dostępu do powiązania grafiki

Po skonfigurowaniu klienta podstawowe powiązanie grafiki można uzyskać `AzureSession.GraphicsBinding` za pomocą metody getter. Na przykład statystyki ostatniej ramki można pobrać w następujący sposób:

``` cs
AzureSession currentSesson = ...;
if (currentSesson.GraphicsBinding)
{
    FrameStatistics frameStatistics;
    if (session.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        ...
    }
}
```

## <a name="graphic-apis"></a>Graficzne interfejsy API

Obecnie można wybrać dwa interfejsy API grafiki `WmrD3D11` `SimD3D11`oraz . Trzeci istnieje, `Headless` ale nie jest jeszcze obsługiwany po stronie klienta.

### <a name="windows-mixed-reality"></a>Windows Mixed Reality

`GraphicsApiType.WmrD3D11`jest domyślnym powiązaniem do uruchomienia na HoloLens 2. Spowoduje to `GraphicsBindingWmrD3d11` utworzenie powiązania. W tym trybie narzędzia Azure Remote Rendering łączy bezpośrednio holograficzne interfejsy API.

Aby uzyskać dostęp do pochodnych `GraphicsBinding` powiązań graficznych, podstawa musi zostać rzutowana.
Istnieją dwie rzeczy, które należy wykonać, aby użyć powiązania WMR:

#### <a name="inform-remote-rendering-of-the-used-coordinate-system"></a>Informowanie o zdalnym renderowaniu używanego układu współrzędnych

``` cs
AzureSession currentSesson = ...;
IntPtr ptr = ...; // native pointer to ISpatialCoordinateSystem
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
if (binding.UpdateUserCoordinateSystem(ptr) == Result.Success)
{
    ...
}
```

Gdzie powyższe `ptr` musi być wskaźnikiem do obiektu macierzystego, `ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem` który definiuje układ współrzędnych przestrzeni świata, w którym współrzędne w interfejsie API są wyrażone w.

#### <a name="render-remote-image"></a>Renderowanie obrazu zdalnego

Na początku każdej klatki zdalna ramka musi być renderowana do tylnego buforu. Odbywa się to `BlitRemoteFrame`przez wywołanie , który wypełni zarówno kolor i głębokość informacji do aktualnie powiązanego obiektu docelowego renderowania. W związku z tym ważne jest, aby to zrobić po powiązaniu buforu wstecznego jako docelowego renderowania.

``` cs
AzureSession currentSesson = ...;
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
binding.BlitRemoteFrame();
```

### <a name="simulation"></a>Symulacja

`GraphicsApiType.SimD3D11`jest oprawą symulacji i `GraphicsBindingSimD3d11` jeśli jest zaznaczona, tworzy oprawę graficzną. Ten interfejs służy do symulowania ruchu głowy, na przykład w aplikacji klasycznej i renderuje obraz monoskopowy.
Konfiguracja jest nieco bardziej zaangażowana i działa w następujący sposób:

#### <a name="create-proxy-render-target"></a>Tworzenie obiektu docelowego renderowania serwera proxy

Zawartość zdalna i lokalna musi być renderowana do celu renderowania kolorów / głębokości `GraphicsBindingSimD3d11.Update` poza ekranem o nazwie "proxy" przy użyciu danych z kamery proxy dostarczonych przez tę funkcję. Serwer proxy musi być zgodny z rozdzielczością buforu wstecznego. Gdy sesja jest `GraphicsBindingSimD3d11.InitSimulation` gotowa, należy wywołać przed nawiązaniem połączenia z nią:

``` cs
AzureSession currentSesson = ...;
IntPtr d3dDevice = ...; // native pointer to ID3D11Device
IntPtr color = ...; // native pointer to ID3D11Texture2D
IntPtr depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
simBinding.InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically);
```

Funkcja init musi być wyposażona w wskaźniki do macierzystego urządzenia d3d, a także do tekstury kolorów i głębi obiektu docelowego renderowania serwera proxy. Po zainicjowaniu `AzureSession.ConnectToRuntime` `DisconnectFromRuntime` i mogą być wywoływane wiele razy, `GraphicsBindingSimD3d11.DeinitSimulation` ale podczas przełączania do innej `GraphicsBindingSimD3d11.InitSimulation` sesji, musi być wywoływana najpierw na starej sesji, zanim może być wywołana na innej sesji.

#### <a name="render-loop-update"></a>Aktualizacja pętli renderowania

Aktualizacja pętli renderowania składa się z wielu kroków:

1. Każda klatka, przed renderowaniem, `GraphicsBindingSimD3d11.Update` jest wywoływana z bieżącą transformacją kamery, która jest wysyłana do serwera w celu renderowania. W tym samym czasie zwrócone transformacja serwera proxy powinny być stosowane do kamery proxy do renderowania do obiektu docelowego renderowania serwera proxy.
Jeśli zwrócona `SimulationUpdate.frameId` aktualizacja serwera proxy ma wartość null, nie ma jeszcze żadnych danych zdalnych. W takim przypadku zamiast renderowania do obiektu docelowego renderowania serwera proxy, wszelkie lokalne treści powinny być renderowane do buforu wstecznego bezpośrednio przy użyciu bieżących danych kamery i kolejne dwa kroki są pomijane.
1. Aplikacja powinna teraz powiązać obiekt `GraphicsBindingSimD3d11.BlitRemoteFrameToProxy`docelowy renderowania serwera proxy i wywołać . Spowoduje to wypełnienie zdalnych informacji o kolorze i głębi do obiektu docelowego renderowania serwera proxy. Wszystkie lokalne treści mogą być teraz renderowane na serwerze proxy za pomocą transformacji kamery proxy.
1. Następnie bufor z powrotem musi być powiązany jako obiekt docelowy renderowania i `GraphicsBindingSimD3d11.ReprojectProxy` wywoływany w tym momencie można przedstawić bufor wstecz.

``` cs
AzureSession currentSesson = ...;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
SimulationUpdate update = new SimulationUpdate();
// Fill out camera data with current camera data
...
SimulationUpdate proxyUpdate = new SimulationUpdate();
simBinding.Update(update, out proxyUpdate);
// Is the frame data valid?
if (proxyUpdate.frameId != 0)
{
    // Bind proxy render target
    simBinding.BlitRemoteFrameToProxy();
    // Use proxy camera data to render local content
    ...
    // Bind back buffer
    simBinding.ReprojectProxy();
}
else
{
    // Bind back buffer
    // Use current camera data to render local content
    ...
}
```

## <a name="next-steps"></a>Następne kroki

* [Samouczek: Konfigurowanie projektu Unity od podstaw](../tutorials/unity/project-setup.md)
