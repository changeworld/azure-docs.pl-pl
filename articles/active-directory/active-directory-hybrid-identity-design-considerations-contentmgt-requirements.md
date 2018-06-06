---
title: Tożsamość hybrydowym — wymagania dotyczące zarządzania zawartością Azure | Dokumentacja firmy Microsoft
description: Zapewnia wgląd w sposób określenie wymagań w zakresie zarządzania zawartością firmy. Zwykle, gdy użytkownik ma swoje własne urządzenia, może również mieć wiele poświadczeń, które będą naprzemiennych zgodnie z aplikacji, które korzystają z. Należy do odróżnienia zawartość została utworzona przy użyciu poświadczeń osobistego od komputerów utworzone przy użyciu poświadczeń firmowych. Rozwiązania tożsamości powinny mieć możliwość interakcji z chmurą services, aby umożliwić nie zakłóca pracy użytkownika końcowego podczas zapewnienia ich zachowania poufności i zwiększenia ochrony przed wyciekiem danych.
documentationcenter: ''
services: active-directory
author: billmath
manager: mtillman
editor: ''
ms.assetid: dd1ef776-db4d-4ab8-9761-2adaa5a4f004
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: b414a1e93d5a0f4be5be82011482b492fd9ce378
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34800797"
---
# <a name="determine-content-management-requirements-for-your-hybrid-identity-solution"></a>Określenie wymagań dotyczących zarządzania zawartością dla rozwiązania z tożsamością hybrydową
Zapoznanie się z wymaganiami zarządzania zawartością w firmie może bezpośrednio wpływa na na decyzję dotyczącą rozwiązania hybrydowe tożsamości do użycia. Rosnąca liczba wiele urządzeń i możliwość przynoszą własne urządzenia użytkowników ([BYOD](https://aka.ms/byodcg)), firmy muszą chronić własnych danych, ale jego również należy zachowane prywatność użytkowników. Zwykle, gdy użytkownik ma swoje własne urządzenia, może również mieć wiele poświadczeń, które będą naprzemiennych zgodnie z aplikacji, które korzystają z. Należy do odróżnienia zawartość została utworzona przy użyciu poświadczeń osobistego od komputerów utworzone przy użyciu poświadczeń firmowych. Rozwiązania tożsamości powinny mieć możliwość interakcji z chmurą services, aby umożliwić nie zakłóca pracy użytkownika końcowego podczas zapewnienia ich zachowania poufności i zwiększenia ochrony przed wyciekiem danych. 

Rozwiązania tożsamości będą wykorzystywane przez inną kontrolę techniczną w celu zapewnienia zarządzania zawartością, jak pokazano na poniższej ilustracji:

![](./media/hybrid-id-design-considerations/securitycontrols.png)

**Opcji zabezpieczeń, które będą wykorzystaniu systemu zarządzania tożsamościami**

Ogólnie rzecz biorąc wymagania dotyczące zarządzania zawartością będzie korzystać z systemu zarządzania tożsamościami w następujących obszarach:

* Ochrona prywatności: identyfikacji użytkownika, który jest właścicielem zasobu i stosowania kontrole w celu zachowania integralności.
* Klasyfikacja danych: identyfikacji użytkownika lub grupy i poziom dostępu do obiektu zgodnie z ich klasyfikację. 
* Ochrona wycieku danych: wyłączną odpowiedzialność za ochronę danych, aby uniknąć wycieków kontroli bezpieczeństwa należy wchodzić w interakcje z systemu tożsamości w celu weryfikowania tożsamości użytkownika. To ważne jest również cel dziennik inspekcji.

> [!NOTE]
> Odczyt [klasyfikacji danych pod kątem gotowości do chmury](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) Aby uzyskać więcej informacji na temat najlepszych rozwiązań i wytyczne dotyczące klasyfikacji danych.
> 
> 

Podczas planowania rozwiązania z tożsamością hybrydową upewnij się, że zgodnie z wymaganiami organizacji są odpowiedzieć na następujące pytania:

* Czy firma dysponuje kontroli zabezpieczeń w celu wymuszenia prywatność danych
  * Jeśli tak, będzie kontrolami zabezpieczeń można zintegrować z rozwiązania z tożsamością hybrydową, które zamierzasz wdrożyć usługę?
* Czy firma korzysta klasyfikacji danych?
  * Jeśli tak, jest możliwość integracji z rozwiązania z tożsamością hybrydową, które zamierzasz wdrożyć usługę bieżące rozwiązanie?
* Czy firma ma obecnie wszystkie rozwiązania do wycieku danych? 
  * Jeśli tak, jest możliwość integracji z rozwiązania z tożsamością hybrydową, które zamierzasz wdrożyć usługę bieżące rozwiązanie?
* Czy firma musi inspekcji dostępu do zasobów?
  * Jeśli tak, jakiego typu zasobów?
  * Jeśli tak, jakie informacje są niezbędne?
  * Jeśli tak, gdy dziennik inspekcji muszą znajdować się? Lokalnie lub w chmurze?
* Czy firma musi szyfrowania żadnych wiadomości e-mail zawierających poufne dane (numerów PESEL, numerów kart kredytowych, itp.)?
* Czy firma musi szyfrować wszystkie dokumenty/zawartości udostępnione zewnętrznymi partnerami biznesowymi?
* Czy firma potrzebuje wymuszanie zasad firmowych na określonych rodzajów wiadomości e-mail (nie ma wszystkich odpowiedzi, nie przekazuj)?

> [!NOTE]
> Upewnij się zanotować wszystkie odpowiedzi i zrozumieć ich uzasadnienie. [Definiowanie strategii ochrony danych](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) będą przekazywane dostępne opcje oraz zalety/wady każdej opcji.  Po udzieleniu odpowiedzi na te pytania można wybrać opcji najlepiej pasujące do firmy wymaga.
> 
> 

## <a name="next-steps"></a>Kolejne kroki
[Określenie wymagań dotyczących kontroli dostępu](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)

## <a name="see-also"></a>Zobacz też
[Omówienie zagadnień dotyczących projektowania](active-directory-hybrid-identity-design-considerations-overview.md)

