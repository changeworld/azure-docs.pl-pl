---
title: Skonfiguruj klucze zarządzane przez klienta do szyfrowania za pomocą usługi Azure Storage z Azure Portal
description: Dowiedz się, jak używać Azure Portal do konfigurowania kluczy zarządzanych przez klienta do szyfrowania usługi Azure Storage. Klucze zarządzane przez klienta umożliwiają tworzenie, obracanie, wyłączanie i odwoływanie kontroli dostępu.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/16/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 892cbe66222626a6847ad7a5b6c990d23991c182
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002270"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-the-azure-portal"></a>Skonfiguruj klucze zarządzane przez klienta do szyfrowania za pomocą usługi Azure Storage z Azure Portal

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

W tym artykule pokazano, jak skonfigurować magazyn kluczy z kluczami zarządzanymi przez klienta przy użyciu [Azure Portal](https://portal.azure.com/). Aby dowiedzieć się, jak utworzyć magazyn kluczy przy użyciu Azure Portal, [zobacz Szybki Start: Ustaw i Pobierz klucz tajny z Azure Key Vault przy użyciu](../../key-vault/quick-create-portal.md)Azure Portal. 


> [!IMPORTANT]
> W przypadku korzystania z kluczy zarządzanych przez klienta z szyfrowaniem usługi Azure Storage wymagane jest skonfigurowanie dwóch wymaganych właściwości magazynu kluczy, **usuwania nietrwałego** i **nieprzeczyszczania**. Te właściwości są domyślnie włączone podczas tworzenia nowego magazynu kluczy w Azure Portal. Jeśli jednak musisz włączyć te właściwości w istniejącym magazynie kluczy, musisz użyć programu PowerShell lub interfejsu wiersza polecenia platformy Azure.
> Obsługiwane są tylko klucze RSA i rozmiar klucza 2048.

## <a name="enable-customer-managed-keys"></a>Włącz klucze zarządzane przez klienta

Aby włączyć klucze zarządzane przez klienta w Azure Portal, wykonaj następujące kroki:

1. Przejdź do swojego konta magazynu.
1. W bloku **Ustawienia** dla konta magazynu kliknij pozycję **szyfrowanie**. Wybierz opcję **Użyj własnego klucza** , jak pokazano na poniższej ilustracji.

    ![Zrzut ekranu portalu przedstawiający opcję szyfrowania](./media/storage-encryption-keys-portal/ssecmk1.png)

## <a name="specify-a-key"></a>Określ klucz

Po włączeniu kluczy zarządzanych przez klienta będziesz mieć możliwość określenia klucza do skojarzenia z kontem magazynu.

### <a name="specify-a-key-as-a-uri"></a>Określ klucz jako identyfikator URI

Aby określić klucz jako identyfikator URI, wykonaj następujące kroki:

1. Aby zlokalizować identyfikator URI klucza w Azure Portal, przejdź do magazynu kluczy, a następnie wybierz ustawienie **klucze** . Wybierz odpowiedni klucz, a następnie kliknij klucz, aby wyświetlić jego ustawienia. Skopiuj wartość pola **Identyfikator klucza** , która zapewnia identyfikator URI.

    ![Zrzut ekranu przedstawiający identyfikator URI klucza magazynu kluczy](media/storage-encryption-keys-portal/key-uri-portal.png)

1. W ustawieniach **szyfrowania** dla konta magazynu wybierz opcję **Wprowadź identyfikator URI klucza** .
1. W polu **Identyfikator URI klucza** Określ identyfikator URI.

   ![Zrzut ekranu przedstawiający sposób wprowadzania identyfikatora URI klucza](./media/storage-encryption-keys-portal/ssecmk2.png)

### <a name="specify-a-key-from-a-key-vault"></a>Określ klucz z magazynu kluczy

Aby określić klucz z magazynu kluczy, najpierw upewnij się, że masz Magazyn kluczy zawierający klucz. Aby określić klucz z magazynu kluczy, wykonaj następujące kroki:

1. Wybierz opcję **Wybierz z Key Vault** .
2. Wybierz magazyn kluczy zawierający klucz, którego chcesz użyć.
3. Wybierz klucz z magazynu kluczy.

   ![Zrzut ekranu przedstawiający opcję klucz zarządzany przez klienta](./media/storage-encryption-keys-portal/ssecmk3.png)

## <a name="update-the-key-version"></a>Zaktualizuj wersję klucza

Podczas tworzenia nowej wersji klucza należy zaktualizować konto magazynu, aby używało nowej wersji. Wykonaj następujące kroki:

1. Przejdź do konta magazynu i Wyświetl ustawienia **szyfrowania** .
1. Określ identyfikator URI dla nowej wersji klucza. Alternatywnie można wybrać Magazyn kluczy i ponownie klucz, aby zaktualizować wersję.

## <a name="next-steps"></a>Następne kroki

- [Szyfrowanie usługi Azure Storage dla danych magazynowanych](storage-service-encryption.md)
- [Co to jest Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
