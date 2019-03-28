---
title: Wytyczne dotyczące środowiska zakotwiczenia skuteczne, używających kotwic przestrzenne platformy Azure | Dokumentacja firmy Microsoft
description: Wskazówki i uwagi do tworzenia i zlokalizuj kotwic skutecznie za pomocą kotwic przestrzenne Azure.
author: mattwojo
manager: jken
services: azure-spatial-anchors
ms.author: mattwoj
ms.date: 02/24/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 9e77dcd96ffa0fbd57aa0ed1b4f857279ca768a7
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58520806"
---
# <a name="create-an-effective-anchor-experience-by-using-azure-spatial-anchors"></a>Tworzenie środowiska skuteczne zakotwiczenia przy użyciu kotwic przestrzenne platformy Azure

Ten artykuł zawiera wskazówki i zagadnienia, które ułatwiają efektywne tworzenie i zlokalizuj kotwic za pomocą przestrzenne kotwic.

## <a name="good-anchors"></a>Dobre zakotwiczenia

Przestrzenne kotwic pomoże Ci w utworzeniu dobre zakotwiczenia. Należy inwestować czas szkolenie lub przeprowadzi użytkowników w Twojej użytkownika środowiska do tworzenia dobrych kotwic. Inwestowanie w tworzeniu dobre kotwic na początku, ułatwić użytkownikom końcowym wyszukiwanie niezawodnie kotwic:

- Na różnych urządzeniach.
- W różnym czasie.
- W warunkach innego oświetlenia.
- Z żądaną perspektyw w przestrzeni.

## <a name="static-and-dynamic-locations"></a>Lokalizacje statycznych i dynamicznych

Element procesu projektowania środowisko kontrolne jest wybranie lokalizacji. Zostanie lokalizacje mają być statyczna i zdefiniowanych przez administratora miejsce? Lub zostaną one, dynamiczne i zdefiniowanych przez użytkownika?

Menedżer magazynu sprzedaży detalicznej, może być statyczne doświadczenie w sklepie, aby zachęcić użytkowników do odwiedzenia. Jednak Deweloper gra planszowa rzeczywistości mieszanej, prawdopodobnie będzie chciała Pozwól użytkownikom wybrać, gdzie można odtworzyć.

Dla lokalizacji statyczne możesz nauczyć Administratorzy mogą poświęcać czasu na curating obszaru, dobrym kotwic.

Dla lokalizacji dynamicznych możesz pomyśleć o jak, które prowadzisz lub prowadzą użytkowników w Twojej UX tworzyć dobre.

## <a name="stable-visual-features"></a>Stałe funkcje visual

Wizualne śledzenie systemy używane w rzeczywistości mieszanej i urządzeń w rzeczywistości rozszerzonej polegają na funkcje środowiska programu visual. Aby uzyskać najbardziej niezawodnych środowiska:  

- *Czy* tworzyć kotwic w lokalizacjach, które mają stałe funkcje visual (oznacza to, funkcje, które nie zmieniają się często).

- *Nie* tworzyć kotwic na dużych puste powierzchniach, które nie mają wyróżniający cech.

- *Nie* tworzyć kotwic wysoce odbijającą materiałów.

- *Nie* tworzyć kotwic na powierzchniach, w których wzorzec się powtarza, takich jak Dywan lub zmianę tapety.

![Przykłady to dobre środowisko kotwic i nieprawidłowe środowisko do kotwic](./media/stable-visual.png)

## <a name="various-viewing-perspectives"></a>Różnych perspektyw wyświetlania

Podczas tworzenia elementu zakotwiczenia, należy wziąć pod uwagę osób, które później spróbuje znaleźć zakotwiczenia.

Na przykład, należy rozważyć kotwicy w środku pokoju, który ma dwa drzwi. Prawdopodobnie chcesz zezwalać użytkownikom na wprowadzanie pomieszczeniu z obu drzwi biblioteki. Podczas tworzenia zakotwiczenia, należy przeskanować położenia z obu drzwi. Możesz zmienić perspektywy, aby przechwytywać dane środowiska, wokół zakotwiczenia, dzięki czemu użytkownicy mogą zlokalizować zakotwiczenia z obu drzwi biblioteki.

