---
title: Przewodnik po operacjach ogólnych usługi Azure Active Directory
description: W tym przewodniku po operacjach opisano kontrole i działania, które należy podjąć w celu zabezpieczenia operacji ogólnych
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
ms.openlocfilehash: d039373d3e70076149da2b970a234b59d7aa661a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422947"
---
# <a name="azure-active-directory-general-operations-guide-reference"></a>Przewodnik po operacjach ogólnych usługi Azure Active Directory

W tej sekcji [przewodnika odnośny do operacji usługi Azure AD](active-directory-ops-guide-intro.md) opisano kontrole i akcje, które należy podjąć w celu optymalizacji ogólnych operacji usługi Azure Active Directory (Azure AD).

> [!NOTE]
> Zalecenia te są aktualne na dzień publikacji, ale mogą się zmieniać w czasie. Organizacje powinny stale oceniać swoje praktyki operacyjne w miarę rozwoju produktów i usług firmy Microsoft w miarę rozwoju w czasie.

## <a name="key-operational-processes"></a>Kluczowe procesy operacyjne

### <a name="assign-owners-to-key-tasks"></a>Przypisywanie właścicieli do kluczowych zadań

Zarządzanie usługą Azure Active Directory wymaga ciągłego wykonywania kluczowych zadań operacyjnych i procesów, które mogą nie być częścią projektu wdrożenia. Nadal ważne jest, aby skonfigurować te zadania w celu optymalizacji środowiska. Do kluczowych zadań i ich zalecanych właścicieli należą:

| Zadanie | Właściciel |
| :- | :- |
| Ulepszenia dysku w zakresie bezpiecznego wyniku tożsamości | Zespół operacyjny InfoSec |
| Obsługa serwerów usługi Azure AD Connect | Zespół operacyjny IAM |
| Regularne wykonywanie i klasyfikowanie raportów IdFix | Zespół operacyjny IAM |
| Alerty kondycji usługi Azure AD Connect dla synchronizacji i usług AD FS | Zespół operacyjny IAM |
| Jeśli nie jest używany usługa Azure AD Connect Health, klient ma równoważny proces i narzędzia do monitorowania infrastruktury niestandardowej | Zespół operacyjny IAM |
| Jeśli nie korzystasz z usług AD FS, klient ma równoważny proces i narzędzia do monitorowania infrastruktury niestandardowej | Zespół operacyjny IAM |
| Monitorowanie dzienników hybrydowych: łączniki serwera proxy aplikacji usługi Azure AD | Zespół operacyjny IAM |
| Monitorowanie dzienników hybrydowych: Agenci uwierzytelniania przekazywanego | Zespół operacyjny IAM |
| Monitoruj dzienniki hybrydowe: Usługa zapisywania zwrotnego haseł | Zespół operacyjny IAM |
| Monitorowanie dzienników hybrydowych: lokalna brama ochrony hasłem | Zespół operacyjny IAM |
| Monitoruj dzienniki hybrydowe: rozszerzenie nps usługi Azure MFA (jeśli dotyczy) | Zespół operacyjny IAM |

Podczas przeglądania listy może okazać się konieczne przypisanie właściciela do zadań, w których brakuje właściciela, lub dostosowanie własności do zadań z właścicielami, które nie są zgodne z powyższymi zaleceniami.

#### <a name="owners-recommended-reading"></a>Właściciele zalecili czytanie

