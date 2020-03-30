---
title: Kontrola zabezpieczeń platformy Azure — reagowanie na incydenty
description: Reakcja na incydent kontroli bezpieczeństwa
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: fb3560aa2d3fbf48ab63c4da4d3a8d69cb677209
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934417"
---
# <a name="security-control-incident-response"></a>Kontrola bezpieczeństwa: reagowanie na incydenty

Ochrona informacji organizacji, a także jej reputacji, poprzez opracowanie i wdrożenie infrastruktury reagowania na incydenty (np. plany, określone role, szkolenia, komunikacja, nadzór nad zarządzaniem) w celu szybkiego wykrycia ataku, a następnie skutecznie ograniczając uszkodzenia, eliminując obecność atakującego i przywracając integralność sieci i systemów.

## <a name="101-create-an-incident-response-guide"></a>10.1: Tworzenie przewodnika po zdarzeniach

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 10.1 | 19.1, 19.2, 19.3 | Klient |

Skompiluj przewodnik po zdarzeniach dla swojej organizacji. Upewnij się, że istnieją pisemne plany reagowania na incydenty, które definiują wszystkie role personelu, a także fazy obsługi incydentów/zarządzania od wykrywania do przeglądu po incydencie.

Jak skonfigurować automatyzacje przepływu pracy w usłudze Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Wskazówki dotyczące tworzenia własnego procesu reagowania na incydenty bezpieczeństwa:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Anatomia incydentu w centrum reagowania na zabezpieczenia firmy Microsoft:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Klient może również wykorzystać przewodnik obsługi incydentów związanych z bezpieczeństwem komputerowym NIST, aby pomóc w stworzeniu własnego planu reagowania na incydenty:

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Tworzenie procedury oceniania incydentów i ustalania priorytetów

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 10.2 | 19.8 | Klient |

Usługa Security Center przypisuje ważność do każdego alertu, aby ułatwić ustalanie priorytetów alertów, które powinny być najpierw zbadane. Ważność opiera się na tym, jak pewny jest usługa Security Center w znalezieniu lub analityczna używana do wystawiania alertu, a także poziom zaufania, że za działaniem, które doprowadziło do alertu, było złośliwe intencje.

Dodatkowo wyraźnie zaznacz subskrypcje (np. nieprod) i utworzyć system nazewnictwa, aby jasno identyfikować i kategoryzować zasoby platformy Azure.

## <a name="103-test-security-response-procedures"></a>10.3: Testowanie procedur reagowania na zabezpieczenia

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 10.3 | 19 | Klient |

Przeprowadzaj ćwiczenia, aby przetestować możliwości reagowania na incydenty w systemach na regularnym rytmie. Zidentyfikować słabe punkty i luki oraz w razie potrzeby dokonać przeglądu planu.

Zapoznaj się z publikacją NIST: Przewodnik po testach, szkoleniach i programach ćwiczeń dla planów i możliwości IT:

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Podaj dane kontaktowe zdarzeń bezpieczeństwa i konfiguruj powiadomienia o alertach dla incydentów bezpieczeństwa

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 10.4 | 19.5 | Klient |

Informacje kontaktowe dotyczące zdarzeń związanych z bezpieczeństwem będą używane przez firmę Microsoft do kontaktu się z Użytkownikiem, jeśli Centrum Microsoft Security Response Center (MSRC) odkryje, że dane klienta zostały uzyskiane przez osobę niezgodną z prawem lub nieautoryzowaną.  Przejrzyj incydenty po fakcie, aby upewnić się, że problemy zostały rozwiązane.

Jak ustawić kontakt zabezpieczeń usługi Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

## <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Włącz alerty bezpieczeństwa do systemu reagowania na incydenty

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 10.5 | 19.6 | Klient |

Eksportuj alerty i zalecenia usługi Azure Security Center przy użyciu funkcji ciągłego eksportowania. Ciągły eksport umożliwia eksportowanie alertów i zaleceń ręcznie lub w sposób ciągły. Możesz użyć łącznika danych usługi Azure Security Center do przesyłania strumieniowego alertów Sentinel.

Jak skonfigurować eksport ciągły:

https://docs.microsoft.com/azure/security-center/continuous-export

Jak przesyłać strumieniowo alerty do usługi Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

## <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatyzacja reakcji na alerty bezpieczeństwa

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 10.6 | 19 | Klient |

Użyj funkcji Automatyzacja przepływu pracy w usłudze Azure &quot;Security&quot; Center, aby automatycznie wyzwalać odpowiedzi za pośrednictwem aplikacji logiki w alertach i zaleceniach dotyczących zabezpieczeń.

Jak skonfigurować automatyzację przepływu pracy i aplikacje logiki:

https://docs.microsoft.com/azure/security-center/workflow-automation

## <a name="next-steps"></a>Następne kroki

Zobacz następną kontrolę bezpieczeństwa: [testy penetracyjne i ćwiczenia czerwonej drużyny](security-control-penetration-tests-red-team-exercises.md)