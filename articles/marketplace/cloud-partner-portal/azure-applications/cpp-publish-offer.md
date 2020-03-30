---
title: Publikowanie oferty aplikacji platformy Azure | Azure Marketplace
description: W tym artykule opisano proces i kroki publikowania oferty aplikacji platformy Azure w portalu Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: dsindona
ms.openlocfilehash: 9f89e31c2d17ef74971d2057ba58e9572e92184c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280103"
---
# <a name="publish-azure-application-offer"></a>Publikowanie oferty aplikacji platformy Azure

Po utworzeniu oferty, podając informacje na stronie **Nowa oferta,** możesz opublikować ofertę. Wybierz **pozycję Publikuj,** aby rozpocząć proces publikowania.

Na poniższym diagramie przedstawiono główne kroki w procesie publikowania dla oferty "go live".

![Zaoferuj kroki publikowania](./media/offer-publishing-steps.png)


## <a name="detailed-description-of-publishing-steps"></a>Szczegółowy opis kroków publikowania

W poniższej tabeli wymieniono i opisano każdy krok publikowania i zawiera oszacowanie czasu, aby wykonać każdy krok.  Szacowane godziny w "dniach" są definiowane jako dni robocze, które nie obejmują weekendów i świąt.

|  **Krok publikowania**           | **Czas**    | **Opis**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Sprawdzanie poprawności wymagań wstępnych         | < 15 min.    | Informacje o ofercie i ustawienia oferty są sprawdzane.                        |
| Sprawdzanie poprawności ustawień przychodów, na które wpływ miało wpływ | < 15 min.  | Atrybucja użycia zasobów platformy Azure dla oferty jest sprawdzana.             |
| Certyfikacja                  | < 1 dzień     | Oferta jest analizowana przez zespół certyfikacji platformy Azure. Oferta jest skanowana pod kątem wirusów, złośliwego oprogramowania, zgodności z zasadami bezpieczeństwa i problemów z zabezpieczeniami. Oferta jest sprawdzana, czy spełnia wszystkie kryteria kwalifikacyjne. Aby uzyskać więcej informacji, zobacz [wymagania wstępne](./cpp-prerequisites.md). Informacja zwrotna jest dostarczana w przypadku znalezienia problemu. |
| Sprawdzanie poprawności dysku testowego          | < 2 godziny   | (Opcjonalnie) Jeśli dysk testowy jest obecny, firma Microsoft sprawdza, czy można go wdrożyć i zreplikować.  |
| Rejestracja opakowań i promiliacji ołowiu | < 1 godz.  | Zasoby techniczne oferty są pakowane do użytku klienta, a systemy ołowiane są konfigurowane i wdrażane. |
|  Rejestracja wydawcy             |  Ręcznie    | Ostateczna recenzja wydawcy i potwierdzenie przed rozpoczęciem oferty. Oferta jest już dostępna w wersji zapoznawczej.  Możesz wdrożyć ofertę w wybranych subskrypcjach (w krokach informacji o ofercie), aby sprawdzić, czy spełnia ona wszystkie twoje wymagania.  Po zweryfikowaniu oferty wybierz **opcję Przejdź na żywo,** aby Twoja oferta mogła przejść do następnego kroku. |
| Przegląd firmy Microsoft                | 7 - 14 dni | Firma Microsoft całościowo przegląda aplikację platformy Azure i wysyła wiadomości e-mail w przypadku wykrycia problemów.  Długość tego kroku zależy od złożoności aplikacji, problemów odkrytych i jak szybko na nie reagujesz.  |
| Na żywo                           | < 1 dzień | Oferta jest zwalniana, replikowana do określonych regionów i udostępniana publicznie. |
|   |&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|   |

Proces publikowania można monitorować na karcie **Stan** oferty w portalu cloud partnerów.

![Karta Stan oferty aplikacji platformy Azure](./media/offer-status-tab.png)

Po zakończeniu procesu publikowania oferta zostanie wyświetlona w [kategorii aplikacji microsoft azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/).

>[!Note]
>Usługa wyboru kanału partnerskiego dostawców rozwiązań w chmurze (CSP) jest już dostępna.  Zobacz [dostawców rozwiązań w chmurze, aby](../../cloud-solution-providers.md) uzyskać więcej informacji na temat marketingu oferty za pośrednictwem kanałów partnerów CSP firmy Microsoft.

## <a name="errors-and-review-feedback"></a>Błędy i opinie

Oprócz wyświetlania stanu publikowania oferty, na karcie **Stan** są również wyświetlane komunikaty o błędach i opinie z wszelkich kroków publikowania, w których występuje problem.  Jeśli problem jest krytyczny, publikowanie zostanie anulowane.  Następnie należy rozwiązać zgłoszone problemy i ponownie opublikować ofertę.  Ponieważ krok **przeglądu firmy Microsoft** reprezentuje obszerny przegląd oferty i skojarzonych z nią zasobów technicznych (zwłaszcza szablonu usługi Azure Resource Manager), problemy są zazwyczaj przedstawiane jako łącza żądania ściągnięcia (PR).  Szczegółowe informacje na temat wyświetlania tych analiz danych i reagowania na nie można znaleźć w części [Obsługa opinii dotyczących recenzji.](./cpp-handling-review-feedback.md)


## <a name="next-steps"></a>Następne kroki

Jeśli napotkano błędy w co najmniej jednym z kroków publikowania, należy je poprawić i ponownie opublikować ofertę.  Jeśli w kroku przeglądu **firmy Microsoft** wystąpią krytyczne problemy, należy [obsłużyć opinię o recenzji,](./cpp-handling-review-feedback.md) uzyskując dostęp do repozytorium Azure DevOps zespołu recenzentów firmy Microsoft.

Po pomyślnym opublikowaniu aplikacji platformy Azure można [zaktualizować istniejącą ofertę,](./cpp-update-existing-offer.md) aby odzwierciedlić zmieniające się wymagania biznesowe lub techniczne. 
