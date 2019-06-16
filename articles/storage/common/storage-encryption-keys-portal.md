---
title: Konfigurowanie kluczy zarządzanych przez klienta do szyfrowania usługi Azure Storage w witrynie Azure portal
description: Dowiedz się, jak skonfigurować kluczy zarządzanych przez klienta do szyfrowania usługi Azure Storage za pomocą witryny Azure portal. Kluczy zarządzanych przez klienta umożliwiają tworzenie, obracanie, wyłącz i odwołać kontroli dostępu.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/16/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: baabc5a8e1d063cb51a3edea3a7218591e85aa1a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65154156"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-the-azure-portal"></a>Konfigurowanie kluczy zarządzanych przez klienta do szyfrowania usługi Azure Storage w witrynie Azure portal

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

W tym artykule pokazano, jak skonfigurować magazyn kluczy przy użyciu kluczy zarządzanych przez klienta za pomocą [witryny Azure portal](https://portal.azure.com/). Aby dowiedzieć się, jak utworzyć magazyn kluczy przy użyciu witryny Azure portal, zobacz [Szybki Start: Ustawianie i pobieranie wpisu tajnego z usługi Azure Key Vault przy użyciu witryny Azure portal](../../key-vault/quick-create-portal.md). 


> [!IMPORTANT]
> Przy użyciu szyfrowania usługi Azure Storage za pomocą kluczy zarządzanych przez klienta wymaga usługi key vault dwóch wymaganych właściwości skonfigurowane, **usuwanie nietrwałe** i **przeczyszczanie**. Właściwości te są domyślnie włączone, po utworzeniu nowego magazynu kluczy w witrynie Azure portal. Jednak jeśli potrzebujesz włączyć te właściwości istniejącego magazynu kluczy, należy użyć programu PowerShell lub wiersza polecenia platformy Azure.

## <a name="enable-customer-managed-keys"></a>Włącz kluczy zarządzanych przez klienta

Aby włączyć kluczy zarządzanych przez klienta w witrynie Azure portal, wykonaj następujące kroki:

1. Przejdź do swojego konta magazynu.
1. Na **ustawienia** bloku konto magazynu, kliknij przycisk **szyfrowania**. Wybierz **użycie swojego własnego klucza** opcji, jak pokazano na poniższej ilustracji.

    ![Opcja szyfrowania przedstawiający zrzut ekranu portalu](./media/storage-encryption-keys-portal/ssecmk1.png)

## <a name="specify-a-key"></a>Określ klucz

Po włączeniu kluczy zarządzanych przez klienta, będziesz mieć możliwość określenia klucza do skojarzenia z kontem magazynu.

### <a name="specify-a-key-as-a-uri"></a>Określ klucz jako identyfikator URI

Aby określić klucz jako identyfikator URI, wykonaj następujące kroki:

1. Aby zlokalizować identyfikator URI klucza w witrynie Azure portal, przejdź do magazynu kluczy i wybierz **klucze** ustawienie. Wybierz żądany klucz, a następnie klawisz, aby wyświetlić jej ustawienia. Skopiuj wartość **identyfikatora klucza** pola, który zawiera identyfikator URI.

    ![Identyfikator URI klucza zrzut ekranu przedstawiający usługi key vault](media/storage-encryption-keys-portal/key-uri-portal.png)

1. W **szyfrowania** ustawienia konta magazynu wybierz **klawisza Enter URI** opcji.
1. W **identyfikator URI klucza** pola, określ identyfikator URI.

   ![Zrzut ekranu przedstawiający sposób wprowadź identyfikator URI klucza](./media/storage-encryption-keys-portal/ssecmk2.png)

### <a name="specify-a-key-from-a-key-vault"></a>Określenie klucza z magazynu kluczy

Aby określić klucza z magazynu kluczy, należy najpierw upewnić się, że masz magazynu kluczy, który zawiera klucz. Aby określić klucza z magazynu kluczy, wykonaj następujące kroki:

1. Wybierz **wybierz z magazynu Key Vault** opcji.
2. Wybierz magazyn kluczy zawierającego klucz, którego chcesz użyć.
3. Wybierz klawisz z magazynu kluczy.

   ![Zrzut ekranu przedstawiający opcję klucza zarządzanego przez klienta](./media/storage-encryption-keys-portal/ssecmk3.png)

## <a name="update-the-key-version"></a>Aktualizacja wersji klucza

Kiedy tworzysz nową wersję klucza, należy zaktualizować konta magazynu do nowej wersji. Wykonaj następujące kroki:

1. Przejdź do swojego konta magazynu i wyświetlić **szyfrowania** ustawienia.
1. Określ identyfikator URI dla nowej wersji klucza. Alternatywnie można wybrać magazyn kluczy oraz klucz ponownie, aby zaktualizować wersję.

## <a name="next-steps"></a>Kolejne kroki

- [Szyfrowanie usługi Azure Storage dla danych magazynowanych](storage-service-encryption.md)
- [Co to jest usługa Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)?
