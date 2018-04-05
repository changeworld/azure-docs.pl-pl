---
title: Pojęcia dotyczące usługi Mobile Engagement | Microsoft Docs
description: Pojęcia dotyczące usługi Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 8d19abd1-0a6c-4772-9fa5-5e99980ac5da
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 1bc09de37c1b2aca35ef1ea74227df770f15baf5
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="azure-mobile-engagement-concepts"></a>Pojęcia dotyczące usługi Azure Mobile Engagement
> [!IMPORTANT]
> Usługa Azure Mobile Engagement zostanie wycofana w dniu 31-03-2018. Ta strona zostanie usunięta wkrótce potem.
> 

Usługa Mobile Engagement definiuje kilka pojęć wspólnych dla wszystkich obsługiwanych platform. W tym artykule krótko opisano te pojęcia.

Ten artykuł stanowi dobry początek, jeśli dopiero zaczynasz korzystać z usługi Mobile Engagement. Należy również zapoznać się z dokumentacją specyficzną dla używanej platformy, ponieważ pozwoli to uściślić pojęcia opisane w tym artykule i uzyskać więcej szczegółów oraz przykładów, a także informacje o możliwych ograniczeniach.

## <a name="devices-and-users"></a>Urządzenia i użytkownicy
Usługa Mobile Engagement identyfikuje użytkowników, generując unikatowy identyfikator dla każdego urządzenia. Ten identyfikator jest określany jako identyfikator urządzenia (lub `deviceid`). Jest on generowany w taki sposób, że wszystkie aplikacje uruchomione na tym samym urządzeniu współużytkują ten sam identyfikator urządzenia.

Oznacza to, że usługa Mobile Engagement traktuje jedno urządzenie jako należące dokładnie do jednego użytkownika. W związku z tym użytkownicy i urządzenia stanowią równoważne pojęcia.

## <a name="sessions-and-activities"></a>Sesje i działania
Sesja stanowi jedno użycie aplikacji przez użytkownika i trwa od momentu rozpoczęcia korzystania z aplikacji przez użytkownika do momentu, gdy użytkownik skończy z niej korzystać.

Działanie stanowi jedno użycie danej części podrzędnej aplikacji przez jednego użytkownika (jest to zazwyczaj ekran, ale może to być dowolne działanie odpowiednie dla aplikacji).

Użytkownik może wykonywać tylko jedno działanie naraz.

Działanie jest identyfikowane przez nazwę (ograniczoną do 64 znaków) i opcjonalnie może mieć osadzone dodatkowe dane (ich limit to 1024 bajty).

Sesje są automatycznie obliczane na podstawie sekwencji działań wykonywanych przez użytkowników. Sesja rozpoczyna się, gdy użytkownik rozpoczyna swoje pierwsze działanie, i zatrzymuje się, gdy użytkownik kończy swoje ostatnie działanie. Oznacza to, że sesja nie musi być jawnie rozpoczynana ani zatrzymywana. Zamiast tego ma miejsce jawne rozpoczynanie i zatrzymywanie działań. Jeśli nie zostanie zgłoszone żadne działanie, nie zostanie zgłoszona żadna sesja.

## <a name="events"></a>Zdarzenia
Zdarzenia są używane do raportowania natychmiastowych akcji (na przykład naciśniętego przycisku lub artykułu przeczytanego przez użytkowników).

Zdarzenie może być związane z bieżącą sesją lub z uruchomionym zadaniem. Zdarzenie może też być autonomiczne.

Zdarzenie jest identyfikowane przez nazwę (ograniczoną do 64 znaków) i opcjonalnie może mieć osadzone dodatkowe dane (ich limit to 1024 bajty).

## <a name="error"></a>Błąd
Błędy są używane do zgłaszania problemów poprawnie wykrytych przez aplikację (na przykład nieprawidłowych akcji użytkownika lub niepowodzeń wywołania interfejsu API).

Błąd może być związany z bieżącą sesją lub z uruchomionym zadaniem. Błąd może też być autonomiczny.

Błędy są identyfikowane przez nazwę (ograniczoną do 64 znaków) i opcjonalnie mogą mieć osadzone dodatkowe dane (ich limit to 1024 bajty).

## <a name="job"></a>Zadanie
Zadania są używane do raportowania akcji mających czas trwania (na przykład czas trwania wywołań interfejsu API, czas wyświetlania reklam, czas trwania zadań w tle lub czas trwania akcji użytkowników).

