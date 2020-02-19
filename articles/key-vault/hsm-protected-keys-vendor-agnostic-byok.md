---
title: Jak generować i przesyłać klucze chronione przez moduł HSM dla Azure Key Vault-Azure Key Vault | Microsoft Docs
description: Ten artykuł ułatwia planowanie, generowanie i transferowanie własnych kluczy chronionych przez moduł HSM w celu korzystania z Azure Key Vault. Znany również jako BYOK lub własny klucz.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: ambapat
ms.openlocfilehash: bd70cfb58c9d89f1d454537721e22f36b1fd3d3e
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2020
ms.locfileid: "77429295"
---
# <a name="import-hsm-protected-keys-to-key-vault-preview"></a>Importuj klucze chronione przez moduł HSM do Key Vault (wersja zapoznawcza)

> [!NOTE]
> Ta funkcja jest w wersji zapoznawczej i dostępna tylko w regionach **Wschodnie stany USA 2 — euap** i **środkowe stany USA — euap** . 

Aby zwiększyć gwarancję w przypadku korzystania z Azure Key Vault, można importować lub generować klucze w sprzętowych modułach zabezpieczeń (sprzętowych modułów zabezpieczeń), które nigdy nie opuszczają granicy modułu HSM. Ten scenariusz jest często nazywany *własnym kluczem*lub BYOK. W celu ochrony kluczy Azure Key Vault używa rodziny oprogramowanie wspomagające nCipher sprzętowego nshield sprzętowych modułów zabezpieczeń (zweryfikowany poziom 2 trybu FIPS 140-2).

Informacje przedstawione w tym temacie ułatwiają planowanie, generowanie i transferowanie własnych kluczy chronionych przez moduł HSM w celu korzystania z Azure Key Vault.

