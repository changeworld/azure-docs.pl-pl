---
title: Jednostki SKU modułu usługi Azure IoT Edge | Azure Marketplace
description: Tworzenie jednostek SKU dla modułu usługi IoT Edge.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: dsindona
ms.openlocfilehash: 49ae69263735d6ec35cd911a20100472b32c0b59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286561"
---
# <a name="iot-edge-module-skus-tab"></a>Karta Jednostki SKU modułu usługi IoT

Karta **Jednostki SKU** na stronie **Nowa oferta** umożliwia utworzenie co najmniej jednej jednostki SKU i skojarzenie ich z nową ofertą.  Można użyć różnych jednostek SKU do rozróżniania rozwiązania według zestawów funkcji, modeli rozliczeń lub innych cech.


## <a name="sku-settings"></a>Ustawienia jednostki SKU

Po rozpoczęciu tworzenia nowej oferty nie ma żadnych jednostek SKU skojarzonych z ofertą. Aby utworzyć nową jednostkę SKU, wykonaj następujące kroki:

- Na stronie **Moduły brzegowe IoT > nowa oferta** wybierz kartę **Jednostki SKU.**
- W obszarze Jednostki SKU wybierz **pozycję + Nowa jednostka SKU,** aby otworzyć okno dialogowe.

  ![Przycisk Nowa jednostka SKU na karcie Nowa oferta dla modułów IoT Edge](./media/iot-edge-module-skus-tab-new-sku.png)

- W oknie dialogowym **Nowa jednostka SKU** wprowadź identyfikator jednostki SKU, a następnie wybierz przycisk **OK**.
(Poniższa tabela zawiera konwencje nazewnictwa identyfikatorów).

Karta **Jednostki SKU** są odświeżane i są wyświetlane pola edytowane w celu skonfigurowania jednostki SKU. Gwiazdka (*) dołączona do nazwy pola wskazuje, że jest ona wymagana.

|  **Pole**       |     **Opis**                                                          |
|  ---------       |     ---------------                                                          |
| **Identyfikator jednostki SKU\***       | Identyfikator dla tej jednostki SKU. Ta nazwa ma maksymalnie 50 znaków, składających się z małych liter alfanumeryczne znaki lub myślniki (-), ale nie może zakończyć się myślnikiem. **Uwaga:** Nie można zmienić tej nazwy po opublikowaniu oferty. Nazwa jest publicznie widoczna w adresach URL produktów. |
|  |  |


## <a name="sku-details"></a>SKU Szczegóły

Skonfiguruj **szczegóły jednostki SKU,** aby zdefiniować sposób wyświetlania jednostki SKU w witrynach sieci Web w portalu Azure Marketplace i witrynie Azure Portal.

![Metadane sku modułu IoT Edge](media/iot-edge-module-skus-tab-metadata.png)

W poniższej tabeli opisano cel, zawartość i formatowanie pól w obszarze **Szczegóły jednostki SKU**. Wymagane pola są oskarżane gwiazdką (*).

|  **Pole**       |     **Opis**                                                          |
|  ---------       |     ---------------                                                          |
| **Tytuł\***        | Tytuł tej jednostki SKU. Maksymalna długość 50 znaków. <br/> Zostanie on wyświetlony w witrynie Azure Portal i będzie używany jako domyślna nazwa modułu (bez spacji i znaków specjalnych) po jego wdrożeniu. Zobacz poniższe zdjęcia, aby zobaczyć dokładnie, gdzie to pole jest wyświetlane.|
| **Podsumowanie\***      | Krótkie podsumowanie tej jednostki SKU. Maksymalna długość 100 znaków. **NIE** podsumowyj oferty, tylko jednostkę SKU.  To podsumowanie będzie wyświetlane w portalu Azure Marketplace. Zobacz poniższe zdjęcia, aby zobaczyć dokładnie, gdzie to pole jest wyświetlane.|
| **Opis\***  | Krótki opis tej jednostki SKU. Maksymalna długość 3000 znaków. NIE opisuj ofertę, ale tylko tę jednostkę SKU. Zostanie on wyświetlony w witrynie Azure Marketplace i w witrynie Azure portal. W witrynie Azure portal zostanie ona dołączona do opisu portalu marketplace opisującego ofertę zdefiniowaną na karcie Marketplace.  Może być taka sama jak podsumowanie jednostki SKU. Zobacz poniższe zdjęcia, aby zobaczyć dokładnie, gdzie to pole jest wyświetlane.|
| **Ukryj tę jednostkę SKU\*** | Zachowaj ustawienie domyślne, czyli **Nie**. |
|  |  |


### <a name="sku-example"></a>Przykład jednostki SKU

 W poniższych przykładach pokazano, jak pola **Tytuł**jednostki SKU, **Podsumowanie**i **Opis** są wyświetlane w różnych widokach.
 

