---
title: Często zadawane pytania dotyczące usługi Azure Spatial Anchors | Microsoft Docs
description: Azure Spatial Anchors to zarządzana usługa w chmurze i platforma programistyczna dla wielu użytkowników i urządzeń umożliwiająca korzystanie ze środowisk rzeczywistości mieszanej na urządzeniach HoloLens oraz na urządzeniach z systemem iOS i Android. Ta lista często zadawanych pytań zawiera odpowiedzi na pytania dotyczące usługi z technicznego punktu widzenia.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: overview
ms.service: azure-spatial-anchors
ms.openlocfilehash: cd004e1eb6a40648c52dd3b5aee0ec38bff8f9b3
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2019
ms.locfileid: "66304185"
---
# <a name="frequently-asked-questions-about-azure-spatial-anchors"></a>Często zadawane pytania dotyczące usługi Azure Spatial Anchors

Azure Spatial Anchors to zarządzana usługa w chmurze i platforma programistyczna dla wielu użytkowników i urządzeń umożliwiająca korzystanie ze środowisk rzeczywistości mieszanej z orientacją przestrzenną na urządzeniach HoloLens oraz na urządzeniach z systemem iOS i Android.

Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Spatial Anchors](overview.md).

## <a name="azure-spatial-anchors-product-faqs"></a>Listy często zadawanych pytań dotyczące produktu Azure Spatial Anchors

**Pyt.: Jakie urządzenia obsługuje usługa Azure Spatial Anchors?**

**Odp.:** Usługa Azure Spatial Anchors umożliwia programistom tworzenie aplikacji na urządzeniach HoloLens, na urządzeniach z systemem iOS z obsługą platformy ARKit oraz na urządzeniach z systemem Android z obsługą platformy ARCore. W przypadku systemów iOS i Android obejmuje to zarówno telefony, jak i tablety.

**Pyt.: Czy aby używać usługi Azure Spatial Anchors konieczne jest połączenie z chmurą?**

