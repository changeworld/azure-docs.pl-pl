---
title: Moduł usługi Azure IoT Edge jednostki SKU | Portal Azure Marketplace
description: Tworzenie jednostki SKU dla modułu usługi IoT Edge.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pabutler
ms.openlocfilehash: bf8957c315909785112dea947f2e2e0f7b26349f
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64942190"
---
# <a name="iot-edge-module-skus-tab"></a>Karty jednostki SKU modułu usługi IoT Edge

**Jednostki SKU** karcie **nowa oferta** strona pozwala na tworzenie jednego lub więcej jednostek SKU i kojarzyć je z nowej oferty.  Różne jednostki SKU służy do rozróżnienia rozwiązania przez zestawy funkcji, modelami rozliczeń lub pewne inne cechy.


## <a name="sku-settings"></a>Ustawienia jednostki SKU

Po rozpoczęciu tworzenia nowej oferty, nie ma żadnych jednostek SKU skojarzone z ofertą. Aby utworzyć nowe jednostki SKU, wykonaj następujące kroki:

- Na **IoT Edge modułów > Nowa oferta** wybierz opcję **jednostki SKU** kartę.
- W obszarze jednostek SKU, wybierz **+ nowej jednostki SKU** aby otworzyć okno dialogowe.

  ![Przycisk Nowe jednostki SKU na karcie nowa oferta moduły usługi IoT Edge](./media/iot-edge-module-skus-tab-new-sku.png)

- Na **nowej jednostki SKU** okna dialogowego pole, wprowadź identyfikator dla obiektu SKU, a następnie wybierz **OK**.
(W poniższej tabeli przedstawiono konwencji nazewnictwa identyfikator).

**Jednostki SKU** karta jest odświeżane i wyświetla pola, które możesz edytować skonfigurować jednostkę SKU. Znak gwiazdki (*) dodanym na końcu nazwy pola wskazuje, że jest to wymagane.

|  **Pole**       |     **Opis**                                                          |
|  ---------       |     ---------------                                                          |
| **IDENTYFIKATOR JEDNOSTKI SKU\***       | Identyfikator dla tej jednostki SKU. Ta nazwa może zawierać maksymalnie 50 znaków, składający się z małych znaków alfanumerycznych i kresek (-), ale nie może kończyć się kreską. **Uwaga:** Opublikowane oferty nie można zmienić tej nazwy. Nazwa jest widoczna publicznie w adresach URL produktu. |
|  |  |


## <a name="sku-details"></a>Szczegóły jednostki SKU

Konfigurowanie **szczegółów jednostki SKU** zdefiniować sposób wyświetlania jednostki SKU w witrynach sieci Web portalu Azure Marketplace i witrynie Azure Portal.

![Metadane jednostki sku modułu usługi IoT Edge](media/iot-edge-module-skus-tab-metadata.png)

Poniższa tabela opisuje przeznaczenie, zawartości i formatowania w przypadku pól **szczegółów jednostki SKU**. Wymagane pola są wskazanych przez znak gwiazdki (*).

|  **Pole**       |     **Opis**                                                          |
|  ---------       |     ---------------                                                          |
| **Tytuł\***        | Tytuł dla tej jednostki SKU. Składać z maksymalnie 50 znaków. <br/> Zostaną wyświetlone w witrynie Azure Portal i będzie służyć jako domyślną nazwę modułu (bez spacji ani znaków specjalnych) po jej wdrożeniu. Zobacz poniżej, aby zobaczyć dokładnie, gdzie to pole jest wyświetlane obrazów.|
| **Podsumowanie\***      | Krótkie podsumowanie dotyczące tej jednostki SKU. Składać z maksymalnie 100 znaków. Czy **nie** Podsumowanie oferty, tylko jednostki SKU.  To podsumowanie będą wyświetlane w portalu Azure Marketplace. Zobacz poniżej, aby zobaczyć dokładnie, gdzie to pole jest wyświetlane obrazów.|
| **Opis elementu\***  | Krótki opis tej jednostki SKU. Maksymalna długość 3000 znaków. Nie opisują oferty, ale tylko tej jednostki SKU. Zostanie ona wyświetlona w portalu azure marketplace i w witrynie Azure portal. W witrynie Azure portal zostanie dołączony do opisu Marketplace opisu oferty zdefiniowane na karcie portalu Marketplace.  Może być taki sam jak podsumowanie jednostki SKU. Zobacz poniżej, aby zobaczyć dokładnie, gdzie to pole jest wyświetlane obrazów.|
| **Ukryj tej jednostki SKU\*** | Zachowaj ustawienie domyślne, czyli **nie**. |
|  |  |


### <a name="sku-example"></a>Przykład jednostki SKU

 W poniższych przykładach pokazano sposób jednostki SKU **tytuł**, **Podsumowanie**, i **opis** pola są wyświetlane w różnych widokach.
 

