---
title: Wymagania wstępne oferty aplikacji platformy Azure | Azure Marketplace
description: Wymagania wstępne dotyczące publikowania oferty aplikacji platformy Azure w portalu Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: dsindona
ms.openlocfilehash: b927404758dc59419fb3c5e638ac32758d534681
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281752"
---
# <a name="azure-application-prerequisites"></a>Wymagania wstępne aplikacji platformy Azure

W tym artykule opisano wymagania techniczne i biznesowe dotyczące publikowania oferty aplikacji zarządzanych w portalu Azure Marketplace.  Jeśli jeszcze tego nie zrobiłeś, zapoznaj się z następującymi źródłami informacji:
- W zależności od typu jednostki SKU przewodnik [publikowania aplikacji azure: szablon rozwiązania](../../marketplace-solution-templates.md) lub przewodnik publikowania aplikacji platformy [Azure: Przewodnik publikowania oferty zarządzanych aplikacji](../../marketplace-managed-apps.md)
- [Tworzenie szablonów rozwiązań i aplikacji zarządzanych dla witryny Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603) — wideo


## <a name="technical-requirements"></a>Wymagania techniczne

Wymagania techniczne obejmują następujące elementy:

*   Szablony usługi Azure Resource Manager Aby uzyskać więcej informacji, zobacz [Opis struktury i składni szablonów usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). W tym artykule opisano strukturę szablonu usługi Azure Resource Manager. Przedstawia różne sekcje szablonu i właściwości, które są dostępne w tych sekcjach. Szablon składa się z JSON i wyrażeń, których można użyć do konstruowania wartości dla wdrożenia. 
* Szablony szybki start platformy Azure.<br> Aby uzyskać więcej informacji, zobacz:

  * [Szablony Szybkiego startu platformy Azure](https://azure.microsoft.com/documentation/templates/). Wdrażanie zasobów platformy Azure za pośrednictwem usługi Azure Resource Manager za pomocą szablonów współtworzonych przez społeczność, aby wykonać więcej. Usługa Azure Resource Manager pozwala aprowizować aplikacje za pomocą deklaratywnych szablonów. Pojedynczy szablon umożliwia wdrożenie wielu usług wraz z ich zależnościami. Za pomocą tego samego szablonu możesz wdrażać aplikację na każdym etapie jej cyklu życia.
  * [GitHub: Szablony szybkiego startu usługi Azure Resource Manager](https://github.com/azure/azure-quickstart-templates). To repozytorium zawiera wszystkie aktualnie dostępne szablony usługi Azure Resource Manager udostępnione przez społeczność. Indeks szablonu z wyszukujalny jest utrzymywany na poziomie https://azure.microsoft.com/documentation/templates/.
* Tworzenie definicji interfejsu użytkownika<br>
Aby uzyskać więcej informacji, zobacz [Tworzenie interfejsu użytkownika portalu Azure dla zarządzanej aplikacji](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview). W tym artykule przedstawiono podstawowe pojęcia pliku createUiDefinition.json. Portal Azure używa tego pliku do generowania interfejsu użytkownika do tworzenia aplikacji zarządzanej.


## <a name="business-requirements"></a>Wymagania biznesowe

Wymogi biznesowe obejmują następujące obowiązki proceduralne, umowne i prawne:

* Musisz być zarejestrowanym wydawcą cloud marketplace. Jeśli nie jesteś zarejestrowany, wykonaj czynności opisane w artykule [Zostań wydawcą cloud marketplace](https://docs.microsoft.com/azure/marketplace/become-publisher
).

>[!NOTE]
>Aby zalogować się do portalu Cloud Partner Portal, należy użyć tego samego konta rejestracji w Centrum deweloperów firmy Microsoft. Powinieneś mieć tylko jedno konto Microsoft dla swoich ofert w portalu Azure Marketplace. To konto nie powinno być specyficzne dla poszczególnych usług lub ofert.

* Twoja firma (lub jej spółka zależna) musi znajdować się w sprzedaży z kraju/regionu obsługiwanego przez portal Azure Marketplace. Aby uzyskać aktualną listę tych krajów/regionów, zobacz [Zasady uczestnictwa w portalu Microsoft Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
* Produkt musi być licencjonowany w sposób zgodny z modelami rozliczeń obsługiwanymi przez witrynę Azure Marketplace. Aby uzyskać więcej informacji, zobacz [opcje rozliczeń](https://docs.microsoft.com/azure/marketplace/marketplace-commercial-transaction-capabilities-and-considerations) w portalu Azure Marketplace.
* Użytkownik jest odpowiedzialny za udostępnianie pomocy technicznej klientom w sposób uzasadniony z handlowego punktu widzenia. Wsparcie to może być bezpłatne, płatne lub za pośrednictwem podejścia społeczności.
* Użytkownik jest odpowiedzialny za licencjonowanie oprogramowania i wszelkich zależności oprogramowania innych firm.
* Należy podać zawartość, która spełnia kryteria dla oferty, które mają być wyświetlane w witrynie Azure Marketplace i w witrynie Azure portal.
* Użytkownik musi zaakceptować warunki zasad uczestnictwa w portalu Microsoft Azure Marketplace i umowy dotyczącej wydawcy.
* Użytkownik musi przestrzegać Warunków użytkowania witryny sieci Web platformy Microsoft Azure, Zasad zachowania poufności informacji firmy Microsoft i umowy o certyfikowanym programie platformy Microsoft Azure.


## <a name="publishing-requirements"></a>Wymagania dotyczące publikowania

Aby opublikować nową ofertę aplikacji platformy Azure, należy spełnić następujące wymagania wstępne:

* Przygotuj metadane do użycia. Na poniższej liście (niewyczerpywnej) przedstawiono przykład tych metadanych:
  * Tytuł
  * Opis (w formacie HTML)
  * Obraz logo (w formacie PNG) i w tych stałych rozmiarach obrazu: 40 x 40 pikseli, 90 x 90 pikseli, 115 x 115 pikseli i 255 x 115 pikseli.
* *Warunki użytkowania* i dokumenty *polityki prywatności*
* Dokumentacja aplikacji
* Kontakt z pomocą techniczną


## <a name="next-steps"></a>Następne kroki

Po spełnieniu wszystkich wymagań będziesz gotowy do [utworzenia oferty aplikacji platformy Azure.](./cpp-create-offer.md) 
 
