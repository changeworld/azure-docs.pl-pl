---
title: Wytyczne dotyczące środowiska skuteczne zakotwiczenia w kotwic przestrzenne Azure | Dokumentacja firmy Microsoft
description: Wskazówki i uwagi dotyczące tworzenia i lokalizowania kotwic skutecznie za pomocą kotwic przestrzenne Azure.
author: mattwojo
manager: jken
services: azure-spatial-anchors
ms.author: mattwoj
ms.date: 02/24/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 196958e4818251bd7f2ee78ca472e6f28292d908
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753151"
---
# <a name="guidelines-for-an-effective-anchor-experience-with-azure-spatial-anchors"></a>Wytyczne dotyczące elementu zakotwiczenia efektywne środowiska w usłudze Azure przestrzenne kotwic

Ten artykuł zawiera wskazówki i uwagi dotyczące tworzenia i lokalizowania kotwic skutecznie za pomocą kotwic przestrzenne Azure.

## <a name="creating-anchors"></a>Tworzenie zakotwiczenia

Tworzenia dobrych kotwic jest jednym z największym wpływie aspektów pracy z usługą Azure przestrzenne kotwic. Należy poświęcić czas szkolenie lub przeprowadzi użytkowników w Twojej UX jak tworzyć dobre. Zainwestować w na początku tworzenia zakotwiczenia, należy włączyć użytkownikom końcowym niezawodnie zlokalizować kotwic:

- Na różnych urządzeniach
- W różnym czasie
- W warunkach innego oświetlenia
- Z żądaną perspektyw w przestrzeni
- Itp.

Poniżej przedstawiono zagadnienia i wytyczne ułatwiające projektowanie środowiska tworzenia i lokalizacja zakotwiczenia.

## <a name="static-and-dynamic-locations"></a>Lokalizacje statycznych i dynamicznych

Element procesu projektowania środowisko kontrolne będzie wybranie lokalizacje, które uczestniczą. Zostanie lokalizacje mają być statyczna i zdefiniowanych przez administratora miejsce? Lub one będą dynamiczne i zdefiniowane przez użytkownika?

Menedżer magazynu sprzedaży detalicznej, może być statyczne doświadczenie w sklepie, aby zachęcić użytkowników do odwiedzenia. Podczas gdy deweloper gra planszowa mieszanej prawdopodobnie chce, aby powiadomić użytkowników, wybierz miejsce odtworzyć.

Dla lokalizacji statyczne można nauczyć Administratorzy mogą poświęcać czasu na curating obszaru, dobrym kotwic.

Dla lokalizacji dynamicznych możesz pomyśleć o jak edukować lub prowadzą użytkowników w Twojej UX tworzyć dobre.

## <a name="stable-visual-features"></a>Stałe funkcje visual

Wizualne śledzenie systemy używane w rzeczywistości mieszanej i rzeczywistość rozszerzona urządzeń polegają na funkcje środowiska programu visual. Aby uzyskać najbardziej niezawodnych środowiska:  

- Tworzyć kotwic w lokalizacjach za pomocą funkcji visual stabilne (oznacza to, funkcje, które nie zmieniają się często).

- Nie twórz kotwice w dużych powierzchni puste o nie wyróżniający charakterystyce.

- Nie należy tworzyć kotwic wysoce odbijającą materiałów.

- Nie należy tworzyć kotwic na powierzchniach, gdzie powtarzane tylko wzorca, takich jak Dywan lub zmianę tapety.

![Przykładowe obrazy reprezentujący środowiska dobre i złe](./media/stable-visual.png)

## <a name="consider-various-viewing-perspectives"></a>Należy wziąć pod uwagę różne perspektywy wyświetlania

Podczas tworzenia elementu zakotwiczenia, należy wziąć pod uwagę osób, które będzie można znaleźć w dalszej części można zlokalizować zakotwiczenia.

Na przykład, należy rozważyć kotwicy w środku miejsca przy użyciu dwóch drzwi. Prawdopodobnie chcesz zezwolić na użytkowników, którzy wejść do pokoju z obu drzwi do zlokalizowania zakotwiczenia. Podczas tworzenia zakotwiczenia, należy przeskanować położenia z obu drzwi. Przechwytuje dane o środowisko wokół zakotwiczenia z obu perspektyw, dzięki czemu użytkownicy mogą zlokalizować zakotwiczenia z obu drzwi biblioteki.

