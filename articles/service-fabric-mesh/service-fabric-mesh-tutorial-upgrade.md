---
title: Samouczek — uaktualnianie aplikacji sieci szkieletowej usługi Azure
description: Ten samouczek jest częścią czwartą serii i pokazuje, jak uaktualnić aplikację usługi Azure Service Fabric Mesh bezpośrednio z programu Visual Studio.
author: dkkapur
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 7cdb8868f760ef0f35ab90c06b411110f871738c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75351712"
---
# <a name="tutorial-learn-how-to-upgrade-a-service-fabric-application-using-visual-studio"></a>Samouczek: Dowiedz się, jak uaktualnić aplikację sieci szkieletowej usług przy użyciu programu Visual Studio

Ten samouczek jest częścią czwartą serii i pokazuje, jak uaktualnić aplikację usługi Azure Service Fabric Mesh bezpośrednio z programu Visual Studio. Uaktualnienie będzie zawierać zarówno aktualizację kodu, jak i aktualizację konfiguracji. Zobaczysz, że kroki uaktualniania i publikowania z poziomu programu Visual Studio są takie same.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Uaktualnianie usługi Siatki sieci szkieletowej usług przy użyciu programu Visual Studio

Ta seria samouczków zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * [Tworzenie aplikacji usługi Service Fabric Mesh w programie Visual Studio](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Debugowanie aplikacji usługi Service Fabric Mesh działającej w lokalnym klastrze projektowym](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Wdrażanie aplikacji usługi Service Fabric Mesh](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * Uaktualnianie aplikacji usługi Service Fabric Mesh
> * [Czyszczenie zasobów usługi Service Fabric Mesh](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka:

* Jeśli nie została wdrożona aplikacja z listą zadań do wykonania, postępuj zgodnie z instrukcjami zamieszczonymi w artykule [Publikowanie aplikacji internetowej usługi Service Fabric Mesh](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md).

## <a name="upgrade-a-service-fabric-mesh-service-by-using-visual-studio"></a>Uaktualnianie usługi Siatki sieci szkieletowej usług przy użyciu programu Visual Studio

W tym artykule pokazano, jak uaktualnić mikrousługi w aplikacji. W tym przykładzie zmodyfikujemy `WebFrontEnd` usługę, aby wyświetlić kategorię zadań i zwiększyć ilość procesora CPU, który jest podany. Następnie uaktualnimy wdrożoną usługę.

## <a name="modify-the-config"></a>Modyfikowanie konfiguracji

Podczas tworzenia aplikacji sieci szkieletowej usługi Mesh program Visual Studio dodaje plik **parameters.yaml** dla każdego środowiska wdrażania (chmura i lokalne). W tych plikach można zdefiniować parametry i ich wartości, do których można się odwoływać z plików Mesh *.yaml, takich jak service.yaml lub network.yaml.  Visual Studio zawiera pewne zmienne dla Ciebie, takie jak ile procesora CPU usługa może używać.

Zaktualizujemy `WebFrontEnd_cpu` parametr, aby zaktualizować `1.5` zasoby procesora, aby w oczekiwaniu, że usługa **WebFrontEnd** będzie intensywniej używana.

1. W projekcie **todolistapp** w obszarze **Środowisko chmura** > **Cloud**otwórz plik **parameters.yaml.** Zmodyfikuj `WebFrontEnd_cpu`wartość , do `1.5`. Nazwa parametru jest poprzedzony `WebFrontEnd_` nazwą usługi jako najlepszym rozwiązaniem, aby odróżnić ją od parametrów o tej samej nazwie, które mają zastosowanie do różnych usług.

    ```xml
    WebFrontEnd_cpu: 1.5
    ```

2. Otwórz plik **service.yaml** projektu **WebFrontEnd** w obszarze**Zasoby usługi** **WebFrontEnd** > .

    Należy zauważyć, `resources:` że `cpu:` w `"[parameters('WebFrontEnd_cpu')]"`sekcji, jest ustawiona na . Jeśli projekt jest budowany dla chmury, `'WebFrontEnd_cpu` wartość dla zostanie pobrana z pliku **Environments** >  `1.5`**Cloud** > **parameters.yaml** i będzie . Jeśli projekt jest budowany do uruchamiania lokalnie, wartość zostanie **pobrana** > z pliku Środowiska > **lokalnego.yaml** i będzie "0.5".**Local**

> [!Tip]
> Domyślnie plik parametru, który jest elementem równorzędnym pliku profile.yaml, będzie używany do dostarczania wartości dla tego pliku profile.yaml.
> Na przykład środowiska > Cloud > parameters.yaml zawiera wartości parametrów dla środowiska > Cloud > profile.yaml.
>
> Można to zastąpić, dodając do pliku profile.yaml`parametersFilePath=”relative or full path to the parameters file”` następujące elementy: na przykład `parametersFilePath=”C:\MeshParms\CustomParameters.yaml”` lub`parametersFilePath=”..\CommonParameters.yaml”`

## <a name="modify-the-model"></a>Modyfikowanie modelu

Aby wprowadzić zmianę kodu, `Category` dodaj `ToDoItem` właściwość `ToDoItem.cs` do klasy w pliku.

```csharp
public class ToDoItem
{
    public string Category { get; set; }
    ...
}
```

Następnie zaktualizuj `Load()` metodę w tym samym pliku, aby ustawić kategorię na domyślny ciąg znaków:

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

Projekt `WebFrontEnd` jest aplikacją ASP.NET Core ze stroną sieci web, która pokazuje elementy listy rzeczy do zrobienia. W `WebFrontEnd` projekcie otwórz `Index.cshtml` i dodaj następujące dwa wiersze, wskazane poniżej, aby wyświetlić kategorię zadania:

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

Skompiluj i uruchom aplikację, aby sprawdzić, czy na stronie sieci Web jest widoczna nowa kolumna kategorii z listą zadań.

## <a name="upgrade-the-app-from-visual-studio"></a>Uaktualnianie aplikacji z programu Visual Studio

Niezależnie od tego, czy dokonujesz uaktualnienia kodu, czy uaktualnienia konfiguracji (w tym przypadku wykonujemy oba te elementy), uaktualnij aplikację Sieci szkieletowej usługi na platformie Azure, klikając prawym przyciskiem myszy **todolistapp** w programie Visual Studio, a następnie wybierz pozycję **Publikuj...**

Następnie zobaczysz okno dialogowe **Publikowanie aplikacji usługi Service Fabric**.

Użyj listy rozwijanej **Profil docelowy,** aby wybrać plik profile.yaml do użycia w tym wdrożeniu. Uaktualniamy aplikację w chmurze, więc wybieramy **cloud.yaml** w rozwijanej, która użyje `WebFrontEnd_cpu` wartości 1.0 zdefiniowanej w tym pliku.

![Okno dialogowe publikowania usługi Service Fabric Mesh w programie Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Wybierz konto i subskrypcję platformy Azure. Ustaw **lokalizację** na lokalizację, która została użyta podczas oryginalnego publikowania aplikacji do zrobienia na platformie Azure. W tym artykule użyto **wschodnich stanów USA**.

Ustaw **grupę zasobów** na grupę zasobów, która została użyta podczas oryginalnego publikowania aplikacji do zrobienia na platformie Azure.

Ustaw **usługę Azure Container Registry** na nazwę rejestru kontenerów platformy Azure, która została utworzona podczas oryginalnego publikowania aplikacji do zrobienia na platformie Azure.

W oknie dialogowym publikowania naciśnij przycisk **Publikuj,** aby uaktualnić aplikację do wykonania na platformie Azure.

Monitorowanie postępu uaktualniania, wybierając okienko **Narzędzia sieci szkieletowej usług** w oknie Dane **wyjściowe** programu Visual Studio. 

Po zbudowaniu obrazu i wypchnięciu do rejestru kontenerów platformy Azure łącze **Dla stanu** pojawi się w danych wyjściowych, które można kliknąć, aby monitorować wdrożenie w witrynie Azure portal.

Po zakończeniu uaktualniania dane **wyjściowe narzędzi sieci** szkieletowej usług będą wyświetlać adres IP i port aplikacji w postaci adresu URL.

```json
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

Otwórz przeglądarkę internetową i przejdź do adresu URL, aby wyświetlić witrynę internetową działającą na platformie Azure. Teraz powinna zostać wyświetlona strona internetowa zawierająca kolumnę kategorii.

## <a name="next-steps"></a>Następne kroki

W tej części samouczka przedstawiono informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Jak uaktualnić aplikację siatki sieci szkieletowej usługi przy użyciu programu Visual Studio

Przejdź do następnego samouczka:
> [!div class="nextstepaction"]
> [Czyszczenie zasobów usługi Service Fabric Mesh](service-fabric-mesh-tutorial-cleanup-resources.md)