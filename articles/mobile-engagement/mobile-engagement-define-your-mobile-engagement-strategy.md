---
title: Definiowanie strategii Mobile Engagement | Microsoft Docs
description: Dowiedz się, jak dołączyć i zoptymalizować strategię Mobile Engagement przy użyciu analizy i powiadomień wypychanych.
services: mobile-engagement
documentationcenter: Mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 7533e318-81b9-4360-aace-b7be8225985b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 494dd27acfd59eb79da13dae9bbbe0f80ee41370
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="define-your-mobile-engagement-strategy"></a>Definiowanie strategii Mobile Engagement
> [!IMPORTANT]
> Usługa Azure Mobile Engagement zostanie wycofana w dniu 31-03-2018. Ta strona zostanie usunięta wkrótce potem.
> 

*Twoja aplikacja powstała z jednego powodu: aby Twoi użytkownicy mogli jej używać!*

Wiemy, że stworzenie wspaniałej aplikacji, którą pokochają użytkownicy, wymagało dużego wysiłku. Ponadto najprawdopodobniej pozyskanie użytkowników było związane ze znaczną inwestycją w zakresie marketingu. Niemniej po początkowej, radosnej fazie wielkiego zainteresowania użytkowników możesz zauważyć, że powoli przestają oni używać Twojej aplikacji. *Właśnie o to chodzi w usłudze Azure Mobile Engagement!*: utrzymanie użytkowników i umożliwienie stopniowego usprawniania aplikacji poprzez testowanie i uczenie się.

Nasze podejście do poprawiania przechowywania i użycia opiera się na angażowaniu użytkowników aplikacji poprzez powiadomienia wypychane oraz komunikaty wewnątrz aplikacji, z wykorzystaniem specjalnej metody — nasze komunikaty i wiadomości są dopasowane do użytkowników; cała komunikacja jest dostosowana do zachowania użytkownika w aplikacji. Naszym celem jest umożliwienie komunikowania się z odpowiednimi odbiorcami, w odpowiedniej chwili i w odpowiednim miejscu.

Jednak żeby to osiągnąć, musisz rozpocząć od *zrozumienia swoich użytkowników*, a następnie utworzyć grupy w oparciu o zachowania lub cechy użytkowników (nazywamy je segmentami) i dopiero wtedy zająć się tworzeniem odpowiedniej komunikacji dla każdego segmentu.

## <a name="mobile-engagement-serves-your-objectives"></a>Strategia Mobile Engagement służy Twoim celom
*Wspomnieliśmy o przechowywaniu i użyciu, ale właściwie po co?*

Tworzenie strategii Mobile Engagement wymaga przyjrzenia się celom aplikacji i kluczowym wskaźnikom wydajności (KPI).

Rozpocznij od zdefiniowania celów i wskaźników KPI, które pomogą Ci określić przypadki użycia związane z zaangażowaniem z odpowiedniej perspektywy.

Przypadki użycia to prosta lista kampanii, które chcesz wdrożyć, aby komunikować się ze swoimi użytkownikami, od prostego powitania po bardzo zaawansowane użyteczne powiadomienia wyzwalane przez system IT. Dobrze skonstruowany przypadek użycia musi zawierać co najmniej trzy elementy, tj. *co, kto, kiedy*:

1. Bardzo krótka nazwa (np. „Kampania powitalna”).
2. **Co**: przykładowy komunikat (np. „Cieszymy się, że jesteś z nami! Zaloguj się, aby uzyskać pierwszy miesiąc za darmo!”). Ten komunikat nie jest w żadnym wypadku wersją ostateczną; możesz zmienić go w dowolnym momencie. Niemniej przykład często pomoże skupić się na tym, co chcemy powiedzieć.
3. **Kto**: segment, który otrzyma ten komunikat (np. „Wszyscy użytkownicy, którzy uruchomili aplikację po raz pierwszy 3 dni temu, odwiedzili stronę logowania, ale nie zalogowali się do aplikacji”).
   * Tak, możesz to zrobić bardzo łatwo przy użyciu usługi Azure Mobile Engagement :)
   * Ponownie nie musi to być ostateczne rozwiązanie, ponieważ możesz zdefiniować segmenty w dowolnym momencie. Jednak ważne jest możliwie wczesne zdefiniowanie kryteriów segmentacji, dzięki czemu zapewnisz zbieranie odpowiednich danych.
