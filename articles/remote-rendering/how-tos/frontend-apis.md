---
title: Interfejsy API portalu Azure do uwierzytelniania
description: W tym artykule wyjaśniono, jak używać interfejsu API interfejsu frontendu języka C# do uwierzytelniania
author: florianborn71
ms.author: flborn
ms.date: 02/12/2010
ms.topic: how-to
ms.openlocfilehash: 04296a3dab61fdb569126abc1bc1f975d69e226d
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681352"
---
# <a name="use-the-azure-frontend-apis-for-authentication"></a>Używanie interfejsów API zaplecza platformy Azure do uwierzytelniania

W tej sekcji opisano, jak używać interfejsu API języka C# do uwierzytelniania.

## <a name="azurefrontendaccountinfo"></a>AzureFrontendAccountInfo

AzureFrontendAccountInfo służy do konfigurowania informacji ```AzureFrontend``` uwierzytelniania dla wystąpienia w zestawie SDK.

Ważne są następujące dziedziny:

```cs

    public class AzureFrontendAccountInfo
    {
        // Something akin to "<region>.mixedreality.azure.com"
        public string AccountDomain;

        // Can use one of:
        // 1) ID and Key.
        // 2) AuthenticationToken.
        // 3) AccessToken.
        public string AccountId = Guid.Empty.ToString();
        public string AccountKey = string.Empty;
        public string AuthenticationToken = string.Empty;
        public string AccessToken = string.Empty;
    }

```

W przypadku części _regionu_ w domenie użyj [regionu w pobliżu](../reference/regions.md).

