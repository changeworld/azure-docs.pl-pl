---
title: Wymagania wstępne oferty aplikacji platformy Azure | Portal Azure Marketplace
description: Wymagania wstępne dotyczące publikowania oferty aplikacji platformy Azure w portalu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: pabutler
ms.openlocfilehash: 261af94e233bdb6189d7819f8f28c7e23b5dd112
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826142"
---
# <a name="azure-application-prerequisites"></a>Wymagania wstępne dotyczące aplikacji platformy Azure

W tym artykule opisano wymagania techniczne i biznesowe dotyczące publikowania oferty zarządzanej aplikacji w portalu Azure Marketplace.  Jeśli jeszcze tego nie zrobiono, zapoznaj się z poniższymi źródłami informacji:
- W zależności od typu jednostki SKU [usługa Azure Applications: Przewodnik publikowania oferty szablonów rozwiązań](../../marketplace-solution-templates.md) lub [aplikacji platformy Azure: Przewodnik publikowania oferty aplikacji zarządzanej](../../marketplace-managed-apps.md)
- [Kompilowanie szablonów rozwiązań i aplikacji zarządzanych dla wideo w portalu Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603)


## <a name="technical-requirements"></a>Wymagania techniczne

Wymagania techniczne obejmują następujące elementy:

*   Azure Resource Manager szablonów Aby uzyskać więcej informacji, zobacz [Opis struktury i składni Azure Resource Manager szablonów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). W tym artykule opisano strukturę szablonu Azure Resource Manager. Przedstawia różne sekcje szablonu i właściwości, które są dostępne w tych sekcjach. Szablon składa się z notacji JSON i wyrażeń, których można użyć do konstruowania wartości dla wdrożenia. 
* Szablony szybkiego startu platformy Azure.<br> Aby uzyskać więcej informacji, zobacz:

  * [Szablony Szybkiego startu platformy Azure](https://azure.microsoft.com/documentation/templates/). Wdrażaj zasoby platformy Azure, używając usługi Azure Resource Manager i szablonów współtworzonych przez społeczność, aby robić więcej. Usługa Azure Resource Manager pozwala inicjować obsługę aplikacji za pomocą deklaratywnych szablonów. Pojedynczy szablon umożliwia wdrożenie wielu usług wraz z ich zależnościami. Za pomocą tego samego szablonu możesz wdrażać aplikację na każdym etapie jej cyklu życia.
  * [GitHub: Azure Resource Manager szablonów szybkiego startu](https://github.com/azure/azure-quickstart-templates). To repozytorium zawiera wszystkie szablony Azure Resource Manager udostępniane przez społeczność. Indeks szablonu do przeszukiwania jest obsługiwany w https://azure.microsoft.com/documentation/templates/.
* Tworzenie definicji interfejsu użytkownika<br>
Aby uzyskać więcej informacji, zobacz [Tworzenie interfejsu użytkownika Azure Portal dla aplikacji zarządzanej](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview). W tym artykule wprowadzono podstawowe pojęcia dotyczące pliku createUiDefinition. JSON. Azure Portal używa tego pliku do wygenerowania interfejsu użytkownika do tworzenia aplikacji zarządzanej.


## <a name="business-requirements"></a>Wymagania biznesowe

Wymagania biznesowe obejmują następujące kwestie proceduralne, umowne i prawne:

* Musisz być zarejestrowanym wydawcą portalu Marketplace w chmurze. Jeśli nie zarejestrowano Cię, wykonaj kroki opisane w artykule [Zostań wydawcy portalu Marketplace w chmurze](https://docs.microsoft.com/azure/marketplace/become-publisher
).

>[!NOTE]
>Aby zalogować się do portal Cloud Partner, należy użyć tego samego konta rejestracji Microsoft Developer Center. Dla ofert portalu Azure Marketplace powinna istnieć tylko jedna konto Microsoft. To konto nie powinno być specyficzne dla poszczególnych usług lub ofert.

* Firma (lub jej oddział) musi znajdować się w kraju/regionie, który jest obsługiwany przez portal Azure Marketplace. Aby zapoznać się z bieżącą listą tych krajów/regionów, zobacz [Microsoft Azure Marketplace zasad udziału](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
* Produkt musi być licencjonowany w sposób zgodny z modelami rozliczeń obsługiwanymi przez portal Azure Marketplace. Aby uzyskać więcej informacji, zobacz [Opcje rozliczeń](https://docs.microsoft.com/azure/marketplace/marketplace-commercial-transaction-capabilities-and-considerations) w portalu Azure Marketplace.
* Użytkownik jest odpowiedzialny za pomoc techniczną dla klientów w rozsądnie uzasadniony sposób. Ta pomoc techniczna może być bezpłatna, płatna lub przez podejścia do społeczności.
* Użytkownik jest odpowiedzialny za Licencjonowanie oprogramowania oraz wszelkich zależności oprogramowania innych firm.
* Musisz podać zawartość, która spełnia kryteria oferty, która będzie wyświetlana w witrynie Azure Marketplace i w Azure Portal.
* Należy wyrazić zgodę na warunki Microsoft Azure Marketplace zasad uczestnictwa i umowy wydawcy.
* Musisz przestrzegać warunków użytkowania witryny sieci Web Microsoft Azure, zasad zachowania poufności informacji firmy Microsoft i umowy Microsoft Azure certyfikowanych programów.


## <a name="publishing-requirements"></a>Wymagania dotyczące publikowania

Aby opublikować nową ofertę aplikacji platformy Azure, należy spełnić następujące wymagania wstępne:

* Przygotuj swoje metadane do użycia. Na poniższej liście przedstawiono przykład tych metadanych:
  * Tytuł
  * Opis (w formacie HTML)
  * Obraz logo (w formacie PNG) i w następujących stałych rozmiarach: 40 x 40 pikseli, 90 x 90 pikseli, 115 x 115 pikseli i 255 x 115 pikseli.
* *Warunki użytkowania* i dokumenty *zasad zachowania poufności informacji*
* Dokumentacja aplikacji
* Skontaktuj się z pomocą techniczną


## <a name="next-steps"></a>Następne kroki

Po spełnieniu wszystkich wymagań będziesz mieć możliwość [utworzenia oferty aplikacji platformy Azure](./cpp-create-offer.md). 
 
