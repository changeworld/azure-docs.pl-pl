---
title: Łagodzenie walki z Z
description: Opisuje techniki łagodzenia artefaktów walki z
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: bc06deafe3f589fce9a9178fefdb22388254929d
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680455"
---
# <a name="z-fighting-mitigation"></a>Łagodzenie walki z Z

Gdy dwie powierzchnie nakładają się na siebie, nie jest jasne, która z nich powinna być renderowana na drugiej. Wynik zmienia się nawet na piksel, co powoduje artefakty zależne od widoku. W związku z tym, gdy aparat lub siatka porusza się, te wzory migoczą zauważalnie. Ten artefakt nazywa *się z-walki*. W przypadku aplikacji AR i VR problem jest nasilany, ponieważ urządzenia montowane na głowie naturalnie zawsze się poruszają. Aby zapobiec funkcji łagodzenia dyskomfortu przeglądarki z-fighting jest dostępna w renderowaniu zdalnym platformy Azure.

## <a name="z-fighting-mitigation-modes"></a>Tryby łagodzenia walki z Z

|Sytuacji                        | Wynik                               |
|---------------------------------|:-------------------------------------|
|Regularne walki z z               |![Walki z Z](./media/zfighting-0.png)|
|Włączono łagodzenie walki z Z    |![Walki z Z](./media/zfighting-1.png)|
|Podświetlanie w szachownicy włączone|![Walki z Z](./media/zfighting-2.png)|

Poniższy kod umożliwia łagodzenie walki z:

``` cs
void EnableZFightingMitigation(AzureSession session, bool highlight)
{
    ZFightingMitigationSettings settings = session.Actions.ZFightingMitigationSettings;

    // enabling z-fighting mitigation
    settings.Enabled = true;

    // enabling checkerboard highlighting of z-fighting potential
    settings.Highlighting = highlight;
}
```

> [!NOTE]
> Łagodzenie walki z Z jest ustawieniem globalnym, które wpływa na wszystkie renderowane siatki.

## <a name="reasons-for-z-fighting"></a>Powody walki z z

Walka z Z odbywa się głównie z dwóch powodów:

1. gdy powierzchnie znajdują się bardzo daleko od kamery, dokładność ich wartości głębokości ulega pogorszeniu, a wartości stają się nie do odróżnienia
1. gdy powierzchnie w siatce fizycznie nakładają się na siebie

Pierwszy problem zawsze może się zdarzyć i jest trudny do wyeliminowania. Jeśli tak się stanie w aplikacji, upewnij się, że stosunek odległości *najbliższej płaszczyzny* do odległości *dalekiej płaszczyzny* jest tak niski, jak to praktyczne. Na przykład w pobliżu płaszczyzny w odległości 0,01 i dalekiej płaszczyzny w odległości 1000 spowoduje ten problem znacznie wcześniej, niż posiadanie w pobliżu płaszczyzny na 0,1 i dalekiej płaszczyzny w odległości 20.

Drugim problemem jest wskaźnik źle autorstwa treści. W świecie rzeczywistym dwa obiekty nie mogą znajdować się w tym samym miejscu w tym samym czasie. W zależności od aplikacji użytkownicy mogą chcieć wiedzieć, czy nakładające się powierzchnie istnieją i gdzie się znajdują. Na przykład scena CAD budynku, która jest podstawą konstrukcji w świecie rzeczywistym, nie powinna zawierać fizycznie niemożliwych przecięć powierzchni. Aby umożliwić oględziny, dostępny jest tryb podświetlania, który wyświetla potencjalne walki z jako animowany wzór szachownicy.

## <a name="limitations"></a>Ograniczenia

Pod warunkiem, że łagodzenie walki z jest najlepszym wysiłkiem. Nie ma gwarancji, że usuwa wszystkie z-walki. Ponadto automatycznie preferuje jedną powierzchnię nad drugą. Tak więc, gdy masz powierzchnie, które są zbyt blisko siebie, może się zdarzyć, że "zła" powierzchnia kończy się na górze. Częstym problemem jest to, że tekst i inne naklejki są stosowane do powierzchni. Dzięki łagodzeniu walki z umożliwiło te szczegóły mogą łatwo zniknąć.

## <a name="performance-considerations"></a>Zagadnienia dotyczące wydajności

* Włączenie łagodzenia walki z wiąże się z niewielkim obciążeniem dla wydajności.
* Dodatkowo włączenie nakładki walki z powoduje narzucanie nietrywialnego obciążenia wydajności, choć może się różnić w zależności od sceny.

## <a name="next-steps"></a>Następne kroki

* [Tryby renderowania](../../concepts/rendering-modes.md)
* [Ponowna proces opóźniania się w późnym etapie](late-stage-reprojection.md)
