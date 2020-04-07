---
title: Rozwiązywanie problemów z blokadą konta w usługach domenowych usługi Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się, jak rozwiązywać typowe problemy, które powodują zablokowanie kont użytkowników w Usługach domenowych Usługi domenowe Active Directory platformy Azure.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/06/2020
ms.author: iainfou
ms.openlocfilehash: 7d2e22804c06f589c7990bf8f19319b897363a93
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743455"
---
# <a name="troubleshoot-account-lockout-problems-with-an-azure-ad-domain-services-managed-domain"></a>Rozwiązywanie problemów z blokadą konta w domenie zarządzanej usług domenowych usługi AZURE AD

Aby zapobiec powtarzającym się złośliwym próbom logowania, usługi Azure AD DS blokuje konta po zdefiniowanym progu. Ta blokada konta może również nastąpić przez przypadek bez incydentu ataku logowania. Na przykład jeśli użytkownik wielokrotnie wprowadza nieprawidłowe hasło lub usługa próbuje użyć starego hasła, konto zostanie zablokowane.

W tym artykule dotyczącym rozwiązywania problemów opisano, dlaczego blokady konta i jak można skonfigurować zachowanie oraz jak przeglądać inspekcje zabezpieczeń w celu rozwiązywania problemów ze zdarzeniami blokady.

## <a name="what-is-an-account-lockout"></a>Co to jest blokada konta?

Konto użytkownika w usługach Azure AD DS jest zablokowane po spełnieniu zdefiniowanego progu dla nieudanych prób logowania. To zachowanie blokady konta ma na celu ochronę przed powtarzającymi się próbami logowania, które mogą wskazywać na automatyczny atak cyfrowy.

**Domyślnie, jeśli w ciągu 2 minut zostanie podjętych 5 prób nieprawidłowego hasła, konto zostanie zablokowane na 30 minut.**

Domyślne progi blokady konta są konfigurowane przy użyciu zasad haseł ziarnistych. Jeśli masz określony zestaw wymagań, możesz zastąpić te domyślne progi blokady konta. Nie zaleca się jednak zwiększania limitów progowych w celu zmniejszenia blokady kont. Najpierw rozwiąż problem ze źródłem zachowania blokady konta.

### <a name="fine-grained-password-policy"></a>Zasady dotyczące haseł drobnoziarnistych

Szczegółowe zasady haseł (FGPPs) umożliwiają stosowanie określonych ograniczeń dla zasad blokowania haseł i kont do różnych użytkowników w domenie. FGPP dotyczy tylko użytkowników w domenie zarządzanej usług Azure AD DS. Zasady haseł w usługach Azure AD DS mają wpływ tylko użytkownicy chmury i użytkownicy domeny zsynchronizowani z domeną usług Azure AD DS. Ich konta w usłudze Azure AD lub katalogu lokalnym nie mają wpływu.

Zasady są dystrybuowane za pośrednictwem skojarzenia grupy w domenie zarządzanej usługi Azure AD DS, a wszelkie wprowadzone zmiany są stosowane przy następnym logowanie użytkownika. Zmiana zasad nie odblokowuje konta użytkownika, które jest już zablokowane.

Aby uzyskać więcej informacji na temat zasad haseł precyzyjnych i różnic między użytkownikami utworzonymi bezpośrednio w usługach Azure AD DS w porównaniu z synchronizacją z usługi Azure AD, zobacz [Konfigurowanie zasad blokowania haseł i kont.][configure-fgpp]

## <a name="common-account-lockout-reasons"></a>Typowe przyczyny blokady konta

Najczęstsze przyczyny zablokowania konta bez żadnych złośliwych intencji lub czynników obejmują następujące scenariusze:

* **Użytkownik zablokował się.**
    * Czy po ostatniej zmianie hasła użytkownik nadal używał poprzedniego hasła? Domyślne zasady blokady konta 5 nieudanych prób w ciągu 2 minut może być spowodowane przez użytkownika przypadkowo ponowienie próby starego hasła.
* **Istnieje aplikacja lub usługa, która ma stare hasło.**
    * Jeśli konto jest używane przez aplikacje lub usługi, te zasoby mogą wielokrotnie próbować zalogować się przy użyciu starego hasła. To zachowanie powoduje, że konto jest zablokowane.
    * Spróbuj zminimalizować użycie konta w wielu różnych aplikacjach lub usługach i rejestrowanie, gdzie poświadczenia są używane. Jeśli hasło do konta zostanie zmienione, należy odpowiednio zaktualizować skojarzone aplikacje lub usługi.
* **Hasło zostało zmienione w innym środowisku, a nowe hasło nie zostało jeszcze zsynchronizowane.**
    * Jeśli hasło konta zostanie zmienione poza usługą Azure AD DS, na przykład w środowisku usług AD DS w przedsprzedaży, synchronizacja hasła za pośrednictwem usługi Azure AD i usług Azure AD DS może potrwać kilka minut.
    * Użytkownik, który próbuje zalogować się do zasobu za pośrednictwem usługi Azure AD DS przed zakończeniem tego procesu synchronizacji haseł powoduje, że ich konto jest zablokowane.

## <a name="troubleshoot-account-lockouts-with-security-audits"></a>Rozwiązywanie problemów z blokadami kont za pomocą inspekcji zabezpieczeń

Aby rozwiązać problem, gdy wystąpią zdarzenia blokady konta i skąd pochodzą, [włącz inspekcje zabezpieczeń dla usług Azure AD DS.][security-audit-events] Zdarzenia inspekcji są przechwytywane tylko od momentu włączenia funkcji. W idealnym przypadku należy włączyć inspekcje *zabezpieczeń, zanim* pojawi się problem z blokadą konta do rozwiązania. Jeśli konto użytkownika wielokrotnie ma problemy z blokadą, można włączyć inspekcje zabezpieczeń gotowe do następnego wystąpienia sytuacji.

Po włączeniu inspekcji zabezpieczeń poniższe przykładowe zapytania pokazują, jak przeglądać *zdarzenia blokady konta,* kod *4740*.

Wyświetl wszystkie zdarzenia blokady konta z ostatnich siedmiu dni:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

Wyświetl wszystkie zdarzenia blokady konta z ostatnich siedmiu dni dla konta o nazwie *driley*.

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
| where "driley" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

Zobacz wszystkie zdarzenia blokady konta między 26 czerwca 2019 o godzinie 9:00. i 1 lipca 2019 r., posortowane rosnąco według daty i godziny:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2019-06-26 09:00) and TimeGenerated <= datetime(2019-07-01)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat zasad haseł precyzyjnych w celu dostosowania progów blokady konta, zobacz [Konfigurowanie zasad blokowania haseł i kont][configure-fgpp].

Jeśli nadal występują problemy z dołączeniem maszyny Wirtualnej do domeny zarządzanej usług Azure AD DS, [znajdź pomoc i otwórz bilet pomocy technicznej dla usługi Azure Active Directory][azure-ad-support].

<!-- INTERNAL LINKS -->
[configure-fgpp]: password-policy.md
[security-audit-events]: security-audit-events.md
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
