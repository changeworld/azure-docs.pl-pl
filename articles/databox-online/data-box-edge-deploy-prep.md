---
title: Samouczek dotyczący przygotowania witryny Azure portal, środowiska centrum danych do wdrożenia usługi Azure Data Box Edge | Dokumenty firmy Microsoft
description: Pierwszy samouczek dotyczący wdrażania usługi Azure Data Box Edge obejmuje przygotowywanie witryny Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 06/03/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 90ed4bf8f0389619f130e998ed76c720442092b2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79474479"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-edge"></a>Samouczek: Przygotowanie do wdrożenia usługi Azure Data Box Edge  

To jest pierwszy samouczek z serii samouczków dotyczących wdrażania, wymaganych do pełnego wdrożenia usługi Azure Data Box Edge. W tym samouczku opisano sposób przygotowania witryny Azure Portal do wdrożenia zasobu usługi Data Box Edge.

Do ukończenia procesu instalacji i konfiguracji niezbędne są uprawnienia administratora. Przygotowanie portalu zajmuje mniej niż 10 minut.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Tworzenie nowego zasobu
> * Uzyskiwanie klucza aktywacji

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

### <a name="get-started"></a>Wprowadzenie

Proces wdrażania usługi Data Box Edge opisano w następujących samouczkach w zalecanej kolejności.

| **#** | **W tym kroku** | **Skorzystaj z tych dokumentów** |
| --- | --- | --- | 
| 1. |**[Przygotowywanie witryny Azure Portal na potrzeby usługi Data Box Edge](data-box-edge-deploy-prep.md)** |Utwórz i skonfiguruj zasób usługi Data Box Edge, zanim zainstalujesz urządzenie fizyczne Data Box Edge. |
| 2. |**[Instalowanie krawędzi pola danych](data-box-edge-deploy-install.md)**|Rozpakuj, umieść na stojaku i podłącz kable urządzenia fizycznego Data Box Edge.  |
| 3. |**[Łączenie, konfigurowanie i aktywowanie urządzenia Data Box Edge](data-box-edge-deploy-connect-setup-activate.md)** |Nawiąż połączenie z lokalnym internetowym interfejsem użytkownika, przeprowadź konfigurację urządzenia i je aktywuj. Urządzenie jest gotowe do skonfigurowania udziałów SMB lub NFS.  |
| 4. |**[Przesyłanie danych za pomocą usługi Data Box Edge](data-box-edge-deploy-add-shares.md)** |Dodaj udziały i nawiąż z nimi połączenie za pomocą protokołu SMB lub NFS. |
| 5. |**[Przekształcanie danych za pomocą krawędzi pola danych](data-box-edge-deploy-configure-compute.md)** |Skonfiguruj moduły obliczeniowe na urządzeniu, aby przekształcać dane w miarę przenoszenia ich na platformę Azure. |

Teraz możesz rozpocząć konfigurowanie witryny Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Poniżej opisano wymagania wstępne dotyczące konfiguracji zasobu usługi Data Box Edge, urządzenia Data Box Edge i sieci centrum danych.

### <a name="for-the-data-box-edge-resource"></a>Zasób usługi Data Box Edge

Przed rozpoczęciem upewnij się, że:

