---
title: Projektowania tożsamości hybrydowej — wymagania związane z zarządzaniem zawartością Azure | Dokumentacja firmy Microsoft
description: Zapewnia wgląd w sposób określić wymagania dotyczące zarządzania zawartością Twojej firmy. Zwykle, gdy użytkownik ma swoje własne urządzenia, może również mieć wiele poświadczenia, które będą alternatywnych zgodnie z aplikacji, które używają. Jest ważne, aby odróżnić, jakie zawartość została utworzona przy użyciu osobistych poświadczeń w porównaniu z utworzonymi przy użyciu poświadczeń firmowych. Rozwiązanie tożsamości powinien mieć możliwość interakcji z chmury usługi, aby zapewnić bezproblemowe środowisko użytkownika końcowego podczas zapewnienia zachowania ich poufności i zwiększyć ochronę przed wyciekami danych.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64918439"
---
# <a name="determine-content-management-requirements-for-your-hybrid-identity-solution"></a>Określanie wymagań dotyczących zarządzania zawartością, rozwiązania tożsamości hybrydowej
Opis wymagań dotyczących zarządzania zawartością w firmie bezpośrednie może wpływać na Twoją decyzję, na których rozwiązania tożsamości hybrydowej do użycia. Powszechne stosowanie wielu urządzeń i możliwość przynoszą własne urządzenia użytkowników ([BYOD](https://aka.ms/byodcg)), firmy muszą chronić swoje własne dane, ale jego również należy zachować prywatność użytkowników. Zwykle, gdy użytkownik ma swoje własne urządzenia, może również mieć wiele poświadczenia, które będą alternatywnych zgodnie z aplikacji, które używają. Jest ważne, aby odróżnić, jakie zawartość została utworzona przy użyciu osobistych poświadczeń w porównaniu z utworzonymi przy użyciu poświadczeń firmowych. Rozwiązanie tożsamości powinien mieć możliwość interakcji z chmury usługi, aby zapewnić bezproblemowe środowisko użytkownika końcowego podczas zapewnienia zachowania ich poufności i zwiększyć ochronę przed wyciekami danych. 

Rozwiązanie tożsamości będzie można wykorzystać przez inną kontrolę techniczną w celu zapewnienia zarządzania zawartością, jak pokazano na poniższej ilustracji:

![Opcje zabezpieczeń](./media/plan-hybrid-identity-design-considerations/securitycontrols.png)

**Opcji zabezpieczeń, które będzie korzystanie z systemu zarządzania tożsamościami**

Ogólnie rzecz biorąc wymagania dotyczące zarządzania zawartością będzie korzystać z systemu zarządzania tożsamościami w następujących obszarach:

* Prywatność: identyfikacji użytkownika, który jest właścicielem zasobu i stosowania odpowiednich kontroli w celu zachowania integralności.
* Klasyfikacja danych: Określ użytkownika lub grupy i poziom dostępu do obiektu, zgodnie z ich klasyfikację. 
* Ochrona wycieku danych: odpowiedzialność za ochronę danych w celu uniknięcia wycieku środki kontroli bezpieczeństwa należy korzystać z systemu tożsamości w celu weryfikowania tożsamości użytkownika. Jest to również ważne w przypadku zastosowania dziennik inspekcji.

> [!NOTE]
> Odczyt [klasyfikacja danych pod kątem przygotowania do chmury](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) Aby uzyskać więcej informacji na temat najlepszych rozwiązań i wytycznych dotyczących klasyfikacji danych.
> 
> 

Podczas planowania rozwiązania z tożsamością hybrydową upewnij się, że zgodnie z wymaganiami organizacji są odpowiedzieć na następujące pytania:

* Czy firma dysponuje środki kontroli bezpieczeństwa w celu wymuszenia prywatność danych
  * Jeśli tak, będzie kontroli zabezpieczeń można zintegrować z rozwiązania tożsamości hybrydowej, który ma przyjąć?
* Firma używa klasyfikacji danych?
  * Jeśli tak, jest możliwość integracji z rozwiązania tożsamości hybrydowej, który ma przyjąć bieżące rozwiązanie?
* Czy firma dysponuje obecnie żadne rozwiązanie do wycieku danych? 
  * Jeśli tak, jest możliwość integracji z rozwiązania tożsamości hybrydowej, który ma przyjąć bieżące rozwiązanie?
* Czy firma potrzebuje inspekcji dostępu do zasobów?
  * Jeśli tak, jakiego typu zasobów?
  * Jeśli tak, jakie informacje są niezbędne?
  * Jeśli tak, gdy dziennik inspekcji musi znajdować się? W środowisku lokalnym lub w chmurze?
* Czy firma potrzebuje do zaszyfrowania żadnych wiadomości e-mail zawierających poufne dane (PESEL, numerów kart kredytowych, itp.)?
* Czy firma potrzebuje do szyfrowania wszystkich dokumentów/contents udostępnione zewnętrznymi partnerami biznesowymi?
* Czy firma potrzebuje wymuszanie zasad firmowych na niektórych rodzajów wiadomości e-mail (nie ma wszystkie odpowiedzi, nie przekazuj)?

> [!NOTE]
> Pamiętaj zanotować wszystkie odpowiedzi i zrozumieć uzasadnienie. [Definiowanie strategii ochrony danych](plan-hybrid-identity-design-considerations-data-protection-strategy.md) zostanie umieszczona nad dostępne opcje oraz zalety/wady każdej opcji.  Po udzieleniu odpowiedzi na te pytania można wybrać opcję, która najlepiej pasujące do działalności potrzebuje.
> 
> 

## <a name="next-steps"></a>Kolejne kroki
[Określić wymagania dotyczące kontroli dostępu](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)

## <a name="see-also"></a>Zobacz też
[Omówienie zagadnień dotyczących projektowania](plan-hybrid-identity-design-considerations-overview.md)

