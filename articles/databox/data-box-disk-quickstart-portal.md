---
title: 'Szybki start: usługa Microsoft Azure Data Box Disk | Microsoft Docs'
description: Skorzystaj z tego przewodnika Szybki start, aby przeprowadzić szybkie wdrożenie usługi Azure Data Box Disk w witrynie Azure Portal
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/12/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to quickly deploy Data Box Disk so as to import data into Azure.
ms.openlocfilehash: 20dc414c5cdd309434ba53acf2d7f6716d3edfe5
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39009930"
---
# <a name="quickstart-deploy-azure-data-box-disk-using-the-azure-portal-preview"></a>Szybki start: wdrażanie usługi Azure Data Box Disk (w wersji zapoznawczej) za pomocą witryny Azure Portal

W tym przewodniku Szybki start opisano sposób wdrażania usługi Azure Data Box Disk przy użyciu witryny Azure Portal. Opisano czynności szybkiego tworzenia zamówienia, odbioru dysków, ich rozpakowywania i podłączania oraz kopiowania danych na dyski w celu przekazania na platformę Azure. 

Aby zyskać szczegółowe instrukcje wdrażania i śledzenia krok po kroku, zobacz [Tutorial: Order Azure Data Box Disk (Samouczek: zamawianie urządzenia Azure Data Box Disk)](data-box-disk-deploy-ordered.md). 

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/en-us/free/?WT.mc_id=A261C142F).

