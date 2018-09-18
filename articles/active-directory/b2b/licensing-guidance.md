---
title: Wskazówki dotyczące licencjonowania współpracy w usłudze Azure Active Directory B2B | Dokumentacja firmy Microsoft
description: Usługa Azure Active Directory B2B, które nie wymagają współpracy płatne licencje usługi Azure AD, ale użytkownik może również uzyskać płatnych funkcji dla użytkowników-gości B2B
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: conceptual
ms.date: 08/09/2017
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 1d92f68bbb5e8c001594e4f78f90cb10496aaf29
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2018
ms.locfileid: "45984496"
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Wskazówki dotyczące licencjonowania funkcji współpracy między firmami przy użyciu usługi Azure Active Directory

Funkcje współpracy B2B usługi Azure AD umożliwia zaprosić użytkowników-gości do dzierżawy usługi Azure AD, aby umożliwić im dostęp do usługi Azure AD i innych zasobów w Twojej organizacji. Jeśli chcesz zapewnić dostęp do płatnych funkcji usługi Azure AD, musi mieć licencję użytkowników-gości współpracy B2B odpowiednie licencje usługi Azure AD. 

W szczególności:
* Bezpłatne możliwości usługi Azure AD są dostępne dla użytkowników-gości bez dodatkowych licencji.
* Jeśli chcesz zapewnić dostęp do płatnych funkcji usługi Azure AD dla użytkowników B2B, musi mieć wystarczającą liczbę licencji, do obsługi tych użytkowników-gości B2B.
* Zapraszający dzierżawca z płatnej licencji usługi Azure AD jest współpraca B2B prawa dodatkowe pięciu użytkowników-gości B2B zaproszenie do dzierżawy do używania.
* Klient, który jest właścicielem zapraszający dzierżawca musi być jedna, aby ustalić, ile współpracy B2B, użytkownicy muszą mieć płatnych funkcji usługi Azure AD. W zależności od usługi płatne Azure AD funkcje dla użytkowników gości, musisz mieć dostatecznie usłudze Azure AD płatnych licencji użytkowników we współpracy B2B w tym samym współczynnik 5:1.

Użytkownik-Gość współpracy B2B zostanie dodany jako użytkownik z firmy partnerskiej, a nie pracownika, organizacji lub pracownik różnych firm w swojej konglomeracie. Użytkownik-Gość B2B można zalogować się przy użyciu zewnętrznego poświadczenia lub własnością Twojej organizacji, zgodnie z opisem w tym artykule. 

Innymi słowy Licencjonowanie B2B ustawiono nie, przez jaki użytkownik jest uwierzytelniany, ale raczej przez relację użytkownika dla danej organizacji. Jeśli Ci użytkownicy nie są partnerami, są traktowane inaczej w postanowienia licencyjne. Nie są uwzględniane jako użytkownika współpracy B2B do celów licencjonowania, nawet wtedy, gdy ich UserType jest oznaczony jako "Gość". Powinny być one licencjonowane zwykle w jedną licencję dla poszczególnych użytkowników. Tych użytkowników należą:
* Pracowników
* Personel logowanie się przy użyciu tożsamości zewnętrznych
* Pracownik różnych firm w swojej konglomeracie


## <a name="licensing-examples"></a>Przykłady licencjonowania
- Klient chce, aby zaprosić 100 użytkowników we współpracy B2B do swojej dzierżawy usługi Azure AD. Klient przypisuje zarządzania dostępem i Inicjowanie obsługi administracyjnej dla wszystkich użytkowników, ale 50 użytkowników wymagają uwierzytelniania Wieloskładnikowego i dostępu warunkowego. Ten klient musi zakupić 10 licencji usługi Azure AD podstawowa i 10 Azure AD Premium P1 licencji użytkowników B2B poprawnie. Jeśli klient planuje używać funkcji Identity Protection użytkownikom B2B, muszą mieć licencji usługi Azure AD Premium P2 zaproszonych użytkowników w tej samej współczynnik 5:1.
- Klient ma 10 pracowników, którzy są aktualnie wszystkie licencjonowane przy użyciu usługi Azure AD Premium P1. Firma chce teraz zaprosić 60 B2B użytkowników, którzy wymagają uwierzytelniania wieloskładnikowego (MFA). W obszarze reguła licencjonowania 5:1 klient musi mieć co najmniej 12 licencje usługi Azure AD Premium P1, aby objęły one wszystkich użytkowników współpracy B2B 60. Ponieważ mają one już 10 licencji Premium P1 dla swoich pracowników, 10, mają oni uprawnienia, aby zaprosić 50 użytkowników B2B z funkcji Premium P1, takich jak uwierzytelnianie wieloskładnikowe. W tym przykładzie następnie należy dokupić 2 dodatkowe licencje Premium P1 dla pokrycia pozostałych 10 użytkowników we współpracy B2B.

