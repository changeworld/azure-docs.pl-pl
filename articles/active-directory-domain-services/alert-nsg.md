---
title: Rozwiązywanie alertów sieciowej grupy zabezpieczeń w usługach Azure AD DS | Dokumenty firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z alertami konfiguracji sieciowej grupy zabezpieczeń i rozwiązywać je w usługach domenowych Usługi domenowe Active Directory
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/19/2019
ms.author: iainfou
ms.openlocfilehash: 959f1e3f25602938d769c574ea975c4bba9300e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71258005"
---
# <a name="known-issues-network-configuration-alerts-in-azure-active-directory-domain-services"></a>Znane problemy: alerty konfiguracji sieci w usługach domenowych Active Directory platformy Azure

Aby umożliwić aplikacjom i usługom poprawne komunikowanie się z usługami domenowymi Usługi Active Directory azure (Usługi Azure AD DS), określone porty sieciowe muszą być otwarte, aby umożliwić przepływ ruchu. Na platformie Azure można kontrolować przepływ ruchu za pomocą grup zabezpieczeń sieci. Stan kondycji domeny zarządzanej usług Azure AD DS wyświetla alert, jeśli nie są stosowane wymagane reguły sieciowej grupy zabezpieczeń.

Ten artykuł ułatwia zrozumienie i rozwiązanie typowych alertów dotyczących problemów z konfiguracją sieciowej grupy zabezpieczeń.

## <a name="alert-aadds104-network-error"></a>Alert AADDS104: Błąd sieci

### <a name="alert-message"></a>Komunikat ostrzegawczy

*Firma Microsoft nie może skontaktować się z kontrolerami domeny dla tej domeny zarządzanej. Może się tak zdarzyć, jeśli sieciowa grupa zabezpieczeń skonfigurowana w sieci wirtualnej zablokuje dostęp do domeny zarządzanej. Innym możliwym powodem jest to, że istnieje trasa zdefiniowana przez użytkownika, która blokuje ruch przychodzący z Internetu.*

Nieprawidłowe reguły sieciowej grupy zabezpieczeń są najczęstszą przyczyną błędów sieciowych dla usług Azure AD DS. Sieciowa grupa zabezpieczeń sieci wirtualnej musi zezwalać na dostęp do określonych portów i protokołów. Jeśli te porty są zablokowane, platforma Azure nie może monitorować ani aktualizować domeny zarządzanej. Wpływa również na synchronizację między katalogiem usługi Azure AD i domeną zarządzana usługą Azure AD DS. Upewnij się, że domyślne porty są otwarte, aby uniknąć przerw w działaniu usługi.

## <a name="default-security-rules"></a>Domyślne reguły zabezpieczeń

Następujące domyślne reguły zabezpieczeń przychodzących i wychodzących są stosowane do sieciowej grupy zabezpieczeń dla domeny zarządzanej usług Azure AD DS. Te reguły zapewniają bezpieczeństwo usług Azure AD DS i umożliwiają platformie Azure monitorowanie domeny zarządzanej, zarządzanie nimi i aktualizowanie ich. Może również być dostępna dodatkowa reguła, która zezwala na ruch przychodzący, jeśli [skonfigurujesz bezpieczny protokół LDAP][configure-ldaps].

### <a name="inbound-security-rules"></a>Reguły zabezpieczeń dla ruchu przychodzącego

| Priorytet | Nazwa | Port | Protocol (Protokół) | Element źródłowy | Element docelowy | Akcja |
|----------|------|------|----------|--------|-------------|--------|
| 101      | AllowSyncWithAzureAD | 443 | TCP | Usługi AzureActiveDirectoryDomainServices | Dowolne | Zezwalaj |
| 201      | AllowRD (Zezwalaj na rd) | 3389 | TCP | Piła corpNet | Dowolne | Zezwalaj |
| 301      | AllowPSRemoting | 5986| TCP | Usługi AzureActiveDirectoryDomainServices | Dowolne | Zezwalaj |
| 65000    | AllVnetInBound (AllVnetInBound) | Dowolne | Dowolne | VirtualNetwork | VirtualNetwork | Zezwalaj |
| 65001    | AllowAzureLoadBalancerInBound | Dowolne | Dowolne | AzureLoadBalancer | Dowolne | Zezwalaj |
| 65500    | DenyAllInBound | Dowolne | Dowolne | Dowolne | Dowolne | Zablokuj |

