---
title: Wskazówki dotyczące skutecznych doświadczeń kotwiczenia
description: Wskazówki i zagadnienia dotyczące efektywnego tworzenia i lokalizowania zakotwiczeń przy użyciu zakotwiczeń przestrzennych platformy Azure.
author: mattwojo
manager: jken
services: azure-spatial-anchors
ms.author: mattwoj
ms.date: 02/24/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 9a24da8d76f401f534eccf33312fbf0c2bee9f5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74270518"
---
# <a name="create-an-effective-anchor-experience-by-using-azure-spatial-anchors"></a>Tworzenie efektywnego środowiska kotwiczenia przy użyciu zakotwiczeń przestrzennych platformy Azure

Ten artykuł zawiera wskazówki i zagadnienia ułatwiające skuteczne tworzenie i lokalizowanie zakotwiczeń przy użyciu zakotwiczeń przestrzennych.

## <a name="good-anchors"></a>Dobre kotwice

Przestrzenne kotwice ułatwią tworzenie dobrych kotwic. Ważne jest, aby poświęcić czas na edukowanie lub prowadzenie użytkowników w interfejsie użytkownika (UX), aby tworzyć dobre kotwice. Inwestując w tworzenie dobrych kotwic z góry, pomagasz użytkownikom końcowym niezawodnie znaleźć kotwice:

- Na różnych urządzeniach.
- W różnych momentach.
- W różnych warunkach oświetleniowych.
- Z pożądanych perspektyw w przestrzeni.

## <a name="static-and-dynamic-locations"></a>Lokalizacje statyczne i dynamiczne

Częścią projektowania środowiska kotwicy jest wybór lokalizacji. Czy lokalizacje będą statyczne i zdefiniowane przez administratora przestrzeni? A może będą one dynamiczne i definiowane przez użytkownika?

Menedżer sklepu detalicznego może chcieć statyczne środowisko w sklepie, aby zachęcić użytkowników do odwiedzenia. Ale twórca gry planszowej rzeczywistości mieszanej prawdopodobnie chciałby pozwolić użytkownikom wybrać, gdzie grać.

W przypadku lokalizacji statycznych można nauczyć administratorów spędzania czasu na nadzorowaniu przestrzeni za pomocą dobrych kotwic.

W przypadku dynamicznych lokalizacji należy zastanowić się, w jaki sposób nauczasz lub poprowadzisz użytkowników w ux do tworzenia dobrych kotwic.

## <a name="stable-visual-features"></a>Stabilne funkcje wizualne

Systemy śledzenia wizualnego stosowane w urządzeniach rzeczywistości mieszanej i rozszerzonej rzeczywistości opierają się na wizualnych cechach środowiska. Aby uzyskać najbardziej niezawodne doświadczenie:

- *Czy* tworzyć kotwice w lokalizacjach, które mają stabilne funkcje wizualne (czyli funkcje, które nie zmieniają się często).

- *Nie* należy tworzyć kotew na dużych powierzchniach pustych, które nie mają właściwości wyróżniających.

- *Nie* twórz kotew na wysoce odblaskowych materiałach.

- *Nie* twórz kotwic na powierzchniach, na których wzór się powtarza, na przykład dywan lub tapeta.

![Przykłady dobrego środowiska dla kotew i złego środowiska dla kotew](./media/stable-visual.png)

## <a name="various-viewing-perspectives"></a>Różne perspektywy wyświetlania

Podczas tworzenia kotwicy, pomyśl o ludziach, którzy później spróbują zlokalizować kotwicę.

Rozważmy na przykład kotwicę w środku pomieszczenia, który ma dwa drzwi. Prawdopodobnie chcesz zezwolić użytkownikom na wejście do pokoju z obu drzwi. Podczas tworzenia kotwy należy zeskanować jej pozycję z obu drzwi. Można zmienić perspektywy przechwytywania danych środowiska wokół kotwicy, dzięki czemu użytkownicy mogą zlokalizować kotwicę z obu drzwi.