4. **Kiedy**: czas wdrożenia kampanii. Może być to wybrany dzień lub czas po określonym działaniu (w oparciu o wyzwalacz). Usługa Mobile Engagement oferuje szeroką gamę możliwości, dzięki którym można odpowiednio ustalić czas komunikacji.

Po zdefiniowaniu przypadków użycia i segmentów przechodzimy do wskazówek związanych z definiowaniem danych, które muszą być zbierane w aplikacji. Jest to zadanie *„planu tagu”*. Plan tagu umożliwia upewnienie się, że zbieranie danych jest dostosowane do deweloperów. W związku z tym deweloperzy mogą osadzić usługę Mobile Engagement z odpowiednią konfiguracją, umożliwiając pracę z kampaniami przy użyciu odpowiednich danych. Również bardzo ważne jest przeprowadzenie testów, dzięki którym zapewnia się, że integracja jest prawidłowa i zbiera się odpowiednie dane.

W oparciu o integrację, po opublikowaniu aplikacji, jako marketingowiec będziesz w stanie wyświetlać analizy w czasie rzeczywistym, segmentować odbiorców i wysyłać inteligentne, nakierowane powiadomienia wypychane, aby angażować użytkowników końcowych w aplikacji lub poza nią.

### <a name="use-cases-to-get-started"></a>Przypadki użycia — wprowadzenie
1. Strategia powitalna: utwórz kilka kampanii z wykorzystaniem powiadomień wypychanych opartych na zachowaniu użytkownika końcowego w momencie uruchomienia aplikacji, aby ponownie zaangażować użytkowników w D+2/5/10/15 po pierwszej sesji i zwiększyć przechowywanie po pierwszym uruchomieniu aplikacji.
2. Promuj nową zawartość (funkcję, artykuł/wideo lub produkt) w oparciu o zachowanie użytkownika końcowego, aby wysyłać informacje tylko do tych użytkowników końcowych, którzy najprawdopodobniej zareagują na komunikat.
3. Oceń aplikację: nakieruj komunikat na mniej niż 1 procent użytkowników w bazie, którzy najprawdopodobniej ocenią aplikację na 5 gwiazdek w sklepie.
4. Zwiększ wykorzystanie subskrypcji: promuj cenną zawartość, przedstawiając ją użytkownikom końcowym, którzy jeszcze jej nie widzieli, aby zwiększyć liczbę subskrypcji.
5. Samouczek: koniec obowiązkowych samouczków dla wszystkich. Zamiast tego możesz stworzyć wspaniałe samouczki wewnątrz aplikacji i wyzwalać je poprzez komunikaty w aplikacji tylko wtedy, gdy użytkownik wydaje się nie korzystać z aplikacji lub ma trudności z używaniem danej funkcji.

## <a name="why-do-you-need-analytics-to-engage"></a>Dlaczego do zaangażowania użytkowników potrzebna jest analiza?
Na tym etapie można już wywnioskować, że utworzenie jednego powiadomienia wypychanego do wszystkich użytkowników nie wystarczy. Kluczową ideą usługi Mobile Engagement jest pomaganie marketingowcom i deweloperom w angażowaniu odpowiednich użytkowników końcowych w odpowiednim momencie i w odpowiednim miejscu. Aby poznać te trzy kluczowe aspekty, ważne jest zbieranie danych analitycznych z aplikacji i ich wykorzystanie do segmentowania odbiorców. Takie rozwiązanie jest jeszcze bardziej skuteczne, jeśli segmenty oparte na zachowaniach uzupełniają dane pochodzące z innej bazy danych, systemu CRM lub rozwiązań opartych na wielu kanałach. Usługa Mobile Engagement umożliwia zbieranie danych z dowolnego miejsca i wykorzystuje je do określania odpowiednich odbiorców.

Aby działać w możliwie najlepszym kontekście podczas angażowania swoich odbiorców, niezwykle ważne jest posiadanie odpowiedniej wiedzy o zachowaniach użytkowników końcowych i możliwość wglądu w ich stan w czasie rzeczywistym. Gromadzenie danych umożliwia marketingowcom skupienie się na tym, co naprawdę istotne, aby mogli wykorzystywać przypadki użycia i osiągać strategiczne cele w marketingu na urządzeniach przenośnych. Osiąganie wcześniej ustalonych celów to powód, dla którego najlepszą praktyką nie jest zbieranie wszystkich możliwych danych do analizy, a jedynie tych, które umożliwiają skupienie się na tym, czego chcemy się dowiedzieć i co umożliwia wykorzystanie przypadków użycia. Jest to dobry sposób na początek: spróbować, przetestować i dowiedzieć się, jak używać rozwiązania, a następnie zaadresować inteligentne powiadomienia wypychane i zwiększyć przechowywanie aplikacji, co przełoży się na osiągnięcie sukcesu.

