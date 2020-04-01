---
title: 'Samouczek: Migracja z Map Google do Usługi Azure Maps | Mapy platformy Microsoft Azure'
description: Samouczek dotyczący migracji z Map Google do Map Platformy Microsoft Azure. Wskazówki zawierają informacje o przełączaniu się do interfejsów API i zestawów SDK usługi Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 7b73923b7fc32ae83bfc8405d074835c02031a63
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77913705"
---
# <a name="migrate-from-google-maps-to-azure-maps"></a>Migrowanie z Map Google do Usługi Azure Maps

Ten samouczek zawiera szczegółowe informacje na temat migracji aplikacji internetowych, mobilnych i serwerowych z Map Google na platformę Microsoft Azure Maps. Ten samouczek zawiera przykłady kodu porównawczego, sugestie dotyczące migracji i najlepsze rozwiązania dotyczące migracji do usługi Azure Maps.

## <a name="azure-maps-platform-overview"></a>Omówienie platformy Usługi Azure Maps

Usługa Azure Maps zapewnia deweloperom ze wszystkich branż zaawansowane funkcje geoprzestrzenne. Możliwości są wyposażone w regularnie aktualizowane dane map, aby zapewnić kontekst geograficzny dla aplikacji internetowych i mobilnych. Usługa Azure Maps ma zestaw interfejsów API zgodnych z interfejsem API platformy Azure One. Interfejsy API REST oferują renderowanie map, wyszukiwanie, routing, ruch, strefy czasowe, geolokalizacja, geofencing, dane mapy, pogodę, mobilność i operacje przestrzenne. Operacje są połączone zarówno web i Android SDK, aby programowanie łatwe, elastyczne i przenośne na wielu platformach.

## <a name="high-level-platform-comparison"></a>Porównanie platform wysokiego poziomu

Tabela zawiera listę funkcji usługi Azure Maps wysokiego poziomu, które odpowiadają funkcjom Map Google. Ta lista nie zawiera wszystkich funkcji usługi Azure Maps. Dodatkowe funkcje usługi Azure Maps obejmują: ułatwienia dostępu, geofencing, izochrones, operacje przestrzenne, bezpośredni dostęp do kafelków mapy, usługi wsadowe i porównania pokrycia danych (czyli pokrycie obrazów).

| Funkcja Mapy Google         | Pomoc techniczna usługi Azure Maps                     |
|-----------------------------|:--------------------------------------:|
| Zestaw SDK sieci Web                     | ✓                                      |
| Android SDK                 | ✓                                      |
| Zestaw SDK systemu iOS                     | Planowany                                |
| Interfejsy API usługi REST           | ✓                                      |
| Wskazówki dojazdu (routing)        | ✓                                      |
| Matryca odległości             | ✓                                      |
| Wysokość                   | Planowany                                |
| Geokodowanie (do przodu/do tyłu) | ✓                                      |
| Geolokalizacja                 | Nie dotyczy                                    |
| Najbliższe drogi               | ✓                                      |
| Wyszukiwanie miejsc               | ✓                                      |
| Szczegóły miejsc              | N/A – dostępna strona internetowa & numer telefonu |
| Miejsca zdjęcia               | Nie dotyczy                                    |
| Umieść autouzupełnienie          | ✓                                      |
| Przyciągaj do drogi                | ✓                                      |
| Ograniczenia prędkości                | ✓                                      |
| Mapy statyczne                 | ✓                                      |
| Statyczny widok ulicy          | Nie dotyczy                                    |
| Strefa czasowa                   | ✓                                      |
| Osadzony interfejs API map           | Nie dotyczy                                    |
| Adresy URL map                    | Nie dotyczy                                    |

Mapy Google zapewniają podstawowe uwierzytelnianie oparte na kluczach. Usługa Azure Maps zapewnia zarówno uwierzytelnianie podstawowe oparte na kluczu, jak i uwierzytelnianie usługi Azure Active Directory. Uwierzytelnianie usługi Azure Active Directory zapewnia więcej funkcji zabezpieczeń w porównaniu z podstawowym uwierzytelnianiem opartym na kluczu.

## <a name="licensing-considerations"></a>Zagadnienia dotyczące licencjonowania

Podczas migracji do usługi Azure Maps z Map Google należy wziąć pod uwagę następujące kwestie dotyczące licencjonowania.

