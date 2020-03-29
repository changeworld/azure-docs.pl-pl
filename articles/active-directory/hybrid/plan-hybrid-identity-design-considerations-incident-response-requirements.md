---
title: Projekt tożsamości hybrydowej — wymagania dotyczące reagowania na incydenty Platformy Azure | Dokumenty firmy Microsoft
description: Określanie możliwości monitorowania i raportowania rozwiązania do tożsamości hybrydowej, które może być wykorzystane przez it do podejmowania działań w celu identyfikacji i łagodzenia potencjalnych zagrożeń
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: a3d2a459-599b-4b67-8e51-7369ee25082d
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 52b5e37c29e4b3df3f171f683266b5d0a3e0c95d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109274"
---
# <a name="determine-incident-response-requirements-for-your-hybrid-identity-solution"></a>Określanie wymagań dotyczących reagowania na zdarzenia dla rozwiązania tożsamości hybrydowej
Duże lub średnie organizacje najprawdopodobniej będą miały reagowanie na [incydenty bezpieczeństwa,](https://technet.microsoft.com/library/cc700825.aspx) aby pomóc it w podejmowaniu działań odpowiednio do poziomu incydentu. System zarządzania tożsamościami jest ważnym składnikiem w procesie reagowania na incydenty, ponieważ może służyć do identyfikacji, kto wykonał określone działanie przeciwko obiektowi docelowemu. Rozwiązanie tożsamości hybrydowej musi być w stanie zapewnić funkcje monitorowania i raportowania, które mogą być wykorzystywane przez IT do podejmowania działań w celu identyfikacji i łagodzenia potencjalnego zagrożenia. W typowym planie reagowania na incydenty w ramach planu będą dostępne następujące fazy:

1. Ocena wstępna.
2. Komunikacja incydentów.
3. Kontrola uszkodzeń i redukcja ryzyka.
4. Identyfikacja tego, co było kompromisem i dotkliwością.
5. Zachowanie dowodów.
6. Powiadomienie odpowiednich stron.
7. Odzyskiwanie systemu.
8. Dokumentacji.
9. Ocena szkód i kosztów.
10. Proces i planowanie rewizji.

Podczas identyfikacji tego, co było kompromisem i fazy ważności, konieczne będzie zidentyfikowanie systemów, które zostały naruszone, pliki, które zostały dostępne i określić czułość tych plików. Hybrydowy system tożsamości powinien być w stanie spełnić te wymagania, aby ułatwić identyfikację użytkownika, który dokonał tych zmian. 

## <a name="monitoring-and-reporting"></a>Monitorowanie i raportowanie
Wiele razy system tożsamości może również pomóc w fazie wstępnej oceny, głównie wtedy, gdy system ma wbudowane funkcje inspekcji i raportowania. Podczas wstępnej oceny administrator IT musi być w stanie zidentyfikować podejrzaną aktywność lub system powinien być w stanie wyzwolić ją automatycznie na podstawie wstępnie skonfigurowanego zadania. Wiele działań może wskazywać na możliwy atak, jednak w innych przypadkach źle skonfigurowany system może prowadzić do wielu fałszywych alarmów w systemie wykrywania włamań. 

System zarządzania tożsamościami powinien pomóc administratorom IT w identyfikowaniu i zgłaszaniu tych podejrzanych działań. Zazwyczaj te wymagania techniczne mogą być spełnione przez monitorowanie wszystkich systemów i posiadające zdolność raportowania, które mogą wyróżnić potencjalne zagrożenia. Skorzystaj z poniższych pytań, aby zaprojektować rozwiązanie tożsamości hybrydowej, biorąc pod uwagę wymagania dotyczące reagowania na incydenty:

* Czy Twoja firma ma reagowanie na incydenty bezpieczeństwa?
  * Jeśli tak, czy bieżący system zarządzania tożsamościami jest używany jako część procesu?
* Czy Twoja firma musi identyfikować podejrzane próby logowania od użytkowników na różnych urządzeniach?
* Czy twoja firma musi wykryć potencjalne poświadczenia użytkownika, którego bezpieczeństwo zostało naruszone?
* Czy Twoja firma musi przeprowadzić inspekcję dostępu i działań użytkownika?
* Czy Twoja firma musi wiedzieć, kiedy użytkownik resetuje swoje hasło?

## <a name="policy-enforcement"></a>Wymuszanie zasad
Podczas kontroli uszkodzeń i fazy redukcji ryzyka ważne jest, aby szybko zmniejszyć rzeczywiste i potencjalne skutki ataku. To działanie, które podejmiesz w tym momencie, może zrobić różnicę między małoletnim a głównym. Dokładna odpowiedź będzie zależeć od twojej organizacji i charakteru ataku, z którą się zmierzysz. Jeśli wstępna ocena wykaże, że konto zostało przejęte, musisz wymusić zasady, aby zablokować to konto. To tylko jeden z przykładów, w którym system zarządzania tożsamościami będzie lewarował. Skorzystaj z poniższych pytań, aby zaprojektować rozwiązanie tożsamości hybrydowej, biorąc pod uwagę, w jaki sposób zasady będą egzekwowane w celu reagowania na trwający incydent:

* Czy twoja firma ma zasady, aby uniemożliwić użytkownikom dostęp do sieci, jeśli to konieczne?
  * Jeśli tak, czy obecne rozwiązanie integruje się z hybrydowym systemem zarządzania tożsamościami, który zamierzasz przyjąć?
* Czy twoja firma musi wymusić dostęp warunkowy dla użytkowników poddanych kwarantannie? 

> [!NOTE]
> Pamiętaj, aby zanotować wszystkie odpowiedzi i zrozumieć ich uzasadnienie. [Zdefiniuj strategię ochrony danych](plan-hybrid-identity-design-considerations-data-protection-strategy.md) będzie wykraczać poza dostępne opcje i zalety / wady każdej opcji.  Odpowiadając na te pytania, wybierzesz opcję, która najlepiej odpowiada Twoim potrzebom biznesowym.
> 
> 

## <a name="next-steps"></a>Następne kroki
[Definiowanie strategii ochrony danych](plan-hybrid-identity-design-considerations-data-protection-strategy.md)

## <a name="see-also"></a>Zobacz też
[Omówienie zagadnień projektowych](plan-hybrid-identity-design-considerations-overview.md)