Ogólnie rzecz biorąc podczas tworzenia elementu zakotwiczenia, powinny być przeskanowane z różnych miejsc lub perspektywy, czy ma być stały, podczas próby jego znalezienie.  

Przy umieszczaniu wirtualny zawartość na aktywność na świeżym powietrzu rzeźby, dobrym pomysłem będzie zapoznaj się z całym rzeźby, oglądając, jak utworzyć zakotwiczenia.  

Z drugiej strony jeśli zakotwiczenia znajduje się w górnym roku pokoju, istnieje tylko w jednym kierunku z podejścia do. Podczas tworzenia zakotwiczenia, po prostu musi skanowanego z tej perspektywy.

## <a name="multiple-anchors"></a>Kilka punktów kontrolnych

Oświetlenie mogą się przyczynić visual funkcji, które są wykrywane. Kotwice utworzone w silne fizyczne światła może być trudny do lokalizowanie po ciemny w obszarze oświetlenia i na odwrót.  

Jeśli wystąpi ten problem, może pomóc utworzyć dwie kotwice — jeden w czasu letniego, a drugi w obszarze oświetlenia — w tym samym miejscu. Aplikację można następnie utworzyć zapytanie względem dla obu zakotwiczenia. Jeśli albo znajdują się, aplikacja będzie mieć ułożenia dla zakotwiczenia. 

Podobnie w środowiskach, gdzie funkcje visual zmienić, ponieważ większość obiektów przenieść kilka punktów kontrolnych może pomóc. W przypadku elementu zakotwiczenia staje się zbyt trudne do zlokalizowania ze względu na istotne zmiany w środowisku, można zastąpić ją na nową. Może to być tak, na przykład w sklepie detalicznym gdzie układu są odświeżane co kilka miesięcy.

## <a name="targets-and-rooms"></a>Cele i pokoje

W wielu przypadkach lokalizowania elementu zakotwiczenia reprezentuje punkt wejścia środowiska unikatowy Twojej aplikacji. Będziesz chciał szybko przy użyciu tego kroku i niezawodnie tak użytkownicy mogą wprowadzać swoje środowisko. Poświęcania czasu na jak prowadzą do Twojej kotwic jest krokiem ważnych projektu. Warto to porównać dwa scenariusze szerokiego: **Obiekty docelowe** i **pokojów**.

### <a name="targets"></a>Cele

![Zacznij tutaj edycji](./media/start-here-edit.png)

W tym scenariuszu docelowej lokalizacji elementu zakotwiczenia jest dobrze znana. Na przykład w fikcyjnej aplikacji rysowania MR jeden użytkownik umieszcza wirtualnego kanwę na tablicy. Powoduje ona, że inni użytkownicy w pomieszczeniu aby wskazywały swoich urządzeń, w tym samym miejscu na tablicy, aby zlokalizować zakotwiczenia i rozpocząć proces.  

Inny przykład scenariusz docelowy musi być znakiem z informacją "Skanuj w poszukiwaniu ofertami" podczas oczekiwania w wierszu w kawiarni. Kawiarni — wcześniej umieścił w tym miejscu elementu zakotwiczenia. Jak użytkownicy skanowania logowania, ich zlokalizować zakotwiczenia, a następnie wprowadź środowisko AR okazje na kawę.

W tym scenariuszu docelowego może pomóc zdjęcia. Jeśli użytkownicy mogą wyświetlić zdjęcie na swoim urządzeniu zamierzonym obiektem docelowym, mogą szybko identyfikować co do skanowania w świecie rzeczywistym. Na przykład może pomóc użytkownikom pojawić się w pobliżu ogólne docelowej przy użyciu GPS. Po odebraniu użytkownik aplikacji przedstawia zdjęcie elementu docelowego. Użytkownik wygląda wokół miejsca, wyszukuje element docelowy i przechodzi do skanowania w poszukiwaniu zakotwiczenia.

### <a name="rooms"></a>pokoje

![Skanowanie miejsca](./media/scan-room.png)

