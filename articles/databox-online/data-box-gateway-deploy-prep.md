---
title: Samouczek dotyczący przygotowywania witryny Azure Portal do wdrożenia usługi Data Box Gateway | Microsoft Docs
description: Pierwszy samouczek dotyczący wdrażania usługi Azure Data Box Gateway obejmuje przygotowywanie witryny Azure Portal.
services: databox-edge-gateway
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: 74fec059bdffb91f5a7774d430e2f1897f0e863c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79474462"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-gateway"></a>Samouczek: Przygotowanie do wdrożenia bramy usługi Azure Data Box

To jest pierwszy samouczek z serii samouczków dotyczących wdrażania opisujących pełne wdrożenie usługi Azure Data Box Gateway. W tym samouczku opisano sposób przygotowania witryny Azure Portal do wdrażania zasobu usługi Data Box Gateway.

Do ukończenia procesu instalacji i konfiguracji niezbędne są uprawnienia administratora. Przygotowanie portalu zajmuje mniej niż 10 minut.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Tworzenie nowego zasobu
> * Pobieranie obrazu urządzenia wirtualnego
> * Uzyskiwanie klucza aktywacji

## <a name="get-started"></a>Wprowadzenie

Proces wdrażania usługi Data Box Gateway opisano w następujących samouczkach w zalecanej kolejności.

| **#** | **W tym kroku** | **Skorzystaj z tych dokumentów** |
| --- | --- | --- | 
| 1. |**[Przygotowywanie witryny Azure Portal na potrzeby usługi Data Box Gateway](data-box-gateway-deploy-prep.md)** |Utwórz i skonfiguruj zasób usługi Data Box Gateway przed aprowizowaniem urządzenia wirtualnego usługi Data Box Gateway. |
| 2. |**[Aprowizowanie usługi Data Box Gateway w funkcji Hyper-V](data-box-gateway-deploy-provision-hyperv.md)** <br><br><br>**[Aprowizowanie usługi Data Box Gateway w oprogramowaniu VMware](data-box-gateway-deploy-provision-vmware.md)**|W przypadku funkcji Hyper-V przeprowadź aprowizację i nawiąż połączenie z urządzeniem wirtualnym usługi Data Box Gateway w systemie hosta funkcji Hyper-V z systemem Windows Server 2016 lub Windows Server 2012 R2. <br><br><br> W przypadku oprogramowania VMware przeprowadź aprowizację i nawiąż połączenie z urządzeniem wirtualnym usługi Data Box Gateway w systemie hosta z oprogramowaniem VMware ESXi 6.0, 6.5 lub 6.7.<br></br> |
| 3. |**[Łączenie, konfigurowanie i aktywowanie usługi Data Box Gateway](data-box-gateway-deploy-connect-setup-activate.md)** |Nawiąż połączenie z lokalnym internetowym interfejsem użytkownika, przeprowadź konfigurację urządzenia i je aktywuj. Następnie można aprowizować udziały SMB.  |
| 4. |**[Przesyłanie danych za pomocą usługi Data Box Gateway](data-box-gateway-deploy-add-shares.md)** |Dodaj udziały i nawiąż z nimi połączenie za pomocą protokołu SMB lub NFS. |

Teraz możesz rozpocząć konfigurowanie witryny Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

W tym miejscu opisano wymagania wstępne dotyczące konfiguracji zasobu usługi Data Box Gateway, urządzenia usługi Data Box Gateway i sieci centrum danych.

### <a name="for-the-data-box-gateway-resource"></a>Zasób usługi Data Box Gateway

Przed rozpoczęciem upewnij się, że:

