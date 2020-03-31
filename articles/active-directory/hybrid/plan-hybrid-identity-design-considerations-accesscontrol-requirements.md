---
title: Wymagania dotyczące kontroli dostępu do projektu tożsamości hybrydowej Platformy Azure | Dokumenty firmy Microsoft
description: Obejmuje filary tożsamości i identyfikowanie wymagań dotyczących dostępu dla zasobów dla użytkowników w środowisku hybrydowym.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: e3b3b984-0d15-4654-93be-a396324b9f5e
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84b786a1701892823554a83fa2015ac88d6eff4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60295147"
---
# <a name="determine-access-control-requirements-for-your-hybrid-identity-solution"></a>Określanie wymagań dotyczących kontroli dostępu dla rozwiązania tożsamości hybrydowej
Gdy organizacja projektuje swoje rozwiązanie tożsamości hybrydowej, mogą również użyć tej okazji do przeglądania wymagań dotyczących dostępu dla zasobów, które planują udostępnić je użytkownikom. Dostęp do danych przekracza wszystkie cztery filary tożsamości, które są:

* Administracja
* Uwierzytelnianie
* Autoryzacja
* Inspekcja

Sekcje, które następują, obejmują uwierzytelnianie i autoryzację bardziej szczegółowo, administracja i inspekcja są częścią cyklu życia tożsamości hybrydowej. Przeczytaj [określ zadania zarządzania tożsamościami hybrydowymi,](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) aby uzyskać więcej informacji na temat tych możliwości.

> [!NOTE]
> Przeczytaj [cztery filary tożsamości - zarządzanie tożsamością w wieku hybrydowego IT, aby](https://social.technet.microsoft.com/wiki/contents/articles/15530.the-four-pillars-of-identity-identity-management-in-the-age-of-hybrid-it.aspx) uzyskać więcej informacji na temat każdego z tych filarów.
> 
> 

## <a name="authentication-and-authorization"></a>Uwierzytelnianie i autoryzacja
Istnieją różne scenariusze uwierzytelniania i autoryzacji, te scenariusze będą miały określone wymagania, które muszą być spełnione przez rozwiązanie tożsamości hybrydowej, które firma zamierza przyjąć. Scenariusze obejmujące komunikację między firmami (B2B) mogą być dodatkowym wyzwaniem dla administratorów IT, ponieważ będą musieli zapewnić, że metoda uwierzytelniania i autoryzacji używana przez organizację może komunikować się ze swoimi partnerami biznesowymi. Podczas procesu projektowania wymagań dotyczących uwierzytelniania i autoryzacji należy upewnić się, że odpowiedzi na następujące pytania:

* Czy twoja organizacja uwierzytelni i autoryzuje tylko użytkowników znajdujących się w ich systemie zarządzania tożsamościami?
  * Czy są jakieś plany dla scenariuszy B2B?
  * Jeśli tak, czy wiesz już, które protokoły (SAML, OAuth, Kerberos lub Certyfikaty) będą używane do łączenia obu firm?
* Czy rozwiązanie tożsamości hybrydowej, które zamierzasz przyjąć, obsługuje te protokoły?

Innym ważnym punktem do rozważenia jest to, gdzie repozytorium uwierzytelniania, które będą używane przez użytkowników i partnerów będzie znajdować się i model administracyjny, który ma być używany. Rozważmy następujące dwie podstawowe opcje:

* Scentralizowane: w tym modelu poświadczenia użytkownika, zasady i administracja mogą być scentralizowane lokalnie lub w chmurze.
* Hybrydowy: w tym modelu poświadczenia użytkownika, zasady i administracja będą scentralizowane lokalnie i replikowane w chmurze.

Który model przyjmie organizacja będzie się różnić w zależności od wymagań biznesowych, chcesz odpowiedzieć na następujące pytania, aby określić, gdzie będzie mieszkał system zarządzania tożsamościami i tryb administracyjny do użycia:

* Czy twoja organizacja ma obecnie lokalne zarządzanie tożsamościami?
  * Jeśli tak, czy planują go zatrzymać?
  * Czy istnieją jakieś wymagania dotyczące regulacji lub zgodności, które muszą być przestrzegane przez organizację, które określają, gdzie powinien znajdować się system zarządzania tożsamościami?
* Czy organizacja używa logowania jednokrotnego dla aplikacji znajdujących się lokalnie lub w chmurze?
  * Jeśli tak, czy przyjęcie modelu tożsamości hybrydowej wpływa na ten proces?

## <a name="access-control"></a>Kontrola dostępu
Podczas gdy uwierzytelnianie i autoryzacja są podstawowymi elementami umożliwiającymi dostęp do danych firmowych poprzez sprawdzanie poprawności przez użytkownika, ważne jest również kontrolowanie poziomu dostępu, jaki będą mieli ci użytkownicy, oraz poziomu dostępu, jaki administratorzy będą mieli nad zasobami. które zarządzają. Rozwiązanie tożsamości hybrydowej musi być w stanie zapewnić szczegółowy dostęp do zasobów, delegowania i kontroli dostępu podstawowego roli. Upewnij się, że udzielono odpowiedzi na następujące pytanie dotyczące kontroli dostępu:

* Czy Twoja firma ma więcej niż jednego użytkownika z podwyższonym uprawnieniami do zarządzania systemem tożsamości?
  * Jeśli tak, czy każdy użytkownik potrzebuje tego samego poziomu dostępu?
* Czy twoja firma musi delegować dostęp do użytkowników w celu zarządzania określonymi zasobami?
  * Jeśli tak, jak często tak się dzieje?
* Czy Twoja firma musi zintegrować możliwości kontroli dostępu między zasobami lokalnymi i chmurowymi?
* Czy Twoja firma będzie musiała ograniczyć dostęp do zasobów zgodnie z pewnymi warunkami?
* Czy Twoja firma ma dowolną aplikację, która wymaga niestandardowego dostępu do niektórych zasobów?
  * Jeśli tak, gdzie znajdują się te aplikacje (lokalnie lub w chmurze)?
  * Jeśli tak, gdzie znajdują się te zasoby docelowe (lokalnie lub w chmurze)?

> [!NOTE]
> Pamiętaj, aby zanotować wszystkie odpowiedzi i zrozumieć ich uzasadnienie. [Zdefiniuj strategię ochrony danych](plan-hybrid-identity-design-considerations-data-protection-strategy.md) będzie wykraczać poza dostępne opcje i zalety / wady każdej opcji.  Odpowiadając na te pytania, wybierzesz opcję, która najlepiej odpowiada Twoim potrzebom biznesowym.
> 
> 

## <a name="next-steps"></a>Następne kroki
[Określanie wymagań dotyczących odpowiadania na zdarzenia](plan-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="see-also"></a>Zobacz też
[Omówienie zagadnień projektowych](plan-hybrid-identity-design-considerations-overview.md)

