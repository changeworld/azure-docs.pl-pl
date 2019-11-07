---
title: Samouczek — Uaktualnianie aplikacji siatkowej platformy Azure Service Fabric | Microsoft Docs
description: Ten samouczek jest czwartą częścią serii i pokazuje, jak uaktualnić aplikację sieci Web usługi Azure Service Fabric, bezpośrednio z programu Visual Studio.
services: service-fabric-mesh
documentationcenter: .net
author: dkkapur
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/29/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 81f155d5708a2fca2fc1145feb20af12d2fd151e
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686201"
---
# <a name="tutorial-learn-how-to-upgrade-a-service-fabric-application-using-visual-studio"></a>Samouczek: informacje na temat uaktualniania aplikacji Service Fabric przy użyciu programu Visual Studio

Ten samouczek jest czwartą częścią serii i pokazuje, jak uaktualnić aplikację sieci Web usługi Azure Service Fabric, bezpośrednio z programu Visual Studio. Uaktualnienie obejmie zarówno aktualizację kodu, jak i konfigurację aktualizacji. Zobaczysz, że kroki uaktualniania i publikowania w programie Visual Studio są takie same.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Uaktualnianie usługi siatki Service Fabric przy użyciu programu Visual Studio

Ta seria samouczków zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * [Tworzenie aplikacji usługi Service Fabric Mesh w programie Visual Studio](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Debugowanie aplikacji usługi Service Fabric Mesh w lokalnym klastrze projektowym](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Wdrażanie aplikacji usługi Service Fabric Mesh](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * Uaktualnianie aplikacji siatki Service Fabric
> * [Czyszczenie zasobów usługi Service Fabric Mesh](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka:

* Jeśli nie została wdrożona aplikacja z listą zadań do wykonania, postępuj zgodnie z instrukcjami zamieszczonymi w artykule [Publikowanie aplikacji internetowej usługi Service Fabric Mesh](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md).

## <a name="upgrade-a-service-fabric-mesh-service-by-using-visual-studio"></a>Uaktualnianie usługi siatki Service Fabric przy użyciu programu Visual Studio

W tym artykule przedstawiono sposób uaktualniania mikrousługi w aplikacji. W tym przykładzie zmodyfikujemy usługę `WebFrontEnd`, aby wyświetlić kategorię zadania i zwiększyć ilość procesora CPU, którą zostanie podaną. Następnie uaktualnimy wdrożoną usługę.

## <a name="modify-the-config"></a>Modyfikuj konfigurację

Podczas tworzenia aplikacji siatki Service Fabric, program Visual Studio dodaje plik **Parameters. YAML** dla każdego środowiska wdrażania (w chmurze i lokalnej). W tych plikach można definiować parametry i ich wartości, do których można się odwoływać z plików siatki *. YAML, takich jak Service. YAML lub Network. YAML.  Program Visual Studio udostępnia pewne zmienne, takie jak ilość procesora, która może być używana przez usługę.

Zaktualizujemy parametr `WebFrontEnd_cpu`, aby zaktualizować zasoby procesora CPU do `1.5` w przewidywaniu, że usługa **Webfrontonu** będzie bardziej intensywnie używana.

1. W projekcie **todolistapp** w obszarze **środowiska** > w **chmurze**Otwórz plik **Parameters. YAML** . Zmodyfikuj `WebFrontEnd_cpu`wartość, aby `1.5`. Nazwa parametru jest poprzedzona nazwą usługi `WebFrontEnd_` jako najlepsze rozwiązanie, aby odróżnić ją od parametrów o tej samej nazwie, która ma zastosowanie do różnych usług.

    ```xml
    WebFrontEnd_cpu: 1.5
    ```

2. Otwórz plik **Service. YAML** projektu **webfrontonu** w obszarze **zasoby usługi** **webfronton** > .

    Należy zauważyć, że w sekcji w `resources:` `cpu:` jest ustawiona na `"[parameters('WebFrontEnd_cpu')]"`. Jeśli projekt jest kompilowany dla chmury, wartość `'WebFrontEnd_cpu` będzie pobierana ze **środowisk** ** >  > ** pliku **parameters. YAML** i zostanie `1.5`. Jeśli projekt jest kompilowany do uruchamiania lokalnego, wartość zostanie pobrana ze **środowisk** > **lokalnego** > **parametry. 0,5 YAML** .

> [!Tip]
> Domyślnie plik parametrów, który jest elementem równorzędnym pliku profile. YAML, będzie używany do dostarczania wartości dla tego pliku profile. YAML.
> Na przykład środowiska > > w chmurze. YAML udostępnia wartości parametrów dla środowisk > Cloud > Profile. YAML.
>
> Można to zastąpić, dodając następujący plik do pliku profile. YAML:`parametersFilePath=”relative or full path to the parameters file”` na przykład `parametersFilePath=”C:\MeshParms\CustomParameters.yaml”` lub `parametersFilePath=”..\CommonParameters.yaml”`

## <a name="modify-the-model"></a>Modyfikowanie modelu

Aby wprowadzić zmianę kodu, Dodaj właściwość `Category` do klasy `ToDoItem` w pliku `ToDoItem.cs`.

```csharp
public class ToDoItem
{
    public string Category { get; set; }
    ...
}
```

Następnie zaktualizuj metodę `Load()` w tym samym pliku, aby ustawić dla kategorii domyślny ciąg:

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

Projekt `WebFrontEnd` jest aplikacją ASP.NET Coreą ze stroną sieci Web, która pokazuje elementy listy czynności do wykonania. W projekcie `WebFrontEnd` Otwórz `Index.cshtml` i Dodaj następujące dwa wiersze, które zostały wskazane poniżej, aby wyświetlić kategorię zadania:

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

Skompiluj i uruchom aplikację, aby sprawdzić, czy na stronie sieci Web jest widoczna nowa kolumna Kategoria, która zawiera listę zadań.

## <a name="upgrade-the-app-from-visual-studio"></a>Uaktualnianie aplikacji z programu Visual Studio

Bez względu na to, czy przeprowadzasz uaktualnienie kodu, czy też uaktualniasz konfigurację (w tym przypadku robimy obie te czynności), Uaktualnij aplikację Service Fabric siatką na platformie Azure, klikając prawym przyciskiem myszy pozycję **todolistapp** w programie Visual Studio, a następnie wybierz pozycję **Publikuj...**

Następnie zobaczysz okno dialogowe **Publikowanie aplikacji usługi Service Fabric**.

Użyj listy rozwijanej **profil docelowy** , aby wybrać plik profile. YAML, który ma być używany dla tego wdrożenia. Aktualizujemy aplikację w chmurze, więc wybieramy wartość **Cloud. YAML** na liście rozwijanej, która będzie używać wartości `WebFrontEnd_cpu` 1,0 zdefiniowanej w tym pliku.

![Okno dialogowe publikowania usługi Service Fabric Mesh w programie Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Wybierz konto i subskrypcję platformy Azure. Ustaw **lokalizację** do lokalizacji, która była używana podczas pierwotnie opublikowania aplikacji do wykonania na platformie Azure. W tym artykule użyto **Wschodnie stany USA**.

W polu **Grupa zasobów** wybierz grupę zasobów, która została użyta podczas pierwotnie opublikowanej aplikacji do wykonania na platformie Azure.

Ustaw **Azure Container Registry** na nazwę rejestru kontenerów platformy Azure, która została utworzona podczas publikowania aplikacji do wykonania na platformie Azure.

W oknie dialogowym Publikowanie kliknij przycisk **Publikuj** , aby uaktualnić aplikację do wykonania na platformie Azure.

Monitoruj postęp uaktualniania, zaznaczając okienko **Service Fabric Tools** w oknie **danych wyjściowych** programu Visual Studio. 

Po skompilowaniu i wypchnięciu obrazu do Azure Container Registry w danych wyjściowych zostanie wyświetlony link do **stanu** , który można kliknąć, aby monitorować wdrożenie w Azure Portal.

Po zakończeniu uaktualniania dane wyjściowe **Service Fabric Tools** będą wyświetlały adres IP i port aplikacji w postaci adresu URL.

```json
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

Otwórz przeglądarkę internetową i przejdź do adresu URL, aby wyświetlić witrynę internetową działającą na platformie Azure. Powinna zostać wyświetlona strona sieci Web, która zawiera kolumnę kategorii.

## <a name="next-steps"></a>Następne kroki

W tej części samouczka przedstawiono informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Jak uaktualnić aplikację siatki Service Fabric przy użyciu programu Visual Studio

Przejdź do następnego samouczka:
> [!div class="nextstepaction"]
> [Czyszczenie zasobów usługi Service Fabric Mesh](service-fabric-mesh-tutorial-cleanup-resources.md)