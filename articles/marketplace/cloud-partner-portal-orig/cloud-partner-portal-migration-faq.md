---
title: Często zadawane pytania dotyczące migracji do Centrum partnerskiego | Azure Marketplace
description: W tym artykule znajdują się często zadawane pytania dotyczące migracji ofert z portalu cloud partner do centrum partnerów.
author: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: mingshen
ms.openlocfilehash: 672153eba4aa2b739b67694f939c4796b39ac4c6
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274382"
---
# <a name="frequently-asked-questions-for-migrating-from-the-cloud-partner-portal-to-partner-center"></a>Często zadawane pytania dotyczące migracji z portalu cloud partner do centrum partnerskiego

W tym artykule znajdują się często zadawane pytania dotyczące migracji ofert z portalu cloud partner do centrum partnerów.

## <a name="what-does-offer-migration-mean"></a>Co oznacza migracja ofertowa?

Przenosimy dane oferty z portalu Cloud Partner do Centrum partnerów ze zmianami w zakresie publikowania ofert i zarządzania nimi.

| Obszar  | Zmiany  |
|-------|----------|
| **Publikowanie i zarządzanie ofertami** | Będziesz mieć lepsze środowisko użytkownika dzięki intuicyjnemu interfejsowi w Centrum partnerów. Aby uzyskać więcej informacji, zobacz [Jakie są różnice między Centrum partnerów a portalem cloud partnerów?](#what-are-the-differences-between-partner-center-and-the-cloud-partner-portal) |
| **Dostępność ofert na rynku** | Brak zmian. Jeśli twoja oferta jest dostępna na rynku, będzie ona nadal kontynuować migrację i po jej zakończeniu. |
| **Nowe zakupy i wdrożenia** | Brak zmian. Twoi klienci będą nadal mogli kupować i wdrażać oferty bez przerw. |
| **Wypłaty** | Wszelkie zakupy i wdrożenia, które mają miejsce podczas lub po migracji, będą nadal wypłacane w zwykły sposób. |
|**Integracja interfejsów API z istniejącymi [interfejsami API portalu partnerów w chmurze](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)** | Istniejące interfejsy API portalu partnerów w chmurze będą nadal obsługiwane po migracji, a istniejące integracje będą nadal działać. Aby uzyskać więcej informacji, zobacz [Czy interfejsy API REST portalu partnerów w chmurze będą obsługiwane po migracji?](#will-the-cloud-partner-portal-rest-apis-be-supported-post-migration) |
| | |

## <a name="can-i-still-access-the-cloud-partner-portal-and-manage-my-offers-during-migration"></a>Czy nadal mogę uzyskać dostęp do portalu cloud partnerów i zarządzać ofertami podczas migracji?

Oferty będą przemyczane do Centrum partnerskiego od 13 kwietnia przez cały miesiąc maj. W tym okresie będziesz mieć dostęp do portalu cloud partner w zwykły sposób, z wyjątkiem czasu, który oferta jest zaplanowana do migracji.
Podczas migracji ofert będą one miały stan "Zablokowany — przejście do Centrum partnerskiego", jak widać na poniższym zrzucie ekranu. Ten okres migracji powinien być krótszy niż 24 godziny. W tym czasie nie będziesz mógł wprowadzać żadnych aktualizacji ofert. Po zakończeniu migracji twoich ofert otrzymasz powiadomienie e-mail.

:::image type="content" source="media/migration-faq/all-offers-1.png" alt-text="Ilustruje stan zmigrowanych ofert.":::

Po migracji ofert będą one w trybie tylko **do odczytu przez ograniczony czas** w portalu Cloud Partner Portal. Ich status będzie wyświetlany "Przeniesiony do Centrum partnerskiego" i zawierać link do oferty w Centrum partnerów, jak pokazano na poniższych zrzutach ekranu. Od tego momentu będziesz zarządzać aktualizacjami wszystkich ofert lub tworzyć nowe oferty wyłącznie za pośrednictwem Centrum partnerskiego,

:::image type="content" source="media/migration-faq/all-offers-2.png" alt-text="Ilustruje komunikat podany dla ofert, które zostały przeniesione do Centrum partnerskiego":::

:::image type="content" source="media/migration-faq/offer-has-moved.png" alt-text="Ilustruje stronę Portalu partnerów w chmurze dla zmigrowanego oferty.":::

## <a name="how-will-i-create-new-offers"></a>Jak utworzyć nowe oferty?

Z portalu Cloud Partner Portal zostaniesz przekierowany do tworzenia nowych ofert w Centrum partnerów

:::image type="content" source="media/migration-faq/create-new-offer-1.png" alt-text="Ilustruje menu, aby utworzyć nową ofertę w portalu cloud partner":::

Po wybraniu nowej oferty zostanie wyświetlony komunikat, na przykład poniższy.

:::image type="content" source="media/migration-faq/create-new-offer-2.png" alt-text="Ilustruje komunikat otrzymany podczas tworzenia nowej oferty w CPP":::

## <a name="do-i-need-to-create-a-new-account-to-manage-offers-in-partner-center"></a>Czy muszę utworzyć nowe konto, aby zarządzać ofertami w Centrum partnerów?

Nie. Twoje konto bazowe zostanie zachowane i powinieneś już nim zarządzać w Centrum partnerów. Oznacza to, że jeśli jesteś istniejącym partnerem, możesz użyć istniejących poświadczeń konta portalu partnerów w chmurze, aby zalogować się do Centrum partnerów po migracji. Tylko oferty i skojarzone środowisko zarządzania przechodzą z portalu Cloud Partner do Centrum partnerów. Prosimy o nietworzenie nowych kont, ponieważ Twoje oferty nie będą powiązane z nowym kontem.

## <a name="i-see-a-message-in-the-cloud-partner-portal-to-activate-my-account-what-does-this-mean"></a>Widzę komunikat w portalu Cloud Partner Portal, aby aktywować moje konto, co to oznacza?

Potrzebujemy kilku szczegółowych informacji od Ciebie, aby prawidłowo przenieść konto do Centrum partnerskiego i umożliwić zarządzanie ofertami w Centrum partnerów po zakończeniu migracji oferty. Aby uzyskać więcej informacji na temat aktywacji konta, zobacz [Migracja konta z portalu cloud partner do centrum partnerów](https://docs.microsoft.com/azure/marketplace/partner-center-portal/account-migration-from-cpp-to-pc).

Kroki wymagane do ukończenia aktywacji konta różnią się w zależności od roli konta.

| Rola konta | Kroki aktywacji |
|--------------|----------------|
|Właściciel | Przejdź do strony [Profil wydawcy](https://cloudpartner.azure.com/#profile) w portalu Cloud Partner Portal, a następnie kliknij łącze na banerze, aby je aktywować. Nastąpi przekierowanie do Centrum partnerów w celu ukończenia aktywacji konta. |
| Współautor | Tylko użytkownik na koncie z rolą właściciela może aktywować konto. Skontaktuj się z właścicielami konta, aby zakończyć aktywację konta. Właściciele twoich kont powinni być wymienieni w wiadomości banerowej. |
| | |

## <a name="im-having-trouble-logging-in-to-my-account-and-opening-a-support-ticket"></a>Mam problem z zalogowaniem się na konto i otwarciem biletu pomocy technicznej

Jeśli nie możesz zalogować się na swoje konto, możesz otworzyć [bilet pomocy technicznej.](https://partner.microsoft.com/support/v2/?stage=1)

## <a name="where-can-i-find-documentation-on-the-new-partner-center-publishing-experience"></a>Gdzie mogę znaleźć dokumentację dotyczącą nowego środowiska publikowania w Centrum partnerów?

Przejdź do [dokumentacji rynku komercyjnego](https://docs.microsoft.com/azure/marketplace/). Then expand **Commercial Marketplace Portal in Partner Center**  > **Create a new offer** to see the help topics for creating each type of offer.

:::image type="content" source="media/migration-faq/marketplace-help-topics.png" alt-text="Ilustruje tematy pomocy dla Centrum partnerów":::

### <a name="what-are-the-differences-between-partner-center-and-the-cloud-partner-portal"></a>Jakie są różnice między Centrum partnerów a portalem cloud partnerów?

Można zauważyć następujące różnice między portalem partnerów w chmurze a centrum partnerów.

### <a name="modular-publishing-capabilities"></a>Modułowe możliwości wydawnicze

Centrum partnerów udostępnia modułową opcję publikowania, która umożliwia wybranie zmian, które chcesz opublikować, zamiast zawsze publikować wszystkie aktualizacje jednocześnie. Na przykład poniższy zrzut ekranu pokazuje, że jedynymi zmianami wybranymi do opublikowania są zmiany **właściwości** i **aukcja oferty**.

:::image type="content" source="media/migration-faq/review-and-publish-migration.png" alt-text="Ilustruje stronę Podgląd i Publikowanie":::

Aktualizacje, które nie są publikowane są zapisywane jako wersje robocze. Kontynuuj korzystanie z podglądu oferty, aby zweryfikować swoją ofertę przed upublicznieniem.

### <a name="rich-text-format"></a>Format tekstu sformatowanym

Popraw opis oferty i planu, korzystając z Edytora tekstu sformatowego na stronie Aukcja oferty i Aukcja planu.

:::image type="content" source="media/migration-faq/rich-text-editor-migration.png" alt-text="Ilustruje edytor tekstu sformatacyjnego":::

### <a name="enhanced-preview-options"></a>Ulepszone opcje podglądu

Centrum partnerów zawiera [funkcję porównania](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#compare-changes-to-marketplace-offers) z ulepszonymi opcjami filtrowania. Daje to możliwość porównania z wersjami podglądu i wersji na żywo oferty.

:::image type="content" source="media/migration-faq/enhanced-preview.png" alt-text="Ilustruje funkcję porównywania":::

### <a name="branding-and-navigation-changes"></a>Zmiany w znakowaniu i nawigacji

Zauważysz pewne zmiany w brandingu. Na przykład "Jednostki SKU" są oznaczone jako "Plany" w Centrum partnerów.

:::image type="content" source="media/migration-faq/plan-overview-migration.png" alt-text="Ilustruje przegląd planu.":::

Ponadto informacje używane do podawania na stronach **Marketplace** lub S**torefront Details** (Consulting Service, Power BI app) w portalu Cloud Partner Portal są zbierane na stronie **Lista ofert** w Centrum partnerów.

:::image type="content" source="media/migration-faq/offer-listing-migration.png" alt-text="Ilustruje stronę aukcji ofert.":::

Informacje używane do dostarczania jednostek SKU na jednej stronie w portalu Cloud Partner Portal mogą być teraz zbierane na kilku stronach w Centrum partnerów:

* Strona konfigurowania planu
* Strona aukcji planu
* Strona dostępności planu
* Zaplanuj techniczną stronę konfiguracji, jak pokazano na tym zrzucie ekranu.

:::image type="content" source="media/migration-faq/tech-config-migration.png" alt-text="Ilustruje stronę konfiguracji technicznej planu.":::

Identyfikator oferty jest teraz wyświetlany na lewym pasku nawigacyjnym oferty.

:::image type="content" source="media/migration-faq/offer-id-offer-overview.png" alt-text="Ilustruje menu nawigacji po lewej stronie z identyfikatorem oferty.":::

### <a name="stop-selling-an-offer"></a>Zaprzestanie sprzedaży oferty

Możesz poprosić o [zaprzestanie sprzedaży oferty](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan) na rynku bezpośrednio z portalu Centrum partnerów. Opcja jest dostępna na stronie **Przegląd oferty** dla twojej oferty.

:::image type="content" source="media/migration-faq/stop-selling-migration.png" alt-text="Ilustruje stronę Przegląd oferty z opcją stop sprzedaży.":::

## <a name="what-pages-in-partner-center-correspond-to-pages-i-used-in-the-cloud-partner-portal"></a>Jakie strony w Centrum partnerów odpowiadają stronom używanym w portalu Cloud Partner?

W poniższej tabeli przedstawiono odpowiednie łącza między dwoma portalami.

| Strona | Łącze portalu partnerów w chmurze | Łącze Centrum partnerów |
|------|---------------------------|---------------------|
| **Strona ze wszystkimi ofertami** | https://cloudpartner.azure.com/#alloffers | https://partner.microsoft.com/dashboard/commercial-marketplace/overview |
| **Strona wszystkich wydawców** | https://cloudpartner.azure.com/#publishers | https://partner.microsoft.com/dashboard/account/v3/publishers/list |
| **Profil wydawcy** | https://cloudpartner.azure.com/#profile | https://partner.microsoft.com/dashboard/account/management |
| **Strona użytkowników** | https://cloudpartner.azure.com/#users | https://partner.microsoft.com/dashboard/account/usermanagement |
| **Strona historia** | https://cloudpartner.azure.com/#history | Funkcja Historia nie jest jeszcze obsługiwana w Centrum partnerów. |
| **Pulpit nawigacyjny szczegółowych informacji** | https://cloudpartner.azure.com/#insights | https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary |
| **Raport wypłat** | https://cloudpartner.azure.com/#insights/payout | https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| | |

## <a name="will-the-cloud-partner-portal-rest-apis-be-supported-post-migration"></a>Czy interfejsy API REST portalu partnerów w chmurze będą obsługiwane po migracji?

Interfejsy API portalu partnerów w chmurze są zintegrowane z centrum partnerów i będą nadal działać po migracji ofert do Centrum partnerów. Integracja wprowadza niewielkie zmiany. Przejrzyj zmiany w poniższej tabeli, aby upewnić się, że kod będzie nadal działać po migracji do Centrum partnerskiego.

| **interfejs API** | **Zmień opis** | **Wpływ** |
| ------- | ---------------------- | ---------- |
| PUBLIKUJ, GoLive, Anuluj | W przypadku zmigrowanych ofert nagłówek odpowiedzi będzie miał inny format, ale będzie nadal działać w ten sam sposób, oznaczając ścieżkę względną w celu pobrania stanu operacji. | Podczas wysyłania dowolnego z odpowiednich żądań POST dla oferty, nagłówek Lokalizacja będzie miał jeden z dwóch formatów w zależności od stanu migracji oferty:<ul><li>Oferty niezmiagrowane<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>Zmigrowane oferty<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| Operacja GET | W przypadku ofert, które wcześniej obsługiwały pole "notification-email" w odpowiedzi, to pole zostanie przestarzałe i nie będzie już zwracane dla zmigrowanych ofert. | W przypadku zmigrowanych ofert nie będziemy już wysyłać powiadomień do listy wiadomości e-mail określonych w żądaniach. Zamiast tego usługa interfejsu API będzie zgodna z procesem wiadomości e-mail powiadomień w Centrum partnerów, aby wysyłać wiadomości e-mail. W szczególności powiadomienia będą wysyłane na adres e-mail ustawiony w sekcji Informacje kontaktowe sprzedającego w ustawieniach konta w Centrum partnerów, aby powiadomić Cię o postępie operacji.<br><br>Przejrzyj adres e-mail ustawiony w sekcji Informacje kontaktowe sprzedającego w [ustawieniach konta](https://partner.microsoft.com/dashboard/account/management) w Centrum partnerów, aby upewnić się, że dla powiadomień jest dostępna poprawna wiadomość e-mail.  |
| | | |
