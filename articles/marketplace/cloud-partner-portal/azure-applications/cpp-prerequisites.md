---
title: Wymagania wstępne oferty aplikacji platformy Azure | Portal Azure Marketplace
description: Wymagania wstępne dotyczące publikowania aplikacji systemu Azure oferują w portalu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: pabutler
ms.openlocfilehash: 64039234a3863332ca19b915fb59a5271625d695
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66258186"
---
# <a name="azure-application-prerequisites"></a>Wstępnie wymagane składniki aplikacji platformy Azure

W tym artykule opisano wymagania wstępne techniczne i biznesowe dotyczące publikowania w portalu Azure Marketplace oferty aplikacji zarządzanej.  Jeśli jeszcze tego nie zrobiono, zapoznaj się z następujących źródeł danych:
- Zależnie od typu jednostki SKU, albo [aplikacji platformy Azure: Szablon rozwiązania oferują Podręcznik publikowania](../../marketplace-solution-templates.md) lub [aplikacje platformy Azure: Oferty aplikacji zarządzanej Podręcznik publikowania](../../marketplace-managed-apps.md)
- [Tworzenie szablonów rozwiązań, a Managed Applications dla witryny Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603) wideo


## <a name="technical-requirements"></a>Wymagania techniczne

Wymagania techniczne zawierają następujące elementy:

*   Szablony usługi Azure Resource Manager, aby uzyskać więcej informacji, zobacz [Omówienie struktury i składni szablonów usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). W tym artykule opisano strukturę szablonu usługi Azure Resource Manager. Przedstawia on różne części szablonu i właściwości, które są dostępne w tych sekcjach. Szablon składa się z kodu JSON i wyrażeń, których można używać do tworzenia wartości na potrzeby wdrożenia. 
* Szablony szybkiego startu platformy Azure.<br> Aby uzyskać więcej informacji, zobacz:

  * [Szablony Szybkiego startu platformy Azure](https://azure.microsoft.com/documentation/templates/). Wdrażaj zasoby platformy Azure, używając usługi Azure Resource Manager i szablonów współtworzonych przez społeczność, aby robić więcej. Usługa Azure Resource Manager pozwala inicjować obsługę aplikacji za pomocą deklaratywnych szablonów. W pojedynczym szablonie możesz wdrożyć wiele usług wraz z ich zależnościami. Za pomocą tego samego szablonu możesz wdrażać aplikację na każdym etapie jej cyklu życia.
  * [GitHub: Szablony szybkiego startu usługi Azure Resource Manager](https://github.com/azure/azure-quickstart-templates). To repozytorium zawiera wszystkie aktualnie dostępne usługi Azure Resource Manager Szablony udostępnione przez społeczność. Indeks szablonu z możliwością wyszukiwania jest utrzymywany na https://azure.microsoft.com/documentation/templates/.
* Tworzenie definicji interfejsu użytkownika<br>
Aby uzyskać więcej informacji, zobacz [interfejs użytkownika portalu tworzenie platformy Azure dla aplikacji zarządzanej](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview). W tym artykule przedstawiono podstawowe pojęcia dotyczące pliku createUiDefinition.json. Azure portal używa tego pliku do generowania interfejsu użytkownika do tworzenia aplikacji zarządzanej.


## <a name="business-requirements"></a>Wymagania biznesowe

Wymagania biznesowe obejmują następujące obowiązki umowne stosowane w UE i prawne proceduralne:

* Musi być zarejestrowany wydawcy chmury w witrynie Marketplace. Jeśli nie są zarejestrowane, postępuj zgodnie z instrukcjami w artykule [stają się wydawcy w witrynie Marketplace chmury](https://docs.microsoft.com/azure/marketplace/become-publisher
).

>[!NOTE]
>Zalogować się do portalu Cloud Partner, należy używać tego samego konta Microsoft Developer Center w rejestracji. Powinna mieć tylko jedno konto Microsoft, dla ofert portalu Azure Marketplace. To konto nie powinny być specyficzne dla poszczególnych usług lub oferty.

* Siedziba firmy (lub firmy od niej zależnej) musi być w sprzedaży z kraj/region obsługiwanym przez Portal Azure Marketplace. Aby uzyskać bieżącą listę tych krajów/regionów, zobacz [Microsoft Azure Marketplace — zasady udziału](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
* Musi być licencjonowany produkt w sposób, który jest zgodny z modelami rozliczeń obsługiwanym przez Portal Azure Marketplace. Aby uzyskać więcej informacji, zobacz [opcjami rozliczania](https://docs.microsoft.com/azure/marketplace/marketplace-commercial-transaction-capabilities-and-considerations) w witrynie Azure Marketplace.
* Jesteś odpowiedzialny za wprowadzanie pomocy technicznej dostępne dla klientów w sposób rozsądny z komercyjnego punktu widzenia. Może to mieć wolne, płatną lub za pośrednictwem metody społeczności.
* Jesteś odpowiedzialny za Licencjonowanie oprogramowania oraz wszystkie zależności oprogramowania innych firm.
* Należy podać zawartość, która spełnia kryteria za ofertę użytkownika był wyświetlany w portalu Azure Marketplace i w witrynie Azure portal.
* Musisz wyrazić zgodę na warunki Microsoft Azure Marketplace — zasady udziału oraz Umowie wydawcy portalu.
* Muszą być zgodne z Microsoft Azure warunkami korzystania z witryny, zasady zachowania poufności informacji firmy Microsoft i umowę programu certyfikat platformy Azure firmy Microsoft.


## <a name="publishing-requirements"></a>Wymagania dotyczące publikowania

Aby opublikować nową ofertę aplikacji platformy Azure, musi spełniać następujące wymagania wstępne:

* Ma metadane gotowe do użycia. Na poniższej liście (otwarta) przedstawiono przykład te metadane:
  * Tytuł
  * Opis (format HTML)
  * Obraz logo (w formacie PNG) i te rozmiary obrazów: 40 40 pikseli, 90 x 90 pikseli, 115 x 115 pikseli i 255 x 115 pikseli.
* A *warunki użytkowania* i *zasady zachowania poufności informacji* dokumentów
* Dokumentacja aplikacji
* Kontakt z pomocą techniczną


## <a name="next-steps"></a>Kolejne kroki

Jeśli zostały spełnione wszystkie wymagania, wszystko będzie gotowe do [Tworzenie oferty usługi Azure application](./cpp-create-offer.md). 
 
