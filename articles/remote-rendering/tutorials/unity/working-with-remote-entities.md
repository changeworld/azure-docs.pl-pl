---
title: Praca z jednostkami zdalnymi w unity
description: Samouczek, który pokazuje, jak pracować z jednostkami ARR.
author: florianborn71
ms.author: flborn
ms.date: 02/01/2020
ms.topic: tutorial
ms.openlocfilehash: 97b2a0e48907ebe5b1cafdf92d14e9a4c6a28360
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679534"
---
# <a name="tutorial-working-with-remote-entities-in-unity"></a>Samouczek: Praca z jednostkami zdalnymi w unity

[Samouczek: Konfigurowanie projektu Unity od podstaw](project-setup.md) pokazało, jak skonfigurować nowy projekt Unity do pracy z renderowaniem zdalnym platformy Azure. W tym samouczku przyjrzymy się najbardziej typowe funkcje, które każdy użytkownik ARR potrzebuje.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Wybieraj obiekty za pomocą promieni odlewanych.
> * Zastądnij stany obiektu, takie jak kolor tinty, stan zaznaczenia i widoczność.
> * Usuwanie encji zdalnych.
> * Przenoszenie zdalnych elementów.
> * Użyj wyciętych płaszczyzn, aby zajrzeć do wnętrza obiektów.

## <a name="prerequisites"></a>Wymagania wstępne

* Ten samouczek opiera się na [tutorialu: Konfigurowanie projektu Unity od podstaw](project-setup.md).

> [!TIP]
> [Repozytorium przykładów ARR](https://github.com/Azure/azure-remote-rendering) zawiera przygotowane projekty Unity dla wszystkich samouczków w folderze *Unity,* których można użyć jako odwołanie.

## <a name="pick-objects"></a>Wybieranie obiektów

Chcemy wchodzić w interakcje z obiektami, więc pierwszą rzeczą, której potrzebujemy, jest zbieranie obiektów pod kursorem myszy.

Utwórz [nowy skrypt](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) o nazwie **RemoteRaycaster** i zastąp całą jego zawartość poniższym kodem:

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System.Collections;
using System.Collections.Generic;
using System.Threading.Tasks;
using UnityEngine;

[RequireComponent(typeof(ARRServiceUnity))]
public class RemoteRaycaster : MonoBehaviour
{
    public double MaxDistance = 30.0;

    private ARRServiceUnity arrService = null;

    private void Awake()
    {
        arrService = GetComponent<ARRServiceUnity>();
    }

    private async Task<Entity> RemoteRayCast(Vector3 origin, Vector3 dir)
    {
        Entity entity = null;

        var raycast = new RayCast(origin.toRemotePos(), dir.toRemoteDir(), MaxDistance, HitCollectionPolicy.ClosestHit);

        var hits = await arrService.CurrentActiveSession.Actions.RayCastQueryAsync(raycast).AsTask();

        if (hits != null)
        {
            foreach (var hit in hits)
            {
                var hitEntity = hit.HitEntity;
                if (hitEntity == null)
                {
                    continue;
                }

                entity = hitEntity;
                break;
            }
        }

        return entity;
    }

    private void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        Vector3 position = Input.mousePresent ? Input.mousePosition : new Vector3(Screen.width / 2, Screen.height / 2, 0);
        var ray = Camera.main.ScreenPointToRay(position);

        Raycast(ray.origin, ray.direction);
    }

    public async void Raycast(Vector3 origin, Vector3 direction)
    {
        var entity = await RemoteRayCast(origin, direction);
        if (entity != null)
        {
            Debug.Log("Object Hit: " + entity.Name);
        }
    }
}
```

Dodaj ten składnik do obiektu *RemoteRendering* w scenie.

> [!WARNING]
>
> Składnik *RemoteRaycaster* wymaga, aby składnik *ARRServiceUnity* został dołączony do tego samego obiektu. *ARRServiceUnity* jest klasą pomocnika, aby uzyskać dostęp do niektórych funkcji ARR łatwiej. Jednak w scenie może istnieć tylko jedno wystąpienie tego składnika. W związku z tym należy dodać wszystkie składniki, które wymagają *ARRServiceUnity* do tego samego GameObject.
> Jeśli chcesz uzyskać dostęp do funkcji ARR z wielu obiektów gry, dodaj składnik *ARRServiceUnity* tylko do jednego z nich i odwołaj się do tego w innych skryptach lub uzyskaj bezpośredni dostęp do funkcji ARR.

Naciśnij przycisk odtwarzania, połącz się z sesją i załaduj model. Teraz wskaż obiekty w scenie i obserwuj wyjście konsoli. Należy wydrukować nazwę obiektu każdej części, nad którą najechasz kursorem.

## <a name="highlight-objects"></a>Wyróżnianie obiektów

Następnym krokiem chcemy przekazać wizualną informację zwrotną, na które części modelu wskazuje użytkownik. Aby to osiągnąć, dołączamy [HierarchiczneStateOverrideComponent](../../overview/features/override-hierarchical-state.md) do jednostki, którą wybraliśmy. Ten składnik może służyć do włączania lub wyłączania różnych funkcji na obiekcie. Tutaj używamy go, aby ustawić kolor odcienia i włączyć [renderowanie konturu](../../overview/features/outlines.md).

Utwórz inny plik skryptu o nazwie **RemoteModelEntity** i zastąp jego zawartość następującym kodem:

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;

public class RemoteModelEntity : MonoBehaviour
{
    public Color HighlightColor = new Color(1.0f, 0.0f, 0.0f, 0.1f);

    public Entity Entity => localSyncObject != null ? localSyncObject?.Entity : null;

    private RemoteEntitySyncObject localSyncObject = null;
    private ARRHierarchicalStateOverrideComponent localStateOverride = null;

    public void OnEnable()
    {
        localSyncObject = GetComponent<RemoteEntitySyncObject>();
        localStateOverride = GetComponent<ARRHierarchicalStateOverrideComponent>();

        if (localStateOverride == null)
        {
            localStateOverride = gameObject.AddComponent<ARRHierarchicalStateOverrideComponent>();
            var remoteStateOverride = localSyncObject.Entity.FindComponentOfType<HierarchicalStateOverrideComponent>();

            if (remoteStateOverride == null)
            {
                // if there is no HierarchicalStateOverrideComponent on the remote side yet, create one
                localStateOverride.Create(RemoteManagerUnity.CurrentSession);
            }
            else
            {
                // otherwise, bind our local stateOverride component to the remote component
                localStateOverride.Bind(remoteStateOverride);
            }
        }

        localStateOverride.RemoteComponent.TintColor = HighlightColor.toRemote();
    }

    public void OnDisable()
    {
        SetStateOverride(false);

        if (localStateOverride.IsComponentValid)
        {
            localStateOverride.RemoteComponent.Enabled = false;
            localStateOverride.enabled = false;
        }
    }

    private void SetStateOverride(bool on)
    {
        if (localStateOverride.IsComponentValid)
        {
            localStateOverride.RemoteComponent.UseTintColorState = on ? HierarchicalEnableState.ForceOn : HierarchicalEnableState.InheritFromParent;
            localStateOverride.RemoteComponent.SelectedState = on ? HierarchicalEnableState.ForceOn : HierarchicalEnableState.InheritFromParent;
        }
    }

    public void SetFocus(bool on)
    {
        SetStateOverride(on);
    }
}
```

