---
title: Pomiary rzeczywistych użytkowników w usłudze Azure Traffic Manager
description: W tym wprowadzeniu dowiedz się, jak działają pomiary rzeczywistych użytkowników usługi Azure Traffic Manager.
services: traffic-manager
documentationcenter: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: rohink
ms.custom: ''
ms.openlocfilehash: e38e1255b1a84ab5d3fd37e16eb65c76001dbfa1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938431"
---
# <a name="traffic-manager-real-user-measurements-overview"></a>Omówienie pomiarów rzeczywistego użytkownika usługi Traffic Manager

Podczas konfigurowania profilu usługi Traffic Manager do korzystania z metody routingu wydajności, usługa sprawdza, gdzie żądania kwerendy DNS pochodzą z i sprawia, że decyzje routingu do kierowania tych żądających do regionu platformy Azure, który daje im najniższe opóźnienie. Jest to realizowane przy użyciu analizy opóźnienia sieci, które usługa Traffic Manager utrzymuje dla różnych sieci użytkowników końcowych.

Pomiary rzeczywistych użytkowników umożliwia pomiary opóźnienia sieci do regionów platformy Azure, z aplikacji klienckich użytkowników końcowych używać i mają Usługi Traffic Manager rozważyć te informacje, jak również podczas podejmowania decyzji routingu. Wybierając opcję używania pomiarów rzeczywistego użytkownika, można zwiększyć dokładność routingu dla żądań pochodzących z tych sieci, w których znajdują się użytkownicy końcowi. 

## <a name="how-real-user-measurements-work"></a>Jak działają pomiary rzeczywistych użytkowników

Pomiary rzeczywistych użytkowników działają, mając aplikacje klienckie pomiaru opóźnienia do regionów platformy Azure, jak to jest widoczne z sieci użytkowników końcowych, w których są one używane. Jeśli na przykład masz stronę sieci Web dostępną dla użytkowników w różnych lokalizacjach (na przykład w regionach Ameryki Północnej), możesz użyć rzeczywistych pomiarów użytkownika z metodą routingu wydajności, aby uzyskać dostęp do najlepszego regionu platformy Azure, w którym serwer jest używany aplikacja jest hostowana.

Rozpoczyna się od osadzania platformy Azure dostarczonej javascript (z unikatowym kluczem) na stronach sieci web. Gdy to zrobisz, za każdym razem, gdy użytkownik odwiedza tę stronę sieci Web, JavaScript wysyła zapytanie do Menedżera ruchu, aby uzyskać informacje o regionach platformy Azure, które powinien zmierzyć. Usługa zwraca zestaw punktów końcowych do skryptu, który następnie mierzy te regiony kolejno, pobierając obraz pojedynczego piksela hostowanego w tych regionach platformy Azure i odnocząc opóźnienie między wysłaniem żądania a czasem odebrania pierwszego bajtu . Te pomiary są następnie zgłaszane z powrotem do usługi Usługi Usługi Traffic Manager.

Z biegiem czasu dzieje się to wiele razy i w wielu sieciach prowadzących do menedżera ruchu coraz dokładniejsze informacje o charakterystyki opóźnienia sieci, w których znajdują się użytkownicy końcowi. Te informacje zaczynają być uwzględniane w decyzjach dotyczących routingu podejmowanych przez usługę Traffic Manager. W rezultacie prowadzi to do zwiększenia dokładności tych decyzji w oparciu o rzeczywiste pomiary użytkownika wysłane.

W przypadku korzystania z pomiarów rzeczywistych użytkowników naliczane są naliczane na podstawie liczby pomiarów wysłanych do Menedżera ruchu. Aby uzyskać więcej informacji na temat cen, odwiedź [stronę z cennikiem Menedżera ruchu](https://azure.microsoft.com/pricing/details/traffic-manager/).

## <a name="faqs"></a>Często zadawane pytania

* [Jakie są korzyści z używania pomiarów rzeczywistych użytkowników?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-the-benefits-of-using-real-user-measurements)

* [Czy mogę używać pomiarów rzeczywistych użytkowników w regionach innych niż platformy Azure?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-real-user-measurements-with-non-azure-regions)

* [Która metoda routingu korzysta z pomiarów rzeczywistych użytkowników?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#which-routing-method-benefits-from-real-user-measurements)

* [Czy muszę włączyć pomiary rzeczywistego użytkownika w każdym profilu oddzielnie?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-enable-real-user-measurements-each-profile-separately)

* [Jak wyłączyć pomiary rzeczywistego użytkownika dla mojej subskrypcji?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-do-i-turn-off-real-user-measurements-for-my-subscription)

* [Czy mogę używać pomiarów rzeczywistych użytkowników w aplikacjach klienckich innych niż strony internetowe?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-real-user-measurements-with-client-applications-other-than-web-pages)

* [Ile pomiarów jest dokonywanych za każdym razem, gdy wyrenderowana jest strona internetowa z włączoną stroną realną pomiarów użytkownika?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered)

* [Czy na mojej stronie pojawi się opóźnienie, zanim skrypt Pomiary rzeczywistego użytkownika zostanie uruchomionym?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage)

* [Czy mogę używać pomiarów rzeczywistych użytkowników tylko w regionach platformy Azure, które chcę zmierzyć?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-real-user-measurements-with-only-the-azure-regions-i-want-to-measure)

* [Czy mogę ograniczyć liczbę pomiarów wykonanych do określonej liczby?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-limit-the-number-of-measurements-made-to-a-specific-number)

* [Czy mogę zobaczyć pomiary wykonane przez aplikację kliencką w ramach pomiarów rzeczywistych użytkowników?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements)

* [Czy mogę zmodyfikować skrypt pomiaru dostarczony przez Traffic Manager?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-modify-the-measurement-script-provided-by-traffic-manager)

* [Czy inni będą mogli zobaczyć klucz, którego używam w pomiarach rzeczywistego użytkownika?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements)

* [Czy inni mogą nadużywać mojego klucza RUM?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-others-abuse-my-rum-key)

* [Czy muszę umieścić pomiar JavaScript na wszystkich moich stronach internetowych?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages)

* [Czy informacje o moich użytkownikach końcowych mogą być identyfikowane przez Traffic Manager, jeśli używam pomiarów rzeczywistych użytkowników?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements)

* [Czy strona internetowa mierząca pomiary rzeczywistego użytkownika musi używać Menedżera ruchu do routingu?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing)

* [Czy muszę obsługiwać dowolną usługę w regionach platformy Azure, aby używać z pomiarami rzeczywistego użytkownika?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements)

* [Czy użycie przepustowości platformy Azure wzrośnie, gdy używam pomiarów rzeczywistego użytkownika?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements)

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak korzystać [z pomiarów rzeczywistych użytkowników na stronach internetowych](traffic-manager-create-rum-web-pages.md)
- Dowiedz [się, jak działa Usługa Traffic Manager](traffic-manager-overview.md)
- Dowiedz się więcej o [Mobile Center](https://docs.microsoft.com/mobile-center/)
- Dowiedz się więcej o [metodach routingu ruchu](traffic-manager-routing-methods.md) obsługiwanych przez usługę Traffic Manager
- Dowiedz się, jak [utworzyć profil usługi Traffic Manager](traffic-manager-create-profile.md)

