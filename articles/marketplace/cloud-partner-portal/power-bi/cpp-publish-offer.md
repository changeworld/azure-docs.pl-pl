---
title: Publikowanie oferty aplikacji Power BI | Azure Marketplace
description: Publikowanie oferty aplikacji usługi Power BI w portalu Microsoft AppSource marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: d52270e623a713d7188ac5ff907ebb82ada13283
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745603"
---
# <a name="publish-a-power-bi-app-offer"></a>Publikowanie oferty aplikacji Usługi Power BI

>[!Important]
>Od 30 marca 2020 r. rozpoczniemy przenoszenie zarządzania ofertami aplikacji Usługi Power BI do Centrum partnerów. Po migracji utworzysz oferty i zarządzasz nimi w Centrum partnerów. Postępuj zgodnie z instrukcjami zawartymi w [omówienie tworzenia aplikacji usługi Power BI,](https://aka.ms/AzureCreatePBIServiceApp) aby zarządzać zmigrowanymi ofertami.

Ostatnim krokiem po zdefiniowaniu oferty w portalu cloud partner i utworzeniu skojarzonych zasobów technicznych jest przesłanie oferty do publikacji. Aby rozpocząć ten proces, w lewym okienku okna **Nowa oferta** wybierz pozycję **Publikuj**. Aby uzyskać więcej informacji, zobacz [Publikowanie ofert portalu Azure Marketplace i AppSource](../manage-offers/cpp-publish-offer.md).


## <a name="publishing-steps"></a>Kroki publikowania

Oto główne kroki procesu publikowania:

![Kroki procesu publikowania dla oferty aplikacji Power BI](./media/publishing-process-steps.png)

W tej tabeli opisano każdy krok i przedstawiono szacowany czas ukończenia:

|   Krok publikowania            |   Time     |   Opis                                                                  |
| --------------------         |------------| ----------------                                                               |
| Sprawdzanie poprawności wymagań wstępnych       | 15 minut     | Informacje o ofercie i ustawienia oferty są sprawdzane.                            |
| Certyfikacja                | 1-7 dni   | Zespół certyfikacji usługi Power BI analizuje ofertę. Zespół uruchamia aplikację usługi Power BI za pomocą testu weryfikacji ręcznej, instalując aplikację za pomocą podanego adresu URL instalacji. Podstawowe weryfikacje są wykonywane w ramach procesu certyfikacji aplikacji (opisane w dalszej części tego dokumentu).         |
| Tworzenie pakietów                    | \<1 godz.  | Aktywa techniczne oferty są pakowane do użytku klienta.                        |
| Rejestracja generowania potencjalnych osób | \<1 godz.  | Systemy potencjalnych potencjalnych klient są konfigurowane i wdrażane.                                      |
| Znakowanie wydawcy            | \-         | Przed rozpoczęciem realizacji oferty należy wykonać ostateczną recenzję i potwierdzenie. Teraz będziesz mieć link do podglądu oferty. Po zadowoleniu z wyglądu podglądu wybierz pozycję Przejdź na **żywo** na karcie **Stan.** Spowoduje to wysłanie żądania do zespołu dołączania do listy aplikacji w u źródła aplikacji.    |
| Na żywo                         | \<3 godz. | Twoja oferta jest teraz publicznie wyświetlana ("na żywo") w usłudze AppSource, a klienci mogą wyświetlać aplikację i wdrażać ją w swoich subskrypcjach usługi Power BI. Otrzymasz również wiadomość e-mail z potwierdzeniem. W prawej kolumnie na karcie **Wszystkie oferty** możesz zobaczyć stan wszystkich ofert. Na karcie **Stan** możesz zobaczyć szczegółowy stan przepływu publikowania oferty. |
|   |   |

Poczekaj do ośmiu dni na zakończenie tego procesu. Po przejściu przez te kroki publikowania oferta aplikacji usługi Power BI zostanie wyświetlona w sekcji Aplikacje usługi Power BI [appsource.](https://appsource.microsoft.com/marketplace/apps?product=power-bi%20)


### <a name="app-certification-process"></a>Proces certyfikacji aplikacji

Zespół dołączania firmy Microsoft używa tego procesu do sprawdzania poprawności przesyłania oferty aplikacji Power BI:

1. Przejrzyj dokumenty prawne i linki pomocy.
2. Sprawdź poprawność informacji kontaktowych pomocy technicznej.
3. Użyj adresu URL instalatora, aby zweryfikować prawidłową instalację.
4. Przeskanuj aplikację w poszukiwaniu złośliwego oprogramowania i innej szkodliwej zawartości.
5. Sprawdź, czy wyświetlana zawartość jest zgodna z opisem aplikacji.
6. Sprawdź, czy operacje związane z aplikacjami działają zgodnie z oczekiwaniami w usłudze Power BI. Zespół otwiera raporty i pulpity nawigacyjne z przykładowymi danymi, łączy się z niestandardowymi źródłami danych, odświeża dane i tak dalej.

Zespół certyfikacyjny przekazuje opinie w przypadku znalezienia jakichkolwiek problemów.  Aby uzyskać więcej informacji na temat wymagań aplikacji usługi Power BI, zobacz [dokumentację aplikacji usługi Power BI](https://go.microsoft.com/fwlink/?linkid=2028636).


## <a name="next-steps"></a>Następne kroki

Zalecamy regularne monitorowanie aplikacji w [witrynie AppSource Marketplace.](https://appsource.microsoft.com)  Należy również użyć funkcji [Statystyki sprzedawcy](../../cloud-partner-portal-orig/si-getting-started.md) w [portalu Cloud Partner Portal,](https://cloudpartner.azure.com/#insights) aby uzyskać szczegółowe informacje na temat klientów z marketplace i użycia aplikacji. Na koniec możesz [zaktualizować ofertę](./cpp-update-existing-offer.md).