Następnie musimy rozszerzyć nasz *RemoteRaycaster,* aby dodać składnik *RemoteModelEntity* do obiektu, który właśnie wybraliśmy.

Dodaj następujący kod do implementacji **programu RemoteRaycaster** i usuń zduplikowane funkcje:

```csharp
    private RemoteModelEntity focusedModel = null;

    public async void Raycast(Vector3 origin, Vector3 direction)
    {
        var remoteEntity = await RemoteRayCast(origin, direction);

        if (focusedModel != null)
        {
            if (focusedModel.Entity == remoteEntity)
            {
                // picked the same object as before
                return;
            }

            ClearFocus();
        }

        if (remoteEntity == null)
        {
            // picked no object at all
            return;
        }

        // get the instance of a Unity GameObject for the ARR entity
        var entityGO = remoteEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        // ensure the game object has the RemoteModelEntity component
        focusedModel = entityGO.GetComponent<RemoteModelEntity>();
        if (focusedModel == null)
        {
            focusedModel = entityGO.AddComponent<RemoteModelEntity>();
        }

        focusedModel.SetFocus(true);
    }

    private void ClearFocus()
    {
        focusedModel.SetFocus(false);
        focusedModel = null;
    }
```

Uruchom projekt i wskaż model, powinien on zobaczyć, że otrzymuje czerwony odcień i biały kontur zaznaczenia.

## <a name="isolate-the-selected-object"></a>Wyizolowanie zaznaczonego obiektu

