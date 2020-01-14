---
title: Wybierz odpowiednią warstwę cenową | Mapy Microsoft Azure
description: W tym artykule znajdziesz informacje o warstwach cenowych oferowanych przez Microsoft Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 01/02/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 5ff774f9848db948058075a98504e6c13db5467a
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911766"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Wybierz odpowiednią warstwę cenową w Azure Maps

Usługa Azure Maps oferuje dwie warstwy cenowe. Celem tego artykułu jest ułatwienie wyboru odpowiedniej warstwy cenowej dla potrzeb użytkownika. Aby wybrać odpowiednią warstwę cenową, należy zadać sobie poniższe dwa pytania.

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>Jakie możliwości geoprzestrzenne są planowane do użycia?
Warstwa cenowa S0 jest odpowiednia dla Ciebie, jeśli podstawowe interfejsy API geoprzestrzenne spełniają wymagania dotyczące usługi. Jeśli chcesz uzyskać bardziej zaawansowane możliwości aplikacji, rozważ rezygnację z warstwy cenowej S1. Przykładowe możliwości to Areal Plus obrazów hybrydowych, uzyskiwanie zakresu tras i geokodowania wsadowego. W poniższej tabeli **możliwości warstwy cenowej** podano lepszy pomysł dotyczący potrzeb aplikacji. Pomaga również wybrać warstwę cenową, która jest odpowiednia dla Twojej aplikacji.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>Ile współbieżnych użytkowników planuję obsługiwać? 
Warstwy cenowe S0 i S1 obsługują różne ilości przepływności danych. Przed wybraniem warstwy cenowej Azure Maps Zwróć się do siebie o kilka pytań. Przykładem jest "jak dużo równoczesnych użytkowników chcemy obsługiwać?". Warstwa cenowa S0 obsługuje do **50 zapytań na sekundę**. Warstwa cenowa S1 obsługuje **więcej niż 50 zapytań na sekundę**.

### <a name="pricing-tier-capabilities"></a>Możliwości warstwy cenowej

| Możliwość                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| Search (FWD/Rev geokodowanie, punkty orientacyjne)  |        ✓           |     ✓    |
| Geokodowanie wsadowe (wersja zapoznawcza)              |                   |     ✓    |
| Wielokąty od wyszukiwania          |                   |     ✓    |
| Wyznaczanie tras                                 |        ✓           |     ✓    |
| Zakres tras                    |                   |     ✓    |
| Routing wsadowy (wersja zapoznawcza)                |                   |     ✓    |
| Routing macierzy (wersja zapoznawcza)               |                   |     ✓    |
| Renderowanie                                  |        ✓           |     ✓    |
| Obrazów i obrazów hybrydowych    |            |     ✓    |
| Ruch                                 |        ✓           |     ✓    |
| Strefy czasowe                              |        ✓           |     ✓    |
| Geolokalizacja (wersja zapoznawcza)                |        ✓           |     ✓    |
| Dane (wersja zapoznawcza)               |                   |     ✓    |
| Przestrzenny (wersja zapoznawcza)               |                   |     ✓    |
| Geofencingu (wersja zapoznawcza)               |                   |     ✓    |



Rozważane są następujące dodatkowe punkty danych:
* Jakiego rodzaju przedsiębiorstwa masz?
* Jak krytyczne są kompilowane aplikacje?

Zapoznaj się z tabelą **docelową dla klientów warstwy cenowej** , aby lepiej poznać warstwy cenowe S0 i S1. Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure Maps](https://azure.microsoft.com/pricing/details/azure-maps/). 

### <a name="pricing-tier-targeted-customers"></a>Zamierzone odbiorcy warstwy cenowej

| Warstwa cenowa  |     Dokierowany klienci                                                                |
|---------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>Warstwa cenowa S0 jest przeznaczony dla klientów, którzy są małymi lub średnimi przedsiębiorstwami. Jest to odpowiednia warstwa cenowa, jeśli nie oczekuje się dużych ilości równoczesnych użytkowników. Jest to również właściwe, jeśli podstawowe interfejsy API geoprzestrzenne, które przedstawiono w powyższej tabeli, spełniają wymagania dotyczące usługi. Ta warstwa jest ogólnie dostępna. Działa to w przypadku aplikacji na wszystkich etapach produkcji: od tworzenia i testowania wczesnych etapów do produkcji i wdrażania aplikacji.<p>|
| S1            |    <p>Warstwa cenowa S1 jest dla klientów potrzebujących wsparcia dla dużych aplikacji o znaczeniu strategicznym, a także dużych ilości równoczesnych użytkowników. Jest on również przeznaczony dla tych klientów, którzy wymagają zaawansowanych usług geoprzestrzennych.</p>|

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o sposobie wyświetlania i zmieniania warstw cenowych:

> [!div class="nextstepaction"] 
> [Zarządzanie warstwą cenową](how-to-manage-pricing-tier.md)