> [!IMPORTANT]
> Usługa Data Box Disk jest dostępna w wersji zapoznawczej. Przed wdrożeniem tego rozwiązania zapoznaj się z [warunkami świadczenia usług Azure w wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem:

- Upewnij się, że w ramach Twojej subskrypcji włączono usługę Azure Data Box. Aby włączyć tę usługę w subskrypcji, [zarejestruj się](http://aka.ms/azuredataboxfromdiskdocs).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [http://aka.ms/azuredataboxfromdiskdocs](http://aka.ms/azuredataboxfromdiskdocs).

## <a name="order"></a>Zamówienie

Ten krok zajmuje około 5 minut.

1. Utwórz nowy zasób usługi Azure Data Box w witrynie Azure Portal. 
2. Wybierz subskrypcję, w której włączono tę usługę, a następnie wybierz typ transferu **Import**. Podaj **Kraj źródłowy**, w którym są przechowywane dane, oraz **Docelowy region platformy Azure**, do którego mają zostać przekazane dane.
3. Wybierz pozycję **Data Box Disk**. Maksymalna pojemność tego rozwiązania to 35 TB. W przypadku większych ilości danych możesz utworzyć większą liczbę zamówień.  
4. Wprowadź szczegóły zamówienia i informacje dotyczące wysyłki. Jeśli ta usługa jest dostępna w Twoim regionie, podaj adresy e-mail na potrzeby powiadomień, zapoznaj się z podsumowaniem i utwórz zamówienie. 

Po utworzeniu zamówienia dyski zostaną przygotowane do wysłania. 


## <a name="unpack"></a>Rozpakowywanie

Ten krok zajmuje około 5 minut.

Urządzenia Data Box Disk są wysyłane w opakowaniu UPS Express Box. Otwórz opakowanie i sprawdź, czy zawiera:

- od 1 do 5 dysków USB zapakowanych w folię bąbelkową,
- jeden kabel połączeniowy dla każdego dysku, 
- etykietę wysyłkową na potrzeby wysyłki zwrotnej.
 

## <a name="connect-and-unlock"></a>Podłączanie i odblokowywanie

Ten krok zajmuje około 5 minut.

1. Podłącz dysk do komputera z obsługiwaną wersją systemu Windows za pomocą dołączonego kabla. Aby uzyskać więcej informacji na temat obsługiwanych wersji systemu operacyjnego, zapoznaj się z [wymaganiami systemowymi rozwiązania Azure Data Box Disk](data-box-disk-system-requirements.md). 
2. Aby odblokować dysk:

    1. W witrynie Azure Portal przejdź do pozycji **Ogólne > Szczegóły urządzenia** i uzyskaj klucz dostępu.
    2. Pobierz i wyodrębnij narzędzie do odblokowywania dysków Data Box na komputerze, którego używasz do skopiowania danych na dyski. 
    3. Uruchom plik *DataBoxDiskUnlock.exe* i podaj klucz dostępu. Powtórz tę czynność po podłączeniu każdego kolejnego dysku.
    4. W narzędziu jest wyświetlana litera dysku przypisana do danego dysku. Zanotuj tę literę dysku. Będzie używana w kolejnych krokach.



## <a name="copy-data-and-verify"></a>Kopiowanie danych i weryfikacja

Czas wymagany do ukończenia tej operacji zależy od rozmiaru danych. 

1. Dysk zawiera foldery *PageBlob*, *BlockBlob* oraz *AzureImportExport*. Przeciągnij i upuść dane, które mają zostać zaimportowane jako blokowe obiekty blob, do folderu *BlockBlob*. Analogicznie przeciągnij i upuść dane w formatach takich jak VHD/VHDX do folderu *PageBlob*.

    Dla każdego podfolderu w folderach *BlockBlob* i *PageBlob* zostanie utworzony kontener na koncie usługi Azure Storage. Wszystkie pliki w folderach *BlockBlob* i *PageBlob* zostaną skopiowane do domyślnego kontenera `$root` na koncie usługi Azure Storage.

    > [!NOTE] 
    > - Wszystkie kontenery i obiekty blob powinny mieć nazwy zgodne z [konwencją nazewnictwa platformy Azure](data-box-disk-limits.md#azure-block-blob-and-page-blob-naming-conventions). W przypadku niezgodności z tymi regułami przekazywanie danych na platformę Azure zakończy się niepowodzeniem.
    > - Rozmiar plików nie może przekraczać ~4.7 TiB w przypadku blokowych obiektów blob, a ~8 TiB w przypadku stronicowych obiektów blob.

2. (Opcjonalnie) Po zakończeniu kopiowania zalecamy uruchomienie narzędzia `AzureExpressDiskService.ps1` znajdującego się w folderze *AzureImportExport* w celu wygenerowania sum kontrolnych na potrzeby sprawdzania poprawności. Czas wykonywania tej operacji zależy od rozmiaru danych. 
3. Odłącz dysk. 


## <a name="ship-to-azure"></a>Wysyłka do platformy Azure

Ten krok zajmuje około 5–7 minut.

1. Umieść wszystkie dyski w oryginalnym opakowaniu. Użyj dołączonej etykiety wysyłkowej. W przypadku uszkodzenia lub utraty etykiety pobierz etykietę z portalu. W sekcji **Przegląd** kliknij pozycję **Pobierz etykietę wysyłkową** na pasku poleceń.
2. Oddaj zapieczętowaną paczkę firmie przewozowej.  

Usługa Data Box Disk wysyła powiadomienie e-mail i aktualizuje stan zamówienia w witrynie Azure Portal.


## <a name="verify-your-data"></a>Weryfikowanie danych

Czas wymagany do ukończenia tej operacji zależy od rozmiaru danych.

1. Gdy urządzenie Data Box Disk zostanie podłączone do sieci w centrum danych platformy Azure, automatycznie rozpocznie się przekazywanie danych na platformę Azure. 
2. Usługa Azure Data Box powiadomi Cię o zakończeniu kopiowania danych w witrynie Azure Portal. 
    
    1. Sprawdź dzienniki błędów pod kątem ewentualnych niepowodzeń i podejmij odpowiednie działania.
    2. Sprawdź, czy dane znajdują się na kontach magazynu, zanim usuniesz je ze źródła.


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Ten krok zajmuje około 2–3 minut.

Aby wyczyścić zasoby, możesz anulować zamówienie urządzenia Data Box i usunąć je.

- Możesz anulować zamówienie urządzenia Data Box w witrynie Azure Portal przed jego przetworzeniem. Po przetworzeniu zamówienia nie można go anulować. Realizacja zamówienia będzie kontynuowana aż do etapu ukończenia. 

    Aby anulować zamówienie, w sekcji **Przegląd** kliknij pozycję **Anuluj** na pasku poleceń.  

- Możesz usunąć zamówienie, gdy jego stan w witrynie Azure Portal to **Ukończono** lub **Anulowano**. 

    Aby usunąć zamówienie, w sekcji **Przegląd** kliknij pozycję **Usuń** na pasku poleceń.

## <a name="next-step"></a>Następny krok

Podczas pracy z tym przewodnikiem Szybki start wdrożono usługę Azure Data Box Disk w celu zaimportowania danych na platformę Azure. Aby dowiedzieć się więcej na temat zarządzania usługą Azure Data Box Disk, przejdź do kolejnego samouczka: 

> [!div class="nextstepaction"]
> [Use the Azure portal to administer Data Box Disk (Administrowanie usługą Data Box Disk w witrynie Azure Portal)](data-box-portal-ui-admin.md)


