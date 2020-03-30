---
title: Przewodnik po operacjach usługi Azure Active Directory
description: W tym przewodniku dotyczącym operacji opisano kontrole i akcje, które należy podjąć w celu zabezpieczenia i utrzymania zarządzania tożsamościami i dostępem, uwierzytelniania, nadzoru i operacji
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: c815256e60e5ca6582220d247d587a4488c4a55a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74535316"
---
# <a name="azure-active-directory-operations-reference-guide"></a>Przewodnik po operacjach usługi Azure Active Directory

W tym przewodniku po operacjach opisano kontrole i działania, które należy podjąć w celu zabezpieczenia i utrzymania następujących obszarów:

- **[Zarządzanie tożsamościami i dostępem](active-directory-ops-guide-iam.md)** - możliwość zarządzania cyklem życia tożsamości i ich uprawnień.
- **[Zarządzanie uwierzytelnianiem](active-directory-ops-guide-auth.md)** — możliwość zarządzania poświadczeniami, definiowania środowiska uwierzytelniania, przypisywania delegowania, mierzenia użycia i definiowania zasad dostępu na podstawie postawy zabezpieczeń przedsiębiorstwa.
- **[Zarządzanie](active-directory-ops-guide-govern.md)** — możliwość oceny i testowania dostępu przyznane tożsamości nieuprzywilejowanych i uprzywilejowanych, inspekcji i kontroli zmian w środowisku.
- **[Operacje](active-directory-ops-guide-ops.md)** — optymalizacja operacji usługi Azure Active Directory (Azure AD).

Niektóre zalecenia w tym miejscu mogą nie mieć zastosowania do środowiska wszystkich klientów, na przykład najlepsze rozwiązania usługi AD FS mogą nie mieć zastosowania, jeśli organizacja używa synchronizacji skrótów haseł.

> [!NOTE]
> Zalecenia te są aktualne na dzień publikacji, ale mogą się zmieniać w czasie. Organizacje powinny stale oceniać swoje praktyki tożsamości w miarę rozwoju produktów i usług firmy Microsoft w miarę rozwoju w czasie. Zalecenia mogą ulec zmianie, gdy organizacje subskrybują inną licencję usługi Azure AD Premium. Na przykład usługa Azure AD Premium P2 będzie zawierać więcej zaleceń dotyczących nadzoru.

## <a name="stakeholders"></a>Zainteresowanych stron

Każda sekcja w tym przewodniku referencyjnym zaleca przypisanie zainteresowanych stron do pomyślnego planowania i wdrażania kluczowych zadań. W poniższej tabeli przedstawiono listę wszystkich zainteresowanych stron w tym przewodniku:

| Uczestnik projektu | Opis |
| :- | :- |
| Zespół operacyjny IAM | Ten zespół zajmuje się zarządzaniem codziennymi operacjami systemu zarządzania tożsamościami i dostępem |
| Zespół produktywności | Ten zespół jest właścicielem aplikacji zwiększających produktywność, takich jak poczta e-mail, udostępnianie i współpraca plików, wiadomości błyskawiczne i konferencje. |
| Właściciel aplikacji | Ten zespół jest właścicielem określonej aplikacji z firmy i zwykle techniczne punktu widzenia w organizacji. |
| Zespół architektury InfoSec | Ten zespół planuje i projektuje praktyki bezpieczeństwa informacji w organizacji. |
| Zespół operacyjny InfoSec | Ten zespół uruchamia i monitoruje wdrożone praktyki bezpieczeństwa informacji zespołu architektury InfoSec. |

## <a name="next-steps"></a>Następne kroki

Wprowadzenie do [sprawdzania i akcji zarządzania tożsamościami i dostępem](active-directory-ops-guide-iam.md).