> [!NOTE]
> Pamiętaj: za dużo danych to złe rozwiązanie!
> 
> 

### <a name="use-cases-and-best-practices"></a>Przypadki użycia i najlepsze praktyki
W poniższych sekcjach omówiono pokrótce niektóre kluczowe przypadki użycia nadsyłane przez naszych klientów, dzięki którym łatwiej będzie rozpocząć pracę.

#### <a name="media"></a>Multimedia
Zbieraj dane o typie zawartości, którą wykorzystują użytkownicy końcowi, a następnie segmentuj odbiorców w oparciu o te zachowania, aby nakierowywać wybrane typy zawartości na odbiorców, którzy najprawdopodobniej z nich skorzystają. Takie rozwiązanie pomaga uniknąć spamowania całej bazy użytkowników i zapewnia lepsze przechowywanie.

#### <a name="m-commerce"></a>M-commerce
Zbieraj dane o kategoriach produktów najczęściej odwiedzanych w aplikacji i nakieruj działania na odbiorców, aby promować oferty lub nowe produkty w danej kategorii, co zwiększa szansę na dokonanie zakupu przez użytkownika końcowego. Staraj się zwiększyć przychody. Celem nie jest spamowanie użytkowników!

#### <a name="gaming"></a>Gry
Zbieraj dane o poziomie gry użytkownika końcowego i czasie spędzonym w grze w danym okresie, aby nakierować działania na odbiorców, którzy utknęli w danym momencie i chętniej przejdą do następnego poziomu przy użyciu oferty bonusowej.

Przekazuj informacje o konkretnych zdarzeniach, zachęcając użytkowników, którzy nie grali od dłuższego czasu, aby spróbować zachęcić ich do powrotu.

#### <a name="retail"></a>Sprzedaż detaliczna
Zbieraj dane o produktach i markach, które najprawdopodobniej wybiorą odbiorcy w oparciu o ich ulubione produkty lub zachowania i motywuj odbiorców do wizyt w sklepie, aby zwiększyć swoje przychody z zakupów.

#### <a name="banking"></a>Bankowość
Zbieraj dane od użytkowników końcowych, którzy utworzyli konto podczas pierwszego uruchomienia aplikacji. Staraj się wdrożyć strategię powitalną z nakierowanymi powiadomieniami wypychanymi, aby zwiększyć liczbę subskrypcji kont.

### <a name="how-to-create-a-great-tag-plan"></a>Jak utworzyć wspaniały plan tagu?
Plan tagu musi być swoistym opisem ścieżki użytkownika lub rodzajem przepływu pracy aplikacji zawierającym wszystkie niezbędne tagi (dane), które należy zebrać, aby zapewnić odpowiednią analizę, zrozumieć zachowania użytkownika i odpowiednio segmentować bazę użytkowników. Nie jest to proces techniczny. W związku z tym marketingowcy mogą określać dane, które chcą zebrać, w oparciu o strategię Mobile Engagement.

Jako minimum uznaje się tagowanie co najmniej wszystkich ekranów (nazwanych *działaniami* w usłudze Mobile Engagement) aplikacji. Dzięki temu można ustalić ścieżkę użytkownika.

Działanie może zawierać osadzone *zdarzenia*, które zbierają informacje o działaniu, np. kliknięcie przycisku. Umożliwia to zbieranie danych o interakcji w aplikacji. Dzięki temu marketingowcy mogą dowiedzieć się, które ekrany odwiedzają użytkownicy i co robią w aplikacji.

`Jobs`to działania o czasie trwania. Bardzo przydatna dla marketingowca jest możliwość zrozumienia, jak długo użytkownik tworzy konto lub loguje się do aplikacji itp. Ponadto jest to przydatna funkcja dla deweloperów, którzy mogą monitorować czas wywoływania usługi sieci Web.

`Errors`można również monitorować, aby dowiedzieć się, czy użytkownicy mają problemy w aplikacji. Na przykład częste problemy z nawiązywaniem połączenia.

