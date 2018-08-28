---
title: Wprowadzenie do akustyki — Cognitive Services
description: Wtyczka Project Acoustics Unity zapewnia okluzję, pogłos i przestrzenność dla projektów, których medium będzie rzeczywistość wirtualna i tradycyjne ekrany.
services: cognitive-services
author: kegodin
manager: noelc
ms.service: cognitive-services
ms.component: acoustics
ms.topic: overview
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: 08c6d8b8e229aabcc05fdfde2d8ffadf3a6346c2
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/17/2018
ms.locfileid: "40181005"
---
# <a name="what-is-project-acoustics"></a>Co to jest akustyka projektu?
Wtyczka Project Acoustics Unity zapewnia okluzję, pogłos i przestrzenność dla projektów, których medium będzie rzeczywistość wirtualna i tradycyjne ekrany. Zapewnia ona sposób projektowania akustyki w grach, który nakłada intencje projektanta na opartą na fizyce symulację fal.

## <a name="why-use-acoustics-in-virtual-environments"></a>Dlaczego warto używać akustyki w wirtualnych środowiskach?
Ludzie używają wskazówek audiowizualnych do poznawania otaczającego ich świata. W wirtualnych światach połączenie dźwięku przestrzennego z akustyką zwiększa imersję użytkownika. Opisane tutaj narzędzie akustyki analizuje wirtualne światy i tworzy realistyczną symulację akustyki, a także obsługuje proces projektowania po symulacji. Analiza obejmuje zarówno geometrię, jak i materiały każdej powierzchni w świecie. Symulacja uwzględnia parametry takie jak kierunek nadejścia (portaling), siła odbicia, czasy zanikania, oraz efekty okluzji i obstrukcji.

## <a name="how-does-this-approach-to-acoustics-work"></a>Jak działa to podejście do akustyki?
System opiera się na obliczaniu świata wirtualnego w trybie offline, co zapewnia bardziej złożoną symulację niż w przypadku analizy przeprowadzanej w czasie wykonywania. Efektem obliczeń w trybie offline jest tabela odnośników z parametrami akustycznymi. Projektant określa reguły stosowane do parametrów w czasie wykonywania. Dzięki dostrojeniu tych reguł można osiągnąć hiperrealistyczne efekty zapewniające wysoką intensywność emocjonalną lub hiporealistyczne sceny w celu zwiększenia liczby dźwięków tła.

## <a name="design-process-comparison"></a>Porównanie procesów projektowania
Wtyczka Project Acoustics obsługuje nowy proces projektowania na potrzeby akustyki w scenach aparatu Unity. Aby wyjaśnić ten nowy proces projektowania, porównajmy go do jednego z popularnych obecnie podejść do akustyki.

### <a name="typical-approach-to-acoustics-today"></a>Typowe współczesne podejście do akustyki
W typowym współczesnym podejściu do akustyki rysowane są głośności odbić:

![Widok projektu](media/reverbZonesAltSPace2.png)

Następnie parametry są dostrajane dla każdego obszaru:

![Widok projektu](media/TooManyReverbParameters.png)

Na koniec dodaje się logikę śledzenia promienia w celu uzyskania odpowiedniego filtrowania okluzji i obstrukcji w scenie oraz logikę wyszukiwania ścieżek na potrzeby portalingu. Ten kod może zwiększać koszt w czasie wykonywania. Ma również problemy z płynnością za rogami oraz przypadki brzegowe w scenach o nieregularnych kształtach.

### <a name="an-alternative-approach-with-physics-based-design"></a>Alternatywne podejście z projektowaniem opartym na fizyce
W przypadku podejścia zapewnianego przez wtyczkę Project Acoustics Unity należy podać materiały i kształt statycznej sceny. Ponieważ scena jest przetwarzana na woksele, a proces nie używa śledzenia promienia, nie trzeba dostarczać uproszczonej ani hermetycznej siatki akustycznej. Nie trzeba też oznaczać sceny głośnościami odbić. Wtyczka przekazuje scenę do chmury, gdzie używana jest oparta na fizyce symulacja fal. Wynik jest integrowany z projektem jako tabela odnośników i można go modyfikować na potrzeby efektów estetycznych lub dotyczących rozgrywki.

![Widok projektu](media/GearsWithVoxels.jpg)

## <a name="requirements"></a>Wymagania
* Unity 2018.2+ na potrzeby tworzenia akustyki oraz Unity 5.2+ na potrzeby projektowania dźwięku i wdrażania
* Unity Editor — wersja 64-bitowa dla systemu Windows
* Subskrypcja usługi Azure Batch na potrzeby tworzenia akustyki
* Środowisko uruchomieniowe skryptów aparatu Unity musi mieć wartość „równoważne .NET 4.x”

## <a name="platform-support"></a>Obsługa platform
* Komputer z systemem Windows (x86 i AMD64)
* Windows UWP (x86, AMD64 i ARM)
* Android (x86 i ARM64)

## <a name="download"></a>Do pobrania
Jeśli interesuje Cię ocena wtyczki akustyki, zarejestruj się [tutaj](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRwMoAEhDCLJNqtVIPwQN6rpUOFRZREJRR0NIQllDOTQ1U0JMNVc4OFNFSy4u), aby dołączyć do programu Designer Preview.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [procesie projektowania](design-process.md)
* Rozpocznij [wprowadzanie akustyki do projektu Unity](getting-started.md)