* Twoja subskrypcja platformy Microsoft Azure jest włączona dla zasobu usługi Azure Stack Edge. Upewnij się, że była używana obsługiwana subskrypcja, taka jak [Microsoft Enterprise Agreement (EA),](https://azure.microsoft.com/overview/sales-number/) [Dostawca rozwiązań w chmurze (CSP)](https://docs.microsoft.com/partner-center/azure-plan-lp)lub [Sponsorowanie platformy Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0036p/).
* Masz dostęp właściciela lub współautora na poziomie grupy zasobów dla zasobów Data Box Edge/Data Box Gateway, IoT Hub i Azure Storage.
    - Aby utworzyć dowolny zasób Data Box Edge/Data Box Gateway, należy mieć uprawnienia jako współautora (lub wyższy) o zakresie na poziomie grupy zasobów. Należy również upewnić się, że `Microsoft.DataBoxEdge` dostawca jest zarejestrowany. Aby uzyskać informacje na temat sposobu rejestracji, przejdź do [strony Zarejestruj dostawcę zasobów](data-box-gateway-manage-access-power-connectivity-mode.md#register-resource-providers).
    - Aby utworzyć zasób konta magazynu, ponownie potrzebujesz współautora lub wyższego zakresu dostępu na poziomie grupy zasobów. Usługa Azure Storage jest domyślnie zarejestrowanym dostawcą zasobów.
- Masz administratora lub użytkownika dostęp do interfejsu API programu Microsoft Graph. Aby uzyskać więcej informacji, zobacz [Odwołanie do uprawnień programu Microsoft Graph](https://docs.microsoft.com/graph/permissions-reference).
- Masz konto magazynu platformy Microsoft Azure z poświadczeniami dostępu.

### <a name="for-the-data-box-gateway-device"></a>Urządzenie usługi Data Box Gateway

Przed wdrożeniem urządzenia wirtualnego upewnij się, że są spełnione następujące warunki:

- Masz dostęp do hosta funkcji Hyper-V z systemem Windows Server 2012 R2 lub nowszym bądź z oprogramowaniem VMware (ESXi 6.0, 6.5 lub 6.7), którego można użyć do aprowizacji urządzenia.
- System hosta może przeznaczyć następujące zasoby wyłącznie na potrzeby aprowizacji urządzenia wirtualnego usługi Data Box:
  
  - Co najmniej 4 procesory wirtualne.
  - Co najmniej 8 GB pamięci RAM.
  - Jeden interfejs sieciowy.
  - Dysk systemu operacyjnego o rozmiarze 250 GB.
  - Dysk wirtualny o rozmiarze 2 TB do przechowywania danych systemu.

### <a name="for-the-datacenter-network"></a>Sieć centrum danych

Przed rozpoczęciem upewnij się, że:

- Sieć w centrum danych jest skonfigurowana zgodnie z wymaganiami sieciowymi dla urządzenia usługi Data Box Gateway. Aby uzyskać więcej informacji, zobacz [wymagania systemowe bramy usługi Data Box](data-box-gateway-system-requirements.md)Gateway .

- W normalnych warunkach pracy bramy data box należy mieć:

    - Przepustowość pobierania co najmniej 10 Mb/s w celu zapewnienia, że urządzenie pozostanie na bieżąco.
    - Co najmniej 20 Mb/s dedykowanej przepustowości wysyłania i pobierania do przesyłania plików.

## <a name="create-a-new-resource"></a>Tworzenie nowego zasobu

Jeśli masz istniejący zasób usługi Data Box Gateway, którego możesz użyć do zarządzania urządzeniami wirtualnymi, pomiń ten krok i przejdź do sekcji [Uzyskiwanie klucza aktywacji](#get-the-activation-key).

Aby utworzyć zasób bramy pola danych, należy wykonać następujące kroki w witrynie Azure portal.

1. Użyj poświadczeń platformy Microsoft Azure, aby zalogować się do:

    - Portal Azure pod tym [https://portal.azure.com](https://portal.azure.com)adresem URL: .
    - Lub portal Azure Government pod [https://portal.azure.us](https://portal.azure.us)tym adresem URL: . Aby uzyskać więcej informacji, przejdź do [połącz się z platformą Azure Dla instytucji rządowych przy użyciu portalu](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal).

2. W lewym okienku wybierz pozycję **+ Utwórz zasób**. Wyszukaj **krawędź pola danych / bramę pola danych**. Wybierz pozycję Krawędź pola danych / brama pola danych. Wybierz **pozycję Utwórz**.
3. Wybierz subskrypcję, której chcesz użyć dla urządzenia Bramy pola danych. Wybierz region, w którym chcesz wdrożyć zasób bramy pola danych. Aby uzyskać listę wszystkich regionów, w których dostępny jest zasób usługi Azure Stack Edge, zobacz [Produkty platformy Azure dostępne według regionów.](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all) Wybierz lokalizację najbliżej regionu geograficznego, w którym chcesz wdrożyć urządzenie. W opcji **Brama pola danych** wybierz pozycję **Utwórz**.

    ![Wyszukiwanie usługi Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-edge-sku.png)

4. Na karcie **Podstawy** wprowadź lub wybierz następujące **szczegóły projektu**.
    
    |Ustawienie  |Wartość  |
    |---------|---------|
    |Subskrypcja    |Jest to wypełniane automatycznie na podstawie wcześniejszego wyboru. Subskrypcja jest połączona z kontem rozliczeniowym. |
    |Grupa zasobów  |Wybierz istniejącą grupę lub utwórz nową.<br>Dowiedz się więcej o [grupach zasobów platformy Azure](../azure-resource-manager/management/overview.md).     |

5. Wprowadź lub wybierz następujące **szczegóły wystąpienia**.

    |Ustawienie  |Wartość  |
    |---------|---------|
    |Nazwa   | Przyjazna nazwa identyfikująca zasób.<br>Nazwa może zawierać od 2 do 50 znaków, w tym litery, cyfry i łączniki.<br> Nazwa rozpoczyna się i kończy literą lub cyfrą.        |   
    |Region     |Aby uzyskać listę wszystkich regionów, w których dostępny jest zasób usługi Azure Stack Edge, zobacz [Produkty platformy Azure dostępne według regionów.](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all) Dla platformy Azure Government dostępne są wszystkie regiony instytucji rządowych wymienione w [regionach platformy Azure.](https://azure.microsoft.com/global-infrastructure/regions/) <br> Wybierz lokalizację najbliżej regionu geograficznego, w którym chcesz wdrożyć urządzenie.|
    
    ![Tworzenie zasobu usługi Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource.png)
    
6. Wybierz pozycję **Przegląd + utwórz**.
 
7. Na karcie **Recenzja + tworzenie** przejrzyj szczegóły **cen**, **Warunki użytkowania**i szczegóły zasobu. Wybierz **pozycję Utwórz**.

    ![Przeglądanie szczegółów zasobu bramy pola danych](media/data-box-gateway-deploy-prep/data-box-gateway-resource1.png)

Tworzenie zasobu trwa kilka minut. Po pomyślnym utworzeniu i wdrożeniu zasobu zostanie o tym powiadomiony. Wybierz pozycję **Przejdź do zasobu**.

![Przeglądanie szczegółów zasobu bramy pola danych](media/data-box-gateway-deploy-prep/data-box-gateway-resource2.png)

## <a name="download-the-virtual-device-image"></a>Pobieranie obrazu urządzenia wirtualnego

Po utworzeniu zasobu usługi Data Box Gateway pobierz odpowiedni obraz urządzenia wirtualnego do aprowizowania urządzenia wirtualnego w systemie hosta. Obrazy urządzeń wirtualnych są specyficzne dla systemu operacyjnego.

> [!IMPORTANT]
> Oprogramowania uruchomionego w usłudze Data Box Gateway można używać tylko z zasobem usługi Data Box Gateway.

Wykonaj następujące kroki w [witrynie Azure portal,](https://portal.azure.com/) aby pobrać obraz urządzenia wirtualnego.

1. W utworzonym zasobie, a następnie wybierz pozycję **Przegląd**. Jeśli masz istniejący zasób usługi Azure Data Box Gateway, wybierz zasób i przejdź do **przeglądu**. Wybierz **pozycję Konfiguracja urządzenia**.

    ![Nowy zasób usługi Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource-created.png)

2. Na kafelku **Pobierz obraz** wybierz obraz urządzenia wirtualnego odpowiadający systemowi operacyjnemu na serwerze hosta używanym do aprowizowania maszyny Wirtualnej. Pliki obrazów mają około 5,6 GB.
   
   * [Dysk VHDX dla funkcji Hyper-V w systemie Windows Server 2012 R2 i nowszych](https://aka.ms/dbe-vhdx-2012).
   * [Dysk VMDK dla oprogramowania VMWare ESXi 6.0, 6.5 lub 6.7](https://aka.ms/dbe-vmdk).

    ![Pobierz obraz urządzenia wirtualnego bramy danych](media/data-box-gateway-deploy-prep/data-box-gateway-download-image.png)

5. Pobierz i rozpakuj plik na lokalnym dysku. Zanotuj lokalizację rozpakowanego pliku.


## <a name="get-the-activation-key"></a>Uzyskiwanie klucza aktywacji

Po uruchomieniu zasobu bramy pola danych musisz uzyskać klucz aktywacyjny. Ten klucz jest używany do aktywowania urządzenia usługi Data Box Gateway i nawiązaniu połączenia z zasobem. Ten klucz można uzyskać już teraz za pośrednictwem witryny Azure Portal.

1. Wybierz utworzony zasób, a następnie wybierz pozycję **Przegląd**. W **oknie Konfiguracja urządzenia**przejdź do kafelka **Konfiguruj i aktywuj.**

    ![Konfigurowanie i aktywowanie kafelka](media/data-box-gateway-deploy-prep/data-box-gateway-configure-activate.png)

2. Wybierz pozycję **Generuj klucz**, aby utworzyć klucz aktywacji. Wybierz ikonę kopiowania, aby skopiować klucz i zapisać go do użytku w przyszłości.

    ![Pobieranie klucza aktywacji](media/data-box-gateway-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - Klucz aktywacji wygasa po trzech dniach od jego wygenerowania.
> - Jeśli klucz wygaśnie, wygeneruj nowy klucz. Starszy klucz nie jest prawidłowy.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono zagadnienia dotyczące usługi Data Box Gateway, takie jak:

> [!div class="checklist"]
> * Tworzenie nowego zasobu
> * Pobieranie obrazu urządzenia wirtualnego
> * Uzyskiwanie klucza aktywacji

Przejdź do następnego samouczka, aby dowiedzieć się, jak aprowizować maszynę wirtualną na potrzeby usługi Data Box Gateway. W zależności od systemu operacyjnego hosta szczegółowe instrukcje znajdują się w temacie:

> [!div class="nextstepaction"]
> [Aprowizowanie usługi Data Box Gateway w funkcji Hyper-V](./data-box-gateway-deploy-provision-hyperv.md)

LUB

> [!div class="nextstepaction"]
> [Aprowizowanie usługi Data Box Gateway w oprogramowaniu VMware](./data-box-gateway-deploy-provision-vmware.md)