#### <a name="on-the-azure-marketplace-website"></a>W witrynie Azure Marketplace:

- Po wyświetleniu szczegółów jednostki SKU:

    ![Wyświetlanie jednostek SKU w witrynie Azure Marketplace](media/iot-edge-module-ampdotcom-pdp-plans.png)


#### <a name="on-the-azure-portal-website"></a>W witrynie Azure Portal:

- Podczas przeglądania jednostek SKU:

    ![Jak moduł usługi IoT Edge zostaną wyświetlone podczas przeglądania portalu Azure #1](media/iot-edge-module-portal-browse.png)

    ![Jak moduł usługi IoT Edge zostaną wyświetlone podczas przeglądania portalu Azure #2](media/iot-edge-module-portal-product-picker.png)

- Podczas wyszukiwania dla jednostki SKU:

    ![Jak moduł usługi IoT Edge zostaną wyświetlone podczas wyszukiwania w witrynie Azure portal](media/iot-edge-module-portal-search.png)

- Po wyświetleniu szczegółów jednostki SKU:

    ![Jak moduł usługi IoT Edge zostaną wyświetlone podczas wyszukiwania szczegóły produktu w portalu](./media/iot-edge-module-portal-pdp.png)

- Podczas wdrażania modułu:
    
    ![Jak moduł usługi IoT Edge zostaną wyświetlone podczas wdrażania](./media/iot-edge-module-deployment.png)


## <a name="sku-content"></a>Zawartość jednostki SKU

W obszarze **obrazy modułu krawędzi**, podaj informacje, należy przekazać modułu usługi IoT Edge.

Przekaż nam dostęp do Twojego [usługi Azure Container Registry](https://azure.microsoft.com/services/container-registry/) (ACR), który zawiera usługi IoT Edge modułu obrazu tak, aby firma Microsoft może przekazać go, a także zaświadczasz, go. Po opublikowaniu, moduł usługi IoT Edge zostaną skopiowane i rozpowszechniane za pośrednictwem rejestru publicznego kontenera pracujących w portalu Azure Marketplace.

Można wybierać docelowo wiele platform i zapewnia kilka wersji przy użyciu tagów. Dowiedz się więcej o [tagów i przechowywanie wersji w "Przygotowanie usługi IoT Edge zasoby techniczne modułu"](./cpp-create-technical-assets.md).

![Obrazy modułu usługi IoT Edge](./media/iot-edge-module-skus-tab-acr.png)

W poniższej tabeli opisano przeznaczenie zawartości i formatowania pól dla sekcji **szczegółów repozytorium obrazów** i **wersję obrazu**.  Wymagane pola są wskazanych przez znak gwiazdki (*).


|  **Pole**       |     **Opis**                                                          |
|  ---------       |     ---------------                                                          |
|  |  ***Szczegółowe informacje dotyczące obrazu repozytorium***    |
| **Identyfikator subskrypcji\***        | Identyfikator subskrypcji platformy Azure rekordu ACR.|
| **Nazwa grupy zasobów\***      | Nazwa grupy zasobów rekordu ACR.|
| **Nazwa rejestru\***  | Nazwa rejestru ACR. Kopiuj tylko nazwę rejestru, a nie nazwę serwera logowania (na przykład, bez `azurecr.io`.) |
| **Nazwa repozytorium\***  | Nazwa repozytorium usługi ACR, zawierający modułu usługi IoT Edge. **Uwaga:** Po ustawieniu nazwy nie można zmienić później. Użyj unikatowej nazwy, aby upewnić się, że żadne inne oferty w ramach Twojego konta ma taką samą nazwę. |
| **Nazwa użytkownika\*** | Nazwa użytkownika skojarzony z rekordu ACR (nazwa użytkownika administratora). |
| **Hasło\*** | Hasło skojarzone z rekordu ACR. |
|    |  ***Wersja obrazu***   |
| **Tag obrazu lub skrótu\*** | Musi zawierać co najmniej `latest` znacznikiem i wersji (na przykład, rozpoczynając od `xx.xx.xx-` gdzie xx jest liczbą). Powinny one być [manifestu tagi](https://github.com/estesp/manifest-tool) do wielu platform docelowych. Wszystkie tagi, które odwołuje się tag manifestu również musi zostać dodany, dzięki czemu możemy przekazać je. Możesz dodać różne wersje modułu usługi IoT Edge, przy użyciu tagów. Manifest wszystkie tagi (z wyjątkiem `latest`) musi rozpoczynać się albo `X.Y-` lub `X.Y.Z-` gdzie X, Y, Z są liczbami całkowitymi. Dowiedz się więcej o [tagów i przechowywanie wersji w "Przygotowanie usługi IoT Edge zasoby techniczne modułu"](./cpp-create-technical-assets.md). <br/> Na przykład jeśli `latest` tag wskazuje, na którą wskazuje `1.0.1-linux-x64`, `1.0.1-linux-arm32`,, a `1.0.1-windows-arm32`te 6 tagi należy dodać w tym miejscu. |
|  |  |


### <a name="help-your-customers-launch-by-using-default-settings"></a>Pomóc klientom uruchamianie przy użyciu ustawień domyślnych

Definiowanie najczęściej używane ustawienia, aby wdrożyć moduł usługi IoT Edge. Optymalizacja wdrożenia klienta, umożliwiając ich uruchamiania usługi IoT Edge moduł out-of box przy użyciu tych ustawień domyślnych.

![Ustawienia domyślne modułu usługi IoT Edge podczas wdrażania](./media/iot-edge-module-skus-tab-iot-edge-defaults.png)

W poniższej tabeli opisano przeznaczenie zawartości i formatowania pól dla **domyślne trasy**, **bliźniaczej reprezentacji domyślne żądane właściwości**, **domyślne zmienne środowiskowe**, i **domyślne CreateOptions, można żądań**.

|  **Pole**       |     **Opis**                                                          |
|  ---------       |     ---------------                                                          |
| **Trasy domyślne**        | Każdy domyślnej nazwy trasy i wartości musi być krótsza niż 512 znaków. Można zdefiniować maksymalnie 5 tras domyślnych. Pamiętaj użyć poprawne [trasy składni](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes) w swojej wartości trasy. Aby odwołać się do modułu, użyj domyślnej nazwy modułu, który będzie Twoja **tytuł jednostki SKU** bez spacji i znaków specjalnych. Aby odwołać się do innych modułów, które nie są jeszcze znane, należy użyć `<FROM_MODULE_NAME>` Konwencji Poinformuj klientów, aby zaktualizować te informacje. Dowiedz się więcej o [kieruje usługi IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes). <br/> Na przykład jeśli moduł `ContosoModule` dla danych wejściowych nasłuchuje `ContosoInput` i danych wyjściowych `ContosoOutput`, warto zdefiniować następujące trasy domyślne 2:<br/>— Nazwa #1: `ToContosoModule`<br/>-Wartość #1:`FROM /messages/modules/<FROM_MODULE_NAME>/outputs/* INTO BrokeredEndpoint("/modules/ContosoModule/inputs/ContosoInput")`<br/>— Nazwa #2: `FromContosoModuleToCloud`<br/>-Wartość #2: `FROM /messages/modules/ContonsoModule/outputs/ContosoOutput INTO $upstream`<br/>  |
| **Domyślne odpowiednich właściwości bliźniaka**      | Każda domyślna nazwa żądane właściwości bliźniaczej reprezentacji wartości muszą mieć mniej niż 512 znaków. Można zdefiniować maksymalnie 5 nazw i wartości odpowiednich właściwości bliźniaka. Wartości odpowiednich właściwości bliźniaka musi być prawidłowym plikiem JSON bez sekwencji specjalnych, bez tablic oraz o maksymalnej hierarchii zagnieżdżonych 4. Dowiedz się więcej o [bliźniaczej reprezentacji żądane właściwości](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties). <br/> Na przykład jeśli moduł obsługuje częstotliwość odświeżania dynamiczną konfigurację za pomocą odpowiednich właściwości bliźniaka, warto zdefiniować następującą właściwość domyślną w bliźniaczej reprezentacji żądanego:<br/> — Nazwa #1: `RefreshRate`<br/>-Wartość #1: `60`|
| **Zmienne środowiskowe domyślne**  | Każdy domyślnej nazwy zmiennych środowiskowych i wartości musi być krótsza niż 512 znaków. Można zdefiniować maksymalnie 5 zmiennych środowiskowych nazwa/wartość. <br/>Na przykład jeśli moduł wymaga, aby zaakceptować warunki użytkowania, zanim uruchamiana, można zdefiniować następującą zmienną środowiskową:<br/> — Nazwa #1: `ACCEPT_EULA`<br/>-Wartość #1: `Y`|
| **Domyślne CreateOptions, można żądań**  | CreateOptions, można żądań musi być krótsza niż 512 znaków. Musi być prawidłowym kodem JSON wyjściowym. Dowiedz się więcej o [CreateOptions, można żądań](https://docs.microsoft.com/azure/iot-edge/module-composition#configure-modules). <br/> Na przykład, jeśli moduł wymaga powiązanie portu, można zdefiniować następujące CreateOptions, można żądań:<br/>  `"HostConfig":{"PortBindings":{"5012/tcp":[{"HostPort":"5012"}]}`|
|   |   |

Wybierz **Zapisz** można zapisać ustawień jednostki SKU. 


## <a name="next-steps"></a>Kolejne kroki

Użyj [kartę Marketplace](./cpp-marketplace-tab.md) utworzyć portalu marketplace opis oferty.
