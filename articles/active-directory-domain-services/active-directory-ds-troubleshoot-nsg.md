---
title: 'Azure Active Directory Domain Services: Rozwiązywanie problemów z sieciową grupą zabezpieczeń konfiguracji | Dokumentacja firmy Microsoft'
description: Rozwiązywanie problemów z konfiguracją sieciowej grupy zabezpieczeń dla usług domenowych Azure AD
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: ''
editor: ''
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2018
ms.author: ergreenl
ms.openlocfilehash: 503e52266c1c6be71e60a751c40ef0a54f0d9b12
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60416431"
---
# <a name="troubleshoot-invalid-networking-configuration-for-your-managed-domain"></a>Rozwiązywanie problemów z nieprawidłowej konfiguracji sieci dla domeny zarządzanej
Ten artykuł pomoże Ci rozwiązać błędy konfiguracji odnoszące się do sieci, których wynikiem następujący komunikat alertu:

## <a name="alert-aadds104-network-error"></a>AADDS104 alertu: Błąd sieci
**Komunikat alertu:** *Microsoft nie może nawiązać połączenia z kontrolerami domeny dla tej domeny zarządzanej. Może to nastąpić, jeśli sieciowa grupa zabezpieczeń (NSG) skonfigurowane w Twojej sieci wirtualnej blokuje dostęp do domeny zarządzanej. Inny możliwych przyczyn jest to, jeśli istnieje trasa zdefiniowana przez użytkownika, która blokuje ruch przychodzący z Internetu.*

Nieprawidłowe konfiguracje sieciowej grupy zabezpieczeń są najbardziej typowe przyczyny błędów sieci dla usługi Azure AD Domain Services. Grupy zabezpieczeń sieci (NSG) skonfigurowany na potrzeby sieci wirtualnej muszą zezwalać na dostęp do [określonych portów](active-directory-ds-networking.md#ports-required-for-azure-ad-domain-services). Jeśli te porty są zablokowane, firma Microsoft nie może monitorować lub zaktualizować Twojej domeny zarządzanej. Ponadto ma wpływ na synchronizacji katalogu usługi Azure AD Twojej domeny zarządzanej. Podczas tworzenia w sieciowej grupie zabezpieczeń, nie zamykaj tych portów w celu uniknięcia przerwy w działaniu usługi.

### <a name="checking-your-nsg-for-compliance"></a>Sprawdzanie sieciowa grupa zabezpieczeń pod kątem zgodności

1. Przejdź do [sieciowe grupy zabezpieczeń](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) strony w witrynie Azure portal
2. Z tabeli wybierz sieciową grupę zabezpieczeń skojarzoną z podsiecią, w którym Twoja domena zarządzana jest włączona.
3. W obszarze **ustawienia** w okienku po lewej stronie kliknij **reguły zabezpieczeń dla ruchu przychodzącego**
4. Zapoznaj się z zasadami w miejscu i zidentyfikować, które zasady blokują dostęp do [tych portów](active-directory-ds-networking.md#ports-required-for-azure-ad-domain-services)
5. Edytuj sieciowej grupy zabezpieczeń w celu zapewnienia zgodności przez usunięcie reguły, dodanie reguły albo całkowicie tworzenia nowej sieciowej grupy zabezpieczeń. Kroki umożliwiające [Dodaj regułę](#add-a-rule-to-a-network-security-group-using-the-azure-portal) lub Utwórz nową, zgodne sieciowej grupy zabezpieczeń znajdują się poniżej

## <a name="sample-nsg"></a>Sample NSG
Poniższa tabela przedstawia przykładowe sieciowej grupy zabezpieczeń, który będzie chronić Twojej domeny zarządzanej zezwalając firmy Microsoft, aby monitorować, zarządzania i aktualizowania informacji.

![sample NSG](./media/active-directory-domain-services-alerts/default-nsg.png)

>[!NOTE]
> Azure AD Domain Services wymaga nieograniczony dostęp ruchu wychodzącego z sieci wirtualnej. Nie zaleca się tworzenie wszelkie dodatkowe reguły sieciowej grupy zabezpieczeń, która ogranicza dostęp ruchu wychodzącego dla sieci wirtualnej.

## <a name="add-a-rule-to-a-network-security-group-using-the-azure-portal"></a>Dodawanie reguły do sieciowej grupy zabezpieczeń w witrynie Azure portal
Jeśli nie chcesz przy użyciu programu PowerShell, można ręcznie dodawać jednej reguły do sieciowych grup zabezpieczeń przy użyciu witryny Azure portal. Tworzenie reguł w sieciowej grupy zabezpieczeń, wykonaj następujące czynności:

1. Przejdź do [sieciowe grupy zabezpieczeń](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) strony w witrynie Azure portal.
2. Z tabeli wybierz sieciową grupę zabezpieczeń skojarzoną z podsiecią, w którym Twoja domena zarządzana jest włączona.
3. W obszarze **ustawienia** w panelu po lewej stronie kliknij opcję **reguły zabezpieczeń dla ruchu przychodzącego** lub **reguły zabezpieczeń dla ruchu wychodzącego**.
4. Utworzyć regułę, klikając **Dodaj** i wypełniając informacje. Kliknij przycisk **OK**.
5. Sprawdź, czy reguła została utworzona, umieszczając go w tabeli reguł.


## <a name="need-help"></a>Potrzebujesz pomocy?
Skontaktuj się z zespołem produktu usługi Azure Active Directory Domain Services, aby [Podziel się opinią lub pomocy technicznej](active-directory-ds-contact-us.md).
