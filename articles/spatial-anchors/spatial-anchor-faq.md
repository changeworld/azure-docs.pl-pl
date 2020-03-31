---
title: Często zadawane pytania
description: Często zadawane pytania dotyczące usługi Azure Spatial Anchors.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: overview
ms.service: azure-spatial-anchors
ms.openlocfilehash: 563e2da8eba228636b05db2112739fdead4a4aa3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76844891"
---
# <a name="frequently-asked-questions-about-azure-spatial-anchors"></a>Często zadawane pytania dotyczące usługi Azure Spatial Anchors

Azure Spatial Anchors to zarządzana usługa w chmurze i platforma programistyczna dla wielu użytkowników i urządzeń umożliwiająca korzystanie ze środowisk rzeczywistości mieszanej z orientacją przestrzenną na urządzeniach HoloLens oraz na urządzeniach z systemem iOS i Android.

Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Spatial Anchors](overview.md).

## <a name="azure-spatial-anchors-product-faqs"></a>Listy często zadawanych pytań dotyczące produktu Azure Spatial Anchors

**Pyt.: Jakie urządzenia obsługuje zakotwiczenia przestrzenne platformy Azure?**

**Odp.:** Zakotwiczenia przestrzenne platformy Azure umożliwiają deweloperom tworzenie aplikacji na urządzeniu HoloLens, na urządzeniach z systemem iOS z obsługą ARKit oraz na urządzeniach z systemem Android z obsługą ARCore; dla systemów iOS i Android obejmuje to zarówno telefony, jak i tablety.

**Pyt.: Czy muszę być połączony z chmurą, aby korzystać z zakotwiczeń przestrzennych platformy Azure?**

