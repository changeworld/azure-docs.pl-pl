---
title: Oferty prywatne | Portal Azure Marketplace
description: Prywatne oferty w witrynie Azure Marketplace dla wydawców aplikacji i usług.
services: Azure, Marketplace, Compute
author: qianw211
manager: pabutler
ms.service: marketplace
ms.topic: article
ms.date: 11/1/2018
ms.author: pabutler
ms.openlocfilehash: 3d97fd167f0f946239d4de24bb180796ebb498d3
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70885877"
---
# <a name="private-offers"></a>Oferty prywatne

Oferty prywatne na [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/) umożliwiają wydawcom tworzenie jednostek SKU, które są widoczne tylko dla klientów skierowanych do określonych.

## <a name="unlock-enterprise-deals-with-private-offers"></a>Odblokuj transakcje przedsiębiorstwa z ofertami prywatnymi

Klienci korporacyjni coraz częściej korzystają z rynków online do znajdowania, wypróbowania i kupowania rozwiązań w chmurze. Teraz dzięki ofertom prywatnym wydawcy mogą używać portalu Marketplace do prywatnego udostępniania dostosowanych rozwiązań klientom przeznaczonym dla klientów z możliwościami wymaganymi przez przedsiębiorstwa:

- *Wynegocjowane ceny* umożliwiają wydawcom rozliczanie rabatów i cenników spoza listy dostępnych publicznie.
- Warunki i postanowienia *prywatne* umożliwiają wydawcom Dostosowywanie warunków i postanowień do określonego klienta.
- *Wyspecjalizowane konfiguracje* umożliwiają wydawcom Dostosowywanie Virtual Machines, aplikacji platformy Azure i aplikacji SaaS do potrzeb poszczególnych klientów. Ta opcja umożliwia również wydawcom zapewnienie dostępu do wersji zapoznawczej do nowych funkcji produktu, przed rozpoczęciem szerszego uruchamiania wszystkich klientów.

Oferty prywatne umożliwiają wydawcom skorzystanie z zalet skalowalności i globalnej dostępności publicznej witryny Marketplace, z elastycznością i kontrolą potrzebną do negocjowania i dostarczania niestandardowych transakcji i konfiguracji. Razem te funkcje otwierają drzwi w celu uzyskania silnych rozwiązań korporacyjnych w chmurze.  Przedsiębiorstwa mogą teraz kupować i sprzedawać w oczekiwany sposób i popyt.

Oferty prywatne są teraz dostępne dla maszyny wirtualnej, aplikacji platformy Azure (wdrożonej jako szablony rozwiązań lub aplikacji zarządzanych) i oferowanych przez aplikacje SaaS. Podobnie jak w przypadku ofert publicznych, można tworzyć oferty prywatne i zarządzać nimi za pośrednictwem [Portal Cloud partner](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md).  Klientom można udzielić lub odwołać dostęp do ofert prywatnych w ciągu kilku minut.

## <a name="creating-private-offers-using-skus-and-plans"></a>Tworzenie ofert prywatnych przy użyciu jednostek SKU i planów

W przypadku *nowych lub istniejących ofert z publicznymi jednostkami SKU lub planami*wydawcy mogą łatwo tworzyć nowe, prywatne zmiany, tworząc nowe jednostki SKU lub plany i oznacz je jako prywatne.  [Prywatne jednostki SKU](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) i plany są składnikami oferty i są widoczne tylko dla klientów, którzy są jednostek. Prywatne jednostki SKU i plany mogą ponownie używać obrazów bazowych i/lub zaproponować już istniejące metadane dla publicznej jednostki SKU lub planu. Ta opcja umożliwia wydawcom tworzenie wielu prywatnych odmian oferty publicznej bez konieczności publikowania wielu wersji tego samego obrazu podstawowego i metadanych oferty. W przypadku maszyn wirtualnych i aplikacji platformy Azure tylko w przypadku, gdy prywatna jednostka SKU udostępnia obraz podstawowy z publiczną jednostką SKU, wszelkie zmiany obrazu podstawowego oferty będą propagowane dla wszystkich publicznych i prywatnych jednostek SKU przy użyciu tego obrazu podstawowego.

W przypadku *nowych ofert, które zawierają tylko prywatne jednostki SKU lub plany*, wydawcy mogą utworzyć oferty jako każdą inną ofertę, a następnie oznaczyć jednostki SKU lub plany jako prywatne. Oferty, które mają tylko prywatne jednostki SKU lub plany, nie będą wykrywalne lub dostępne za pośrednictwem [portalu Azure Marketplace](https://azuremarketplace.microsoft.com) ani [Azure Portal](https://azure.microsoft.com/features/azure-portal/) przez klientów, którzy nie zostali powiązani z ofertą.

## <a name="targeting-customers-with-private-offers"></a>Kierowanie klientów do ofert prywatnych
W przypadku nowych i istniejących ofert prywatnych wydawcy mogą kierować klientów, którzy korzystają z identyfikatorów subskrypcji. Wydawcy korzystający z maszyny wirtualnej lub oferty aplikacji platformy Azure mogą ograniczyć dostępność prywatnej jednostki SKU do indywidualnego identyfikatora subskrypcji platformy Azure lub przekazać plik CSV o wartości do 20 000 identyfikatorów subskrypcji platformy Azure. Przy użyciu prywatnej oferty aplikacji SaaS wydawcy mogą skojarzyć identyfikator subskrypcji platformy Azure lub identyfikator dzierżawy, aby ograniczyć dostępność planu prywatnego przy użyciu metody przekazywania ręcznego lub woluminu CSV.

Po pobraniu i opublikowaniu oferty klienci mogą zaktualizować lub usunąć ją z jednostki SKU lub zaplanować w ciągu kilku minut, korzystając z funkcji Synchronizuj subskrypcje prywatne. Ta funkcja umożliwia wydawcom szybkie i łatwe aktualizowanie listy klientów, do których zostanie przedstawiona prywatna jednostka SKU lub plan bez ponownej certyfikacji lub ponownego opublikowania oferty.

## <a name="deploying-private-offers"></a>Wdrażanie ofert prywatnych

Oferty prywatne są wykrywalne wyłącznie za pośrednictwem [Azure Portal](https://azure.microsoft.com/features/azure-portal/) i nie są prezentowane za pośrednictwem [portalu Azure Marketplace](https://azuremarketplace.microsoft.com). Po zalogowaniu się do Azure Portal klienci mogą wybrać element nawigacyjny portalu Marketplace, aby uzyskać dostęp do swoich ofert prywatnych. Oferty prywatne będą również wyświetlane w wynikach wyszukiwania i można je wdrożyć za pośrednictwem wiersza polecenia i szablonów Azure Resource Manager, takich jak wszystkie inne oferty.

![[Oferty prywatne]](./media/marketplace-publishers-guide/private-offer.png)

Oferty prywatne będą również wyświetlane w wynikach wyszukiwania. Po prostu wyszukaj wskaźnik "prywatny".

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć korzystanie z ofert prywatnych, wykonaj kroki opisane w podręczniku [prywatne jednostki SKU i plany](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) .
