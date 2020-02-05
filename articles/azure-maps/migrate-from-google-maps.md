---
title: 'Samouczek: Migrowanie z usługi Google Maps do Azure Maps | Mapy Microsoft Azure'
description: Samouczek dotyczący migracji z usługi Google Maps do Microsoft Azure Maps. Wskazówki przeprowadzą Cię przez proces przełączania do Azure Maps interfejsów API i zestawów SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 1f6f282406c6813b2b126c300f21bda21e8f9464
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988978"
---
# <a name="migrate-from-google-maps-to-azure-maps"></a>Migrowanie z usługi Google Maps do Azure Maps

Ten samouczek zawiera szczegółowe informacje na temat migrowania aplikacji sieci Web, mobilnych i opartych na serwerze z usługi Google Maps do platformy Maps Microsoft Azure. Ten samouczek zawiera porównawcze przykłady kodu, sugestie dotyczące migracji oraz najlepsze rozwiązania dotyczące migracji do Azure Maps.

## <a name="azure-maps-platform-overview"></a>Omówienie platformy Azure Maps

Azure Maps oferuje deweloperom możliwość korzystania z zaawansowanych możliwości geograficznych, spakowanych ze regularnie aktualizowanymi danymi mapy w celu zapewnienia geograficznego kontekstu dla aplikacji sieci Web i mobilnych. Azure Maps ma zestaw interfejsów API REST zgodny z platformą Azure. Te interfejsy API REST oferują mapy, wyszukiwanie, routing, ruch, strefy czasowe, geolokalizację, geoogrodzenie, dane mapy, Pogoda, mobilność i operacje przestrzenne. Do operacji dołączone są zestawy SDK sieci Web i Android, które ułatwiają tworzenie, elastyczne i przenośne Programowanie na wielu platformach.

## <a name="high-level-platform-comparison"></a>Porównanie platform wysokiego poziomu

Poniższa tabela zawiera listę funkcji Azure Maps, które odpowiadają funkcjom usługi Google Maps. Na tej liście nie są wyświetlane wszystkie funkcje Azure Maps. Dodatkowe Azure Maps obejmują ułatwienia dostępu, interfejsy API geoogrodzenia, izochron, operacje przestrzenne, bezpośredni dostęp do kafelków, usługi Batch i porównania pokrycia danych (czyli pokrycie obrazów).

| Funkcja usługi Google Maps         | Obsługa Azure Maps                     |
|-----------------------------|:--------------------------------------:|
| Zestaw SDK sieci Web                     | ✓                                      |
| Android SDK                 | ✓                                      |
| Zestaw SDK systemu iOS                     | Planowany                                |
| Interfejsy API usługi REST           | ✓                                      |
| Kierunki (routing)        | ✓                                      |
| Macierz odległości             | ✓                                      |
| Noszeniu                   | Planowany                                |
| Geokodowanie (do przodu/wstecz) | ✓                                      |
| Geolokalizacja                 | ND                                    |
| Wyszukiwanie miejsc               | ✓                                      |
| Szczegóły miejsc              | Nie dotyczy — witryna sieci Web & jest dostępny numer telefonu |
| Umieszcza Zdjęcia               | ND                                    |
| Umieść Autouzupełnianie          | ✓                                      |
| Mapy statyczne                 | ✓                                      |
| Widok statyczny          | ND                                    |
| Strefa czasowa                   | ✓                                      |
| Maps — osadzony interfejs API           | ND                                    |
| Adresy URL map                    | ND                                    |

Usługi Mapy Google zapewniają podstawowe uwierzytelnianie oparte na kluczach. Azure Maps zapewnia podstawowe uwierzytelnianie oparte na kluczach oraz wysoce bezpieczne, Azure Active Directory uwierzytelnianie.

## <a name="licensing-considerations"></a>Zagadnienia dotyczące licencjonowania

W przypadku migrowania do Azure Maps z usługi Google Maps w odniesieniu do licencjonowania należy wziąć pod uwagę następujące kwestie.

