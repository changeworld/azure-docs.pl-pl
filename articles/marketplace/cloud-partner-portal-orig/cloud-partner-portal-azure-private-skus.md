---
title: Jednostki SKU w prywatnej i plany | Portal Azure Marketplace
description: Jak zarządzać dostępność oferty przy użyciu prywatnego jednostki SKU.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 6efdb1c28777d9230727066fdba03d2850be62b0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935921"
---
<a name="private-skus-and-plans"></a>Jednostki SKU w prywatnej i plany
============

Jednostki SKU w prywatnej pozwalają ograniczyć dostępności jednostki SKU dla specyficznych klientów. Gdy jednostka SKU jest oznaczony jako prywatny, nie jest dostępna w dowolnej publicznego katalogu, w tym na [portalu Azure Marketplace](https://azuremarketplace.microsoft.com) i [witryny Azure portal](https://portal.azure.com). W witrynie Azure portal tylko klientów z dostępem do jednostki SKU mogli je zobaczyć. Ponadto są również jest proszony mają dostęp do oferty prywatne.

>[!NOTE]
>Prywatne jednostki SKU musi mieć nowe unikatowe identyfikatory jednostka SKU/Plan w celu uniknięcia konfliktów z od publicznego jednostek SKU.

Jednostki SKU w prywatnej umożliwia obsługę następujących scenariuszy:

1.  Publikowanie oprogramowania, które mają tylko dostępne publicznie dla określonych klientów i nie jest dostępna publicznie.
2.  Publikowanie zmian publicznego oprogramowania cenie dostosowane dla określonych odbiorców.
3.  Publikowanie zmian publicznego oprogramowania za pomocą dostosowanych opis i warunki (przy użyciu nowej oferty).

Jeśli chcesz zmienić cenę, można ponownie użyć dysków z inną jednostkę SKU w ramach tej samej oferty. Za pomocą prywatnej jednostek SKU nie trzeba ponownie przesłać dysków dla jednostek SKU.

<a name="mark-a-sku-private"></a>Oznacz prywatnej jednostki SKU
---------------------

Aby oznaczyć jednostkę SKU jako prywatny, przełącz opcję pytaniem, jeśli jednostka SKU jest prywatny:

![Oznacz jednostkę SKU jako prywatne](./media/cloud-partner-portal-publish-virtual-machine/markingskuprivate.png)

Można ponownie użyć dysków w inną jednostkę SKU i zmodyfikować ceny lub opisu. Aby ponownie użyć dysków, wybierz **tak** w odpowiedzi na "Ma jednostkę SKU ponownego użycia obrazy z publicznego jednostki SKU" monit.

Jeśli oferta ma inne jednostki SKU z dyskami reuseable jednostki SKU jest oznaczony jako prywatny, jest wymagane, aby wskazać, że jednostka SKU ponownie używa dysków z inną jednostkę SKU. Jesteś także wymagana do określania docelowych odbiorców w ramach jednostki SKU prywatne.

>[!NOTE]
>Po jej opublikowania publicznego jednostki SKU nie można dokonać prywatnych.

<a name="select-an-image"></a>Wybierz obraz
------------------

Można podać nowe dyski dla prywatnych jednostki SKU lub ponownie użyć tych samych dysków poprawiał w innej jednostki SKU i tylko modyfikowanie ceny lub opis. Aby ponownie użyć dysków, wybierz **tak** w odpowiedzi na monit "Does ten obraz ponownego użycia jednostki SKU z publicznych jednostki SKU".

![Wskazać obraz ponownego użycia.](./media/cloud-partner-portal-publish-virtual-machine/selectimage1.png)

Po upewnieniu się, że jednostka SKU ponownie używa obrazów z inną jednostkę SKU, należy zidentyfikować jednostkę SKU, który jest źródłem obrazów.

Monity na następnym ekranie Przechwyć show sposób identyfikacji prywatnej jednostki SKU spowoduje ponowne użycie obrazów z wybrana jednostka SKU:

![Wybierz obraz](./media/cloud-partner-portal-publish-virtual-machine/selectimage2.png)

Po opublikowaniu oferty obrazów z wybrana jednostka SKU powinno być udostępniane w ramach prywatnej identyfikator jednostki SKU z niestandardowych stawki lub warunków. Prywatne jednostki SKU tylko będzie widoczny dla docelowej grupie odbiorców.

Aktualizowanie obrazu możesz tylko będzie potrzebować można zaktualizować obrazu podstawowej jednostki SKU. Za kulisami obrazu dla jednostki SKU w prywatnej zostaną również zaktualizowane automatycznie. Podobnie jeśli usuniesz obrazu z podstawowej jednostki SKU obrazu będzie również usunięte z prywatnego jednostki SKU.

<a name="restricting-the-audience"></a>Ograniczanie odbiorców
------------------------

Oferty prywatne można znaleźć i wdrożona tylko przez wybranych użytkowników.
Obecnie obsługujemy porę przy użyciu identyfikatory subskrypcji.

Te subskrypcje można wprowadzić za pomocą formularza ręcznego wprowadzania **dla subskrypcji w maksymalnie 10**, lub przez przekazanie pliku CSV, który umożliwia **dla subskrypcji w maksymalnie 20 000**.

Ręczne wprowadzanie ograniczonych odbiorców dla:

![Ręcznie ograniczyć odbiorców](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience1.png)

Przekaż CSV dla odbiorców ograniczeniami:

![Użyj pliku CSV, aby ograniczyć odbiorców](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience2.png)

Przykład zawartości pliku CSV:

            Type,Id,Description
            SubscriptionId,7738d703-3135-4e8d-8b81-1e70379abd9d,Private Customer

Po przełączeniu się z ręcznego wprowadzania do pliku CSV należy przekazać widoku lub z pliku CSV do ręcznego wprowadzania, stara lista identyfikatory subskrypcji z uprawnieniami do jednostki SKU nie są zachowywane. Zostanie wyświetlone ostrzeżenie, a listy tylko zostanie zastąpiony podczas zapisywania tej oferty.

<a name="sync-private-subscriptions"></a>Subskrypcje prywatne synchronizacji
-------------------------

Podczas dodawania subskrypcji do opublikowane oferty prywatne jednostki SKU lub planu, nie musisz ponownie opublikować ofertę Dodaj informacje o odbiorców. Po prostu użyć Identyfikatora subskrypcji platformy Azure (planów i jednostki SKU) lub identyfikator dzierżawy (tylko w planach), aby dodać odbiorców.

<a name="previewing-private-offers"></a>Wyświetlanie podglądu prywatne oferuje
-------------------------

Podczas (wersja zapoznawcza) / wdrażanie przejściowe krok tylko oferty subskrypcji poziomu (wersja zapoznawcza) będą mogli korzystać z jednostki SKU. Czy fazie testowania, w tym czasie możesz sprawdzić, jakie oferty będzie wyglądać docelowych klientów i jest standardem dla wszystkich typów publikowania.

Oferty subskrypcji (wersja zapoznawcza) poziom dostępu przygotowane oferty do:

![Identyfikatory subskrypcji (wersja zapoznawcza)](./media/cloud-partner-portal-publish-virtual-machine/previewoffer1.png)

Po oferta jest dostępna na żywo, tylko subskrypcje ograniczeniami odbiorców (wprowadzone za pośrednictwem wpis ręczny lub CSV) będzie mogła wyświetlać i wdrażanie prywatnego jednostki SKU. Zaleca się, że możesz **zawsze zawierać własne subskrypcje w ograniczonym odbiorców** prywatnej jednostki SKU do celów sprawdzania poprawności.

>[!NOTE]
>Na potrzeby debugowania, pomocy technicznej firmy Microsoft i zespoły inżynierów również mieć dostęp do tych oferty prywatne.
