---
title: Skonfiguruj własny klucz szyfrowania danych Azure Service Bus przechowywanych w spoczynku
description: Ten artykuł zawiera informacje dotyczące sposobu konfigurowania własnego klucza do szyfrowania danych Azure Service Bus Rest.
services: service-bus-messaging
ms.service: service-bus
documentationcenter: ''
author: axisc
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: aschhab
ms.openlocfilehash: 356f825524192c3b6cf7df7f0460975f23ea4f7c
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74852291"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-service-bus-data-at-rest-by-using-the-azure-portal-preview"></a>Konfigurowanie kluczy zarządzanych przez klienta do szyfrowania Azure Service Bus danych przechowywanych przy użyciu Azure Portal (wersja zapoznawcza)
Azure Service Bus Premium zapewnia szyfrowanie danych przechowywanych przy użyciu usługi Azure szyfrowanie usługi Storage (SSE platformy Azure). Service Bus Premium bazuje na usłudze Azure Storage do przechowywania danych i domyślnie wszystkie dane przechowywane w usłudze Azure Storage są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. 

## <a name="overview"></a>Przegląd
Azure Service Bus teraz obsługuje opcję szyfrowania danych przechowywanych przy użyciu kluczy zarządzanych przez firmę Microsoft lub kluczy zarządzanych przez klienta (Bring Your Own Key-BYOK). Ta funkcja umożliwia tworzenie, obracanie, wyłączanie i odwoływanie dostępu do kluczy zarządzanych przez klienta, które są używane do szyfrowania Azure Service Bus w stanie spoczynku.

Włączenie funkcji BYOK to jednorazowy proces konfiguracji w przestrzeni nazw.

> [!NOTE]
> Klucz zarządzany przez klienta ma pewne zastrzeżenia dotyczące szyfrowania po stronie usługi. 
>   * Ta funkcja jest obsługiwana przez [Azure Service Bus warstwy Premium](service-bus-premium-messaging.md) . Nie można jej włączyć dla Service Bus przestrzeni nazw w warstwie Standardowa.
>   * Szyfrowanie można włączyć tylko dla nowych lub pustych przestrzeni nazw. Jeśli przestrzeń nazw zawiera dane, operacja szyfrowania zakończy się niepowodzeniem.
>   * Jeśli [punkty końcowe usługi Sieć wirtualna (VNET)](service-bus-service-endpoints.md) są skonfigurowane na Azure Key Vault dla Service Bus przestrzeni nazw, BYOK nie będzie obsługiwana. 

Za pomocą Azure Key Vault można zarządzać kluczami i przeprowadzać inspekcję użycia klucza. Możesz utworzyć własne klucze i zapisać je w magazynie kluczy lub użyć Azure Key Vault interfejsów API do wygenerowania kluczy. Aby uzyskać więcej informacji na temat Azure Key Vault, zobacz [co to jest Azure Key Vault?](../key-vault/key-vault-overview.md)

W tym artykule pokazano, jak skonfigurować magazyn kluczy z kluczami zarządzanymi przez klienta przy użyciu Azure Portal. Aby dowiedzieć się, jak utworzyć magazyn kluczy przy użyciu Azure Portal, zobacz [Szybki Start: Ustawianie i pobieranie klucza tajnego z Azure Key Vault przy użyciu Azure Portal](../key-vault/quick-create-portal.md).

> [!IMPORTANT]
> Użycie kluczy zarządzanych przez klienta w programie Azure Service Bus wymaga skonfigurowania dwóch wymaganych właściwości magazynu kluczy. Są to: **usuwanie nietrwałe** i **nie przeczyszczanie**. Te właściwości są domyślnie włączone podczas tworzenia nowego magazynu kluczy w Azure Portal. Jeśli jednak musisz włączyć te właściwości w istniejącym magazynie kluczy, musisz użyć programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