**Odp.:** Kotwice przestrzenne platformy Azure obecnie wymaga połączenia sieciowego z Internetem. Zachęcamy do zgłaszania komentarzy na naszej [witrynie opinii](https://feedback.azure.com/forums/919252-azure-spatial-anchors).

**Pyt.: Jakie są wymagania dotyczące łączności dla zakotwiczeń przestrzennych platformy Azure?**

**Odp.:** Platforma Azure Spatial Anchors współpracuje z siecią Wi-Fi i mobilnymi połączeniami szerokopasmowymi.

**Pyt.: Jak dokładnie można zakotwiczenia przestrzenne platformy Azure zlokalizować kotwice?**

**Odp.:** Wiele czynników wpływa na dokładność lokalizowania kotew - warunków oświetlenia, obiektów w środowisku, a nawet powierzchni, na której znajduje się kotwica. Aby określić, czy dokładność będzie odpowiadać Twoim potrzebom, spróbuj użyć kotwic w środowiskach reprezentatywnych dla planowanego miejsca użycia. W przypadku środowisk, dla którym dokładność nie spełnia Twoich potrzeb, zobacz [Logging and diagnostics in Azure Spatial Anchors (Rejestrowanie i diagnostyka w usłudze Azure Spatial Anchors)](./concepts/logging-diagnostics.md).

**Pyt.: Jak długo trwa tworzenie i lokalizowanie zakotwiczeń?**

**Odp.:** Czas wymagany do tworzenia i lokalizowania zakotwiczeń zależy od wielu czynników — połączenia sieciowego, przetwarzania i ładowania urządzenia oraz określonego środowiska. Nasi klienci tworzą aplikacje w wielu branżach, takich jak produkcja przemysłowa, handel detaliczny i przemysł gier, co wskazuje, że usługa zapewnia świetne środowisko użytkownika dla ich scenariuszy.

## <a name="privacy-faq"></a>Często zadawane pytania dotyczące ochrony prywatności

**Pyt.: Kiedy moja aplikacja umieszcza kotwicę przestrzenną gdzieś, czy wszystkie aplikacje mają do niej dostęp?**

**Odp.:** Zakotwiczenia są izolowane przez konto platformy Azure. Tylko aplikacje, dla których przyznano dostęp do Twojego konta, będą mogły uzyskać dostęp do kotwic w ramach tego konta.

**Pyt.: Jakie informacje o środowisku są przesyłane i przechowywane w usłudze podczas korzystania z zakotwiczenia przestrzennego platformy Azure? Czy zdjęcia środowiska są przesyłane i przechowywane?**

**A:** Podczas tworzenia lub lokalizowania kotwic obrazy środowiska są przetwarzane na urządzeniu w formacie pochodnym. Ten format pochodny jest przesyłany i przechowywany w ramach usługi.

Aby zapewnić przejrzystość, poniżej znajduje się obraz środowiska i pochodna chmura punktów rozrzedzonych. Chmura punktów przedstawia geometryczną reprezentację środowiska, która jest przesyłana i przechowywane w usłudze. Dla każdego punktu w chmurze punktów rozrzedzonych przesyłamy i przechowujemy jego skrót właściwości wizualnych. Skrót jest tworzony na podstawie danych pikseli, ale nie zawiera żadnych z nich.

Usługa Azure Spatial Anchors jest zgodna z [postanowieniami umowy serwisowej platformy Azure](https://go.microsoft.com/fwLink/?LinkID=522330&amp;amp;clcid=0x9) i [oświadczeniem firmy Microsoft o ochronie prywatności](https://go.microsoft.com/fwlink/?LinkId=521839&amp;clcid=0x409).

![Środowisko i jego pochodna chmura](./media/sparse-point-cloud.png)
punktów rozrzedzonych*Rysunek 1: Środowisko i jego pochodna chmura punktów rozrzedzonych*


**Pyt.: Czy istnieje sposób, w jaki można wysyłać informacje diagnostyczne do firmy Microsoft?**

**A:** Tak. W usłudze Azure Spatial Anchors istnieje tryb diagnostyczny, z którego mogą korzystać deweloperzy za pomocą interfejsu API usługi Azure Spatial Anchors. Jest to przydatne, gdy na przykład napotkasz środowisko, w którym nie ma możliwości tworzenia ani przewidywalnego znajdowania kotwic. Możemy Cię poprosić o przesłanie raportu diagnostyki zawierającego informacje pomocne w debugowaniu. Aby uzyskać więcej informacji, zobacz [Logging and diagnostics in Azure Spatial Anchors (Rejestrowanie i diagnostyka w usłudze Azure Spatial Anchors)](./concepts/logging-diagnostics.md).

## <a name="availability-and-pricing-faqs"></a>Często zadawane pytania dotyczące dostępności i cennika

**P: Czy udostępniasz umowę SLA?**

**Odp.:** Standardem w przypadku usług platformy Azure jest dostępność większa niż 99,9%. Należy zauważyć, że usługa Azure Spatial Anchors jest obecnie w wersji zapoznawczej i w związku z tym mają względem niej zastosowanie [Uzupełniające warunki korzystania z Wersji Zapoznawczych](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

**Pyt.: Czy mogę publikować aplikacje przy użyciu zakotwiczenia przestrzennego platformy Azure w sklepach z aplikacjami? Czy mogę używać zakotwiczenia przestrzennego platformy Azure do scenariuszy produkcji o znaczeniu krytycznym?**

**Odp.:** Zakotwiczenia przestrzenne platformy Azure są obecnie dostępne w wersji zapoznawczej i w tym czasie zapraszamy do tworzenia aplikacji, [przekazywania opinii](https://feedback.azure.com/forums/919252-azure-spatial-anchors) na temat produktu i planowania wdrożeń produkcyjnych.

Daty wprowadzania wersji ogólnie dostępnej zostaną ogłoszone wkrótce.

**Pyt.: Czy masz jakieś ograniczenia ograniczania przepustowości w miejscu?**

**Odp**.: Tak, mamy limity ograniczania przepustowości.  Nie oczekujemy, że zostaną osiągnięte podczas zwykłego tworzenia aplikacji i ich testowania. W przypadku wdrożeń produkcyjnych jesteśmy gotowi do obsługi wymagań naszych klientów związanych z wysoką skalowalnością. [Skontaktuj się z nami](mailto:azuremrs@microsoft.com) w celu omówienia tych zagadnień. W tej fazie wersji zapoznawczej nie została jeszcze opublikowana struktura warstw i cen, ale nastąpi to wkrótce.

**Pyt.: W jakich regionach są dostępne kotwice przestrzenne platformy Azure?**

**Odp.:** Konto zakotwiczenia przestrzenne platformy Azure można utworzyć już dziś w regionie Azure East US 2. Oznacza to, że w tym regionie dostępne są zarówno zasoby obliczeniowe, jak i zasoby magazynowe wymagane przez tę usługę. Nie ma jednak żadnych ograniczeń związanych z tym, gdzie znajdują się klienci. W przyszłości zamierzamy rozszerzyć dostępność regionalną usługi do wszystkich podstawowych regionów platformy Azure.

**Pyt.: Czy opłaty za kotwice przestrzenne platformy Azure? Czy kiedykolwiek naliczysz?**

**Odp.:** Szczegółowe informacje na temat cen można znaleźć podczas podglądu na naszej [stronie cenowej.](https://azure.microsoft.com/pricing/details/spatial-anchors/)

## <a name="technical-faqs"></a>Często zadawane pytania techniczne

**Pyt.: Jak działają kotwice przestrzenne platformy Azure?**

**Odp.:** Azure Spatial Anchors zależy od rzeczywistości mieszanej / rozszerzonej rzeczywistości trackerów. Te moduły śledzące służą do obserwacji środowiska za pomocą kamer i śledzenia urządzenia w sześciu stopniach swobody, gdy przemieszcza się ono w przestrzeni.

Posiadając moduł śledzący działający w sześciu stopniach swobody jako blok konstrukcyjny, usługa Azure Spatial Anchors umożliwia określenie pewnych punktów orientacyjnych w rzeczywistym środowisku jako punktów zakotwiczenia. Możesz na przykład użyć punktu zakotwiczenia do renderowania zawartości w określonym miejscu w rzeczywistych warunkach.

Podczas tworzenia takiego punktu zestaw SDK klienta przechwytuje informacje o środowisku wokół tego punktu i przekazuje je do usługi. Jeśli inne urządzenie szuka kotwicy w tym samym miejscu, do usługi przesyłane są podobne dane. Te dane są dopasowywane do wcześniej zapisanych danych dotyczących środowiska. Pozycja kotwicy względem urządzenia zostanie następnie przesłana z powrotem w celu użycia w aplikacji.

**Pyt.: Jak platformy Azure Spatial Anchors zintegrować z ARKit i ARCore na iOS i Android?**

**Odp.:** Zakotwiczenia przestrzenne platformy Azure wykorzystują natywne możliwości śledzenia ARKit i ARCore. Ponadto nasze zestawy SDK dla systemów iOS i Android oferują funkcje takie jak utrwalanie kotwic w ramach zarządzanej usługi w chmurze oraz umożliwianie aplikacjom ponownego znajdowania tych kotwic za pomocą standardowego połączenia z usługą.

**Pyt.: Jak platformy Azure Spatial Anchors integruje się z HoloLens?**

**Odp.:** Funkcja Azure Spatial Anchors wykorzystuje natywne funkcje śledzenia urządzenia HoloLens. Zapewniamy zestaw SDK usługi Azure Spatial Anchors do tworzenia aplikacji na urządzeniu HoloLens. Zestaw SDK integruje się z natywnymi funkcjami urządzenia HoloLens i udostępnia dodatkowe funkcje. Te funkcje umożliwiają deweloperom aplikacji utrwalanie kotwic w ramach zarządzanej usługi w chmurze oraz ponowne znajdowanie tych kotwic przez aplikacje dzięki nawiązaniu połączenia z usługą.

**Pyt.: Które platformy i języki obsługuje usługi Azure Spatial Anchors?**

**Odp.:** Deweloperzy mogą tworzyć aplikacje za pomocą zakotwiczenia przestrzennego platformy Azure przy użyciu znanych narzędzi i struktur dla swojego urządzenia:

- Platforma Unity na urządzeniu HoloLens oraz w systemie iOS i Android
- Xamarin na iOS i Android
- Platforma SWIFT i Objective-C w systemie iOS
- Platforma Java lub zestaw Android NDK w systemie Android
- Platforma C++/ WinRT na urządzeniu HoloLens

Rozpocznij programowanie od zapoznania się z [dokumentacją](index.yml).

**P: Czy działa z Unreal?**

**Odp.:** Wsparcie dla Unreal będzie rozważane w przyszłości.

**Pyt.: Jakich portów i protokołów używa zakotwiczenia przestrzenne platformy Azure?**

**Odp.:** Zakotwiczenia przestrzenne platformy Azure komunikują się za pośrednictwem portu TCP 443 przy użyciu zaszyfrowanego protokołu. Do uwierzytelniania używa [usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/), która komunikuje się przy użyciu protokołu HTTPS za pośrednictwem portu 443.