W tym scenariuszu miejsca użytkownicy wprowadzają spację po prostu, wiedząc, że występuje gdzieś kotwicy w tym miejscu. Użytkownicy skanowanie miejsca za pomocą swoich urządzeń i szybko znaleźć zakotwiczenia.

Zazwyczaj osiągnięcie to środowisko wymaga, tworzenie kotwic dobrze wyselekcjonowane, zgodnie z opisem w [należy wziąć pod uwagę różne perspektywy wyświetlania](#consider-various-viewing-perspectives) wcześniej. Pokoju został zeskanowany z wielu perspektyw, tworząc zakotwiczenia, następnie użytkowników może skanować praktycznie dowolnym miejscu podczas próby jego znalezienie.

Zasadniczo osobą odpowiedzialną za tworzenie zakotwiczenia zużywa więcej czasu na skanowanie miejsca, dzięki czemu osoby, które są dostarczane później można skanować i Znajdź zakotwiczenia szybko. Jest to ważne kompromis, które należy wziąć pod uwagę do własnych potrzeb.

Aplikacji rysowania MR omówionych powyżej jest przykładowi, który nie jest dobrze nadaje się jako scenariusza miejsca. W tym miejscu użytkownika, umieszczając zakotwiczenia chce, aby inne osoby do dołączenia do środowiska szybko. Użytkownicy nie chcesz poczekać, aż jest dobrze zeskanowane, aby rozpocząć proces. Ponieważ wszystkim użytkownikom dokładnie wiemy, gdzie na ich urządzenia, aby zlokalizować kotwic punktu, w tym scenariuszu lepiej jest traktowany jako scenariusz docelowy.

## <a name="effectively-locating-anchors"></a>Efektywne lokalizowanie zakotwiczenia

Wizualne śledzenie systemy opierają się na cech wizualnych środowiska funkcji. Bardziej wizualny funkcje są częścią skanowania, tym większe prawdopodobieństwo znajdowania elementu zakotwiczenia.

Istnieją pewne ogólne wytyczne, które można wykonać w celu tworzenia środowiska użytkownika, które wspiera przydatne skanowania środowiska.

Po pierwsze Jeśli użytkownik nie znajdzie kotwicy w ciągu kilku sekund, aplikacji powinny zachęcać użytkownikom przenoszenie urządzenia, aby przechwycić więcej perspektywy.  Aplikacja może również zachęcać samych użytkowników do poruszania się po środowiska podczas skanowania pod kątem zakotwiczenia z perspektywy więcej. Więcej perspektywy, z których urządzenie będzie widział lepiej tych samych punktów.

W przypadku scenariuszy docelowego należy poprosić użytkownika o zmieniają położenie obiektu docelowego, korzystając z możliwości wyświetlania jej z różnych perspektyw. Innymi słowy należy poprosić użytkownika o przenoszenia ich m do przechwytywania obiekt docelowy z nowej perspektywy, dopóki nie znajduje się kotwica.

W przypadku scenariuszy pokoju poprosić użytkownika o powoli skanowania pomieszczeniu. Na przykład poprosić użytkownika o Obróć do przechwytywania, 180 lub nawet 360 stopni pokoju. Lub poproś użytkownika, aby przejść do nowej perspektywy w pomieszczeniu. Najbardziej znaczący oznacza, że skanowanie jest skanowanie w pomieszczeniu. To przechwytuje więcej funkcji visual środowiska niż say skanowanie pobliskich tablicy. Skanowanie pobliskich tablicy nie przechwytuje jako wiele przydatnych funkcji visual środowiska.

Nie jest to przydatne przenieść urządzenia do równoległego wielokrotnie podczas wyszukiwania elementu zakotwiczenia. Spowoduje to po prostu przechwytywać tych samych punktów z tego samego punktu widzenia.

## <a name="testing-the-experience"></a>Testowanie w środowisku

Powyższe zawierają ogólne wytyczne. Z przestrzennego zakotwiczenia platformy Azure podczas pisania aplikacji współdziałających ze świata rzeczywistego. W efekcie należy przeznaczyć czas testowania scenariuszy zakotwiczenia Twojej aplikacji w rzeczywistych środowiskach. Jest to szczególnie istotne w przypadku środowisk, które są reprezentatywne, z którego można spodziewać się użytkownikom korzystanie z aplikacji.