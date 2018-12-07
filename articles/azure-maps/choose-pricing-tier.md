---
title: Wybrać właściwą warstwę cenową dla usługi Azure Maps | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o oferowanych przez usługi Azure Maps warstw cenowych
author: walsehgal
ms.author: v-musehg
ms.date: 12/05/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: ee277867f449afddeb89c3fd73b5b577a68a4497
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52998380"
---
# <a name="choosing-the-right-pricing-tier-in-azure-maps"></a>Wybieranie odpowiedniej warstwy cenowej w usługi Azure Maps

Usługi Azure Maps oferuje dwie warstwy cenowe. Ten artykuł ma na celu pomóc wybrać właściwą ceny dla Twoich potrzeb warstwę. Aby wybrać właściwą warstwę cenową, spróbuj odpowiedzieć sobie dwa pytania:

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>Jakie funkcje geoprzestrzenne chcę użyć?
Jeśli uważasz, że Twoje wymagania dotyczące usługi są spełnione przez dane geograficzne podstawowych interfejsów API, a następnie warstwy cenowej S0 jest odpowiedni dla Ciebie. Jeśli chcesz bardziej zaawansowane funkcje dla swojej aplikacji, takich jak obrazach hybrydowych areal + wprowadzenie zakres z trasy, batch geokodowanie itp., należy wziąć pod uwagę, rezygnując na warstwę cenową S1. Poniższa tabela z **ceny warstwy możliwości** będzie udostępniać lepiej zrozumieć wymagania używanej aplikacji i spowoduje również pomoc, możesz wybrać warstwę cenową najbardziej odpowiedni dla aplikacji.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>Jak wiele równoczesnych użytkowników zaplanować do obsługi? 
S0 i warstw cenowych S1 może obsługiwać różne ilości przepływność danych. Przed wybraniem usługi Azure Maps warstwy cenowej, należy wziąć pod uwagę pytaniem samodzielnie pytania, jak wiele równoczesnych użytkowników mają być obsługiwane? S0 warstwa cenowa może obsługiwać maksymalnie **50 zapytań na sekundę** i S1 warstwy cenowej może obsługiwać **więcej niż 50 zapytań na sekundę**.

### <a name="pricing-tier-capabilities"></a>Cennik warstwy możliwości

| Możliwości                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| Wyszukiwanie                                  |        ✓           |     ✓    |
| Routing                                 |        ✓           |     ✓    |
| Renderowanie                                  |        ✓           |     ✓    |
| Ruch                                 |        ✓           |     ✓    |
| Strefy czasowe                              |        ✓           |     ✓    |
| * Obrazach i obrazach hybrydowych (wersja zapoznawcza)     |        ✓           |     ✓    |
| * Zakres z trasy (wersja zapoznawcza)                  |        ✓           |     ✓    |
| * Lokalizacja 2 IP (wersja zapoznawcza)                |        ✓           |     ✓    |
| * Wielokąty z wyszukiwania (wersja zapoznawcza)         |        ✓           |     ✓    |
| * Geokodowanie usługi batch (wersja zapoznawcza)              |        ✓           |     ✓    |
| * Routing usługi batch (wersja zapoznawcza)                |        ✓           |     ✓    |
| * Routing macierzy (wersja zapoznawcza)               |        ✓           |     ✓    |

> [!Note]
> Dostęp do funkcji z S0 warstwy cenowej staną się przestarzałe po 4 lutego 2019 r.

Kilka dodatkowych punktów danych warto biorąc pod uwagę, jakiego rodzaju enterprise masz lub jak bardzo krytyczna jest aplikacją kompilowanego na bieżąco?

Zobacz tabelę z **warstwa cenowa docelowych klientów** aby lepiej poznać S0 i S1 warstw cenowych. Aby uzyskać więcej informacji na temat cen usługi Azure Maps, zobacz, [cennik usługi Azure Maps](https://azure.microsoft.com/pricing/details/azure-maps/). 

### <a name="pricing-tier-targeted-customers"></a>Warstwa cenowa docelowych klientów

| Warstwa cenowa  |        Klientów docelowych                                                                |
|---------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>Warstwa cenowa S0 jest przeznaczona dla klientów, którzy przedsiębiorstwom małych i średnich. To prawo warstwy cenowej dla Ciebie, jeśli nie będzie dużej liczby równoczesnych użytkowników, a Twoje wymagania dotyczące usługi są spełnione przez geoprzestrzenne podstawowych interfejsów API, opisane w poniższej tabeli. Ta warstwa jest ogólnie dostępna i ma zastosowanie w przypadku aplikacji we wszystkich etapów cyklu produkcji od weryfikacji koncepcji programowania i wczesnym etapie testowania do produkcyjnych aplikacji i wdrożenia.<p>|
| S1            |    <p>Warstwę cenową S1 jest przeznaczona dla klientów wymagających pomocy technicznej dla przedsiębiorstw na dużą skalę, dla aplikacji o znaczeniu, dużej liczby równoczesnych użytkowników lub wymaga usług geoprzestrzennych zaawansowane.</p>|


## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat wyświetlania i zmieniania warstwy cenowej:

> [!div class="nextstepaction"]
> [Zarządzanie warstwy cenowej](how-to-manage-pricing-tier.md)