**Odp.:** Usługa Azure Spatial Anchors obecnie wymaga połączenia sieciowego z Internetem. Zachęcamy do zgłaszania komentarzy na naszej [witrynie opinii](https://feedback.azure.com/forums/919252-azure-spatial-anchors).

**Pyt.: Jakie są wymagania dotyczące łączności dla usługi Azure Spatial Anchors?**

**Odp.:** Usługa Azure Spatial Anchors działa z połączeniami Wi-Fi oraz mobilnymi połączeniami szerokopasmowymi.

**Pyt.: Z jaką dokładnością usługa Azure Spatial Anchors może znajdować kotwice?**

**Odp.:** Na dokładność znajdowania kotwic ma wpływ wiele czynników — warunki oświetleniowe, obiekty znajdujące się w środowisku, a nawet powierzchnia, na której znajduje się kotwica. Aby określić, czy dokładność będzie odpowiadać Twoim potrzebom, spróbuj użyć kotwic w środowiskach reprezentatywnych dla planowanego miejsca użycia. W przypadku środowisk, dla którym dokładność nie spełnia Twoich potrzeb, zobacz [Logging and diagnostics in Azure Spatial Anchors (Rejestrowanie i diagnostyka w usłudze Azure Spatial Anchors)](./concepts/logging-diagnostics.md).

**Pyt.: Jak długo trwa tworzenie i znajdowanie kotwic?**

**Odp.:** Czas wymagany do tworzenia i znajdowania kotwic zależy od wielu czynników — połączenia sieciowego, przetwarzania na urządzeniu i jego obciążenia oraz danego środowiska. Nasi klienci tworzą aplikacje w wielu branżach, takich jak produkcja przemysłowa, handel detaliczny i przemysł gier, co wskazuje, że usługa zapewnia świetne środowisko użytkownika dla ich scenariuszy.

## <a name="privacy-faq"></a>Często zadawane pytania dotyczące ochrony prywatności

**Pyt.: Czy gdy moja aplikacja umieszcza w pewnym miejscu kotwicę przestrzenną, to wszystkie aplikacje mają do niej dostęp?**

**Odp.:** Kotwice są izolowane przez konto platformy Azure. Tylko aplikacje, dla których przyznano dostęp do Twojego konta, będą mogły uzyskać dostęp do kotwic w ramach tego konta.

**Pyt.: Jakie informacje o środowisku są przesyłane i przechowywane w ramach usługi podczas używania usługi Azure Spatial Anchors? Czy obrazy środowiska są przesyłane i przechowywane?**

**Odp.:** Podczas tworzenia lub znajdowania kotwic obrazy środowiska są przetwarzane na urządzeniu w formacie pochodnym. Ten format pochodny jest przesyłany i przechowywany w ramach usługi.

Aby zapewnić przejrzystość, poniżej znajduje się obraz środowiska i pochodna chmura punktów rozrzedzonych. Chmura punktów przedstawia geometryczną reprezentację środowiska, która jest przesyłana i przechowywane w usłudze. Dla każdego punktu w chmurze punktów rozrzedzonych przesyłamy i przechowujemy jego skrót właściwości wizualnych. Skrót jest tworzony na podstawie danych pikseli, ale nie zawiera żadnych z nich.

Usługa Azure Spatial Anchors jest zgodna z [postanowieniami umowy serwisowej platformy Azure](https://go.microsoft.com/fwLink/?LinkID=522330&amp;amp;clcid=0x9) i [oświadczeniem firmy Microsoft o ochronie prywatności](https://go.microsoft.com/fwlink/?LinkId=521839&amp;clcid=0x409).

![Środowisko i jego pochodna chmury punktów rozrzedzonych](./media/sparse-point-cloud.png)
*Rysunek 1.: Środowisko i jego pochodna chmury punktów rozrzedzonych*


**Pyt.: Czy istnieje sposób, aby przesłać informacje diagnostyczne do firmy Microsoft?**

**Odp.:** Tak. W usłudze Azure Spatial Anchors istnieje tryb diagnostyczny, z którego mogą korzystać deweloperzy za pomocą interfejsu API usługi Azure Spatial Anchors. Jest to przydatne, gdy na przykład napotkasz środowisko, w którym nie ma możliwości tworzenia ani przewidywalnego znajdowania kotwic. Możemy Cię poprosić o przesłanie raportu diagnostyki zawierającego informacje pomocne w debugowaniu. Aby uzyskać więcej informacji, zobacz [Logging and diagnostics in Azure Spatial Anchors (Rejestrowanie i diagnostyka w usłudze Azure Spatial Anchors)](./concepts/logging-diagnostics.md).

## <a name="availability-and-pricing-faqs"></a>Często zadawane pytania dotyczące dostępności i cennika

**Pyt.: Czy oferowana jest umowa SLA?**

**Odp.:** Standardem dla usług platformy Azure jest to, że staramy się zapewnić dostępność większą niż 99,9%. Należy zauważyć, że usługa Azure Spatial Anchors jest obecnie w wersji zapoznawczej i w związku z tym mają względem niej zastosowanie [Uzupełniające warunki korzystania z Wersji Zapoznawczych](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

**Pyt.: Czy mogę publikować moje aplikacje w sklepach z aplikacjami przy użyciu usługi Azure Spatial Anchors? Czy mogę używać usługi Azure Spatial Anchors na potrzeby scenariuszy produkcyjnych o kluczowym znaczeniu?**

**Odp.:** Usługa Azure Spatial Anchors jest obecnie w wersji zapoznawczej i w tym czasie zachęcamy do tworzenia aplikacji, [przesyłania opinii](https://feedback.azure.com/forums/919252-azure-spatial-anchors) o produkcie i planowania wdrożeń produkcyjnych.

Daty wprowadzania wersji ogólnie dostępnej zostaną ogłoszone wkrótce.

**Pyt.: Czy istnieją jakiekolwiek limity ograniczania?**
 
**Odp.:** Tak, istnieją.  Nie oczekujemy, że zostaną osiągnięte podczas zwykłego tworzenia aplikacji i ich testowania. W przypadku wdrożeń produkcyjnych jesteśmy gotowi do obsługi wymagań naszych klientów związanych z wysoką skalowalnością. [Skontaktuj się z nami](mailto:azuremrs@microsoft.com) w celu omówienia tych zagadnień. W tej fazie wersji zapoznawczej nie została jeszcze opublikowana struktura warstw i cen, ale nastąpi to wkrótce.

**Pyt.: W jakich regionach jest dostępna usługa Azure Spatial Anchors?**

**Odp.:** Aktualnie konto usługi Azure Spatial Anchors można utworzyć w regionie Wschodnie stany USA 2. Oznacza to, że w tym regionie dostępne są zarówno zasoby obliczeniowe, jak i zasoby magazynowe wymagane przez tę usługę. Nie ma jednak żadnych ograniczeń związanych z tym, gdzie znajdują się klienci. W przyszłości zamierzamy rozszerzyć dostępność regionalną usługi do wszystkich podstawowych regionów platformy Azure.

**Pyt.: Czy za korzystanie z usługi Azure Spatial Anchors naliczane są opłaty? Czy kiedykolwiek będzie miało to miejsce?**

**Odp.:** Szczegółowe informacje o cenach podczas okresu zapoznawczego można znaleźć na naszej [stronie z cennikiem](https://azure.microsoft.com/pricing/details/spatial-anchors/).

## <a name="technical-faqs"></a>Często zadawane pytania techniczne

**Pyt.: W jaki sposób działa usługa Azure Spatial Anchors?**

**Odp.:** Usługa Azure Spatial Anchors jest zależna od modułów śledzących rzeczywistości mieszanej lub rzeczywistości rozszerzonej. Te moduły śledzące służą do obserwacji środowiska za pomocą kamer i śledzenia urządzenia w sześciu stopniach swobody, gdy przemieszcza się ono w przestrzeni.

Posiadając moduł śledzący działający w sześciu stopniach swobody jako blok konstrukcyjny, usługa Azure Spatial Anchors umożliwia określenie pewnych punktów orientacyjnych w rzeczywistym środowisku jako punktów zakotwiczenia. Możesz na przykład użyć punktu zakotwiczenia do renderowania zawartości w określonym miejscu w rzeczywistych warunkach.

Podczas tworzenia takiego punktu zestaw SDK klienta przechwytuje informacje o środowisku wokół tego punktu i przekazuje je do usługi. Jeśli inne urządzenie szuka kotwicy w tym samym miejscu, do usługi przesyłane są podobne dane. Te dane są dopasowywane do wcześniej zapisanych danych dotyczących środowiska. Pozycja kotwicy względem urządzenia zostanie następnie przesłana z powrotem w celu użycia w aplikacji.

**Pyt.: W jaki sposób usługa Azure Spatial Anchors integruje się z platformami ARKit i ARCore w systemach iOS i Android?**

**Odp.:** Usługa Azure Spatial Anchors wykorzystuje natywne możliwości śledzenia platform ARKit i ARCore. Ponadto nasze zestawy SDK dla systemów iOS i Android oferują funkcje takie jak utrwalanie kotwic w ramach zarządzanej usługi w chmurze oraz umożliwianie aplikacjom ponownego znajdowania tych kotwic za pomocą standardowego połączenia z usługą.

**Pyt.: W jaki sposób usługa Azure Spatial Anchors integruje się z urządzeniem HoloLens?**

**Odp.:** Usługa Azure Spatial Anchors wykorzystuje natywne możliwości śledzenia urządzenia HoloLens. Zapewniamy zestaw SDK usługi Azure Spatial Anchors do tworzenia aplikacji na urządzeniu HoloLens. Zestaw SDK integruje się z natywnymi funkcjami urządzenia HoloLens i udostępnia dodatkowe funkcje. Te funkcje umożliwiają deweloperom aplikacji utrwalanie kotwic w ramach zarządzanej usługi w chmurze oraz ponowne znajdowanie tych kotwic przez aplikacje dzięki nawiązaniu połączenia z usługą.

**Pyt.: Jakie platformy i języki są obsługiwane przez usługę Azure Spatial Anchors?**

**Odp.:** Deweloperzy mogą tworzyć aplikacje przy użyciu usługi Azure Spatial Anchors, korzystając z dobrze znanych narzędzi i platform dla ich urządzeń:

- Platforma Unity na urządzeniu HoloLens oraz w systemie iOS i Android
- Platforma SWIFT i Objective-C w systemie iOS
- Platforma Java lub zestaw Android NDK w systemie Android
- Platforma C++/ WinRT na urządzeniu HoloLens

Rozpocznij programowanie od zapoznania się z [dokumentacją](index.yml).

**Pyt.: Czy ta usługa współpracuje z aparatem Unreal?**

**Odp.:** Oczekujemy, że obsługa aparatu Unreal zostanie wkrótce wprowadzona.

**Pyt.: Czy ta usługa współpracuje z platformą Xamarin?**

**Odp.:** Tak. Mimo że nie udostępniamy zestawu Xamarin SDK, oczekujemy, że deweloperzy mogą używać usługi Azure Spatial Anchors w ramach swoich aplikacji platformy Xamarin dzięki integracji z interfejsem API usługi Azure Spatial Anchors.
