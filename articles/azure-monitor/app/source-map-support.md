---
title: Obsługa map źródłowych dla aplikacji JavaScript — usługa Azure Monitor Application Insights
description: Dowiedz się, jak przekazać mapy źródłowe do kontenera obiektów Blob konta magazynu przy użyciu usługi Application Insights.
ms.topic: conceptual
author: markwolff
ms.author: marwolff
ms.date: 03/04/2020
ms.openlocfilehash: 4b452b31338760a8f53eed54420319101836bc00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474887"
---
# <a name="source-map-support-for-javascript-applications"></a>Obsługa mapy źródłowej dla aplikacji JavaScript

Usługa Application Insights obsługuje przekazywanie map źródłowych do własnego kontenera obiektów blob konta magazynu.
Mapy źródłowe mogą służyć do ujednolicenia stosów wywołań znalezionych na stronie szczegółów transakcji od końca do końca. Każdy wyjątek wysłany przez [javascript SDK][ApplicationInsights-JS] lub [Node.js SDK][ApplicationInsights-Node.js] można unminified z map źródłowych.

![Ujednolicenie stosu połączeń przez połączenie z kontem magazynu](./media/source-map-support/details-unminify.gif)

## <a name="create-a-new-storage-account-and-blob-container"></a>Tworzenie nowego konta magazynu i kontenera obiektów Blob

Jeśli masz już istniejące konto magazynu lub kontener obiektów blob, możesz pominąć ten krok.

1. [Tworzenie nowego konta magazynu][create storage account]
2. [Utwórz kontener obiektów blob][create blob container] wewnątrz konta magazynu. Pamiętaj, aby ustawić "Poziom dostępu `Private`publicznego" na , aby upewnić się, że mapy źródłowe nie są publicznie dostępne.

> [!div class="mx-imgBorder"]
>![Poziom dostępu do kontenera musi być ustawiony na](./media/source-map-support/container-access-level.png)

## <a name="push-your-source-maps-to-your-blob-container"></a>Wypychanie map źródłowych do kontenera obiektów Blob

Potok ciągłego wdrażania należy zintegrować z kontem magazynu, konfigurując go w celu automatycznego przekazywania map źródłowych do skonfigurowanym kontenerze obiektów blob. Nie należy przekazywać map źródłowych do podfolderu w kontenerze obiektów blob; obecnie mapa źródłowa zostanie pobrana tylko z folderu głównego.

### <a name="upload-source-maps-via-azure-pipelines-recommended"></a>Przekaż mapy źródłowe za pośrednictwem potoków platformy Azure (zalecane)

Jeśli używasz usługi Azure Potoki do ciągłego tworzenia i wdrażania aplikacji, dodaj zadanie [kopiowania plików platformy Azure][azure file copy] do potoku, aby automatycznie przekazać mapy źródłowe.

> [!div class="mx-imgBorder"]
> ![Dodawanie zadania kopiowania plików platformy Azure do potoku w celu przekazania map źródłowych do usługi Azure Blob Storage](./media/source-map-support/azure-file-copy.png)

## <a name="configure-your-application-insights-resource-with-a-source-map-storage-account"></a>Konfigurowanie zasobu usługi Application Insights przy za pomocą konta magazynu mapy źródłowej

### <a name="from-the-end-to-end-transaction-details-page"></a>Ze strony szczegółów transakcji typu end-to-end

Na karcie szczegóły transakcji end-to-end można kliknąć *przycisk Ujednolicenie* i wyświetli monit o skonfigurowanie, jeśli zasób jest nieskonfigurowany.

1. W portalu, zobacz szczegóły wyjątku, który jest zdominowany.
2. Kliknij na *Ujednolicenie*
3. Jeśli zasób nie został skonfigurowany, pojawi się komunikat z monitem o skonfigurowanie.

### <a name="from-the-properties-page"></a>Na stronie właściwości

Jeśli chcesz skonfigurować lub zmienić konto magazynu lub kontener obiektów Blob, który jest połączony z zasobem usługi Application Insights, możesz to zrobić, wyświetlając kartę *Właściwości* zasobu usługi Application Insights.

1. Przejdź do karty *Właściwości* zasobu usługi Application Insights.
2. Kliknij *zmień kontener obiektów blob mapy źródłowej*.
3. Wybierz inny kontener obiektów blob jako kontener map źródłowych.
4. Kliknij pozycję `Apply` (Dalej).

> [!div class="mx-imgBorder"]
> ![Ponowne konfigurowanie wybranego kontenera obiektów blob platformy Azure przez przejście do bloku właściwości](./media/source-map-support/reconfigure.png)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="required-role-based-access-control-rbac-settings-on-your-blob-container"></a>Wymagane ustawienia kontroli dostępu opartej na rolach (RBAC) w kontenerze obiektów Blob

Każdy użytkownik portalu przy użyciu tej funkcji musi być co najmniej przypisany jako [czytnik danych obiektów blob magazynu][storage blob data reader] do kontenera obiektów blob. Tę rolę należy przypisać innym osobom, które będą używać map źródłowych za pośrednictwem tej funkcji.

> [!NOTE]
> W zależności od sposobu utworzenia kontenera może to nie zostać automatycznie przypisane do Ciebie lub Twojego zespołu.

### <a name="source-map-not-found"></a>Nie znaleziono mapy źródłej

1. Sprawdź, czy odpowiednia mapa źródłowa jest przekazycona do właściwego kontenera obiektów blob
2. Sprawdź, czy plik mapy źródłowej został nazwany po pliku JavaScript, do którego jest mapowany, z `.map`przyrostkiem .
    - Na przykład `/static/js/main.4e2ca5fa.chunk.js` wyszuka obiekt blob o nazwie`main.4e2ca5fa.chunk.js.map`
3. Sprawdź konsolę przeglądarki, aby sprawdzić, czy są rejestrowane błędy. Uwzględnij to w dowolnym bilecie pomocy technicznej.

## <a name="next-steps"></a>Następne kroki

* [Zadanie kopiowania plików platformy Azure](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops)


<!-- Remote URLs -->
[create storage account]: https://docs.microsoft.com/azure/storage/common/storage-account-create?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal
[create blob container]: https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal
[storage blob data reader]: https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader
[ApplicationInsights-JS]: https://github.com/microsoft/applicationinsights-js
[ApplicationInsights-Node.js]: https://github.com/microsoft/applicationinsights-node.js
[azure file copy]: https://aka.ms/azurefilecopyreadme