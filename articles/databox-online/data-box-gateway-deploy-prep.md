---
title: Samouczek dotyczący przygotowywania witryny Azure Portal do wdrożenia usługi Data Box Gateway | Microsoft Docs
description: Pierwszy samouczek dotyczący wdrażania usługi Azure Data Box Gateway obejmuje przygotowywanie witryny Azure Portal.
services: databox-edge-gateway
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox-edge-gateway
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/24/2018
ms.author: alkohli
ms.custom: ''
ms.openlocfilehash: f4c3cf5329c10cda3691370e946b8397662a8d66
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46953416"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-gateway-preview"></a>Samouczek: przygotowywanie do wdrażania usługi Azure Data Box Gateway (wersja zapoznawcza)


To jest pierwszy samouczek z serii samouczków dotyczących wdrażania opisujących pełne wdrożenie usługi Azure Data Box Gateway. W tym samouczku opisano sposób przygotowania witryny Azure Portal do wdrażania zasobu usługi Data Box Gateway. 

Do ukończenia procesu instalacji i konfiguracji niezbędne są uprawnienia administratora. Przygotowanie portalu zajmuje mniej niż 10 minut.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie nowego zasobu
> * Pobieranie obrazu urządzenia wirtualnego
> * Uzyskiwanie klucza aktywacji

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).


