---
title: Publikowanie oferty modułów Usługi Azure IoT Edge | Azure Marketplace
description: Jak opublikować ofertę modułu IoT Edge.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 2220352f817b488b3992b88920428063edde006e
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745016"
---
# <a name="publish-iot-edge-module-offer"></a>Publikowanie oferty modułu usługi IoT Edge

>[!Important]
>Od 30 marca 2020 r. rozpoczniemy przenoszenie zarządzania ofertami modułu IoT Edge do Centrum partnerskiego. Po migracji utworzysz oferty i zarządzasz nimi w Centrum partnerów. Postępuj zgodnie z instrukcjami w [tworzenie oferty modułu IoT Edge,](https://aka.ms/AzureCreateIoT) aby zarządzać zmigrowanymi ofertami.

 Po utworzeniu nowej oferty, podając informacje na stronie **Nowa oferta,** możesz opublikować ofertę. Wybierz **pozycję Publikuj,** aby rozpocząć proces publikowania.

Na poniższym diagramie przedstawiono główne kroki w procesie publikowania dla oferty "go live".

![Kroki publikowania dla oferty modułów IoT Edge](./media/iot-edge-module-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>Szczegółowy opis kroków publikowania

W poniższej tabeli opisano każdy krok publikowania, z oszacowaniem czasu (maksymalnie), aby wykonać każdy krok.
<!-- P2: we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **Krok publikowania**           | **Czas**    | **Opis**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Sprawdzanie poprawności wymagań wstępnych         | 15 min.   | Informacje o ofercie i ustawienia oferty są sprawdzane.                        |
| Certyfikacja                  | 2 tygodnie | Oferta jest analizowana przez zespół certyfikacji platformy Azure. Ten krok spowoduje wykonanie skanowania w poszukiwaniu wirusów, złośliwego oprogramowania, zgodności z przepisami bezpieczeństwa i problemów z zabezpieczeniami. Zweryfikuje również, czy ta oferta modułu IoT Edge spełnia wszystkie kryteria kwalifikowalności (patrz [wymagania wstępne](./cpp-prerequisites.md) i [przygotowanie zasobów technicznych).](./cpp-create-technical-assets.md) Informacja zwrotna jest dostarczana w przypadku znalezienia problemu. |
| Tworzenie pakietów | 1 godzina  | Zasoby techniczne oferty są pakowane do użytku klienta, a systemy ołowiane są konfigurowane i konfigurowane. |
|  Znak wydawcy             |  -        | Ostateczna recenzja wydawcy i potwierdzenie przed rozpoczęciem oferty. Możesz wdrożyć ofertę w wybranych subskrypcjach (w krokach informacji o ofercie), aby sprawdzić, czy spełnia ona wszystkie twoje wymagania.  Wybierz **opcję Przejdź na żywo,** aby Twoja oferta mogła przejść do następnego kroku. |
| Tworzenie pakietów                 | 1 godzina | Sfinalizowana oferta jest replikowana w systemach produkcyjnych i regionach marketplace. | 
| Na żywo                           | 4 dni |Oferta jest zwalniana, replikowana do wymaganych regionów i udostępniana publicznie. |

Poczekaj do 10 dni roboczych na zakończenie procesu publikowania i wydanie oferty. Po zakończeniu procesu publikowania oferta modułu Usługi IoT Edge zostanie wyświetlona w portalu [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).

## <a name="next-steps"></a>Następne kroki

- [Aktualizowanie istniejącej oferty modułów usługi IoT Edge w portalu Azure Marketplace](./cpp-update-existing-offer.md)
