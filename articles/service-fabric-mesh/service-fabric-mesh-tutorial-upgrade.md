---
title: Samouczek dotyczący uaktualniania aplikacji usługi Azure Service Fabric siatki | Dokumentacja firmy Microsoft
description: Dowiedz się, jak uaktualnić aplikację usługi Service Fabric przy użyciu programu Visual Studio
services: service-fabric-mesh
documentationcenter: .net
author: tylerMSFT
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/18/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: c23646bca6109d27e57b2f928363e65c83c634eb
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47031156"
---
# <a name="tutorial-learn-how-to-upgrade-a-service-fabric-application-using-visual-studio"></a>Samouczek: Dowiedz się, jak uaktualnić aplikację usługi Service Fabric przy użyciu programu Visual Studio

Niniejszy samouczek jest czwartą częścią serii i pokazuje, jak uaktualnić aplikację usługi Azure Service Fabric siatki bezpośrednio z programu Visual Studio. Uaktualnienie będzie zawierać aktualizacji konfiguracji i aktualizacji kodu. Pojawi się, że proces uaktualniania i publikowanie z poziomu programu Visual Studio są takie same.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Uaktualnienie usługi Service Fabric siatki za pomocą programu Visual Studio

Ta seria samouczków zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * [Tworzenie siatki sieci szkieletowej usługi aplikacji w programie Visual Studio](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Debugowanie aplikacji usługi Service Fabric siatki w lokalnego klastra projektowego](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Wdrażanie usługi Service Fabric siatki aplikacji](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * Uaktualnienie usługi Service Fabric siatki aplikacji
> * [Oczyszczanie zasobów usługi Service Fabric siatki](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka:

* Jeśli nie została wdrożona aplikacja to-do, postępuj zgodnie z instrukcjami [publikowania aplikacji sieci web usługi Service Fabric siatki](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md).

## <a name="upgrade-a-service-fabric-mesh-service-by-using-visual-studio"></a>Uaktualnienie usługi Service Fabric siatki za pomocą programu Visual Studio

W tym artykule pokazano, jak uaktualnić niezależnie mikrousług w aplikacji.  W tym przykładzie firma Microsoft zmodyfikuje `WebFrontEnd` usługi do wyświetlania kategorii zadań. Firma Microsoft będzie uaktualnienie wdrożonej usługi.

## <a name="modify-the-config"></a>Modyfikowanie konfiguracji

Uaktualnienia może być z powodu zmian w kodzie i/lub zmiany w konfiguracji.  Aby wprowadzić zmiany konfiguracji, otwórz `WebFrontEnd` projektu `service.yaml` pliku (w **zasobów usługi** węzła).

W `resources:` sekcji, zmień `cpu:` od 0,5 do 1.0, w oczekiwaniu, że frontonu sieci web będzie intensywnie używane. Powinna teraz wyglądać następująco:

```xml
              ...
              resources:
                requests:
                  cpu: 1.0
                  memoryInGB: 1
              ...
```

## <a name="modify-the-model"></a>Modyfikowanie modelu

Aby wprowadzić zmiany w kodzie, należy dodać `Category` właściwości `ToDoItem` klasy w `ToDoItem.cs` pliku.

```csharp
public class ToDoItem
{
    public string Category { get; set; }
    ...
}
```

Następnie zaktualizuj `Load()` metody, w tym samym pliku, kategoria zostanie ustawiona na domyślny ciąg:

```csharp
public static ToDoItem Load(string description, int index, bool completed)
{
    ToDoItem newItem = new ToDoItem(description)
    {
        Index = index,
        Completed = completed, 
        Category = "none"    // <-- add this line
    };

    return newItem;
}
```

## <a name="modify-the-service"></a>Modyfikowanie usługi

`WebFrontEnd` Projekt jest aplikacją programu ASP.NET Core przy użyciu strony sieci web, który pokazuje elementy listy zadań do wykonania. W `WebFrontEnd` otwarty projekt `Index.cshtml` i dodaj następujące dwa wiersze wymienione poniżej, aby wyświetlić kategorii zadań:

```HTML
<div>
    <table class="table-bordered">
        <thead>
            <tr>
                <th>Description</th>
                <th>Category</th>           @*add this line*@
                <th>Done?</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var item in Model.Items)
            {
                <tr>
                    <td>@item.Description</td>
                    <td>@item.Category</td>           @*add this line*@
                    <td>@item.Completed</td>
                </tr>
            }
        </tbody>
    </table>
</div>
```

Skompiluj i uruchom aplikację w celu zweryfikowania, czy widzisz nową kolumnę kategorii na stronie sieci web, która zawiera listę zadań.

## <a name="upgrade-the-app-from-visual-studio"></a>Uaktualnianie aplikacji w programie Visual Studio

Niezależnie od tego, czy wykonujesz uaktualnienie kodu lub uaktualnienia konfiguracji (w tym przypadku Radzimy sobie oba), aby uaktualnić aplikację usługi Service Fabric siatki dla platformy Azure kliknij prawym przyciskiem myszy na **todolistapp** w programie Visual Studio i wybierz pozycję **publikowania ...**

Następnie zobaczysz okno dialogowe **Publikowanie aplikacji usługi Service Fabric**.

![Okno dialogowe publikowania usługi Service Fabric Mesh w programie Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Wybierz konto i subskrypcję platformy Azure. Upewnij się, że **lokalizacji** jest ustawiona na lokalizację, która została użyta podczas aplikacja to-do oryginalnie opublikowane na platformie Azure. W tym artykule używane **wschodnie stany USA**.

Upewnij się, że **grupy zasobów** jest ustawiony do grupy zasobów, która została użyta podczas aplikacja to-do oryginalnie opublikowane na platformie Azure.

Upewnij się, że **usługi Azure Container Registry** jest ustawiona na nazwę rejestru kontenerów platformy azure, który został utworzony, gdy aplikacja to-do pierwotnie opublikowany na platformie Azure.

W oknie dialogowym publikowania naciśnij **Publikuj** przycisk, aby uaktualnić aplikację do wykonania na platformie Azure.

Możesz monitorować postęp uaktualnienia, wybierając **narzędzia usługi Service Fabric** programu Visual Studio okienko **dane wyjściowe** okna. Po zakończeniu uaktualniania **narzędzia usługi Service Fabric** dane wyjściowe będą wyświetlane adresu IP i portu aplikacji w postaci adresu URL.

```json
Packaging Application...
Building Images...
Web1 -> C:\Code\ServiceFabricMeshApp\ToDoService\bin\Any CPU\Release\netcoreapp2.0\ToDoService.dll
Uploading the images to Azure Container Registy...
Deploying application to remote endpoint...
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

Otwórz przeglądarkę internetową i przejdź do adresu URL, aby wyświetlić witrynę internetową działającą na platformie Azure. Powinna zostać wyświetlona strony sieci web, która zawiera kolumny kategorii.

## <a name="next-steps"></a>Kolejne kroki

W tej części samouczka przedstawiono informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Jak uaktualnić aplikację usługi Service Fabric MES za pomocą programu Visual Studio

Przejdź do następnego samouczka:
> [!div class="nextstepaction"]
> [Oczyszczanie zasobów usługi Service Fabric siatki](service-fabric-mesh-tutorial-cleanup-resources.md)