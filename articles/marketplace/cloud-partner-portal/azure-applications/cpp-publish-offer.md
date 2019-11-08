---
title: Publikowanie oferty aplikacji platformy Azure | Portal Azure Marketplace
description: W tym artykule opisano proces i kroki publikowania oferty aplikacji platformy Azure w portalu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: pabutler
ms.openlocfilehash: 0005760a16f5109ca3555df5c5c5137facc84c40
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826158"
---
# <a name="publish-azure-application-offer"></a>Publikowanie oferty aplikacji platformy Azure

Po utworzeniu oferty przez udostępnienie informacji na **nowej stronie oferty** można opublikować tę ofertę. Wybierz pozycję **Publikuj** , aby rozpocząć proces publikowania.

Na poniższym diagramie przedstawiono główne kroki w procesie publikowania dla oferty "go Live".

![Oferuj kroki publikowania](./media/offer-publishing-steps.png)


## <a name="detailed-description-of-publishing-steps"></a>Szczegółowy opis kroków publikowania

W poniższej tabeli wymieniono i opisano każdy krok publikowania i przedstawiono oszacowanie czasu na ukończenie każdego kroku.  Czasy szacowania w ciągu "dni" są definiowane jako dni robocze, które nie wykluczają weekendów i dni wolnych od pracy.

|  **Etap publikowania**           | **Pierwszym**    | **Opis**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Sprawdzanie wymagań wstępnych         | < 15 min    | Informacje o ofercie i ustawienia oferty są weryfikowane.                        |
| Sprawdź poprawność ustawień przychodu | < 15 min  | Dla oferty jest sprawdzane przypisanie użycia zasobów platformy Azure.             |
| Certyfikacja                  | < 1 dzień     | Oferta jest analizowana przez zespół certyfikacji platformy Azure. Oferta jest skanowana w poszukiwaniu wirusów, złośliwego oprogramowania, zgodności z bezpieczeństwem i problemów z zabezpieczeniami. Oferta jest sprawdzana, aby sprawdzić, czy spełnia ona wszystkie kryteria kwalifikacyjne. Aby uzyskać więcej informacji, zobacz [wymagania wstępne](./cpp-prerequisites.md). Opinie są podawane w przypadku znalezienia problemu. |
| Sprawdzanie poprawności dysku testowego          | < 2 godz.   | Obowiązkowe Jeśli jest obecny dysk testowy, firma Microsoft sprawdza, czy można go wdrożyć i zreplikować.  |
| Pakowanie i rejestracja generacji potencjalnego klienta | < 1 godz.  | Zasoby techniczne oferty są pakowane do użytku przez klienta, a systemy liderów zostały skonfigurowane i wdrożone. |
|  Wylogowanie wydawcy             |  Ręcznie    | Końcowe przeglądy i potwierdzenie wydawcy przed rozpoczęciem oferty. Oferta jest teraz dostępna w wersji zapoznawczej.  Ofertę można wdrożyć w wybranych subskrypcjach (w krokach dotyczących informacji o ofercie), aby sprawdzić, czy spełnia ona wszystkie wymagania.  Po sprawdzeniu oferty wybierz pozycję **Przejdź na żywo** , aby Twoja oferta mogła przejść do następnego kroku. |
| Przegląd firmy Microsoft                | 7-14 dni | Firma Microsoft w całości przegląda swoją aplikację platformy Azure i wiadomości e-mail w przypadku odnalezienia problemów.  Ten krok zależy od złożoności aplikacji, nieznanych problemów oraz sposobu, w jaki użytkownik reaguje na nie.  |
| Na żywo                           | < 1 dzień | Oferta jest wydawana, replikowana do określonych regionów i udostępniona publicznie. |
|   |&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|   |

Proces publikowania można monitorować na karcie **stan** oferty w Portal Cloud partner.

![Karta stanu dla oferty aplikacji platformy Azure](./media/offer-status-tab.png)

Po zakończeniu procesu publikowania oferta zostanie wyświetlona w [kategorii aplikacji Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/).

>[!Note]
>Niedostępność kanału partnera dostawcy rozwiązań w chmurze (CSP) jest teraz dostępna.  Zobacz [dostawcy rozwiązań w chmurze](../../cloud-solution-providers.md) , aby uzyskać więcej informacji na temat marketingu oferty przez kanały partnerskie programu Microsoft CSP.

## <a name="errors-and-review-feedback"></a>Błędy i przejrzyj opinię

Oprócz wyświetlania stanu publikacji oferty na karcie **stan** są również wyświetlane komunikaty o błędach i informacje zwrotne z wszelkich kroków publikowania, w których napotkano problem.  Jeśli problem ma krytyczne znaczenie, publikowanie zostało anulowane.  Następnie należy skorygować zgłoszone problemy i ponownie opublikować ofertę.  Ponieważ krok **przeglądu firmy Microsoft** reprezentuje obszerny przegląd oferty i skojarzonych z nią zasobów technicznych (szczególnie szablon Azure Resource Manager), problemy są zwykle przedstawiane jako linki żądania ściągnięcia.  Informacje na temat sposobu wyświetlania tych żądań ściągnięcia i reagowania na nie można znaleźć w temacie [Obsługa informacji zwrotnych](./cpp-handling-review-feedback.md).


## <a name="next-steps"></a>Następne kroki

Jeśli wystąpią błędy w co najmniej jednym z kroków publikacji, należy je poprawić i ponownie opublikować ofertę.  Jeśli w kroku **przeglądu firmy Microsoft** napotkasz problemy krytyczne, musisz [obsłużyć opinię na temat przeglądu](./cpp-handling-review-feedback.md) , uzyskując dostęp do repozytorium Azure DevOps zespołu.

Po pomyślnym opublikowaniu aplikacji platformy Azure możesz [zaktualizować istniejącą ofertę](./cpp-update-existing-offer.md) , aby odzwierciedlić zmieniające się wymagania biznesowe lub techniczne. 
