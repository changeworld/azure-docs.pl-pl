---
title: Konfigurowanie projektu Unity od podstaw
description: W tym artykule wyjaśniono, jak skonfigurować pusty projekt Unity do użytku z renderowaniem zdalnym platformy Azure.
author: florianborn71
ms.author: flborn
ms.date: 01/30/2020
ms.topic: tutorial
ms.openlocfilehash: 33801316e4c0446865169560bb42f98052acba70
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679597"
---
# <a name="tutorial-setting-up-a-unity-project-from-scratch"></a>Samouczek: Konfigurowanie projektu Unity od podstaw

Ten samouczek zawiera następujące informacje:

> [!div class="checklist"]
>
> * Konfigurowanie projektu zarysowania Unity dla ARR.
> * Tworzenie i zatrzymywanie sesji renderowania.
> * Ponowne czesanie istniejących sesji.
> * Łączenie i odłączanie się od sesji.
> * Ładowanie modeli do sesji renderowania.
> * Wyświetlanie statystyk połączeń.

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku potrzebujesz:

* Informacje o koncie (identyfikator konta, klucz konta, identyfikator subskrypcji). Jeśli nie masz konta, [utwórz konto](../../how-tos/create-an-account.md).
* Windows SDK 10.0.18362.0 [(do pobrania)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* Najnowsza wersja programu Visual Studio 2019 [(do pobrania)](https://visualstudio.microsoft.com/vs/older-downloads/)
* GIT [(do pobrania)](https://git-scm.com/downloads)
* Unity 2019.3.1 [(do pobrania)](https://unity3d.com/get-unity/download)
  * Zainstaluj te moduły w unity:
    * **Uwp** — obsługa kompilacji platformy systemu Windows
    * **IL2CPP** - Obsługa kompilacji systemu Windows (IL2CPP)

> [!TIP]
> [Repozytorium przykładów ARR](https://github.com/Azure/azure-remote-rendering) zawiera przygotowane projekty Unity dla wszystkich samouczków. Można użyć tych projektów jako odwołania.

## <a name="create-a-new-unity-project"></a>Tworzenie nowego projektu Unity

Z Unity Hub, utwórz nowy projekt.
W tym przykładzie zakładamy, że projekt jest `RemoteRendering`tworzony w folderze o nazwie .

![nowe okno projektu](media/new-project.png)

## <a name="configure-the-projects-manifest"></a>Konfigurowanie manifestu projektu

Należy zmodyfikować plik, `Packages/manifest.json` który znajduje się w folderze projektu Unity. Otwórz plik w edytorze tekstu i dołącz wiersze wymienione poniżej:

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
    "com.unity.render-pipelines.universal": "7.2.1",
    ...existing dependencies...
  }
}
```

Pakiet potoku renderowania uniwersalnego jest opcjonalny, ale zalecane ze względu na wydajność.
Po zmodyfikowaniu i zapisaniu manifestu unity zostanie automatycznie odświeżona. Upewnij się, że pakiety zostały załadowane w oknie *Projekt:*

![potwierdzanie importu pakietów](media/confirm-packages.png)

## <a name="ensure-you-have-the-latest-version-of-the-package"></a>Upewnij się, że masz najnowszą wersję pakietu

Poniższe kroki upewnij się, że projekt korzysta z najnowszej wersji pakietu renderowania zdalnego.
1. Wybierz pakiet w oknie Projekt i kliknij ![ikonę pakietu: Wybieranie ikony pakietu](media/package-icons.png)
1. W inspektorze kliknij "Wyświetl w ![Menedżerze pakietów": inspektor pakietów](media/package-properties.png)
1. Na stronie Menedżera pakietów dla pakietu zdalnego renderowania sprawdź, czy przycisk aktualizacji jest dostępny. Jeśli tak jest, kliknięcie go spowoduje zaktualizowanie pakietu ![do najnowszej dostępnej wersji: Pakiet ARR w menedżerze pakietów](media/package-manager.png)
1. Czasami aktualizowanie pakietu może prowadzić do błędów w konsoli. W takim przypadku spróbuj zamknąć i ponownie otworzyć projekt.

## <a name="configure-the-camera"></a>Konfigurowanie kamery

Wybierz węzeł **Kamera główna.**

1. Zresetuj *transformację:*

    ![resetowanie transformacji kamery](media/camera-reset-transform.png)

1. Ustaw **wyczyść flagi** na *Jednolity kolor*

1. Ustawianie **tła** na *czarne*

1. Ustaw **płaszczyzny przycinania** na *Blisko = 0,3* i *Daleko = 20*. Oznacza to, że renderowanie przycina geometrię, która jest bliższa 30 cm lub dalej niż 20 metrów.

    ![Właściwości kamery Unity](media/camera-properties.png)

## <a name="adjust-the-project-settings"></a>Dostosowywanie ustawień projektu

1. Otwórz *pozycję Edytuj > ustawienia projektu...*
1. Na liście po lewej stronie wybierz pozycję Jakość.
1. Zmiana **domyślnego poziomu jakości** na *Niski*

    ![zmienianie ustawień jakości projektu](media/settings-quality.png)

1. Wybierz **pozycję Grafika** po lewej stronie.
1. Zmień ustawienie **potoku renderowania skryptowego** na *HybridRenderingPipeline*. Pomiń ten krok, jeśli potok renderowania uniwersalnego nie jest używany.

    ![zmiana ustawień](media/settings-graphics-lwrp.png) grafiki projektu Czasami interfejs użytkownika nie wypełnia listy dostępnych typów potoku z pakietów, w którym to przypadku zasób ![ *HybridRenderingPipeline* musi zostać przeciągnięty do pola ręcznie: zmiana ustawień grafiki projektu](media/hybrid-rendering-pipeline.png)
1. Wybierz **opcję Gracz** po lewej stronie.
1. Wybieranie karty **Ustawienia uniwersalnej platformy systemu Windows**
1. Zmienianie **ustawień XR** w celu ![obsługi ustawień systemu Windows Mixed Reality: player](media/xr-player-settings.png)
1. Wybierz ustawienia, jak na powyższym zrzucie ekranu:
    1. Włącz **obsługę wirtualnej rzeczywistości**
    1. Ustawianie **formatu głębokości** na *głębokość 16-bitową*
    1. Włącz **udostępnianie buforu głębokości**
    1. Ustawianie **trybu renderowania stereo** na *wystąpienie pojedynczego przejścia*

1. W tym samym oknie powyżej *ustawienia XR*rozwiń rozwiń ustawienia **publikowania**
1. Przewiń w dół do **opcji** i wybierz:
    * **InternetClient (InternetClient)**
    * **Serwer internetowy**
    * **Przestrzennepercepcja**
    * Opcjonalnie dla rozwoju: **PrivateNetworkClientServer**

      Ta opcja jest potrzebna, jeśli chcesz podłączyć debuger zdalny Unity do urządzenia.

1. W **obsługiwanych rodzinach urządzeń**włącz **holografię** i **pulpit**

1. Jeśli chcesz użyć zestawu narzędzi rzeczywistości mieszanej, zapoznaj się z [dokumentacją MRTK](https://docs.microsoft.com/windows/mixed-reality/unity-development-overview), aby uzyskać więcej informacji na temat zalecanych ustawień i możliwości.

## <a name="validate-project-setup"></a>Sprawdzanie poprawności konfiguracji projektu

Wykonaj następujące kroki, aby sprawdzić, czy ustawienia projektu są poprawne.

1. Wybierz wpis ValidateProject z menu RemoteRendering na pasku narzędzi edytora Unity.
1. Okno ValidateProject służy do sprawdzania i naprawianie ustawień projektu w razie potrzeby.

    ![Sprawdzanie poprawności projektu edytora Unity](media/arr-unity-validation.png)

## <a name="create-a-script-to-initialize-azure-remote-rendering"></a>Tworzenie skryptu w celu zainicjowania zdalnego renderowania platformy Azure

Utwórz [nowy skrypt](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) i nadaj mu nazwę **RemoteRendering**. Otwórz plik skryptu i zastąp całą jego zawartość poniższym kodem:

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;

#if UNITY_WSA
    using UnityEngine.XR.WSA;
#endif

// ask Unity to automatically append an ARRServiceUnity component when a RemoteRendering script is attached
[RequireComponent(typeof(ARRServiceUnity))]
public class RemoteRendering : MonoBehaviour
{
    // fill out the variables below with your account details

    // AccountDomain must be '<region>.mixedreality.azure.com' - if no '<region>' is specified, connections will fail
    // See the documentation for the list of available regions.
    public string AccountDomain = "westus2.mixedreality.azure.com";
    public string AccountId = "<enter your account id here>";
    public string AccountKey = "<enter your account key here>";

    public uint MaxLeaseTimeHours = 0;
    public uint MaxLeaseTimeMinutes = 10;
    public RenderingSessionVmSize VMSize = RenderingSessionVmSize.Standard;

    private ARRServiceUnity arrService = null;

    private void Awake()
    {
        // initialize Azure Remote Rendering for use in Unity:
        // it needs to know which camera is used for rendering the scene
        RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
        RemoteManagerUnity.InitializeManager(clientInit);

        // lookup the ARRServiceUnity component and subscribe to session events
        arrService = GetComponent<ARRServiceUnity>();
        arrService.OnSessionErrorEncountered += ARRService_OnSessionErrorEncountered;
        arrService.OnSessionStatusChanged += ARRService_OnSessionStatusChanged;
    }

#if !UNITY_EDITOR
    private void Start()
    {
        StartCoroutine(AutoStartSessionAsync());
    }
#endif

    private void OnDestroy()
    {
        arrService.OnSessionErrorEncountered -= ARRService_OnSessionErrorEncountered;
        arrService.OnSessionStatusChanged -= ARRService_OnSessionStatusChanged;

        RemoteManagerStatic.ShutdownRemoteRendering();
    }

    private void CreateFrontend()
    {
        if (arrService.Frontend != null)
        {
            // early out if the front-end has been created before
            return;
        }

        // initialize the ARR service with our account details
        AzureFrontendAccountInfo accountInfo = new AzureFrontendAccountInfo();
        accountInfo.AccountKey = AccountKey;
        accountInfo.AccountId = AccountId;
        accountInfo.AccountDomain = AccountDomain;

        arrService.Initialize(accountInfo);
    }

    public void CreateSession()
    {
        CreateFrontend();

        // StartSession will call ARRService_OnSessionStarted once the session becomes available
        arrService.StartSession(new RenderingSessionCreationParams(VMSize, MaxLeaseTimeHours, MaxLeaseTimeMinutes));
    }

    public void StopSession()
    {
        arrService.StopSession();
    }

    private void ARRService_OnSessionStatusChanged(ARRServiceUnity caller, AzureSession session)
    {
        LogSessionStatus(session);
    }

    private void ARRService_OnSessionErrorEncountered(ARRServiceUnity caller, SessionGeneralContext context)
    {
        Debug.LogError($"Session error encountered. Context: {context.ErrorMessage}. Request Cv: {context.RequestCorrelationVector}. Response Cv: {context.ResponseCorrelationVector}");
    }

    private async void LogSessionStatus(AzureSession session)
    {
        if (session != null)
        {
            var sessionProperties = await session.GetPropertiesAsync().AsTask();
            LogSessionStatus(sessionProperties);
        }
        else
        {
            var sessionProperties = arrService.LastProperties;
            Debug.Log($"Session ended: Id={sessionProperties.Id}");
        }
    }

    private void LogSessionStatus(RenderingSessionProperties sessionProperties)
    {
        Debug.Log($"Session '{sessionProperties.Id}' is {sessionProperties.Status}. Size={sessionProperties.Size}" +
            (!string.IsNullOrEmpty(sessionProperties.Hostname) ? $", Hostname='{sessionProperties.Hostname}'" : "") +
            (!string.IsNullOrEmpty(sessionProperties.Message) ? $", Message='{sessionProperties.Message}'" : ""));
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
    }
#endif
}
```

Ten skrypt inicjuje zdalne renderowanie platformy Azure, informuje go, który obiekt kamery ma być używany do renderowania, i umieszcza przycisk **Utwórz sesję** w rzutni po uaktywnieniu *trybu odtwarzania.*

> [!CAUTION]
> Modyfikowanie skryptu i zapisywanie go, gdy tryb odtwarzania jest aktywny w Unity może spowodować zamrożenie unity i jesteś zmuszony do zamknięcia go za pośrednictwem menedżera zadań. W związku z tym zawsze zatrzymać tryb odtwarzania przed edycją *skryptu RemoteRendering.*

## <a name="test-azure-remote-rendering-session-creation"></a>Testowanie tworzenia sesji zdalnego renderowania platformy Azure

Utwórz nowy gameobject w scenie i dodaj do niego składnik *RemoteRendering.* Wypełnij odpowiednią *domenę konta,* *identyfikator konta*i klucz *konta* dla konta zdalnego renderowania:

![Właściwości składnika renderowania zdalnego](media/remote-rendering-component.png)

Uruchom aplikację w edytorze **(naciśnij przycisk Odtwórz** lub CTRL+P). W rzutni powinien pojawić się przycisk **Utwórz sesję.** Kliknij ją, aby rozpocząć pierwszą sesję ARR:

![Tworzenie pierwszej sesji](media/test-create.png)

Jeśli to się nie powiedzie, upewnij się, że dane konta zostały poprawnie wprowadzone we właściwościach składnika RemoteRendering. W przeciwnym razie w oknie konsoli pojawi się komunikat z przypisanym identyfikatorem sesji i informacją, że sesja jest obecnie w stanie *początkowym:*

![Wyjście początkowe sesji](media/create-session-output.png)

W tym momencie platforma Azure inicjuje inicjowanie obsługi administracyjnej serwera i uruchamianie maszyny wirtualnej renderowania zdalnego. Zwykle trwa to **od 3 do 5 minut.** Gdy maszyna wirtualna jest gotowa, `OnSessionStatusChanged` wywołanie zwrotne naszego skryptu Unity jest wykonywane i wydrukuje nowy stan sesji:

![Wyjście gotowe do sesji](media/create-session-output-2.png)

To jest to! Na razie nic więcej się nie stanie. Aby zapobiec naliczaniu opłat, należy zawsze przerywać sesje, gdy nie są już potrzebne. W tym przykładzie można to zrobić, klikając przycisk **Zatrzymaj sesję** lub zatrzymując symulację Unity. Ze względu na **auto-stop session** właściwość w *składniku ARRServiceUnity,* który jest domyślnie włączony, sesja zostanie zatrzymana automatycznie dla Ciebie. Jeśli wszystko nie powiedzie się, z powodu awarii lub problemów z połączeniem, sesja może działać tak długo, jak *MaxLeaseTime* przed zamknięciem przez serwer.

> [!NOTE]
> Zatrzymanie sesji będzie natychmiastowe i nie można jej cofnąć. Po zatrzymaniu należy utworzyć nową sesję z tym samym obciążeniem dla uruchamiania.

## <a name="reusing-sessions"></a>Ponowne odtwarzanie sesji

Tworzenie nowej sesji jest niestety czasochłonną operacją. W związku z tym należy spróbować tworzyć sesje rzadko i używać ich ponownie, gdy tylko jest to możliwe.

Wstaw następujący kod do skryptu *RemoteRendering* i usuń stare wersje zduplikowanych funkcji:

```csharp
    public string SessionId = null;

    private void ARRService_OnSessionStatusChanged(ARRServiceUnity caller, AzureSession session)
    {
        LogSessionStatus(session);

        var status = caller.LastProperties.Status;

        // Capture the session id for the starting/ready session so it can be reused
        if (status == RenderingSessionStatus.Ready || status == RenderingSessionStatus.Starting)
        {
            SessionId = session.SessionUUID;
        }
        // Our session is stopped, expired, or in an error state.
        else
        {
            SessionId = null;
        }
    }

    public async void QueryActiveSessions()
    {
        CreateFrontend();

        var allSessionsProperties = await arrService.Frontend.GetCurrentRenderingSessionsAsync().AsTask();

        Debug.Log($"Number of active sessions: {allSessionsProperties.Length}");

        foreach (var sessionProperties in allSessionsProperties)
        {
            if (string.IsNullOrEmpty(SessionId))
            {
                Debug.Log($"Number of active sessions: {allSessionsProperties.Length}");
                SessionId = sessionProperties.Id;
            }

            LogSessionStatus(sessionProperties);
        }
    }

    public void UseExistingSession()
    {
        CreateFrontend();

        // OpenSession will call ARRService_OnSessionStarted once the session becomes available
        arrService.OpenSession(SessionId);
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }

            if (GUI.Button(new Rect(10, 50, 175, 30), "Query Active Sessions"))
            {
                QueryActiveSessions();
            }

            if (!string.IsNullOrEmpty(SessionId))
            {
                if (GUI.Button(new Rect(10, 90, 175, 30), "Use Existing Session"))
                {
                    UseExistingSession();
                }
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
    }
#endif
```

> [!CAUTION]
> Przed uruchomieniem tego kodu należy dezaktywować opcję **Auto-Stop Session** w składniku RemoteRendering. W przeciwnym razie każda utworzona sesja zostanie automatycznie zatrzymana po zatrzymaniu symulacji, a próba ponownego użycia zakończy się niepowodzeniem.

Po *naciśnięciu przycisku Odtwórz*otrzymujesz teraz trzy przyciski w rzutni: **Tworzenie sesji,** **Sesje aktywne kwerendy**i **Używanie istniejącej sesji**. Pierwszy przycisk zawsze tworzy nową sesję. Drugi przycisk kwerendy, które *istnieją aktywne* sesje. Jeśli nie określono ręcznie identyfikatora sesji, który ma być używany, ta akcja automatycznie wybierze ten identyfikator sesji do wykorzystania w przyszłości. Trzeci przycisk próbuje połączyć się z istniejącą sesją. Albo ten określony ręcznie za pomocą właściwości *składnika Identyfikator sesji,* albo ten znaleziony przez *sesje aktywne kwerendy*.

**Funkcja AutoStartSessionAsync** służy do symulowania naciśnięć przycisku poza edytorem.

> [!TIP]
> Istnieje możliwość otwarcia sesji, które zostały zatrzymane, wygasły lub są w stanie błędu. Chociaż nie można ich już używać do renderowania, można zbadać ich szczegóły po otwarciu nieaktywnej sesji. Powyższy kod sprawdza stan sesji `ARRService_OnSessionStarted`w , aby automatycznie zatrzymać, gdy sesja stała się bezużyteczna.

Dzięki tej funkcji można teraz tworzyć i ponownie używać sesji, co powinno znacznie poprawić przepływ pracy deweloperów.

Zazwyczaj tworzenie sesji zostanie wyzwolone poza aplikacją kliencką ze względu na czas wymagany do uruchomienia serwera.

## <a name="connect-to-an-active-session"></a>Łączenie się z aktywną sesją

Do tej pory stworzyliśmy lub otworzyliśmy sesje. Następnym krokiem jest *połączenie z* sesją. Po podłączeniu serwer renderowania będzie produkował obrazy i wysyłał strumień wideo do naszej aplikacji.

Wstaw następujący kod do skryptu *RemoteRendering* i usuń stare wersje zduplikowanych funkcji:

```csharp
    private bool isConnected = false;

    private void ARRService_OnSessionStatusChanged(ARRServiceUnity caller, AzureSession session)
    {
        LogSessionStatus(session);

        var status = caller.LastProperties.Status;

        // Capture the session id for the starting/ready session so it can be reused
        if (status == RenderingSessionStatus.Ready || status == RenderingSessionStatus.Starting)
        {
            SessionId = session.SessionUUID;

            // If our session properties are 'Ready' we are ready to start connecting to the runtime of the service.
            if (status == RenderingSessionStatus.Ready)
            {
                session.ConnectionStatusChanged += AzureSession_OnConnectionStatusChanged;
            }
        }
        // Our session is stopped, expired, or in an error state.
        else
        {
            SessionId = null;
            session.ConnectionStatusChanged -= AzureSession_OnConnectionStatusChanged;
        }
    }

    private void AzureSession_OnConnectionStatusChanged(ConnectionStatus status, Result result)
    {
        Debug.Log($"Connection status: '{status}', result: '{result}'");
        isConnected = (status == ConnectionStatus.Connected);
    }

    public void ConnectSession()
    {
        arrService.CurrentActiveSession?.ConnectToRuntime(new ConnectToRuntimeParams());
    }

    public void DisconnectSession()
    {
        if (isConnected)
        {
            arrService.CurrentActiveSession?.DisconnectFromRuntime();
        }
    }

    private void LateUpdate()
    {
        // The session must have its runtime pump updated.
        // The update will push messages to the server, receive messages, and update the frame-buffer with the remotely rendered content.
        arrService.CurrentActiveSession?.Actions.Update();
    }

    private void OnDisable()
    {
        DisconnectSession();
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }

            if (GUI.Button(new Rect(10, 50, 175, 30), "Query Active Sessions"))
            {
                QueryActiveSessions();
            }

            if (!string.IsNullOrEmpty(SessionId))
            {
                if (GUI.Button(new Rect(10, 90, 175, 30), "Use Existing Session"))
                {
                    UseExistingSession();
                }
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }

            if (arrService.LastProperties.Status == RenderingSessionStatus.Ready)
            {
                if (!isConnected)
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Connect"))
                    {
                        ConnectSession();
                    }
                }
                else
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Disconnect"))
                    {
                        DisconnectSession();
                    }
                }
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
        while (arrService.CurrentActiveSession == null)
        {
            yield return null;
        }
        ConnectSession();
    }
#endif
```

Aby przetestować tę funkcję:

1. Naciśnij **przycisk Odtwórz** w Unity.
1. Otwórz sesję:
    1. Jeśli masz już sesję, naciśnij klawisz **Query Active Sessions,** a następnie **użyj istniejącej sesji**.
    1. W przeciwnym razie naciśnij klawisz **Utwórz sesję**.
1. Naciśnij **przycisk Połącz**.
1. Po kilku sekundach wyjście konsoli powinno zostać wydrukowane, które są podłączone.
1. Na razie nic więcej nie powinno się wydarzyć.
1. Naciśnij **przycisk Rozłącz** lub zatrzymaj tryb odtwarzania Unity.

>[!NOTE]
> Wielu użytkowników może *otworzyć* sesję, aby zbadać jej informacje, ale tylko jeden użytkownik może być *połączony z* sesją naraz. Jeśli inny użytkownik jest już podłączony, połączenie zakończy się niepowodzeniem z **błędem uzgadniania**.

## <a name="load-a-model"></a>Ładowanie modelu

Wstaw następujący kod do skryptu *RemoteRendering* i usuń stare wersje zduplikowanych funkcji:

```csharp

    public string ModelName = "builtin://Engine";

    private Entity modelEntity = null;
    private GameObject modelEntityGO = null;

#if UNITY_WSA
    private WorldAnchor modelWorldAnchor = null;
#endif

    public async void LoadModel()
    {
        // create a root object to parent a loaded model to
        modelEntity = arrService.CurrentActiveSession.Actions.CreateEntity();

        // get the game object representation of this entity
        modelEntityGO = modelEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        // ensure the entity will sync translations with the server
        var sync = modelEntityGO.GetComponent<RemoteEntitySyncObject>();
        sync.SyncEveryFrame = true;

        // set position to an arbitrary distance from the parent
        modelEntityGO.transform.position = Camera.main.transform.position + Camera.main.transform.forward * 2;
        modelEntityGO.transform.localScale = Vector3.one;

#if UNITY_WSA
        // anchor the model in the world
        modelWorldAnchor = modelEntityGO.AddComponent<WorldAnchor>();
#endif

        // load a model that will be parented to the entity
        // We are using the 'from SAS' flavor because that variant can load the built-in model
        var loadModelParams = new LoadModelFromSASParams(ModelName, modelEntity);
        var async = arrService.CurrentActiveSession.Actions.LoadModelFromSASAsync(loadModelParams);
        async.ProgressUpdated += (float progress) =>
        {
            Debug.Log($"Loading: {progress * 100.0f}%");
        };

        await async.AsTask();
    }

    public void DestroyModel()
    {
        if (modelEntity == null)
        {
            return;
        }

#if UNITY_WSA
        DestroyImmediate(modelWorldAnchor);
#endif

        modelEntity.Destroy();
        modelEntity = null;

        DestroyImmediate(modelEntityGO);
    }

    public void DisconnectSession()
    {
        if (isConnected)
        {
            arrService.CurrentActiveSession?.DisconnectFromRuntime();
        }

        DestroyModel();
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }

            if (GUI.Button(new Rect(10, 50, 175, 30), "Query Active Sessions"))
            {
                QueryActiveSessions();
            }

            if (!string.IsNullOrEmpty(SessionId))
            {
                if (GUI.Button(new Rect(10, 90, 175, 30), "Use Existing Session"))
                {
                    UseExistingSession();
                }
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }

            if (arrService.LastProperties.Status == RenderingSessionStatus.Ready)
            {
                if (!isConnected)
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Connect"))
                    {
                        ConnectSession();
                    }
                }
                else
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Disconnect"))
                    {
                        DisconnectSession();
                    }

                    if (modelEntity == null)
                    {
                        if (GUI.Button(new Rect(10, 90, 175, 30), "Load Model"))
                        {
                            LoadModel();
                        }
                    }
                    else
                    {
                        if (GUI.Button(new Rect(10, 90, 175, 30), "Destroy Model"))
                        {
                            DestroyModel();
                        }
                    }
                }
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
        while (arrService.CurrentActiveSession == null)
        {
            yield return null;
        }
        ConnectSession();
        while (!isConnected)
        {
            yield return null;
        }
        LoadModel();
    }
#endif
```

Po naciśnięciu przycisku odtwarzania, otwarciu sesji i nawiązaniu z nią **przycisku Załaduj model** zostanie wyświetlony przycisk Załaduj model. Po kliknięciu go wyjście konsoli pokaże postęp ładowania, a gdy osiągnie 100% powinien pojawić się model silnika:

![Model załadowany do edytora](media/model-loaded-replace-me.png)

[WorldAnchor](https://docs.unity3d.com/ScriptReference/XR.WSA.WorldAnchor.html) jest ważnym elementem używanym do [stabilności hologramu.](https://docs.microsoft.com/windows/mixed-reality/hologram-stability) Jednak będzie to miało wpływ tylko wtedy, gdy zostanie wdrożony na urządzeniu rzeczywistości mieszanej.

> [!TIP]
> Jeśli po [szybki start: Konwersja modelu do renderowania](../../quickstarts/convert-model.md), już wiesz, jak przekonwertować własne modele. Wszystko, co musisz zrobić teraz, aby go renderować, jest umieszczenie identyfikatora URI do przekonwertowanego modelu do *właściwości Nazwa modelu.*

## <a name="display-frame-statistics"></a>Wyświetlanie statystyk ramki

Zdalne renderowanie platformy Azure śledzi różne informacje o jakości połączenia. Aby szybko wyświetlić te informacje, wykonaj następujące czynności:

Utwórz [nowy skrypt](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) i nadaj mu nazwę **RemoteFrameStats**. Otwórz plik skryptu i zastąp całą jego zawartość poniższym kodem:

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;

public class RemoteFrameStats : MonoBehaviour
{
    public Text FrameStats = null;

    ARRServiceStats arrServiceStats = null;

#if UNITY_EDITOR
    private void OnEnable()
    {
        arrServiceStats = new ARRServiceStats();
    }

    void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            FrameStats.text = string.Empty;
            return;
        }

        arrServiceStats.Update(RemoteManagerUnity.CurrentSession);

        if (FrameStats != null)
        {
            FrameStats.text = arrServiceStats.GetStatsString();
        }
    }
#endif
}
```

Utwórz GameObject i nadaj jej nazwę *FrameStats*. Dołącz go jako węzeł podrzędny do obiektu *Kamera główna* i ustaw jego położenie na **x = 0, y = 0, z = 0,325**. Dodaj składnik **RemoteFrameStats** do obiektu.

Dodaj obiekt podrzędny **interfejsu użytkownika > kanwy** do obiektu *FrameStats* i ustaw jego właściwości w ten sposób:

![właściwości płótna](media/framestats-canvas.png)

Dodaj obiekt **interfejsu użytkownika > Text** jako obiekt podrzędny obszaru roboczego i ustaw jego właściwości w następujący sposób:

![właściwości tekstu](media/framestats-text.png)

Zaznacz obiekt *FrameStats* i wypełnij **pole FrameStats,** klikając ikonę okręgu i zaznaczając obiekt **Tekst:**

![ustawianie właściwości tekstowej](media/framestats-set-text.png)

Teraz, po podłączeniu do sesji zdalnej, tekst powinien wyświetlać statystyki przesyłania strumieniowego:

![wyjście statystyk ramki](media/framestats-output.png)

Kod wyłącza aktualizację statystyk poza edytorem, ponieważ pole tekstowe zablokowane głową byłoby rozpraszające. Bardziej wyrafinowana implementacja znajduje się w projekcie [Szybki start.](../../quickstarts/render-model.md)

## <a name="next-steps"></a>Następne kroki

W tym samouczku poznaliście wszystkie kroki niezbędne do podjęcia pustego projektu Unity i uzyskania go do pracy z renderowaniem zdalnym platformy Azure. W następnym samouczku przyjrzymy się bliżej, jak pracować z jednostkami zdalnymi.

> [!div class="nextstepaction"]
> [Samouczek: Praca z jednostkami zdalnymi w unity](working-with-remote-entities.md)
