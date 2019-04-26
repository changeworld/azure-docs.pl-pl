---
title: Hybrydowe tożsamości projektowania wymagania dotyczące kontroli dostępu platformy Azure | Dokumentacja firmy Microsoft
description: Obejmuje tożsamości i identyfikowania wymagań dostępu do zasobów dla użytkowników w środowisku hybrydowym.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60295147"
---
# <a name="determine-access-control-requirements-for-your-hybrid-identity-solution"></a>Określić wymagania dotyczące kontroli dostępu dla danego rozwiązania tożsamości hybrydowej
Gdy organizacji projektuje swoje rozwiązania z tożsamością hybrydową, aby przejrzeć wymagania dotyczące dostępu do zasobów, które zamierza udostępnić użytkownikom mogą również wykorzystać tej możliwości. Dostęp do danych między wszystkie cztery filarów tożsamości, które są:

* Administracja
* Authentication
* Autoryzacja
* Inspekcja

W kolejnych sekcjach opisano uwierzytelnianie i autoryzacja w bardziej szczegółowe informacje, Administracja i inspekcji są dostępne w ramach cyklu życia tożsamości hybrydowej. Odczyt [określić zadania związane z zarządzaniem tożsamości hybrydowej](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) Aby uzyskać więcej informacji na temat tych możliwości.

> [!NOTE]
> Odczyt [czterech filarach z Identity — do zarządzania tożsamościami w wieku hybrydowego IT](https://social.technet.microsoft.com/wiki/contents/articles/15530.the-four-pillars-of-identity-identity-management-in-the-age-of-hybrid-it.aspx) Aby uzyskać więcej informacji na temat każdej z tych filarów.
> 
> 

## <a name="authentication-and-authorization"></a>Uwierzytelnianie i autoryzacja
Istnieją różne scenariusze uwierzytelniania i autoryzacji, te scenariusze będzie mieć specyficzne wymagania, które muszą zostać spełnione przez rozwiązania tożsamości hybrydowej, który firma zamierza przyjmuje. Scenariusze obejmujące firmami (B2B) komunikacji można dodać dodatkowe wyzwanie dla administratorów IT, ponieważ, należy upewnić się, że metoda uwierzytelniania i autoryzacji, używane przez organizację może komunikować się z ich partnerów biznesowych. Podczas procesu projektowania, wymagań uwierzytelniania i autoryzacji upewnij się, że należy odpowiedzieć na następujące pytania:

* Będzie organizacji uwierzytelnianie i autoryzacja tylko użytkownicy znajdujący się w ich systemu zarządzania tożsamościami?
  * Czy jest planowane dla scenariuszy B2B?
  * Jeśli tak, znasz już protokołów (SAML, OAuth, Kerberos lub certyfikatów), będzie używany do łączenia z obu firm?
* Czy rozwiązania tożsamości hybrydowej, który ma przyjąć obsługi tych protokołów?

Innym ważnym punktem należy wziąć pod uwagę jest, gdzie zostaną umieszczone w repozytorium uwierzytelniania, które będą używane przez użytkowników i partnerów i modelu administracyjnego, które ma być używany. Należy wziąć pod uwagę następujące dwa podstawowe opcje:

* Scentralizowane: w tym modelu poświadczeń użytkownika, zasady i Administracja może być scentralizowanie lokalne lub w chmurze.
* Hybrydowe: w tym modelu poświadczeń użytkownika, zasady i administrowanie będzie scentralizowanie lokalne i replikowane w chmurze.

Model, który przyjmie organizacji różny w zależności od swoich wymagań biznesowych, na który chcesz odpowiedzieć na następujące pytania, aby zidentyfikować lokalizację systemu zarządzania tożsamościami i trybu administracyjnego do użycia:

* Twoja organizacja ma obecnie platforma typu zarządzanie tożsamością w środowisku lokalnym?
  * Jeśli tak, czy planują zachować?
  * Czy istnieją jakiekolwiek wymagania rozporządzenia lub zgodność, Twoja organizacja musi wykonać tego określają lokalizację systemu zarządzania tożsamościami?
* Twoja organizacja używa logowania jednokrotnego dla aplikacji znajdujących się w środowisku lokalnym lub w chmurze?
  * Jeśli tak, wdrożenie modelu tożsamości hybrydowej wpływa na ten proces?

## <a name="access-control"></a>Kontrola dostępu
Uwierzytelnianie i autoryzacja są podstawowych elementów, aby umożliwić dostęp do danych firmowych za pośrednictwem weryfikacji użytkownika, ważne jest również kontrolować poziom dostępu tych użytkownicy będą mieć i stopień Administratorzy dostępu będzie miał nad zasobami czy zarządzają. Rozwiązania z tożsamością hybrydową musi mieć możliwość zapewnienia szczegółowych dostępu do zasobów, delegowanie i kontrolę dostępu na podstawie roli. Upewnij się, że następujących pojawi się odpowiedź dotyczące kontroli dostępu:

* Czy Twoja firma ma więcej niż jednego użytkownika z podwyższonym poziomem uprawnień, aby zarządzać systemem tożsamości?
  * Jeśli tak, czy każdy użytkownik potrzebuje ten sam poziom dostępu?
* Czy firma musi delegować dostęp do użytkowników w celu zarządzania zasobami w określonej?
  * Jeśli tak, jak często dzieje?
* Będzie potrzebny do integracji kontroli dostępu, których między lokalizacją lokalną i chmurą zasobów?
* Będzie potrzebny do ograniczania dostępu do zasobów zgodnie z warunkami niektórych?
* Czy firma dysponuje każdą aplikację, która wymaga formantu niestandardowego dostępu do niektórych zasobów?
  * Jeśli tak, gdzie aplikacji, które znajdują się (lokalnie lub w chmurze)?
  * Jeśli tak, gdzie są te zasoby docelowe znajduje się (lokalnie lub w chmurze)?

> [!NOTE]
> Pamiętaj zanotować wszystkie odpowiedzi i zrozumieć uzasadnienie. [Definiowanie strategii ochrony danych](plan-hybrid-identity-design-considerations-data-protection-strategy.md) zostanie umieszczona nad dostępne opcje oraz zalety/wady każdej opcji.  Za udzielenie odpowiedzi na te pytania będą wybrać, która opcja najlepiej pasuje do potrzeb biznesowych.
> 
> 

## <a name="next-steps"></a>Kolejne kroki
[Określanie wymagań dotyczących odpowiedzi na zdarzenia](plan-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="see-also"></a>Zobacz też
[Omówienie zagadnień dotyczących projektowania](plan-hybrid-identity-design-considerations-overview.md)