#### <a name="on-the-azure-marketplace-website"></a>W witrynie sieci Web portalu Azure Marketplace:

- Patrząc na szczegóły jednostki SKU:

    ![Sposób wyświetlania jednostek SKU w witrynie sieci Web w portalu Azure Marketplace](media/iot-edge-module-ampdotcom-pdp-plans.png)


#### <a name="on-the-azure-portal-website"></a>W witrynie sieci Web usługi Azure Portal:

- Podczas przeglądania jednostek SKU:

    ![Jak pokazuje się moduł usługi IoT Edge podczas przeglądania witryny Azure portal #1](media/iot-edge-module-portal-browse.png)

    ![Jak pokazuje się moduł usługi IoT Edge podczas przeglądania witryny Azure portal #2](media/iot-edge-module-portal-product-picker.png)

- Podczas wyszukiwania jednostek SKU:

    ![Jak pokazuje się moduł Usługi IoT Edge podczas przeszukiwania witryny Azure portal](media/iot-edge-module-portal-search.png)

- Patrząc na szczegóły jednostki SKU:

    ![Jak pokazuje się moduł IoT Edge, gdy szuka się szczegółów produktu w portalu](./media/iot-edge-module-portal-pdp.png)

- Podczas wdrażania modułu:
    
    ![Jak pokazuje się moduł IoT Edge podczas wdrażania](./media/iot-edge-module-deployment.png)


## <a name="sku-content"></a>Zawartość jednostki SKU

W **obszarze Obrazy modułów brzegowych**podaj informacje potrzebne do przesłania modułu Usługi IoT Edge.

Zapewnij nam dostęp do [rejestru kontenerów platformy Azure](https://azure.microsoft.com/services/container-registry/) (ACR), który zawiera obraz modułu usługi IoT Edge, dzięki czemu możemy go przekazać i certyfikować. Po opublikowaniu moduł usługi IoT Edge zostanie skopiowany i rozpowszechniony przy użyciu publicznego rejestru kontenerów hostowanego przez witrynę Azure Marketplace.

Możesz kierować wiele platform i udostępniać kilka wersji za pomocą tagów. Dowiedz się więcej o [tagach i przechowywanie wersji w "Przygotowanie zasobów technicznych modułu Usługi IoT Edge"](./cpp-create-technical-assets.md).

![Obrazy modułów krawędzi IoT](./media/iot-edge-module-skus-tab-acr.png)

W poniższej tabeli opisano przeznaczenie, zawartość i formatowanie pól sekcji **Szczegóły repozytorium obrazów** i **wersja obrazu**.  Wymagane pola są oskarżane gwiazdką (*).


|  **Pole**       |     **Opis**                                                          |
|  ---------       |     ---------------                                                          |
|  |  ***Szczegóły repozytorium obrazów***    |
| **Identyfikator subskrypcji\***        | Identyfikator subskrypcji platformy Azure usługi ACR.|
| **Nazwa grupy zasobów\***      | Nazwa grupy zasobów usługi ACR.|
| **Nazwa rejestru\***  | Twoja nazwa rejestru ACR. Tylko skopiować nazwę rejestru, a nie nazwę serwera `azurecr.io`logowania (na przykład bez .) |
| **Nazwa repozytorium\***  | Nazwa repozytorium usługi ACR zawierającej moduł usługi IoT Edge. **Uwaga:** Po ustawieniu nazwy nie można jej później zmienić. Użyj unikatowej nazwy, aby upewnić się, że żadna inna oferta na Twoim koncie nie ma tej samej nazwy. |
| **Nazwę użytkownika\*** | Nazwa użytkownika skojarzona z twoją nazwą użytkownika ACR (administratora). |
| **Hasło\*** | Hasło skojarzone z urządzeniem ACR. |
|    |  ***Wersja obrazu***   |
| **Znacznik lub skrót obrazu\*** | Musi zawierać co `latest` najmniej tag i tag wersji (na przykład, począwszy od `xx.xx.xx-` tego, gdzie xx jest liczbą). Powinny one być [manifest tagi](https://github.com/estesp/manifest-tool) do kierowania na wiele platform. Wszystkie tagi, do których odwołuje się znacznik manifestu, muszą również zostać dodane, abyśmy mogli je przesłać. Za pomocą tagów można dodać kilka wersji modułu usługi IoT Edge. Wszystkie znaczniki manifestu (z wyjątkiem) `latest`muszą zaczynać się od jednego `X.Y-` lub `X.Y.Z-` miejsca, w którym X, Y, Z są liczby całkowite. Dowiedz się więcej o [tagach i przechowywanie wersji w "Przygotowanie zasobów technicznych modułu Usługi IoT Edge"](./cpp-create-technical-assets.md). <br/> Na przykład, `latest` jeśli znacznik wskazuje `1.0.1-linux-x64`na `1.0.1-linux-arm32`to `1.0.1-windows-arm32`wskazuje , , i , te 6 tagów musi zostać dodany tutaj. |
|  |  |


### <a name="help-your-customers-launch-by-using-default-settings"></a>Pomóż klientom uruchomić przy użyciu ustawień domyślnych

Zdefiniuj najczęściej stosowane ustawienia, aby wdrożyć moduł IoT Edge. Zoptymalizuj wdrożenia klientów, umożliwiając im uruchomienie modułu Usługi IoT Edge po wyjęciu z tych ustawień domyślnych.

![Domyślne ustawienia modułu usługi IoT Edge podczas wdrażania](./media/iot-edge-module-skus-tab-iot-edge-defaults.png)

W poniższej tabeli opisano przeznaczenie, zawartość i formatowanie pól dla **tras domyślnych,** **Domyślne właściwości — dwie wartości żądane,** **Domyślne zmienne środowiskowe**i **Domyślne opcje tworzenia.**

|  **Pole**       |     **Opis**                                                          |
|  ---------       |     ---------------                                                          |
| **Trasy domyślne**        | Każda domyślna nazwa i wartość trasy musi być mniejsza niż 512 znaków. Można zdefiniować maksymalnie 5 tras domyślnych. Upewnij się, że w wartości trasy użyto poprawnej [składni trasy.](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes) Aby odwołać się do modułu, użyj jego domyślnej nazwy modułu, która będzie **tytułem jednostki SKU** bez spacji i znaków specjalnych. Aby odwołać się do innych modułów jeszcze nie znanych, należy użyć `<FROM_MODULE_NAME>` konwencji, aby poinformować klientów, że muszą zaktualizować te informacje. Dowiedz się więcej o [trasach IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes). <br/> Na przykład, `ContosoModule` jeśli moduł nasłuchuje danych wejściowych na `ContosoInput` i danych wyjściowych w `ContosoOutput`programie , warto zdefiniować następujące 2 trasy domyślne:<br/>- Imię #1:`ToContosoModule`<br/>- Wartość #1:`FROM /messages/modules/<FROM_MODULE_NAME>/outputs/* INTO BrokeredEndpoint("/modules/ContosoModule/inputs/ContosoInput")`<br/>- Imię #2:`FromContosoModuleToCloud`<br/>- Wartość #2:`FROM /messages/modules/ContonsoModule/outputs/ContosoOutput INTO $upstream`<br/>  |
| **Domyślne właściwości — dwójce — żądane**      | Każda domyślna nazwa i wartość właściwości bliźniaczej reprezentacji musi być mniejsza niż 512 znaków. Można zdefiniować maksymalnie 5 nazw/wartości bliźniaczej reprezentacji żądanych właściwości. Wartości wstępnie pożądanych właściwości musi być prawidłowy JSON, bez zmiany znaczenia, bez tablic i z maksymalną hierarchii zagnieżdżonej 4. Dowiedz się więcej o [bliźniaczych żądanych właściwościach](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties). <br/> Na przykład jeśli moduł obsługuje dynamicznie konfigurowalną częstotliwość odświeżania za pomocą dwóch żądanych właściwości, warto zdefiniować następującą domyślną właściwość bliźniaczej reprezentacji:<br/> - Imię #1:`RefreshRate`<br/>- Wartość #1:`60`|
| **Domyślne zmienne środowiskowe**  | Każda domyślna nazwa zmiennych środowiskowych i wartość musi być mniejsza niż 512 znaków. Można zdefiniować maksymalnie 5 zmiennych środowiskowych nazwy/wartości. <br/>Na przykład jeśli moduł wymaga zaakceptowania warunków użytkowania przed rozpoczęciem, można zdefiniować następującą zmienną środowiskową:<br/> - Imię #1:`ACCEPT_EULA`<br/>- Wartość #1:`Y`|
| **Domyślne opcje tworzenia**  | CreateOptions musi być mniejsza niż 512 znaków. Musi być prawidłowy JSON, bezumiejsce. Dowiedz się więcej o [createOptions](https://docs.microsoft.com/azure/iot-edge/module-composition#configure-modules). <br/> Na przykład jeśli moduł wymaga powiązania portu, można zdefiniować następujące createOptions:<br/>  `"HostConfig":{"PortBindings":{"5012/tcp":[{"HostPort":"5012"}]}`|
|   |   |

Wybierz **pozycję Zapisz,** aby zapisać ustawienia jednostki SKU. 


## <a name="next-steps"></a>Następne kroki

Użyj [karty Marketplace,](./cpp-marketplace-tab.md) aby utworzyć opis witryny dla swojej oferty.
