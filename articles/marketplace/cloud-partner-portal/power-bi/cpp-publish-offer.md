---
title: Publikowanie aplikacji Power BI — oferty w portalu Azure Marketplace | Dokumentacja firmy Microsoft
description: Publikowanie oferty aplikacji Power BI w witrynie Marketplace AppSource firmy Microsoft.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: pbutlerm
ms.openlocfilehash: 2b3783060cf5502076ce3bc98cf07f005366a9e2
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2019
ms.locfileid: "55666716"
---
# <a name="publish-power-bi-app-offer"></a>Publikowanie oferty aplikacji Power BI

Ostatnim krokiem po zdefiniowane oferty w portalu i utworzyć skojarzone zasoby techniczne, jest przesyłać oferty do opublikowania.  Aby rozpocząć ten proces, kliknij przycisk **Publikuj** przycisk menu pionowy w **nowa oferta** okna.  Aby uzyskać więcej informacji, zobacz [oferty publikowania z portalu Azure Marketplace i AppSource](../manage-offers/cpp-publish-offer.md).


## <a name="publishing-steps"></a>Kroki publikowania

Poniższy diagram przedstawia podstawowe kroki w procesie publikowania "Przejdź na żywo".

![Kroki procesu publikowania aplikacji Power BI](./media/publishing-process-steps.png)

Poniższej tabeli opisano te kroki i udostępnia Szacowanie maksymalny czas na ich zakończenie:

|   Krok publikowania            |   Time     |   Opis                                                                  |
| --------------------         |------------| ----------------                                                               |
| Weryfikowanie wymagań wstępnych       | 15 min     | Oferuje informacje i ofertę, ustawienia zostaną zweryfikowane.                            |
| Certyfikacja                | 1-7 dni   | Zespół certyfikacji usługi Power BI analizuje oferty. Przeprowadzamy przez zainstalowanie aplikacji za pośrednictwem adresu URL podanego instalacji aplikacji usługi Power BI poprzez testowanie ręcznej weryfikacji. Główne operacji sprawdzania poprawności są wykonywane w ramach procesu certyfikacji aplikacji; Zobacz poniżej.         |
| Pakowanie                    | \< 1 godzina  | Zasoby techniczne oferty są pakowane do użytku klienta.                        |
| Generowanie potencjalnych klientów rejestracji | \< 1 godzina  | Systemy potencjalnych klientów są konfigurowane i wdrażane.                                      |
| Przygotowania wydawcy            | \-         | Wydawcy ostateczny Przegląd i potwierdzenie oferty przejdzie na żywo. Konieczne będzie teraz również link do oferty w wersji zapoznawczej. Gdy jesteś zadowolony z wygląd w wersji zapoznawczej, kliknij przycisk **Go Live** znajdujący się w **stan** kartę. Ta akcja spowoduje wysłanie żądania zespół ds. dołączania, aby wyświetlić listę aplikacji w usłudze AppSource.    |
| Na żywo                         | \< 3 godziny | Oferty publicznie znajduje się teraz ("na żywo") w usłudze AppSource, a klienci będą mogli przeglądać i wdrażanie aplikacji w ramach ich subskrypcji usługi Power BI. Otrzymasz również wiadomość e-mail z potwierdzeniem. W dowolnym momencie możesz kliknąć **wszystkich ofert** kartę, a następnie zobacz stan dla wszystkich Twoich ofert na liście w prawej kolumnie. Możesz kliknąć **stan** kartę, aby zobaczyć stan przepływu publikowania szczegóły oferty. |
|   |   |

Zezwala na maksymalnie osiem dni na ukończenie tego procesu. Po przejściu przez następujące kroki publikowania oferty aplikacji Power BI zostaną wyświetlone w [AppSource](https://appsource.microsoft.com/marketplace/apps?product=power-bi%20) sekcji aplikacji usługi Power BI.


### <a name="app-certification-process"></a>Proces certyfikacji aplikacji

Zespół ds. dołączania firmy Microsoft przystępuje do następującej procedury, aby zweryfikować swoje zgłoszenie oferty usługi Power BI:

1. Prawne łącza dokumentów oraz pomocy są przeglądane.
2. Informacje kontaktowe pomocy technicznej jest weryfikowana.
3. Instalator adres URL jest używany do upewnij się. 
4. Aplikacja jest skanowany w poszukiwaniu złośliwego oprogramowania i innych złośliwej zawartości. 
5. Weryfikacja odbywa się tę zawartość, wyświetlany jest zgodny z parametrem opis aplikacji.
6. Operacje związane z aplikacją działają zgodnie z oczekiwaniami w usłudze Power BI: Otwórz raporty i pulpity nawigacyjne z przykładowymi danymi, nawiązać połączenie z niestandardowymi źródłami danych, odświeżanie itp.

Zespół certyfikacji zapewnia informacje zwrotne, jeśli zespół dowiaduje się wszelkie problemy.  Aby uzyskać więcej informacji na temat wymagań aplikacji Power BI, zobacz [dokumentacji aplikacji Power BI](https://go.microsoft.com/fwlink/?linkid=2028636).


## <a name="next-steps"></a>Kolejne kroki

Firma Microsoft zaleca, regularnie monitorować swoją aplikację w [Marketplace usługi AppSource](https://appsource.microsoft.com).  Ponadto należy używać [Insights sprzedawcy](../../cloud-partner-portal-orig/si-getting-started.md) funkcji [portalu Cloud Partner](https://cloudpartner.azure.com/#insights) udostępnienie szczegółowych informacji w portalu marketplace klienci i użycia.  Można również wykonywać pewne [aktualizacje oferty](./cpp-update-existing-offer.md).