> [!NOTE]
> Nie ma możliwości jeszcze, aby przypisać licencje bezpośrednio do użytkowników B2B, aby włączyć te prawa użytkownika współpracy B2B.

Klient, który jest właścicielem zapraszający dzierżawca musi być jedna, aby ustalić, ile współpracy B2B, użytkownicy muszą mieć płatnych funkcji usługi Azure AD. W zależności od tego, które płatne funkcje usługi Azure AD, które chcesz dla użytkowników z gościa musi mieć wystarczająco usługi Azure AD płatne licencje użytkowników współpracy B2B w stosunku do 5:1. 

## <a name="additional-licensing-details"></a>Dodatkowe szczegóły licencji
- Nie ma potrzeby faktycznie przypisać licencje do kont użytkowników B2B. Oparty na stosunku 5:1, licencjonowania jest automatycznie obliczana i raportowany.
- Jeśli nie płatnych licencja usługi Azure AD istnieje w dzierżawie, co zaproszony użytkownik otrzyma prawa, które oferuje bezpłatna wersja usługi Azure AD.
- Jeśli licencji B2B, współpracy użytkownik już ma płatnej usługi Azure AD w swojej organizacji, nie zajmują jednej licencji współpracy B2B zapraszający dzierżawca.

## <a name="advanced-discussion-what-are-the-licensing-considerations-when-we-add-users-from-a-conglomerate-organization-as-members-using-your-apis"></a>Zaawansowane dyskusji: co to są licencjonowania zagadnienia, po dodaniu użytkowników z konglomeratu organizacji jako "Członkowie", przy użyciu interfejsów API?
Użytkownik-Gość B2B to taki, który otrzymał zaproszenie z organizacji partnerskiej do pracy z organizacji hosta. Zazwyczaj każdym innym przypadku nie kwalifikuje się jako B2B nawet jej używa funkcji B2B. Spójrzmy na dwa przypadki w szczególności:

1. Jeśli host zaprasza pracownika przy użyciu adresu odbiorcy
  * W tym scenariuszu nie jest zgodne z naszymi zasadami licencjonowania i nie jest zalecane.

2. Jeśli organizacja hosta dodaje użytkownika z innej organizacji konglomeratu
  1. W takim przypadku użytkownik otrzymał zaproszenie za pomocą interfejsów API B2B, ale ten przypadek nie jest tradycyjnie B2B. W idealnym przypadku powinien mamy tych organizacji zapraszać innych użytkowników organizacje jako elementy członkowskie (nasze interfejsy API umożliwia, które). W tym przypadku licencje muszą być przypisane do tych członków, ich dostęp do zasobów w organizacji zapraszającej.

  2. Niektóre organizacje mogą zdecydować dodać użytkowników z innych organizacji do dodania jako "Gość" jako zasady. Istnieją dwa przypadki, w tym miejscu:
      * Konglomeratu organizacja używa już usługi Azure AD i zaproszeni użytkownicy są licencjonowane w innej organizacji: w tym przypadku nie oczekujemy, że zaproszeni użytkownicy muszą wykonać formuły 1:5 rozmieszczony we wcześniejszej części tego dokumentu. 

      * Konglomeratu organizacja nie korzysta z usługi Azure AD lub nie ma odpowiedniej licencji: W tym przypadku postępuj zgodnie z formuły 1:5 rozmieszczony we wcześniejszej części tego dokumentu.

## <a name="next-steps"></a>Kolejne kroki

Na współpracy B2B usługi Azure AD, zobacz następujące artykuły:

* [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
* [Często zadawane pytania (FAQ) współpracy w usłudze Azure Active Directory B2B](faq.md)