Innym zastosowaniem [HierarchiiStateOverrideComponent](../../overview/features/override-hierarchical-state.md) jest możliwość zastąpienia widoczności. Dzięki temu można wyizolować zaznaczony obiekt od reszty modelu. Otwórz skrypt **RemoteModelEntity,** dodaj następujący kod i usuń zduplikowane funkcje:

```csharp
    private bool isolated = false;
    private HierarchicalStateOverrideComponent parentOverride = null;

    public void ToggleIsolate()
    {
        SetIsolated(!isolated);
    }

    public void SetIsolated(bool on)
    {
        if (localStateOverride == null || !localStateOverride.IsComponentValid || isolated == on)
        {
            return;
        }

        // find the top most parent object that has a HierarchicalStateOverrideComponent
        if (parentOverride == null)
        {
            var modelRoot = transform;

            while (modelRoot.parent != null)
            {
                modelRoot = modelRoot.parent;

                var parentSyncObject = modelRoot.GetComponent<RemoteEntitySyncObject>();

                var stateOverrideComp = parentSyncObject?.Entity.FindComponentOfType<HierarchicalStateOverrideComponent>();

                if (stateOverrideComp != null)
                {
                    parentOverride = stateOverrideComp;
                }
            }
        }

        if (parentOverride != null)
        {
            isolated = on;

            parentOverride.HiddenState = isolated ? HierarchicalEnableState.ForceOn : HierarchicalEnableState.InheritFromParent;
            localStateOverride.RemoteComponent.HiddenState = isolated ? HierarchicalEnableState.ForceOff : HierarchicalEnableState.InheritFromParent;
        }
    }

    public void SetFocus(bool on)
    {
        SetStateOverride(on);

        if (!on)
        {
            SetIsolated(false);
        }
    }
```

Ten kod opiera się na posiadaniu składnika zastępowania stanu w najwyższym obiekcie w hierarchii, co sprawia, że wszystkie obiekty są niewidoczne. Następnie ponownie zastępuje widoczność zaznaczonego obiektu, aby ten jeden obiekt był widoczny. W związku z tym musimy utworzyć składnik zastępowania stanu w obiekcie głównym.

Otwórz skrypt **RemoteRendering** i wstaw poniższy kod u góry funkcji *LoadModel:*

```csharp
    public async void LoadModel()
    {
        // create a root object to parent a loaded model to
        modelEntity = arrService.CurrentActiveSession.Actions.CreateEntity();
        arrService.CurrentActiveSession.Actions.CreateComponent(ObjectType.HierarchicalStateOverrideComponent, modelEntity);

        ...
    }
```

Wreszcie potrzebujemy sposobu, aby przełączyć widoczność. Otwórz skrypt **Programu RemoteRaycaster** i zastąp funkcję *Update:*

```csharp
    private void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        var ray = Camera.main.ScreenPointToRay(Input.mousePosition);

        Raycast(ray.origin, ray.direction);

        if (Input.GetMouseButtonDown(1) && focusedModel != null)
        {
            focusedModel.ToggleIsolate();
        }
    }
```

Uruchom kod i kliknij prawym przyciskiem myszy część modelu. Reszta modelu zniknie i tylko podświetlony element pozostanie widoczny.

## <a name="remove-gameobject-instances-of-remote-entities"></a>Usuwanie wystąpień GameObject encji zdalnych

Być może zauważyłeś, że kod utrzymuje tworzenie obiektów, ale nigdy nie czyści ich. Jest to również widoczne w panelu hierarchii obiektów. Po rozwinięciu hierarchii obiektów zdalnych podczas symulacji za każdym razem, gdy najedziesz kursorem na nową część modelu, pojawia się coraz więcej obiektów.

Posiadanie wielu obiektów w scenie negatywnie wpływa na wydajność. Należy zawsze oczyścić obiekty, które nie są już potrzebne.

Wstaw poniższy kod do skryptu **RemoteRaycaster** i usuń zduplikowane funkcje:

```csharp
    private void ClearFocus()
    {
        focusedModel.SetFocus(false);
        CleanHierarchy(focusedModel.gameObject);
        focusedModel = null;
    }

    private void CleanHierarchy(GameObject focusedGO)
    {
        var sync = focusedGO?.GetComponent<RemoteEntitySyncObject>();
        if (sync == null || !sync.IsEntityValid)
        {
            return;
        }

        sync.Entity.DestroyGameObject(EntityExtensions.DestroyGameObjectFlags.DestroyEmptyParents | EntityExtensions.DestroyGameObjectFlags.KeepRemoteRoot);
    }
```

## <a name="move-objects"></a>Przenoszenie obiektów

