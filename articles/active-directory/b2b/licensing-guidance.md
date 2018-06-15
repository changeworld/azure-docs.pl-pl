---
title: Azure współpracy B2B usługi Active Directory licencjonowania wskazówki | Dokumentacja firmy Microsoft
description: Azure Active Directory B2B nie wymagają współpracy płatnej licencji usługi Azure AD, ale można również pobrać płatności funkcje dla gości B2B
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 08/09/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: beeb7801465d38743fdd654496c7b1b8e3ba1cc3
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
ms.locfileid: "34267440"
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Wskazówki dotyczące licencjonowania funkcji współpracy między firmami przy użyciu usługi Azure Active Directory

Możliwości współpracy B2B usługi Azure AD umożliwia zaprosić użytkowników gościa do dzierżawy usługi Azure AD, aby mogły uzyskiwać dostęp do usługi Azure AD i innych zasobów w organizacji. Jeśli chcesz zapewnić dostęp do płatnej funkcje usługi Azure AD, B2B współpracy gościa użytkownicy muszą mieć licencję z odpowiednie licencje usługi Azure AD. 

W szczególności:
* Azure AD możliwości wolnego są dostępne dla gości bez dodatkowych licencji.
* Jeśli chcesz zapewnić dostęp do płatnej funkcje usługi Azure AD dla użytkowników B2B, musi mieć wystarczającą liczbę licencji w celu obsługi tych gości B2B.
* Zapraszanie dzierżawy z usługi Azure AD z płatnej licencji ma współpracy B2B korzystanie z praw dodatkowe pięć B2B reklamy zaproszenie do dzierżawy.
* Klient, który jest właścicielem zaproszenia dzierżawy musi być jedna, aby ustalić, ile współpracy B2B, użytkownicy muszą płatnej możliwości usługi Azure AD. W zależności od płatnej usługi Azure AD funkcje dla użytkownika gościa musi mieć wystarczająco usługi Azure AD płatnej licencji, aby pokrywał się użytkowników współpracy B2B w tej samej proporcji 5:1.

Współpraca B2B gościa jest dodawana jako użytkownik z partnerskiej firmy, a nie pracownika z organizacji lub pracownik służbowych w Twojej konglomeratu. Użytkownika gościa B2B można zalogować się przy użyciu poświadczeń zewnętrznych poświadczeń należących do organizacji zgodnie z opisem w tym artykule. 

Innymi słowy B2B licencjonowania ustawiono nie sposób uwierzytelniania użytkownika, ale raczej relacji użytkownika dla danej organizacji. Jeśli Ci użytkownicy nie są partnerów, są one traktowane inaczej w umowy licencyjnej. Nie są uznawane za za użytkownika współpracy B2B do celów licencjonowania, nawet jeśli ich UserType jest oznaczona jako "Gość". Powinny być one licencjonowane zwykle na jedną licencję dla poszczególnych użytkowników. Tych użytkowników należą:
* Pracownikom
* Personel logowanie przy użyciu tożsamości zewnętrznych
* Pracownik służbowych w Twojej konglomeratu


## <a name="licensing-examples"></a>Przykłady licencjonowania
- Klient chce zaprosić 100 użytkowników współpracy B2B do jego dzierżawy usługi Azure AD. Klient przypisuje zarządzania dostępem i Inicjowanie obsługi administracyjnej dla wszystkich użytkowników, ale 50 użytkowników wymagają usługi MFA i dostępu warunkowego. Ten klient musi zakupić 10 licencji Azure AD podstawowa i 10 licencji Azure AD Premium P1 dotyczyć tych użytkowników B2B poprawnie. Jeśli klient planuje używać funkcji Identity Protection B2B użytkownikom, muszą one mieć licencji Azure AD Premium P2 tak, aby pokrywał zaproszonych użytkowników w tej samej proporcji 5:1.
- Klient ma 10 pracowników, którzy mają wszystkie aktualnie licencję z usługi Azure AD Premium P1. Teraz chce zaprosić 60 B2B użytkowników, którzy wymagają uwierzytelniania wieloskładnikowego (MFA). W obszarze reguły licencjonowania 5:1 klient musi mieć co najmniej 12 licencje usługi Azure AD Premium P1 dotyczyć wszystkich użytkowników 60 współpracy B2B. Ponieważ są one już 10 Premium P1 licencji dla swoich pracowników, 10, mają prawa do zapraszania 50 użytkowników B2B z funkcji Premium P1, takich jak uwierzytelnianie wieloskładnikowe. W tym przykładzie następnie, muszą one kupić 2 dodatkowe licencje Premium P1 tak, aby pokrywał pozostałe 10 użytkowników współpracy B2B.

