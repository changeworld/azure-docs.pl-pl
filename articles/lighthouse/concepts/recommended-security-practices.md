---
title: Zalecane najlepsze rozwiązania dotyczące zabezpieczeń
description: Podczas korzystania z usługi Azure delegowanego zarządzania zasobami, ważne jest, aby wziąć pod uwagę zabezpieczeń i kontroli dostępu.
ms.date: 03/24/2020
ms.topic: conceptual
ms.openlocfilehash: d9b806aaf988fedfde6ce468f3eff948aa8ce344
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246912"
---
# <a name="recommended-security-practices"></a>Zalecane najlepsze rozwiązania dotyczące zabezpieczeń

Podczas korzystania z [usługi Azure delegowanego zarządzania zasobami,](azure-delegated-resource-management.md)ważne jest, aby wziąć pod uwagę zabezpieczeń i kontroli dostępu. Użytkownicy w dzierżawie będą mieli bezpośredni dostęp do subskrypcji klientów i grup zasobów, więc należy podjąć kroki w celu utrzymania zabezpieczeń dzierżawy. Należy również upewnić się, że zezwalasz tylko na dostęp potrzebny do skutecznego zarządzania zasobami klientów. Ten temat zawiera zalecenia, które pomogą Ci to zrobić.

## <a name="require-azure-multi-factor-authentication"></a>Wymaganie usługi Azure Multi-Factor Authentication

[Uwierzytelnianie wieloskładnikowe platformy Azure](../../active-directory/authentication/concept-mfa-howitworks.md) (znane również jako weryfikacja dwuetapowa) pomaga uniemożliwić osobom atakującym uzyskanie dostępu do konta, wymagając wielu kroków uwierzytelniania. Należy wymagać uwierzytelniania wieloskładnikowego dla wszystkich użytkowników w dzierżawie dostawcy usług, w tym dla wszystkich użytkowników, którzy będą mieli dostęp do zasobów klienta.

Sugerujemy, aby poprosić klientów o wdrożenie uwierzytelniania wieloskładnikowego platformy Azure również w ich dzierżawach.

## <a name="assign-permissions-to-groups-using-the-principle-of-least-privilege"></a>Przypisywanie uprawnień do grup przy użyciu zasady najmniejszych uprawnień

Aby ułatwić zarządzanie, zaleca się używanie grup użytkowników usługi Azure AD dla każdej roli wymaganej do zarządzania zasobami klientów. Dzięki temu można dodać lub usunąć poszczególnych użytkowników do grupy w razie potrzeby, zamiast przypisywania uprawnień bezpośrednio do tego użytkownika.

> [!IMPORTANT]
> Aby dodać uprawnienia dla grupy usługi Azure AD, **typem grupy** musi być **security,** a nie **Office 365**. Ta opcja jest zaznaczona podczas tworzenia grupy. Aby uzyskać więcej informacji, zobacz [Tworzenie podstawowej grupy i dodawanie członków w usłudze Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Podczas tworzenia struktury uprawnień należy przestrzegać zasady najmniejszych uprawnień, aby użytkownicy mieli tylko uprawnienia potrzebne do ukończenia zadania, co pomaga zmniejszyć ryzyko niezamierzonych błędów.

Na przykład można użyć struktury w ten sposób:

|Nazwa grupy  |Typ  |principalId  |Definicja roli  |Identyfikator definicji roli  |
|---------|---------|---------|---------|---------|
|Architektów     |Grupa użytkowników         |\<principalId\>         |Współautor         |b24988ac-6180-42a0-ab88-20f7382dd24c  |
|Ocena     |Grupa użytkowników         |\<principalId\>         |Czytelnik         |acdd72a7-3385-48ef-bd42-f606fba81ae7  |
|Specjaliści od maszyn wirtualnych     |Grupa użytkowników         |\<principalId\>         |Współautor maszyny Wirtualnej         |9980e02c-c2be-4d73-94e8-173b1dc7cf3c  |
|Automatyzacja     |Nazwa główna usługi (SPN)         |\<principalId\>         |Współautor         |b24988ac-6180-42a0-ab88-20f7382dd24c  |

Po utworzeniu tych grup można przypisać użytkowników w razie potrzeby. Dodaj tylko użytkowników, którzy naprawdę muszą mieć dostęp. Pamiętaj, aby regularnie sprawdzać członkostwo w grupie i usuwać wszystkich użytkowników, którzy nie są już odpowiedni lub niezbędni do uwzględnienia.

Należy pamiętać, że podczas [dołączania klientów za pośrednictwem publicznej oferty usług zarządzanych](../how-to/publish-managed-services-offers.md)każda grupa (lub użytkownik lub usługodawca), która zostanie doprowadzona, będzie miała takie same uprawnienia dla każdego klienta, który kupuje plan. Aby przypisać różne grupy do pracy z każdym klientem, musisz opublikować oddzielny plan prywatny, który jest dostępny wyłącznie dla każdego klienta lub klientów wbudowanych indywidualnie przy użyciu szablonów usługi Azure Resource Manager. Na przykład można opublikować plan publiczny, który ma bardzo ograniczony dostęp, a następnie pracować z klientem bezpośrednio do wbudowanych zasobów dla dodatkowego dostępu przy użyciu dostosowanego szablonu zasobów platformy Azure, przyznając dodatkowy dostęp w razie potrzeby.


## <a name="next-steps"></a>Następne kroki

- [Wdrażanie uwierzytelniania wieloskładnikowego platformy Azure](../../active-directory/authentication/howto-mfa-getstarted.md).
- Dowiedz się więcej o [środowiskach zarządzania między dzierżawcami](cross-tenant-management-experience.md).