W następnym kroku chcemy przenieść zaznaczony obiekt. W skrypcie **RemoteRaycaster** wstaw ten kod i usuń zduplikowaną funkcję:

```csharp
    private Vector3 lastPosition = Vector3.zero;

    private void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        if (Input.GetMouseButton(0))
        {
            if (focusedModel)
            {
                // note: mousePosition is in 2D screen-space coordinates and has no relation with
                // the 3D object position. This just happens to work good enough for demonstration.
                var delta = Input.mousePosition - lastPosition;
                focusedModel.transform.position += delta * Time.deltaTime;
            }
        }
        else
        {
            Vector3 position = Input.mousePresent ? Input.mousePosition : new Vector3(Screen.width / 2, Screen.height / 2, 0);
            var ray = Camera.main.ScreenPointToRay(position);

            Raycast(ray.origin, ray.direction);

            if (Input.GetMouseButtonDown(1) && focusedModel != null)
            {
                focusedModel.ToggleIsolate();
            }
        }

        lastPosition = Input.mousePosition;
    }
```

> [!IMPORTANT]
> Jeśli uruchomisz ten kod, zauważysz, że nic się nie dzieje. Dzieje się tak dlatego, że zmiana przekształcenia obiektu nie powoduje automatycznej synchronizacji zmiany stanu na serwerze ze względu na wydajność. Zamiast tego należy ręcznie wypchnąć tę zmianę stanu na serwer lub włączyć **SyncEveryFrame** w składniku *RemoteEntitySyncObject.*

Otwórz skrypt **RemoteModelEntity** i dodaj ten wiersz:

```csharp
    public void OnEnable()
    {
        ...

        localSyncObject.SyncEveryFrame = true;
    }
```

Ponowne uruchomienie kodu powinno być możliwe do kliknięcia lewym przyciskiem myszy na obiekcie i przeciągnąć go wokół.

## <a name="add-a-cut-plane"></a>Dodawanie płaszczyzny cięcia

Ostatnią funkcją, którą chcemy wypróbować w tym samouczku, jest użycie [ciętych płaszczyzn](../../overview/features/cut-planes.md). Wycięta płaszczyzna odcina części renderowanych obiektów, tak aby można było zajrzeć do nich.

Utwórz nowy GameObject w scenie **CutPlane**. Utwórz nowy skrypt i nazwij go **RemoteCutPlane**. Dodaj składnik do nowego GameObject.

Otwórz plik skryptu i zastąp jego zawartość następującym kodem:

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class RemoteCutPlane : MonoBehaviour
{
    private ARRCutPlaneComponent localCutPlaneComponent = null;
    private RemoteEntitySyncObject remoteEntitySync = null;

    void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            // can't do anything while we are not connected
            return;
        }

        if (localCutPlaneComponent == null)
        {
            localCutPlaneComponent = gameObject.CreateArrComponent<ARRCutPlaneComponent>(RemoteManagerUnity.CurrentSession);
        }

        if (remoteEntitySync == null)
        {
            remoteEntitySync = gameObject.GetComponent<RemoteEntitySyncObject>();
            remoteEntitySync.SyncEveryFrame = true;
        }

        localCutPlaneComponent.RemoteComponent.Normal = Axis.X;
        localCutPlaneComponent.RemoteComponent.FadeLength = 0.025f;
        localCutPlaneComponent.RemoteComponent.FadeColor = new Color4Ub(255, 128, 0, 255);
        localCutPlaneComponent.RemoteComponent.Enabled = true;
    }

    void OnDisable()
    {
        if (localCutPlaneComponent && localCutPlaneComponent.IsComponentValid)
        {
            localCutPlaneComponent.RemoteComponent.Enabled = false;
        }

        if (remoteEntitySync && remoteEntitySync.IsEntityValid)
        {
            remoteEntitySync.SyncEveryFrame = false;
        }
    }
}
```

Po uruchomieniu kodu teraz, należy zobaczyć, jak model jest przeciąć otwarte przez płaszczyznę. Można wybrać obiekt *CutPlane* i przenieść go i obrócić w oknie *Scena.* Płaszczyznę cięcia można włączać i wyłączać, wyłączając obiekt płaszczyzny cięcia.

## <a name="next-steps"></a>Następne kroki

Znasz teraz najważniejsze funkcje interakcji z obiektami zdalnymi. W następnym samouczku przyjrzymy się dostosowywaniu wyglądu sceny.

> [!div class="nextstepaction"]
> [Samouczek: Zmiana środowiska i materiałów](changing-environment-and-materials.md)
