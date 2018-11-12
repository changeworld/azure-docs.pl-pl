---
title: Zestaw narzędzi Marketplace umożliwia tworzenie i publikowanie elementów portalu marketplace | Dokumentacja firmy Microsoft
description: Dowiedz się, jak szybko utworzyć elementów portalu marketplace przy użyciu publikowania Toolkit
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 4758c29d1cdb7e09c397524574ba1ab6701efeeb
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51238031"
---
#  <a name="add-marketplace-items-using-publishing-tool"></a>Dodawanie elementów portalu marketplace za pomocą narzędzia do publikowania

Dodawanie zawartości do [usługi Azure Stack w portalu Marketplace](azure-stack-marketplace.md) udostępnia rozwiązania dla Ciebie i dzierżawców do wdrożenia. Zestaw narzędzi Marketplace tworzy pliki pakietu (.azpkg) portalu Azure Marketplace, na podstawie usługi IaaS usługi Azure Resource Manager lub rozszerzeń maszyn wirtualnych. Umożliwia także zestaw narzędzi witryny Marketplace do opublikowania .azpkg plików, można utworzyć za pomocą narzędzia lub [ręczne](azure-stack-create-and-publish-marketplace-item.md) kroki. W tym temacie opisano pobrać narzędzie, tworzenie elementu portalu marketplace, na podstawie szablonu maszyny Wirtualnej i publikowania w portalu Azure Marketplace stosu tego elementu.     

