---
title: Rozwiązywanie problemów z logowaniem z dostępem warunkowym — usługa Azure Active Directory
description: W tym artykule opisano, co zrobić, gdy zasady dostępu warunkowego powodują nieoczekiwane wyniki
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 03/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdf1daca79f3ed20d9b7a89af20d74ff5f3148b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337443"
---
# <a name="troubleshooting-sign-in-problems-with-conditional-access"></a>Rozwiązywanie problemów z logowaniem z dostępem warunkowym

Informacje zawarte w tym artykule mogą służyć do rozwiązywania problemów z nieoczekiwanymi wynikami logowania związanymi z dostępem warunkowym przy użyciu komunikatów o błędach i dziennika logowania usługi Azure AD.

## <a name="conditional-access-sign-in-interrupt"></a>Przerwanie logowania dostępu warunkowego

Pierwszym sposobem jest przejrzenie wyświetla się komunikatu o błędzie. W przypadku problemów z logowaniem się podczas korzystania z przeglądarki internetowej sama strona błędu zawiera szczegółowe informacje. Tylko te informacje mogą opisywać, na czym polega problem i które mogą sugerować rozwiązanie.

![Błąd logowania — wymagane urządzenie zgodne](./media/troubleshoot-conditional-access/image1.png)

W powyższym błędzie komunikat stwierdza, że dostęp do aplikacji można uzyskać tylko z urządzeń lub aplikacji klienckich, które spełniają zasady zarządzania urządzeniami przenośnymi firmy. W takim przypadku aplikacja i urządzenie nie spełniają tych zasad.

## <a name="azure-ad-sign-in-events"></a>Zdarzenia logowania usługi Azure AD

Drugą metodą, aby uzyskać szczegółowe informacje na temat przerwy w logowanie jest przejrzenie zdarzeń logowania usługi Azure AD, aby zobaczyć, które zasady dostępu warunkowego lub zasady zostały zastosowane i dlaczego.

Więcej informacji na temat problemu można znaleźć, klikając **więcej szczegółów** na początkowej stronie błędu. Kliknięcie **przycisku Więcej szczegółów** spowoduje wyświetli informacje dotyczące rozwiązywania problemów, które są przydatne podczas przeszukiwania zdarzeń logowania usługi Azure AD dla określonego zdarzenia awarii, które użytkownik zobaczył lub podczas otwierania zdarzenia pomocy technicznej z firmą Microsoft.

![Więcej szczegółów z programu Dostęp warunkowy przerwane logowanie do przeglądarki sieci Web.](./media/troubleshoot-conditional-access/image2.png)

Aby dowiedzieć się, które zasady lub zasady dostępu warunkowego stosowane i dlaczego wykonaj następujące czynności.

1. Zaloguj się do **witryny Azure portal** jako administrator globalny, administrator zabezpieczeń lub czytnik globalny.
1. Przejdź do logowania usługi **Azure Active Directory** > **.**
1. Znajdź zdarzenie do sprawdzenia logowania. Dodaj lub usuń filtry i kolumny, aby odfiltrować niepotrzebne informacje.
   1. Dodaj filtry, aby zawęzić zakres:
      1. **Identyfikator korelacji,** gdy masz określone zdarzenie do zbadania.
      1. **Dostęp warunkowy,** aby zobaczyć niepowodzenie i powodzenie zasad. Zakres filtru, aby wyświetlić tylko błędy, aby ograniczyć wyniki.
      1. **Nazwa użytkownika,** aby wyświetlić informacje związane z określonymi użytkownikami.
      1. **Data** obejmuje dany przedział czasowy.

   ![Wybieranie filtru Dostęp warunkowy w dzienniku logowania](./media/troubleshoot-conditional-access/image3.png)

1. Po znalezieniu zdarzenia logowania odpowiadającego niepowodzeniu logowania użytkownika wybierz kartę **Dostęp warunkowy.** Na karcie Dostęp warunkowy zostaną wyświetlone określone zasady lub zasady, które spowodowały przerwanie logowania.
   1. Informacje na karcie **Rozwiązywanie problemów i pomoc techniczna** mogą stanowić wyraźny powód, dla którego logowanie nie powiodło się, takie jak urządzenie, które nie spełnia wymagań dotyczących zgodności.
   1. Aby dokładniej zbadać, przejdź do konfiguracji zasad, klikając nazwę **zasad**. Kliknięcie **przycisku Nazwa zasad** spowoduje wyświetlenie interfejsu użytkownika konfiguracji zasad dla wybranych zasad do przeglądania i edytowania.
   1. Dane użytkownika i **urządzenia** **klienta,** które zostały użyte do oceny zasad dostępu warunkowego, są również dostępne na kartach **Informacje podstawowe**, **Lokalizacja**, Informacje **o urządzeniu,** **Szczegóły uwierzytelniania**i **Dodatkowe szczegóły** zdarzenia logowania.

   ![Karta Logowania w przypadku dostępu warunkowego](./media/troubleshoot-conditional-access/image5.png)

Jeśli informacje w zdarzeniu nie wystarczy, aby zrozumieć wyniki logowania lub dostosować zasady, aby uzyskać pożądane wyniki, a następnie zdarzenie pomocy technicznej może zostać otwarty. Przejdź do karty **Rozwiązywanie problemów i pomocy technicznej** tego zdarzenia logowania i wybierz pozycję **Utwórz nowe żądanie pomocy technicznej**.

![Karta Rozwiązywanie problemów i pomoc techniczna zdarzenia logowania](./media/troubleshoot-conditional-access/image6.png)

Przesyłając zdarzenie, podaj identyfikator żądania oraz godzinę i datę od zdarzenia logowania w szczegółach zgłoszenia incydentu. Te informacje umożliwią firmie Microsoft pomoc techniczną w znalezieniu zdarzenia, którego dotyczysz.

### <a name="conditional-access-error-codes"></a>Kody błędów dostępu warunkowego

| Kod błędu logowania | Ciąg błędu |
| --- | --- |
| 53000 | DeviceNotCompliant (Nieskładanie urządzeń) |
| 53001 | DeviceNotDomainJoined |
| 53002 | ApplicationUsedIsNotAnApprovedApp |
| 53003 | BlockedByConditionalAccess |
| 53004 | Korekta blokady dowoduDueToRisk |

## <a name="next-steps"></a>Następne kroki

- [Raporty dotyczące logowań w portalu Azure Active Directory](../reports-monitoring/concept-sign-ins.md)
- [Rozwiązywanie problemów z dostępem warunkowym za pomocą narzędzia Co jeśli](troubleshoot-conditional-access-what-if.md)
- Najważniejsze wskazówki dotyczące [dostępu warunkowego w usłudze Azure Active Directory](best-practices.md)