Ogólnie rzecz biorąc, podczas tworzenia kotwicy, zeskanuj ją z perspektywy ludzi, którzy spróbują ją zlokalizować. Więc jeśli umieszczasz wirtualne treści na rzeźbie na świeżym powietrzu, warto chodzić po rzeźbie, podczas skanowania jej, tworząc kotwicę. Jeśli kotwica znajduje się w rogu pokoju, jest tylko jeden kierunek, aby zbliżyć się do niej. Podczas tworzenia tej kotwicy można ją zeskanować tylko z tej perspektywy.

## <a name="multiple-anchors"></a>Wiele kotew

Oświetlenie może mieć wpływ na funkcje wizualne wykryte przez aplikację. Kotwice stworzone w silnym naturalnym świetle mogą być trudne do zlokalizowania w sztucznym świetle i odwrotnie.

Jeśli masz ten problem, może pomóc w utworzeniu dwóch kotwic. W tym samym miejscu utwórz jedną kotwicę w świetle dziennym, a drugą w sztucznym świetle. Aplikacja może następnie wysyłać zapytania do obu zakotwiczeń. Gdy znajduje się albo kotwica, aplikacja będzie miała pozę do kotwicy.

Podobnie w środowiskach, w których funkcje wizualne zmieniają się, ponieważ większość obiektów się porusza, wiele zakotwiczeń może pomóc. Gdy zakotwiczenie staje się zbyt trudne do znalezienia z powodu istotnych zmian w środowisku, można zastąpić kotwicę nową. Można to zrobić, na przykład w sklepie detalicznym, gdzie układ jest odświeżany co kilka miesięcy.

## <a name="targets-and-rooms"></a>Cele i pomieszczenia

W wielu przypadkach kotwica jest punktem wejścia do środowiska aplikacji. Będziesz chciał przejść przez ten krok szybko i niezawodnie, aby użytkownicy mogli wprowadzić swoje doświadczenie. Spędzanie czasu na tym, jak użytkownicy znajdą swoje kotwice, jest ważnym krokiem projektowym. Warto pomyśleć o znalezieniu kotwic w kategoriach dwóch ogólnych scenariuszy: *celów* i *pomieszczeń.*

### <a name="targets"></a>Obiekty docelowe

W scenariuszu docelowym lokalizacja kotwicy jest dobrze znana. Na przykład w fikcyjnej aplikacji do malowania rzeczywistości mieszanej jeden użytkownik umieszcza wirtualne płótno na ścianie. Instruuje innych użytkowników w pokoju, aby skierowali swoje urządzenia w tym samym miejscu na ścianie, aby zlokalizować kotwicę i rozpocząć doświadczenie.

Innym przykładem scenariusza docelowego może być znak w kawiarni z napisem "Skanuj w poszukiwaniu ofert". Kawiarnia umieściła tutaj kotwicę. Gdy użytkownicy skanują znak, lokalizują kotwicę i wchodzą do rozszerzonej rzeczywistości, aby znaleźć oferty na kawę.

W scenariuszu docelowym zdjęcia mogą pomóc. Jeśli pokażesz użytkownikom zdjęcie zamierzonego celu na swoim urządzeniu, mogą szybko określić, co mają skanować w świecie rzeczywistym. Na przykład użytkownik może pomóc użytkownikom w dotarcie do ogólnego obszaru zamierzonego celu za pomocą gps. Po nadejdzie użytkownik aplikacji wyświetla zdjęcie obiektu docelowego. Użytkownik rozgląda się po przestrzeni, znajduje obiekt docelowy i skanuje w poszukiwaniu kotwicy.

![Ilustracja kotwicy przedstawiająca zdjęcie obiektu docelowego na urządzeniu przenośnym użytkownika](./media/start-here-edit.png)

### <a name="rooms"></a>Pokoje

W scenariuszu pokoju użytkownicy wchodzą do przestrzeni, wiedząc, że gdzieś jest tu kotwica. Użytkownicy skanują przestrzeń za pomocą urządzenia i szybko lokalizują kotwicę.

