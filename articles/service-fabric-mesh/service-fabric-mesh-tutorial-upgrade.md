---
title: Samouczek dotyczący uaktualniania aplikacji usługi Azure Service Fabric siatki | Dokumentacja firmy Microsoft
description: Dowiedz się, jak uaktualnić aplikację usługi Service Fabric przy użyciu programu Visual Studio
services: service-fabric-mesh
documentationcenter: .net
author: dkkapur
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/29/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 23809abd06d626eb87e5d5d15d265f1769b97b66
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60809087"
---
# <a name="tutorial-learn-how-to-upgrade-a-service-fabric-application-using-visual-studio"></a>Samouczek: Dowiedz się, jak uaktualnić aplikację usługi Service Fabric przy użyciu programu Visual Studio

Niniejszy samouczek jest czwartą częścią serii i pokazuje, jak uaktualnić aplikację usługi Azure Service Fabric siatki bezpośrednio z programu Visual Studio. Uaktualnienie będzie zawierać aktualizacji konfiguracji i aktualizacji kodu. Zobaczysz, że proces uaktualniania i publikowanie z poziomu programu Visual Studio są takie same.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Uaktualnienie usługi Service Fabric siatki za pomocą programu Visual Studio

Ta seria samouczków zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * [Tworzenie aplikacji usługi Service Fabric Mesh w programie Visual Studio](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Debugowanie aplikacji usługi Service Fabric Mesh działającej w lokalnym klastrze projektowym](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Wdrażanie aplikacji usługi Service Fabric Mesh](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * Uaktualnienie usługi Service Fabric siatki aplikacji
> * [Czyszczenie zasobów usługi Service Fabric Mesh](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka:

* Jeśli nie została wdrożona aplikacja z listą zadań do wykonania, postępuj zgodnie z instrukcjami zamieszczonymi w artykule [Publikowanie aplikacji internetowej usługi Service Fabric Mesh](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md).

## <a name="upgrade-a-service-fabric-mesh-service-by-using-visual-studio"></a>Uaktualnienie usługi Service Fabric siatki za pomocą programu Visual Studio

W tym artykule pokazano, jak uaktualnić mikrousług w aplikacji. W tym przykładzie zmodyfikujemy `WebFrontEnd` usługi do wyświetlania kategorii zadań i zwiększyć moc procesora CPU jest mu nadawana. Firma Microsoft będzie uaktualnienie wdrożonej usługi.

## <a name="modify-the-config"></a>Modyfikowanie konfiguracji

Podczas tworzenia aplikacji usługi Service Fabric siatki Visual studio dodaje **parameters.yaml** pliku dla poszczególnych środowisk wdrażania (w chmurze i lokalnych). W tych plikach można zdefiniować parametry i ich wartości, które następnie mogą być przywoływane z plików *.yaml siatki, takie jak service.yaml lub network.yaml.  Visual Studio zawiera niektóre zmienne, takie jak Procesora mogą być używane przez usługę.

Zaktualizujemy `WebFrontEnd_cpu` parametru, aby zaktualizować zasobów procesora cpu `1.5` w oczekiwaniu, **WebFrontEnd** usługi będzie bardziej intensywnie używany.

1. W **todolistapp** projektu w obszarze **środowisk** > **chmury**, otwórz **parameters.yaml** pliku. Modyfikowanie `WebFrontEnd_cpu`, wartość `1.5`. Nazwa parametru jest poprzedzona nazwą usługi `WebFrontEnd_` najlepszym rozwiązaniem, w odróżnieniu od parametrów o takiej samej nazwie, które są stosowane do różnych usług.

    ```xml
    WebFrontEnd_cpu: 1.5
    ```

2. Otwórz **WebFrontEnd** projektu **service.yaml** plik **WebFrontEnd** > **zasobów usługi**.

    Należy pamiętać, że w `resources:` sekcji `cpu:` ustawiono `"[parameters('WebFrontEnd_cpu')]"`. Jeśli projekt jest tworzona dla chmury, a wartość `'WebFrontEnd_cpu` zostaną pobrane z **środowisk** > **chmury** > **parameters.yaml** pliku, zostanie ona `1.5`. Jeśli projekt jest kompilowana do uruchomienia lokalnie, wartość zostanie pobrana z **środowisk** > **lokalnego** > **parameters.yaml** pliku, i będzie "0.5".

> [!Tip]
> Domyślnie plik parametrów, który jest równorzędny pliku profile.yaml będzie służyć do Podaj wartości dla tego pliku profile.yaml.
> Na przykład środowiska > chmura > parameters.yaml zawiera wartości parametrów dla środowisk > chmura > profile.yaml.
>
> Możesz przesłonić to przez dodanie poniższego pliku profile.yaml:`parametersFilePath=”relative or full path to the parameters file”` Na przykład `parametersFilePath=”C:\MeshParms\CustomParameters.yaml”` lub `parametersFilePath=”..\CommonParameters.yaml”`

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

Czy wprowadzasz uaktualniania kodu lub uaktualnienia konfiguracji (w tym przypadku stosujemy metodykę zarówno), uaktualnienie aplikacji usługi Service Fabric siatki na platformie Azure, klikając prawym przyciskiem myszy **todolistapp** w programie Visual Studio, a następnie wybierz **publikowania...**

Następnie zobaczysz okno dialogowe **Publikowanie aplikacji usługi Service Fabric**.

Użyj **docelowy profil** listę rozwijaną, aby wybrać plik profile.yaml do użycia dla tego wdrożenia. Firma Microsoft jest uaktualniany aplikacji w chmurze, więc wybieramy **cloud.yaml** na liście rozwijanej, która będzie używać `WebFrontEnd_cpu` wartość 1,0 zdefiniowane w tym pliku.

![Okno dialogowe publikowania usługi Service Fabric Mesh w programie Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Wybierz konto i subskrypcję platformy Azure. Ustaw **lokalizacji** do lokalizacji, która została użyta podczas aplikacja to-do oryginalnie opublikowane na platformie Azure. W tym artykule używane **wschodnie stany USA**.

Ustaw **grupy zasobów** do grupy zasobów, która została użyta podczas aplikacja to-do oryginalnie opublikowane na platformie Azure.

Ustaw **usługi Azure Container Registry** do nazwy rejestru kontenerów platformy azure, który został utworzony, gdy aplikacja to-do pierwotnie opublikowany na platformie Azure.

W oknie dialogowym publikowania naciśnij **Publikuj** przycisk, aby uaktualnić aplikację do wykonania na platformie Azure.

Monitoruj postęp uaktualnienia, wybierając **narzędzia usługi Service Fabric** programu Visual Studio okienko **dane wyjściowe** okna. 

Po utworzone i wypchnięte do usługi Azure Container Registry obrazu **stanu** w danych wyjściowych, który można kliknąć, aby monitorować wdrożenie w witrynie Azure portal pojawi się łącze.

Po zakończeniu uaktualniania **narzędzia usługi Service Fabric** dane wyjściowe będą wyświetlane adresu IP i portu aplikacji w postaci adresu URL.

```json
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

Otwórz przeglądarkę internetową i przejdź do adresu URL, aby wyświetlić witrynę internetową działającą na platformie Azure. Powinna zostać wyświetlona strony sieci web, która zawiera kolumny kategorii.

## <a name="next-steps"></a>Kolejne kroki

W tej części samouczka przedstawiono informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Jak uaktualnić aplikację usługi Service Fabric siatki za pomocą programu Visual Studio

Przejdź do następnego samouczka:
> [!div class="nextstepaction"]
> [Czyszczenie zasobów usługi Service Fabric Mesh](service-fabric-mesh-tutorial-cleanup-resources.md)