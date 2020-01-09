---
title: Kontrola zabezpieczeń Azure — reagowanie na zdarzenia
description: Odpowiedź na zdarzenia kontroli zabezpieczeń
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: b027a668403f47e9ffb824179ae54b89cded7a0c
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75564233"
---
# <a name="security-control-incident-response"></a>Kontrola zabezpieczeń: odpowiedź na zdarzenie

Ochrona informacji organizacji, a także jej reputacja, poprzez opracowywanie i wdrażanie infrastruktury odpowiedzi na zdarzenia (np. planów, zdefiniowanych ról, szkoleń, komunikacji, nadzoru zarządzania) w celu szybkiego wykrywania ataku, a następnie skutecznie zawiera to uszkodzenie, likwidowanie obecności osoby atakującej i przywrócenie integralności sieci i systemów.

## <a name="101-create-an-incident-response-guide"></a>10,1: Tworzenie przewodnika odpowiedzi na zdarzenia

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 10.1 | 19,1, 19,2, 19,3 | Klient |

Utwórz Przewodnik po odpowiedzi na zdarzenia dla swojej organizacji. Upewnij się, że istnieją zarejestrowane plany reakcji na zdarzenia, które definiują wszystkie role pracowników, a także etapy obsługi zdarzeń/zarządzania z wykrywania do oceny po zdarzeniu.

Jak skonfigurować automatyzację przepływu pracy w ramach Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Anatomia w centrum Microsoft Security Response:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Klient może także skorzystać z przewodnika obsługi zdarzeń związanych z bezpieczeństwem programu NIST, aby pomóc w tworzeniu własnego planu reagowania na zdarzenia:

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: Tworzenie oceny incydentu i procedury priorytetyzacji

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 10.2 | 19,8 | Klient |

Security Center przypisuje ważność do każdego alertu, aby pomóc w ustaleniu, które alerty należy najpierw zbadać. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analitycznym używanym do wystawiania alertu, a także poziom pewności, że istniało złośliwy wpływ na działanie, które prowadziło do alertu.

Dodatkowo jasno Oznacz subskrypcje (na przykład produkcyjny, nieprodukcyjny) i Utwórz system nazewnictwa, aby jasno identyfikować i klasyfikować zasoby platformy Azure.

## <a name="103-test-security-response-procedures"></a>10,3: procedury odpowiedzi na zabezpieczenia testowe

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 10,3 | 19 | Klient |

Przeprowadzaj ćwiczenia, aby testować możliwości reagowania na zdarzenia systemu w regularnych erze. Zidentyfikuj słabe punkty i przerwy i popraw plan zgodnie z wymaganiami.

Zapoznaj się z publikacją NIST: Przewodnik dotyczący testowania, uczenia i ćwiczeń programów dla planów i możliwości IT:

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-nbspfor-security-incidents"></a>10,4: Podaj szczegóły kontaktu dotyczącego zabezpieczeń i Skonfiguruj powiadomienia o alertach &nbsp;dla zdarzeń związanych z zabezpieczeniami

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 10,4 | 19,5 | Klient |

Firma Microsoft będzie używać informacji kontaktowych o zdarzeniach dotyczących zabezpieczeń, aby skontaktować się z Tobą, jeśli firma Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych klienta został uzyskany przez nielegalną lub nieautoryzowaną osobę.  Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane.

Jak ustawić Azure Security Center kontaktu zabezpieczeń:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

## <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Uwzględnij alerty zabezpieczeń w systemie odpowiedzi na zdarzenia

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 10.5 | 19,6 | Klient |

Eksportuj alerty i zalecenia dotyczące Azure Security Center przy użyciu funkcji eksportu ciągłego. Eksport ciągły umożliwia wyeksportowanie alertów i zaleceń ręcznie lub w stały sposób ciągły. Możesz użyć łącznika danych Azure Security Center, aby przesłać strumieniowo wskaźnik do alertów.

Jak skonfigurować eksport ciągły:

https://docs.microsoft.com/azure/security-center/continuous-export

Jak przesłać strumieniowo alerty do usługi Azure wskaźnikowej:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

## <a name="106-automate-the-response-to-security-alerts"></a>10,6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 10,6 | 19 | Klient |

Funkcja automatyzacji przepływu pracy w programie Azure Security Center umożliwia automatyczne wyzwalanie odpowiedzi za pośrednictwem &quot;Logic Apps&quot; na temat alertów zabezpieczeń i zaleceń.

Jak skonfigurować automatyzację przepływu pracy i Logic Apps:

https://docs.microsoft.com/azure/security-center/workflow-automation

## <a name="next-steps"></a>Następne kroki

Zobacz następną kontrolę zabezpieczeń: [testy penetracji i czerwone ćwiczenia zespołu](security-control-penetration-tests-red-team-exercises.md)