Wszystkie te typy danych można rozszerzyć za pomocą parametrów (*extra-information* w usłudze Mobile Engagement), które umożliwiają zbieranie dynamicznych danych z aplikacji. Jest to niezwykle ważne, jeśli chcemy skorzystać z precyzyjnej segmentacji. Przykładowo marketingowcy mogą segmentować użytkowników w oparciu o typ zawartości, z którego korzystają. Typ zawartości będzie dynamiczną informacją działania lub zdarzenia.

*Informacje o aplikacji* to dane, które umożliwiają potwierdzenie stanu aplikacji lub użytkownika w czasie rzeczywistym. Pomaga to również w kategoryzowaniu bazy odbiorców i szybkim nakierowywaniu działań. Przykładowo można użyć stanu prawda/fałsz dla logowania użytkownika lub daty wygaśnięcia subskrypcji.

#### <a name="example-of-tags"></a>Przykład tagów
*Przypadek użycia: segmentowanie zachowań odbiorców w celu nakierowania odpowiedniej zawartości powiadomienia wypychanego na odpowiedniego użytkownika końcowego*

1. Wysyłaj powiadomienia wypychane, aby promować kategorię lub produkt: zbieraj dane o zachowaniach, aby segmentować odbiorców w oparciu o kategorię lub produkt, który odwiedzili x razy w danym okresie czasu lub z uwzględnieniem konkretnego produktu, który dodali do koszyka. Zebrane dane umożliwią segmentowanie odbiorców i wysyłanie powiadomień wypychanych do odpowiednich użytkowników.
2. Oceń aplikację: zbieraj dane w oparciu o zawartość udostępnioną przez odbiorców w sieciach społecznościowych. Celem takiego działania jest segmentowanie odbiorców poprzez określenie *ambasadorów* aplikacji. W przypadku korzystania z powiadomień wypychanych w aplikacji ambasadorzy będą najlepszymi odbiorcami aplikacji, których możesz poprosić o ocenienie aplikacji w sklepie na 5 gwiazdek.
   
   ![][1]

*Przypadek użycia: dane deklaratywne*

1. Wiadomości w formie alertu dla segmentu: zbieraj dane deklaratywne, aby segmentować odbiorców w oparciu o ich preferencje. Dzięki temu możesz wysyłać powiadomienia wypychane na dany temat do odbiorców, którzy będą naprawdę zainteresowani.
2. Segmentuj odbiorców w oparciu o stan logowania. Zbieraj dane, aby dowiedzieć się, czy użytkownik nawiązał połączenie lub utworzył konto. Pomaga nakierować działania na użytkowników końcowych, którzy jeszcze się nie zalogowali, i wysyła powiadomienia wypychane, które mają zachęcić użytkowników końcowych do skorzystania z aplikacji.
   ![][2]

### <a name="next-steps"></a>Następne kroki
* Odwiedź stronę [Mobile Engagement Concepts] \(Pojęcia dotyczące usługi Mobile Engagement), aby dowiedzieć się więcej o podstawowych pojęciach dotyczących usługi Mobile Engagement.
* Odwiedź stronę [Create a Mobile Engagement App](mobile-engagement-create.md) (Tworzenie aplikacji usługi Mobile Engagement), aby utworzyć nową kolekcję aplikacji usługi Mobile Engagement na platformie Azure i rozpocząć zarządzanie aplikacjami za pomocą portalu Mobile Engagement.
* Odwiedź stronę [Best practices](mobile-engagement-getting-started-best-practices.md) (Najlepsze praktyki), aby uzyskać szczegółowe informacje.
* Odwiedź stronę [Gaming App scenario](mobile-engagement-gaming-scenario.md) (Scenariusz aplikacji do grania), aby dowiedzieć się więcej o implementacji usługi Mobile Engagement przy użyciu prostej aplikacji do grania. 
* Odwiedź stronę [Media App scenario](mobile-engagement-media-scenario.md) (Scenariusz aplikacji multimedialnej), aby dowiedzieć się więcej o implementacji usługi Mobile Engagement przy użyciu prostej aplikacji multimedialnej. 
* Odwiedź stronę [Samouczki], aby dowiedzieć się więcej o implementacji.

<!-- Images. -->
[1]: ./media/mobile-engagement-define-your-mobile-engagement-strategy/use-case1.png
[2]: ./media/mobile-engagement-define-your-mobile-engagement-strategy/use-case2.png

<!-- URLs. -->
[Mobile Engagement Concepts]: http://azure.microsoft.com/documentation/articles/mobile-engagement-concepts/
[Samouczki]: http://azure.microsoft.com/documentation/articles/mobile-engagement-ios-get-started/