Ogólnie rzecz biorąc podczas tworzenia elementu zakotwiczenia, zeskanować przy użyciu perspektywy osób, które spróbuje go zlokalizować. Więc jeśli chcesz umieścić zawartość wirtualnego na aktywność na świeżym powietrzu rzeźby, dobrym pomysłem będzie zapoznaj się z całym rzeźby, podczas skanowania, jak utworzyć zakotwiczenia. W przypadku zakotwiczenia w rogu pokoju, istnieje tylko w jednym kierunku z podejścia do. Podczas tworzenia tego zakotwiczenia, można skanować go tylko na podstawie tej perspektywy.

## <a name="multiple-anchors"></a>Kilka punktów kontrolnych

Oświetlenie mogą się przyczynić w funkcjach visual, wykryte przez aplikację. Kotwice utworzone w silne fizyczne światła może być trudny do zlokalizuj w świetle sztuczny i na odwrót.  

Jeśli ten problem, może pomóc utworzyć dwie kotwice. W tym samym miejscu należy utworzyć jeden zakotwiczenia w czasu letniego, a drugi w świetle sztucznym. Aplikację można następnie utworzyć zapytanie względem dla obu zakotwiczenia. Gdy znajduje się albo zakotwiczenia, aplikacja będzie mieć ułożenia dla zakotwiczenia. 

Podobnie w środowiskach, gdzie funkcje visual zmienić, ponieważ większość obiektów przenieść kilka punktów kontrolnych może pomóc. W przypadku elementu zakotwiczenia staje się zbyt trudne do znalezienia ze względu na istotne zmiany w środowisku, można zastąpić zakotwiczenia nową. Może to zrobisz, na przykład w sklepie detalicznym gdzie układu są odświeżane co kilka miesięcy.

## <a name="targets-and-rooms"></a>Cele i pokoje

W wielu przypadkach elementu zakotwiczenia jest punktem wejścia do środowiska aplikacji. Będziesz chciał szybko przy użyciu tego kroku i niezawodnie tak użytkownicy mogą wprowadzać swoje środowisko. Poświęcania czasu na jak prowadzą do Twojej kotwic jest krokiem ważnych projektu. Warto wziąć pod uwagę znajdowanie kotwic pod względem dwa scenariusze szerokiego: *cele* i *pokojów*.

### <a name="targets"></a>Obiekty docelowe

W tym scenariuszu docelowej lokalizacji elementu zakotwiczenia jest dobrze znana. Na przykład w aplikacji fikcyjnej malowania rzeczywistości mieszanej, jeden użytkownik umieszcza wirtualnego kanwę na tablicy. Powoduje ona, że inni użytkownicy w pomieszczeniu aby wskazywały swoich urządzeń, w tym samym miejscu na tablicy, aby zlokalizować zakotwiczenia i rozpocząć proces.  

Inny przykład scenariusz docelowy może być znakiem w kawiarni, która odczytuje "Skanuj w poszukiwaniu okazje." Kawiarni — został umieszczony w tym miejscu elementu zakotwiczenia. Jak użytkownicy skanowania logowania, ich zlokalizować zakotwiczenia, a następnie wprowadź środowisko rzeczywistości rozszerzonej, aby znaleźć oferty na kawę.

W tym scenariuszu docelowego może pomóc zdjęcia. Jeśli użytkowników można wyświetlić zdjęcie docelową na swoim urządzeniu, mogą szybko zidentyfikować, co do skanowania w świecie rzeczywistym. Na przykład może pomóc użytkownikom pojawić się w obszarze Ogólne zamierzonym obiektem docelowym za pomocą GPS. Po odebraniu użytkownik aplikacji przedstawia zdjęcie elementu docelowego. Użytkownik wygląda wokół miejsca, wyszukuje element docelowy i skanowania pod kątem zakotwiczenia.

![Ilustracja elementu zakotwiczenia wyświetlanie zdjęć elementu docelowego na urządzeniu przenośnym użytkownika](./media/start-here-edit.png)

### <a name="rooms"></a>pokoje

