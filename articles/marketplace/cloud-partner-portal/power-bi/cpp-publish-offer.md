---
title: Publikowanie oferty aplikacji Power BI | Portal Azure Marketplace
description: Opublikuj ofertę aplikacja Power BI w portalu marketplace, Microsoft AppSource.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: pabutler
ms.openlocfilehash: aae23feaf1cc5887de061414af985ef16070546b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64943195"
---
# <a name="publish-a-power-bi-app-offer"></a>Publikowanie oferty aplikacji Power BI

Ostatnim krokiem po utworzeniu zdefiniowane oferty w portalu Cloud Partner i utworzyć skojarzone zasoby techniczne, jest przesyłać oferty do opublikowania. Aby rozpocząć ten proces, w lewym okienku **nowa oferta** wybierz **Publikuj**. Aby uzyskać więcej informacji, zobacz [oferty publikowania z portalu Azure Marketplace i AppSource](../manage-offers/cpp-publish-offer.md).


## <a name="publishing-steps"></a>Kroki publikowania

Oto główne kroki w procesie publikowania:

![Kroki procesu publikowania aplikacji Power BI oferują](./media/publishing-process-steps.png)

W tej tabeli opisano poszczególne kroki i zapewnia jego szacowany czas:

|   Krok publikowania            |   Time     |   Opis                                                                  |
| --------------------         |------------| ----------------                                                               |
| Weryfikowanie wymagań wstępnych       | 15 minut     | Oferuje informacje i ofertę, ustawienia zostaną zweryfikowane.                            |
| Certyfikacja                | 1-7 dni   | Zespół certyfikacji usługi Power BI analizuje oferty. Zespół uruchamia aplikację usługi Power BI poprzez testowanie ręcznej weryfikacji przez zainstalowanie aplikacji za pośrednictwem adresu URL podanego instalacji. Podstawowe sprawdzanie poprawności są wykonywane w ramach procesu certyfikacji aplikacji (opisanych w dalszej części tego dokumentu).         |
| Tworzenie pakietów                    | \< 1 godzina  | Zasoby techniczne oferty są pakowane do użytku klienta.                        |
| Generowanie potencjalnych klientów rejestracji | \< 1 godzina  | Systemy potencjalnych klientów są konfigurowane i wdrażane.                                      |
| Przygotowania wydawcy            | \-         | Wykonaj ostateczny Przegląd i potwierdzenie oferty przejdzie na żywo. Konieczne będzie teraz również link do oferty w wersji zapoznawczej. Po zakończeniu wygląd w wersji zapoznawczej, wybierz **Go Live** na **stan** kartę. Spowoduje to wysłanie żądania do zespół ds. dołączania, aby wyświetlić listę aplikacji w usłudze AppSource.    |
| Na żywo                         | \< 3 godziny | Oferty publicznie znajduje się teraz ("na żywo") w usłudze AppSource i klientów można wyświetlić aplikację i wdrożyć ją w swojej subskrypcji usługi Power BI. Otrzymasz również wiadomość e-mail z potwierdzeniem. W prawej kolumnie na **wszystkich ofert** karcie, można wyświetlić stan wszystkich Twoich ofert. Na **stan** kartę, zobaczysz szczegółowe stan przepływu publikowania oferty. |
|   |   |

Zezwalaj na maksymalnie osiem dni na ukończenie tego procesu. Po przejściu przez następujące kroki publikowania oferty aplikacji Power BI zostaną wyświetlone w [AppSource](https://appsource.microsoft.com/marketplace/apps?product=power-bi%20) sekcji aplikacji usługi Power BI.


### <a name="app-certification-process"></a>Proces certyfikacji aplikacji

Zespół ds. dołączania firmy Microsoft używa tego procesu, aby zweryfikować swoje zgłoszenie oferty aplikacji Power BI:

1. Przejrzyj prawne łącza dokumentów oraz pomocy.
2. Sprawdź informacje kontaktowe pomocy technicznej.
3. Użyj adresu URL Instalatora, aby sprawdzić prawidłowej instalacji.
4. Skanowanie aplikacji w poszukiwaniu złośliwego oprogramowania i innych złośliwej zawartości.
5. Sprawdź, czy wyświetlana zawartość odpowiada opis aplikacji.
6. Sprawdź, czy operacje związane z aplikacją działają zgodnie z oczekiwaniami w usłudze Power BI. Zespół otwiera raporty i pulpity nawigacyjne z przykładowymi danymi, nawiązanie połączenia z niestandardowymi źródłami danych, odświeża dane i tak dalej.

Zespół certyfikacji zapewnia informacje zwrotne, jeśli zespół dowiaduje się wszelkie problemy.  Aby uzyskać więcej informacji na temat wymagań dotyczących aplikacji w usłudze Power BI, zobacz [dokumentacji aplikacji usługi Power BI](https://go.microsoft.com/fwlink/?linkid=2028636).


## <a name="next-steps"></a>Kolejne kroki

Firma Microsoft zaleca, regularnie monitorować swoją aplikację w [marketplace usługi AppSource](https://appsource.microsoft.com).  Należy również użyć [Insights sprzedawcy](../../cloud-partner-portal-orig/si-getting-started.md) funkcji [portalu Cloud Partner](https://cloudpartner.azure.com/#insights) uzyskać wgląd w dane dotyczące klientów w portalu marketplace i użycie aplikacji. Na koniec można [zaktualizować swoje oferty](./cpp-update-existing-offer.md).