> [!NOTE]
> Ta funkcja jest niedostępna dla platformy Azure w Chinach. 
> 
> Ta metoda importowania jest dostępna tylko dla [obsługiwanych sprzętowych modułów zabezpieczeń](#supported-hsms). 

Aby uzyskać więcej informacji na temat Azure Key Vault, zobacz [co to jest Azure Key Vault?](key-vault-overview.md)  Aby zapoznać się z samouczkiem wprowadzającym, które obejmuje tworzenie magazynu kluczy dla kluczy chronionych przez moduł HSM, zobacz [co to jest Azure Key Vault?](key-vault-overview.md).

## <a name="overview"></a>Omówienie

* Wygeneruj klucz (nazywany kluczem wymiany klucza lub KEK) w magazynie kluczy. Musi to być klucz modułu HSM RSA z elementem "Import" jako jedyną operacją klucza. Tylko Magazyn kluczy Premium SKU obsługuje klucze HSM.
* Pobierz klucz publiczny z KEK jako plik PEM
* Transfer klucza publicznego KEK do stacji roboczej w trybie offline połączonej z lokalnym modułem HSM.
* Z poziomu stacji roboczej offline Użyj narzędzia BYOK dostarczonego przez dostawcę modułu HSM, aby utworzyć plik BYOK. 
* Klucz docelowy jest szyfrowany przy użyciu KEK, który pozostaje zaszyfrowany do momentu przeniesienia do Azure Key Vault sprzętowych modułów zabezpieczeń. Tylko zaszyfrowana wersja klucza spowoduje pozostawienie lokalnego modułu HSM.
* KEK, który jest generowany w Azure Key Vault sprzętowych modułów zabezpieczeń i nie jest eksportowalny. Sprzętowych modułów zabezpieczeń wymusza, że nie może być wyczyszczona wersja KEK poza Key Vault sprzętowych modułów zabezpieczeń.
* KEK musi znajdować się w tym samym magazynie kluczy, w którym ma zostać zaimportowany klucz docelowy.
* Gdy plik BYOK jest przekazywany do Key Vault, Key Vault sprzętowych modułów zabezpieczeń użyć klucza prywatnego KEK w celu odszyfrowania materiału klucza docelowego i zaimportowania go jako klucza HSM. Ta operacja występuje całkowicie w Key Vault sprzętowych modułów zabezpieczeń, a klucz docelowy zawsze pozostaje w granicy ochrony modułu HSM.

## <a name="prerequisites"></a>Wymagania wstępne

Zapoznaj się z poniższą tabelą, aby uzyskać listę wymagań wstępnych dotyczących przenoszenia własnych kluczy (BYOK) dla Azure Key Vault.

| Wymaganie | Więcej informacji |
| --- | --- |
| Subskrypcja platformy Azure |Aby utworzyć Azure Key Vault, musisz mieć subskrypcję platformy Azure: [zarejestruj się, aby skorzystać z bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/) |
| Magazyn kluczy (SKU Premium) do importowania kluczy chronionych przez moduł HSM |Więcej informacji o warstwach i możliwościach usługi dla Azure Key Vault można znaleźć w witrynie internetowej [cennika Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) . |
| Moduł HSM z obsługiwanej listy sprzętowych modułów zabezpieczeń wraz z narzędziem BYOK i instrukcjami dostarczonymi przez dostawcę modułu HSM | Musisz mieć dostęp do sprzętowego modułu zabezpieczeń i podstawowej wiedzy operacyjnej sprzętowych modułów zabezpieczeń. Zobacz [obsługiwane sprzętowych modułów zabezpieczeń](#supported-hsms). |
| Interfejs wiersza polecenia platformy Azure w wersji 2.0.82 lub nowszej | Aby uzyskać więcej informacji [, zobacz Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) .|

## <a name="supported-hsms"></a>Obsługiwane sprzętowych modułów zabezpieczeń

|Nazwa dostawcy modułu HSM|Obsługiwane modele HSM|Dodatkowe szczegóły|
|---|---|---|
|Firmy Thales|Rodzina SafeNet Luna modułu HSM 7 z oprogramowaniem układowym w wersji 7,3 lub nowszej| [SafeNet Luna BYOK — narzędzie i dokumentacja](https://safenet.gemalto.com/blah-blah)|


> [!NOTE]
> Aby zaimportować klucze chronione przez moduł HSM z rodziny oprogramowanie wspomagające nCipher sprzętowego nshield sprzętowych modułów zabezpieczeń, [Użyj starszej procedury BYOK](hsm-protected-keys-legacy.md)


## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>Generowanie i transferowanie klucza do Azure Key Vault modułu HSM

Aby wygenerować i przesłać klucz do modułu HSM Azure Key Vault, należy wykonać następujące czynności:

* [Krok 1. Generowanie elementu KEK](#step-1-generate-a-kek)
* [Krok 2. Pobieranie klucza publicznego KEK](#step-2-download-kek-public-key)
* [Krok 3. Generowanie i przygotowywanie klucza do przeniesienia](#step-3-generate-and-prepare-your-key-for-transfer)
* [Krok 4. przeniesienie klucza do Azure Key Vault](#step-4-transfer-your-key-to-azure-key-vault)

### <a name="step-1-generate-a-kek"></a>Krok 1. Generowanie elementu KEK

KEK (klucz wymiany klucza) to klucz RSA wygenerowany w module HSM Key Vault. Ten klucz służy do szyfrowania klucza do zaimportowania (klucz docelowy).

KEK musi być:
1. klucz modułu **HSM RSA** (2048-bitowy lub 3072-bitowy lub 4096-bitowy)
2. generowane w tym samym magazynie kluczy, w którym zamierzasz zaimportować klucz docelowy
3. utworzono z dozwolonymi operacjami Key ustawionymi na **Import**

Za pomocą polecenia [AZ Key magazynu klucz Create Utwórz](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) KEK z zestawem operacji Key, które mają zostać zaimportowane. Zanotuj identyfikator klucza "dziecko" zwrócony przez poniższe polecenie. Będzie ona potrzebna w [kroku 3](#step-3-generate-and-prepare-your-key-for-transfer).


```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-download-kek-public-key"></a>Krok 2. Pobieranie klucza publicznego KEK

Aby pobrać klucz publiczny KEK do pliku PEM, użyj okna [AZ Key magazynu Download](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-download) . Importowany klucz docelowy jest szyfrowany przy użyciu klucza publicznego KEK.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

Prześlij plik KEKforBYOK. PublicKey. pem do stacji roboczej w trybie offline. Ten plik będzie potrzebny w następnym kroku.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>Krok 3. Generowanie i przygotowywanie klucza do przeniesienia

Zapoznaj się z dokumentacją dostawcy modułu HSM, aby pobrać i zainstalować narzędzie BYOK. Wykonaj instrukcje od dostawcy modułu HSM, aby wygenerować klucz docelowy, a następnie Utwórz pakiet transferu klucza (plik BYOK). Narzędzie BYOK użyje identyfikatora klucza z [kroku 1](#step-1-generate-a-kek) i KEKforBYOK. PublicKey. pem pobranego w [kroku 2](#step-2-download-kek-public-key) , aby wygenerować zaszyfrowany klucz docelowy w pliku BYOK.

Prześlij plik BYOK do podłączonej stacji roboczej.

> [!NOTE] 
> Klucz docelowy musi być kluczem RSA o rozmiarze 2048-bitowym lub 3072-bitowym lub 4096-bitowym. Importowanie kluczy krzywej eliptyczna nie jest teraz obsługiwane.
> <br/><strong>Znany problem:</strong> Importowanie klucza docelowego RSA 4K z SafeNet Luna sprzętowych modułów zabezpieczeń nie powiodło się. Po rozwiązaniu problemu ten dokument zostanie zaktualizowany.

### <a name="step-4-transfer-your-key-to-azure-key-vault"></a>Krok 4. przeniesienie klucza do Azure Key Vault

W tym ostatnim kroku Przenieś pakiet transferu kluczy (plik BYOK) z rozłączonej stacji roboczej do stacji roboczej podłączonej do Internetu, a następnie za pomocą polecenia [AZ Key magazynu klucza import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) Przekaż Azure Key Vault plik BYOK, aby ukończyć Importowanie klucza.

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

Jeśli przekazywanie zakończy się pomyślnie, zobaczysz wyświetlone właściwości klucza, który został właśnie zaimportowany.

## <a name="next-steps"></a>Następne kroki

Teraz można używać tego klucza chronionego przez moduł HSM w magazynie kluczy. Aby uzyskać więcej informacji, zobacz to [porównanie](https://azure.microsoft.com/pricing/details/key-vault/)cen i funkcji.
