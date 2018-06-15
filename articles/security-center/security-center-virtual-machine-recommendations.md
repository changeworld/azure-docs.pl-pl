---
title: Ochrona maszyn wirtualnych w Centrum zabezpieczeń Azure | Dokumentacja firmy Microsoft
description: Adresy tego dokumentu, zalecenia w Centrum zabezpieczeń Azure, które ułatwiają ochronę maszyn wirtualnych i pozostać zgodnie z zasadami zabezpieczeń.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2018
ms.author: terrylan
ms.openlocfilehash: 54375f6f98b4989a7af8bcde649d967f77c6c862
ms.sourcegitcommit: 719dd33d18cc25c719572cd67e4e6bce29b1d6e7
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2018
ms.locfileid: "27623502"
---
# <a name="protecting-your-virtual-machines-in-azure-security-center"></a>Ochrona maszyn wirtualnych w Centrum zabezpieczeń Azure
Centrum zabezpieczeń Azure analizuje stan zabezpieczeń zasobów platformy Azure. Jeśli Centrum zabezpieczeń zostanie zidentyfikowana potencjalnych luk w zabezpieczeniach, tworzy zaleceń, które przeprowadzają użytkownika przez proces konfigurowania wymaganych elementów sterujących.  Zalecenia dotyczą typów zasobów platformy Azure: maszynach wirtualnych (VM), sieci, SQL i aplikacji.

W tym artykule opisano zaleceń, które są stosowane do maszyn wirtualnych.  Maszyna wirtualna zaleceń Centrum wokół zbierania danych, stosowanie aktualizacji systemu, inicjowanie obsługi ochrony przed złośliwym kodem, szyfrowania dysków maszyny Wirtualnej i inne.  Użyj poniższej tabeli jako odwołanie, aby lepiej zrozumieć dostępne zalecenia dotyczące maszyny Wirtualnej i co każdej z nich zrobić w przypadku zastosowania.

## <a name="available-vm-recommendations"></a>Dostępne zalecenia dotyczące maszyny Wirtualnej
| Zalecenie | Opis |
| --- | --- |
| [Włącz zbieranie danych dla subskrypcji](security-center-enable-data-collection.md) |Zaleca włączenie zbierania danych w zasadach zabezpieczeń dla każdej subskrypcji i wszystkich maszyn wirtualnych w subskrypcjach. |
| [Włącz szyfrowanie dla konta magazynu Azure](security-center-enable-encryption-for-storage-account.md) | Zaleca się, Włącz szyfrowanie usługi Magazyn Azure dla przechowywanych danych. Szyfrowanie usługi Magazyn (SSE) polega na szyfrowaniu danych podczas ich zapisywania do magazynu Azure i odszyfrowuje przed pobierania. SSE jest obecnie dostępny tylko dla usługi obiektów Blob platformy Azure i może służyć do blokowe i stronicowe obiekty BLOB i uzupełnialnych obiektów blob. Aby dowiedzieć się więcej, zobacz [szyfrowanie usługi Magazyn danych magazynowanych](../storage/common/storage-service-encryption.md).</br>SSE jest obsługiwana tylko na kontach magazynu Menedżera zasobów. Klasycznych kont magazynu nie są obecnie obsługiwane. Aby poznać klasycznego i modeli wdrażania usługi Resource Manager, zobacz [modele wdrażania Azure](../azure-classic-rm.md). |
| [Korygowanie konfiguracji zabezpieczeń](security-center-remediate-os-vulnerabilities.md) |Zaleca, aby wyrównać konfiguracje systemu operacyjnego z zasadami konfiguracji zabezpieczeń, np. nie zezwalaj na zapisywanie haseł. |
| [Zastosuj aktualizacje systemu](security-center-apply-system-updates.md) |Zaleca wdrożenie na maszynach wirtualnych brakujących zabezpieczeń systemu i aktualizacji krytycznych. |
| [Zastosuj Just In Time kontroli dostępu do sieci](security-center-just-in-time.md) | Zaleca się zastosowanie tylko w dostęp maszyny Wirtualnej. Tylko w czasie jest funkcja w wersji zapoznawczej i dostępne w warstwie standardowa Centrum zabezpieczeń. Zobacz [cennik](security-center-pricing.md) Aby dowiedzieć się więcej na temat Centrum zabezpieczeń firmy ceny warstw. |
| [Uruchom ponownie po zaktualizowaniu systemu](security-center-apply-system-updates.md#reboot-after-system-updates) |Zaleca ponowne uruchomienie maszyny wirtualnej w celu ukończenia procesu stosowania aktualizacji systemu. |
| [Zainstaluj punkt końcowy](security-center-install-endpoint-protection.md) |Zaleca aprowizację programów chroniących przed złośliwym oprogramowaniem na maszynach wirtualnych (dotyczy tylko maszyn wirtualnych z systemem Windows). |
| [Włącz agenta maszyny wirtualnej](security-center-enable-vm-agent.md) |Umożliwia zobaczenie, które maszyny wirtualne wymagają agenta maszyny wirtualnej. Agent maszyny wirtualnej musi być zainstalowany na maszynach wirtualnych, aby zapewnić aprowizację skanowania w poszukiwaniu poprawek, skanowania linii bazowej i programów chroniących przed złośliwym oprogramowaniem. Agent maszyny wirtualnej jest instalowany domyślnie w przypadku maszyn wirtualnych wdrażanych z poziomu portalu Azure Marketplace. Informacje na temat instalowania agenta maszyny wirtualnej można znaleźć w artykule [Agent maszyny wirtualnej i rozszerzenia — część 2](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/). |
| [Zastosuj szyfrowanie dysków](security-center-apply-disk-encryption.md) |Zaleca szyfrowanie dysków maszyny wirtualnej przy użyciu usługi Azure Disk Encryption (maszyny wirtualne z systemami Windows i Linux). Szyfrowanie jest zalecane zarówno w przypadku woluminów z systemem operacyjnym, jak i z danymi maszyny wirtualnej. |
| [Aktualizacja wersji systemu operacyjnego](security-center-update-os-version.md) |Zaleca się, zaktualizuj wersję systemu operacyjnego (OS) dla usługi w chmurze do najnowszej dostępnej wersji do Twojej rodziny systemów operacyjnych.  Aby dowiedzieć się więcej na temat usługi w chmurze, zobacz [Omówienie usługi w chmurze](../cloud-services/cloud-services-choose-me.md). |
| [Funkcja oceny luk w zabezpieczeniach nie jest zainstalowana](security-center-vulnerability-assessment-recommendations.md) |Zaleca się zainstalowanie na maszynie wirtualnej rozwiązania do oceny luk w zabezpieczeniach. |
| [Korygowanie luk w zabezpieczeniach](security-center-vulnerability-assessment-recommendations.md#review-the-recommendation) |Umożliwia wyświetlenie luk w zabezpieczeniach systemu i aplikacji wykrytych przez rozwiązanie do oceny luk w zabezpieczeniach zainstalowane na maszynie wirtualnej. |

## <a name="see-also"></a>Zobacz także
Aby dowiedzieć się więcej na temat zaleceń, które są stosowane do innych typów zasobów platformy Azure, zobacz następujące tematy:

* [Ochrona aplikacji w Centrum zabezpieczeń Azure](security-center-application-recommendations.md)
* [Ochrona sieci w Centrum zabezpieczeń Azure](security-center-network-recommendations.md)
* [Ochrona usługi Azure SQL w Centrum zabezpieczeń Azure](security-center-sql-service-recommendations.md)

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](security-center-policies.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