W tym scenariuszu miejsca użytkownicy wprowadzają spację po prostu, wiedząc, że występuje gdzieś kotwicy w tym miejscu. Użytkownicy skanowanie miejsca za pomocą swoich urządzeń i szybko znaleźć zakotwiczenia.

Środowisko to zwykle wymaga utworzenia zakotwiczenia dobrze wyselekcjonowane, zgodnie z opisem w różnych perspektyw wyświetlania. Po utworzeniu zakotwiczenia skanowany pokoju z wielu perspektyw, użytkownicy może skanować praktycznie dowolnym miejscu przy próbie jego znalezienie.

![Ilustracja przedstawiająca sposób użytkownik może skanować miejsca można znaleźć elementu zakotwiczenia](./media/scan-room.png)

Zasadniczo możesz poświęcić więcej czasu, na skanowanie miejsce po utworzeniu zakotwiczenia tak, aby użytkownicy nowszej można skanować i szybko zlokalizować zakotwiczenia. Jak utworzyć środowisko, należy wziąć pod uwagę ten ważne kompromis.

Przykład aplikacji rysowania rzeczywistości mieszanej, omówionej wcześniej nie działa również jako scenariusza miejsca. W tym miejscu użytkownik, który umieszcza zakotwiczenia chce innym użytkownikom szybko Dołącz do środowiska. Użytkownicy nie mają być zaczekać z rozpoczęciem doświadczenia, dopóki dobrze jest skanowany w pomieszczeniu. Ponieważ wszystkim użytkownikom dokładnie wiemy, gdzie wskazywały na swoim urządzeniu, aby zlokalizować kotwic, ten przykład sprawdzi się najlepiej jako scenariusz docelowy.

## <a name="anchor-location"></a>Lokalizacja zakotwiczenia

Systemy śledzenia Visual opierają się na funkcje visual w środowisku. Bardziej wizualny funkcje, które obejmuje skanowania, tym większe prawdopodobieństwo znajdowania elementu zakotwiczenia.

Postępuj zgodnie z ogólnymi wytycznymi w tej sekcji, aby zbudować środowiska użytkownika, które wspiera przydatne skanowania środowiska.

Po pierwsze Jeśli użytkownik nie znajdzie kotwicy w ciągu kilku sekund, aplikacji powinny zachęcać użytkownikom przenoszenie urządzenia, aby przechwycić więcej perspektywy. Aplikacja również może zachęcić użytkowników do poruszania się środowisko do skanowania w poszukiwaniu zakotwiczenia z perspektywy więcej. Więcej perspektywy funkcji, które widzi urządzenia, tym lepiej.

W przypadku scenariuszy docelowego należy poprosić użytkownika o zmieniają położenie obiektu docelowego do wyświetlania go z różnych perspektyw. Innymi słowy poproś użytkownika o Przechwyć obiekt docelowy z nowej perspektywy, aż do zlokalizowania zakotwiczenia.

W przypadku scenariuszy pokoju poprosić użytkownika o powoli skanowania pomieszczeniu. Na przykład poprosić użytkownika o włączyć do przechwytywania 180 stopni lub nawet 360 stopni pokoju. Lub poprosić użytkownika, aby wyświetlić miejsce z nowej perspektywy. 

Najbardziej znaczący metoda polega na skanowanie w pomieszczeniu. Skanowanie w pomieszczeniu przechwytuje więcej funkcji visual środowiska niż skanowanie pobliskich tablicy, na przykład. Skanowanie pobliskich tablicy nie będzie przechwytywać tyle przydatne funkcje programu visual środowiska.

Nie jest to przydatne wielokrotnie przenieść urządzenie bok, podczas wyszukiwania elementu zakotwiczenia. Umożliwia to po prostu Przechwytywanie tych samych punktów z tego samego punktu widzenia.

## <a name="experience-tests"></a>Środowisko testów

W tym artykule zostały omówione ogólne wytyczne. Z przestrzennego kotwic pisania aplikacji współdziałających ze świata rzeczywistego. Z tego powodu należy przeznaczyć czas i testowania aplikacji zakotwiczenia scenariuszy w rzeczywistych środowiskach. Jest to szczególnie istotne w przypadku środowisk, które reprezentują których można oczekiwać od użytkowników do korzystania z aplikacji.
