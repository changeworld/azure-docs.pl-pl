---
title: Wybierz odpowiednią warstwę cenową | Mapy platformy Microsoft Azure
description: W tym artykule dowiesz się o warstwach cenowych oferowanych przez usługi Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: a8bf7ff9bacd4fe84ee5b64d0aed5cb271ce06f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335675"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Wybierz odpowiednią warstwę cenową w usłudze Azure Maps

Usługa Azure Maps oferuje dwie warstwy cenowe, S0 i S1. Celem tego artykułu jest pomoc w wyborze odpowiedniej warstwy cenowej dla Twoich potrzeb. Aby wybrać odpowiednią warstwę cenową, zadaj sobie następujące dwa pytania.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>Ilu współbieżnych użytkowników mam zamiar obsługiwać? 
Warstwy cenowe S0 i S1 obsługują różne ilości przepływności danych. Warstwa cenowa S0 obsługuje do **50 zapytań na sekundę.** Warstwa S1 obsługuje **więcej niż 50 zapytań na sekundę.**

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>Jakich możliwości geoprzestrzennych zamierzam wykorzystać?
Warstwa cenowa S0 jest odpowiednia dla Ciebie, jeśli podstawowe interfejsy API geoprzestrzenne spełniają wymagania usługi. Jeśli chcesz bardziej zaawansowane możliwości dla aplikacji, należy rozważyć wybranie warstwy cenowej S1. Zaawansowane funkcje obejmują: zdjęcia lotnicze i hybrydowe, uzyskiwanie zasięgu trasy i geokodowanie wsadowe. Przejrzyj tabelę **możliwości warstwy cenowej,** aby wybrać warstwę cenową najbardziej odpowiednią dla danego zastosowania.

### <a name="pricing-tier-capabilities"></a>Możliwości warstwy cenowej

| Możliwości                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| Renderowanie mapy                              | ✓                   | ✓       |
| Satelitarne                       |                     | ✓        |
| Wyszukiwanie                                  | ✓                    | ✓        |
| Wyszukiwanie wsadowe                            |                     | ✓        |
| Trasa                                   | ✓                    |✓        |
| Routing wsadowych                            |                    | ✓        |
| Routing macierzy                          |                     | ✓        |
| Zasięg trasy (Izochrony)                |                     | ✓        |
| Ruch                                |✓                    |✓        |
| Strefa czasowa                               |✓                    |✓        |
| Geolokalizacja (wersja zapoznawcza)                    |✓                   |✓        |
| Operacje przestrzenne                        |                    |✓        |
| Geofencing                                |                    |✓        |
| Dane usługi Azure Maps (wersja zapoznawcza)                |                     | ✓        |
| Mobilność (wersja zapoznawcza)                       |                     | ✓        |
| Pogoda (wersja zapoznawcza)                        |✓                    |✓        |

Należy wziąć pod uwagę te dodatkowe punkty:
* Jaki rodzaj przedsiębiorstwa posiadasz?
* Jak ważne jest twoje zgłoszenie?

### <a name="pricing-tier-targeted-customers"></a>Klienci ukierunkowani na warstwę cenową

Zobacz tabelę klientów kierowanych na **warstwę cenową,** aby lepiej zrozumieć warstwy cenowe S0 i S1. Aby uzyskać więcej informacji, zobacz [Ceny usługi Azure Maps](https://azure.microsoft.com/pricing/details/azure-maps/). 

| Warstwa cenowa  |     Klienci ukierunkowani                                                                |
|-----------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>Warstwa cenowa S0 działa dla aplikacji na wszystkich etapach produkcji: od opracowywania weryfikacji koncepcji i testowania na wczesnym etapie do produkcji i wdrażania aplikacji. Jednak ta warstwa jest przeznaczona dla rozwoju na małą skalę lub klientów o niskiej równoczesnych użytkowników lub obu. <p>|
| S1            |    <p>Warstwa cenowa S1 jest dla klientów z aplikacjami korporacyjnymi na dużą skalę, aplikacjami o znaczeniu krytycznym lub dużą liczbą równoczesnych użytkowników. Jest to również dla tych klientów, którzy wymagają zaawansowanych usług geoprzestrzennych.</p>|

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o wyświetlaniu i zmienianiu warstw cenowych:

> [!div class="nextstepaction"] 
> [Zarządzanie warstwą cenową](how-to-manage-pricing-tier.md)
