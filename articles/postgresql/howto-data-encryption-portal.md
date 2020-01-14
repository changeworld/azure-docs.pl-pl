---
title: Szyfrowanie danych dla Azure Database for PostgreSQL pojedynczego serwera przy użyciu portalu
description: Dowiedz się, jak skonfigurować szyfrowanie danych dla Azure Database for PostgreSQL jednego serwera i zarządzać nimi za pomocą Azure Portal
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/10/2020
ms.openlocfilehash: e579866b1df6d32dc2b90a19ac001c381da625a6
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/13/2020
ms.locfileid: "75922799"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-using-portal"></a>Szyfrowanie danych dla Azure Database for PostgreSQL pojedynczego serwera przy użyciu portalu

W tym artykule dowiesz się, jak skonfigurować Azure Portal program i zarządzać nim, aby skonfigurować szyfrowanie danych dla Azure Database for PostgreSQL jednego serwera programu i zarządzania nim.

## <a name="prerequisites-for-cli"></a>Wymagania wstępne dotyczące interfejsu wiersza polecenia

* Musisz mieć subskrypcję platformy Azure i być administratorem tej subskrypcji.
* Utwórz Azure Key Vault i klucz do użycia dla klucza zarządzanego przez klienta.
* Magazyn kluczy musi mieć następującą właściwość, która ma być używana jako klucz zarządzany przez klienta
    * [Usuwanie nietrwałe](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)

        ```azurecli-interactive
        az resource update --id $(az keyvault show --name \ <key_valut_name> -test -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
        ```
    
    * [Przeczyść chronione](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection)

        ```azurecli-interactive
        az keyvault update --name <key_valut_name> --resource-group <resource_group_name>  --enable-purge-protection true
        ```

* Klucz musi mieć następujące atrybuty, aby można było używać klucza zarządzanego przez klienta.
    * Brak daty wygaśnięcia
    * Niewyłączone
    * Możliwość wykonania operacji get, zawijania klucza, odpakowania klucza

## <a name="setting-the-right-permissions-for-key-operations"></a>Ustawianie odpowiednich uprawnień dla operacji kluczowych

1. Na Azure Key Vault wybierz **zasady dostępu** i **Dodaj zasady dostępu** 

   ![Przegląd zasad dostępu](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. W obszarze **uprawnienia klucza** wybierz pozycję **Pobierz**, **Zawijaj**, **Odpakuj** i **podmiot zabezpieczeń**, która jest nazwą serwera PostgreSQL. Jeśli nie można znaleźć podmiotu zabezpieczeń serwera na liście istniejących podmiotów zabezpieczeń, należy zarejestrować go, próbując skonfigurować szyfrowanie danych po raz pierwszy, co zakończy się niepowodzeniem.  

   ![Przegląd zasad dostępu](media/concepts-data-access-and-security-data-encryption/access-policy-warp-unwrap.png)

3. **Zapisz** ustawienia.

## <a name="setting-data-encryption-for-azure-database-for-postgresql-single-server"></a>Ustawianie szyfrowania danych dla Azure Database for PostgreSQL pojedynczego serwera

1. Na **Azure Database for PostgreSQL**wybierz **szyfrowanie danych** , aby ustawić konfigurację klucza zarządzanego przez klienta.

   ![Ustawianie szyfrowania danych](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. Można wybrać **Key Vault** i parę **kluczy** lub przekazać **Identyfikator klucza**.

   ![Ustawienie Key Vault](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. **Zapisz** ustawienia.

4. Aby upewnić się, że wszystkie pliki (w tym **pliki tymczasowe**) są zaszyfrowane, **wymagane**jest **ponowne uruchomienie** serwera.

## <a name="restoring-or-creating-replica-of-the-server-which-has-data-encryption-enabled"></a>Przywracanie lub tworzenie repliki serwera z włączonym szyfrowaniem danych

Gdy Azure Database for PostgreSQL pojedynczym serwerze jest szyfrowany przy użyciu klucza zarządzanego przez klienta przechowywanego w Key Vault, nowo utworzona kopia serwera jest operacją lokalną lub z powodu operacji przywracania geograficznego lub repliki (lokalnej/obejmującej region). W przypadku zaszyfrowanego serwera PostgreSQL można wykonać poniższe kroki, aby utworzyć zaszyfrowany przywrócony serwer.

1. Na serwerze wybierz pozycję **Przegląd**, a następnie wybierz pozycję **Przywróć**.

   ![Inicjowanie — przywracanie](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   Lub dla serwera z włączoną replikacją, w polu Nagłówek **ustawień** wybierz pozycję **replikacja** .

   ![Inicjowanie repliki](media/concepts-data-access-and-security-data-encryption/postgresql-replica.png)

2. Po zakończeniu operacji przywracania tworzony nowy serwer jest szyfrowany danymi przy użyciu klucza serwera podstawowego. Jednak funkcje i opcje na serwerze są wyłączone, a serwer zostanie oznaczony jako **niedostępny** . Jest to Zapobieganie utracie danych od momentu, w którym nowy serwer nadal nie uzyskał uprawnień dostępu do Key Vault.

   ![Oznacz serwer jako niedostępny](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)


3. Aby naprawić stan niedostępności, należy ponownie sprawdzić poprawność klucza na przywróconym serwerze. Kliknij blok **szyfrowanie danych** , a następnie przycisk ponownie **Zweryfikuj klucz** .

   > [!NOTE]
   > Pierwsza próba ponownego zweryfikowania zakończy się niepowodzeniem, ponieważ nazwa główna usługi nowego serwera musi mieć dostęp do magazynu kluczy. Aby wygenerować jednostkę usługi, kliknij ponownie przycisk **Sprawdź poprawność klucza**, co spowoduje błąd, ale generuje jednostkę usługi. Następnie zapoznaj się z instrukcjami [w sekcji 2](https://docs.microsoft.com/azure/postgresql/howto-data-encryption-portal#setting-the-right-permissions-for-key-operations) powyżej.

   ![ponownie Zweryfikuj serwer](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   Trzeba będzie zapewnić dostęp do nowego serwera do Key Vault. 

4. Po zarejestrowaniu jednostki usługi należy ponownie sprawdzić poprawność klucza, a serwer wznawia jego normalne działanie.

   ![Przywrócono normalny serwer](media/concepts-data-access-and-security-data-encryption/restore-successful.png)

## <a name="next-steps"></a>Następne kroki

 Aby dowiedzieć się więcej na temat szyfrowania danych, zobacz [co to jest usługa Azure Data Encryption](concepts-data-encryption-postgresql.md).