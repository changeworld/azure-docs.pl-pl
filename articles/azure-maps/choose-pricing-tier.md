---
title: Wybierz odpowiednią warstwę cenową | Mapy Microsoft Azure
description: W tym artykule znajdziesz informacje o warstwach cenowych oferowanych przez Microsoft Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 8764e9161f952118ca7ae28343dcd16477cf1eee
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76155761"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Wybierz odpowiednią warstwę cenową w Azure Maps

Azure Maps oferuje dwie warstwy cenowe, S0 i S1. Celem tego artykułu jest ułatwienie wyboru odpowiedniej warstwy cenowej dla potrzeb użytkownika. Aby wybrać odpowiednią warstwę cenową, należy zadać sobie poniższe dwa pytania.

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>Jakie możliwości geoprzestrzenne są planowane do użycia?
Warstwa cenowa S0 jest odpowiednia dla Ciebie, jeśli podstawowe interfejsy API geoprzestrzenne spełniają wymagania dotyczące usługi. Jeśli chcesz uzyskać bardziej zaawansowane możliwości aplikacji, rozważ rezygnację z warstwy cenowej S1. Przykład zaawansowanych możliwości: lotnicze oraz zdjęcia hybrydowe, uzyskiwanie zakresu tras i geokodowanie partii. Tabela **możliwości warstwy cenowej** może pomóc w wyborze warstwy cenowej, która jest odpowiednia dla danej aplikacji.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>Ile współbieżnych użytkowników planuję obsługiwać? 
Warstwy cenowe S0 i S1 obsługują różne ilości przepływności danych. Warstwa cenowa S0 obsługuje do **50 zapytań na sekundę**, podczas gdy warstwa S1 obsługuje **więcej niż 50 zapytań na sekundę**.

### <a name="pricing-tier-capabilities"></a>Możliwości warstwy cenowej

| Możliwość                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| Renderowanie mapy                              | ✓                   | ✓       |
| Obraz satelitarny                       |                     | ✓        |
| Search                                  | ✓                    | ✓        |
| Wyszukiwanie partii                            |                     | ✓        |
| Trasa                                   | ✓                    |✓        |
| Partie tras                            |                    | ✓        |
| Wyznaczanie tras na podstawie macierzy                          |                     | ✓        |
| Zakres trasy (izochrony)                |                     | ✓        |
| Ruch                                |✓                    |✓        |
| Strefa czasowa                               |✓                    |✓        |
| Geolokalizacja (wersja zapoznawcza)                    |✓                   |✓        |
| Operacje przestrzenne                        |                    |✓        |
| Wirtualne grodzenie                                |                    |✓        |
| Dane Azure Maps (wersja zapoznawcza)                |                     | ✓        |
| Mobilność (wersja zapoznawcza)                       |                     | ✓        |
| Pogoda (wersja zapoznawcza)                        |✓                    |✓        |

Rozważane są następujące dodatkowe punkty danych:
* Jakiego typu przedsiębiorstwa masz?
* Jak jest krytyczna aplikacja?

### <a name="pricing-tier-targeted-customers"></a>Zamierzone odbiorcy warstwy cenowej

Zapoznaj się z tabelą **docelową dla klientów warstwy cenowej** , aby lepiej poznać warstwy cenowe S0 i S1. Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure Maps](https://azure.microsoft.com/pricing/details/azure-maps/). 

| Warstwa cenowa  |     Dokierowany klienci                                                                |
|-----------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>Warstwa cenowa S0 działa w przypadku aplikacji na wszystkich etapach produkcji: od projektowania rozwiązań do tworzenia koncepcji i wczesnego etapu testowania do produkcji i wdrażania aplikacji. Jednak ta warstwa została zaprojektowana na potrzeby rozwoju małych lub klientów, którzy mają mniej równoczesnych użytkowników lub obie te funkcje. <p>|
| S1            |    <p>Warstwa cenowa S1 jest dla klientów potrzebujących wsparcia dla dużych aplikacji o znaczeniu strategicznym, a także dużych ilości równoczesnych użytkowników. Jest on również przeznaczony dla tych klientów, którzy wymagają zaawansowanych usług geoprzestrzennych.</p>|

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o sposobie wyświetlania i zmieniania warstw cenowych:

> [!div class="nextstepaction"] 
> [Zarządzanie warstwą cenową](how-to-manage-pricing-tier.md)
