---
title: Oferty prywatne | Azure Marketplace
description: Oferty prywatne w portalu Azure Marketplace dla wydawców aplikacji i usług.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/1/2018
ms.author: dsindona
ms.openlocfilehash: 67aba077304117ad357d4e004ce7bdb25ac58352
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285048"
---
# <a name="private-offers"></a>Oferty prywatne

Oferty prywatne w portalu [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/) umożliwiają wydawcom tworzenie jednostek SKU, które są widoczne tylko dla docelowych klientów.

## <a name="unlock-enterprise-deals-with-private-offers"></a>Odblokowywanie ofert dla przedsiębiorstw za pomocą ofert prywatnych

Klienci korporacyjni coraz częściej korzystają z internetowych platform handlowych, aby znaleźć, wypróbować i kupić rozwiązania w chmurze. Teraz dzięki ofertom prywatnym wydawcy mogą korzystać z portalu Marketplace do prywatnego udostępniania niestandardowych rozwiązań klientom docelowym z możliwościami, których potrzebują przedsiębiorstwa:

- *Wynegocjowane ceny* pozwalają wydawcom rozszerzyć rabaty i ceny poza listą z publicznie dostępnych ofert.
- *Prywatne warunki umożliwiają* wydawcom dostosowanie warunków do konkretnego klienta.
- *Wyspecjalizowane konfiguracje* umożliwiają wydawcom dostosowanie swoich maszyn wirtualnych, aplikacji platformy Azure i aplikacji SaaS do indywidualnych potrzeb klienta. Ta opcja umożliwia również wydawcom zapewnienie dostępu w wersji zapoznawczej do nowych funkcji produktu przed wprowadzeniem na rynek szerzej dla wszystkich klientów.

Oferty prywatne umożliwiają wydawcom korzystanie ze skali i globalnej dostępności rynku publicznego dzięki elastyczności i kontroli niezbędnej do negocjowania i dostarczania niestandardowych ofert i konfiguracji. Te funkcje razem otwierają drzwi do silnego wdrażania rynków w chmurze w przedsiębiorstwach.  Przedsiębiorstwa mogą teraz kupować i sprzedawać w sposób, którego oczekują i popyt.

Oferty prywatne są teraz dostępne dla maszyny wirtualnej, aplikacji platformy Azure (zaimplementowanych jako szablony rozwiązań lub aplikacje zarządzane) i oferty aplikacji SaaS. Podobnie jak oferty publiczne, oferty prywatne mogą być tworzone i zarządzane za pośrednictwem [portalu Cloud Partner.](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md)  Klienci mogą otrzymać lub odwołać dostęp do ofert prywatnych w ciągu kilku minut.

## <a name="creating-private-offers-using-skus-and-plans"></a>Tworzenie ofert prywatnych przy użyciu jednostek SKU i planów

W przypadku *nowych lub istniejących ofert z publicznymi jednostkami SKU lub planami*wydawcy mogą łatwo tworzyć nowe, prywatne odmiany, tworząc nowe jednostki SKU lub plany i oznaczając je jako prywatne.  [Prywatne jednostki SKU](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) i plany są składnikami oferty i są widoczne i dostępne tylko dla klientów docelowych. Prywatne jednostki SKU i plany mogą ponownie używać obrazów podstawowych i/lub oferować metadane już opublikowane dla publicznej jednostki SKU lub planu. Ta opcja umożliwia wydawcom tworzenie wielu prywatnych odmian oferty publicznej bez konieczności publikowania wielu wersji tego samego obrazu podstawowego i oferowania metadanych. W przypadku aplikacji Maszyny wirtualnej i platformy Azure oferuje tylko, gdy prywatna jednostka SKU udostępnia obraz podstawowy z publiczną jednostką SKU, wszelkie zmiany w obrazie podstawowym oferty będą propagowane we wszystkich publicznych i prywatnych jednostkach SKU przy użyciu tego obrazu podstawowego.

W przypadku *nowych ofert, które obejmują tylko prywatne jednostki SKU lub plany,* wydawcy mogą tworzyć swoje oferty jako dowolną inną ofertę, a następnie oznaczać jednostki SKU lub plany jako prywatne. Oferty, które mają tylko prywatne jednostki SKU lub plany nie będą wykrywalne lub dostępne za pośrednictwem [portalu Azure Marketplace](https://azuremarketplace.microsoft.com) lub [witryny Azure](https://azure.microsoft.com/features/azure-portal/) przez klientów, którzy nie są skojarzone z ofertą.

## <a name="targeting-customers-with-private-offers"></a>Kierowanie reklam do klientów za pomocą ofert prywatnych
W przypadku zarówno nowych, jak i istniejących ofert prywatnych wydawcy mogą kierować reklamy do klientów przy użyciu identyfikatorów subskrypcji. Wydawcy korzystający z oferty aplikacji maszyny wirtualnej lub usługi Azure mogą ograniczyć dostępność prywatnej jednostki SKU do indywidualnego identyfikatora subskrypcji platformy Azure lub przekazać plik CSV o identyfikatorach subskrypcji platformy Azure o wydajności do 20 000. Podczas korzystania z oferty prywatnej aplikacji SaaS wydawcy mogą skojarzyć identyfikator subskrypcji platformy Azure lub identyfikator dzierżawy, aby ograniczyć dostępność planu prywatnego przy użyciu metody przekazywania ręcznego lub CSV.

Po poświadczeniu i opublikowaniu oferty klienci mogą zostać zaktualizowani lub usunięci z jednostki SKU lub planu w ciągu kilku minut za pomocą funkcji Synchronizuj subskrypcje prywatne. Ta funkcja umożliwia wydawcom szybkie i łatwe aktualizowanie listy klientów, do których prezentowana jest prywatna jednostka SKU lub plan bez ponownej certyfikacji lub ponownego opublikowania oferty.

## <a name="deploying-private-offers"></a>Wdrażanie ofert prywatnych

Oferty prywatne można wykrywać tylko za pośrednictwem [portalu Azure](https://azure.microsoft.com/features/azure-portal/) i nie są prezentowane za pośrednictwem portalu [Azure Marketplace.](https://azuremarketplace.microsoft.com) Po zalogowaniu się do witryny Azure portal klienci mogą wybrać element nawigacji portalu Marketplace, aby uzyskać dostęp do swoich ofert prywatnych. Oferty prywatne pojawią się również w wynikach wyszukiwania i mogą być wdrażane za pomocą wiersza polecenia i szablonów usługi Azure Resource Manager, jak wszystkie inne oferty.

![[Oferty prywatne]](./media/marketplace-publishers-guide/private-offer.png)

Oferty prywatne pojawią się również w wynikach wyszukiwania. Wystarczy zwrócić uwagę na odznakę "Prywatne".

>[!Note]
>Oferty prywatne nie są obsługiwane w ramach subskrypcji utworzonych za pośrednictwem sprzedawcy programu Dostawcy rozwiązań w chmurze (CSP).

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć korzystanie z ofert prywatnych, wykonaj kroki opisane w [przewodniku Prywatne jednostki SKU i plany.](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md)
