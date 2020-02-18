---
title: Szyfrowanie danych dla Azure Database for PostgreSQL — Rozwiązywanie problemów z pojedynczym serwerem
description: Dowiedz się, jak rozwiązywać problemy z szyfrowaniem danych dla Azure Database for PostgreSQL-pojedynczego serwera
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 6d2e027c47bef3186f95c2183b316b5c15511070
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2020
ms.locfileid: "77371503"
---
# <a name="troubleshooting-data-encryption-with-customer-managed-keys-in-azure-database-for-postgresql---single-server"></a>Rozwiązywanie problemów z szyfrowaniem danych za pomocą kluczy zarządzanych przez klienta w Azure Database for PostgreSQL-pojedynczym serwerze
W tym artykule opisano sposób identyfikowania i rozwiązywania typowych problemów/błędów występujących na serwerze Azure Database for PostgreSQL-pojedynczym skonfigurowanym z szyfrowaniem danych przy użyciu klucza zarządzanego przez klienta.

## <a name="introduction"></a>Wprowadzenie
Gdy szyfrowanie danych jest skonfigurowane do korzystania z klucza zarządzanego przez klienta w Azure Key Vault, dostęp do tego klucza jest wymagany do zapewnienia dostępności serwera. Jeśli serwer utraci dostęp do klucza zarządzanego przez klienta w Azure Key Vault, serwer zacznie odrzucać wszystkie połączenia z odpowiednim komunikatem o błędzie i zmienić jego stan na ***niedostępny*** w Azure Portal.

Jeśli niedostępny Azure Database for PostgreSQL-pojedynczy serwer nie jest już wymagany, można go natychmiast usunąć, aby zatrzymać ponoszenia kosztów. Wszystkie inne akcje na serwerze nie są dozwolone, dopóki nie zostanie przywrócony dostęp do magazynu kluczy platformy Azure, a serwer będzie ponownie dostępny. Zmiana opcji szyfrowania danych z "tak" (zarządzanej przez klienta) na "No" (zarządzanej przez usługę) na serwerze niedostępnym jest również niemożliwa, gdy serwer jest szyfrowany przy użyciu klienta. Należy ponownie zweryfikować klucz ręcznie, aby serwer był dostępny. Jest to konieczne, aby chronić dane przed nieautoryzowanym dostępem, podczas gdy uprawnienia do klucza zarządzanego przez klienta zostały odwołane.

## <a name="common-errors-causing-server-to-become-inaccessible"></a>Typowe błędy powodujące, że serwer stał się niedostępny

Większość problemów występujących podczas korzystania z szyfrowania danych za pomocą Azure Key Vault jest spowodowana przez jedną z następujących błędnych konfiguracji —

Magazyn kluczy jest niedostępny lub nie istnieje

* Magazyn kluczy został przypadkowo usunięty.
* Sporadyczny błąd sieci powoduje, że magazyn kluczy jest niedostępny.

Brak uprawnień dostępu do magazynu kluczy lub klucz nie istnieje

* Klucz został przypadkowo usunięty, wyłączony lub wygasł.
* Tożsamość zarządzana Azure Database for PostgreSQL-pojedynczego wystąpienia serwera została przypadkowo usunięta.
* Uprawnienia przyznane Azure Database for PostgreSQL zarządzanej tożsamości dla kluczy nie są wystarczające (nie obejmują funkcji GET, zawijania i rozwinięcia).
* Uprawnienia do Azure Database for PostgreSQL tożsamości zarządzanej przez wystąpienie pojedynczego serwera zostały cofnięte lub usunięte.

## <a name="identify-and-resolve-common-errors"></a>Identyfikowanie i rozwiązywanie typowych błędów
### <a name="errors-on-the-key-vault"></a>Błędy w magazynie kluczy

#### <a name="disabled-key-vault"></a>Wyłączony Magazyn kluczy
* AzureKeyVaultKeyDisabledMessage
* **Wyjaśnienie** : nie można ukończyć operacji na serwerze, ponieważ klucz Azure Key Vault jest wyłączony.

#### <a name="missing-key-vault-permissions"></a>Brak uprawnień magazynu kluczy
* AzureKeyVaultMissingPermissionsMessage
* Na serwerze nie ma wymaganych uprawnień pobieranie, zawijanie i depakowanie do uprawnień Azure Key Vault. Udziel każdemu brakującemu uprawnienia do nazwy głównej usługi o IDENTYFIKATORze.

### <a name="mitigation"></a>Środki zaradcze
* Upewnij się, że klucz zarządzany przez klienta jest obecny w Key Vault:
* Zidentyfikuj Magazyn kluczy, a następnie przejdź do magazynu kluczy w Azure Portal.
* Upewnij się, że klucz identyfikowany przez identyfikator URI klucza jest obecny.


## <a name="next-steps"></a>Następne kroki
[Skonfiguruj szyfrowanie danych za pomocą klucza zarządzanego przez klienta dla usługi Azure Database for PostgreSQL przy użyciu Azure Portal](howto-data-encryption-portal.md).