* Twoja subskrypcja platformy Microsoft Azure jest włączona dla zasobu usługi Azure Stack Edge. Upewnij się, że była używana obsługiwana subskrypcja, taka jak [Microsoft Enterprise Agreement (EA),](https://azure.microsoft.com/overview/sales-number/) [Dostawca rozwiązań w chmurze (CSP)](https://docs.microsoft.com/partner-center/azure-plan-lp)lub [Sponsorowanie platformy Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0036p/).
* Masz dostęp właściciela lub współautora na poziomie grupy zasobów dla zasobów Data Box Edge/Data Box Gateway, IoT Hub i Azure Storage.
    - Aby utworzyć dowolny zasób Data Box Edge/Data Box Gateway, należy mieć uprawnienia jako współautora (lub wyższy) o zakresie na poziomie grupy zasobów. Należy również upewnić się, że `Microsoft.DataBoxEdge` dostawca jest zarejestrowany. Aby uzyskać informacje na temat sposobu rejestracji, przejdź do [strony Zarejestruj dostawcę zasobów](data-box-edge-manage-access-power-connectivity-mode.md#register-resource-providers).
    - Aby utworzyć dowolny zasób Usługi IoT Hub, upewnij się, że dostawca microsoft.devices jest zarejestrowany. Aby uzyskać informacje na temat sposobu rejestracji, przejdź do [strony Zarejestruj dostawcę zasobów](data-box-edge-manage-access-power-connectivity-mode.md#register-resource-providers).
    - Aby utworzyć zasób konta magazynu, ponownie potrzebujesz współautora lub wyższego zakresu dostępu na poziomie grupy zasobów. Usługa Azure Storage jest domyślnie zarejestrowanym dostawcą zasobów.
- Masz administratora lub użytkownika dostęp do interfejsu API programu Microsoft Graph. Aby uzyskać więcej informacji, zobacz [Odwołanie do uprawnień programu Microsoft Graph](https://docs.microsoft.com/graph/permissions-reference).
- Masz konto magazynu platformy Microsoft Azure z poświadczeniami dostępu.

### <a name="for-the-data-box-edge-device"></a>Urządzenie Data Box Edge

Przed wdrożeniem urządzenia fizycznego upewnij się, że są spełnione następujące warunki:

- Zapoznałeś się z informacjami o bezpieczeństwie zawartymi w paczce wysyłkowej.
- Masz gniazdo 1U dostępne w standardowym stelażu 19" w centrum danych do montażu w stelażu.
- Masz dostęp do płaskiej, stabilnej i poziomej powierzchni roboczej, gdzie można bezpiecznie umieścić urządzenie.
- Miejsce, w którym chcesz skonfigurować urządzenie, ma standardowe zasilanie prądem przemiennym z niezależnego źródła lub jednostkę dystrybucji zasilania na stojaku (PDU, rack power distribution unit) z zasilaczem UPS.
- Masz dostęp do urządzenia fizycznego.


### <a name="for-the-datacenter-network"></a>Sieć centrum danych

Przed rozpoczęciem upewnij się, że:

- Sieć w centrum danych jest skonfigurowana zgodnie z wymaganiami sieciowymi dla urządzenia Data Box Edge. Aby uzyskać więcej informacji, zobacz [wymagania systemowe usługi Data Box Edge](data-box-edge-system-requirements.md).

- W normalnych warunkach pracy urządzenia Data Box Edge dostępne są:

    - Przepustowość pobierania co najmniej 10 Mb/s w celu zapewnienia, że urządzenie pozostanie na bieżąco.
    - Co najmniej 20 Mb/s dedykowanej przepustowości wysyłania i pobierania do przesyłania plików.

## <a name="create-a-new-resource"></a>Tworzenie nowego zasobu

Jeśli masz istniejący zasób usługi Data Box Edge, którego możesz użyć do zarządzania urządzeniem fizycznym, pomiń ten krok i przejdź do sekcji [Uzyskiwanie klucza aktywacji](#get-the-activation-key).

Aby utworzyć zasób usługi Data Box Edge, wykonaj poniższe czynności w witrynie Azure Portal.

1. Zaloguj się za pomocą poświadczeń platformy Microsoft Azure 
    
    - Portal Azure pod tym [https://portal.azure.com](https://portal.azure.com)adresem URL: .
    - Lub portal Azure Government pod [https://portal.azure.us](https://portal.azure.us)tym adresem URL: . Aby uzyskać więcej informacji, przejdź do [połącz się z platformą Azure Dla instytucji rządowych przy użyciu portalu](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal).

2. W lewym okienku wybierz pozycję **+ Utwórz zasób**. Wyszukaj **krawędź pola danych / bramę pola danych**. Wybierz **pozycję Data Box Edge / Data Box Gateway**. Wybierz **pozycję Utwórz**.
3. Wybierz subskrypcję, której chcesz użyć dla urządzenia Data Box Edge. Wybierz region, w którym chcesz wdrożyć zasób rozwiązania Data Box Edge. Aby uzyskać listę wszystkich regionów, w których dostępny jest zasób usługi Azure Stack Edge, zobacz [Produkty platformy Azure dostępne według regionów.](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)

    Wybierz lokalizację najbliżej regionu geograficznego, w którym chcesz wdrożyć urządzenie. Region przechowuje tylko metadane do zarządzania urządzeniami. Rzeczywiste dane mogą być przechowywane na dowolnym koncie magazynu.
    
    W opcji usługi **Data Box Edge** wybierz pozycję **Utwórz**.

    ![Wyszukiwanie usługi Data Box Edge](media/data-box-edge-deploy-prep/data-box-edge-sku.png)

3. Na karcie **Podstawy** wprowadź lub wybierz następujące **szczegóły projektu**.
    
    |Ustawienie  |Wartość  |
    |---------|---------|
    |Subskrypcja    |Jest to wypełniane automatycznie na podstawie wcześniejszego wyboru. Subskrypcja jest połączona z kontem rozliczeniowym. |
    |Grupa zasobów  |Wybierz istniejącą grupę lub utwórz nową.<br>Dowiedz się więcej o [grupach zasobów platformy Azure](../azure-resource-manager/management/overview.md).     |

4. Wprowadź lub wybierz następujące **szczegóły wystąpienia**.

    |Ustawienie  |Wartość  |
    |---------|---------|
    |Nazwa   | Przyjazna nazwa identyfikująca zasób.<br>Nazwa może zawierać od 2 do 50 znaków, w tym litery, cyfry i łączniki.<br> Nazwa rozpoczyna się i kończy literą lub cyfrą.        |
    |Region     |Aby uzyskać listę wszystkich regionów, w których dostępny jest zasób usługi Azure Stack Edge, zobacz [Produkty platformy Azure dostępne według regionów.](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all) Jeśli korzystasz z platformy Azure dla instytucji rządowych, wszystkie regiony instytucji rządowych są dostępne w sposób pokazany w [regionach platformy Azure.](https://azure.microsoft.com/global-infrastructure/regions/)<br> Wybierz lokalizację najbliżej regionu geograficznego, w którym chcesz wdrożyć urządzenie.|

    ![Szczegóły projektu i wystąpienia](media/data-box-edge-deploy-prep/data-box-edge-resource.png)

5. Wybierz **dalej: Adres wysyłki**.

    - Jeśli masz już urządzenie, wybierz pole kombi, ponieważ **mam urządzenie Data Box Edge**.
    - Jeśli jest to nowe urządzenie, które zamawiasz, wprowadź nazwę kontaktu, firmę, adres do wysłania urządzenia i informacje kontaktowe.

    ![Adres wysyłki nowego urządzenia](media/data-box-edge-deploy-prep/data-box-edge-resource1.png)

6. Wybierz **dalej: Przejrzyj + utwórz**.

7. Na karcie **Recenzja + tworzenie** przejrzyj szczegóły **cen**, **Warunki użytkowania**i szczegóły zasobu. Wybierz pole kombi dla **przejrzałem warunki prywatności**.

    ![Przejrzyj szczegóły zasobów data box edge i warunki dotyczące prywatności](media/data-box-edge-deploy-prep/data-box-edge-resource2.png)

8. Wybierz **pozycję Utwórz**.

Tworzenie zasobu trwa kilka minut. Po pomyślnym utworzeniu i wdrożeniu zasobu zostanie o tym powiadomiony. Wybierz pozycję **Przejdź do zasobu**.

![Przejdź do zasobu Krawędź pola danych](media/data-box-edge-deploy-prep/data-box-edge-resource3.png)

Po złożeniu zamówienia firma Microsoft przejrza zamówienie i dociera do Ciebie (za pośrednictwem poczty e-mail) ze szczegółami wysyłki.

![Powiadomienie o przeglądzie kolejności data box edge](media/data-box-edge-deploy-prep/data-box-edge-resource4.png)

## <a name="get-the-activation-key"></a>Uzyskiwanie klucza aktywacji

Po skonfigurowaniu i uruchomieniu zasobu usługi Data Box Edge konieczne będzie uzyskanie klucza aktywacji. Ten klucz jest używany do aktywowania urządzenia Data Box Edge i nawiązania połączenia z zasobem. Ten klucz można uzyskać już teraz za pośrednictwem witryny Azure Portal.

1. Wybierz utworzony zasób. Wybierz **pozycję Przegląd,** a następnie wybierz pozycję **Konfiguracja urządzenia**.

    ![Wybierz pozycję Konfiguracja urządzenia](media/data-box-edge-deploy-prep/data-box-edge-select-devicesetup.png)

2. Na kafelku **Aktywuj** wybierz pozycję **Generuj klucz,** aby utworzyć klucz aktywacyjny. Wybierz ikonę kopiowania, aby skopiować klucz i zapisać go do użytku w przyszłości.

    ![Pobieranie klucza aktywacji](media/data-box-edge-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - Klucz aktywacji wygasa po trzech dniach od jego wygenerowania.
> - Jeśli klucz wygaśnie, wygeneruj nowy klucz. Starszy klucz nie jest prawidłowy.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono zagadnienia dotyczące usługi Data Box Edge, takie jak:

> [!div class="checklist"]
> * Tworzenie nowego zasobu
> * Uzyskiwanie klucza aktywacji

Przejdź do następnego samouczka, aby dowiedzieć się, jak zainstalować urządzenie Data Box Edge.

> [!div class="nextstepaction"]
> [Instalowanie urządzenia Data Box Edge](./data-box-edge-deploy-install.md)



