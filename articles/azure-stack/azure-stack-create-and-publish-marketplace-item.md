---
title: Tworzenie i publikowanie elementu portalu Marketplace w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Tworzenie i publikowanie elementu portalu Marketplace w usłudze Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2019
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 01/08/2019
ms.openlocfilehash: 44cf5b2cc7547a4e85c65215fdc1e4fe2cb585a9
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2019
ms.locfileid: "55243644"
---
# <a name="create-and-publish-a-marketplace-item"></a>Tworzenie i publikowanie elementu portalu Marketplace

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

## <a name="create-a-marketplace-item"></a>Tworzenie elementu portalu Marketplace

1. Pobierz [narzędzie Pakowarka galerii Azure](https://www.aka.ms/azurestackmarketplaceitem) i próbka Azure Stack w portalu Marketplace.
2. Otwórz przykładowy elementu portalu Marketplace i Zmień nazwę **SimpleVMTemplate** folderu. Użyj takiej samej nazwie jak Twoje elementu portalu Marketplace; na przykład **Contoso.TodoList**. Ten folder zawiera:

   ```shell
   /Contoso.TodoList/
   /Contoso.TodoList/Manifest.json
   /Contoso.TodoList/UIDefinition.json
   /Contoso.TodoList/Icons/
   /Contoso.TodoList/Strings/
   /Contoso.TodoList/DeploymentTemplates/
   ```

3. [Tworzenie szablonu usługi Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) lub wybierz szablon z serwisu GitHub. Elementu portalu Marketplace używa tego szablonu, aby utworzyć zasób.

    > [!Note]  
    > Nigdy nie ciężko kodu wszystkie wpisy tajne, takie jak klucze produktów, hasła lub żadnych informacji do zidentyfikowania klientów w szablonie usługi Azure Resource Manager. Szablon JSON pliki są dostępne bez konieczności uwierzytelniania po opublikowaniu w galerii. Store wszystkich wpisów tajnych w [usługi Key Vault](../azure-resource-manager/resource-manager-keyvault-parameter.md) i wywoływać je z w ramach szablonu.

4. Aby upewnić się, czy zasób można pomyślnie wdrożyć, przetestować szablon przy użyciu interfejsów API usługi Microsoft Azure Stack.
5. Jeśli Twój szablon opiera się na obrazie maszyny wirtualnej, postępuj zgodnie z instrukcjami, aby [Dodaj obraz maszyny wirtualnej do usługi Azure Stack](azure-stack-add-vm-image.md).
6. Zapisz szablon usługi Azure Resource Manager w **/Contoso.TodoList/DeploymentTemplates/** folderu.
7. Wybierz, ikon i tekstu dla elementu portalu Marketplace. Dodawanie ikon do **ikony** folderze i Dodaj tekst do **zasobów** w pliku **ciągi** folderu. Użyj **małych**, **średni**, **dużych**, i **szeroki** konwencje nazewnictwa dla ikony. Zobacz [elementu portalu Marketplace informacje o interfejsie użytkownika](#reference-marketplace-item-ui) szczegółowy opis tych rozmiarów.

   > [!NOTE]
   > Wszystkie rozmiary cztery ikony (szeroki mały, Średni, duży) są wymagane dla poprawnie kompilowane elementu portalu Marketplace.
   >
   >
8. W **Manifest.json** pliku, zmień **nazwa** na nazwę elementu portalu Marketplace. Również zmienić **wydawcy** do nazwy lub firmy.
9. W obszarze **artefaktów**, zmienić **nazwa** i **ścieżki** do poprawnych informacji dla szablonu usługi Azure Resource Manager, które zostały dodane:

   ```json
   "artifacts": [
      {
          "name": "Your template name",
          "type": "Template",
          "path": "DeploymentTemplates\\your path",
          "isDefault": true
      }
   ```

10. Zastąp **Moje elementy portalu Marketplace** listę kategorii, w którym powinna zostać wyświetlona Twoja elementu portalu Marketplace:

   ```json
   "categories":[
   "My Marketplace Items"
   ],
   ```

11. Wszelkie dalsze zmiany do pliku Manifest.json się [odwołania: Manifest.json elementu portalu Marketplace](#reference-marketplace-item-manifestjson).

12. Aby spakować foldery do pliku .azpkg, otwórz wiersz polecenia i uruchom następujące polecenie:

   ```shell
   AzureGalleryPackager.exe package –m <path to manifest.json> -o <output location for the package>
   ```

    > [!NOTE]
    > Pełna ścieżka do pakietu danych wyjściowych musi istnieć. Na przykład jeśli ścieżka wyjściowa ma C:\MarketPlaceItem\yourpackage.azpkg, musi istnieć folder C:\MarketPlaceItem.
    >
    >

## <a name="publish-a-marketplace-item"></a>Publikowanie elementu portalu Marketplace

1. Użyj programu PowerShell lub Eksploratora usługi Azure Storage przekazywać swoje elementu portalu Marketplace (.azpkg) do usługi Azure Blob storage. Możesz przekazać do lokalnego magazynu usługi Azure Stack lub przekazać do usługi Azure Storage; czyli tymczasowej lokalizacji pakietu. Upewnij się, że obiekt blob jest dostępny publicznie.
2. Na maszynie wirtualnej klienta w środowisku Microsoft Azure Stack upewnij się, że sesja programu PowerShell jest skonfigurowany przy użyciu poświadczeń administratora usługi. Instrukcje można znaleźć na temat uwierzytelniania programu PowerShell w usłudze Azure Stack w [wdrażanie szablonu przy użyciu programu PowerShell](user/azure-stack-deploy-template-powershell.md).
3. Kiedy używasz [PowerShell 1.3.0](azure-stack-powershell-install.md) lub nowszej, możesz użyć **AzsGalleryItem Dodaj** polecenia cmdlet programu PowerShell publikowanie elementu portalu Marketplace do usługi Azure Stack. Przed użyciem środowiska PowerShell 1.3.0, należy użyć polecenia cmdlet **AzureRMGalleryitem Dodaj** zamiast **AzsGalleryItem Dodaj**. Na przykład, kiedy używać programu PowerShell 1.3.0 lub nowszej:

   ```powershell
   Add-AzsGalleryItem -GalleryItemUri `
   https://sample.blob.core.windows.net/gallerypackages/Microsoft.SimpleTemplate.1.0.0.azpkg –Verbose
   ```

   | Parametr | Opis |
   | --- | --- |
   | Identyfikator subskrypcji |Identyfikator administratora subskrypcji. Możesz pobrać go przy użyciu programu PowerShell. Jeśli chcesz użyć je pobrać w portalu, przejdź do subskrypcji dostawcy i skopiuj identyfikator subskrypcji. |
   | GalleryItemUri |Identyfikator URI obiektu blob dla pakietu galerii, który został już przekazany do magazynu. |
   | Wersja interfejsu API |Ustaw jako **2015-04-01**. |

4. Przejdź do portalu. Teraz widać elementu portalu Marketplace w portalu, jako operator lub użytkownika. Pakiet może potrwać kilka minut.

5. Teraz został zapisany z elementu portalu Marketplace w portalu Azure Marketplace stosu. Można go usunąć z lokalizacji magazynu obiektów Blob.
    > [!Caution]  
    > Wszystkie artefakty galerii domyślne i artefaktów Galeria niestandardowa są teraz dostępne bez uwierzytelniania w ramach następujących adresów URL:  
`https://adminportal.[Region].[external FQDN]:30015/artifact/20161101/[Template Name]/DeploymentTemplates/Template.json`
`https://portal.[Region].[external FQDN]:30015/artifact/20161101/[Template Name]/DeploymentTemplates/Template.json`
`https://systemgallery.blob.[Region].[external FQDN]/dev20161101-microsoft-windowsazure-gallery/[Template Name]/UiDefinition.json`

6. Można usunąć elementu portalu Marketplace przy użyciu **AzureRMGalleryItem Usuń** polecenia cmdlet. Na przykład:

   ```powershell
   Remove-AzsGalleryItem -Name Microsoft.SimpleTemplate.1.0.0  –Verbose
   ```

   > [!NOTE]
   > Interfejs użytkownika portalu Marketplace, mogą być wyświetlane błąd, po usunięciu elementu. Aby naprawić błąd, kliknij przycisk **ustawienia** w portalu. Następnie wybierz **Odrzuć modyfikacje** w obszarze **dostosowywania portalu**.
   >
   >

## <a name="reference-marketplace-item-manifestjson"></a>Informacje ogólne: Manifest.json elementu portalu Marketplace

### <a name="identity-information"></a>Informacje o tożsamości

| Name | Wymagany | Type | Ograniczenia | Opis |
| --- | --- | --- | --- | --- |
| Name |X |Ciąg |[A-Za-z0-9]+ | |
| Wydawca |X |Ciąg |[A-Za-z0-9]+ | |
| Wersja |X |Ciąg |[SemVer v2](https://semver.org/) | |

### <a name="metadata"></a>Metadane

| Name | Wymagany | Type | Ograniczenia | Opis |
| --- | --- | --- | --- | --- |
| Nazwa wyświetlana |X |Ciąg |Zalecenie wynoszącą 80 znaków |Portalu mogą być wyświetlane nazwy elementu bez problemu zmieniała, jeśli jest więcej niż 80 znaków. |
| PublisherDisplayName |X |Ciąg |Zalecenie 30 znaków |Portalu mogą być wyświetlane swoją nazwę wydawcy zostanie wyłączone poprawnie, jeśli jest więcej niż 30 znaków. |
| PublisherLegalName |X |Ciąg |Maksymalnie 256 znaków | |
| Podsumowanie |X |Ciąg |60 do 100 znaków | |
| LongSummary |X |Ciąg |140 do 256 znaków |Jeszcze nieobsługiwane w usłudze Azure Stack. |
| Opis |X |[HTML](https://auxdocs.azurewebsites.net/en-us/documentation/articles/gallery-metadata#html-sanitization) |500 do 5000 znaków | |

### <a name="images"></a>Obrazy

Portal Marketplace korzysta z następujących ikon:

| Name | Szerokość | Wysokość | Uwagi |
| --- | --- | --- | --- |
| Szerokie |255 px |115 px |Zawsze wymagane |
| Large |115 px |115 px |Zawsze wymagane |
| Medium |90 px |90 px |Zawsze wymagane |
| Small |40 piks. |40 piks. |Zawsze wymagane |
| Zrzut ekranu |533 px |32 px |Optional (Opcjonalność) |

### <a name="categories"></a>Kategorie

Każdy element portalu Marketplace powinny być oznakowane za pomocą kategorii, która określa, gdzie pojawia się on w portalu, interfejsu użytkownika. Można wybrać jedną z istniejących kategorii w usłudze Azure Stack (obliczeniowe, dane i Magazyn, itp.) lub wybrać nowe hasło.

### <a name="links"></a>Linki

Każdy element portalu Marketplace, mogą obejmować różne linki do dodatkowej zawartości. Linki są określone jako listę wartości nazwy i identyfikatory URI:

| Name | Wymagany | Type | Ograniczenia | Opis |
| --- | --- | --- | --- | --- |
| Nazwa wyświetlana |X |Ciąg |Co najwyżej 64 znaków. | |
| Identyfikator URI |X |URI | | |

### <a name="additional-properties"></a>Dodatkowe właściwości

Oprócz poprzedniego metadane autorzy Marketplace można podać niestandardowy klucz/wartość pary danych w następującej postaci:

| Name | Wymagany | Type | Ograniczenia | Opis |
| --- | --- | --- | --- | --- |
| Nazwa wyświetlana |X |Ciąg |Maksymalnie 25 znaków. | |
| Value |X |Ciąg |Maksymalnie 30 znaków | |

### <a name="html-sanitization"></a>Narzędzie oczyszczania HTML

Dla dowolnego pola, która umożliwia HTML dozwolone są następujące elementy i atrybuty:

`h1, h2, h3, h4, h5, p, ol, ul, li, a[target|href], br, strong, em, b, i`

## <a name="reference-marketplace-item-ui"></a>Informacje ogólne: Elementu portalu Marketplace interfejsu użytkownika

Ikon i tekstu dla elementów portalu Marketplace w portalu usługi Azure Stack to.

### <a name="create-blade"></a>Blok tworzenia

![Blok tworzenia](media/azure-stack-marketplace-item-ui-reference/image1.png)

### <a name="marketplace-item-details-blade"></a>Blok szczegółów elementu portalu Marketplace

![Blok szczegółów elementu portalu Marketplace](media/azure-stack-marketplace-item-ui-reference/image3.png)
