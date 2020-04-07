---
title: Zmiana środowiska i materiałów
description: Samouczek, który pokazuje, jak zmodyfikować mapę nieba i materiały obiektów w scenie Unity.
author: jakrams
ms.author: jakras
ms.date: 02/03/2020
ms.topic: tutorial
ms.openlocfilehash: 1354ac3cf2c6fc716ac72ae339928fa49171893e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679618"
---
# <a name="tutorial-changing-the-environment-and-materials"></a>Samouczek: Zmiana środowiska i materiałów

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Zmienianie mapy środowiska sceny.
> * Zmodyfikuj parametry materiału.
> * Załaduj niestandardowe tekstury.

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku założono, że znasz [samouczek: Praca z jednostkami zdalnymi w Unity](working-with-remote-entities.md). Jednak wystarczy tylko projekt Unity, z którym można połączyć się z sesjami i załadować model, jak pokazano w [tutorialu: Konfigurowanie projektu Unity od podstaw](project-setup.md).

> [!TIP]
> [Repozytorium przykładów ARR](https://github.com/Azure/azure-remote-rendering) zawiera przygotowane projekty Unity dla wszystkich samouczków w folderze *Unity,* których można użyć jako odwołanie.

## <a name="change-the-environment-map"></a>Zmienianie mapy środowiska

Funkcja zdalnego renderowania platformy Azure obsługuje używanie [pól nieba](../../overview/features/sky.md) (czasami nazywanych również "mapami środowiska") do symulowania oświetlenia otoczenia. Jest to szczególnie przydatne, gdy obiekty używają *[renderowania fizycznego,](../../overview/features/pbr-materials.md)* tak jak robią to nasze przykładowe modele. ARR jest również wyposażony w wiele wbudowanych tekstur nieba, które użyjemy w tym samouczku.

Utwórz nowy skrypt o nazwie **RemoteSky** i dodaj go do nowego GameObject. Otwórz plik skryptu i zastąp go następującym kodem:

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class RemoteSky : MonoBehaviour
{
    private int skyIndex = 0;

    private LoadTextureFromSASParams[] builtIns =
    {
        new LoadTextureFromSASParams("builtin://Autoshop", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://BoilerRoom", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://ColorfulStudio", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://Hangar", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://IndustrialPipeAndValve", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://Lebombo", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://SataraNight", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://SunnyVondelpark", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://Syferfontein", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://TearsOfSteelBridge", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://VeniceSunset", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://WhippleCreekRegionalPark", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://WinterRiver", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://DefaultSky", TextureType.CubeMap)
    };

    public async void ToggleSky()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        skyIndex = (skyIndex + 1) % builtIns.Length;

        var texture = await RemoteManagerUnity.CurrentSession.Actions.LoadTextureFromSASAsync(builtIns[skyIndex]).AsTask();

        Debug.Log($"Switching sky to: {builtIns[skyIndex].TextureId}");

        var settings = RemoteManagerUnity.CurrentSession.Actions.SkyReflectionSettings;

        settings.SkyReflectionTexture = texture;
    }

    private void OnGUI()
    {
        if (RemoteManagerUnity.IsConnected)
        {
            if (GUI.Button(new Rect(10, Screen.height - 50, 175, 30), "Toggle Sky"))
            {
                ToggleSky();
            }
        }
    }
}
```

Należy zauważyć, że `LoadTextureFromSASAsync` wariant jest używany powyżej, ponieważ wbudowane tekstury są ładowane. W przypadku ładowania z [połączonych magazynów obiektów blob](../../how-tos/create-an-account.md#link-storage-accounts)użyj wariantu. `LoadTextureAsync` Przykład, jak to działa dla modeli można znaleźć w [sekcji ładowania modelu](../../concepts/models.md#loading-models).

Po uruchomieniu kodu i przełączać się na mapy nieba, można zauważyć drastycznie różne oświetlenie na modelu. Jednak tło pozostanie czarne i nie widać rzeczywistej tekstury nieba. Jest to celowe, ponieważ renderowanie tła rozpraszałoby urządzenie rzeczywistości rozszerzonej. W odpowiedniej aplikacji należy używać tekstur nieba, które są podobne do twojego prawdziwego otoczenia, ponieważ pomoże to sprawić, że obiekty staną się bardziej realne.

## <a name="modify-materials"></a>Modyfikowanie materiałów

W poprzednim samouczku użyliśmy [składników zastępowania stanu,](../../overview/features/override-hierarchical-state.md) aby zmienić kolor odcienia zaznaczonych obiektów. Teraz chcemy osiągnąć podobny efekt, ale robimy to modyfikując [materiał](../../concepts/materials.md) obiektu.

Najpierw potrzebujemy skryptu do wybierania obiektów, tak jak zrobiliśmy to w [drugim tutorialu](working-with-remote-entities.md). Jeśli nie masz jeszcze skryptu **RemoteRaycaster,** utwórz go teraz. Zastąp jego zawartość następującym kodem:

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
}
```

Dodaj składnik do obiektu gry *RemoteRendering.* Jest odpowiedzialny za pobieranie obiektów pod myszą i dodawanie *składników RemoteModelEntity* do wybranych obiektów. Ta klasa składnika jest, gdzie możemy implementować rzeczywiste funkcje zmiany materiału.

Jeśli nie masz jeszcze skryptu **RemoteModelEntity,** utwórz go i zastąp jego zawartość tym kodem:

```csharp
using UnityEngine;
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;

// to prevent namespace conflicts
using ARRTexture = Microsoft.Azure.RemoteRendering.Texture;

public class RemoteModelEntity : MonoBehaviour
{
    private Color HighlightColor = new Color(1.0f, 0.4f, 0.1f, 1.0f);

    public Entity Entity => localSyncObject != null ? localSyncObject?.Entity : null;
    private RemoteEntitySyncObject localSyncObject = null;
    private ARRMeshComponent meshComponent = null;
    private Color4 originalColor;

    public void OnEnable()
    {
        localSyncObject = GetComponent<RemoteEntitySyncObject>();

        meshComponent = GetComponent<ARRMeshComponent>();
        if (meshComponent == null)
        {
            var mesh = localSyncObject.Entity.FindComponentOfType<MeshComponent>();
            if (mesh != null)
            {
                gameObject.BindArrComponent<ARRMeshComponent>(mesh);
                meshComponent = gameObject.GetComponent<ARRMeshComponent>();
            }
        }
        meshComponent.enabled = true;
    }

    public void SetFocus(bool on)
    {
        SetMaterialColor(on);
    }

    private void SetMaterialColor(bool on)
    {
        if (!meshComponent.IsComponentValid)
        {
            return;
        }

        // get the list of materials from the picked mesh
        // we only modify the first material; depending on how the model is authored, this may
        // work exactly as desired, or not ;-)
        var materials = meshComponent.RemoteComponent.Mesh.Materials;
        if (materials == null || materials.Count == 0)
        {
            return;
        }

        // there are different types of materials, cast as necessary
        if (materials[0].MaterialSubType == MaterialType.Color)
        {
            var materialColor = materials[0] as ColorMaterial;

            if (on)
            {
                // modify the color of the material
                originalColor = materialColor.AlbedoColor;
                materialColor.AlbedoColor = HighlightColor.toRemoteColor4();
            }
            else
            {
                materialColor.AlbedoColor = originalColor;
            }
        }
        else if (materials[0].MaterialSubType == MaterialType.Pbr)
        {
            var materialColor = materials[0] as PbrMaterial;

            if (on)
            {
                // modify the color of the material
                originalColor = materialColor.AlbedoColor;
                materialColor.AlbedoColor = HighlightColor.toRemoteColor4();
            }
            else
            {
                materialColor.AlbedoColor = originalColor;
            }
        }
    }
}
```

Po uruchomieniu tego kodu obiekty, które można najechać za pomocą myszy, są wyróżnione. Efekt jest podobny do tego, co zrobiliśmy w tutorialu 2, ale sposób, w jaki jest osiągnięty jest inny. Tutaj otrzymujemy listę materiałów na wybranym obiekcie, a następnie modyfikujemy pierwszy z nich, aby miał inny kolor albedo.

> [!IMPORTANT]
> Należy pamiętać, że to, czy ta metoda wyróżnia poprawne części modelu, zależy od sposobu tworzenia modelu. Będzie działać idealnie, jeśli każdy obiekt używa dokładnie jednego materiału. Jeśli jednak model nie ma relacji 1:1 między częściami i materiałami, naiwny kod powyżej nie zrobi właściwej rzeczy.

## <a name="use-a-different-texture"></a>Używanie innej tekstury

[Tekstury](../../concepts/textures.md) są zazwyczaj częścią modelu źródłowego. Podczas [konwersji modelu](../../quickstarts/convert-model.md)wszystkie tekstury są konwertowane na niezbędny format środowiska wykonawczego i pakowane do pliku końcowego modelu. Aby zastąpić teksturę w czasie wykonywania, należy zapisać ją w [formacie DDS](https://en.wikipedia.org/wiki/DirectDraw_Surface) i przekazać do magazynu obiektów blob platformy Azure. Zapoznaj się z [tym przewodnikiem szybki start,](../../quickstarts/convert-model.md) aby dowiedzieć się, jak utworzyć kontener obiektów blob platformy Azure. Po kontenerze obiektów blob można go otworzyć w Eksploratorze usługi Azure Storage i przekazać plik za pomocą przeciągania i upuszczania.

Po stronie środowiska wykonawczego można rozwiązać zasób tekstury w magazynie obiektów blob na dwa różne sposoby:

* Adres tekstury przez jego identyfikator URI sygnatury dostępu Współdzielonego. W tym celu kliknij prawym przyciskiem myszy przesłany plik i wybierz polecenie **"Pobierz podpis dostępu współdzielonego..."** z menu kontekstowego. Użyj tego identyfikatora `LoadTextureFromSASAsync` URI sygnatury dostępu Współdzielonego z wariantem funkcji (patrz przykładowy kod poniżej).
* Adres tekstury przez parametry magazynu obiektów blob bezpośrednio, w przypadku, gdy [magazyn obiektów blob jest połączony z kontem](../../how-tos/create-an-account.md#link-storage-accounts). Odpowiednią funkcją ładowania `LoadTextureAsync`w tym przypadku jest .

Teraz otwórz skrypt **RemoteModelEntity,** dodaj następujący kod i usuń zduplikowane funkcje:

```csharp
    private string textureFile = ""; //<SAS URI for your texture>
    private ARRTexture originalTexture = null;

    private async void SetMaterialTexture(bool on)
    {
        if (!meshComponent.IsComponentValid)
        {
            return;
        }

        var materials = meshComponent.RemoteComponent.Mesh.Materials;
        if (materials == null || materials.Count == 0)
        {
            return;
        }

        ARRTexture newTexture = originalTexture;

        if (on)
        {
            var textureParams = new LoadTextureFromSASParams(textureFile, TextureType.Texture2D);

            newTexture = await RemoteManagerUnity.CurrentSession.Actions.LoadTextureFromSASAsync(textureParams).AsTask();
        }

        if (materials[0].MaterialSubType == MaterialType.Color)
        {
            var materialColor = materials[0] as ColorMaterial;

            originalTexture = materialColor.AlbedoTexture;
            materialColor.AlbedoTexture = newTexture;
        }
        else if (materials[0].MaterialSubType == MaterialType.Pbr)
        {
            var materialColor = materials[0] as PbrMaterial;

            originalTexture = materialColor.AlbedoTexture;
            materialColor.AlbedoTexture = newTexture;
        }
    }

    public void SetFocus(bool on)
    {
        if (string.IsNullOrEmpty(textureFile))
        {
            SetMaterialColor(on);
        }
        else
        {
            SetMaterialTexture(on);
        }
    }
```

Uruchom ten kod i najedź kursorem na model. Jeśli model ma odpowiednie współrzędne UV, powinna pojawić się tekstura. W przeciwnym razie można zauważyć tylko zmianę koloru.

## <a name="next-steps"></a>Następne kroki

To kończy naszą serię wprowadzającą na temat korzystania z renderowania zdalnego platformy Azure z unity. W następnym kroku należy zapoznać się z kilkoma podstawowymi pojęciami ARR, takimi jak [sesje,](../../concepts/sessions.md) [jednostki](../../concepts/entities.md)i [modele,](../../concepts/models.md) aby zbudować głębsze zrozumienie. Istnieją również różne funkcje, takie jak [światła,](../../overview/features/lights.md) [renderowanie konspektu,](../../overview/features/outlines.md) [hierarchiczne zastąpienia stanu](../../overview/features/override-hierarchical-state.md)i [materiały,](../../concepts/materials.md) które należy zbadać bardziej szczegółowo.

> [!div class="nextstepaction"]
> [Obiekty i komponenty gry Unity](../../how-tos/unity/objects-components.md)