> [!NOTE]
> Nie istnieje sposób jeszcze, aby przypisać licencje bezpośrednio do użytkowników B2B, aby włączyć te prawa użytkownika współpracy B2B.

Klient, który jest właścicielem zaproszenia dzierżawy musi być jedna, aby ustalić, ile współpracy B2B, użytkownicy muszą płatnej możliwości usługi Azure AD. W zależności od tego, który płatnej funkcje usługi Azure AD, które chcesz dla użytkownika gościa musi mieć wystarczająco usługi Azure AD płatnej licencji, aby pokrywał się użytkowników współpracy B2B w stosunku do 5:1. 

## <a name="additional-licensing-details"></a>Dodatkowe szczegóły licencjonowania
- Nie istnieje potrzeba do rzeczywistego przypisania licencji do kont użytkowników B2B. W oparciu o wskaźnik 5:1, licencjonowania jest obliczana i automatycznie zgłaszane.
- Jeśli nie płatnej licencji usługi Azure AD istnieje w dzierżawie, co zaproszonych użytkownik uzyskuje uprawnienia, które oferuje bezpłatna wersja usługi Azure AD.
- Jeśli licencji B2B współpracy użytkownik ma już płatne Azure AD w organizacji, nie zużywają jedną licencji współpracy B2B zaproszenia dzierżawcy.

## <a name="advanced-discussion-what-are-the-licensing-considerations-when-we-add-users-from-a-conglomerate-organization-as-members-using-your-apis"></a>Zaawansowane dyskusji: jakie są zagadnień związanych z licencjami, gdy możemy dodać użytkowników z konglomeratu organizacji jako "Członkowie" przy użyciu swoje interfejsy API?
Użytkownik-Gość B2B to taki, który jest zaproszenie od organizacji będącej partnerem do pracy z organizacji hosta. Zazwyczaj innym przypadku nie kwalifikuje się B2B nawet go używa funkcji B2B. Oto dwa przypadki w szczególności:

1. Jeśli host zaprasza pracownika przy użyciu adresu konsumenta
  * W tym scenariuszu nie jest zgodne z naszych zasadami licencjonowania i nie jest zalecane.

2. Jeśli organizacja hosta dodaje użytkownika z innej organizacji konglomeratu
  1. W takim przypadku użytkownik jest zaproszony przy użyciu interfejsów API B2B, ale ten przypadek nie jest tradycyjnie B2B. Najlepiej firma Microsoft ma organizacjach zapraszanie innych użytkowników organizacjami jako elementy członkowskie (interfejsach API umożliwia które). W takim przypadku licencje muszą być przypisane do tych elementów członkowskich dla nich uzyskać dostęp do zasobów w organizacji zaproszenia.

  2. Niektóre organizacje mogą zdecydować dodać użytkowników z innych organizacji ma zostać dodany jako "Gość" jako zasady. Istnieją dwa przypadki, w tym miejscu:
      * Konglomeratu organizacja już korzysta z usługi Azure AD i zaproszonych użytkowników są licencjonowane w innej organizacji: w tym przypadku nie powinien zaproszonych użytkowników, trzeba wykonać formuła 1:5, którego układ określa się wcześniej w tym dokumencie. 

      * Konglomeratu organizacja nie korzysta z usługi Azure AD lub nie ma odpowiedniej licencji: W tym przypadku należy wykonać formuła 1:5, którego układ określa się wcześniej w tym dokumencie.

## <a name="next-steps"></a>Kolejne kroki

Zobacz następujące artykuły artykułów na współpracy B2B usługi Azure AD:

* [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
* [Azure współpracy B2B usługi Active Directory — często zadawane pytania (FAQ)](faq.md)
