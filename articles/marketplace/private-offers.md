---
title: Oferty prywatne | Portal Azure Marketplace
description: Oferty prywatne w portalu Azure Marketplace wydawcy aplikacji i usługi.
services: Azure, Marketplace, Compute
author: qianw211
manager: pabutler
ms.service: marketplace
ms.topic: article
ms.date: 11/1/2018
ms.author: qianw211
ms.openlocfilehash: 179775076382022b9a97e7d76bc1e0dc4fbf69db
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64937570"
---
# <a name="private-offers"></a>Oferty prywatne

Prywatne oferuje [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/) Włącz wydawcy utworzyć jednostki SKU, które są widoczne tylko dla klientów docelowych.

## <a name="unlock-enterprise-deals-with-private-offers"></a>Odblokuj enterprise odnosi się do oferty prywatne

Klienci korporacyjni coraz bardziej umożliwia rynków online znajdowanie, testowanie i kupować rozwiązania w chmurze. Teraz przy użyciu oferty prywatne wydawców umożliwia marketplace prywatnie udostępniać dostosowane rozwiązania docelowych klientów z funkcjami, które wymagają przedsiębiorstwa:

- *Wynegocjowane ceny* umożliwia wydawców rozszerzyć rabaty i ceny z publicznie dostępnych ofert spoza listy.
- *Prywatne warunków i postanowień* Włącz wydawcy dostosować warunki i postanowienia dla określonego klienta.
- *Wyspecjalizowane konfiguracje* umożliwiają wydawców dostosować swoje maszyny wirtualne, aplikacje platformy Azure i aplikacji SaaS oferty do potrzeb indywidualnych klientów. Ta opcja umożliwia także wydawcy zapewnić dostęp (wersja zapoznawcza) do nowych funkcji produktów, przed uruchomieniem szerzej dla wszystkich klientów.

Oferty prywatne zezwala na wydawcy korzystać z zalet skalowania i globalnej dostępności publicznej platformy handlowej, elastyczność i kontrolę niezbędne do negocjowania i dostarczania niestandardowych oferty i konfiguracji. Razem te funkcje otwórz drzwi do przyjęcia silne enterprise platformom handlowym w chmurze.  Przedsiębiorstw można teraz kupować i sprzedawać w sposób, który chcą i żądanie.

Oferty prywatne są teraz dostępne dla maszyny wirtualnej i aplikacji na platformie Azure (zaimplementowane jako szablony rozwiązań lub zarządzanych aplikacji) oraz umożliwia aplikacji SaaS. Oferty publicznej, np. oferty prywatne mogą być tworzone i zarządzane za pośrednictwem [portalu Cloud Partner](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus).  Klientów można udzielić lub odwołać dostęp do oferty prywatne w ciągu kilku minut.

## <a name="creating-private-offers-using-skus-and-plans"></a>Tworzenie prywatnego oferuje przy użyciu jednostki SKU i plany

Aby uzyskać *nowej lub istniejącej oferty, jednostki SKU publicznego lub plany*, wydawców można łatwo utworzyć nowe, prywatne zmian przez tworzenie nowej jednostki SKU lub planów i oznaczysz je jako prywatne.  [Jednostki SKU w prywatnej](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus) i plany są składnikami oferty są tylko widoczne, jak i płatnej wersji przez klientów docelowych. Jednostki SKU w prywatnej i plany można ponownie użyć podstawowa obrazów i/lub oferują metadanych już opublikowane dla jednostki SKU publicznego lub planu. Ta opcja umożliwia wydawców do utworzenia wielu prywatnych wariantów oferty publicznej bez konieczności publikowanie wielu wersji tego samego obrazu podstawowego i oferują metadanych. Dla maszyny wirtualnej platformy Azure aplikacji oferty i tylko wtedy gdy prywatnej jednostki SKU udostępni obraz podstawowy SKU publicznego, wszelkie zmiany oferty obrazu podstawowego rozpropaguje we wszystkich publicznych i prywatnych wersji SKU. za pomocą tego obrazu podstawowego.

Aby uzyskać *nowych ofert obejmujących tylko prywatne jednostki SKU lub plany*, wydawcy można tworzyć ofert jako jakąkolwiek inną ofertę i następnie Oznacz jako prywatne jednostki SKU lub plany. Oferty, zawierających tylko prywatne jednostki SKU lub planów nie będą się wykrywalny ani dostępne za pośrednictwem [portalu Azure Marketplace](https://azuremarketplace.microsoft.com) lub [witryny Azure portal](https://azure.microsoft.com/features/azure-portal/) przez klientów, które nie są skojarzone z ofertą.

## <a name="targeting-customers-with-private-offers"></a>Przeznaczone dla klientów z oferty prywatne
Dla istniejących i nowych oferty prywatne wydawcy mogą kierować klientów przy użyciu identyfikatorów subskrypcji. Wydawcy przy użyciu oferty maszyny wirtualnej lub aplikacji na platformie Azure można ograniczyć dostępności jednostki SKU prywatne identyfikatorowi indywidualnej subskrypcji platformy Azure lub przekazywanie pliku CSV do 20 000 identyfikatorów subskrypcji platformy Azure. Podczas korzystania z oferty prywatne aplikacji SaaS, wydawców można skojarzyć Identyfikatora subskrypcji platformy Azure lub identyfikator dzierżawy, aby ograniczyć dostępność planu prywatnej za pomocą ręcznego inicjowania obsługi lub metody upload CSV.

Gdy oferta zostanie certyfikowane i opublikowane, klienci mogą zostać zaktualizowane lub usunięte z jednostki SKU lub planu w ciągu kilku minut za pomocą funkcji synchronizacji prywatne subskrypcji. Ta funkcja umożliwia wydawcy szybko i łatwo zaktualizować listę klientów, do których prywatne jednostki SKU lub planu są prezentowane bez recertifying lub ponowne opublikowanie oferty.

## <a name="deploying-private-offers"></a>Wdrażanie prywatnego oferuje

Oferty prywatne są tylko odnaleźć za pomocą [witryny Azure portal](https://azure.microsoft.com/features/azure-portal/) i nie są prezentowane za pomocą [portalu Azure Marketplace](https://azuremarketplace.microsoft.com). Po zalogowaniu się do portalu Azure, klienci mogą wybrać element nawigacyjny portalu Marketplace na dostęp do swojej oferty prywatne. Oferty prywatne będą również wyświetlane w wynikach wyszukiwania i którą można wdrożyć za pomocą wiersza polecenia i szablonami usługi Azure Resource Manager, takimi jak inne oferty.

![[Oferty prywatne]](./media/marketplace-publishers-guide/private-offer.png)

Oferty prywatne będą również wyświetlane w wynikach wyszukiwania. Po prostu Zwróć uwagę na wskaźniku "Private".

> [!Note]
> Oferty prywatne nie są obsługiwane z subskrypcjami nawiązane za pośrednictwem odsprzedawcy programu Cloud Solution Provider (CSP).

## <a name="next-steps"></a>Kolejne kroki

Jeśli chcesz móc korzystać z nowych funkcji, możesz rozpocząć sprzedaż w [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/sell).
