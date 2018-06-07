---
title: 'Azure Active Directory Domain Services: Rozwiązywania problemów z konfiguracją Secure LDAP | Dokumentacja firmy Microsoft'
description: Rozwiązywanie problemów z bezpiecznego protokołu LDAP dla usług domenowych Azure AD
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: ''
editor: ''
ms.assetid: 81208c0b-8d41-4f65-be15-42119b1b5957
ms.service: active-directory
ms.component: domains
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: ergreenl
ms.openlocfilehash: 29554230adc9d30f357c4e8d0082c0fe7d8d9035
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34588427"
---
# <a name="azure-ad-domain-services---troubleshooting-secure-ldap-configuration"></a>Usługi domenowe AD Azure - Konfiguracja Rozwiązywanie problemów z bezpiecznego protokołu LDAP

Ten artykuł zawiera rozwiązania dla typowe problemy podczas [Konfigurowanie bezpiecznego protokołu LDAP](active-directory-ds-admin-guide-configure-secure-ldap.md) dla usług domenowych Azure AD.

## <a name="aadds101-secure-ldap-network-security-group-configuration"></a>AADDS101: Konfiguracja bezpiecznego LDAP sieciowej grupy zabezpieczeń

**Komunikat alertu:**

*Bezpiecznego protokołu LDAP, za pośrednictwem Internetu jest włączona dla domeny zarządzanej. Dostęp do portu 636 nie jest zablokowany w dół za pomocą grupy zabezpieczeń sieci. Ta operacja może narazić kont użytkowników w domenie zarządzanej do ataków siłowych hasła.*

### <a name="secure-ldap-port"></a>Bezpieczne port LDAP

Po włączeniu bezpiecznego protokołu LDAP, zaleca się utworzenie dodatkowych reguł, aby dostęp do przychodzących LDAPS wyłącznie z określonych adresów IP. Te zasady chronią domenę przed atakami siłowymi, które mogą stanowić zagrożenie bezpieczeństwa. Port 636 zezwala na dostęp do domeny zarządzanej. Oto jak można zaktualizować Twojego NSG, aby umożliwić dostęp do bezpiecznego LDAP:

1. Przejdź do [karty sieciowe grupy zabezpieczeń](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) w portalu Azure
2. Wybierz grupy NSG skojarzony z domeną z tabeli.
3. Polecenie **reguły zabezpieczeń dla ruchu przychodzącego**
4. Tworzenie reguły portu 636
   1. Kliknij przycisk **Dodaj** na górnym pasku nawigacyjnym.
   2. Wybierz **adresów IP** źródła.
   3. Określ zakresy portów źródła dla tej reguły.
   4. Dane wejściowe "636" dla docelowego zakresy portów.
   5. Protokół jest **TCP**.
   6. Nadaj regule odpowiednią nazwę, opis i priorytet. Priorytet tej reguły powinien być większy niż priorytet reguły "Deny wszystkie", jeśli istnieje.
   7. Kliknij przycisk **OK**.
5. Sprawdź, czy reguła została utworzona.
6. Sprawdź kondycję Twojej domeny w dwóch godzin, aby upewnić się, że kroki zostały wykonane prawidłowo.

> [!TIP]
> Port 636 nie jest tylko reguły wymagane dla usług domenowych Azure AD bezproblemowe działanie. Aby dowiedzieć się więcej, odwiedź stronę [wytyczne sieci](active-directory-ds-networking.md) lub [NSG Rozwiązywanie problemów z konfiguracji](active-directory-ds-troubleshoot-nsg.md) artykułów.
>

## <a name="aadds502-secure-ldap-certificate-expiring"></a>AADDS502: Secure LDAP certyfikat wygasa

**Komunikat alertu:**

*Bezpiecznego certyfikatu LDAP do domeny zarządzanej wygaśnie XX.*

**Rozwiązanie:**

Utwórz nowy certyfikat LDAP bezpiecznego, wykonując czynności opisane w temacie [skonfigurować bezpiecznego protokołu LDAP](active-directory-ds-admin-guide-configure-secure-ldap.md) artykułu.

## <a name="contact-us"></a>Skontaktuj się z nami
Skontaktuj się z zespołem produktu usługi Azure Active Directory Domain Services, aby [udostępnić opinię lub pomocy technicznej](active-directory-ds-contact-us.md).
