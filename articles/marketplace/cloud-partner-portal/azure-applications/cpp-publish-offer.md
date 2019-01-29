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
ms.openlocfilehash: 2adf07cf2337611b9136af47ce6a35b617e2e9ff
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55177036"
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



## <a name="errors-and-review-feedback"></a>Błędy i opinii

Oprócz wyświetlania stanu publikowania oferty, **stan** karta zawiera również komunikaty o błędach i informacje zwrotne z **przeglądu Microsoft** kroku.  Zwykle Przejrzyj problemy są przywoływane jako żądania ściągnięcia (PR).  Każdego żądania Ściągnięcia jest połączony z online programu Visual Studio Team Services (VSTS, zmieniona na [DevOps platformy Azure](https://azure.microsoft.com/services/devops/)) elementu, który zawiera szczegółowe informacje o problemie.  Poniższa ilustracja przedstawia przykład odwołania do przeglądu żądania Ściągnięcia.  Dla bardziej złożonych przypadkach zespoły przeglądu i pomoc techniczna może wiadomość e-mail. 

![Stan karty zawierające opinii](./media/status-tab-ms-review.png)

Opisywane problemy muszą spełnić, przed kontynuowaniem tej oferty, przez proces publikowania.  Na poniższym diagramie przedstawiono, jak ten proces opinii odnosi się do publikowania.

![Kroki publikowania opinii w usłudze VSTS](./media/pub-flow-vsts-access.png)


### <a name="vsts-access"></a>Dostęp do usługi VSTS

Aby wyświetlić elementy usługi VSTS, do którego odwołuje się opinii, wydawcy muszą mieć uprawnienie odpowiednią autoryzacją.  W przeciwnym razie odbieranie nowych wydawców `401 - Not Authorized` strony odpowiedzi.  Aby zażądać dostępu do systemu usług VSTS Przegląd oferty, wykonaj następujące czynności:

1. Zbierz następujące informacje:
    - Twoja nazwa wydawcy i Identyfikatora
    - Oferty (aplikacje platformy Azure), oferują nazwy i Identyfikatora jednostki SKU
    - Żądanie ściągnięcia link na przykład: `https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>`  Ten adres URL można pobrać z komunikatu powiadomienia lub adres strony odpowiedzi 401.
    - Adresy e-mail osób z organizacji publikowania, który chcesz udzielić dostępu do.  Te powinna zawierać adresy właściciela, podanych podczas rejestracji jako wydawcę w portalu Cloud Partner.
2. Utwórz żądanie pomocy technicznej.  Na pasku tytułu w portalu Cloud Partner wybierz **pomocy** przycisk, a następnie wybierz **pomocy technicznej** z menu.  Domyślne internetowej przeglądarki należy uruchomić i przejdź do firmy Microsoft nowe strony pomocy technicznej zdarzenia.  (Może mieć się się zalogować).
3. Określ **typ problemu** jako **przechodzenia do portalu marketplace** i **kategorii** jako **problem z dostępem**, a następnie wybierz **Start żądanie**.

    ![Kategoria bilet pomocy technicznej](./media/support-incident1.png)

4. W **krok 1 z 2** strony, podaj swoje informacje kontaktowe i wybierz **Kontynuuj**.
5. W **krok 2 z 2** Określ tytuł zdarzenia (na przykład `Request VSTS access`) i podaj informacje zebrane w pierwszym kroku (powyżej).  Przeczytaj i zaakceptuj umowę, a następnie wybierz **przesyłania**.

Jeśli tworzenie zdarzenia zakończyło się pomyślnie, zostanie wyświetlona strona potwierdzenia.  Zapisz informacje o potwierdzenie dla Twojej informacji.  Pomoc techniczna firmy Microsoft należy odpowiedzieć na Twoje żądanie dostępu w ciągu kilku dni roboczych.


## <a name="next-steps"></a>Kolejne kroki

Po opublikowaniu aplikacji platformy Azure możesz [aktualizowanie istniejącej oferty](./cpp-update-existing-offer.md) aby odzwierciedlić zmiany wymagania biznesowe lub techniczne. 
