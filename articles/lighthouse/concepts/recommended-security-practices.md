---
title: Wskazówki dotyczące zabezpieczeń zalecane dla platformy Azure morskiej
description: Podczas korzystania z systemu Azure delegowane zarządzanie zasobami, warto wziąć pod uwagę zabezpieczeń i kontroli dostępu.
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 843b965e6ea74a7c11dc11459ff5d30ddbe5c987
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809869"
---
# <a name="recommended-security-practices"></a>Wskazówki dotyczące zalecanych zabezpieczeń

Podczas korzystania z systemu Azure delegowane zarządzanie zasobami, warto wziąć pod uwagę zabezpieczeń i kontroli dostępu. Użytkownicy w Twojej dzierżawie będzie miała bezpośredni dostęp do subskrypcji klienta i grupy zasobów, dlatego należy wykonać czynności, aby zachować bezpieczeństwo dzierżawy usługi. Należy również upewnić, że tylko zezwolisz na dostęp, potrzebne do skutecznego zarządzania zasobami Twoich klientów. Ten temat zawiera zalecenia, aby to zrobić.

## <a name="require-azure-multi-factor-authentication"></a>Wymagaj uwierzytelniania wieloskładnikowego Azure

[Usługa Azure Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) (znany także jako weryfikacji dwuetapowej) pomaga uniemożliwiają osobom atakującym uzyskanie dostępu do konta, wymagając wiele kroków uwierzytelniania. Należy wymagać uwierzytelniania wieloskładnikowego dla wszystkich użytkowników w dzierżawie dostawcy usług, w tym użytkownicy, którzy będą mieć dostęp do zasobów klienta.

Sugerujemy, możesz zadawać pytania klientów do wdrożenia usługi Azure Multi-Factor Authentication w również dzierżawcom.

## <a name="assign-permissions-to-groups-using-the-principle-of-least-privilege"></a>Przypisywanie uprawnień do grup, za pomocą zasadę najmniejszych uprawnień

Aby ułatwić zarządzanie, zaleca się przy użyciu grup użytkowników usługi Azure AD dla każdej roli, wymagane do zarządzania zasobami Twoich klientów. Dzięki temu można dodawać lub usuwać pojedynczych użytkowników do grupy, zgodnie z potrzebami, a nie przypisywania uprawnień bezpośrednio do tego użytkownika.

Podczas tworzenia struktury uprawnień, pamiętaj, że postępuj zgodnie z zasadą najniższych uprawnień, aby użytkownicy mają tylko uprawnienia wymagane do wykonania swojej pracy, aby zmniejszyć prawdopodobieństwo przypadkowego błędy.

Na przykład można użyć struktury następująco:

|Nazwa grupy  |Type  |principalId  |Definicja roli  |Identyfikator definicji roli  |
|---------|---------|---------|---------|---------|
|Architekci     |Grupy użytkowników         |\<principalId\>         |Współautor         |b24988ac-6180-42a0-ab88-20f7382dd24c  |
|Ocena     |Grupy użytkowników         |\<principalId\>         |Czytelnik         |acdd72a7-3385-48ef-bd42-f606fba81ae7  |
|Maszyna wirtualna specjaliści ds.     |Grupy użytkowników         |\<principalId\>         |Współautor maszyny Wirtualnej         |9980e02c-c2be-4d73-94e8-173b1dc7cf3c  |
|Automatyzacja     |Główna nazwa usługi (SPN)         |\<principalId\>         |Współautor         |b24988ac-6180-42a0-ab88-20f7382dd24c  |

Po utworzeniu grupy można przypisać użytkowników, zgodnie z potrzebami. Dodawać tylko użytkowników, którzy rzeczywiście muszą mieć dostęp. Pamiętaj regularnie przeglądanie członkostwa w grupie i usuń wszystkich użytkowników, które nie są już odpowiednie lub niezbędne do uwzględnienia.

Należy pamiętać, że w przypadku możesz [angażowania nowych klientów za pośrednictwem oferty publicznej usługi zarządzanej](../how-to/publish-managed-services-offers.md), wszystkie grupy (lub użytkownika lub nazwy głównej usługi), możesz uwzględnić będą mieć takie same uprawnienia dla każdego klienta, który zakupi plan. Aby przypisać różnych grup, aby pracować z każdego klienta, należy opublikować oddzielne prywatne plan wyłącznie dla każdego klienta lub dołączanie klientów indywidualnie przy użyciu szablonów usługi Azure Resource Manager. Na przykład spróbujesz opublikować publiczny plan, który ma bardzo ograniczony dostęp, a następnie pracować z klientem bezpośrednio do dołączenia ich zasoby dla dodatkowych praw dostępu za pomocą dostosowanego szablonu zasobów platformy Azure, udzielanie dodatkowe prawa dostępu, zgodnie z potrzebami.


## <a name="next-steps"></a>Następne kroki

- [Wdrażanie usługi Azure Multi-Factor Authentication](../../active-directory/authentication/howto-mfa-getstarted.md).
- Dowiedz się więcej o [międzydzierżawowa środowiska zarządzania](cross-tenant-management-experience.md).
