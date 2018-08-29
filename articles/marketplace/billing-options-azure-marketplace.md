---
title: Opcje rozliczeń w witrynie Azure Marketplace | Azure
description: Opcje rozliczeń w witrynie Azure Marketplace dla wydawców.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: jm-aditi-ms
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 06/05/2018
ms.author: ellacroi
ms.openlocfilehash: 67806563dcd67876d4d922c7f2c76379f7ad7222
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2018
ms.locfileid: "43125517"
---
# <a name="billing-options-in-the-azure-marketplace"></a>Opcje rozliczeń w witrynie Azure Marketplace

W tym artykule opisano opcje rozliczeń, które są dostępne w [portalu Azure Marketplace](https://azuremarketplace.microsoft.com).

## <a name="commercial-considerations-in-the-marketplace"></a>Zagadnienia komercyjnych w portalu Marketplace
Portal Marketplace nie udostępnia przychód w przypadku następujących typów listy: 
*   List
*   Wersja próbna
*   Przeprowadzaj transakcje za pomocą modelu rozliczeniowego Bring Your Own License (BYOL)

Nie ma rozliczane dodatkowe opłaty za udział w sklepów w portalu Marketplace.

Aby uzyskać więcej informacji, zobacz [Microsoft Azure Marketplace — zasady udziału](https://azure.microsoft.com/support/legal/marketplace/participation-policies).  

## <a name="pay-as-you-go-and-byol-billing-options"></a>Płatność za rzeczywiste użycie i rozliczenia opcje BYOL
Jeśli zgodnie z rzeczywistym użyciem model rozliczeń jest używany jako opcja publikowania, na podstawie użycia oprogramowanie przychód zostaną udostępnione w 80/20% podzielony między użytkownikiem a firmą Microsoft. Ceny z jednej oferty zarówno, za pomocą płatność za rzeczywiste użycie i rozliczenia modeli w ramach opcji BYOL. Dwa modele rozliczeniowe współistnieć na tym poziomie ofertę jako osobne jednostki SKU. Należy skonfigurować modelami rozliczeń w swojej oferty w portalu Cloud Partner. 

Należy wziąć pod uwagę następujące przykłady:
*   Jeśli włączona jest opcja płatności, masz następujące wyniki:
    | Koszt licencji | $1.00 za godzinę |
    |:--- |:--- |
    | Koszt użycia platformy Azure (D1/1-rdzeniowe) | 0,14 USD za godzinę |
    | *Rozliczenie przez firmę Microsoft* | *$1.14 za godzinę* |

    W tym scenariuszu firma Microsoft rozlicza 1.14 $ na godzinę w przypadku użycia opublikowanego obrazu maszyny Wirtualnej:
    | W ramach firmy Microsoft | $1.14 za godzinę |
    |:--- |:--- |
    | Microsoft płaci 80% kosztów licencji | $0.80 za godzinę |
    | Firma Microsoft zachowuje 20% przychodów kosztami licencji | 0,20 USD za godzinę |
    | Firma Microsoft zachowuje koszty użycia platformy Azure | 0,14 USD za godzinę |

*   Po włączeniu opcji BYOL, masz następujące wyniki:
    | Koszt licencji | Opłata za licencję negocjowane i rozliczane przez użytkownika |
    |:--- |:--- |
    | Koszt użycia platformy Azure (D1/1-rdzeniowe) | 0,14 USD za godzinę |
    | *Rozliczenie przez firmę Microsoft* | *0,14 USD za godzinę* |

    W tym scenariuszu firma Microsoft rozlicza 0,14 USD za godzinę do użycia opublikowanego obrazu maszyny Wirtualnej: 
    | W ramach firmy Microsoft | 0,14 USD za godzinę |
    |:--- |:--- |
    | Firma Microsoft zachowuje koszty użycia platformy Azure | 0,14 USD za godzinę |
    | Firma Microsoft zachowuje 0% kosztów licencji | 0,00 USD za godzinę |

## <a name="single-billing-and-payment-methods"></a>Pojedynczej metody rozliczeń i płatności
Ważną zaletą używania Transact publikowania opcji w portalu Marketplace jest, czy swoje koszty licencjonowania i użycie platformy Azure są rozliczane pojedynczego bezpośrednio do klienta.

W tym scenariuszu firma Microsoft rozlicza i zbiera w Twoim imieniu. Rozliczenia Microsoft eliminuje konieczność utworzenia relacji zamówienia z klientem. Wspólne rozliczenia może zapewnić oszczędność czasu i zasobów. On również mogą pomóc skupić się na docelowa sprzedaży, a nie na zbieranie na rachunku. 

## <a name="enterprise-agreement"></a>Umowa Enterprise  
Jeśli jesteś klientem Microsoft Enterprise Agreement, można użyć umowy Enterprise Agreement do zapłacenia za produkty firmy Microsoft. Opłata może być produktów, w tym w przypadku użycia platformy Azure. Płatność przy użyciu umowy Enterprise Agreement jest przeznaczona dla organizacji, które mają licencje na oprogramowanie i usługi w chmurze dla co najmniej trzy lata. Można rozłożyć płatności zamiast wywołania jednej płatności z góry. Jeśli korzystasz z płatnością za rzeczywiste użycie opcji publikowania, naliczania opłat za koszty licencjonowania oprogramowania następuje co kwartał nadwyżkowe cyklu rozliczeniowym umowy Enterprise Agreement.  

### <a name="monetary-commitment"></a>Zobowiązanie pieniężne
Jeśli jesteś klientem z umową Enterprise Agreement może dodać do Umowy Azure. Azure należy dodać do Umowy, podejmując zobowiązanie pieniężne na platformie Azure. Zobowiązania pieniężnego jest wykorzystywane przez cały rok. Zobowiązanie obejmuje dowolną kombinację użycie usług systemu Azure.

## <a name="next-steps"></a>Kolejne kroki
Przegląd [Podręcznik publikowania w portalu Azure Marketplace i AppSource](./marketplace-publishers-guide.md).
