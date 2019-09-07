---
title: 'Azure Active Directory Domain Services: Rozwiązywanie problemów z sieciowymi grupami zabezpieczeń | Microsoft Docs'
description: Rozwiązywanie problemów z konfiguracją sieciowej grupy zabezpieczeń dla Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: ''
editor: ''
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: 450ee5635b378ed7c4d4e4bedc1c4245f6b52d70
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "70743422"
---
# <a name="troubleshoot-invalid-networking-configuration-for-your-managed-domain"></a>Rozwiązywanie problemów z nieprawidłową konfiguracją sieci dla domeny zarządzanej
Ten artykuł pomaga rozwiązywać problemy i rozwiązywać związane z nimi błędy konfiguracji, które powodują następujący komunikat o alercie:

## <a name="alert-aadds104-network-error"></a>AADDS104 alertu: Błąd sieci
**Komunikat alertu:** *Firma Microsoft nie może nawiązać połączenia z kontrolerami domeny dla tej domeny zarządzanej. Może się tak zdarzyć, jeśli sieciowa Grupa zabezpieczeń (sieciowej grupy zabezpieczeń) skonfigurowana w sieci wirtualnej blokuje dostęp do domeny zarządzanej. Kolejną możliwą przyczyną jest to, że istnieje trasa zdefiniowana przez użytkownika, która blokuje ruch przychodzący z Internetu.*

Nieprawidłowe konfiguracje sieciowej grupy zabezpieczeń są Najczęstszymi przyczynami błędów sieci dla Azure AD Domain Services. Grupa zabezpieczeń sieci (sieciowej grupy zabezpieczeń) skonfigurowana dla sieci wirtualnej musi zezwalać na dostęp do [określonych portów](network-considerations.md#network-security-groups-and-required-ports). Jeśli te porty są zablokowane, firma Microsoft nie może monitorować ani aktualizować domeny zarządzanej. Ponadto ma to wpływ na synchronizację między katalogiem usługi Azure AD i domeną zarządzaną. Podczas tworzenia sieciowej grupy zabezpieczeń należy pozostawić te porty otwarte, aby uniknąć przerw w działaniu usługi.

### <a name="checking-your-nsg-for-compliance"></a>Sprawdzanie zgodności sieciowej grupy zabezpieczeń

1. Przejdź do strony [grupy zabezpieczeń sieci](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) w Azure Portal
2. Z tabeli wybierz sieciowej grupy zabezpieczeń skojarzoną z podsiecią, w której włączono domenę zarządzaną.
3. W obszarze **Ustawienia** w panelu po lewej stronie kliknij pozycję **reguły zabezpieczeń dla ruchu przychodzącego**
4. Przejrzyj reguły na miejscu i ustal, które reguły blokują dostęp do [tych portów](network-considerations.md#network-security-groups-and-required-ports)
5. Edytuj sieciowej grupy zabezpieczeń, aby zapewnić zgodność przez usunięcie reguły, dodanie reguły lub utworzenie nowej sieciowej grupy zabezpieczeń w całości. Kroki umożliwiające [dodanie reguły](#add-a-rule-to-a-network-security-group-using-the-azure-portal) lub utworzenie nowej, zgodnej sieciowej grupy zabezpieczeń są poniżej

## <a name="sample-nsg"></a>Przykład sieciowej grupy zabezpieczeń
W poniższej tabeli przedstawiono przykładową sieciowej grupy zabezpieczeń, która spowodowałaby bezpieczną ochronę domeny zarządzanej, a jednocześnie umożliwia firmie Microsoft monitorowanie i aktualizowanie informacji oraz zarządzanie nimi.

![przykład sieciowej grupy zabezpieczeń](./media/active-directory-domain-services-alerts/default-nsg.png)

>[!NOTE]
> Azure AD Domain Services wymaga nieograniczonego dostępu wychodzącego z sieci wirtualnej. Nie zaleca się tworzenia żadnych dodatkowych reguł sieciowej grupy zabezpieczeń, które ograniczają dostęp wychodzący do sieci wirtualnej.

## <a name="add-a-rule-to-a-network-security-group-using-the-azure-portal"></a>Dodawanie reguły do sieciowej grupy zabezpieczeń przy użyciu Azure Portal
Jeśli nie chcesz używać programu PowerShell, możesz ręcznie dodać pojedyncze reguły do sieciowych grup zabezpieczeń przy użyciu Azure Portal. Aby utworzyć reguły w sieciowej grupie zabezpieczeń, wykonaj następujące czynności:

1. Przejdź do strony [sieciowe grupy zabezpieczeń](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) w Azure Portal.
2. Z tabeli wybierz sieciowej grupy zabezpieczeń skojarzoną z podsiecią, w której włączono domenę zarządzaną.
3. W obszarze **Ustawienia** w panelu po lewej stronie kliknij pozycję **reguły zabezpieczeń dla ruchu przychodzącego** lub **reguły zabezpieczeń dla ruchu wychodzącego**.
4. Utwórz regułę, klikając przycisk **Dodaj** i wypełniając informacje. Kliknij przycisk **OK**.
5. Sprawdź, czy reguła została utworzona, umieszczając ją w tabeli reguł.


## <a name="need-help"></a>Potrzebujesz pomocy?
Skontaktuj się z zespołem pomocy technicznej Azure Active Directory Domain Services, aby [udostępnić opinię lub pomoc techniczną](contact-us.md).