- Azure Maps opłaty za użycie map interaktywnych, które są oparte na liczbie załadowanych kafelków mapy, ale usługi Google Maps są naliczane za ładowanie kontrolki mapy. W zestawach SDK interakcyjne Azure Maps kafelki mapy są automatycznie buforowane, aby zmniejszyć koszty dla deweloperów. Dla każdego 15 ładowanych kafelków mapy jest generowana jedna transakcja Azure Maps. Zestawy SDK Interactive Azure Maps używają kafelków 512-pikselowych, a średnio generują co najmniej jedną transakcję na widok strony.
- Zastępowanie obrazów mapy statycznej z usług sieci Web usługi Google Maps przy użyciu zestawu SDK Azure Maps sieci Web jest często znacznie bardziej ekonomiczne. Zestaw SDK sieci Web Azure Maps używa kafelków mapy, chyba że użytkownik Pans i powiększa mapę, często generują tylko część transakcji na obciążenie mapy. Zestaw SDK sieci Web Azure Maps zawiera opcje wyłączania panoramowania i powiększania. Ponadto zestaw SDK sieci Web Azure Maps udostępnia wiele opcji wizualizacji niż usługa sieci Web mapy statycznej.
- Azure Maps umożliwia przechowywanie danych z platformy na platformie Azure. Może ona być również buforowana w innym miejscu przez maksymalnie sześć miesięcy zgodnie z [warunkami użytkowania](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46).

Poniżej przedstawiono kilka powiązanych zasobów Azure Maps:

- [Strona cennika Azure Maps](https://azure.microsoft.com/pricing/details/azure-maps/)
- [Kalkulator cen platformy Azure](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
- [Azure Maps okres użytkowania](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46) (zawarty w postanowieniach dotyczących usług online firmy Microsoft)
- [Wybierz odpowiednią warstwę cenową w Azure Maps](https://docs.microsoft.com/azure/azure-maps/choose-pricing-tier)

## <a name="suggested-migration-plan"></a>Sugerowany plan migracji

Poniżej znajduje się plan migracji wysokiego poziomu.

1. Utwórz spis zestawów SDK i usług usługi Google Maps używanych przez aplikację i sprawdź, czy Azure Maps udostępnia alternatywne zestawy SDK i usługi, z którymi można przeprowadzić migrację.
2. Utwórz subskrypcję platformy Azure (jeśli jeszcze jej nie masz) w [https://azure.com](https://azure.com).
3. Utwórz konto Azure Maps ([dokumentację](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys)) i klucz uwierzytelniania lub Azure Active Directory ([Dokumentacja](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)).
4. Migruj kod aplikacji.
5. Przetestuj zmigrowane aplikacje.
6. Wdróż zmigrowane aplikacje w środowisku produkcyjnym.

## <a name="azure-maps-technical-resources"></a>Azure Maps zasoby techniczne

Poniżej znajduje się lista przydatnych zasobów technicznych dla Azure Maps.

- Przegląd: [https://azure.com/maps](https://azure.com/maps)
- Dokumentacja: [https://aka.ms/AzureMapsDocs](https://aka.ms/AzureMapsDocs)
- Przykłady kodu zestawu SDK sieci Web: [https://aka.ms/AzureMapsSamples](https://aka.ms/AzureMapsSamples)
- Fora dla deweloperów: [https://aka.ms/AzureMapsForums](https://aka.ms/AzureMapsForums)
- Wideo: [https://aka.ms/AzureMapsVideos](https://aka.ms/AzureMapsVideos)
- Blog: [https://aka.ms/AzureMapsBlog](https://aka.ms/AzureMapsBlog)
- Azure Maps opinię (UserVoice): [https://aka.ms/AzureMapsFeedback](https://aka.ms/AzureMapsFeedback)

## <a name="migration-support"></a>Pomoc techniczna dotycząca migracji

Deweloperzy mogą szukać pomocy technicznej dotyczącej migracji za pomocą [forów](https://aka.ms/AzureMapsForums) lub jednej z wielu opcji pomocy technicznej platformy Azure: [https://azure.microsoft.com/support/options](https://azure.microsoft.com/support/options)

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak migrować aplikację usługi Google Maps z następującymi artykułami:

> [!div class="nextstepaction"]
> [Migrowanie aplikacji sieci Web](migrate-from-google-maps-web-app.md)

> [!div class="nextstepaction"]
> [Migrowanie aplikacji systemu Android](migrate-from-google-maps-android-app.md)

> [!div class="nextstepaction"]
> [Migrowanie usługi sieci Web](migrate-from-google-maps-web-services.md)
