---
title: Projekt tożsamości hybrydowej — wymagania dotyczące zarządzania zawartością Azure | Dokumenty firmy Microsoft
description: Zapewnia wgląd w sposób określania wymagań dotyczących zarządzania zawartością firmy. Zazwyczaj, gdy użytkownik ma własne urządzenie, mogą mieć również wiele poświadczeń, które będą naprzemiennie w zależności od używanej aplikacji. Ważne jest, aby odróżnić zawartość utworzoną przy użyciu poświadczeń osobistych w porównaniu z tymi utworzonymi przy użyciu poświadczeń firmowych. Rozwiązanie tożsamości powinno być w stanie wchodzić w interakcje z usługami w chmurze, aby zapewnić bezproblemowe środowisko dla użytkownika końcowego, zapewniając jednocześnie ich prywatność i zwiększyć ochronę przed wyciekiem danych.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: dd1ef776-db4d-4ab8-9761-2adaa5a4f004
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d970fd133f8c43319e7f1fdb6b3a50c3c05f687
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "64918439"
---
# <a name="determine-content-management-requirements-for-your-hybrid-identity-solution"></a>Określanie wymagań dotyczących zarządzania zawartością dla rozwiązania tożsamości hybrydowej
Zrozumienie wymagań dotyczących zarządzania zawartością dla Twojej firmy może mieć bezpośredni wpływ na decyzję o tym, którego rozwiązania tożsamości hybrydowej użyć. Wraz z rozprzestrzenianiem się wielu urządzeń i możliwością użytkowników do przywiezienia własnych urządzeń[(BYOD),](https://aka.ms/byodcg)firma musi chronić własne dane, ale musi również zachować prywatność użytkownika w stanie nienaruszonym. Zazwyczaj, gdy użytkownik ma własne urządzenie, mogą mieć również wiele poświadczeń, które będą naprzemiennie w zależności od używanej aplikacji. Ważne jest, aby odróżnić zawartość utworzoną przy użyciu poświadczeń osobistych w porównaniu z tymi utworzonymi przy użyciu poświadczeń firmowych. Rozwiązanie tożsamości powinno być w stanie wchodzić w interakcje z usługami w chmurze, aby zapewnić bezproblemowe środowisko dla użytkownika końcowego, zapewniając jednocześnie ich prywatność i zwiększyć ochronę przed wyciekiem danych. 

Twoje rozwiązanie tożsamościowe zostanie wykorzystane przez różne mechanizmy kontroli technicznej w celu zapewnienia zarządzania treścią, jak pokazano na poniższym rysunku:

![kontroli bezpieczeństwa](./media/plan-hybrid-identity-design-considerations/securitycontrols.png)

**Mechanizmy kontroli bezpieczeństwa, które będą wykorzystywać system zarządzania tożsamościami**

Ogólnie rzecz biorąc, wymagania dotyczące zarządzania treścią będą wykorzystywać system zarządzania tożsamościami w następujących obszarach:

* Prywatność: identyfikowanie użytkownika, który jest właścicielem zasobu i stosowanie odpowiednich formantów w celu zachowania integralności.
* Klasyfikacja danych: identyfikowanie użytkownika lub grupy i poziomu dostępu do obiektu zgodnie z jego klasyfikacją. 
* Ochrona przed wyciekami danych: środki kontroli bezpieczeństwa odpowiedzialne za ochronę danych w celu uniknięcia wycieków będą musiały wchodzić w interakcje z systemem tożsamości, aby zweryfikować tożsamość użytkownika. Jest to również ważne dla celów inspekcji szlaku.

> [!NOTE]
> Przeczytaj [klasyfikację danych, aby uzyskać gotowość do pracy w chmurze,](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) aby uzyskać więcej informacji na temat najlepszych rozwiązań i wskazówek dotyczących klasyfikacji danych.
> 
> 

Podczas planowania rozwiązania tożsamości hybrydowej upewnij się, że na następujące pytania odpowiada się zgodnie z wymaganiami organizacji:

* Czy Twoja firma posiada środki kontroli bezpieczeństwa w celu egzekwowania prywatności danych?
  * Jeśli tak, czy mechanizmy kontroli bezpieczeństwa będą w stanie zintegrować się z rozwiązaniem tożsamości hybrydowej, które zamierzasz przyjąć?
* Czy Twoja firma korzysta z klasyfikacji danych?
  * Jeśli tak, to czy obecne rozwiązanie jest w stanie zintegrować się z rozwiązaniem tożsamości hybrydowej, które zamierzasz przyjąć?
* Czy Twoja firma ma obecnie jakieś rozwiązanie dotyczące wycieku danych? 
  * Jeśli tak, to czy obecne rozwiązanie jest w stanie zintegrować się z rozwiązaniem tożsamości hybrydowej, które zamierzasz przyjąć?
* Czy Twoja firma musi przeprowadzić inspekcję dostępu do zasobów?
  * Jeśli tak, jaki rodzaj zasobów?
  * Jeśli tak, jaki poziom informacji jest konieczny?
  * Jeśli tak, gdzie musi znajdować się dziennik inspekcji? Lokalnie czy w chmurze?
* Czy Twoja firma musi szyfrować wiadomości e-mail zawierające poufne dane (SSN, numery kart kredytowych itp.)?
* Czy Twoja firma musi szyfrować wszystkie dokumenty/treści udostępnione zewnętrznym partnerom biznesowym?
* Czy Twoja firma musi egzekwować zasady firmowe dotyczące niektórych rodzajów wiadomości e-mail (nie odpowiadaj wszystkim, nie przesyłaj dalej)?

> [!NOTE]
> Pamiętaj, aby zanotować wszystkie odpowiedzi i zrozumieć ich uzasadnienie. [Zdefiniuj strategię ochrony danych](plan-hybrid-identity-design-considerations-data-protection-strategy.md) będzie wykraczać poza dostępne opcje i zalety / wady każdej opcji.  Odpowiadając na te pytania, wybierzesz opcję, która najlepiej odpowiada Twoim potrzebom biznesowym.
> 
> 

## <a name="next-steps"></a>Następne kroki
[Określanie wymagań dotyczących kontroli dostępu](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)

## <a name="see-also"></a>Zobacz też
[Omówienie zagadnień projektowych](plan-hybrid-identity-design-considerations-overview.md)

