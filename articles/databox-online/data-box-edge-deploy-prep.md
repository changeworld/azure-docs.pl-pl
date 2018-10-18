---
title: Samouczek dotyczący przygotowywania witryny Azure Portal do wdrożenia usługi Data Box Edge | Microsoft Docs
description: Pierwszy samouczek dotyczący wdrażania usługi Azure Data Box Edge obejmuje przygotowywanie witryny Azure Portal.
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
ms.date: 10/08/2018
ms.author: alkohli
ms.custom: ''
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 79061caac3d598df79f383b9b13458ab9537cd81
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2018
ms.locfileid: "48833067"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-edge-preview"></a>Samouczek: przygotowywanie do wdrożenia usługi Azure Data Box Edge (wersja zapoznawcza)


To jest pierwszy samouczek z serii samouczków dotyczących wdrażania opisujących pełne wdrożenie usługi Azure Data Box Edge. W tym samouczku opisano sposób przygotowania witryny Azure Portal do wdrożenia zasobu usługi Data Box Edge. 

Do ukończenia procesu instalacji i konfiguracji niezbędne są uprawnienia administratora. Przygotowanie portalu zajmuje mniej niż 10 minut.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie nowego zasobu
> * Uzyskiwanie klucza aktywacji

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).


> [!IMPORTANT]
> Usługa Data Box Edge jest dostępna w wersji zapoznawczej. Przed zamówieniem i wdrożeniem tego rozwiązania zapoznaj się z [warunkami świadczenia usług Azure w wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

### <a name="get-started"></a>Rozpoczęcie pracy

Proces wdrażania usługi Data Box Edge opisano w następujących samouczkach w zalecanej kolejności.

| **#** | **W tym kroku** | **Skorzystaj z tych dokumentów** |
| --- | --- | --- | 
| 1. |**[Przygotowywanie witryny Azure Portal na potrzeby usługi Data Box Edge](data-box-edge-deploy-prep.md)** |Utwórz i skonfiguruj zasób usługi Data Box Edge, zanim zainstalujesz urządzenie fizyczne Data Box Edge. |
| 2. |**[Instalowanie urządzenia Data Box Edge](data-box-edge-deploy-install.md)**|Rozpakuj, umieść na stojaku i podłącz kable urządzenia fizycznego Data Box Edge.  |
| 3. |**[Łączenie, konfigurowanie i aktywowanie urządzenia Data Box Edge](data-box-edge-deploy-connect-setup-activate.md)** |Nawiąż połączenie z lokalnym internetowym interfejsem użytkownika, przeprowadź konfigurację urządzenia i je aktywuj. Urządzenie jest gotowe do skonfigurowania udziałów SMB lub NFS.  |
| 4. |**[Przesyłanie danych za pomocą usługi Data Box Edge](data-box-edge-deploy-add-shares.md)** |Dodaj udziały i nawiąż z nimi połączenie za pomocą protokołu SMB lub NFS. |
| 5. |**[Przekształcanie danych za pomocą usługi Data Box Edge](data-box-edge-deploy-configure-compute.md)** |Skonfiguruj moduły usługi Edge na urządzeniu w celu przekształcania danych podczas ich przenoszenia na platformę Azure. |

Teraz możesz rozpocząć konfigurowanie witryny Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

W tym miejscu opisano wymagania wstępne dotyczące konfiguracji zasobu usługi Data Box Edge, urządzenia Data Box Edge i sieci centrum danych.

### <a name="for-the-data-box-edge-resource"></a>Zasób usługi Data Box Edge

Przed rozpoczęciem upewnij się, że:

* Twoja subskrypcja platformy Microsoft Azure powinna być dostępna dla zasobu usługi Data Box Edge.
* Masz konto magazynu platformy Microsoft Azure z poświadczeniami dostępu.

### <a name="for-the-data-box-edge-device"></a>Urządzenie Data Box Edge

Przed wdrożeniem urządzenia fizycznego upewnij się, że są spełnione następujące warunki:

- Na standardowym stojaku 19” w centrum danych masz dostępne gniazdo 1U w celu zamontowania urządzenia. 
- Masz dostęp do płaskiej, stabilnej i poziomej powierzchni roboczej, gdzie można bezpiecznie umieścić urządzenie.
- Miejsce, w którym chcesz skonfigurować urządzenie, ma standardowe zasilanie prądem przemiennym z niezależnego źródła lub jednostkę dystrybucji zasilania na stojaku (PDU, rack power distribution unit) z zasilaczem UPS.
- Masz dostęp do urządzenia fizycznego.


### <a name="for-the-datacenter-network"></a>Sieć centrum danych

Przed rozpoczęciem upewnij się, że:

* Sieć w centrum danych jest skonfigurowana zgodnie z wymaganiami sieciowymi dla urządzenia Data Box Edge. Aby uzyskać więcej informacji, przejrzyj [wymagania systemowe usługi Data Box Edge](data-box-gateway-system-requirements.md).

* Twoja usługa Data Box Edge dysponuje przez cały czas dedykowanym połączeniem internetowym o przepustowości co najmniej 20 Mb/s. Ta przepustowość nie powinna być współużytkowana z żadną inną aplikacją. Jeśli chcesz korzystać z ograniczania przepustowości sieci, zaleca się użycie połączenia internetowego o przepustowości co najmniej 32 Mb/s.

## <a name="create-a-new-resource"></a>Tworzenie nowego zasobu

Wykonaj poniższe kroki, aby utworzyć nowy zasób usługi Data Box Edge. 

Jeśli masz istniejący zasób usługi Data Box Edge, którego możesz użyć do zarządzania urządzeniem fizycznym, pomiń ten krok i przejdź do sekcji [Uzyskiwanie klucza aktywacji](#get-the-activation-key).

Wykonaj poniższe czynności w witrynie Azure Portal, aby utworzyć zasób usługi Data Box.

1. Użyj swoich poświadczeń platformy Microsoft Azure, aby zalogować się do witryny Azure Portal w wersji zapoznawczej pod tym adresem URL: [https://aka.ms/databox-edge](https://aka.ms/databox-edge). 

2. Wybierz subskrypcję, w której chcesz używać rozwiązania Data Box Edge w wersji zapoznawczej. Wybierz region, w którym chcesz wdrożyć zasób rozwiązania Data Box Edge. W opcji rozwiązania **Data Box Edge** kliknij pozycję **Utwórz**.

    ![Wyszukiwanie usługi Data Box Edge](media/data-box-edge-deploy-prep/data-box-edge-sku.png)

3. Dla nowego zasobu wprowadź lub wybierz następujące informacje.
    
    |Ustawienie  |Wartość  |
    |---------|---------|
    |Nazwa zasobu   | Przyjazna nazwa identyfikująca zasób.<br>Nazwa zasobu może zawierać od 2 do 50 znaków, w tym litery, cyfry i łączniki.<br> Nazwa rozpoczyna się i kończy literą lub cyfrą.        |
    |Subskrypcja    |Subskrypcja jest połączona z kontem rozliczeniowym. |
    |Grupa zasobów  |Wybierz istniejącą grupę lub utwórz nową.<br>Dowiedz się więcej o [grupach zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md).     |
    |Lokalizacja     |W tej wersji są dostępne następujące regiony: Wschodnie stany USA, Zachodnie stany USA 2, Azja Południowo-Wschodnia i Europa Zachodnia. <br> Wybierz lokalizację najbliżej regionu geograficznego, w którym chcesz wdrożyć urządzenie.|
    
    ![Utwórz zasób usługi Data Box Edge](media/data-box-edge-deploy-prep/data-box-edge-resource.png)
    
4. Kliknij przycisk **OK**.
 
Tworzenie zasobu trwa kilka minut. Po pomyślnym utworzeniu zasobu zostanie wyświetlone odpowiednie powiadomienie.


## <a name="get-the-activation-key"></a>Uzyskiwanie klucza aktywacji

Po skonfigurowaniu i uruchomieniu zasobu usługi Data Box Edge konieczne będzie uzyskanie klucza aktywacji. Ten klucz jest używany do aktywowania urządzenia Data Box Edge i nawiązania połączenia z zasobem. Ten klucz można uzyskać już teraz za pośrednictwem witryny Azure Portal.

1. Kliknij utworzony zasób, a następnie kliknij pozycję **Przegląd**.

2. Kliknij przycisk **Generuj klucz**, aby utworzyć klucz aktywacji. Kliknij ikonę kopiowania, aby skopiować klucz i zapisać go do użytku w przyszłości.

    ![Pobieranie klucza aktywacji](media/data-box-edge-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - Klucz aktywacji wygasa po 3 dniach od jego wygenerowania. 
> - Jeśli klucz wygaśnie, wygeneruj nowy klucz. Starszy klucz nie jest prawidłowy.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono zagadnienia dotyczące usługi Data Box Edge, takie jak:

> [!div class="checklist"]
> * Tworzenie nowego zasobu
> * Uzyskiwanie klucza aktywacji

Przejdź do następnego samouczka, aby dowiedzieć się, jak zainstalować urządzenie Data Box Edge. 

> [!div class="nextstepaction"]
> [Instalowanie urządzenia Data Box Edge](./data-box-edge-deploy-install.md)



