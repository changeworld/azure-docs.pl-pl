---
title: Publikowanie aplikacji platformy Azure — oferty w portalu Azure Marketplace | Dokumentacja firmy Microsoft
description: W tym artykule opisano proces i kroki programu stawiane ofertom aplikacji platformy Azure w portalu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: pbutlerm
ms.openlocfilehash: fdca47877d4cb2192eef0a26448cd21e8afe4b77
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60740253"
---
# <a name="publish-azure-application-offer"></a>Publikowanie oferty aplikacji platformy Azure

Po utworzeniu oferty, podając informacje o **nowa oferta** strony, możesz opublikować ofertę. Wybierz **Publikuj** aby rozpocząć proces publikowania.

Na poniższym diagramie przedstawiono główne kroki w procesie publikowania w celu skorzystania z oferty "Przejdź na żywo".

![Kroki publikowania oferty](./media/offer-publishing-steps.png)


## <a name="detailed-description-of-publishing-steps"></a>Szczegółowy opis kroków publikowania

Poniższej tabeli wymieniono i opisano poszczególne kroki publikowania i zapewnia szacowany czas wykonania poszczególnych kroków.  Czas szacowania w "dni" są zdefiniowane jako dni roboczych, których wykluczone podczas weekendów i dni wolnych od pracy.

|  **Krok publikowania**           | **czas**    | **Opis**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Weryfikowanie wymagań wstępnych         | < 15 min    | Oferuje informacje i ofertę, ustawienia zostaną zweryfikowane.                        |
| Sprawdź poprawność ustawień ich przychód | < 15 min  | Zaznaczono autorstwa użycia zasobów platformy Azure dla tej oferty.             |
| Certyfikacja                  | < 1 dzień     | Oferty są analizowane przez zespół certyfikacji platformy Azure. Oferta jest skanowany w poszukiwaniu wirusów, złośliwego oprogramowania, bezpieczeństwo, zgodność i problemy z zabezpieczeniami. Oferta jest sprawdzenie spełnia kryteria kwalifikacyjne. Aby uzyskać więcej informacji, zobacz [wymagania wstępne](./cpp-prerequisites.md). Ile opinii jest, że problem zostanie znaleziony. |
| Testowanie poprawności dysku          | < 2 godziny   | (Opcjonalnie) Jeśli wersji testowej jest obecny, Microsoft weryfikuje, że można go wdrożyć i replikowane.  |
| Tworzenie pakietów i rejestracji generowania potencjalnych klientów | < 1 godz.  | Zasoby techniczne oferty są pakowane do użytku klienta i systemy potencjalnych klientów są konfigurowane i wdrażane. |
|  Podpisywania wydawcy             |  Ręcznie    | Wydawcy ostateczny Przegląd i potwierdzenie oferty przejdzie na żywo. Oferta jest teraz dostępna w wersji zapoznawczej.  Sprawdź, czy spełnia wszystkie wymagania dotyczące można wdrożyć swoją ofertę w wybranych subskrypcjach (w krokach informacji oferty).  Po upewnieniu się, oferty, wybierz **Go Live** dzięki ofercie przejściem do następnego kroku. |
| Przegląd firmy Microsoft                | 7 — 14 dni | Firma Microsoft całościowo przeglądy aplikacji systemu Azure i wiadomości e-mail, jeśli zostaną wykryte problemy.  Długość tego kroku zależy od złożoności aplikacji problemów niewykrytych i jak szybko możesz Reaguj na nie.  |
| Na żywo                           | < 1 dzień | Oferta jest zwolniony, replikowane do określonych regionów i udostępnione publicznie. |
|   |&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|   |

Możesz monitorować proces publikowania w **stan** karcie oferty w portalu Cloud Partner.

![Karta Stan dla oferty aplikacji platformy Azure](./media/offer-status-tab.png)

Po zakończeniu procesu publikowania, oferty, zostaną wyświetlone w [kategorii aplikacji Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/).

>[!Note]
>Dostawcy rozwiązań (CSP) partnera kanału zoptymalizowany pod kątem w chmurze jest teraz dostępna.  Zobacz [dostawców rozwiązań w chmurze](../../cloud-solution-providers.md) więcej informacji na temat marketingowych oferty za pośrednictwem programu Microsoft CSP partner kanałów.

## <a name="errors-and-review-feedback"></a>Błędy i opinii

Oprócz wyświetlania stanu publikowania oferty, **stan** karta zawiera również komunikaty o błędach i informacje zwrotne z wszystkie kroki publikowania, gdzie występuje problem.  Jeśli ten problem ma kluczowe znaczenie, publikując zostanie anulowane.  Następnie należy rozwiązać zgłoszone problemy i ponownie opublikować ofertę.  Ponieważ **przeglądu Microsoft** kroku reprezentuje przeglądu rozbudowane oferty i jego skojarzone zasoby techniczne (szczególnie szablonu usługi Azure Resource Manager), problemy zwykle są uporządkowane jako łącza (PR) żądanie ściągnięcia.  Wyjaśnienie, jak i Odpowiedz na te żądania ściągnięcia, zobacz [obsługi opinii](./cpp-handling-review-feedback.md).


## <a name="next-steps"></a>Kolejne kroki

Napotkano błędy w co najmniej jednej publikacji kroków należy poprawić je i ponownie opublikować ofertę.  Jeśli nie zostaną napotkane problemy krytyczne w **przeglądu Microsoft** kroku, należy najpierw [obsługi opinii](./cpp-handling-review-feedback.md) , uzyskując dostęp do firmy Microsoft zapoznaj się z repozytorium DevOps platformy Azure dla zespołu.

Po pomyślnym opublikowaniu aplikacji platformy Azure, możesz [aktualizowanie istniejącej oferty](./cpp-update-existing-offer.md) aby odzwierciedlić zmiany wymagania biznesowe lub techniczne. 
