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
ms.openlocfilehash: 428b42e62f44d182de109740359544135561e54b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67441492"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-gateway"></a>Samouczek: Przygotowanie do wdrożenia bramy pola danych platformy Azure


To jest pierwszy samouczek z serii samouczków dotyczących wdrażania opisujących pełne wdrożenie usługi Azure Data Box Gateway. W tym samouczku opisano sposób przygotowania witryny Azure Portal do wdrażania zasobu usługi Data Box Gateway. 

Do ukończenia procesu instalacji i konfiguracji niezbędne są uprawnienia administratora. Przygotowanie portalu zajmuje mniej niż 10 minut.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie nowego zasobu
> * Pobieranie obrazu urządzenia wirtualnego
> * Uzyskiwanie klucza aktywacji

## <a name="get-started"></a>Rozpoczęcie pracy

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

- Twoja subskrypcja Microsoft Azure powinna być obsługiwana dla zasobu bramy pola danych. Płatność za rzeczywiste użycie subskrypcje są również obsługiwane.
- Masz właściciela lub współautora dostęp na poziomie grupy zasobów dla zasobów bramy okno usługi Edge i dane pole danych, usługi IoT Hub i Azure Storage.

    - Aby utworzyć brzeg urządzenia Data Box / zasobu bramy pola danych, powinni mieć uprawnienia współautora (lub wyższy) zakresu na poziomie grupy zasobów. Należy również upewnić, że `Microsoft.DataBoxEdge` dostawca jest zarejestrowany. Aby uzyskać informacje dotyczące rejestrowania, przejdź do [procedura Rejestruj dostawcę zasobów](data-box-gateway-manage-access-power-connectivity-mode.md#register-resource-providers).
    - Aby utworzyć zasób konta magazynu, ponownie potrzebny współautora lub nowszej dostępu zakresu na poziomie grupy zasobów. Usługa Azure Storage jest domyślnie zarejestrowanego dostawcy zasobów.
- Masz administratora lub użytkownika, dostęp do interfejsu API usługi Azure Active Directory Graph. Aby uzyskać więcej informacji, zobacz [interfejsu API usługi Azure Active Directory Graph](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).
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

- Sieć w centrum danych jest skonfigurowana zgodnie z wymaganiami sieciowymi dla urządzenia usługi Data Box Gateway. Aby uzyskać więcej informacji, zobacz [wymagania systemowe bramy pola danych](data-box-gateway-system-requirements.md).

- Normalnych warunkach pracy Twojej bramy Data Gateway pole powinny mieć Odp.:

    - Co najmniej 10 MB/s Pobierz przepustowość tak, aby upewnić się, że urządzenie jest w pełni zaktualizowany.
    - Co najmniej 20 MB/s dedykowanych przekazywanie i pobieranie przepustowości do transferu plików.

## <a name="create-a-new-resource"></a>Tworzenie nowego zasobu

Jeśli masz istniejący zasób usługi Data Box Gateway, którego możesz użyć do zarządzania urządzeniami wirtualnymi, pomiń ten krok i przejdź do sekcji [Uzyskiwanie klucza aktywacji](#get-the-activation-key).

Do utworzenia zasobu bramy pola danych, wykonaj następujące kroki w witrynie Azure portal.

1. Użyj swoich poświadczeń Microsoft Azure, aby zalogować się do:

    - Witryny Azure portal pod tym adresem URL: [ https://portal.azure.com ](https://portal.azure.com).
    - Lub, w portalu Azure dla instytucji rządowych pod tym adresem URL: [ https://portal.azure.us ](https://portal.azure.us). Aby uzyskać więcej informacji, przejdź do [nawiązywanie połączenia z platformy Azure Government przy użyciu portalu](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal).

2. W okienku po lewej stronie wybierz **+ Utwórz zasób**. Wyszukaj **Data Box Edge / Data Box bramy**. Wybierz krawędź pola Data / Data Box bramy. Wybierz pozycję **Utwórz**.
3. Wybierz subskrypcję, dla której chcesz użyć dla urządzenia bramy pola danych. Wybierz region, w której chcesz wdrożyć zasobu bramy pola danych. W tej wersji są dostępne wschodnie stany USA, Azja południowo-wschodnia i Europa Zachodnia. Wybierz lokalizację najbliżej regionu geograficznego, w którym chcesz wdrożyć urządzenie. W **bramy pola danych** wybierz **Utwórz**.

    ![Wyszukiwanie usługi Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-edge-sku.png)

4. Na **podstawy** karcie wpisz lub wybierz następujące pozycje **projektu szczegóły**.
    
    |Ustawienie  |Wartość  |
    |---------|---------|
    |Subskrypcja    |To jest automatycznie wypełniane na podstawie wybranych wcześniej. Subskrypcja jest połączona z kontem rozliczeniowym. |
    |Grupa zasobów  |Wybierz istniejącą grupę lub utwórz nową.<br>Dowiedz się więcej o [grupach zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md).     |

5. Wprowadź lub wybierz następujące pozycje **wystąpienia szczegóły**.

    |Ustawienie  |Wartość  |
    |---------|---------|
    |Name (Nazwa)   | Przyjazna nazwa identyfikująca zasób.<br>Nazwa może zawierać od 2 do 50 znaków, w tym litery, cyfry i łączniki.<br> Nazwa rozpoczyna się i kończy literą lub cyfrą.        |   
    |Region     |W tej wersji są dostępne do wdrożenia usługi resource wschodnie stany USA, Azja południowo-wschodnia i Europa Zachodnia. Dla platformy Azure Government, wszystkie regiony dla instytucji rządowych na liście [regionów świadczenia usługi Azure](https://azure.microsoft.com/global-infrastructure/regions/) są dostępne. <br> Wybierz lokalizację najbliżej regionu geograficznego, w którym chcesz wdrożyć urządzenie.|
    
    ![Tworzenie zasobu usługi Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource.png)
    
6. Wybierz pozycję **Przegląd + utwórz**.
 
7. Na **Przejrzyj + Utwórz** kartę, przejrzyj **szczegóły cennika**, **warunki użytkowania**oraz szczegóły zasobu bazy danych. Wybierz pozycję **Utwórz**.

    ![Przejrzyj szczegóły zasobu bramy pola danych](media/data-box-gateway-deploy-prep/data-box-gateway-resource1.png)

Tworzenie zasobu trwa kilka minut. Po pomyślnym utworzeniu i wdrożeniu zasobu, otrzymasz powiadomienie. Wybierz pozycję **Przejdź do zasobu**.

![Przejrzyj szczegóły zasobu bramy pola danych](media/data-box-gateway-deploy-prep/data-box-gateway-resource2.png)

## <a name="download-the-virtual-device-image"></a>Pobieranie obrazu urządzenia wirtualnego

Po utworzeniu zasobu usługi Data Box Gateway pobierz odpowiedni obraz urządzenia wirtualnego do aprowizowania urządzenia wirtualnego w systemie hosta. Obrazy urządzenia wirtualnego są specyficzne dla systemu operacyjnego.

> [!IMPORTANT]
> Oprogramowania uruchomionego w usłudze Data Box Gateway można używać tylko z zasobem usługi Data Box Gateway.

Wykonaj poniższe kroki w [witryny Azure portal](https://portal.azure.com/) do pobierania obrazu urządzenia wirtualnego.

1. W zasobie, który został utworzony i następnie wybierz pozycję **Przegląd**. Jeśli masz istniejący zasób bramy pola danych platformy Azure, wybierz zasób i przejdź do **Przegląd**. Wybierz **konfiguracji urządzenia**.

    ![Nowy zasób usługi Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource-created.png)

2. Na **pobierania obrazu** kafelka, wybierz obraz urządzenia wirtualnego, odpowiadający systemowi operacyjnemu na serwerze hosta używany do aprowizowania maszyny Wirtualnej. Pliki obrazów są około 5.6 GB.
   
   * [Dysk VHDX dla funkcji Hyper-V w systemie Windows Server 2012 R2 i nowszych](https://aka.ms/dbe-vhdx-2012).
   * [Dysk VMDK dla oprogramowania VMWare ESXi 6.0, 6.5 lub 6.7](https://aka.ms/dbe-vmdk).

    ![Pobranie obrazu urządzenia wirtualnego bramy pola danych](media/data-box-gateway-deploy-prep/data-box-gateway-download-image.png)

5. Pobierz i rozpakuj plik na lokalnym dysku. Zanotuj lokalizację rozpakowanego pliku.


## <a name="get-the-activation-key"></a>Uzyskiwanie klucza aktywacji

Po skonfigurowaniu i uruchomieniu zasobu bramy pola danych należy uzyskać klucz aktywacji. Ten klucz jest używany do aktywowania urządzenia usługi Data Box Gateway i nawiązaniu połączenia z zasobem. Ten klucz można uzyskać już teraz za pośrednictwem witryny Azure Portal.

1. Wybierz utworzony zasób, a następnie wybierz pozycję **Przegląd**. W **konfiguracji urządzenia**, przejdź do **Konfiguruj i Aktywuj** kafelka.

    ![Konfigurowanie i Aktywuj kafelka](media/data-box-gateway-deploy-prep/data-box-gateway-configure-activate.png)

2. Wybierz pozycję **Generuj klucz**, aby utworzyć klucz aktywacji. Wybierz ikonę kopiowania, aby skopiować klucz i zapisać go do użytku w przyszłości.

    ![Pobieranie klucza aktywacji](media/data-box-gateway-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - Klucz aktywacji wygasa po trzech dniach od jego wygenerowania.
> - Jeśli klucz wygaśnie, wygeneruj nowy klucz. Starszy klucz nie jest prawidłowy.

## <a name="next-steps"></a>Kolejne kroki

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


