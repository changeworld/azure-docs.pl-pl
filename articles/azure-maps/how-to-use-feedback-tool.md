---
title: Podaj dane zwrotne do Azure Maps | Mapy Microsoft Azure
description: Prześlij opinię na temat danych za pomocą narzędzia do Microsoft Azure Maps — Opinie.
author: walsehgal
ms.author: v-musehg
ms.date: 08/19/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 59670742586f596a817ad52383160a38358a6786
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911382"
---
# <a name="provide-data-feedback-to-azure-maps"></a>Podaj dane zwrotne do Azure Maps

Azure Maps jest ogólnie dostępna od momentu 2018 maja, dostarczając dane z danych dotyczących mapy, łatwe do użycia interfejsy API REST i zaawansowane zestawy SDK do obsługi naszych klientów korporacyjnych w różnych przypadkach użycia biznesowego. Rzeczywista część świata jest zmieniana co sekundę i ma kluczowe znaczenie dla naszych klientów. Nasi klienci, którzy planują otwieranie i zamykanie udogodnień, muszą mieć pewność, że nasze mapy są aktualizowane bezzwłocznie, dzięki czemu mogą efektywnie planować dostarczanie, konserwację lub obsługę klienta w odpowiednich obiektach. Utworzyliśmy witrynę opinii o danych Azure Maps, aby umożliwić naszym klientom dostarczanie bezpośrednich opinii o danych. Opinie o danych klientów przechodzą bezpośrednio do naszych dostawców danych i ich edytorów mapy, którzy mogą szybko oszacować i dołączyć Opinie do naszych produktów mapowania.  

[Witryna opinii o danych Azure Maps](https://feedback.azuremaps.com) zapewnia klientom łatwą w obsłudze opinię na temat danych mapy, szczególnie w kwestiach związanych z zainteresowaniami i adresami lokalnymi. W tym artykule opisano sposób udostępniania różnych rodzajów opinii przy użyciu witryny Azure Maps opinię.

## <a name="add-a-business-place-or-a-residential-address"></a>Dodaj miejsce biznesowe lub adres mieszkalny 

Możesz podać opinię na temat brakującego punktu orientacyjnego lub adresu zamieszkania na mapie. Istnieją dwa sposoby, aby to zrobić, Otwórz witrynę opinii o danych mapy platformy Azure, a następnie wyszukaj współrzędne brakującej lokalizacji, a następnie kliknij pozycję "Dodaj miejsce".

  ![Wyszukaj brakującą lokalizację](./media/how-to-use-feedback-tool/search-poi.png)

Możesz też korzystać z mapy i klikać lokalizację, aby porzucić numer PIN we współrzędnych i kliknąć pozycję "Dodaj miejsce". 

  ![Dodaj kod PIN](./media/how-to-use-feedback-tool/add-poi.png)

Po kliknięciu nastąpi przekierowanie do formularza, aby podać odpowiednie szczegóły dotyczące miejsca.

  ![Dodaj miejsce](./media/how-to-use-feedback-tool/add-a-place.png)

## <a name="fix-a-business-place-or-a-residential-address"></a>Napraw miejsce biznesowe lub adres mieszkalny 

Witryna opinii umożliwia także wyszukiwanie i lokalizowanie miejsca biznesowego lub adresu oraz przekazywanie informacji zwrotnych w celu naprawienia adresu lub lokalizacji kodu PIN, jeśli nie są one poprawne. Aby przekazać opinię dotyczącą naprawienia adresu, użyj paska wyszukiwania, aby wyszukać miejsce pracy lub adres zamieszkania. Kliknij lokalizację zainteresowania na liście wyników, a następnie kliknij pozycję "Napraw to miejsce".

  ![Wyszukaj miejsce do rozwiązania](./media/how-to-use-feedback-tool/fix-place.png)

Aby przesłać opinię dotyczącą naprawienia adresu, Wypełnij formularz "Napraw miejsce", a następnie kliknij przycisk "Prześlij".

  ![Napraw formularz](./media/how-to-use-feedback-tool/fix-form.png)

Jeśli lokalizacja numeru PIN dla miejsca jest nieprawidłowa, zaznacz pole wyboru w formularzu "Napraw miejsce", w którym znajduje się komunikat "Lokalizacja numeru PIN jest niepoprawna" i Przenieś kod PIN do odpowiedniej lokalizacji, a następnie kliknij przycisk "Prześlij".

  ![Przenieś lokalizację numeru PIN](./media/how-to-use-feedback-tool/move-pin.png)

## <a name="add-a-comment"></a>Dodaj komentarz 

Oprócz tego, aby wyszukać lokalizację, narzędzie do przesyłania opinii umożliwia również dodanie bezpłatnego komentarza do tekstu formularza w celu uzyskania szczegółowych informacji dotyczących lokalizacji. Aby dodać wyszukiwanie komentarzy do lokalizacji lub kliknij lokalizację, a następnie kliknij pozycję "Dodaj komentarz", Napisz komentarz, a następnie kliknij pozycję "Prześlij". 

  ![dodaj komentarz](./media/how-to-use-feedback-tool/add-comment.png)

## <a name="track-status"></a>Śledzenie stanu 

Możesz również śledzić stan żądania, sprawdzając pole "chcę śledzić status" i dostarczając swój adres e-mail podczas wykonywania żądania. W wiadomości e-mail zostanie wyświetlony link do śledzenia z aktualnym stanem żądania. 

  ![stan opinii](./media/how-to-use-feedback-tool/feedback-status.png)


## <a name="next-steps"></a>Następne kroki

Aby ogłosić wszystkie pytania techniczne dotyczące Azure Maps, odwiedź stronę:

* [Azure Maps Stack Overflow](https://stackoverflow.com/questions/tagged/azure-maps)
* [Forum opinii Azure Maps](https://feedback.azure.com/forums/909172-azure-maps)
