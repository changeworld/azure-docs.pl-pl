---
title: Wymaganiom kontroli dostępu projektowania tożsamości hybrydowej usługi Azure | Dokumentacja firmy Microsoft
description: Obejmuje tożsamości i ustalenie wymagań dotyczących dostępu do zasobów dla użytkowników w środowisku hybrydowym.
documentationcenter: ''
services: active-directory
author: billmath
manager: mtillman
editor: ''
ms.assetid: e3b3b984-0d15-4654-93be-a396324b9f5e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.component: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 3a61e7ab4c738f6cba17bcc74c3bfd335378ab83
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34801222"
---
# <a name="determine-access-control-requirements-for-your-hybrid-identity-solution"></a>Określić wymagania dotyczące rozwiązania z tożsamością hybrydową kontroli dostępu
Gdy organizacji projektuje ich rozwiązania z tożsamością hybrydową, mogą również wykorzystać korzystając z okazji, aby przejrzeć wymagania dotyczące dostępu do zasobów, które są planowane był dostępny dla użytkowników. Dostęp do danych granic wszystkich czterech filarach tożsamości, które są:

* Administracja
* Authentication
* Autoryzacja
* Inspekcja

W kolejnych sekcjach opisano uwierzytelnianie i autoryzację w bardziej szczegółowe informacje, Administracja i inspekcja stanowią część cyklu życia tożsamości hybrydowej. Odczyt [określić zadań zarządzania tożsamości hybrydowej](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) Aby uzyskać więcej informacji o tych funkcjach.

> [!NOTE]
> Odczyt [czterech filarach tożsamości — zarządzania tożsamościami w wieku hybrydowego się](http://social.technet.microsoft.com/wiki/contents/articles/15530.the-four-pillars-of-identity-identity-management-in-the-age-of-hybrid-it.aspx) uzyskać więcej informacji o każdej z nich tych słupków.
> 
> 

## <a name="authentication-and-authorization"></a>Uwierzytelnianie i autoryzacja
Istnieją różne scenariusze uwierzytelniania i autoryzacji, te scenariusze będzie mieć określone wymagania, które muszą być spełnione przez rozwiązania z tożsamością hybrydową, które firma zamierza przyjąć. Scenariusze obejmujące Business-to-Business komunikacji (B2B) można dodać dodatkowe żądania dla administratorów IT, ponieważ będzie musiał upewnij się, że metoda uwierzytelniania i autoryzacji, używane przez organizację może komunikować się z ich partnerów biznesowych. Podczas procesu projektowania wymagania w zakresie uwierzytelniania i autoryzacji upewnij się, że należy odpowiedzieć na następujące pytania:

* Zostanie organizacji uwierzytelniania i autoryzacji tylko użytkownicy znajdujący się w ich tożsamości systemu zarządzania?
  * Czy jest planowane dla scenariusze B2B?
  * Jeśli tak, znasz już protokołów (SAML, OAuth, protokołu Kerberos lub certyfikatów) będzie służyć do nawiązania połączenia zarówno firmy?
* Czy rozwiązania z tożsamością hybrydową, który ma przyjąć obsługi tych protokołów?

Innym ważnym wziąć pod uwagę jest której zostaną umieszczone w repozytorium uwierzytelniania, który będzie używany przez użytkowników i partnerów i modelu administracyjnego do użycia. Należy wziąć pod uwagę następujące opcje dwa podstawowe:

* Scentralizowane: w tym modelu poświadczeń użytkownika, zasady i Administracja może być scentralizowanie lokalne lub w chmurze.
* Hybrydowe: w tym modelu poświadczeń użytkownika, zasady i administrowanie będzie scentralizowanie lokalne i replikowane w chmurze.

Model, który przyjmuje organizacji różnią się zgodnie z ich wymaganiami biznesowymi chcesz odpowiedzieć na następujące pytania, aby zidentyfikować której będą znajdować się system zarządzania tożsamościami i trybu administratora do użycia:

* Twoja organizacja ma obecnie Zarządzanie tożsamościami lokalnej?
  * Jeśli tak, czy planują schowaj?
  * Czy istnieją wszystkie rozporządzenia lub wymaganiami dotyczącymi zgodności czy organizacji wykonaj określają, że gdy system zarządzania tożsamościami powinien znajdować się?
* Twoja organizacja używa logowania jednokrotnego dla aplikacji znajdujących się lokalnie lub w chmurze?
  * Jeśli tak, przyjęcia modelu tożsamości hybrydowej wpływa na ten proces?

## <a name="access-control"></a>Kontrola dostępu
Uwierzytelnianie i autoryzacja są podstawowe elementy umożliwiające dostęp do firmowych danych za pośrednictwem weryfikacji użytkownika, należy również kontrolować poziom dostępu Ci użytkownicy będą mieć i ma poziom dostępu administratorów za pośrednictwem zasoby, które zarządzania. Rozwiązania z tożsamością hybrydową musi mieć możliwość zapewnienia szczegółowej dostęp do zasobów, delegowania i kontrolę dostępu na podstawie ról. Upewnij się, że poniższe pytanie jest udzielana jest odpowiedź dotyczące kontroli dostępu:

* Czy firma ma więcej niż jednego użytkownika z podwyższonym poziomem uprawnień, aby zarządzać systemem tożsamości?
  * Jeśli tak, czy każdy użytkownik potrzebuje poziomu dostępu?
* Czy firma musi delegować dostęp do użytkowników w celu zarządzania określone zasoby?
  * Jeśli tak, jak często dzieje się tak?
* Czy firma musi integracji możliwości kontroli dostępu między lokalnymi i w chmurze zasobów?
* Czy firma potrzebuje do ograniczania dostępu do zasobów zgodnie z niektóre warunki?
* Czy firma dysponuje dowolnej aplikacji, która wymaga niestandardowych kontroli dostępu do niektórych zasobów?
  * Jeśli tak, czy aplikacje lokalizację (lokalnie lub w chmurze)?
  * Jeśli tak, gdzie są te docelowych zasobów (lokalnie lub w chmurze)?

> [!NOTE]
> Upewnij się zanotować wszystkie odpowiedzi i zrozumieć ich uzasadnienie. [Definiowanie strategii ochrony danych](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) będą przekazywane dostępne opcje oraz zalety/wady każdej opcji.  Udzielenie odpowiedzi na te pytania wybierzesz opcji najlepiej odpowiadała potrzebom biznesowym użytkownika.
> 
> 

## <a name="next-steps"></a>Kolejne kroki
[Określenie wymagań dotyczących odpowiedzi na zdarzenia](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="see-also"></a>Zobacz też
[Omówienie zagadnień dotyczących projektowania](active-directory-hybrid-identity-design-considerations-overview.md)