Zadanie nie jest powiązane z sesją, ponieważ może być wykonywane w tle bez żadnej interakcji użytkownika.

Zadania są identyfikowane przez nazwę (ograniczoną do 64 znaków) i opcjonalnie mogą mieć osadzone dodatkowe dane (ich limit to 1024 bajty).

## <a name="crash"></a>Awaria
Awarie są generowane przez zestaw Mobile Engagement SDK w celu raportowania błędów aplikacji w sytuacji, gdy problemy niewykryte przez aplikację powodują jej awarię.

## <a name="application-information"></a>Informacje o aplikacji
Informacje o aplikacji służą do oznaczania użytkowników, czyli kojarzenia określonych danych z użytkownikami aplikacji (jest to podobne do plików cookie w sieci Web, z tą różnicą, że informacje o aplikacji są przechowywane po stronie serwera na platformie Azure Mobile Engagement).

Informacje o aplikacji można rejestrować przy użyciu interfejsu API zestawu Mobile Engagement SDK lub przy użyciu interfejsu API urządzenia platformy Mobile Engagement.

Informacje o aplikacji to para klucz/wartość skojarzona z urządzeniem. Klucz to nazwa informacji o aplikacji (maksymalnie 64 litery ASCII [a-zA-Z], cyfry [0-9] i znaki podkreślenia [_]). Wartość (maksymalnie 1024 znaki) może być dowolnym ciągiem, liczbą całkowitą, datą (format rrrr-MM-dd) lub wartością logiczną (true lub false).

Z urządzeniem można skojarzyć dowolną liczbę informacji o aplikacji w granicach limitu zdefiniowanego przez warunki cenowe usługi Mobile Engagement. Dla danego klucza usługa Mobile Engagement śledzi tylko najnowszy zestaw wartości (brak historii). Ustawienie lub zmiana informacji o aplikacji wymusza przeprowadzenie przez usługę Mobile Engagement ponownej oceny kryteriów odbiorców ustawionych dla tych informacji o aplikacji (jeśli istnieją), co oznacza, że informacje o aplikacji mogą służyć do wyzwalania wypychań w czasie rzeczywistym.

## <a name="extra-data"></a>Dodatkowe dane
Dodatkowe dane (dodatki) to dowolne dane, które mogą być dołączone do zdarzeń, błędów, działań i zadań.

Dodatki mają strukturę podobną do obiektów JSON: składają się z drzewa par klucz/wartość. Klucze są ograniczone do 64 liter ASCII [a-zA-Z], cyfr [0-9] i znaków podkreślenia [_]), a całkowity rozmiar dodatków jest ograniczony do 1024 znaków (po zakodowaniu w formacie JSON przez zestaw Mobile Engagement SDK).

Całe drzewo par klucz/wartość jest przechowywane jako obiekt JSON. Jednak tylko pierwszy poziom par klucz/wartość jest rozkładany, aby był dostępny dla niektórych zaawansowanych funkcji, takich jak segmenty (na przykład można łatwo zdefiniować segment o nazwie „Fani science fiction” składający się z wszystkich użytkowników, którzy w ciągu ostatniego miesiąca wysłali co najmniej 10 razy zdarzenie o nazwie „wyświetlono_zawartość” z dodatkowym kluczem „typ_zawartości” ustawionym na wartość „scifi”). Dlatego zdecydowanie zaleca się wysyłanie tylko dodatków składających się z prostej listy par klucz/wartość przy użyciu wartości skalarnych (na przykład ciągów, dat, liczb całkowitych lub wartości logicznych).

## <a name="next-steps"></a>Następne kroki
* [Omówienie uniwersalnego zestawu SDK systemu Windows dla usługi Azure Mobile Engagement](mobile-engagement-windows-store-sdk-overview.md)
* [Omówienie zestawu SDK programu Windows Phone Silverlight SDK dla usługi Azure Mobile Engagement](mobile-engagement-windows-phone-sdk-overview.md)
* [Zestaw SDK systemu iOS dla usługi Azure Mobile Engagement](mobile-engagement-ios-sdk-overview.md)
* [Zestaw SDK systemu Android dla usługi Azure Mobile Engagement](mobile-engagement-android-sdk-overview.md)