To środowisko zazwyczaj wymaga tworzenia dobrze wyselekcjonowanych kotwic, zgodnie z omówieną w różnych perspektywach wyświetlania. Jeśli przeskanowano pomieszczenie z wielu perspektyw podczas tworzenia kotwicy, użytkownicy mogą skanować niemal wszędzie, gdy próbują ją zlokalizować.

![Ilustracja przedstawiająca sposób skanowania pomieszczenia w celu znalezienia kotwicy](./media/scan-room.png)

Zasadniczo spędzasz więcej czasu na skanowaniu miejsca podczas tworzenia kotwicy, aby późniejsi użytkownicy mogli szybko skanować i lokalizować kotwicę. Tworząc swoje doświadczenie, musisz wziąć pod uwagę ten ważny kompromis.

Przykład aplikacji do malowania rzeczywistości mieszanej, którą omówiliśmy wcześniej, nie działa dobrze jako scenariusz pokoju. W tym miejscu użytkownik, który umieszcza kotwicę, chce, aby inni szybko dołączyli do środowiska. Użytkownicy nie chcą czekać, aby rozpocząć doświadczenie, aż pokój jest dobrze skanowane. Ponieważ wszyscy użytkownicy wiedzą dokładnie, gdzie należy wskazać swoje urządzenie, aby zlokalizować kotwice, w tym przykładzie działa lepiej jako scenariusz docelowy.

## <a name="anchor-location"></a>Lokalizacja kotwicy

Wizualne systemy śledzenia opierają się na funkcjach wizualnych w środowisku. Im więcej funkcji wizualnych zawiera skanowanie, tym większe prawdopodobieństwo znalezienia kotwicy.

Postępuj zgodnie z ogólnymi wytycznymi w tej sekcji, aby utworzyć środowisko użytkownika, który zachęca do użytecznego skanowania środowiska.

Po pierwsze, jeśli użytkownik nie znajdzie kotwicy w ciągu kilku sekund, aplikacja powinna zachęcić użytkowników do przeniesienia urządzenia, aby uchwycić więcej perspektyw. Aplikacja może również zachęcić użytkowników do poruszania się po środowisku, aby skanować w poszukiwaniu kotwicy z większej perspektywy. Im więcej perspektyw funkcji, które urządzenie widzi, tym lepiej.

W przypadku scenariuszy docelowych poproś użytkownika o poruszanie się po okolicy obiektu docelowego, aby wyświetlić go z różnych perspektyw. Innymi słowy, poproś użytkownika, aby przechwycił obiekt docelowy z nowych perspektyw, aż do zlokalizowania kotwicy.

W przypadku scenariuszy pomieszczeń poproś użytkownika o powolne skanowanie pomieszczenia. Na przykład poproś użytkownika, aby obrócił się, aby uchwycić 180 stopni lub nawet 360 stopni pomieszczenia. Możesz też poprosić użytkownika o wyświetlenie pokoju z nowej perspektywy.

Najbardziej miarodajna jest skanowanie w całym pomieszczeniu. Skanowanie w całym pomieszczeniu rejestruje więcej cech wizualnych środowiska niż na przykład skanowanie pobliskiej ściany. Skanowanie pobliskiej ściany nie przechwytuje tylu przydatnych cech wizualnych środowiska.

Wielokrotne przesuwanie urządzenia z boku na bok podczas szukania kotwicy nie jest pomocne. To po prostu oddaje te same punkty z tej samej perspektywy.

## <a name="experience-tests"></a>Testy doświadczenia

W tym artykule omówiliśmy ogólne wytyczne. Za pomocą kotwic przestrzennych piszesz aplikacje, które wchodzą w interakcję ze światem rzeczywistym. Z tego powodu należy poświęcić czas na testowanie scenariuszy kotwicy aplikacji w rzeczywistych środowiskach. Jest to szczególnie ważne w przypadku środowisk, które reprezentują, gdzie można oczekiwać, że użytkownicy będą korzystać z aplikacji.
