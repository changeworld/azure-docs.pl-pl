---
title: Niestandardowe poddomeny
titleSuffix: Azure Cognitive Services
description: Niestandardowe nazwy poddomen dla każdego zasobu usługi Cognitive Service są tworzone za pośrednictwem witryny Azure portal, usługi Azure Cloud Shell lub interfejsu wiersza polecenia platformy Azure.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: erhopf
ms.openlocfilehash: 108e5ebfa34db711af4ff33cbf4fbba897b8947d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647692"
---
# <a name="custom-subdomain-names-for-cognitive-services"></a>Niestandardowe nazwy poddomen dla usług Cognitive Services

Usługi Azure Cognitive Services używają niestandardowych nazw poddomen dla każdego zasobu utworzonego za pośrednictwem [portalu Azure,](https://portal.azure.com) [usługi Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)lub interfejsu [wiersza polecenia platformy Azure.](https://docs.microsoft.com/cli/azure/install-azure-cli) W przeciwieństwie do regionalnych punktów końcowych, które były wspólne dla wszystkich klientów w określonym regionie platformy Azure, niestandardowe nazwy poddomen są unikatowe dla zasobu. Niestandardowe nazwy poddomen są wymagane, aby włączyć funkcje, takie jak usługa Azure Active Directory (Azure AD) do uwierzytelniania.

## <a name="how-does-this-impact-existing-resources"></a>Jak wpływa to na istniejące zasoby?

Zasoby usług Cognitive Services utworzone przed 1 lipca 2019 r. będą używać regionalnych punktów końcowych dla skojarzonej usługi. Te punkty końcowe będą działać z istniejącymi i nowymi zasobami.

Jeśli chcesz przeprowadzić migrację istniejącego zasobu w celu wykorzystania niestandardowych nazw poddomen, aby włączyć funkcje, takie jak usługa Azure AD, wykonaj następujące instrukcje:

1. Zaloguj się do witryny Azure portal i znajdź zasób usług Cognitive Services, do którego chcesz dodać niestandardową nazwę subdomeny.
2. W **bloku Przegląd** znajdź i wybierz pozycję **Generuj niestandardową nazwę domeny**.
3. Spowoduje to otwarcie panelu z instrukcjami tworzenia unikatowej niestandardowej poddomeny zasobu.
   > [!WARNING]
   > Po utworzeniu niestandardowej nazwy poddomeny **nie można** jej zmienić.

## <a name="do-i-need-to-update-my-existing-resources"></a>Czy muszę zaktualizować istniejące zasoby?

Nie. Regionalny punkt końcowy będzie nadal działać dla nowych i istniejących usług Cognitive Services, a niestandardowa nazwa poddomeny jest opcjonalna. Nawet jeśli zostanie dodana niestandardowa nazwa poddomeny, regionalny punkt końcowy będzie nadal działał z zasobem.

## <a name="what-if-an-sdk-asks-me-for-the-region-for-a-resource"></a>Co zrobić, jeśli SDK prosi mnie o region dla zasobu?

> [!WARNING]
> Usługi mowy **nie** obsługują obecnie niestandardowych poddomen. Użyj regionalnych punktów końcowych podczas korzystania z usług mowy i skojarzonych sks.

Regionalne punkty końcowe i niestandardowe nazwy poddomen są obsługiwane i mogą być używane zamiennie. Jednak pełny punkt końcowy jest wymagany.

Informacje o regionie są dostępne w **bloku Przegląd** zasobu w [witrynie Azure portal](https://portal.azure.com). Aby uzyskać pełną listę regionalnych punktów końcowych, zobacz [Czy istnieje lista regionalnych punktów końcowych?](#is-there-a-list-of-regional-endpoints)

## <a name="are-custom-subdomain-names-regional"></a>Czy niestandardowe nazwy poddomen są regionalne?

Tak. Użycie niestandardowej nazwy poddomeny nie zmienia żadnych regionalnych aspektów zasobu usług Cognitive Services.

## <a name="what-are-the-requirements-for-a-custom-subdomain-name"></a>Jakie są wymagania dotyczące niestandardowej nazwy poddomeny?

Niestandardowa nazwa poddomeny jest unikatowa dla zasobu. Nazwa może zawierać tylko znaki alfanumeryczne i `-` znak; musi mieć długość od 2 do 64 znaków `-`i nie może kończyć się literą .

## <a name="can-i-change-a-custom-domain-name"></a>Czy mogę zmienić niestandardową nazwę domeny?

Nie. Po utworzeniu niestandardowej nazwy poddomeny i skojarzenie z zasobem nie można jej zmienić.

## <a name="can-i-reuse-a-custom-domain-name"></a>Czy mogę ponownie użyć niestandardowej nazwy domeny?

Każda niestandardowa nazwa poddomeny jest unikatowa, więc aby ponownie użyć niestandardowej nazwy poddomeny przypisanej do zasobu usług Cognitive Services, należy usunąć istniejący zasób. Po usunięciu zasobu można ponownie użyć niestandardowej nazwy poddomeny.

## <a name="is-there-a-list-of-regional-endpoints"></a>Czy istnieje lista regionalnych punktów końcowych?

Tak. Jest to lista regionalnych punktów końcowych, których można używać z zasobami usługi Azure Cognitive Services.

> [!NOTE]
> Interfejs API tekstu tłumacza i interfejsy API wyszukiwania Bing używają globalnych punktów końcowych.

| Typ punktu końcowego | Region | Endpoint |
|---------------|--------|----------|
| Public | Globalny (Tekst tłumacza & Bing) | `https://api.cognitive.microsoft.com` |
| | Australia Wschodnia | `https://australiaeast.api.cognitive.microsoft.com` |
| | Brazylia Południowa | `https://brazilsouth.api.cognitive.microsoft.com` |
| | Kanada Środkowa | `https://canadacentral.api.cognitive.microsoft.com` |
| | Środkowe stany USA | `https://centralus.api.cognitive.microsoft.com` |
| | Azja Wschodnia | `https://eastasia.api.cognitive.microsoft.com` |
| | Wschodnie stany USA | `https://eastus.api.cognitive.microsoft.com` |
| | Wschodnie stany USA 2 | `https://eastus2.api.cognitive.microsoft.com` |
| | Francja Środkowa | `https://francecentral.api.cognitive.microsoft.com` |
| | Indie Środkowe | `https://centralindia.api.cognitive.microsoft.com` |
| | Japonia Wschodnia | `https://japaneast.api.cognitive.microsoft.com` |
| | Korea Środkowa | `https://koreacentral.api.cognitive.microsoft.com` |
| | Północno-środkowe stany USA | `https://northcentralus.api.cognitive.microsoft.com` |
| | Europa Północna | `https://northeurope.api.cognitive.microsoft.com` |
| | Republika Południowej Afryki Północ | `https://southafricanorth.api.cognitive.microsoft.com` |
| | Południowo-środkowe stany USA | `https://southcentralus.api.cognitive.microsoft.com` |
| | Azja Południowo-Wschodnia | `https://southeastasia.api.cognitive.microsoft.com` |
| | Południowe Zjednoczone Królestwo | `https://uksouth.api.cognitive.microsoft.com` |
| | Zachodnio-środkowe stany USA | `https://westcentralus.api.cognitive.microsoft.com` |
| | Europa Zachodnia | `https://westeurope.api.cognitive.microsoft.com` |
| | Zachodnie stany USA | `https://westus.api.cognitive.microsoft.com` |
| | Zachodnie stany USA 2 | `https://westus2.api.cognitive.microsoft.com` |
| US Gov | US Gov Wirginia | `https://virginia.api.cognitive.microsoft.us` |
| Chiny | Chiny Wschodnie 2 | `https://chinaeast2.api.cognitive.azure.cn` |
| | Chiny Północne | `https://chinanorth.api.cognitive.azure.cn` |

## <a name="see-also"></a>Zobacz też

* [Co to są usługi Cognitive Services?](Welcome.md)
* [Uwierzytelnianie](authentication.md)