## <a name="prerequisites"></a>Wymagania wstępne

 - Musisz uruchomić narzędzi na hoście usługi Azure Stack lub mieć [VPN](.\asdk\asdk-connect.md#connect-with-vpn) połączenie z hostem ASDK z komputera, na którym uruchomiono narzędzie.

 - Pobierz [szablony szybkiego startu platformy Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/archive/master.zip) i Wyodrębnij zawartość.

 - Pobierz [narzędzia pakietu galerii Azure](https://aka.ms/azurestackmarketplaceitem) (AzureGalleryPackage.exe). 

 - Publikowanie w portalu Marketplace wymaga, ikon i pliku miniatury. Możesz użyć własnego lub zapisać [przykładowe](azure-stack-marketplace-publisher.md#support-files) pliki lokalnie na potrzeby tego przykładu.

## <a name="download-the-tool"></a>Pobierz narzędzia

Możesz pobrać z witryny Marketplace zestawu narzędzi [z repozytorium narzędzia usługi Azure Stack](azure-stack-powershell-download.md).

##  <a name="create-marketplace-items"></a>Tworzenie elementów portalu marketplace

W tej sekcji używasz toolkit witryny Marketplace do utworzenia pakietu elementu portalu marketplace w formacie .azpkg.  

### <a name="provide-marketplace-information-with-wizard"></a>Podaj informacje portalu marketplace przy użyciu Kreatora

1. Uruchom zestaw narzędzi portalu Marketplace w sesji programu PowerShell:
   ```PowerShell
   .\MarketplaceToolkit.ps1
   ```

2. Kliknij przycisk **rozwiązania** kartę. Ten ekran akceptuje informacji na temat usługi elementu portalu marketplace. Wprowadź informacje na temat usługi elementu, która będzie wyświetlana w portalu marketplace. Można również określić [pliku parametrów](azure-stack-marketplace-publisher.md#use-a-parameters-file) Aby wstępnie wypełnić formularz.  
    
    ![Zrzut ekranu przedstawiający ekran pierwszy zestaw narzędzi do witryny Marketplace](./media/azure-stack-marketplace-publisher/image7.png)
3. Kliknij przycisk **Przeglądaj** i wybierz plik obrazu dla każdego pola, ikona i tworzenia zrzutów ekranu. Możesz użyć własnego ikony lub ikony próbki w [pliki obsługi](azure-stack-marketplace-publisher.md#support-files) sekcji.
4. Gdy wszystkie pola zostaną wypełnione, wybierz "(wersja zapoznawcza) rozwiązanie" w wersji zapoznawczej w rozwiązania w portalu Marketplace. Można skorygować i edytować tekst, obrazy i zrzut ekranu przed kliknięciem przycisku **dalej**.  

### <a name="import-template-and-create-package"></a>Zaimportuj szablon i Utwórz pakiet

W tej sekcji, zaimportuj szablon i pracować z danymi wejściowymi dla Twojego rozwiązania.

1.  Kliknij przycisk **Przeglądaj** i wybierz *azuredeploy.json* z folderu 101-Simple-Windows-VM w szablonach pobrany.

    ![Zrzut ekranu przedstawiający ekran drugi zestaw narzędzi do witryny Marketplace](./media/azure-stack-marketplace-publisher/image8.png)
2.  Kreator wdrażania jest wypełniana przy użyciu *podstawowe* elementy kroku i dane wejściowe dla wszystkich parametrów określonych w szablonie. Można dodać kolejne kroki i przenieść dane wejściowe między krokami. Na przykład możesz "Frontonu konfiguracji" i "Back-End" kroki rozwiązania.
3.  Określ ścieżkę do AzureGalleryPackager.exe.  
4.  Kliknij pozycję **Utwórz**. Zestaw narzędzi Marketplace pakietów rozwiązania do pliku .azpkg. Po zakończeniu kreatora jest wyświetlana ścieżka do pliku rozwiązania i zapewniają możliwość kontynuować publikowanie pakietu do usługi Azure Stack.

## <a name="publish-marketplace-items"></a>Publikowanie elementów portalu marketplace

W tej sekcji możesz opublikować elementu portalu marketplace w portalu Marketplace usługi Azure Stack.

![Zrzut ekranu przedstawiający ekran pierwszy zestaw narzędzi do witryny Marketplace](./media/azure-stack-marketplace-publisher/image9.png)

1.  Kreator wymagane informacje, aby publikować rozwiązanie usługi:
    
    |Pole|Opis|
    |-----|-----|
    | Nazwa administratora usługi | Konto administratora usługi.  Przykład: ServiceAdmin@mydomain.onmicrosoft.com |
    | Hasło | Hasło dla konta administratora usługi. |
    | Punkt końcowy interfejsu API | Punkt końcowy usługi Azure Stack usługi Azure Resource Manager. Przykład: management.local.azurestack.external |
2.  Kliknij przycisk **Publikuj** i jest wyświetlany Dziennik publikowania.
3.  Jesteś teraz mogli wdrażać swoje opublikowanego elementu za pośrednictwem portalu usługi Azure Stack.

## <a name="use-a-parameters-file"></a>Przy użyciu pliku parametrów

Plik parametrów umożliwia również informacje elementu portalu marketplace.  

Obejmuje zestaw narzędzi Marketplace *solution.parameters.ps1* można użyć do utworzenia własnego pliku parametrów.

## <a name="support-files"></a>Pliki pomocy technicznej

| Opis | Sample |
| ----- | ----- |
| ikona .png 40 x 40 | ![](./media/azure-stack-marketplace-publisher/image1.png) |
| 90x90 .png icon | ![](./media/azure-stack-marketplace-publisher/image2.png) |
| ikona .png 115 x 115 | ![](./media/azure-stack-marketplace-publisher/image3.png) |
| ikona .png 255 x 115 | ![](./media/azure-stack-marketplace-publisher/image4.png) |
| Miniatura .png 533 x 324 | ![](./media/azure-stack-marketplace-publisher/image5.png) |

## <a name="next-steps"></a>Kolejne kroki

[Pobieranie elementów portalu Marketplace](azure-stack-download-azure-marketplace-item.md)  
[Tworzenie i publikowanie elementu portalu Marketplace](azure-stack-create-and-publish-marketplace-item.md)