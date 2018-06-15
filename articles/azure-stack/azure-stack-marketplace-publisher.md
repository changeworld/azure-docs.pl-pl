---
title: Użyj narzędzi Marketplace można tworzyć i publikować elementów marketplace | Dokumentacja firmy Microsoft
description: Dowiedz się, jak szybko utworzyć elementów marketplace z publikowaniem Toolkit
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/14/2017
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 61ee3296429f9641643f1c9268ae89e3691fcfa1
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2018
ms.locfileid: "29386881"
---
#  <a name="add-marketplace-items-using-publishing-tool"></a>Dodawanie elementów marketplace za pomocą narzędzia do publikowania
Dodawanie zawartości do [Azure Marketplace stosu](azure-stack-marketplace.md) udostępnia rozwiązań Tobie i dzierżawcy dla wdrożenia.  Zestaw narzędzi Marketplace tworzy pliki Azure Marketplace pakietów (.azpkg) oparte na szablony Menedżera zasobów Azure IaaS lub rozszerzeń maszyny Wirtualnej.  Umożliwia także Marketplace Toolkit do publikowania plików .azpkg, utworzone przy użyciu narzędzia albo przy użyciu [ręczne](azure-stack-create-and-publish-marketplace-item.md) czynności.  W tym temacie przedstawiono pobrać narzędzie, tworzenie elementu portalu marketplace, na podstawie szablonu maszyny Wirtualnej i opublikować ten element w portalu Azure Marketplace stosu.     


## <a name="prerequisites"></a>Wymagania wstępne
 - Należy uruchomić zestaw narzędzi na hoście stosu Azure lub mieć [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) łączności z komputera, na którym należy uruchomić narzędzie.

 - Pobierz [szablonów Szybki Start Azure stosu](https://github.com/Azure/AzureStack-QuickStart-Templates/archive/master.zip) i wyodrębnić.

 - Pobierz [narzędzia pakowania galerii Azure](http://aka.ms/azurestackmarketplaceitem) (AzureGalleryPackage.exe). 

 - Publikowanie w portalu Marketplace wymaga ikony i miniatur plików.  Można użyć własnych lub zapisać [próbki](azure-stack-marketplace-publisher.md#support-files) plików lokalnie w tym przykładzie.

## <a name="download-the-tool"></a>Pobierz narzędzie
Zestaw narzędzi Marketplace może być [pobrane z repozytorium Azure stosu narzędzia](azure-stack-powershell-download.md).


##  <a name="create-marketplace-items"></a>Tworzenie elementów marketplace
W tej sekcji umożliwiają Marketplace Toolkit Utwórz pakiet elementów marketplace w formacie .azpkg.  

### <a name="provide-marketplace-information-with-wizard"></a>Podaj informacje marketplace przy użyciu Kreatora
1. Uruchom zestaw narzędzi Marketplace w sesji programu PowerShell:
```PowerShell
    .\MarketplaceToolkit.ps1
```

2. Kliknij przycisk **rozwiązania** kartę.  Ten ekran akceptuje informacji na temat tego elementu portalu marketplace. Wprowadź informacje dotyczące tego elementu ma być wyświetlana w portalu marketplace.  Można również określić [pliku parametrów](azure-stack-marketplace-publisher.md#use-a-parameters-file) Aby wstępnie wypełnić formularza.  
    
    ![Zrzut ekranu przedstawiający pierwszym ekranie Marketplace Toolkit](./media/azure-stack-marketplace-publisher/image7.png)
3. Kliknij przycisk **Przeglądaj** i wybierz plik obrazu dla każdego pola ikonę i tworzenia zrzutów ekranu.  Można użyć własnych ikony lub ikony próbki w [pliki obsługi](azure-stack-marketplace-publisher.md#support-files) sekcji.
4. Gdy wszystkie pola są wypełnione, wybierz "Podgląd rozwiązanie" Podgląd rozwiązań w witrynie Marketplace.  Można skorygować i edytować tekst, obrazy i tworzenia zrzutów ekranu przed kliknięciem przycisku **dalej**.  

### <a name="import-template-and-create-package"></a>Zaimportuj szablon i Utwórz pakiet
W tej sekcji zaimportuj szablon i pracy z danych wejściowych dla rozwiązania.

1.  Kliknij przycisk **Przeglądaj** i wybierz *azuredeploy.json* z folderu 101-prosty-Windows-VM w szablonach pobrany.

    ![Zrzut ekranu przedstawiający ekran drugi Marketplace Toolkit](./media/azure-stack-marketplace-publisher/image8.png)
2.  Kreatora wdrażania jest wypełniana *podstawowe* elementy kroku i dane wejściowe dla każdego parametru określona w szablonie.  Możesz dodać kolejne kroki i przenieść dane wejściowe między krokami.  Na przykład może być "Frontonu konfiguracji" i "wewnętrzne" kroki do rozwiązania.
3.  Określ ścieżkę do AzureGalleryPackager.exe.  
4.  Kliknij przycisk **Utwórz** i zestawu narzędzi Marketplace pakietów rozwiązania do pliku .azpkg.  Po wykonaniu tych czynności kreatora jest wyświetlana ścieżka do pliku rozwiązania i podamy opcję, aby kontynuować publikowanie pakietu Azure stosu.


## <a name="publish-marketplace-items"></a>Publikowanie elementów portalu marketplace
W tej sekcji publikowania portalu Marketplace z Azure Marketplace stosu.

![Zrzut ekranu przedstawiający pierwszym ekranie Marketplace Toolkit](./media/azure-stack-marketplace-publisher/image9.png)

1.  Kreator wymaga informacji do publikowania rozwiązania:
    
    |Pole|Opis|
    |-----|-----|
    | Nazwa administratora usługi | Konto administratora usługi.  Przykład:  ServiceAdmin@mydomain.onmicrosoft.com |
    | Hasło | Hasło dla konta administratora usługi. |
    | Punkt końcowy interfejsu API | Punktu końcowego platformy Azure stosu usługi Azure Resource Manager.  Przykład: management.local.azurestack.external |
2.  Kliknij przycisk **publikowania** i nie jest wyświetlany Dziennik publikowania.
3.  Jesteś teraz możliwość wdrażania tego elementu opublikowane w portalu Azure stosu.


## <a name="use-a-parameters-file"></a>Użycie pliku parametrów
Aby ukończyć informacje o elementach marketplace umożliwia także pliku parametrów.  

Zestaw Marketplace zawiera *solution.parameters.ps1* służy do tworzenia pliku parametrów.


## <a name="support-files"></a>Pliki obsługi
| Opis | Sample |
| ----- | ----- |
| 40x40 .png icon | ![](./media/azure-stack-marketplace-publisher/image1.png) |
| 90x90 .png icon | ![](./media/azure-stack-marketplace-publisher/image2.png) |
| ikona .png 115 x 115 | ![](./media/azure-stack-marketplace-publisher/image3.png) |
| ikona .png 255 x 115 | ![](./media/azure-stack-marketplace-publisher/image4.png) |
| 533 x 324 .png miniatur | ![](./media/azure-stack-marketplace-publisher/image5.png) |