> [!IMPORTANT]
> - Usługa Data Box Gateway jest dostępna w wersji zapoznawczej. Przed zamówieniem i wdrożeniem tego rozwiązania zapoznaj się z [warunkami świadczenia usług Azure w wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

### <a name="get-started"></a>Rozpoczęcie pracy

Proces wdrażania usługi Data Box Gateway opisano w następujących samouczkach w zalecanej kolejności.

| **#** | **W tym kroku** | **Skorzystaj z tych dokumentów** |
| --- | --- | --- | 
| 1. |**[Przygotowywanie witryny Azure Portal na potrzeby usługi Data Box Gateway](data-box-gateway-deploy-prep.md)** |Utwórz i skonfiguruj zasób usługi Data Box Gateway przed aprowizowaniem urządzenia wirtualnego usługi Data Box Gateway. |
| 2. |**[Aprowizowanie usługi Data Box Gateway w funkcji Hyper-V](data-box-gateway-deploy-provision-hyperv.md)** <br><br><br>**[Aprowizowanie usługi Data Box Gateway w oprogramowaniu VMware](data-box-gateway-deploy-provision-vmware.md)**|W przypadku funkcji Hyper-V przeprowadź aprowizację i nawiąż połączenie z urządzeniem wirtualnym usługi Data Box Gateway w systemie hosta funkcji Hyper-V z systemem Windows Server 2016 lub Windows Server 2012 R2. <br><br><br> W przypadku oprogramowania VMware przeprowadź aprowizację i nawiąż połączenie z urządzeniem wirtualnym usługi Data Box Gateway w systemie hosta z oprogramowaniem VMware ESXi 6.0 lub 6.5.<br></br> |
| 3. |**[Łączenie, konfigurowanie i aktywowanie usługi Data Box Gateway](data-box-gateway-deploy-connect-setup-activate.md)** |Nawiąż połączenie z lokalnym internetowym interfejsem użytkownika, przeprowadź konfigurację urządzenia i je aktywuj. Następnie można aprowizować udziały SMB.  |
| 4. |**[Przesyłanie danych za pomocą usługi Data Box Gateway](data-box-gateway-deploy-add-shares.md)** |Dodaj udziały i nawiąż z nimi połączenie za pomocą protokołu SMB lub NFS. |

Teraz możesz rozpocząć konfigurowanie witryny Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

W tym miejscu opisano wymagania wstępne dotyczące konfiguracji zasobu usługi Data Box Gateway, urządzenia usługi Data Box Gateway i sieci centrum danych.

### <a name="for-the-data-box-gateway-resource"></a>Zasób usługi Data Box Gateway

Przed rozpoczęciem upewnij się, że:

* Twoja subskrypcja platformy Microsoft Azure powinna być dostępna dla zasobu usługi Data Box Gateway.
* Masz konto magazynu platformy Microsoft Azure z poświadczeniami dostępu.

### <a name="for-the-data-box-gateway-device"></a>Urządzenie usługi Data Box Gateway

Przed wdrożeniem urządzenia wirtualnego upewnij się, że są spełnione następujące warunki:

* Masz dostęp do hosta funkcji Hyper-V z systemem Windows Server 2012 R2 lub nowszym bądź z oprogramowaniem VMware (ESXi 6.0 lub 6.5), którego można użyć do aprowizacji urządzenia.
* System hosta może przeznaczyć następujące zasoby wyłącznie na potrzeby aprowizacji urządzenia wirtualnego usługi Data Box:
  
  * Co najmniej 4 rdzenie.
  * Co najmniej 8 GB pamięci RAM. 
  * Jeden interfejs sieciowy.
  * Dysk systemu operacyjnego o rozmiarze 250 GB.
  * Dysk wirtualny o rozmiarze 2 TB do przechowywania danych systemu.

### <a name="for-the-datacenter-network"></a>Sieć centrum danych

Przed rozpoczęciem upewnij się, że:

* Sieć w centrum danych jest skonfigurowana zgodnie z wymaganiami sieciowymi dla urządzenia usługi Data Box Gateway. Aby uzyskać więcej informacji, przejrzyj [wymagania systemowe usługi Data Box Gateway](data-box-gateway-system-requirements.md).

* Twoja usługa Data Box Gateway dysponuje przez cały czas dedykowanym połączeniem internetowym o przepustowości co najmniej 20 Mb/s. Ta przepustowość nie powinna być współużytkowana z żadną inną aplikacją. Jeśli chcesz korzystać z ograniczania przepustowości sieci, zaleca się użycie połączenia internetowego o przepustowości co najmniej 32 Mb/s.

## <a name="create-a-new-resource"></a>Tworzenie nowego zasobu

Pojedyncze wystąpienie zasobu usługi Data Box Gateway może zarządzać wieloma urządzeniami wirtualnymi usługi Data Box Gateway. Wykonaj poniższe kroki, aby utworzyć nowy zasób usługi Data Box Gateway. 

Jeśli masz istniejący zasób usługi Data Box Gateway, którego możesz użyć do zarządzania urządzeniami wirtualnymi, pomiń ten krok i przejdź do sekcji [Uzyskiwanie klucza aktywacji](#get-the-activation-key).

Wykonaj poniższe czynności w witrynie Azure Portal, aby utworzyć zasób usługi Data Box.
1. Użyj swoich poświadczeń platformy Microsoft Azure, aby zalogować się do witryny Azure Portal w wersji zapoznawczej pod tym adresem URL: [https://aka.ms/databox-edge](https://aka.ms/databox-edge). 

2. Wybierz subskrypcję, w której chcesz używać rozwiązania Data Box Edge w wersji zapoznawczej. Wybierz region, w którym chcesz wdrożyć zasób rozwiązania Data Box Edge. W opcji **Data Box Gateway** kliknij przycisk **Utwórz**.

    ![Wyszukiwanie usługi Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-edge-sku.png)

3. Dla nowego zasobu wprowadź lub wybierz następujące informacje.
    
    |Ustawienie  |Wartość  |
    |---------|---------|
    |Nazwa zasobu   | Przyjazna nazwa identyfikująca zasób.<br>Nazwa może zawierać od 2 do 50 znaków, w tym litery, cyfry i łączniki.<br> Nazwa rozpoczyna się i kończy literą lub cyfrą.        |
    |Subskrypcja    |Subskrypcja jest połączona z kontem rozliczeniowym. |
    |Grupa zasobów  |Wybierz istniejącą grupę lub utwórz nową.<br>Dowiedz się więcej o [grupach zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md).     |
    |Lokalizacja     |W tej wersji są dostępne następujące regiony: Wschodnie stany USA, Zachodnie stany USA 2, Azja Południowo-Wschodnia i Europa Zachodnia. <br> Wybierz lokalizację najbliżej regionu geograficznego, w którym chcesz wdrożyć urządzenie.|
    
    ![Tworzenie zasobu usługi Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource.png)
    
4. Kliknij przycisk **OK**.
 
Tworzenie zasobu trwa kilka minut. Po pomyślnym utworzeniu zasobu zostanie wyświetlone odpowiednie powiadomienie.


## <a name="download-the-virtual-device-image"></a>Pobieranie obrazu urządzenia wirtualnego

Po utworzeniu zasobu usługi Data Box Gateway pobierz odpowiedni obraz urządzenia wirtualnego do aprowizowania urządzenia wirtualnego w systemie hosta. Obrazy urządzeń wirtualnych są specyficzne dla systemu operacyjnego. Można je pobrać z poziomu bloku **Szybki start** zasobu w witrynie Azure Portal.

> [!IMPORTANT]
> Oprogramowania uruchomionego w usłudze Data Box Gateway można używać tylko z zasobem usługi Data Box Gateway.


Wykonaj poniższe czynności w witrynie [Azure Portal](https://portal.azure.com/).

1. Kliknij utworzony zasób, a następnie kliknij pozycję **Przegląd**. Jeśli masz istniejący zasób usługi Azure Data Box Gateway, kliknij go i przejdź do pozycji **Przegląd**.

    ![Nowy zasób usługi Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource-created.png)

4. W przewodniku Szybki start w okienku po prawej stronie kliknij link odpowiadający obrazowi, który chcesz pobrać. Pliki obrazów mają rozmiar około 4,8 GB.
   
   * [Dysk VHDX dla funkcji Hyper-V w systemie Windows Server 2012 R2 i nowszych](https://aka.ms/dbe-vhdx-2012).
   * [Dysk VMDK dla oprogramowania VMWare ESXi 6.0 lub 6.5](https://aka.ms/dbe-vmdk).

5. Pobierz i rozpakuj plik na lokalnym dysku. Zanotuj lokalizację rozpakowanego pliku.


## <a name="get-the-activation-key"></a>Uzyskiwanie klucza aktywacji

Po skonfigurowaniu i uruchomieniu zasobu usługi Data Box Gateway konieczne będzie uzyskanie klucza aktywacji. Ten klucz jest używany do aktywowania urządzenia usługi Data Box Gateway i nawiązaniu połączenia z zasobem.

Klucz aktywacji jest używany do rejestrowania wszystkich urządzeń usługi Data Box Gateway, które muszą być aktywowane za pomocą zasobu usługi Data Box Gateway. Ten klucz można uzyskać już teraz za pośrednictwem witryny Azure Portal.

1. Kliknij utworzony zasób, a następnie kliknij pozycję **Przegląd**.

    ![Nowy zasób usługi Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource-created.png)

2. Kliknij przycisk **Generuj klucz**, aby utworzyć klucz aktywacji. Kliknij ikonę kopiowania, aby skopiować klucz i zapisać go do użytku w przyszłości.

    ![Pobieranie klucza aktywacji](media/data-box-gateway-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - Klucz aktywacji wygasa po 3 dniach od jego wygenerowania. 
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


