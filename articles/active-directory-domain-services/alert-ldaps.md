---
title: 'Azure Active Directory Domain Services: Rozwiązywanie problemów z bezpiecznym protokołem LDAP | Microsoft Docs'
description: Secure LDAP rozwiązywania problemów Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: ''
editor: ''
ms.assetid: 81208c0b-8d41-4f65-be15-42119b1b5957
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: 8f9f4a8b52548dad011f5e825fa42c50da970ea7
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69613155"
---
# <a name="azure-ad-domain-services---troubleshooting-secure-ldap-configuration"></a>Azure AD Domain Services — Rozwiązywanie problemów z konfiguracją Secure LDAP

Ten artykuł zawiera rozwiązania typowych problemów podczas [konfigurowania bezpiecznego protokołu LDAP](tutorial-configure-ldaps.md) dla Azure AD Domain Services.

## <a name="aadds101-secure-ldap-network-security-group-configuration"></a>AADDS101: Konfiguracja sieciowej grupy zabezpieczeń Secure LDAP

**Komunikat alertu:**

*Secure LDAP przez Internet jest włączony dla domeny zarządzanej. Jednak dostęp do portu 636 nie jest zablokowany przy użyciu sieciowej grupy zabezpieczeń. Może to spowodować ujawnienie kont użytkowników w domenie zarządzanej w celu wymuszenia hasła.*

### <a name="secure-ldap-port"></a>Port Secure LDAP

Po włączeniu bezpiecznego protokołu LDAP zalecamy utworzenie dodatkowych reguł zezwalających na dostęp do ruchu przychodzącego LDAP tylko z określonych adresów IP. Te reguły chronią domenę przed atakami polegającymi na wymuszeniu, które mogą stanowić zagrożenie dla bezpieczeństwa. Port 636 zezwala na dostęp do domeny zarządzanej. Oto jak zaktualizować sieciowej grupy zabezpieczeń, aby zezwolić na dostęp do Secure LDAP:

1. Przejdź do [karty sieciowe grupy zabezpieczeń](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) w Azure Portal
2. Wybierz sieciowej grupy ZABEZPIECZEŃą skojarzoną z domeną z tabeli.
3. Kliknij pozycję **reguły zabezpieczeń dla ruchu przychodzącego**
4. Tworzenie reguły portu 636
   1. Kliknij przycisk **Dodaj** na górnym pasku nawigacyjnym.
   2. Wybierz **adresy IP** dla źródła.
   3. Określ zakresy portów źródłowych dla tej reguły.
   4. Wprowadź "636" dla docelowych zakresów portów.
   5. Protokół to **TCP**.
   6. Nadaj regule odpowiednią nazwę, opis i priorytet. Priorytet tej reguły powinien być wyższy niż priorytet reguły "Odmów wszystkiego", jeśli istnieje.
   7. Kliknij przycisk **OK**.
5. Sprawdź, czy reguła została utworzona.
6. Sprawdź kondycję domeny w ciągu dwóch godzin, aby upewnić się, że kroki zostały wykonane prawidłowo.

> [!TIP]
> Port 636 nie jest jedyną regułą wymaganą do bezproblemowego uruchamiania Azure AD Domain Services. Aby dowiedzieć się więcej, zobacz [wytyczne dotyczące sieci](network-considerations.md) lub [Rozwiązywanie problemów z artykułami konfiguracji sieciowej grupy zabezpieczeń](alert-nsg.md) .
>

## <a name="aadds502-secure-ldap-certificate-expiring"></a>AADDS502: Secure LDAP wygaśnięcia certyfikatu

**Komunikat alertu:**

*Certyfikat bezpiecznego protokołu LDAP dla domeny zarządzanej wygaśnie w dniu [Date]].*

**Tłumaczenia**

Utwórz nowy certyfikat bezpiecznego protokołu LDAP, wykonując czynności opisane w artykule [Konfigurowanie bezpiecznego protokołu LDAP](tutorial-configure-ldaps.md) .

## <a name="contact-us"></a>Skontaktuj się z nami
Skontaktuj się z zespołem pomocy technicznej Azure Active Directory Domain Services, aby [udostępnić opinię lub pomoc techniczną](contact-us.md).
