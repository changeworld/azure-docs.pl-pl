---
title: Szyfrowanie danych dla Azure Database for MySQL przy użyciu portalu
description: Dowiedz się, jak skonfigurować szyfrowanie danych i zarządzać nimi Azure Database for MySQL przy użyciu Azure Portal.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 5bd100a5858660d80f3704844ed974f2bc9257b4
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028939"
---
# <a name="data-encryption-for-azure-database-for-mysql-server-using-azure-portal"></a>Szyfrowanie danych dla serwera Azure Database for MySQL przy użyciu Azure Portal

W tym artykule dowiesz się, jak skonfigurować program i zarządzać nim, aby skonfigurować szyfrowanie danych dla Azure Database for MySQL za pomocą Azure Portal.

## <a name="prerequisites-for-cli"></a>Wymagania wstępne dotyczące interfejsu wiersza polecenia

* Musisz mieć subskrypcję platformy Azure i być administratorem tej subskrypcji.
* Utwórz Azure Key Vault i klucz do użycia dla klucza zarządzanego przez klienta.
* Key Vault musi mieć następującą właściwość, która będzie używana jako klucz zarządzany przez klienta:
  * [Usuwanie nietrwałe](../key-vault/key-vault-ovw-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -test -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Przeczyść chronione](../key-vault/key-vault-ovw-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* Klucz musi mieć następujące atrybuty, aby można było używać klucza zarządzanego przez klienta.
  * Brak daty wygaśnięcia
  * Niewyłączone
  * Możliwość wykonania operacji _Get_, _zawijania klucza_, _odpakowania klucza_

## <a name="setting-the-right-permissions-for-key-operations"></a>Ustawianie odpowiednich uprawnień dla operacji kluczowych

1. Na Azure Key Vault wybierz **zasady dostępu**, a następnie **Dodaj zasady dostępu**:

   ![Przegląd zasad dostępu](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. Wybierz **uprawnienia klucza**i wybierz pozycję **Pobierz**, **Zawijaj**, **Odpakuj** i **podmiot zabezpieczeń**, który jest nazwą serwera MySQL. Jeśli nie można znaleźć podmiotu zabezpieczeń serwera na liście istniejących podmiotów zabezpieczeń, należy zarejestrować go, próbując skonfigurować szyfrowanie danych po raz pierwszy, co zakończy się niepowodzeniem.

   ![Przegląd zasad dostępu](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. **Zapisz** ustawienia.

## <a name="setting-data-encryption-for-azure-database-for-mysql"></a>Ustawianie szyfrowania danych dla Azure Database for MySQL

1. Na **Azure Database for MySQL**wybierz **szyfrowanie danych** , aby ustawić konfigurację klucza zarządzanego przez klienta.

   ![Ustawianie szyfrowania danych](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. Można wybrać **Key Vault** i parę **kluczy** lub przekazać **Identyfikator klucza**.

   ![Ustawienie Key Vault](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. **Zapisz** ustawienia.

4. Aby upewnić się, że wszystkie pliki (w tym **pliki tymczasowe**) są zaszyfrowane, **wymagane**jest **ponowne uruchomienie** serwera.

## <a name="restoring-or-creating-replica-of-the-server-which-has-data-encryption-enabled"></a>Przywracanie lub tworzenie repliki serwera z włączonym szyfrowaniem danych

Gdy Azure Database for MySQL jest szyfrowany przy użyciu klucza zarządzanego przez klienta przechowywanego w Key Vault, nowo utworzona kopia serwera jest operacją operacji przywracania lokalnego lub w trybie geograficznym lub repliką (lokalna/międzyregionową). W przypadku szyfrowanego serwera MySQL można wykonać poniższe kroki, aby utworzyć zaszyfrowany przywrócony serwer.

1. Na serwerze wybierz pozycję **Przegląd**, a następnie wybierz pozycję **Przywróć**.

   ![Inicjowanie — przywracanie](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   Lub dla serwera z włączoną replikacją w obszarze **Ustawienia** wybierz pozycję **replikacja**, jak pokazano poniżej:

   ![Inicjowanie repliki](media/concepts-data-access-and-security-data-encryption/mysql-replica.png)

2. Po zakończeniu operacji przywracania tworzony nowy serwer jest szyfrowany przy użyciu klucza używanego do szyfrowania serwera podstawowego. Jednak funkcje i opcje na serwerze są wyłączone, a serwer zostanie oznaczony jako **niedostępny** . Takie zachowanie zostało zaprojektowane, aby zapobiec manipulowaniu danymi, ponieważ nie ma jeszcze uprawnień do uzyskiwania dostępu do Key Vault przy użyciu tożsamości nowego serwera.

   ![Oznacz serwer jako niedostępny](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. Aby naprawić stan niedostępności, należy ponownie sprawdzić poprawność klucza na przywróconym serwerze. Wybierz okienko **szyfrowanie danych** , a następnie przycisk ponownie **Zweryfikuj klucz** .

   > [!NOTE]
   > Pierwsza próba ponownego zweryfikowania zakończy się niepowodzeniem, ponieważ nazwa główna usługi nowego serwera musi mieć dostęp do magazynu kluczy. Aby wygenerować jednostkę usługi, wybierz pozycję **Zweryfikuj ponownie klucz**, co spowoduje wystąpienie błędu, ale wygeneruje nazwę główną usługi. Następnie zapoznaj się z instrukcjami [w sekcji 2](#setting-the-right-permissions-for-key-operations) powyżej.

   ![ponownie Zweryfikuj serwer](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   Trzeba będzie zapewnić dostęp do nowego serwera do Key Vault.

4. Po zarejestrowaniu jednostki usługi należy ponownie sprawdzić poprawność klucza, a serwer wznawia jego normalne działanie.

   ![Przywrócono normalny serwer](media/concepts-data-access-and-security-data-encryption/restore-successful.png)

## <a name="next-steps"></a>Następne kroki

 Aby dowiedzieć się więcej na temat szyfrowania danych, zobacz [co to jest usługa Azure Data Encryption](concepts-data-encryption-mysql.md).
