---
title: Omówienie sposobu tworzenia i wdrażania oferty w portalu Marketplace | Dokumentacja firmy Microsoft
description: Opis czynności, wymagane do stają się zatwierdzonych Microsoft dla deweloperów i tworzenia i wdrażania obrazu maszyny wirtualnej, szablonu, usługi danych lub usługi dla deweloperów w witrynie Azure Marketplace
services: marketplace-publishing
documentationcenter: ''
author: HannibalSII
manager: hascipio
editor: ''
ms.assetid: 5343bd26-c6e4-4589-85b7-4a2c00bba8ab
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2017
ms.author: hascipio
ms.openlocfilehash: 82580fbab68eab28a2027cd277213f1fb2a76e07
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39716005"
---
> [!NOTE]
> Ta dokumentacja nie jest już aktualny i nie jest właściwa lokalizacja. Zamiast tego, przejdź do portalu Azure Marketplace [Podręcznik sprzedawcy](https://docs.microsoft.com/azure/marketplace/seller-guide/cloud-partner-portal-seller-guide) wskazówki dotyczące publikowania oferty w portalu Azure Marketplace.

# <a name="publish-and-manage-an-offer-in-the-azure-marketplace"></a>Publikowanie i zarządzanie nimi oferty w portalu Azure Marketplace
W tym artykule znajduje się w celu ułatwienia deweloperom tworzenie, wdrażanie i zarządzanie ich rozwiązania wymienione w portalu Azure Marketplace dla innych klientów platformy Azure i partnerom kupowanie oraz używanie.

## <a name="marketplace-publishing"></a>Publikowanie w witrynie Marketplace
Jako platformy Azure wydawcy dystrybucji i sprzedawać innowacyjne rozwiązania lub usług innych deweloperów niezależnych dostawców oprogramowania i specjalistów IT w portalu Marketplace. Za pośrednictwem witryny Marketplace mogą dotrzeć do klientów, którzy chcą szybko tworzyć ich aplikacji opartych na chmurze i rozwiązań mobilnych. Jeśli rozwiązanie jest przeznaczony dla użytkowników biznesowych, warto wziąć pod uwagę [AppSource](http://appsource.microsoft.com) portalu marketplace.


## <a name="supported-types-of-solutions"></a>Obsługiwane typy rozwiązania
Pierwszą rzeczą, jaką chcesz zrobić jako wydawca jest określenie rodzaju rozwiązania oferty Twojej firmy. Portal Marketplace obsługuje następujące typy ofert:

|Typ rozwiązania|Maszyna wirtualna|Szablon rozwiązania|
|---|---|---|
|**Definicja**|Wstępnie skonfigurowane obrazy zawiera zainstalowany kompletny system operacyjny i co najmniej jednej aplikacji. Obraz maszyny wirtualnej zawiera informacje niezbędne do tworzenia i wdrażania maszyn wirtualnych w usłudze Azure Virtual Machines.|Struktura danych, która może odwoływać się do jednego lub więcej różnych usług platformy Azure, w tym usług opublikowanych przez innych sprzedawców. Subskrybenci platformy Azure służy do wdrożenia ofert z co najmniej w jednej, skoordynowanej sposób.|
|**Przykład**|Jako wydawca platformy Azure po utworzeniu i zweryfikować maszyny Wirtualnej za pomocą usługi innowacyjne bazy danych. Ma inne subskrybentom platformy Azure i wdrożeniem tej maszyny Wirtualnej do swoimi środowiskami usługi w chmurze.|Jako wydawca platformy Azure już powiązane zestawu usług firmy na platformie Azure, które ułatwiają szybkie do wdrożenia usług cloud services i równoważenia obciążenia i lepsze zabezpieczenia i wysokiej dostępności. Inne subskrybentom platformy Azure, można zaoszczędzić czas, przez szablon rozwiązania, który spełnia ich cel. Nie muszą ręcznie zlokalizować, nabywania, wdrażanie i konfigurowanie usług platformy Azure takie same lub podobne.|

> [!NOTE]
> Niektóre kroki są wspólne dla różnych rozwiązań, a inne są distinct do typu odpowiedniego rozwiązania. Ten artykuł zawiera krótkie omówienie kroków, które należy wykonać dla każdego typu rozwiązania.

## <a name="publish-a-solution"></a>Publikowanie rozwiązania
![Nominowanie, rejestrowanie, publikowanie](media/marketplace-publishing-getting-started/img01.png)

### <a name="nominate-your-solution-for-pre-approval"></a>Nominowanie rozwiązania dla wstępnego zatwierdzania
Aby opublikować maszynę wirtualną [rozwiązania](https://createopportunity.azurewebsites.net) w portalu Marketplace, należy wykonać, certyfikat platformy Microsoft Azure **formularz nominacji rozwiązania**.

>[!NOTE]
> Pracując przy użyciu Menedżera kont partnerów lub Menedżera partnera DX poproś nominuj rozwiązania do programu certyfikatów platformy Azure. Można także przejść do [certyfikat platformy Microsoft Azure](http://createopportunity.azurewebsites.net) strony sieci Web i wypełnij formularz zgłoszeniowy. Wprowadź adres e-mail menedżera konta partnera lub DX Menedżera partnera w **skontaktuj się z Microsoft sponsora** pole.

Jeśli spełniasz kryteria kwalifikacji [zasad uczestnictwa w portalu Azure Marketplace](http://go.microsoft.com/fwlink/?LinkID=526833) i wniosek zostanie zatwierdzony, możemy rozpocząć pracę możesz dołączyć rozwiązania w portalu Marketplace.

### <a name="register-your-account-as-a-microsoft-seller"></a>Zarejestruj swoje konto jako sprzedawcy firmy Microsoft
Zarejestruj konto Microsoft jako [konta Microsoft Developer](marketplace-publishing-accounts-creation-registration.md).

### <a name="publish-your-solution"></a>Publikowanie rozwiązania
Aby publikować rozwiązania w portalu Marketplace, wykonaj następujące kroki:
1. Spełniają te wymagania nietechnicznym.

    a. Spełnianie [nietechnicznym wymagania wstępne](marketplace-publishing-pre-requisites.md).

    b. Spełnianie [techniczne wymagania wstępne maszyny Wirtualnej](marketplace-publishing-vm-image-creation-prerequisites.md).

    c. Spełnianie [techniczne wymagania wstępne szablonu rozwiązania](marketplace-publishing-solution-template-creation-prerequisites.md).

2. Utwórz ofertę.

    a. Tworzenie [maszyny wirtualnej](marketplace-publishing-vm-image-creation.md) oferty.

    b. Tworzenie [szablon rozwiązania](marketplace-publishing-solution-template-creation.md) oferty.

3. Utwórz ofertę [marketingowych zawartości](marketplace-publishing-push-to-staging.md).

4. Przetestuj swoją ofertę w środowisku tymczasowym.

    a. Testowanie oferty maszyny Wirtualnej [przemieszczania](marketplace-publishing-vm-image-test-in-staging.md).

    b. Testowanie oferty szablonu rozwiązania [przemieszczania](marketplace-publishing-solution-template-test-in-staging.md).

5. Wdrażanie oferty [Marketplace](marketplace-publishing-push-to-production.md).


### <a name="create-and-manage-a-virtual-machine-image"></a>Tworzenie i zarządzanie nimi obraz maszyny wirtualnej
Tworzenie i zarządzanie nimi z obrazu maszyny Wirtualnej, korzystając z tych zasobów:
* Tworzenie obrazu maszyny Wirtualnej [lokalnych](marketplace-publishing-vm-image-creation-on-premise.md).
* Tworzenie maszyny wirtualnej z systemem [Windows w witrynie Azure portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Tworzenie maszyny wirtualnej z systemem [systemu Linux w witrynie Azure portal](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Rozwiązywanie typowych problemów napotykanych podczas [tworzenia wirtualnego dysku twardego](marketplace-publishing-vm-image-creation-troubleshooting.md).

## <a name="manage-your-solution"></a>Rozwiązania do zarządzania
Zarządzanie swoje rozwiązanie przy pomocy z następujących zasobów:
* [Zapoznaj się z przewodnikiem poprodukcyjnych dla oferty maszyny wirtualnej](marketplace-publishing-vm-image-post-publishing.md)
* [Aktualizowanie nietechnicznym szczegółów oferty lub jednostki SKU](marketplace-publishing-vm-image-post-publishing.md#update-the-nontechnical-details-of-an-offer-or-a-sku)
* [Aktualizuj szczegóły techniczne dotyczące oferty lub jednostki SKU](marketplace-publishing-vm-image-post-publishing.md#update-the-technical-details-of-a-sku)
* [Dodawanie nowej jednostki SKU w ramach oferty uwzględnione na liście](marketplace-publishing-vm-image-post-publishing.md#add-a-new-sku-under-a-listed-offer)
* [Zmień liczba dysków danych dla listy jednostek SKU](marketplace-publishing-vm-image-post-publishing.md#change-the-data-disk-count-for-a-listed-sku)
* [Usuń ofertę wymienionych w witrynie Marketplace](marketplace-publishing-vm-image-post-publishing.md)
* [Usuń jednostki SKU wymienione w portalu Marketplace](marketplace-publishing-vm-image-post-publishing.md#delete-a-listed-sku-from-the-marketplace)
* [Usuń bieżącą wersję jednostki SKU wymienionych w witrynie Marketplace](marketplace-publishing-vm-image-post-publishing.md#delete-the-current-version-of-a-listed-sku-from-the-marketplace)
* [Przywróć cena listy do produkcji wartości](marketplace-publishing-vm-image-post-publishing.md#revert-the-listing-price-to-production-values)
* [Przywróć model rozliczeń do produkcji wartości](marketplace-publishing-vm-image-post-publishing.md#revert-the-billing-model-to-production-values)
* [Przywróć ustawienie widoczności wymienionych jednostki SKU na wartość produkcji](marketplace-publishing-vm-image-post-publishing.md#revert-the-visibility-setting-of-a-listed-sku-to-the-production-value)

## <a name="additional-resources"></a>Zasoby dodatkowe
[Konfigurowanie programu Azure PowerShell](marketplace-publishing-powershell-setup.md)