### <a name="outbound-security-rules"></a>Reguły zabezpieczeń dla ruchu wychodzącego

| Priorytet | Nazwa | Port | Protocol (Protokół) | Element źródłowy | Element docelowy | Akcja |
|----------|------|------|----------|--------|-------------|--------|
| 65000    | AllVnetOutBound (AllVnetOutBound) | Dowolne | Dowolne | VirtualNetwork | VirtualNetwork | Zezwalaj |
| 65001    | AllowAzureLoadBalancerOutBound | Dowolne | Dowolne |  Dowolne | Internet | Zezwalaj |
| 65500    | DenyAllOutBound | Dowolne | Dowolne | Dowolne | Dowolne | Zablokuj |

>[!NOTE]
> Usługi Azure AD DS wymaga nieograniczonego dostępu wychodzącego z sieci wirtualnej. Nie zaleca się tworzenia żadnych dodatkowych reguł, które ograniczają dostęp wychodzący dla sieci wirtualnej.

## <a name="verify-and-edit-existing-security-rules"></a>Weryfikowanie i edytowanie istniejących reguł zabezpieczeń

Aby zweryfikować istniejące reguły zabezpieczeń i upewnić się, że porty domyślne są otwarte, wykonaj następujące czynności:

1. W witrynie Azure portal wyszukaj i wybierz **pozycję Sieciowe grupy zabezpieczeń**.
1. Wybierz sieciową grupę zabezpieczeń skojarzoną z domeną zarządzaną, taką jak *AADDS-contoso.com-NSG*.
1. Na stronie **Przegląd** wyświetlane są istniejące reguły zabezpieczeń przychodzących i wychodzących.

    Przejrzyj reguły przychodzące i wychodzące i porównaj je z listą wymaganych reguł w poprzedniej sekcji. W razie potrzeby wybierz, a następnie usuń wszystkie reguły niestandardowe, które blokują wymagany ruch. Jeśli brakuje którakolwiek z wymaganych reguł, dodaj regułę w następnej sekcji.

    Po dodaniu lub usunięciu reguł, aby zezwolić na wymagany ruch, kondycja domeny zarządzanej usług Azure AD DS automatycznie aktualizuje się w ciągu dwóch godzin i usuwa alert.

### <a name="add-a-security-rule"></a>Dodawanie reguły zabezpieczeń

Aby dodać brakującą regułę zabezpieczeń, wykonaj następujące czynności:

1. W witrynie Azure portal wyszukaj i wybierz **pozycję Sieciowe grupy zabezpieczeń**.
1. Wybierz sieciową grupę zabezpieczeń skojarzoną z domeną zarządzaną, taką jak *AADDS-contoso.com-NSG*.
1. W obszarze **Ustawienia** w panelu po lewej stronie kliknij pozycję *Reguły zabezpieczeń ruchu przychodzącego* lub *Reguły zabezpieczeń ruchu wychodzącego,* w zależności od reguły, którą należy dodać.
1. Wybierz **dodaj**, a następnie utwórz wymaganą regułę na podstawie portu, protokołu, kierunku itp. Gdy będzie gotowy, wybierz przycisk **OK**.

Potrzeba kilku chwil, aby reguła zabezpieczeń została dodana i wyświetlona na liście.

## <a name="next-steps"></a>Następne kroki

Jeśli nadal występują problemy, [otwórz żądanie pomocy technicznej platformy Azure,][azure-support] aby uzyskać dodatkową pomoc w rozwiązywaniu problemów.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
