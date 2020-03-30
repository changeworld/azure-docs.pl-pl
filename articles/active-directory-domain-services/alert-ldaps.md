---
title: Rozwiązywanie bezpiecznych alertów LDAP w usługach domenowych usługi Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się, jak rozwiązywać typowe alerty i rozwiązywać je za pomocą bezpiecznego protokołu LDAP dla usług domenowych Active Directory platformy Azure.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 81208c0b-8d41-4f65-be15-42119b1b5957
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/18/2019
ms.author: iainfou
ms.openlocfilehash: 06b0fa1979f18981ec5cf78dc9a9dbad8b196394
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71258049"
---
# <a name="known-issues-secure-ldap-alerts-in-azure-active-directory-domain-services"></a>Znane problemy: bezpieczne alerty LDAP w usługach domenowych Usługi domenowe Active Directory platformy Azure

Aplikacje i usługi korzystające z protokołu LDAP (Lightweight Directory Access Protocol) do komunikowania się z usługami domenowymi Usługi Azure Active Directory (Azure AD DS) można [skonfigurować do używania bezpiecznego protokołu LDAP](tutorial-configure-ldaps.md). Aby bezpieczny LDAP działał poprawnie, musi być otwarty odpowiedni certyfikat i wymagane porty sieciowe.

Ten artykuł ułatwia zrozumienie i rozwiązywanie typowych alertów za pomocą bezpiecznego dostępu LDAP w usługach Azure AD DS.

## <a name="aadds101-secure-ldap-network-configuration"></a>AADDS101: Bezpieczna konfiguracja sieci LDAP

### <a name="alert-message"></a>Komunikat ostrzegawczy

*Bezpieczna usługa LDAP przez Internet jest włączona dla domeny zarządzanej. Jednak dostęp do portu 636 nie jest zablokowany przy użyciu sieciowej grupy zabezpieczeń. Może to narazić konta użytkowników w domenie zarządzanej na ataki typu password brute-force.*

### <a name="resolution"></a>Rozwiązanie

Po włączeniu bezpiecznego protokołu LDAP zaleca się utworzenie dodatkowych reguł ograniczających przychodzący dostęp LDAPS do określonych adresów IP. Te reguły chronią domenę zarządzana usługą Azure AD DS przed atakami siłowymi. Aby zaktualizować sieciową grupę zabezpieczeń w celu ograniczenia dostępu do portu TCP 636 dla bezpiecznego protokołu LDAP, wykonaj następujące czynności:

1. W witrynie Azure portal wyszukaj i wybierz **pozycję Sieciowe grupy zabezpieczeń**.
1. Wybierz sieciową grupę zabezpieczeń skojarzoną z domeną zarządzaną, taką jak *AADDS-contoso.com-NSG*, a następnie wybierz pozycję **Przychodzące reguły zabezpieczeń**
1. **+ Dodaj** regułę dla portu TCP 636. W razie potrzeby wybierz pozycję **Zaawansowane** w oknie, aby utworzyć regułę.
1. W menu **rozwijanym źródło**wybierz *polecenie Adresy IP.* Wprowadź źródłowe adresy IP, do których chcesz udzielić dostępu dla bezpiecznego ruchu LDAP.
1. Wybierz *opcję Dowolna* jako **miejsce docelowe**, a następnie wprowadź *636* dla **zakresów portów docelowych**.
1. Ustaw **protokół** jako *TCP* i **akcję,** aby *zezwolić*.
1. Określ priorytet reguły, a następnie wprowadź nazwę, taką jak *RestrictLDAPS*.
1. Gdy będzie gotowy, wybierz pozycję **Dodaj,** aby utworzyć regułę.

Kondycja domeny zarządzanej usług Azure AD DS automatycznie aktualizuje się w ciągu dwóch godzin i usuwa alert.

> [!TIP]
> Port TCP 636 nie jest jedyną regułą wymaganą do płynnego działania usług Azure AD DS. Aby dowiedzieć się więcej, zobacz [grupy zabezpieczeń sieci usług Azure AD DS i wymagane porty](network-considerations.md#network-security-groups-and-required-ports).

## <a name="aadds502-secure-ldap-certificate-expiring"></a>AADDS502: Wygasanie bezpiecznego certyfikatu LDAP

### <a name="alert-message"></a>Komunikat ostrzegawczy

*Bezpieczny certyfikat LDAP dla domeny zarządzanej wygaśnie w dniu [data]].*

### <a name="resolution"></a>Rozwiązanie

Utwórz zastępczy bezpieczny certyfikat LDAP, wykonując kroki, aby [utworzyć certyfikat dla bezpiecznego LDAP](tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap). Zastosuj certyfikat zastępczy do usług Azure AD DS i rozpowszechniaj certyfikat do wszystkich klientów, którzy łączą się przy użyciu bezpiecznego protokołu LDAP.

## <a name="next-steps"></a>Następne kroki

Jeśli nadal występują problemy, [otwórz żądanie pomocy technicznej platformy Azure,][azure-support] aby uzyskać dodatkową pomoc w rozwiązywaniu problemów.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
