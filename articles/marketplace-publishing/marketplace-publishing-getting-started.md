---
title: Omówienie sposobu tworzenia i wdrażania oferty w portalu Marketplace | Dokumentacja firmy Microsoft
description: Opis czynności, wymagane zatwierdzone Microsoft developer i utworzyć i wdrożyć obraz maszyny wirtualnej, szablonu, Usługa danych lub dewelopera usługi w portalu Azure Marketplace
services: marketplace-publishing
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: 5343bd26-c6e4-4589-85b7-4a2c00bba8ab
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2017
ms.author: mbaldwin
ms.openlocfilehash: 02df78de48c8466f3488ef5f8346e1852b4e3ded
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2018
ms.locfileid: "29943177"
---
> [!NOTE]
> Ta dokumentacja nie jest już aktualny i nie jest dokładne. Zamiast tego, przejdź do portalu Azure Marketplace [przewodnik sprzedawcy](https://docs.microsoft.com/azure/marketplace/seller-guide/cloud-partner-portal-seller-guide) wskazówki dotyczące publikowanie oferty w portalu Azure Marketplace.

# <a name="publish-and-manage-an-offer-in-the-azure-marketplace"></a>Publikowanie i zarządzanie nimi oferty w portalu Azure Marketplace
Aby pomóc deweloperom tworzenie, wdrażanie i zarządzanie ich rozwiązania wyświetlane w portalu Azure Marketplace dla innych klientów platformy Azure i partnerów, zakupu i użycie znajduje się w tym artykule.

## <a name="marketplace-publishing"></a>Publikowanie witryny Marketplace
Jako Azure wydawcy dystrybucji i sprzedawać innowacyjne rozwiązania lub usługi inni deweloperzy ISV i specjalistów IT w witrynie Marketplace. Za pośrednictwem portalu Marketplace można osiągnąć klientów, którzy chcą szybkie opracowywanie aplikacji opartej na chmurze i rozwiązań mobilnych. Jeśli rozwiązania jest przeznaczony dla użytkowników biznesowych, warto rozważyć [AppSource](http://appsource.microsoft.com) marketplace.


## <a name="supported-types-of-solutions"></a>Obsługiwane typy rozwiązań
Pierwsza rzecz, którą chcesz wykonać jako wydawca jest określenie jakiego rodzaju rozwiązania oferty firmy. Witryny Marketplace obsługuje następujące typy ofert:

|Typ rozwiązania|Maszyna wirtualna|Szablon rozwiązania|
|---|---|---|
|**Definicja**|Wstępnie skonfigurowane obrazów za pomocą pełnego zainstalowanego systemu operacyjnego i co najmniej jednej aplikacji. Obraz maszyny wirtualnej zawiera informacje niezbędne do tworzenia i wdrażania maszyn wirtualnych w usłudze maszynach wirtualnych platformy Azure.|Struktura danych, która może odwoływać się co najmniej jednej różne usługi platformy Azure, takich jak usługi opublikowana przez innych sprzedawców. Subskrybenci platformy Azure służy do wdrażania jednego lub więcej ofert w jednej, skoordynowanej sposób.|
|**Przykład**|Jako wydawca Azure utworzeniu i zweryfikować maszynę Wirtualną za pomocą usługi innowacyjnych bazy danych. Inne subskrybenci platformy Azure ma zostać nabywania i wdrażanie tej maszyny Wirtualnej w swoich środowiskach usługi w chmurze.|Jako wydawca Azure zostały połączone zestaw usług z między platformy Azure, która była szybkiego wdrażania usługi w chmurze z równoważeniem obciążenia, zwiększone zabezpieczenia i wysokiej dostępności. Inne subskrybenci platformy Azure, można zaoszczędzić czas, przez szablon rozwiązania, które spełnia ich cel. Pracownicy nie muszą ręcznie zlokalizować, nabywania, wdrażanie i konfigurowanie usług Azure takie same lub podobne.|

> [!NOTE]
> Niektóre kroki są wspólne w różnych rodzajów rozwiązań, a inne są różne do odpowiedniego typu rozwiązania. Ten artykuł zawiera krótki przegląd czynności, które należy wykonać dla każdego typu rozwiązania.

## <a name="publish-a-solution"></a>Publikowanie rozwiązania
![Wyznaczyć, rejestrowanie, Opublikuj](media/marketplace-publishing-getting-started/img01.png)

### <a name="nominate-your-solution-for-pre-approval"></a>Wyznaczyć rozwiązania na zatwierdzenie przed
Aby opublikować maszyny wirtualnej [rozwiązania](https://createopportunity.azurewebsites.net) do witryny Marketplace ukończyć certyfikowane Microsoft Azure **formularza wyznaczenie rozwiązania**.

>[!NOTE]
> Jeśli pracujesz z menedżerem konta partnera lub DX Menedżera partnera, poproś go o wyznaczyć rozwiązania dla programu Azure certyfikowane. Można także przejść do [Microsoft Azure certyfikowane](http://createopportunity.azurewebsites.net) strony sieci Web i wypełnij formularz aplikacji. Wprowadź adres e-mail z menedżerem partnera lub menedżerem partnera DX w **skontaktuj się z Microsoft Sponsor** pole.

Jeśli spełniają kryteria kwalifikujące w [zasady uczestnictwa w portalu Azure Marketplace](http://go.microsoft.com/fwlink/?LinkID=526833) i aplikacja została zatwierdzona, możemy rozpocząć pracę należy do dołączenia rozwiązania do witryny Marketplace.

### <a name="register-your-account-as-a-microsoft-seller"></a>Zarejestruj konto przy użyciu jako sprzedawcy firmy Microsoft
Zarejestruj konto Microsoft jako [konta Microsoft Developer](marketplace-publishing-accounts-creation-registration.md).

### <a name="publish-your-solution"></a>Publikowanie rozwiązania
Aby opublikować rozwiązaniem na rynku, wykonaj następujące kroki:
1. Spełnianie wymagań nietechnicznym.

    a. Spełnienia [nietechnicznym wymagania wstępne](marketplace-publishing-pre-requisites.md).

    b. Spełnienia [techniczne wymagania wstępne maszyny Wirtualnej](marketplace-publishing-vm-image-creation-prerequisites.md).

    c. Spełnienia [wymagania wstępne dotyczące technicznej rozwiązanie szablonu](marketplace-publishing-solution-template-creation-prerequisites.md).

2. Utwórz ofertę.

    a. Utwórz [maszyny wirtualnej](marketplace-publishing-vm-image-creation.md) oferty.

    b. Utwórz [szablon rozwiązania](marketplace-publishing-solution-template-creation.md) oferty.

3. Utwórz ofertę [marketingu zawartości](marketplace-publishing-push-to-staging.md).

4. Przetestuj ofertę tymczasowych.

    a. Testowanie ofertę maszyny Wirtualnej w [przemieszczania](marketplace-publishing-vm-image-test-in-staging.md).

    b. Testowanie ofertę szablon rozwiązania w [przemieszczania](marketplace-publishing-solution-template-test-in-staging.md).

5. Wdrażanie Twojej oferty [Marketplace](marketplace-publishing-push-to-production.md).


### <a name="create-and-manage-a-virtual-machine-image"></a>Tworzenie i zarządzanie nimi obraz maszyny wirtualnej
Tworzenie i zarządzanie nimi obrazu maszyny Wirtualnej przy użyciu tych zasobów:
* Tworzenie obrazu maszyny Wirtualnej [lokalnymi](marketplace-publishing-vm-image-creation-on-premise.md).
* Utwórz maszynę wirtualną działającą [systemu Windows w portalu Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Utwórz maszynę wirtualną działającą [systemu Linux w portalu Azure](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Rozwiązywanie typowych problemów podczas [tworzenia dysku VHD](marketplace-publishing-vm-image-creation-troubleshooting.md).

## <a name="manage-your-solution"></a>Rozwiązania do zarządzania
Zarządzanie za pomocą rozwiązania z następujących zasobów:
* [Przeczytaj przewodnik po produkcyjnych w przypadku ofert maszyny wirtualnej](marketplace-publishing-vm-image-post-publishing.md)
* [Aktualizuj szczegóły nietechnicznym oferty lub wersji](marketplace-publishing-vm-image-post-publishing.md#update-the-nontechnical-details-of-an-offer-or-a-sku)
* [Aktualizuj szczegóły techniczne dotyczące oferty lub wersji](marketplace-publishing-vm-image-post-publishing.md#update-the-technical-details-of-a-sku)
* [Dodaj nowe jednostki SKU w ramach wymienionych oferty](marketplace-publishing-vm-image-post-publishing.md#add-a-new-sku-under-a-listed-offer)
* [Zmień liczbę dysków danych wymienionych jednostki SKU](marketplace-publishing-vm-image-post-publishing.md#change-the-data-disk-count-for-a-listed-sku)
* [Usuń to wymienione oferta z witryny Marketplace](marketplace-publishing-vm-image-post-publishing.md)
* [Usuń wymienionych jednostki SKU z witryny Marketplace](marketplace-publishing-vm-image-post-publishing.md#delete-a-listed-sku-from-the-marketplace)
* [Usuń bieżącej wersji listy jednostki SKU z witryny Marketplace](marketplace-publishing-vm-image-post-publishing.md#delete-the-current-version-of-a-listed-sku-from-the-marketplace)
* [Przywróć cen listę wartości produkcji](marketplace-publishing-vm-image-post-publishing.md#revert-the-listing-price-to-production-values)
* [Przywrócenie modelu rozliczeń do produkcji](marketplace-publishing-vm-image-post-publishing.md#revert-the-billing-model-to-production-values)
* [Przywróć ustawienie widoczności wymienionych SKU wartość produkcji](marketplace-publishing-vm-image-post-publishing.md#revert-the-visibility-setting-of-a-listed-sku-to-the-production-value)

## <a name="additional-resources"></a>Zasoby dodatkowe
[Konfigurowanie programu Azure PowerShell](marketplace-publishing-powershell-setup.md)