- [Przypisywanie ról administratorów w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Nadzór na platformie Azure](https://docs.microsoft.com/azure/security/governance-in-azure)

## <a name="hybrid-management"></a>Zarządzanie hybrydowe

### <a name="recent-versions-of-on-premises-components"></a>Najnowsze wersje składników lokalnych

Posiadanie najbardziej aktualnych wersji składników lokalnych zapewnia klientowi wszystkie najnowsze aktualizacje zabezpieczeń, ulepszenia wydajności, a także funkcje, które mogą pomóc w dalszym uproszczeniu środowiska. Większość składników ma ustawienie automatycznego uaktualniania, które automatyzuje proces uaktualniania.

Składniki te obejmują:

- Azure AD Connect
- Łączniki serwera proxy aplikacji usługi Azure AD
- Agenci uwierzytelniania przekazywania usługi Azure AD
- Agenci kondycji usługi Azure AD Connect

O ile nie zostało ustalone, należy zdefiniować proces uaktualniania tych składników i polegać na funkcji automatycznego uaktualniania, gdy tylko jest to możliwe. Jeśli znajdziesz składniki, które są sześć lub więcej miesięcy za, należy uaktualnić tak szybko, jak to możliwe.

#### <a name="hybrid-management-recommended-reading"></a>Zalecana do zarządzania hybrydowego odczyt

- [Azure AD Connect: automatyczne uaktualnianie](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-automatic-upgrade)
- [Opis łączników serwera proxy aplikacji usługi Azure AD | Aktualizacje automatyczne](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#automatic-updates)

### <a name="azure-ad-connect-health-alert-baseline"></a>Alert kondycji usługi Azure AD Connect według planu bazowego

Organizacje powinny wdrożyć [usługę Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-azure-ad-connect#what-is-azure-ad-connect-health) do monitorowania i raportowania usług Azure AD Connect i usług AD FS. Usługi Azure AD Connect i usługi AD FS są krytycznymi składnikami, które mogą przerwać zarządzanie cyklem życia i uwierzytelnianie, a tym samym prowadzić do awarii. Usługa Azure AD Connect Health pomaga monitorować i uzyskiwać wgląd w lokalną infrastrukturę tożsamości, zapewniając w ten sposób niezawodność środowiska.

![Architektura usługi Azure AD Connect Heath](./media/active-directory-ops-guide/active-directory-ops-img16.png)

Podczas monitorowania kondycji środowiska należy natychmiast rozwiązać wszelkie alerty o wysokim poziomie ważności, a następnie alerty o niższym poziomie ważności.

#### <a name="azure-ad-connect-health-recommended-reading"></a>Zalecane czytanie usługi Azure AD Connect Health

- [Instalowanie agenta programu Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-agent-install)

### <a name="on-premises-agents-logs"></a>Dzienniki agentów lokalnych

Niektóre usługi zarządzania tożsamościami i dostępem wymagają agentów lokalnych, aby włączyć scenariusze hybrydowe. Przykłady obejmują resetowanie hasła, uwierzytelnianie przekazywane (PTA), serwer proxy aplikacji usługi Azure AD i rozszerzenie nps usługi Azure MFA. Jest to kluczowe, że zespół operacyjny linii bazowej i monitorować kondycję tych składników przez archiwizowanie i analizowanie dzienników agenta składnika przy użyciu rozwiązań, takich jak System Center Operations Manager lub SIEM. Równie ważne jest, aby zespół operacji infosec lub pomoc owa obsługi zrozumiała sposób rozwiązywania problemów z wzorcami błędów.

#### <a name="on-premises-agents-logs-recommended-reading"></a>Lokalne dzienniki agentów zalecane odczyt

- [Rozwiązywanie problemów z serwerem proxy aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot)
- [Samoobsługowe resetowanie haseł — usługa Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#password-writeback-event-log-error-codes)
- [Opis łączników serwera proxy aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors)
- [Usługa Azure AD Connect: rozwiązywanie problemów z uwierzytelnianiem przekazywania](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-pass-through-authentication#collecting-pass-through-authentication-agent-logs)
- [Rozwiązywanie problemów z kodami błędów rozszerzenia serwera NPS usługi Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-errors)

### <a name="on-premises-agents-management"></a>Zarządzanie agentami lokalnymi

Przyjęcie najlepszych praktyk może pomóc w optymalnym działaniu agentów lokalnych. Należy wziąć pod uwagę następujące najlepsze rozwiązania:

- Wiele łączników serwera proxy usługi Azure AD na grupę łączników są zalecane w celu zapewnienia płynnego równoważenia obciążenia i wysokiej dostępności, unikając pojedynczych punktów awarii podczas uzyskiwania dostępu do aplikacji proxy. Jeśli obecnie masz tylko jeden łącznik w grupie łączników, który obsługuje aplikacje w produkcji, należy wdrożyć co najmniej dwa łączniki dla nadmiarowości.
- Tworzenie i używanie grupy łączników serwera proxy aplikacji do celów debugowania może być przydatne do rozwiązywania scenariuszy i dołączania nowych aplikacji lokalnych. Zaleca się również zainstalowanie narzędzi sieciowych, takich jak Analizator komunikatów i Skrzypek na komputerach łączników.
- Zaleca się wiele agentów uwierzytelniania przekazywania, aby zapewnić bezproblemowe równoważenie obciążenia i wysoką dostępność, unikając pojedynczego punktu awarii podczas przepływu uwierzytelniania. Pamiętaj, aby wdrożyć co najmniej dwa agentów uwierzytelniania przekazywania dla nadmiarowości.

#### <a name="on-premises-agents-management-recommended-reading"></a>Kierownictwo agentów lokalnych zaleciło odczyt

- [Opis łączników serwera proxy aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors)
- [Uwierzytelnianie przekazywane usługi Azure AD — szybki start](../hybrid/how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)

## <a name="management-at-scale"></a>Zarządzanie na dużą skalę

### <a name="identity-secure-score"></a>Wskaźnik bezpieczeństwa tożsamości

[Wynik bezpiecznego tożsamości](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score) zapewnia wymierną miarę postawy zabezpieczeń organizacji. Kluczem jest do ciągłego przeglądu i adres ustaleń zgłoszonych i dążyć do jak najwyższego wyniku możliwe. Ten wskaźnik ułatwi Ci:

- Obiektywne mierzenie poziomu bezpieczeństwa tożsamości
- Planowanie poprawy bezpieczeństwa tożsamości
- Sprawdzanie powodzenia wprowadzonych udoskonaleń

![Wskaźnik bezpieczeństwa](./media/active-directory-ops-guide/active-directory-ops-img17.png)

Jeśli organizacja nie ma obecnie programu do monitorowania zmian w bezpiecznym wyniku tożsamości, zaleca się wdrożenie planu i przypisanie właścicieli do monitorowania i prowadzenia działań ulepszania. Organizacje powinny jak najszybciej korygować działania udoskonalania z wynikiem wyższym niż 30.

### <a name="notifications"></a>Powiadomienia

Firma Microsoft wysyła wiadomości e-mail do administratorów w celu powiadamiania o różnych zmianach w usłudze, wymaganych aktualizacjach konfiguracji oraz błędach wymagających interwencji administratora. Ważne jest, aby klienci ustawiali adresy e-mail powiadomień, aby powiadomienia były wysyłane do odpowiednich członków zespołu, którzy mogą potwierdzać i działać na wszystkich powiadomieniach. Zaleca się dodanie wielu adresatów do [Centrum wiadomości usługi Office 365](https://docs.microsoft.com/office365/admin/manage/message-center) i żądanie, aby powiadomienia (w tym powiadomienia usługi Azure AD Connect Health) były wysyłane do listy dystrybucyjnej lub udostępnionej skrzynki pocztowej. Jeśli masz tylko jedno globalne konto administratora z adresem e-mail, skonfiguruj co najmniej dwa konta obsługujące wiadomości e-mail.

Istnieją dwa adresy "Od" używane <o365mc@email2.microsoft.com>przez usługę Azure AD: , który wysyła powiadomienia Centrum komunikatów usługi Office 365; oraz <azure-noreply@microsoft.com>, która wysyła powiadomienia dotyczące:

- [Recenzje programu Azure AD Access](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)
- [Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations#enable-email-notifications)
- [Azure AD Identity Protection](/azure/active-directory/identity-protection/howto-identity-protection-configure-notifications)
- [Azure AD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications)
- [Powiadomienia o certyfikatach wygasających aplikacji przedsiębiorstwa](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-certificates-for-federated-single-sign-on#add-email-notification-addresses-for-certificate-expiration)
- Powiadomienia o usłudze inicjowania obsługi administracyjnej aplikacji przedsiębiorstwa

Zapoznaj się z poniższą tabelą, aby dowiedzieć się, jakiego rodzaju powiadomienia są wysyłane i gdzie je sprawdzić:

| Źródło powiadomień | Co jest wysyłane | Gdzie sprawdzić |
|:-|:-|:-|
| Kontakt techniczny | Błędy synchronizacji | Azure portal - przymiot właściwości |
| Centrum wiadomości usługi Office 365 | Powiadomienia o zdarzeniach i degradacji usług tożsamości i usług zaplecza usługi O365 | Office Portal |
| Cotygodniowe podsumowanie ochrony tożsamości | Skrót ochrony tożsamości | Blok usługi Azure AD Identity Protection |
| Azure AD Connect Health | Powiadomienia o alertach | Portal Azure — narzędzie Azure AD Connect— przyłącze kondycji |
| Powiadomienia o aplikacjach dla przedsiębiorstw | Powiadomienia o wygaśnięciu certyfikatów i błędy inicjowania obsługi administracyjnej | Portal Azure — blok aplikacji dla przedsiębiorstw (każda aplikacja ma własne ustawienie adresu e-mail) |

#### <a name="notifications-recommended-reading"></a>Powiadomienia zalecane czytanie

- [Zmienianie adresu organizacji, kontaktu technicznego i innych liczniejszych — Office 365](https://docs.microsoft.com/office365/admin/manage/change-address-contact-and-more)

## <a name="operational-surface-area"></a>Powierzchnia operacyjna

### <a name="ad-fs-lockdown"></a>Blokada usługi AD FS

Organizacje, które konfigurują aplikacje do uwierzytelniania bezpośrednio do usługi Azure AD korzystają z [inteligentnej blokady usługi Azure AD.](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords) Jeśli używasz usług AD FS w systemie Windows Server 2012 R2, zaimplementuj [ochronę przed blokadą pozasieci](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)usług AD FS . Jeśli używasz usług AD FS w systemie Windows Server 2016 lub nowszym, zaimplementuj [inteligentną blokadę ekstranetu](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016). Zaleca się co najmniej włączenie blokady ekstranetu w celu ograniczenia ryzyka ataków siłowych na lokalną usługę Active Directory. Jeśli jednak masz usługi AD FS w systemie Windows 2016 lub nowszym, należy również włączyć inteligentną blokadę extranet, która pomoże ograniczyć ataki [natryskowe haseł.](https://www.microsoft.com/microsoft-365/blog/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/)

Jeśli usługi AD FS jest używany tylko dla federacji usługi Azure AD, istnieją pewne punkty końcowe, które można wyłączyć, aby zminimalizować obszar powierzchni ataku. Na przykład jeśli usługi AD FS są używane tylko dla usługi Azure AD, należy wyłączyć punkty końcowe WS-Trust inne niż punkty końcowe włączone dla **użytkownikamieciona** i **windowstransport**.

### <a name="access-to-machines-with-on-premises-identity-components"></a>Dostęp do maszyn z lokalnymi składnikami tożsamości

Organizacje powinny zablokować dostęp do komputerów z lokalnymi składnikami hybrydowymi w taki sam sposób, jak domena lokalna. Na przykład operator kopii zapasowej lub administrator funkcji Hyper-V nie powinien być w stanie zalogować się do usługi Azure AD Connect Server w celu zmiany reguł.

Model warstwy administracyjnej usługi Active Directory został zaprojektowany do ochrony systemów tożsamości przy użyciu zestawu stref buforowych między pełną kontrolą środowiska (warstwa 0) a zasobami stacji roboczych wysokiego ryzyka, które osoby atakujące często naruszają zabezpieczeń. ![Diagram przedstawiający trzy warstwy modelu warstwy](./media/active-directory-ops-guide/active-directory-ops-img18.png)

[Model warstwy](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material) składa się z trzech poziomów i obejmuje tylko konta administracyjne, a nie konta użytkowników standardowych.

- **Warstwa 0** — bezpośrednia kontrola tożsamości przedsiębiorstwa w środowisku. Warstwa 0 obejmuje konta, grupy i inne zasoby, które mają bezpośrednią lub pośrednią kontrolę administracyjną nad lasem usługi Active Directory, domenami, kontrolerami domeny i wszystkimi zasobami znajdującymi się w tym lesie. Czułość zabezpieczeń wszystkich zasobów warstwy 0 jest równoważna ich wzajemnej, efektywnej kontroli.
- **Warstwa 1** — kontrola serwerów i aplikacji przedsiębiorstwa. Zasoby warstwy 1 obejmują systemy operacyjne serwerów, usługi w chmurze i aplikacje używane w przedsiębiorstwie. Konta administratorów w warstwie 1 umożliwiają prowadzenie nadzoru nad znaczną częścią elementów generujących wartość biznesową, które są hostowane w tych zasobach. Typowym przykładem takiej roli są administratorzy serwerów, którzy obsługują te systemy operacyjne i mogą zmieniać działanie wszystkich usług w przedsiębiorstwie.
- **Warstwa 2** — kontrola stacji roboczych i urządzeń użytkowników. Konta administratorów w warstwie 2 umożliwiają prowadzenie nadzoru nad znaczną częścią elementów generujących wartość biznesową, które są hostowane na urządzeniach i stacjach roboczych użytkowników. Przykłady obejmują administratorów pomocy technicznej, którzy mają wpływ na spójność prawie wszystkich danych użytkowników.

Zablokuj dostęp do lokalnych składników tożsamości, takich jak Usługi Azure AD Connect, AD FS i usługi SQL w taki sam sposób, jak w przypadku kontrolerów domeny.

## <a name="summary"></a>Podsumowanie

Istnieje siedem aspektów bezpiecznej infrastruktury tożsamości. Ta lista pomoże Ci znaleźć akcje, które należy podjąć w celu optymalizacji operacji dla usługi Azure Active Directory (Azure AD).

- Przypisz właścicieli do kluczowych zadań.
- Zautomatyzuj proces uaktualniania lokalnych składników hybrydowych.
- Wdrażanie usługi Azure AD Connect Health do monitorowania i raportowania usług Azure AD Connect i usług AD FS.
- Monitoruj kondycję lokalnych składników hybrydowych, archiwizując i analizując dzienniki agenta składników przy użyciu programu System Center Operations Manager lub rozwiązania SIEM.
- Zaimplementuj ulepszenia zabezpieczeń, mierząc swoją postawę bezpieczeństwa za pomocą wyniku tożsamości Secure Score.
- Blokowanie usług AD FS.
- Zablokuj dostęp do maszyn za pomocą lokalnych składników tożsamości.

## <a name="next-steps"></a>Następne kroki

Szczegółowe informacje na temat implementacji nie zostały wdrożone w [planach wdrażania usługi Azure AD.](active-directory-deployment-plans.md)
