---
title: Jak przenieść zasób usługi między regionami
titleSuffix: Azure Cognitive Search
description: W tym artykule przedstawiono sposób przenoszenia zasobów Wyszukiwanie poznawcze platformy Azure z jednego regionu do innego w chmurze platformy Azure.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 02/18/2020
ms.openlocfilehash: 392c86d8ea24e59d388926d4df581305ea2b531d
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599303"
---
# <a name="move-your-azure-cognitive-search-service-to-another-azure-region"></a>Przenoszenie usługi Wyszukiwanie poznawcze platformy Azure do innego regionu platformy Azure

Aby przenieść konto usługi poznawczej platformy Azure z jednego regionu do innego, utworzysz szablon eksportu, aby przenieść subskrypcje. Po przeniesieniu subskrypcji musisz przenieść dane i ponownie utworzyć usługę.

W tym artykule dowiesz się, jak:

> [!div class="checklist"]
> * Wyeksportuj szablon.
> * Modyfikowanie szablonu: Dodawanie regionu docelowego, wyszukiwanie i nazwy kont magazynu.
> * Wdróż szablon, aby utworzyć nowe konta wyszukiwania i magazynu.
> * Weryfikowanie stanu usługi w nowym regionie
> * Wyczyść zasoby w regionie źródłowym.

## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że usługi i funkcje używane przez konto są obsługiwane w regionie docelowym.

- W przypadku funkcji w wersji zapoznawczej upewnij się, że subskrypcja jest listy dozwolonych dla regionu docelowego. Aby uzyskać więcej informacji na temat funkcji wersji zapoznawczej, zobacz artykuł [Stores](https://docs.microsoft.com/azure/search/knowledge-store-concept-intro), [przyrostowe wzbogacanie](https://docs.microsoft.com/azure/search/cognitive-search-incremental-indexing-conceptual)i [prywatny punkt końcowy](https://docs.microsoft.com/azure/search/service-create-private-endpoint).

## <a name="assessment-and-planning"></a>Ocena i planowanie

Po przeniesieniu usługi wyszukiwania do nowego regionu należy [przenieść dane do nowej usługi magazynu](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#configure-the-new-storage-account) , a następnie ponownie skompilować indeksy, umiejętności i magazyny wiedzy. Należy zarejestrować bieżące ustawienia i skopiować pliki JSON, aby ułatwić i przyspieszyć ponowne kompilowanie usługi.

## <a name="moving-your-search-services-resources"></a>Przeniesienie zasobów usługi wyszukiwania

Aby rozpocząć, będziesz eksportować i modyfikować szablon Menedżer zasobów.

### <a name="export-a-template"></a>Eksportowanie szablonu

1. Zaloguj się do [Azure portal](https://portal.azure.com).

2. Przejdź do strony grupy zasobów.

> [!div class="mx-imgBorder"]
> przykład ![strony grupy zasobów](./media/search-move-resource/export-template-sample.png)

3. Wybierz pozycję **Wszystkie zasoby**.

3. W menu nawigacji po lewej stronie wybierz pozycję **Eksportuj szablon**.

4. Na stronie **Eksportuj szablon** wybierz pozycję **Pobierz** .

5. Znajdź plik zip pobrany z portalu i rozpakuj ten plik do wybranego folderu.

Plik zip zawiera pliki. JSON, które składają się na szablon i skrypty do wdrożenia szablonu.

### <a name="modify-the-template"></a>Modyfikowanie szablonu

Szablon zostanie zmodyfikowany przez zmianę nazw i regionów wyszukiwania oraz konta magazynu. Nazwy muszą być zgodne z regułami dotyczącymi poszczególnych konwencji nazewnictwa usług i regionów. 

Aby uzyskać kody lokalizacji regionu, zobacz [lokalizacje platformy Azure](https://azure.microsoft.com/global-infrastructure/locations/).  Kod regionu to nazwa regionu bez spacji, **środkowe stany usa** = **środkowe**.

1. W witrynie Azure Portal wybierz polecenie **Utwórz zasób**.

2. W obszarze **Przeszukaj witrynę Marketplace** wpisz **wdrożenie szablonu**, a następnie naciśnij klawisz **ENTER**.

3. Wybierz pozycję **Wdrożenie szablonu**.

4. Wybierz pozycję **Utwórz**.

5. Wybierz pozycję **Utwórz własny szablon w edytorze**.

6. Wybierz pozycję **Załaduj plik**, a następnie postępuj zgodnie z instrukcjami, aby załadować plik **Template. JSON** pobrany i rozpakowane w poprzedniej sekcji.

7. W pliku **Template. JSON** Nazwij docelowe konta wyszukiwania i magazynu przez ustawienie wartości domyślnej nazw konta wyszukiwania i magazynu. 

8. Edytuj Właściwość **Location** w pliku **Template. JSON** w regionie docelowym zarówno dla usług wyszukiwania, jak i magazynu. Ten przykład ustawia region docelowy na `centralus`.

```json
},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Search/searchServices",
            "apiVersion": "2015-08-19",
            "name": "[parameters('searchServices_target_region_search_name')]",
            "location": "centralus",
            "sku": {
                "name": "standard"
            },
            "properties": {
                "replicaCount": 1,
                "partitionCount": 1,
                "hostingMode": "Default"
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "[parameters('storageAccounts_tagetstorageregion_name')]",
            "location": "centralus",
            "sku": {
                "name": "Standard_RAGRS",
                "tier": "Standard"
            },
```

### <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Zapisz plik **Template. JSON** .

2. Wprowadź lub wybierz wartości właściwości:

- **Subskrypcja**: wybierz subskrypcję platformy Azure.

- **Grupa zasobów**: wybierz pozycję **Utwórz nową** i nadaj nazwę grupie zasobów.

- **Lokalizacja**: Wybierz lokalizację platformy Azure.

3. Kliknij pole wyboru **Zgadzam się na powyższe warunki i postanowienia** , a następnie kliknij przycisk **Wybierz zakup** .

## <a name="verifying-your-services-status-in-new-region"></a>Weryfikowanie stanu usług w nowym regionie

Aby zweryfikować przeniesienie, Otwórz nową grupę zasobów, a usługi zostaną wyświetlone na liście z nowym regionem.

Aby przenieść dane z regionu źródłowego do regionu docelowego, zobacz Wytyczne dotyczące [przenoszenia danych na nowe konto magazynu](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#move-data-to-the-new-storage-account).

## <a name="clean-up-resources-in-your-original-region"></a>Czyszczenie zasobów w pierwotnym regionie

Aby zatwierdzić zmiany i zakończyć Przenoszenie konta usługi, Usuń konto usługi źródłowej.

## <a name="next-steps"></a>Następne kroki

[Tworzenie indeksu](https://docs.microsoft.com/azure/search/search-get-started-portal)

[Utwórz zestawu umiejętności](https://docs.microsoft.com/azure/search/cognitive-search-quickstart-blob)

[Tworzenie sklepu merytorycznego](https://docs.microsoft.com/azure/search/knowledge-store-create-portal)

