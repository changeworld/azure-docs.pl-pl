---
title: Prywatne jednostki SKU i plany | Azure Marketplace
description: Jak korzystać z prywatnych jednostek SKU do zarządzania dostępnością oferty.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: dsindona
ms.openlocfilehash: ee3ab7be4d15b13a3c0bb014a3ca4d4096299b4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280392"
---
<a name="private-skus-and-plans"></a>Prywatne jednostki SKU i plany
============

Prywatne jednostki SKU umożliwiają ograniczenie dostępności jednostek SKU do określonych klientów. Gdy jednostka SKU jest oznaczona jako prywatna, nie jest dostępna w żadnym katalogu publicznym, w tym w [witrynie Azure Marketplace](https://azuremarketplace.microsoft.com) i [witrynie Azure portal.](https://portal.azure.com) W witrynie Azure portal tylko klienci z dostępem do jednostki SKU mogą go zobaczyć. Ponadto zostaliby również poproszeni o dostęp do ofert prywatnych.

>[!NOTE]
>Prywatne jednostki SKU muszą mieć nowe unikatowe identyfikatory jednostek SKU/Planu, aby uniknąć konfliktu z publicznymi jednostkami SKU.

Prywatne jednostki SKU umożliwiają obsługę następujących scenariuszy:

1.  Publikowanie oprogramowania, które ma być dostępne publicznie tylko dla określonych klientów i nie jest publicznie dostępne.
2.  Publikowanie odmian oprogramowania publicznego po dostosowanej cenie dla konkretnych klientów.
3.  Publikowanie odmian oprogramowania publicznego z dostosowanym opisem i warunkami (za pośrednictwem nowej oferty).

Jeśli chcesz tylko zmienić cenę, możesz ponownie użyć dysków z innej jednostki SKU w tej samej ofercie. W prywatnych jednostkach SKU nie trzeba ponownie przełożyć dysków w jednostkach SKU.

<a name="mark-a-sku-private"></a>Oznaczanie prywatnej jednostki SKU
---------------------

Aby oznaczyć jednostkę SKU jako prywatną, przełącz opcję pytając, czy jednostka SKU jest prywatna:

![Oznaczanie jednostki SKU jako prywatnej](./media/cloud-partner-portal-publish-virtual-machine/markingskuprivate.png)

Można ponownie użyć dysków w innej jednostce SKU i zmodyfikować ceny lub opis. Aby ponownie użyć dysków, wybierz **tak** jako odpowiedź na monit "Czy ta jednostka SKU ponownie używa obrazów z publicznej jednostki SKU".

Jeśli jednostka SKU jest oznaczona jako prywatna, a oferta ma inne jednostki SKU z dyskami wielokrotnego użycia, należy wskazać, że jednostka SKU ponownie używa dysków z innej jednostki SKU. Wymagane jest również określenie grupy docelowej dla prywatnej jednostki SKU.

>[!NOTE]
>Po jego opublikowaniu publiczna jednostka SKU nie może być prywatna.

<a name="select-an-image"></a>Wybieranie obrazu
------------------

Można podać nowe dyski dla prywatnej jednostki SKU lub ponownie użyć tych samych dysków już dostarczonych w innej jednostce SKU, modyfikując tylko ceny lub opis. Aby ponownie użyć dysków, wybierz **tak** jako odpowiedź na monit "Czy ten obraz jednostki SKU ponownie z publicznej jednostki SKU".

![Wskaż ponowne użycie obrazu](./media/cloud-partner-portal-publish-virtual-machine/selectimage1.png)

Po potwierdzeniu ponownego użycia obrazów przez jednostkę SKU wybierz źródło lub *podstawową* jednostkę SKU dla obrazów:

![Wybieranie obrazu](./media/cloud-partner-portal-publish-virtual-machine/selectimage2.png)

Podczas publikowania oferty obrazy z wybranej jednostki SKU zostaną udostępnione w ramach prywatnego identyfikatora jednostki SKU z niestandardowymi stawkami/warunkami. Prywatna jednostka SKU będzie widoczna tylko dla docelowych odbiorców.

W przypadku aktualizacji obrazu będzie wymagana tylko aktualizacja obrazu podstawowej jednostki SKU. Za kulisami obraz prywatnej jednostki SKU również zostanie automatycznie zaktualizowany. Podobnie, jeśli usuniesz obraz z podstawowej jednostki SKU, obraz zostanie również usunięty z prywatnej jednostki SKU.

<a name="restricting-the-audience"></a>Ograniczanie odbiorców
------------------------

Oferty prywatne można znaleźć i wdrożyć tylko przez użytkowników docelowych.
Obecnie obsługujemy kierowanie użytkowników przy użyciu identyfikatorów subskrypcji.

Subskrypcje te można wprowadzić za pomocą ręcznego formularza wejściowego **dla maksymalnie 10 subskrypcji**lub przesłania pliku CSV, który pozwala na maksymalnie **20 000 subskrypcji.**

Ręczny wpis dla odbiorców z ograniczeniami:

![Ręczne ograniczanie odbiorców](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience1.png)

Przesyłanie csv dla odbiorców z ograniczeniami:

![Ograniczanie grona odbiorców za pomocą pliku CSV](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience2.png)

Przykładowa zawartość pliku CSV:

            Type,Id,Description
            SubscriptionId,7738d703-3135-4e8d-8b81-1e70379abd9d,Private Customer

Po przełączeniu z ręcznego wprowadzania do widoku przekazywania CSV lub z pliku CSV do wprowadzania ręcznego stara lista identyfikatorów subskrypcji z dostępem do jednostki SKU nie jest zachowywana. Zostanie wyświetlone ostrzeżenie, a lista zostanie zastąpiona tylko po zapisaniu oferty.

<a name="managing-private-audiences"></a>Zarządzanie odbiorcami prywatnymi
-------------------------

**Aby zaktualizować odbiorców bez ponownego publikowania całej oferty, wprowadzasz żądane zmiany w odbiorcach (przy użyciu interfejsu użytkownika lub interfejsu API), a następnie inicjuj akcję "Synchronizuj grupy odbiorców prywatnych".**

Jeśli grupa odbiorców ma 10 lub mniej subskrypcji, możesz zarządzać nią całkowicie za pomocą interfejsu użytkownika CPP.

Jeśli grupa odbiorców ma więcej niż 10 subskrypcji, możesz zarządzać nim za pomocą pliku CSV, który można przekazać do interfejsu użytkownika CPP lub przy użyciu interfejsu API.

Jeśli używasz interfejsu API i nie chcesz obsługiwać pliku CSV, możesz zarządzać odbiorcami bezpośrednio za pomocą interfejsu API zgodnie z poniższymi instrukcjami.

> [!NOTE]
> Użyj identyfikatora subskrypcji platformy Azure (plany i jednostki SKU) lub identyfikatora dzierżawy (tylko plany), aby dodać odbiorców do oferty prywatnej.

###  <a name="managing-subscriptions-with-the-api"></a>Zarządzanie subskrypcjami za pomocą interfejsu API

Za pomocą interfejsu API można przesłać pliku CSV lub zarządzać odbiorcami bezpośrednio (bez użycia pliku CSV). Ogólnie rzecz biorąc, wystarczy pobrać ofertę, `restrictedAudience` zaktualizować obiekt, a następnie przesłać te zmiany z powrotem do oferty, aby dodać lub usunąć członków grupy odbiorców.

Aby programowo zaktualizować listę odbiorców:

1. Pobierz dane [oferty:](cloud-partner-portal-api-retrieve-specific-offer.md)

    ```
    GET https://cloudpartner.azure.com/api/publishers//offers/?api-version=2017-10-31&includeAllPricing=true
    ```

2. Znajdź obiekty grupy odbiorców z ograniczeniami w każdej jednostce SKU oferty przy użyciu tej kwerendy JPath:

    ```
    $.definition.plans[*].restrictedAudience
    ```
3. Zaktualizuj obiekt(y) grupy odbiorców z ograniczeniami dla oferty.

    **Jeśli pierwotnie przesłano listę subskrypcji dla oferty prywatnej z pliku CSV:**

    Twoje *restrictedAudience* obiekt (s) będzie wyglądać tak.
    ```
    "restrictedAudience": {
                  "uploadedCsvUri": "{SasUrl}"
    }
    ```

    Dla każdego obiektu grupy odbiorców z ograniczeniami:

    a. Pobierz zawartość `restrictedAudience.uploadedCsvUri`programu . Zawartość jest po prostu plikiem CSV z nagłówkami. Przykład:

        type,id,description
        subscriptionId,541a269f-3df2-486e-8fe3-c8f9dcf28205,sub1
        subscriptionId,c0da499c-25ec-4e4b-a42a-6e75635253b9,sub2

    b. W razie potrzeby dodaj lub usuń subskrypcje w pobranym pliku CSV.

    d. Przekaż zaktualizowany plik CSV do lokalizacji, takiej jak [magazyn obiektów Blob platformy Azure](../../storage/blobs/storage-blobs-overview.md) lub usługa [OneDrive,](https://onedrive.live.com)i utwórz łącze tylko do odczytu do pliku. To będzie twój nowy *SasUrl*.

    d. Zaktualizuj `restrictedAudience.uploadedCsvUri` klucz za pomocą nowego *pliku SasUrl*.

    **Jeśli ręcznie wprowadzono oryginalną listę subskrypcji oferty prywatnej z portalu Cloud Partner Portal:**

    Twoje *restrictedAudience* obiekt (s) będzie wyglądać mniej więcej tak:

    ```
    "restrictedAudience": {
        "manualEntries": [{
            "type": "subscriptionId",
            "id": "541a269f-3df2-486e-8fe3-c8f9dcf28205",
            "description": "sub1"
            }, {
            "type": "subscriptionId",
            "id": "c0da499c-25ec-4e4b-a42a-6e75635253b9",
            "description": "sub2"
            }
        ]}
    ```

    a. Dla każdego obiektu grupy odbiorców z ograniczeniami należy w razie potrzeby dodać lub usunąć wpisy na `restrictedAudience.manualEntries` liście.

4. Po zakończeniu aktualizacji wszystkich *restrictedAudience* obiektów dla każdej jednostki SKU oferty [prywatnej, zaktualizować ofertę:](cloud-partner-portal-api-creating-offer.md)

    ```
    PUT https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31
    ```
    Dzięki temu twoja zaktualizowana lista odbiorców jest już obowiązuje.

<a name="previewing-private-offers"></a>Podgląd ofert prywatnych
-------------------------

Podczas kroku podglądu/przemieszczania tylko subskrypcje w wersji zapoznawczej poziomu oferty będą mogły uzyskać dostęp do jednostki SKU. Na tym etapie testowania można wyświetlić podgląd oferty, jak mogłoby się wydawać klientom docelowym.

Subskrypcje w wersji zapoznawczej poziomu oferty, aby uzyskać dostęp do ofert etapowych:

![Identyfikatory subskrypcji w wersji zapoznawczej](./media/cloud-partner-portal-publish-virtual-machine/previewoffer1.png)

Po uruchomieniu oferty tylko subskrypcje grup owych (wprowadzone za pomocą wpisu ręcznego lub CSV) będą mogły wyświetlać i wdrażać prywatną jednostkę SKU. Zaleca się, aby **zawsze dołączać własne subskrypcje do grupy odbiorców z ograniczeniami** dla prywatnej jednostki SKU do celów weryfikacji.

>[!NOTE]
>Do celów debugowania zespoły pomocy technicznej i inżynierów firmy Microsoft będą również miały dostęp do tych prywatnych ofert.