## <a name="enable-customer-managed-keys"></a>Włącz klucze zarządzane przez klienta
Aby włączyć klucze zarządzane przez klienta w Azure Portal, wykonaj następujące kroki:

1. Przejdź do przestrzeni nazw w warstwie Premium Service Bus.
2. Na stronie **Ustawienia** przestrzeni nazw Service Bus wybierz pozycję **szyfrowanie (wersja zapoznawcza)** .
3. Wybierz **szyfrowanie klucza zarządzanego przez klienta** , jak pokazano na poniższej ilustracji.

    ![Włącz klucz zarządzany przez klienta](./media/configure-customer-managed-key/enable-customer-managed-key.png)


## <a name="set-up-a-key-vault-with-keys"></a>Konfigurowanie magazynu kluczy z kluczami

Po włączeniu kluczy zarządzanych przez klienta należy skojarzyć klucz zarządzany klienta z przestrzenią nazw Azure Service Bus. Service Bus obsługuje tylko Azure Key Vault. Po włączeniu opcji **szyfrowania z kluczem zarządzanym przez klienta** w poprzedniej sekcji należy zaimportować klucz do Azure Key Vault. Ponadto klucze muszą mieć **nietrwałe usuwanie** i nie można ich **czyścić** skonfigurować dla klucza. Te ustawienia można skonfigurować przy użyciu [programu PowerShell](../key-vault/key-vault-soft-delete-powershell.md) lub [interfejsu wiersza polecenia](../key-vault/key-vault-soft-delete-cli.md#enabling-purge-protection).

1. Aby utworzyć nowy magazyn kluczy, postępuj zgodnie z Azure Key Vault [przewodnika Szybki Start](../key-vault/key-vault-overview.md). Aby uzyskać więcej informacji o importowaniu istniejących kluczy, zobacz [Informacje o kluczach, wpisach tajnych i certyfikatach](../key-vault/about-keys-secrets-and-certificates.md).
1. Aby włączyć zarówno nietrwałe usuwanie, jak i przeczyszczanie, podczas tworzenia magazynu należy użyć polecenia [AZ Create webmagazynion](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) .

    ```azurecli-interactive
    az keyvault create --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Aby dodać ochronę przed przeczyszczeniem do istniejącego magazynu (ma już włączony nietrwałe usuwanie), użyj polecenia [AZ webmagazyn Update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) .

    ```azurecli-interactive
    az keyvault update --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Utwórz klucze, wykonując następujące czynności:
    1. Aby utworzyć nowy klucz, wybierz pozycję **Generuj/Importuj** z menu **klucze** w obszarze **Ustawienia**.
        
        ![Przycisk Wybierz opcję Generuj/Importuj](./media/configure-customer-managed-key/select-generate-import.png)

    1. Ustaw **Opcje** w celu **wygenerowania** klucza i nadaj mu nazwę.

        ![Utwórz klucz](./media/configure-customer-managed-key/create-key.png) 

    1. Teraz możesz wybrać ten klucz do skojarzenia z przestrzenią nazw Service Bus do szyfrowania z listy rozwijanej. 

        ![Wybierz klucz z magazynu kluczy](./media/configure-customer-managed-key/select-key-from-key-vault.png)
        > [!NOTE]
        > Aby zapewnić nadmiarowość, można dodać maksymalnie 3 klucze. W przypadku, gdy jeden z kluczy wygasł lub jest niedostępny, inne klucze będą używane do szyfrowania.
        
    1. Wprowadź szczegóły klucza i kliknij przycisk **Wybierz**. Umożliwi to szyfrowanie danych przechowywanych w przestrzeni nazw za pomocą klucza zarządzanego przez klienta. 


> [!IMPORTANT]
> Jeśli zamierzasz używać klucza zarządzanego przez klienta wraz z odzyskiwaniem po awarii geograficznej, zapoznaj się z poniższymi tematami. 
>
> Aby włączyć szyfrowanie w spoczynku z kluczem zarządzanym przez klienta, skonfigurowano [zasady dostępu](../key-vault/key-vault-secure-your-key-vault.md) dla tożsamości zarządzanej Service Bus w określonym magazynie kluczy platformy Azure. Zapewnia to kontrolowany dostęp do magazynu kluczy platformy Azure z przestrzeni nazw Azure Service Bus.
>
> Z tego powodu:
> 
>   * Jeśli funkcja [odzyskiwania po awarii geograficznej](service-bus-geo-dr.md) jest już włączona dla przestrzeni nazw Service Bus i chcesz włączyć klucz zarządzany przez klienta, 
>     * Przerwij parowanie
>     * [Skonfiguruj zasady dostępu](../key-vault/managed-identity.md) dla tożsamości zarządzanej zarówno dla głównych, jak i pomocniczych przestrzeni nazw dla magazynu kluczy.
>     * Skonfiguruj szyfrowanie dla podstawowej przestrzeni nazw.
>     * Ponowne parowanie podstawowych i pomocniczych przestrzeni nazw.
> 
>   * Jeśli chcesz włączyć lokalizację geograficzną w Service Bus przestrzeni nazw, w której klucz zarządzany przez klienta został już skonfigurowany, a następnie —
>     * [Skonfiguruj zasady dostępu](../key-vault/managed-identity.md) dla zarządzanej tożsamości dla pomocniczej przestrzeni nazw dla magazynu kluczy.
>     * Parowanie podstawowych i pomocniczych przestrzeni nazw.


## <a name="rotate-your-encryption-keys"></a>Obróć klucze szyfrowania

Możesz obrócić klucz w magazynie kluczy przy użyciu mechanizmu rotacji magazynów kluczy platformy Azure. Aby uzyskać więcej informacji, zobacz [Konfigurowanie rotacji kluczy i inspekcji](../key-vault/key-vault-key-rotation-log-monitoring.md). Daty aktywacji i wygaśnięcia można także skonfigurować w celu zautomatyzowania rotacji kluczy. Usługa Service Bus wykryje nowe wersje klucza i rozpocznie korzystanie z nich automatycznie.

## <a name="revoke-access-to-keys"></a>Odwołaj dostęp do kluczy

Cofnięcie dostępu do kluczy szyfrowania nie spowoduje przeczyszczenia danych z Service Bus. Nie można jednak uzyskać dostępu do danych z przestrzeni nazw Service Bus. Możesz odwołać klucz szyfrowania za pomocą zasad dostępu lub usunąć klucz. Dowiedz się więcej na temat zasad dostępu i zabezpieczania magazynu kluczy, [Aby uzyskać bezpieczny dostęp do magazynu kluczy](../key-vault/key-vault-secure-your-key-vault.md).

Po odwołaniu klucza szyfrowania Usługa Service Bus w zaszyfrowanej przestrzeni nazw stanie się niezależna. Jeśli dostęp do klucza jest włączony lub przywrócono usunięty klucz, Usługa Service Bus wybierze klucz, aby można było uzyskać dostęp do danych z przestrzeni nazw zaszyfrowanej Service Bus.

> [!NOTE]
> Jeśli usuniesz istniejący klucz szyfrowania z magazynu kluczy i zastąpi go nowym kluczem w przestrzeni nazw Service Bus, ponieważ klucz usuwania jest nadal ważny (w pamięci podręcznej) przez maksymalnie godzinę, stare dane (zaszyfrowany przy użyciu starego klucza) mogą nadal być dostępne Alon g z nowymi danymi, które są teraz dostępne tylko przy użyciu nowego klucza. To zachowanie jest zaprojektowana w wersji zapoznawczej funkcji. 

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:
- [Przegląd Service Bus](service-bus-messaging-overview.md)
- [Przegląd Key Vault](../key-vault/key-vault-overview.md)