Informacje o koncie można uzyskać z portalu zgodnie z opisem w akapicie [informacji o pobieraniu konta.](create-an-account.md#retrieve-the-account-information)

## <a name="azure-frontend"></a>Azure Frontend

Odpowiednie klasy ```AzureFrontend``` są ```AzureSession```i . ```AzureFrontend```służy do zarządzania kontem i funkcji na poziomie konta, która obejmuje: konwersję zasobów i tworzenie sesji renderowania. ```AzureSession```jest używany dla funkcji na poziomie sesji i obejmuje: aktualizację sesji, kwerendy, odnawianie i likwidowanie.

Każdy otwarty/utworzony ```AzureSession``` zachowa odwołanie do frontendu, który został utworzony. Aby czysto zamknąć, wszystkie sesje muszą być cofnięte alokacji przed frontonu zostaną cofnięte alokacji.

Rozdzielanie sesji nie zatrzyma maszyny Wirtualnej `AzureSession.StopAsync` na platformie Azure, musi być jawnie wywoływane.

Po utworzeniu sesji i oznaczeniu jej stanu jako gotowego można połączyć `AzureSession.ConnectToRuntime`się ze zdalnym czasem wykonywania renderowania za pomocą pliku .

### <a name="threading"></a>Wątkowość

Wszystkie wywołania asynchronii AzureSession i AzureFrontend są wykonywane w wątku w tle, a nie w wątku głównej aplikacji.

### <a name="conversion-apis"></a>Interfejsy API konwersji

Aby uzyskać więcej informacji na temat usługi konwersji, zobacz [interfejs API REST konwersji modelu](conversion/conversion-rest-api.md).

#### <a name="start-asset-conversion"></a>Rozpocznij konwersję zasobów

``` cs
private StartConversionAsync _pendingAsync = null;

void StartAssetConversion(AzureFrontend frontend, string modelName, string modelUrl, string assetContainerUrl)
{
    _pendingAsync = frontend.StartConversionAsync(
        new AssetConversionParams(modelName, modelUrl, assetContainerUrl));
    _pendingAsync.Completed +=
        (StartConversionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to start asset conversion!");
            }
        };

        _pendingAsync = null;
}
```

#### <a name="get-conversion-status"></a>Uzyskaj status konwersji

``` cs
private ConversionStatusAsync _pendingAsync = null
void GetConversionStatus(AzureFrontend frontend, string assetId)
{
    _pendingAsync = frontend.GetConversionStatusAsync(assetId);
    _pendingAsync.Completed +=
        (ConversionStatusAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to get status of asset conversion!");
            }

            _pendingAsync = null;
        };
}
```

### <a name="rendering-apis"></a>Renderowanie interfejsów API

Szczegółowe informacje na temat [zarządzania sesjami można znaleźć w interfejsie API REST zarządzania sesjami.](session-rest-api.md)

Sesja renderowania można utworzyć dynamicznie w usłudze lub już istniejący identyfikator sesji można "otworzyć" w obiekcie AzureSession.

#### <a name="create-rendering-session"></a>Tworzenie sesji renderowania

``` cs
private CreateSessionAsync _pendingAsync = null;
void CreateRenderingSession(AzureFrontend frontend, RenderingSessionVmSize vmSize, ARRTimeSpan maxLease)
{
    _pendingAsync = frontend.CreateNewRenderingSessionAsync(
        new RenderingSessionCreationParams(vmSize, maxLease));

    _pendingAsync.Completed +=
        (CreateSessionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to create session!");
            }
            _pendingAsync = null;
        };
}
```

#### <a name="open-an-existing-rendering-session"></a>Otwieranie istniejącej sesji renderowania

Otwarcie istniejącej sesji jest wywołaniem synchroniczną.

``` cs
void CreateRenderingSession(AzureFrontend frontend, string sessionId)
{
    AzureSession session = frontend.OpenRenderingSession(sessionId);
    // Query session status, etc.
}
```

#### <a name="get-current-rendering-sessions"></a>Pobierz bieżące sesje renderowania

``` cs
private SessionPropertiesArrayAsync _pendingAsync = null;
void GetCurrentRenderingSessions(AzureFrontend frontend)
{
    _pendingAsync = frontend.GetCurrentRenderingSessionsAsync();
    _pendingAsync.Completed +=
        (SessionPropertiesArrayAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to get current rendering sessions!");
            }
            _pendingAsync = null;
        };
}
```

### <a name="session-apis"></a>Interfejsy API sesji

#### <a name="get-rendering-session-properties"></a>Pobierz właściwości sesji renderowania

``` cs
private SessionPropertiesAsync _pendingAsync = null;
void GetRenderingSessionProperties(AzureSession session)
{
    _pendingAsync = session.GetPropertiesAsync();
    _pendingAsync.Completed +=
        (SessionPropertiesAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to get properties of session!");
            }
            _pendingAsync = null;
        };
}
```

#### <a name="update-rendering-session"></a>Aktualizacja sesji renderowania

``` cs
private SessionAsync _pendingAsync;
void UpdateRenderingSession(AzureSession session, ARRTimeSpan updatedLease)
{
    _pendingAsync = session.RenewAsync(
        new RenderingSessionUpdateParams(updatedLease));
    _pendingAsync.Completed +=
        (SessionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                Console.WriteLine("Rendering session renewed succeeded!");
            }
            else
            {
                Console.WriteLine("Failed to renew rendering session!");
            }
            _pendingAsync = null;
        };
}
```

#### <a name="stop-rendering-session"></a>Zatrzymywanie sesji renderowania

``` cs
private SessionAsync _pendingAsync;
void StopRenderingSession(AzureSession session)
{
    _pendingAsync = session.StopAsync();
    _pendingAsync.Completed +=
        (SessionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                Console.WriteLine("Rendering session stopped successfully!");
            }
            else
            {
                Console.WriteLine("Failed to stop rendering session!");
            }
            _pendingAsync = null;
        };
}
```

#### <a name="connect-to-arr-inspector"></a>Połącz się z inspektorem ARR

``` cs
private ArrInspectorAsync _pendingAsync = null;
void ConnectToArrInspector(AzureSession session, string hostname)
{
    _pendingAsync = session.ConnectToArrInspectorAsync(hostname);
    _pendingAsync.Completed +=
        (ArrInspectorAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                // Launch the html file with default browser
                string htmlPath = res.Result;
#if WINDOWS_UWP
                UnityEngine.WSA.Application.InvokeOnUIThread(async () =>
                {
                    var file = await Windows.Storage.StorageFile.GetFileFromPathAsync(htmlPath);
                    await Windows.System.Launcher.LaunchFileAsync(file);
                }, true);
#else
                InvokeOnAppThreadAsync(() =>
                {
                    System.Diagnostics.Process.Start("file:///" + htmlPath);
                });
#endif
            }
            else
            {
                Console.WriteLine("Failed to connect to ARR inspector!");
            }
        };
}
```

## <a name="next-steps"></a>Następne kroki

* [Tworzenie konta](create-an-account.md)
* [Przykładowe skrypty programu PowerShell](../samples/powershell-example-scripts.md)
