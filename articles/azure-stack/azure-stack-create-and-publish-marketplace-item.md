---
title: Tworzenie i publikowanie elementów Marketplace w stosie Azure | Dokumentacja firmy Microsoft
description: Tworzenie i publikowanie elementów Marketplace w stosie Azure.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 5e0349d6bae9295e7a0ba9f366f84753ebd838c2
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2018
---
# <a name="create-and-publish-a-marketplace-item"></a>Tworzenie i publikowanie elementu portalu Marketplace

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

## <a name="create-a-marketplace-item"></a>Tworzenie elementu portalu Marketplace
1. [Pobierz](http://www.aka.ms/azurestackmarketplaceitem) element Azure Marketplace stosu próbki i narzędzie Pakowarka galerii Azure.
2. Otwórz element Marketplace przykładowych i Zmień nazwę **SimpleVMTemplate** folderu. (Na przykład użyć takiej samej nazwie jako przedmiot Marketplace — **Contoso.TodoList**.) Ten folder zawiera:
   
       /Contoso.TodoList/
       /Contoso.TodoList/Manifest.json
       /Contoso.TodoList/UIDefinition.json
       /Contoso.TodoList/Icons/
       /Contoso.TodoList/Strings/
       /Contoso.TodoList/DeploymentTemplates/
3. [Tworzenie szablonu usługi Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) lub wybierz szablon z usługi GitHub. Element Marketplace używa tego szablonu do utworzenia zasobu.
4. Aby pomyślnie wdrożyć zasobu, należy przetestować szablonu z interfejsów API usługi Microsoft Azure stosu.
5. Jeśli szablon opiera się na obrazie maszyny wirtualnej, postępuj zgodnie z instrukcjami, aby [Dodaj obraz maszyny wirtualnej Azure stos](azure-stack-add-vm-image.md).
6. Zapisz szablon usługi Azure Resource Manager w **/Contoso.TodoList/DeploymentTemplates/** folderu.
7. Wybierz ikon i tekst dla elementu portalu Marketplace. Dodawanie ikon do **ikony** folderu i Dodaj tekst do **zasobów** w pliku **ciągów** folderu. Użyj małych, średnich, duża i całej konwencji nazewnictwa dla ikon. Zobacz [elementów Marketplace informacje o interfejsie użytkownika](#reference-marketplace-item-ui) szczegółowy opis.
   
   > [!NOTE]
   > Wszystkie cztery ikona rozmiary (mały, Średni, duża, WWW) są wymagane dla poprawnie budowanie elementu portalu Marketplace.
   > 
   > 
8. W **manifest.json** Zmień **nazwa** nazwę tego elementu portalu Marketplace. Również zmienić **wydawcy** pod nazwą lub w firmie.
9. W obszarze **artefakty**, zmień **nazwa** i **ścieżki** do szablonu usługi Azure Resource Manager, która zostanie uwzględniona poprawne informacje.
   
         "artifacts": [
            {
                "name": "Type your template name",
                "type": "Template",
                "path": "DeploymentTemplates\\Type your path",
                "isDefault": true
            }
10. Zastąp **Moje elementy Marketplace** z listy Kategorie miejsce tego elementu portalu Marketplace.
    
             "categories":[
                 "My Marketplace Items"
              ],
11. Wszelkie dodatkowe zmiany do manifest.json, można znaleźć w temacie [odwołania: manifest.json elementów Marketplace](#reference-marketplace-item-manifestjson).
12. Aby pakiet foldery do pliku .azpkg, otwórz wiersz polecenia i uruchom następujące polecenie:
    
        AzureGalleryPackager.exe package –m <path to manifest.json> -o <output location for the package>
    
    > [!NOTE]
    > Pełna ścieżka do pakietu danych wyjściowych musi istnieć. Na przykład jeśli ścieżka wyjściowa ma C:\MarketPlaceItem\yourpackage.azpkg, musi istnieć folder C:\MarketPlaceItem.
    > 
    > 

## <a name="publish-a-marketplace-item"></a>Publikowanie elementu portalu Marketplace
1. Użyj programu PowerShell lub Eksploratora usługi Storage platformy Azure, aby przekazać przedmiot witryny Marketplace (.azpkg) do magazynu obiektów Blob Azure. Można przekazać do lokalnego magazynu Azure stosu lub Przekaż do magazynu Azure. (Jest tymczasowej lokalizacji pakietu). Upewnij się, że obiekt blob jest dostępny publicznie.
2. Na maszynie wirtualnej klienta w środowisku Microsoft Azure stosu upewnij się, że sesja programu PowerShell jest skonfigurowany przy użyciu poświadczeń administratora usługi. Można znaleźć instrukcje dotyczące sposobu uwierzytelniania programu PowerShell w programie Azure stosu w [wdrożyć szablon przy użyciu programu PowerShell](user/azure-stack-deploy-template-powershell.md).
3. Użyj **AzureRMGalleryItem Dodaj** polecenia cmdlet programu PowerShell do publikowania stosu Azure w portalu Marketplace. Na przykład:
   
       Add-AzureRMGalleryItem -GalleryItemUri `
       https://sample.blob.core.windows.net/gallerypackages/Microsoft.SimpleTemplate.1.0.0.azpkg –Verbose
   
   | Parametr | Opis |
   | --- | --- |
   | Identyfikator subskrypcji |Identyfikator administratora subskrypcji. Można go pobrać przy użyciu programu PowerShell. Jeśli chcesz pobrać go w portalu, przejdź do dostawcy subskrypcji i skopiuj identyfikator subskrypcji. |
   | GalleryItemUri |Identyfikator URI obiektu blob dla pakietu galerii, który został już przekazany do magazynu. |
   | Apiversion |Ustaw jako **2015-04-01**. |
4. Przejdź do portalu. Element Marketplace w portalu — będą teraz widoczne jako operator lub użytkownika.
   
   > [!NOTE]
   > Pakiet może potrwać kilka minut.
   > 
   > 
5. Przedmiot Marketplace teraz został zapisany w portalu Azure Marketplace stosu. Można go usunąć z lokalizacji magazynu obiektów Blob.
6. Należy usunąć element Marketplace przy użyciu **AzureRMGalleryItem Usuń** polecenia cmdlet. Przykład:
   
        Remove-AzureRMGalleryItem -Name Microsoft.SimpleTemplate.1.0.0  –Verbose
   
   > [!NOTE]
   > Po usunięciu elementu interfejsu użytkownika portalu Marketplace mogą być widoczne wystąpił błąd. Aby naprawić błąd, kliknij przycisk **ustawienia** w portalu. Następnie wybierz opcję **odrzucić modyfikacje** w obszarze **dostosowywania portalu**.
   > 
   > 

## <a name="reference-marketplace-item-manifestjson"></a>Odwołanie: Manifest.json elementów Marketplace
### <a name="identity-information"></a>Informacje o tożsamości
| Name (Nazwa) | Wymagane | Typ | Ograniczenia | Opis |
| --- | --- | --- | --- | --- |
| Name (Nazwa) |X |Ciąg |[A-Za-z0-9]+ | |
| Wydawca |X |Ciąg |[A-Za-z0-9]+ | |
| Wersja |X |Ciąg |[Programu SemVer v2](http://semver.org/) | |

### <a name="metadata"></a>Metadane
| Name (Nazwa) | Wymagane | Typ | Ograniczenia | Opis |
| --- | --- | --- | --- | --- |
| Nazwa wyświetlana |X |Ciąg |Zalecenie 80 znaków |Portalu może nie wyświetlać nazwę elementu bezpiecznie, jeśli jest więcej niż 80 znaków. |
| PublisherDisplayName |X |Ciąg |Zalecenie 30 znaków |Portalu może nie wyświetlać nazwę wydawcy bezpiecznie, jeśli jest więcej niż 30 znaków. |
| PublisherLegalName |X |Ciąg |Maksymalnie 256 znaków | |
| Podsumowanie |X |Ciąg |60 do 100 znaków | |
| LongSummary |X |Ciąg |140 do 256 znaków |Nie jeszcze dotyczy w stosie Azure. |
| Opis |X |[HTML](https://auxdocs.azurewebsites.net/en-us/documentation/articles/gallery-metadata#html-sanitization) |znaki 500 do 5000 | |

### <a name="images"></a>Obrazy
Witryny Marketplace korzysta z następujących ikon:

| Name (Nazwa) | Szerokość | Wysokość | Uwagi |
| --- | --- | --- | --- |
| Międzynarodowe |255 pikseli |115 pikseli |Zawsze wymagany |
| Large |115 pikseli |115 pikseli |Zawsze wymagany |
| Medium |90 pikseli |90 pikseli |Zawsze wymagany |
| Small |40 pikseli |40 pikseli |Zawsze wymagany |
| Zrzut ekranu |533 pikseli |32 pikseli |Optional (Opcjonalność) |

### <a name="categories"></a>Kategorie
Każdy element Marketplace powinny być oznaczane kategorię, która identyfikuje, gdy element jest wyświetlany w portalu interfejsu użytkownika. Można wybrać jedną z istniejących kategorii w stosie Azure (obliczeniowych, dane i Magazyn, itp.) lub wybrać nowe hasło.

### <a name="links"></a>Linki
Każdy element Marketplace mogą zawierać różne linki do dodatkowej zawartości. Łącza są określone jako lista nazw i identyfikatorów URI.

| Name (Nazwa) | Wymagane | Typ | Ograniczenia | Opis |
| --- | --- | --- | --- | --- |
| Nazwa wyświetlana |X |Ciąg |Maksymalnie 64 znaków. | |
| Identyfikator URI |X |Identyfikator URI | | |

### <a name="additional-properties"></a>Dodatkowe właściwości
Oprócz powyższych metadanych autorzy Marketplace można podać danych pary klucza i wartości niestandardowe w następującym formacie:

| Name (Nazwa) | Wymagane | Typ | Ograniczenia | Opis |
| --- | --- | --- | --- | --- |
| Nazwa wyświetlana |X |Ciąg |Maksymalnie 25 znaków | |
| Wartość |X |Ciąg |Maksymalnie 30 znaków | |

### <a name="html-sanitization"></a>Ich oczyszczania HTML
Dla dowolnego pola, który umożliwia HTML dopuszczalne są następujące elementy i atrybuty:

H1, h2, h3, h4, h5, p, ol, ul, li, [docelowy | href], br silne, em, b i

## <a name="reference-marketplace-item-ui"></a>Odwołanie: Marketplace elementu interfejsu użytkownika
Ikony i tekst dla elementów portalu Marketplace, jak pokazano w portalu Azure stosu są.

### <a name="create-blade"></a>Blok tworzenia
![Blok tworzenia](media/azure-stack-marketplace-item-ui-reference/image1.png)

### <a name="marketplace-item-details-blade"></a>Blok szczegółów elementu portalu Marketplace
![Blok szczegółów elementu portalu Marketplace](media/azure-stack-marketplace-item-ui-reference/image3.png)