- Usługa Azure Maps pobiera opłaty za użycie interaktywnych map, która jest oparta na liczbie załadowanych kafelków map. Z drugiej strony, Mapy Google pobiera opłaty za ładowanie kontroli mapy. W interaktywnych zestawach SDK usługi Azure Maps kafelki map są automatycznie buforowane w celu zmniejszenia kosztów tworzenia. Jedna transakcja usługi Azure Maps jest generowana dla każdego 15 kafelków mapy, które są ładowane. Interaktywne zestawy SDK usługi Azure Maps używają kafelków 512 pikseli i średnio generuje jedną lub mniej transakcji na widok strony.
- Często bardziej opłacalne jest zastępowanie statycznych obrazów map z usług internetowych Map Google zestawem SDK usługi Azure Maps. Zestaw Azure Maps Web SDK używa kafelków map. O ile użytkownik nie przesuwa i nie powiększa mapy, usługa często generuje tylko ułamek transakcji na obciążenie mapy. Zestaw SDK sieci Web usługi Azure Maps ma opcje wyłączania przesuwania i powiększania w razie potrzeby. Ponadto zestaw SDK sieci Web usługi Azure Maps udostępnia o wiele więcej opcji wizualizacji niż statyczna usługa sieci web mapy.
- Usługa Azure Maps umożliwia przechowywanie danych z jej platformy na platformie Azure. Ponadto dane mogą być buforowane w innym miejscu przez okres do sześciu miesięcy zgodnie [z warunkami użytkowania.](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46)

Oto kilka powiązanych zasobów dla usługi Azure Maps:

- [Strona z cennikiem usługi Azure Maps](https://azure.microsoft.com/pricing/details/azure-maps/)
- [Kalkulator cen platformy Azure](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
- [Termin użytkowania usługi Azure Maps](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46) (zawarty w Postanowieniach dotyczących usług online firmy Microsoft)
- [Wybierz odpowiednią warstwę cenową w usłudze Azure Maps](https://docs.microsoft.com/azure/azure-maps/choose-pricing-tier)

## <a name="suggested-migration-plan"></a>Sugerowany plan migracji

Poniżej przedstawiono plan migracji wysokiego poziomu.

1. Korzystaj z zasobów seddków i usług Map Google używanych przez aplikację. Sprawdź, czy usługa Azure Maps udostępnia alternatywne zestawy SDK i usługi.
2. Jeśli jeszcze go nie masz, utwórz [https://azure.com](https://azure.com)subskrypcję platformy Azure w obszarze .
3. Utwórz konto usługi Azure Maps[(dokumentacja)](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys)i klucz uwierzytelniania lub usługę Azure Active Directory ([dokumentacja](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)).
4. Migrowanie kodu aplikacji.
5. Przetestuj zmigrowany wniosek.
6. Wdrażanie zmigrowanej aplikacji w procesach produkcyjnych.

## <a name="azure-maps-technical-resources"></a>Zasoby techniczne usługi Azure Maps

Oto lista przydatnych zasobów technicznych dla usługi Azure Maps.

- Przegląd:[https://azure.com/maps](https://azure.com/maps)
- Dokumentacji:[https://aka.ms/AzureMapsDocs](https://aka.ms/AzureMapsDocs)
- Przykłady kodu SDK sieci Web:[https://aka.ms/AzureMapsSamples](https://aka.ms/AzureMapsSamples)
- Fora dla deweloperów:[https://aka.ms/AzureMapsForums](https://aka.ms/AzureMapsForums)
- Filmy:[https://aka.ms/AzureMapsVideos](https://aka.ms/AzureMapsVideos)
- Blog:[https://aka.ms/AzureMapsBlog](https://aka.ms/AzureMapsBlog)
- Blog tech:[https://aka.ms/AzureMapsTechBlog](https://aka.ms/AzureMapsTechBlog)
- Opinie usługi Azure Maps (UserVoice):[https://aka.ms/AzureMapsFeedback](https://aka.ms/AzureMapsFeedback)
- [Notes jupyter usługi Azure Maps] [https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook]

## <a name="migration-support"></a>Wsparcie migracji

Deweloperzy mogą szukać pomocy technicznej migracji za pośrednictwem [forów](https://aka.ms/AzureMapsForums) lub za pośrednictwem jednej z wielu opcji pomocy technicznej platformy Azure:[https://azure.microsoft.com/support/options](https://azure.microsoft.com/support/options)

## <a name="next-steps"></a>Następne kroki

Poznaj szczegóły dotyczące migracji aplikacji Mapy Google za pomocą następujących artykułów:

> [!div class="nextstepaction"]
> [Migracja aplikacji internetowej](migrate-from-google-maps-web-app.md)

> [!div class="nextstepaction"]
> [Migracja aplikacji dla systemu Android](migrate-from-google-maps-android-app.md)

> [!div class="nextstepaction"]
> [Migracja usługi internetowej](migrate-from-google-maps-web-services.md)
